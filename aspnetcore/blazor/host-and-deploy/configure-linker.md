---
title: Configurer l’éditeur de liens pour ASP.NET CoreBlazor
author: guardrex
description: Découvrez comment contrôler l’éditeur de liens en langage intermédiaire (IL) lors de la génération d’une Blazor application.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/host-and-deploy/configure-linker
ms.openlocfilehash: efac62c325b03b5ee19cae58abb5227f3d300b69
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103681"
---
# <a name="configure-the-linker-for-aspnet-core-blazor"></a><span data-ttu-id="51121-103">Configurer l’éditeur de liens pour ASP.NET CoreBlazor</span><span class="sxs-lookup"><span data-stu-id="51121-103">Configure the Linker for ASP.NET Core Blazor</span></span>

<span data-ttu-id="51121-104">Par [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="51121-104">By [Luke Latham](https://github.com/guardrex)</span></span>

Blazor<span data-ttu-id="51121-105">Webassembly effectue une liaison [il (Intermediate Language)](/dotnet/standard/managed-code#intermediate-language--execution) au cours d’une génération pour supprimer l’il inutile des assemblys de sortie de l’application.</span><span class="sxs-lookup"><span data-stu-id="51121-105"> WebAssembly performs [Intermediate Language (IL)](/dotnet/standard/managed-code#intermediate-language--execution) linking during a build to trim unnecessary IL from the app's output assemblies.</span></span> <span data-ttu-id="51121-106">L’éditeur de liens est désactivé lors de la génération dans la configuration Debug.</span><span class="sxs-lookup"><span data-stu-id="51121-106">The linker is disabled when building in Debug configuration.</span></span> <span data-ttu-id="51121-107">Les applications doivent être générées dans la configuration Release pour activer l’éditeur de liens.</span><span class="sxs-lookup"><span data-stu-id="51121-107">Apps must build in Release configuration to enable the linker.</span></span> <span data-ttu-id="51121-108">Nous vous recommandons de créer la version finale lors du déploiement de vos Blazor applications Webassembly.</span><span class="sxs-lookup"><span data-stu-id="51121-108">We recommend building in Release when deploying your Blazor WebAssembly apps.</span></span> 

<span data-ttu-id="51121-109">La liaison d’une application optimise sa taille, mais peut avoir des effets néfastes.</span><span class="sxs-lookup"><span data-stu-id="51121-109">Linking an app optimizes for size but may have detrimental effects.</span></span> <span data-ttu-id="51121-110">Les applications qui utilisent la réflexion ou les fonctionnalités dynamiques associées peuvent s’arrêter en cas de troncation, car l’éditeur de liens ne connaît pas ce comportement dynamique et ne peut pas déterminer en général les types requis pour la réflexion au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="51121-110">Apps that use reflection or related dynamic features may break when trimmed because the linker doesn't know about this dynamic behavior and can't determine in general which types are required for reflection at runtime.</span></span> <span data-ttu-id="51121-111">Pour supprimer de telles applications, l’éditeur de liens doit être informé des types requis par la réflexion dans le code et dans les packages ou infrastructures dont dépend l’application.</span><span class="sxs-lookup"><span data-stu-id="51121-111">To trim such apps, the linker must be informed about any types required by reflection in the code and in packages or frameworks that the app depends on.</span></span> 

<span data-ttu-id="51121-112">Pour garantir le bon fonctionnement de l’application tronquée une fois déployée, il est important de tester fréquemment les versions release de l’application lors du développement.</span><span class="sxs-lookup"><span data-stu-id="51121-112">To ensure the trimmed app works correctly once deployed, it's important to test Release builds of the app frequently while developing.</span></span>

<span data-ttu-id="51121-113">La liaison des Blazor applications peut être configurée à l’aide de ces fonctionnalités MSBuild :</span><span class="sxs-lookup"><span data-stu-id="51121-113">Linking for Blazor apps can be configured using these MSBuild features:</span></span>

* <span data-ttu-id="51121-114">Configurez la liaison globale avec une [propriété MSBuild](#control-linking-with-an-msbuild-property).</span><span class="sxs-lookup"><span data-stu-id="51121-114">Configure linking globally with a [MSBuild property](#control-linking-with-an-msbuild-property).</span></span>
* <span data-ttu-id="51121-115">Contrôle de liaison de chaque assembly avec un [fichier de configuration](#control-linking-with-a-configuration-file).</span><span class="sxs-lookup"><span data-stu-id="51121-115">Control linking on a per-assembly basis with a [configuration file](#control-linking-with-a-configuration-file).</span></span>

## <a name="control-linking-with-an-msbuild-property"></a><span data-ttu-id="51121-116">Liaison de contrôle avec une propriété MSBuild</span><span class="sxs-lookup"><span data-stu-id="51121-116">Control linking with an MSBuild property</span></span>

<span data-ttu-id="51121-117">La liaison est activée lorsqu’une application est intégrée à la `Release` Configuration.</span><span class="sxs-lookup"><span data-stu-id="51121-117">Linking is enabled when an app is built in `Release` configuration.</span></span> <span data-ttu-id="51121-118">Pour modifier cela, configurez la `BlazorWebAssemblyEnableLinking` propriété MSBuild dans le fichier projet :</span><span class="sxs-lookup"><span data-stu-id="51121-118">To change this, configure the `BlazorWebAssemblyEnableLinking` MSBuild property in the project file:</span></span>

```xml
<PropertyGroup>
  <BlazorWebAssemblyEnableLinking>false</BlazorWebAssemblyEnableLinking>
</PropertyGroup>
```

## <a name="control-linking-with-a-configuration-file"></a><span data-ttu-id="51121-119">Contrôler la liaison avec un fichier de configuration</span><span class="sxs-lookup"><span data-stu-id="51121-119">Control linking with a configuration file</span></span>

<span data-ttu-id="51121-120">Contrôlez la liaison pour chaque assembly en fournissant un fichier de configuration XML et en spécifiant le fichier en tant qu’élément MSBuild dans le fichier projet :</span><span class="sxs-lookup"><span data-stu-id="51121-120">Control linking on a per-assembly basis by providing an XML configuration file and specifying the file as a MSBuild item in the project file:</span></span>

```xml
<ItemGroup>
  <BlazorLinkerDescriptor Include="LinkerConfig.xml" />
</ItemGroup>
```

<span data-ttu-id="51121-121">*LinkerConfig.xml*:</span><span class="sxs-lookup"><span data-stu-id="51121-121">*LinkerConfig.xml*:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!--
  This file specifies which parts of the BCL or Blazor packages must not be
  stripped by the IL Linker even if they aren't referenced by user code.
-->
<linker>
  <assembly fullname="mscorlib">
    <!--
      Preserve the methods in WasmRuntime because its methods are called by 
      JavaScript client-side code to implement timers.
      Fixes: https://github.com/dotnet/blazor/issues/239
    -->
    <type fullname="System.Threading.WasmRuntime" />
  </assembly>
  <assembly fullname="System.Core">
    <!--
      System.Linq.Expressions* is required by Json.NET and any 
      expression.Compile caller. The assembly isn't stripped.
    -->
    <type fullname="System.Linq.Expressions*" />
  </assembly>
  <!--
    In this example, the app's entry point assembly is listed. The assembly
    isn't stripped by the IL Linker.
  -->
  <assembly fullname="MyCoolBlazorApp" />
</linker>
```

<span data-ttu-id="51121-122">Pour plus d’informations et d’exemples, consultez [formats de données (référentiel GitHub mono/éditeur de liens)](https://github.com/mono/linker/blob/master/docs/data-formats.md).</span><span class="sxs-lookup"><span data-stu-id="51121-122">For more information and examples, see [Data Formats (mono/linker GitHub repository)](https://github.com/mono/linker/blob/master/docs/data-formats.md).</span></span>

## <a name="add-an-xml-linker-configuration-file-to-a-library"></a><span data-ttu-id="51121-123">Ajouter un fichier de configuration de l’éditeur de liens XML à une bibliothèque</span><span class="sxs-lookup"><span data-stu-id="51121-123">Add an XML linker configuration file to a library</span></span>

<span data-ttu-id="51121-124">Pour configurer l’éditeur de liens pour une bibliothèque spécifique, ajoutez un fichier de configuration de l’éditeur de liens XML dans la bibliothèque en tant que ressource incorporée.</span><span class="sxs-lookup"><span data-stu-id="51121-124">To configure the linker for a specific library, add an XML linker configuration file into the library as an embedded resource.</span></span> <span data-ttu-id="51121-125">La ressource incorporée doit avoir le même nom que l’assembly.</span><span class="sxs-lookup"><span data-stu-id="51121-125">The embedded resource must have the same name as the assembly.</span></span>

<span data-ttu-id="51121-126">Dans l’exemple suivant, le fichier *LinkerConfig.xml* est spécifié en tant que ressource incorporée qui porte le même nom que l’assembly de la bibliothèque :</span><span class="sxs-lookup"><span data-stu-id="51121-126">In the following example, the *LinkerConfig.xml* file is specified as an embedded resource that has the same name as the library's assembly:</span></span>

```xml
<ItemGroup>
  <EmbeddedResource Include="LinkerConfig.xml">
    <LogicalName>$(MSBuildProjectName).xml</LogicalName>
  </EmbeddedResource>
</ItemGroup>
```

### <a name="configure-the-linker-for-internationalization"></a><span data-ttu-id="51121-127">Configurer l’éditeur de liens pour l’internationalisation</span><span class="sxs-lookup"><span data-stu-id="51121-127">Configure the linker for internationalization</span></span>

<span data-ttu-id="51121-128">Par défaut, Blazor la configuration de l’éditeur de liens pour les Blazor applications webassembly supprime les informations d’internationalisation, à l’exception des paramètres régionaux demandés explicitement.</span><span class="sxs-lookup"><span data-stu-id="51121-128">By default, Blazor's linker configuration for Blazor WebAssembly apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="51121-129">La suppression de ces assemblys réduit la taille de l’application.</span><span class="sxs-lookup"><span data-stu-id="51121-129">Removing these assemblies minimizes the app's size.</span></span>

<span data-ttu-id="51121-130">Pour contrôler les assemblys I18N qui sont conservés, définissez la `<BlazorWebAssemblyI18NAssemblies>` propriété MSBuild dans le fichier projet :</span><span class="sxs-lookup"><span data-stu-id="51121-130">To control which I18N assemblies are retained, set the `<BlazorWebAssemblyI18NAssemblies>` MSBuild property in the project file:</span></span>

```xml
<PropertyGroup>
  <BlazorWebAssemblyI18NAssemblies>{all|none|REGION1,REGION2,...}</BlazorWebAssemblyI18NAssemblies>
</PropertyGroup>
```

| <span data-ttu-id="51121-131">Valeur de la région</span><span class="sxs-lookup"><span data-stu-id="51121-131">Region Value</span></span>     | <span data-ttu-id="51121-132">Assembly de région mono</span><span class="sxs-lookup"><span data-stu-id="51121-132">Mono region assembly</span></span>    |
| ---------------- | ----------------------- |
| `all`            | <span data-ttu-id="51121-133">Tous les assemblys inclus</span><span class="sxs-lookup"><span data-stu-id="51121-133">All assemblies included</span></span> |
| `cjk`            | <span data-ttu-id="51121-134">*I18N.CJK.dll*</span><span class="sxs-lookup"><span data-stu-id="51121-134">*I18N.CJK.dll*</span></span>          |
| `mideast`        | <span data-ttu-id="51121-135">*I18N.MidEast.dll*</span><span class="sxs-lookup"><span data-stu-id="51121-135">*I18N.MidEast.dll*</span></span>      |
| <span data-ttu-id="51121-136">`none` (valeur par défaut)</span><span class="sxs-lookup"><span data-stu-id="51121-136">`none` (default)</span></span> | <span data-ttu-id="51121-137">None</span><span class="sxs-lookup"><span data-stu-id="51121-137">None</span></span>                    |
| `other`          | <span data-ttu-id="51121-138">*I18N.Other.dll*</span><span class="sxs-lookup"><span data-stu-id="51121-138">*I18N.Other.dll*</span></span>        |
| `rare`           | <span data-ttu-id="51121-139">*I18N.Rare.dll*</span><span class="sxs-lookup"><span data-stu-id="51121-139">*I18N.Rare.dll*</span></span>         |
| `west`           | <span data-ttu-id="51121-140">*I18N.West.dll*</span><span class="sxs-lookup"><span data-stu-id="51121-140">*I18N.West.dll*</span></span>         |

<span data-ttu-id="51121-141">Utilisez une virgule pour séparer plusieurs valeurs (par exemple, `mideast,west` ).</span><span class="sxs-lookup"><span data-stu-id="51121-141">Use a comma to separate multiple values (for example, `mideast,west`).</span></span>

<span data-ttu-id="51121-142">Pour plus d’informations, consultez [i18n : Pnetlib internationalisation Framework Library (référentiel mono/mono GitHub)](https://github.com/mono/mono/tree/master/mcs/class/I18N).</span><span class="sxs-lookup"><span data-stu-id="51121-142">For more information, see [I18N: Pnetlib Internationalization Framework Library (mono/mono GitHub repository)](https://github.com/mono/mono/tree/master/mcs/class/I18N).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="51121-143">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="51121-143">Additional resources</span></span>

* <xref:blazor/webassembly-performance-best-practices#intermediate-language-il-linking>