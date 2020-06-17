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
uid: host-and-deploy/blazor/configure-linker
ms.openlocfilehash: fd8dc118b8ed9c9e7c0d43e67b14ef4affa09d48
ms.sourcegitcommit: b0062f29cba2e5c21b95cf89eaf435ba830d11a3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/16/2020
ms.locfileid: "83608627"
---
# <a name="configure-the-linker-for-aspnet-core-blazor"></a>Configurer l’éditeur de liens pour ASP.NET CoreBlazor

Par [Luke Latham](https://github.com/guardrex)

BlazorWebassembly effectue une liaison [il (Intermediate Language)](/dotnet/standard/managed-code#intermediate-language--execution) au cours d’une génération pour supprimer l’il inutile des assemblys de sortie de l’application. L’éditeur de liens est désactivé lors de la génération dans la configuration Debug. Les applications doivent être générées dans la configuration Release pour activer l’éditeur de liens. Nous vous recommandons de créer la version finale lors du déploiement de vos Blazor applications Webassembly. 

La liaison d’une application optimise sa taille, mais peut avoir des effets néfastes. Les applications qui utilisent la réflexion ou les fonctionnalités dynamiques associées peuvent s’arrêter en cas de troncation, car l’éditeur de liens ne connaît pas ce comportement dynamique et ne peut pas déterminer en général les types requis pour la réflexion au moment de l’exécution. Pour supprimer de telles applications, l’éditeur de liens doit être informé des types requis par la réflexion dans le code et dans les packages ou infrastructures dont dépend l’application. 

Pour garantir le bon fonctionnement de l’application tronquée une fois déployée, il est important de tester fréquemment les versions release de l’application lors du développement.

La liaison des Blazor applications peut être configurée à l’aide de ces fonctionnalités MSBuild :

* Configurez la liaison globale avec une [propriété MSBuild](#control-linking-with-an-msbuild-property).
* Contrôle de liaison de chaque assembly avec un [fichier de configuration](#control-linking-with-a-configuration-file).

## <a name="control-linking-with-an-msbuild-property"></a>Liaison de contrôle avec une propriété MSBuild

La liaison est activée lorsqu’une application est intégrée à la `Release` Configuration. Pour modifier cela, configurez la `BlazorWebAssemblyEnableLinking` propriété MSBuild dans le fichier projet :

```xml
<PropertyGroup>
  <BlazorWebAssemblyEnableLinking>false</BlazorWebAssemblyEnableLinking>
</PropertyGroup>
```

## <a name="control-linking-with-a-configuration-file"></a>Contrôler la liaison avec un fichier de configuration

Contrôlez la liaison pour chaque assembly en fournissant un fichier de configuration XML et en spécifiant le fichier en tant qu’élément MSBuild dans le fichier projet :

```xml
<ItemGroup>
  <BlazorLinkerDescriptor Include="LinkerConfig.xml" />
</ItemGroup>
```

*LinkerConfig.xml*:

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

Pour plus d’informations et d’exemples, consultez [formats de données (référentiel GitHub mono/éditeur de liens)](https://github.com/mono/linker/blob/master/docs/data-formats.md).

## <a name="add-an-xml-linker-configuration-file-to-a-library"></a>Ajouter un fichier de configuration de l’éditeur de liens XML à une bibliothèque

Pour configurer l’éditeur de liens pour une bibliothèque spécifique, ajoutez un fichier de configuration de l’éditeur de liens XML dans la bibliothèque en tant que ressource incorporée. La ressource incorporée doit avoir le même nom que l’assembly.

Dans l’exemple suivant, le fichier *LinkerConfig.xml* est spécifié en tant que ressource incorporée qui porte le même nom que l’assembly de la bibliothèque :

```xml
<ItemGroup>
  <EmbeddedResource Include="LinkerConfig.xml">
    <LogicalName>$(MSBuildProjectName).xml</LogicalName>
  </EmbeddedResource>
</ItemGroup>
```

### <a name="configure-the-linker-for-internationalization"></a>Configurer l’éditeur de liens pour l’internationalisation

Par défaut, Blazor la configuration de l’éditeur de liens pour les Blazor applications webassembly supprime les informations d’internationalisation, à l’exception des paramètres régionaux demandés explicitement. La suppression de ces assemblys réduit la taille de l’application.

Pour contrôler les assemblys I18N qui sont conservés, définissez la `<BlazorWebAssemblyI18NAssemblies>` propriété MSBuild dans le fichier projet :

```xml
<PropertyGroup>
  <BlazorWebAssemblyI18NAssemblies>{all|none|REGION1,REGION2,...}</BlazorWebAssemblyI18NAssemblies>
</PropertyGroup>
```

| Valeur de la région     | Assembly de région mono    |
| ---------------- | ----------------------- |
| `all`            | Tous les assemblys inclus |
| `cjk`            | *I18N.CJK.dll*          |
| `mideast`        | *I18N.MidEast.dll*      |
| `none` (valeur par défaut) | None                    |
| `other`          | *I18N.Other.dll*        |
| `rare`           | *I18N.Rare.dll*         |
| `west`           | *I18N.West.dll*         |

Utilisez une virgule pour séparer plusieurs valeurs (par exemple, `mideast,west` ).

Pour plus d’informations, consultez [i18n : Pnetlib internationalisation Framework Library (référentiel mono/mono GitHub)](https://github.com/mono/mono/tree/master/mcs/class/I18N).

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:performance/blazor/webassembly-best-practices#intermediate-language-il-linking>
