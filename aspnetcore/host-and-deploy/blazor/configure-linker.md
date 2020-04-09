---
title: Configurer le Linker pour ASP.NET CoreBlazor
author: guardrex
description: Apprenez à contrôler le Lien de langue intermédiaire Blazor (IL) lors de la création d’une application.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/23/2020
no-loc:
- Blazor
- SignalR
uid: host-and-deploy/blazor/configure-linker
ms.openlocfilehash: 109da5ef400c3b9d64ccf3ceb33a5387ea6b5618
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80218659"
---
# <a name="configure-the-linker-for-aspnet-core-blazor"></a><span data-ttu-id="6fa5f-103">Configurer l’éditeur de liens pour ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="6fa5f-103">Configure the Linker for ASP.NET Core Blazor</span></span>

<span data-ttu-id="6fa5f-104">Par [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="6fa5f-104">By [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="6fa5f-105">Blazor WebAssembly effectue des liaisons [en langage intermédiaire (IL)](/dotnet/standard/managed-code#intermediate-language--execution) au cours d’une build pour couper l’IL inutile des assemblages de sortie de l’application.</span><span class="sxs-lookup"><span data-stu-id="6fa5f-105">Blazor WebAssembly performs [Intermediate Language (IL)](/dotnet/standard/managed-code#intermediate-language--execution) linking during a build to trim unnecessary IL from the app's output assemblies.</span></span> <span data-ttu-id="6fa5f-106">Le lien est désactivé lors de la construction dans la configuration Debug.</span><span class="sxs-lookup"><span data-stu-id="6fa5f-106">The linker is disabled when building in Debug configuration.</span></span> <span data-ttu-id="6fa5f-107">Les applications doivent être intégrées dans la configuration de version pour activer le lien.</span><span class="sxs-lookup"><span data-stu-id="6fa5f-107">Apps must build in Release configuration to enable the linker.</span></span> <span data-ttu-id="6fa5f-108">Nous vous recommandons de construire dans Release lors du déploiement de vos applications Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="6fa5f-108">We recommend building in Release when deploying your Blazor WebAssembly apps.</span></span> 

<span data-ttu-id="6fa5f-109">Lier une application optimise la taille, mais peut avoir des effets néfastes.</span><span class="sxs-lookup"><span data-stu-id="6fa5f-109">Linking an app optimizes for size but may have detrimental effects.</span></span> <span data-ttu-id="6fa5f-110">Les applications qui utilisent la réflexion ou les fonctionnalités dynamiques connexes peuvent se briser lorsqu’elles sont taillées parce que le lien ne connaît pas ce comportement dynamique et ne peut pas déterminer en général quels types sont nécessaires pour la réflexion au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="6fa5f-110">Apps that use reflection or related dynamic features may break when trimmed because the linker doesn't know about this dynamic behavior and can't determine in general which types are required for reflection at runtime.</span></span> <span data-ttu-id="6fa5f-111">Pour couper ces applications, le linker doit être informé de tous les types requis par la réflexion dans le code et dans les paquets ou les cadres dont l’application dépend.</span><span class="sxs-lookup"><span data-stu-id="6fa5f-111">To trim such apps, the linker must be informed about any types required by reflection in the code and in packages or frameworks that the app depends on.</span></span> 

<span data-ttu-id="6fa5f-112">Pour s’assurer que l’application taillée fonctionne correctement une fois déployée, il est important de tester fréquemment les versions de l’application tout en développant.</span><span class="sxs-lookup"><span data-stu-id="6fa5f-112">To ensure the trimmed app works correctly once deployed, it's important to test Release builds of the app frequently while developing.</span></span>

<span data-ttu-id="6fa5f-113">Les liaisons pour les applications Blazor peuvent être configurées à l’aide de ces fonctionnalités MSBuild :</span><span class="sxs-lookup"><span data-stu-id="6fa5f-113">Linking for Blazor apps can be configured using these MSBuild features:</span></span>

* <span data-ttu-id="6fa5f-114">Configurer la liaison globale avec une [propriété MSBuild](#control-linking-with-an-msbuild-property).</span><span class="sxs-lookup"><span data-stu-id="6fa5f-114">Configure linking globally with a [MSBuild property](#control-linking-with-an-msbuild-property).</span></span>
* <span data-ttu-id="6fa5f-115">Contrôle de la liaison par assemblage avec un [fichier de configuration](#control-linking-with-a-configuration-file).</span><span class="sxs-lookup"><span data-stu-id="6fa5f-115">Control linking on a per-assembly basis with a [configuration file](#control-linking-with-a-configuration-file).</span></span>

## <a name="control-linking-with-an-msbuild-property"></a><span data-ttu-id="6fa5f-116">Contrôle des liens avec une propriété MSBuild</span><span class="sxs-lookup"><span data-stu-id="6fa5f-116">Control linking with an MSBuild property</span></span>

<span data-ttu-id="6fa5f-117">Le lien est activé lorsqu’une application est intégrée en `Release` configuration.</span><span class="sxs-lookup"><span data-stu-id="6fa5f-117">Linking is enabled when an app is built in `Release` configuration.</span></span> <span data-ttu-id="6fa5f-118">Pour modifier cela, `BlazorWebAssemblyEnableLinking` configurez la propriété MSBuild dans le fichier du projet :</span><span class="sxs-lookup"><span data-stu-id="6fa5f-118">To change this, configure the `BlazorWebAssemblyEnableLinking` MSBuild property in the project file:</span></span>

```xml
<PropertyGroup>
  <BlazorWebAssemblyEnableLinking>false</BlazorWebAssemblyEnableLinking>
</PropertyGroup>
```

## <a name="control-linking-with-a-configuration-file"></a><span data-ttu-id="6fa5f-119">Contrôler la liaison avec un fichier de configuration</span><span class="sxs-lookup"><span data-stu-id="6fa5f-119">Control linking with a configuration file</span></span>

<span data-ttu-id="6fa5f-120">Contrôlez la liaison pour chaque assembly en fournissant un fichier de configuration XML et en spécifiant le fichier en tant qu’élément MSBuild dans le fichier projet :</span><span class="sxs-lookup"><span data-stu-id="6fa5f-120">Control linking on a per-assembly basis by providing an XML configuration file and specifying the file as a MSBuild item in the project file:</span></span>

```xml
<ItemGroup>
  <BlazorLinkerDescriptor Include="LinkerConfig.xml" />
</ItemGroup>
```

<span data-ttu-id="6fa5f-121">*LinkerConfig.xml*:</span><span class="sxs-lookup"><span data-stu-id="6fa5f-121">*LinkerConfig.xml*:</span></span>

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

<span data-ttu-id="6fa5f-122">Pour plus d’informations, voir [les exemples de fichiers Link xml (référentiel GitHub mono/linker)](https://github.com/mono/linker#link-xml-file-examples).</span><span class="sxs-lookup"><span data-stu-id="6fa5f-122">For more information, see [Link xml file examples (mono/linker GitHub repository)](https://github.com/mono/linker#link-xml-file-examples).</span></span>

## <a name="add-an-xml-linker-configuration-file-to-a-library"></a><span data-ttu-id="6fa5f-123">Ajouter un fichier de configuration de liaison XML à une bibliothèque</span><span class="sxs-lookup"><span data-stu-id="6fa5f-123">Add an XML linker configuration file to a library</span></span>

<span data-ttu-id="6fa5f-124">Pour configurer le lien pour une bibliothèque spécifique, ajoutez un fichier de configuration de liaison XML dans la bibliothèque comme une ressource intégrée.</span><span class="sxs-lookup"><span data-stu-id="6fa5f-124">To configure the linker for a specific library, add an XML linker configuration file into the library as an embedded resource.</span></span> <span data-ttu-id="6fa5f-125">La ressource intégrée doit avoir le même nom que l’assemblage.</span><span class="sxs-lookup"><span data-stu-id="6fa5f-125">The embedded resource must have the same name as the assembly.</span></span>

<span data-ttu-id="6fa5f-126">Dans l’exemple suivant, le fichier *LinkerConfig.xml* est spécifié comme une ressource intégrée qui porte le même nom que l’assemblage de la bibliothèque :</span><span class="sxs-lookup"><span data-stu-id="6fa5f-126">In the following example, the *LinkerConfig.xml* file is specified as an embedded resource that has the same name as the library's assembly:</span></span>

```xml
<ItemGroup>
  <EmbeddedResource Include="LinkerConfig.xml">
    <LogicalName>$(MSBuildProjectName).xml</LogicalName>
  </EmbeddedResource>
</ItemGroup>
```

### <a name="configure-the-linker-for-internationalization"></a><span data-ttu-id="6fa5f-127">Configurer le lien pour l’internationalisation</span><span class="sxs-lookup"><span data-stu-id="6fa5f-127">Configure the linker for internationalization</span></span>

<span data-ttu-id="6fa5f-128">Par défaut, la configuration linker de Blazor pour les applications Blazor WebAssembly dépouille les informations d’internationalisation à l’exception des lieux explicitement demandés.</span><span class="sxs-lookup"><span data-stu-id="6fa5f-128">By default, Blazor's linker configuration for Blazor WebAssembly apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="6fa5f-129">La suppression de ces assemblages minimise la taille de l’application.</span><span class="sxs-lookup"><span data-stu-id="6fa5f-129">Removing these assemblies minimizes the app's size.</span></span>

<span data-ttu-id="6fa5f-130">Pour contrôler les assemblages I18N `<MonoLinkerI18NAssemblies>` retenus, définissez la propriété MSBuild dans le dossier du projet :</span><span class="sxs-lookup"><span data-stu-id="6fa5f-130">To control which I18N assemblies are retained, set the `<MonoLinkerI18NAssemblies>` MSBuild property in the project file:</span></span>

```xml
<PropertyGroup>
  <MonoLinkerI18NAssemblies>{all|none|REGION1,REGION2,...}</MonoLinkerI18NAssemblies>
</PropertyGroup>
```

| <span data-ttu-id="6fa5f-131">Valeur de la région</span><span class="sxs-lookup"><span data-stu-id="6fa5f-131">Region Value</span></span>     | <span data-ttu-id="6fa5f-132">Assemblage de la région Mono</span><span class="sxs-lookup"><span data-stu-id="6fa5f-132">Mono region assembly</span></span>    |
| ---------------- | ----------------------- |
| `all`            | <span data-ttu-id="6fa5f-133">Tous les assemblages inclus</span><span class="sxs-lookup"><span data-stu-id="6fa5f-133">All assemblies included</span></span> |
| `cjk`            | <span data-ttu-id="6fa5f-134">*I18N.CJK.dll*</span><span class="sxs-lookup"><span data-stu-id="6fa5f-134">*I18N.CJK.dll*</span></span>          |
| `mideast`        | <span data-ttu-id="6fa5f-135">*I18N.MidEast.dll*</span><span class="sxs-lookup"><span data-stu-id="6fa5f-135">*I18N.MidEast.dll*</span></span>      |
| <span data-ttu-id="6fa5f-136">`none` (par défaut)</span><span class="sxs-lookup"><span data-stu-id="6fa5f-136">`none` (default)</span></span> | <span data-ttu-id="6fa5f-137">None</span><span class="sxs-lookup"><span data-stu-id="6fa5f-137">None</span></span>                    |
| `other`          | <span data-ttu-id="6fa5f-138">*I18N.Other.dll*</span><span class="sxs-lookup"><span data-stu-id="6fa5f-138">*I18N.Other.dll*</span></span>        |
| `rare`           | <span data-ttu-id="6fa5f-139">*I18N.Rare.dll*</span><span class="sxs-lookup"><span data-stu-id="6fa5f-139">*I18N.Rare.dll*</span></span>         |
| `west`           | <span data-ttu-id="6fa5f-140">*I18N.West.dll*</span><span class="sxs-lookup"><span data-stu-id="6fa5f-140">*I18N.West.dll*</span></span>         |

<span data-ttu-id="6fa5f-141">Utilisez une virgule pour séparer `mideast,west`plusieurs valeurs (par exemple, ).</span><span class="sxs-lookup"><span data-stu-id="6fa5f-141">Use a comma to separate multiple values (for example, `mideast,west`).</span></span>

<span data-ttu-id="6fa5f-142">Pour plus d’informations, voir [I18N: Pnetlib Internationalization Framework Library (mono/mono GitHub référentiel)](https://github.com/mono/mono/tree/master/mcs/class/I18N).</span><span class="sxs-lookup"><span data-stu-id="6fa5f-142">For more information, see [I18N: Pnetlib Internationalization Framework Library (mono/mono GitHub repository)](https://github.com/mono/mono/tree/master/mcs/class/I18N).</span></span>
