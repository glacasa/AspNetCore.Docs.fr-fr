---
title: SDK Razor ASP.NET Core
author: Rick-Anderson
description: Découvrez comment les pages Razor dans ASP.NET Core permettent de développer des scénarios orientés page de façon plus simple et plus productive qu’avec MVC.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 03/26/2020
no-loc:
- Blazor
uid: razor-pages/sdk
ms.openlocfilehash: 2284131ce2d45ec6bc01ce38f91e2c951b108605
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80321011"
---
# <a name="aspnet-core-razor-sdk"></a>SDK Razor ASP.NET Core

Par [Rick Anderson](https://twitter.com/RickAndMSFT)

## <a name="overview"></a>Vue d’ensemble

[!INCLUDE[](~/includes/2.1-SDK.md)] inclut le SDK MSBuild `Microsoft.NET.Sdk.Razor` (SDK Razor). Le SDK Razor :

::: moniker range=">= aspnetcore-3.0"

* Est tenu de construire, d’emballer et de publier des projets contenant des fichiers [Razor](xref:mvc/views/razor) pour ASP.NET projets Core MVC ou [Blazor.](xref:blazor/index)
* Inclut un ensemble de cibles prédéfinies, propriétés et éléments qui permettent de personnaliser la compilation de fichiers Razor (*.cshtml* ou *.razor).*

Le Razor `Content` SDK `Include` comprend des éléments `**\*.cshtml` `**\*.razor` avec des attributs réglés aux motifs et des schémas de glisse. Les fichiers correspondants sont publiés.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* Normalise l’expérience liée à la génération, à l’empaquetage et à la publication de projets contenant des fichiers [Razor](xref:mvc/views/razor) pour les projets ASP.NET Core basés sur MVC.
* Comprend un ensemble de cibles, de propriétés et d’éléments prédéfinis qui permettent de personnaliser la compilation des fichiers Razor.

Le Razor SDK `Content` comprend `Include` un élément `**\*.cshtml` avec un ensemble d’attributs au motif de glisse. Les fichiers correspondants sont publiés.

::: moniker-end

## <a name="prerequisites"></a>Prérequis

[!INCLUDE[](~/includes/2.1-SDK.md)]

## <a name="use-the-razor-sdk"></a>Utilisez le Razor SDK

La plupart des applications Web ne sont pas tenues de référencer explicitement le Razor SDK.

::: moniker range=">= aspnetcore-3.0"

Pour utiliser le Razor SDK pour construire des bibliothèques de classe contenant des vues Razor ou des pages Razor, nous vous recommandons de commencer par le modèle de projet de la bibliothèque de la classe Razor (RCL). Un RCL qui est utilisé pour construire des fichiers Blazor (*.razor*) nécessite au minimum une référence au paquet [Microsoft.AspNetCore.Components.](https://www.nuget.org/packages/Microsoft.AspNetCore.Components) Un RCL qui est utilisé pour construire des vues Razor ou des pages `netcoreapp3.0` (fichiers *.cshtml)* nécessite peu à peu de ciblage ou plus tard et a un `FrameworkReference` [métapackage Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) dans son fichier de projet.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Pour utiliser le SDK Razor pour générer des bibliothèques de classes contenant des vues ou des pages Razor :

* Utilisez `Microsoft.NET.Sdk.Razor` à la place de `Microsoft.NET.Sdk` :

  ```xml
  <Project SDK="Microsoft.NET.Sdk.Razor">
    <!-- omitted for brevity -->
  </Project>
  ```

* En règle générale, `Microsoft.AspNetCore.Mvc` une référence globale est nécessaire pour recevoir des dépendances supplémentaires qui sont nécessaires pour construire et compiler des pages de rasoir et des vues Razor. Au minimum, votre projet devrait ajouter des références de paquets à :

  * `Microsoft.AspNetCore.Razor.Design`
  * `Microsoft.AspNetCore.Mvc.Razor.Extensions`
  * `Microsoft.AspNetCore.Mvc.Razor`

  Le `Microsoft.AspNetCore.Razor.Design` paquet fournit les tâches de compilation Razor et les cibles pour le projet.

  Les packages précédents sont inclus dans `Microsoft.AspNetCore.Mvc`. La balisage suivante affiche un fichier de projet qui utilise le Razor SDK pour construire des fichiers Razor pour une application ASP.NET Core Razor Pages :

  [!code-xml[](sdk/sample/RazorSDK.csproj)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

> [!WARNING]
> Le `Microsoft.AspNetCore.Razor.Design` `Microsoft.AspNetCore.Mvc.Razor.Extensions` et les paquets sont inclus dans le [métapackage Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app). Cependant, la référence `Microsoft.AspNetCore.App` de paquet sans version fournit un métapackage à l’application qui n’inclut pas la dernière version de `Microsoft.AspNetCore.Razor.Design`. Les projets doivent `Microsoft.AspNetCore.Razor.Design` faire `Microsoft.AspNetCore.Mvc`référence à une version cohérente de (ou ) de sorte que les dernières corrections de temps de construction pour Razor soient incluses. Pour plus d’informations, consultez [ce problème GitHub](https://github.com/aspnet/Razor/issues/2553).

::: moniker-end

### <a name="properties"></a>Propriétés

Les propriétés suivantes contrôlent le comportement du SDK Razor dans le cadre d’une build de projet :

* `RazorCompileOnBuild`&ndash; Lorsque `true`, compile et émet l’assemblage Razor dans le cadre de la construction du projet. La valeur par défaut est `true`.
* `RazorCompileOnPublish`&ndash; Lorsque `true`, compile et émet l’assemblage Razor dans le cadre de la publication du projet. La valeur par défaut est `true`.

Les propriétés et les éléments du tableau suivant sont utilisés pour configurer les entrées et la sortie du Razor SDK.

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> À partir de ASP.NET Core 3.0, MVC Views ou Razor `RazorCompileOnBuild` Pages `RazorCompileOnPublish` ne sont pas desservis par défaut si les propriétés ou MSBuild dans le fichier du projet sont désactivées. Les applications doivent ajouter une référence explicite au package [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation) si l’application s’appuie sur la compilation en temps d’exécution pour traiter les fichiers *.cshtml.*

::: moniker-end

| Éléments | Description |
| ----- | ----------- |
| `RazorGenerate` | Éléments d’élément (fichiers *.cshtml)* qui sont des entrées à la génération de code. |
| `RazorComponent` | Éléments d’élément *(fichiers .razor)* qui sont des entrées à la génération de code de composant Razor. |
| `RazorCompile` | Éléments*d’élément (fichiers .cs)* qui sont des entrées aux cibles de compilation Razor. Utilisez-le `ItemGroup` pour spécifier des fichiers supplémentaires à compiler dans l’assemblage Razor. |
| `RazorTargetAssemblyAttribute` | Composants d’élément utilisés pour générer le code d’attributs pour l’assembly Razor. Par exemple :  <br>`RazorAssemblyAttribute`<br>`Include="System.Reflection.AssemblyMetadataAttribute"`<br>`_Parameter1="BuildSource" _Parameter2="https://docs.microsoft.com/">` |
| `RazorEmbeddedResource` | Éléments d’élément ajoutés comme ressources intégrées à l’assemblage Razor généré. |

::: moniker range=">= aspnetcore-3.0"

| Propriété | Description |
| -------- | ----------- |
| `RazorTargetName` | Nom de fichier (sans extension) de l’assembly produit par Razor. |
| `RazorOutputPath` | Répertoire de sortie Razor. |
| `RazorCompileToolset` | Permet de déterminer l’ensemble d’outils utilisé pour générer l’assembly Razor. Les valeurs correctes sont `Implicit`, `RazorSDK` et `PrecompilationTool`. |
| [EnableDefaultContentItems](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | La valeur par défaut est `true`. Lorsque `true`, comprend *web.config*, *.json*, et *.cshtml* fichiers comme contenu dans le projet. Lorsqu’il `Microsoft.NET.Sdk.Web`est référencé via , les fichiers sous *wwwroot* et les fichiers config sont également inclus. |
| `EnableDefaultRazorGenerateItems` | Si la valeur est `true`, inclut les fichiers *.cshtml* des éléments `Content` dans les éléments `RazorGenerate`. |
| `GenerateRazorTargetAssemblyInfo` | Lorsque `true`, génère un fichier *.cs* `RazorAssemblyAttribute` contenant des attributs spécifiés par et inclut le fichier dans la sortie de compilation. |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | Si la valeur est `true`, ajoute un ensemble par défaut d’attributs d’assembly à `RazorAssemblyAttribute`. |
| `CopyRazorGenerateFilesToPublishDirectory` | Lorsque `true`, `RazorGenerate` copie des éléments (*.cshtml*) fichiers à l’annuaire de publication. En règle générale, les fichiers Razor ne sont pas requis pour une application publiée s’ils participent à la compilation à l’heure de build ou à l’heure de publication. La valeur par défaut est `false`. |
| `PreserveCompilationReferences` | Si la valeur est `true`, copie les éléments d’assembly de référence dans le répertoire de publication. En règle générale, les assemblages de référence ne sont pas nécessaires pour une application publiée si la compilation Razor se produit à l’heure de build ou à l’heure de publication. Définissez-le `true` pour savoir si votre application publiée nécessite une compilation en temps d’exécution. Par exemple, définissez `true` la valeur à si l’application modifie les fichiers *.cshtml* au moment de l’exécution ou utilise des vues intégrées. La valeur par défaut est `false`. |
| `IncludeRazorContentInPack` | Lorsque, `true`tous les éléments de contenu Razor (fichiers *.cshtml)* sont marqués pour l’inclusion dans le paquet NuGet généré. La valeur par défaut est `false`. |
| `EmbedRazorGenerateSources` | Si la valeur est `true`, ajoute des éléments RazorGenerate (*.cshtml*) comme fichiers incorporés à l’assembly Razor généré. La valeur par défaut est `false`. |
| `UseRazorBuildServer` | Si la valeur est `true`, utilise un processus de serveur de build persistant pour décharger le travail de génération de code. Utilise par défaut la valeur de `UseSharedCompilation`. |
| `GenerateMvcApplicationPartsAssemblyAttributes` | Lorsque `true`, le SDK génère des attributs supplémentaires utilisés par MVC au moment de l’exécution pour effectuer la découverte de pièce d’application. |
| `DefaultWebContentItemExcludes` | Un modèle de glisse pour les éléments d’élément qui doivent être exclus du groupe d’objets dans les `Content` projets ciblant le Web ou Razor SDK |
| `ExcludeConfigFilesFromBuildOutput` | Lorsque `true`, *.config* et *.json* fichiers ne sont pas copiés à l’annuaire de sortie de construction. |
| `AddRazorSupportForMvc` | Lorsque `true`, configure le Razor SDK pour ajouter la prise en charge de la configuration MVC qui est nécessaire lors de la construction d’applications contenant des vues MVC ou des pages Razor. Cette propriété est implicitement définie pour .NET Core 3.0 ou des projets ultérieurs ciblant le Web SDK |
| `RazorLangVersion` | La version de la langue de rasoir à cibler. |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

| Propriété | Description |
| -------- | ----------- |
| `RazorTargetName` | Nom de fichier (sans extension) de l’assembly produit par Razor. |
| `RazorOutputPath` | Répertoire de sortie Razor. |
| `RazorCompileToolset` | Permet de déterminer l’ensemble d’outils utilisé pour générer l’assembly Razor. Les valeurs correctes sont `Implicit`, `RazorSDK` et `PrecompilationTool`. |
| [EnableDefaultContentItems](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | La valeur par défaut est `true`. Lorsque `true`, comprend *web.config*, *.json*, et *.cshtml* fichiers comme contenu dans le projet. Lorsqu’il `Microsoft.NET.Sdk.Web`est référencé via , les fichiers sous *wwwroot* et les fichiers config sont également inclus. |
| `EnableDefaultRazorGenerateItems` | Si la valeur est `true`, inclut les fichiers *.cshtml* des éléments `Content` dans les éléments `RazorGenerate`. |
| `GenerateRazorTargetAssemblyInfo` | Lorsque `true`, génère un fichier *.cs* `RazorAssemblyAttribute` contenant des attributs spécifiés par et inclut le fichier dans la sortie de compilation. |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | Si la valeur est `true`, ajoute un ensemble par défaut d’attributs d’assembly à `RazorAssemblyAttribute`. |
| `CopyRazorGenerateFilesToPublishDirectory` | Lorsque `true`, `RazorGenerate` copie des éléments (*.cshtml*) fichiers à l’annuaire de publication. En règle générale, les fichiers Razor ne sont pas requis pour une application publiée s’ils participent à la compilation à l’heure de build ou à l’heure de publication. La valeur par défaut est `false`. |
| `CopyRefAssembliesToPublishDirectory` | Si la valeur est `true`, copie les éléments d’assembly de référence dans le répertoire de publication. En règle générale, les assemblages de référence ne sont pas nécessaires pour une application publiée si la compilation Razor se produit à l’heure de build ou à l’heure de publication. Définissez-le `true` pour savoir si votre application publiée nécessite une compilation en temps d’exécution. Par exemple, définissez `true` la valeur à si l’application modifie les fichiers *.cshtml* au moment de l’exécution ou utilise des vues intégrées. La valeur par défaut est `false`. |
| `IncludeRazorContentInPack` | Lorsque, `true`tous les éléments de contenu Razor (fichiers *.cshtml)* sont marqués pour l’inclusion dans le paquet NuGet généré. La valeur par défaut est `false`. |
| `EmbedRazorGenerateSources` | Si la valeur est `true`, ajoute des éléments RazorGenerate (*.cshtml*) comme fichiers incorporés à l’assembly Razor généré. La valeur par défaut est `false`. |
| `UseRazorBuildServer` | Si la valeur est `true`, utilise un processus de serveur de build persistant pour décharger le travail de génération de code. Utilise par défaut la valeur de `UseSharedCompilation`. |
| `GenerateMvcApplicationPartsAssemblyAttributes` | Lorsque `true`, le SDK génère des attributs supplémentaires utilisés par MVC au moment de l’exécution pour effectuer la découverte de pièce d’application. |
| `DefaultWebContentItemExcludes` | Un modèle de glisse pour les éléments d’élément qui doivent être exclus du groupe d’objets dans les `Content` projets ciblant le Web ou Razor SDK |
| `ExcludeConfigFilesFromBuildOutput` | Lorsque `true`, *.config* et *.json* fichiers ne sont pas copiés à l’annuaire de sortie de construction. |
| `AddRazorSupportForMvc` | Lorsque `true`, configure le Razor SDK pour ajouter la prise en charge de la configuration MVC qui est nécessaire lors de la construction d’applications contenant des vues MVC ou des pages Razor. Cette propriété est implicitement définie pour .NET Core 3.0 ou des projets ultérieurs ciblant le Web SDK |
| `RazorLangVersion` | La version de la langue de rasoir à cibler. |

::: moniker-end

Pour plus d’informations sur les propriétés, voir [propriétés MSBuild](/visualstudio/msbuild/msbuild-properties).

### <a name="targets"></a>Cibles

Le SDK Razor définit deux cibles principales :

* `RazorGenerate`&ndash; Code génère des fichiers `RazorGenerate` *.cs* à partir d’éléments d’élément. Utilisez `RazorGenerateDependsOn` la propriété pour spécifier des cibles supplémentaires qui peuvent s’exécuter avant ou après cette cible.
* `RazorCompile`&ndash; Compile les fichiers *générés .cs* dans un assemblage Razor. Utilisez `RazorCompileDependsOn` le pour spécifier des cibles supplémentaires qui peuvent s’exécuter avant ou après cette cible.
* `RazorComponentGenerate`&ndash; Code génère des fichiers `RazorComponent` *.cs* pour les éléments d’élément. Utilisez `RazorComponentGenerateDependsOn` la propriété pour spécifier des cibles supplémentaires qui peuvent s’exécuter avant ou après cette cible.

### <a name="runtime-compilation-of-razor-views"></a>Compilation au moment du runtime des vues Razor

* Par défaut, le SDK Razor ne publie pas les assemblys de référence nécessaires à l’exécution de la compilation au moment du runtime. Cela entraîne des échecs de compilation quand le modèle d’application s’appuie sur la compilation au moment du runtime&mdash;par exemple, l’application utilise des vues incorporées ou change les vues une fois l’application publiée. Définissez `CopyRefAssembliesToPublishDirectory` sur `true` pour continuer à publier les assemblys de référence.

* Pour une application web, assurez-vous `Microsoft.NET.Sdk.Web` que votre application cible le SDK.

## <a name="razor-language-version"></a>Version en langue Razor

Lorsque vous `Microsoft.NET.Sdk.Web` ciblez le SDK, la version en langue Razor est déduite de la version cadre cible de l’application. Pour les projets ciblant le `Microsoft.NET.Sdk.Razor` SDK ou dans le cas rare où l’application nécessite une version `<RazorLangVersion>` de type Razor différente de la valeur déduite, une version peut être configurée en définissant la propriété dans le fichier de projet de l’application :

```xml
<PropertyGroup>
  <RazorLangVersion>{VERSION}</RazorLangVersion>
</PropertyGroup>
```

La version linguistique de Razor est étroitement intégrée à la version du temps d’exécution pour laquelle elle a été conçue. Cibler une version linguistique qui n’est pas conçue pour le temps d’exécution n’est pas pris en compte et produit probablement des erreurs de construction.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Ajouts au format csproj pour .NET Core](/dotnet/core/tools/csproj)
* [Éléments communs des projets MSBuild](/visualstudio/msbuild/common-msbuild-project-items)
