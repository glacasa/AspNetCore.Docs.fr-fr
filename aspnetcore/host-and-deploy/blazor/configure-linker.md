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
# <a name="configure-the-linker-for-aspnet-core-blazor"></a>Configurer l’éditeur de liens pour ASP.NET Core Blazor

Par [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor WebAssembly effectue des liaisons [en langage intermédiaire (IL)](/dotnet/standard/managed-code#intermediate-language--execution) au cours d’une build pour couper l’IL inutile des assemblages de sortie de l’application. Le lien est désactivé lors de la construction dans la configuration Debug. Les applications doivent être intégrées dans la configuration de version pour activer le lien. Nous vous recommandons de construire dans Release lors du déploiement de vos applications Blazor WebAssembly. 

Lier une application optimise la taille, mais peut avoir des effets néfastes. Les applications qui utilisent la réflexion ou les fonctionnalités dynamiques connexes peuvent se briser lorsqu’elles sont taillées parce que le lien ne connaît pas ce comportement dynamique et ne peut pas déterminer en général quels types sont nécessaires pour la réflexion au moment de l’exécution. Pour couper ces applications, le linker doit être informé de tous les types requis par la réflexion dans le code et dans les paquets ou les cadres dont l’application dépend. 

Pour s’assurer que l’application taillée fonctionne correctement une fois déployée, il est important de tester fréquemment les versions de l’application tout en développant.

Les liaisons pour les applications Blazor peuvent être configurées à l’aide de ces fonctionnalités MSBuild :

* Configurer la liaison globale avec une [propriété MSBuild](#control-linking-with-an-msbuild-property).
* Contrôle de la liaison par assemblage avec un [fichier de configuration](#control-linking-with-a-configuration-file).

## <a name="control-linking-with-an-msbuild-property"></a>Contrôle des liens avec une propriété MSBuild

Le lien est activé lorsqu’une application est intégrée en `Release` configuration. Pour modifier cela, `BlazorWebAssemblyEnableLinking` configurez la propriété MSBuild dans le fichier du projet :

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

Pour plus d’informations, voir [les exemples de fichiers Link xml (référentiel GitHub mono/linker)](https://github.com/mono/linker#link-xml-file-examples).

## <a name="add-an-xml-linker-configuration-file-to-a-library"></a>Ajouter un fichier de configuration de liaison XML à une bibliothèque

Pour configurer le lien pour une bibliothèque spécifique, ajoutez un fichier de configuration de liaison XML dans la bibliothèque comme une ressource intégrée. La ressource intégrée doit avoir le même nom que l’assemblage.

Dans l’exemple suivant, le fichier *LinkerConfig.xml* est spécifié comme une ressource intégrée qui porte le même nom que l’assemblage de la bibliothèque :

```xml
<ItemGroup>
  <EmbeddedResource Include="LinkerConfig.xml">
    <LogicalName>$(MSBuildProjectName).xml</LogicalName>
  </EmbeddedResource>
</ItemGroup>
```

### <a name="configure-the-linker-for-internationalization"></a>Configurer le lien pour l’internationalisation

Par défaut, la configuration linker de Blazor pour les applications Blazor WebAssembly dépouille les informations d’internationalisation à l’exception des lieux explicitement demandés. La suppression de ces assemblages minimise la taille de l’application.

Pour contrôler les assemblages I18N `<MonoLinkerI18NAssemblies>` retenus, définissez la propriété MSBuild dans le dossier du projet :

```xml
<PropertyGroup>
  <MonoLinkerI18NAssemblies>{all|none|REGION1,REGION2,...}</MonoLinkerI18NAssemblies>
</PropertyGroup>
```

| Valeur de la région     | Assemblage de la région Mono    |
| ---------------- | ----------------------- |
| `all`            | Tous les assemblages inclus |
| `cjk`            | *I18N.CJK.dll*          |
| `mideast`        | *I18N.MidEast.dll*      |
| `none` (par défaut) | None                    |
| `other`          | *I18N.Other.dll*        |
| `rare`           | *I18N.Rare.dll*         |
| `west`           | *I18N.West.dll*         |

Utilisez une virgule pour séparer `mideast,west`plusieurs valeurs (par exemple, ).

Pour plus d’informations, voir [I18N: Pnetlib Internationalization Framework Library (mono/mono GitHub référentiel)](https://github.com/mono/mono/tree/master/mcs/class/I18N).
