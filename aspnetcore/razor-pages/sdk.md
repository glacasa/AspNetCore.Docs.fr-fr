---
title: SDK Razor ASP.net Core
author: Rick-Anderson
description: Découvrez comment Razor les Pages de ASP.net Core rendent le codage des scénarios orientés page plus facile et plus productif que l’utilisation de MVC.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 03/26/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: razor-pages/sdk
ms.openlocfilehash: a49f183c4c037a1654e79bdb672b758684137cbe
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776732"
---
# <a name="aspnet-core-razor-sdk"></a>SDK Razor ASP.NET Core

Par [Rick Anderson](https://twitter.com/RickAndMSFT)

## <a name="overview"></a>Vue d’ensemble

[!INCLUDE[](~/includes/2.1-SDK.md)] inclut le SDK MSBuild `Microsoft.NET.Sdk.Razor` (SDK Razor). Le SDK Razor :

::: moniker range=">= aspnetcore-3.0"

* Est requis pour générer, empaqueter et publier des projets contenant des fichiers [Razor](xref:mvc/views/razor) pour ASP.net Core projets basés sur MVC ou [éblouissants](xref:blazor/index) .
* Comprend un ensemble de cibles, de propriétés et d’éléments prédéfinis qui permettent de personnaliser la compilation des fichiers Razor (*. cshtml* ou *. Razor*).

Le kit de développement `Content` logiciel ( `Include` SDK) Razor contient `**\*.cshtml` des `**\*.razor` éléments dont les attributs sont définis sur les modèles et globbing. Les fichiers correspondants sont publiés.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* Normalise l’expérience liée à la génération, à l’empaquetage et à la publication de projets contenant des fichiers [Razor](xref:mvc/views/razor) pour les projets ASP.NET Core basés sur MVC.
* Comprend un ensemble de cibles, de propriétés et d’éléments prédéfinis qui permettent de personnaliser la compilation des fichiers Razor.

Le kit de développement logiciel `Content` (SDK) `Include` Razor comprend un élément `**\*.cshtml` dont l’attribut a pour valeur le modèle globbing. Les fichiers correspondants sont publiés.

::: moniker-end

## <a name="prerequisites"></a>Prérequis

[!INCLUDE[](~/includes/2.1-SDK.md)]

## <a name="use-the-razor-sdk"></a>Utiliser le kit de développement logiciel (SDK) Razor

La plupart des applications Web ne sont pas requises pour référencer explicitement le kit de développement logiciel (SDK) Razor.

::: moniker range=">= aspnetcore-3.0"

Pour utiliser le kit de développement logiciel (SDK) Razor afin de générer des bibliothèques de classes contenant des vues Razor ou des Razor Pages, nous vous recommandons de commencer par le modèle de projet Bibliothèque de classes Razor (RCL). Un RCL utilisé pour générer au minimum des fichiers éblouissants (*. Razor*) requiert une référence au package [Microsoft. AspNetCore. Components](https://www.nuget.org/packages/Microsoft.AspNetCore.Components) . Un RCL qui est utilisé pour créer des vues ou des pages Razor (fichiers *. cshtml* ) nécessite au `netcoreapp3.0` minimum le ciblage ou une `FrameworkReference` version ultérieure et un vers le [Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) dans son fichier projet.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Pour utiliser le SDK Razor pour générer des bibliothèques de classes contenant des vues ou des pages Razor :

* Utilisez `Microsoft.NET.Sdk.Razor` à la place de `Microsoft.NET.Sdk` :

  ```xml
  <Project SDK="Microsoft.NET.Sdk.Razor">
    <!-- omitted for brevity -->
  </Project>
  ```

* En règle générale, une référence `Microsoft.AspNetCore.Mvc` de package à est requise pour recevoir des dépendances supplémentaires qui sont requises pour générer et compiler des vues Razor pages et Razor. Au minimum, votre projet doit ajouter des références de package à :

  * `Microsoft.AspNetCore.Razor.Design`
  * `Microsoft.AspNetCore.Mvc.Razor.Extensions`
  * `Microsoft.AspNetCore.Mvc.Razor`

  Le `Microsoft.AspNetCore.Razor.Design` package fournit les tâches et les cibles de compilation Razor pour le projet.

  Les packages précédents sont inclus dans `Microsoft.AspNetCore.Mvc`. Le balisage suivant montre un fichier projet qui utilise le kit de développement logiciel (SDK) Razor pour générer des fichiers Razor pour une application ASP.NET Core Razor Pages :

  [!code-xml[](sdk/sample/RazorSDK.csproj)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

> [!WARNING]
> Les `Microsoft.AspNetCore.Razor.Design` packages `Microsoft.AspNetCore.Mvc.Razor.Extensions` et sont inclus dans le sous- [package Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app). Toutefois, la référence de package `Microsoft.AspNetCore.App` sans version fournit un package à l’application qui n’inclut pas la dernière version de `Microsoft.AspNetCore.Razor.Design`. Les projets doivent référencer une version `Microsoft.AspNetCore.Razor.Design` cohérente `Microsoft.AspNetCore.Mvc`de (ou) afin que les correctifs les plus récents au moment de la génération pour Razor soient inclus. Pour plus d’informations, consultez [ce problème GitHub](https://github.com/aspnet/Razor/issues/2553).

::: moniker-end

### <a name="properties"></a>Propriétés

Les propriétés suivantes contrôlent le comportement du SDK Razor dans le cadre d’une build de projet :

* `RazorCompileOnBuild`&ndash; Quand `true`, compile et émet l’assembly Razor dans le cadre de la génération du projet. La valeur par défaut est `true`.
* `RazorCompileOnPublish`&ndash; Quand `true`, compile et émet l’assembly Razor dans le cadre de la publication du projet. La valeur par défaut est `true`.

Les propriétés et les éléments du tableau suivant sont utilisés pour configurer les entrées et la sortie vers le kit de développement logiciel (SDK) Razor.

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> À compter de ASP.NET Core 3,0, les vues MVC ou Razor Pages ne sont pas servies par défaut si les `RazorCompileOnBuild` propriétés ou `RazorCompileOnPublish` MSBuild dans le fichier projet sont désactivées. Les applications doivent ajouter une référence explicite au package [Microsoft. AspNetCore. Mvc. Razor. RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation) si l’application s’appuie sur la compilation au moment de l’exécution pour traiter les fichiers *. cshtml* .

::: moniker-end

| Éléments | Description |
| ----- | ----------- |
| `RazorGenerate` | Éléments Item (fichiers *. cshtml* ) qui sont des entrées de la génération de code. |
| `RazorComponent` | Éléments Item (fichiers *. Razor* ) qui sont des entrées de la génération de code du composant Razor. |
| `RazorCompile` | Éléments Item (fichiers *. cs* ) qui sont des entrées dans les cibles de compilation Razor. Utilisez cette `ItemGroup` valeur pour spécifier des fichiers supplémentaires à compiler dans l’assembly Razor. |
| `RazorTargetAssemblyAttribute` | Composants d’élément utilisés pour générer le code d’attributs pour l’assembly Razor. Par exemple :  <br>`RazorAssemblyAttribute`<br>`Include="System.Reflection.AssemblyMetadataAttribute"`<br>`_Parameter1="BuildSource" _Parameter2="https://docs.microsoft.com/">` |
| `RazorEmbeddedResource` | Éléments Item ajoutés en tant que ressources incorporées à l’assembly Razor généré. |

::: moniker range=">= aspnetcore-3.0"

| Propriété | Description |
| -------- | ----------- |
| `RazorTargetName` | Nom de fichier (sans extension) de l’assembly produit par Razor. |
| `RazorOutputPath` | Répertoire de sortie Razor. |
| `RazorCompileToolset` | Permet de déterminer l’ensemble d’outils utilisé pour générer l’assembly Razor. Les valeurs correctes sont `Implicit`, `RazorSDK` et `PrecompilationTool`. |
| [EnableDefaultContentItems](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | La valeur par défaut est `true`. Lorsque `true`, comprend des fichiers *Web. config*, *. JSON*et *. cshtml* en tant que contenu dans le projet. Lorsqu’ils sont référencés via `Microsoft.NET.Sdk.Web`, les fichiers situés sous les fichiers *wwwroot* et config sont également inclus. |
| `EnableDefaultRazorGenerateItems` | Si la valeur est `true`, inclut les fichiers *.cshtml* des éléments `Content` dans les éléments `RazorGenerate`. |
| `GenerateRazorTargetAssemblyInfo` | Lorsque `true`, génère un fichier *. cs* contenant les attributs spécifiés par `RazorAssemblyAttribute` et comprend le fichier dans la sortie de compilation. |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | Si la valeur est `true`, ajoute un ensemble par défaut d’attributs d’assembly à `RazorAssemblyAttribute`. |
| `CopyRazorGenerateFilesToPublishDirectory` | Lorsque `true`la valeur `RazorGenerate` est, copie les fichiers d’éléments (*. cshtml*) dans le répertoire de publication. En règle générale, les fichiers Razor ne sont pas requis pour une application publiée s’ils participent à la compilation au moment de la génération ou de la publication. La valeur par défaut est `false`. |
| `PreserveCompilationReferences` | Si la valeur est `true`, copie les éléments d’assembly de référence dans le répertoire de publication. En général, les assemblys de référence ne sont pas requis pour une application publiée si la compilation Razor se produit au moment de la génération ou de la publication. Affectez `true` la valeur si votre application publiée requiert la compilation du Runtime. Par exemple, définissez la valeur sur `true` si l’application modifie les fichiers *. cshtml* au moment de l’exécution ou utilise des vues incorporées. La valeur par défaut est `false`. |
| `IncludeRazorContentInPack` | Lorsque `true`, tous les éléments de contenu Razor (fichiers *. cshtml* ) sont marqués pour être inclus dans le package NuGet généré. La valeur par défaut est `false`. |
| `EmbedRazorGenerateSources` | Si la valeur est `true`, ajoute des éléments RazorGenerate (*.cshtml*) comme fichiers incorporés à l’assembly Razor généré. La valeur par défaut est `false`. |
| `UseRazorBuildServer` | Si la valeur est `true`, utilise un processus de serveur de build persistant pour décharger le travail de génération de code. Utilise par défaut la valeur de `UseSharedCompilation`. |
| `GenerateMvcApplicationPartsAssemblyAttributes` | Lorsque `true`la valeur est, le kit de développement logiciel (SDK) génère des attributs supplémentaires utilisés par MVC au moment de l’exécution pour effectuer la découverte des parties |
| `DefaultWebContentItemExcludes` | Modèle globbing pour les éléments Item qui doivent être exclus du `Content` groupe d’éléments dans les projets ciblant le Web ou le kit de développement logiciel (SDK) Razor |
| `ExcludeConfigFilesFromBuildOutput` | Lorsque `true`les fichiers *. config* et *. JSON* ne sont pas copiés dans le répertoire de sortie de la génération. |
| `AddRazorSupportForMvc` | Lorsque `true`la valeur est, configure le kit de développement logiciel (SDK) Razor afin d’ajouter la prise en charge de la configuration MVC requise lors de la génération d’applications contenant des vues MVC ou des Razor pages. Cette propriété est implicitement définie pour les projets .NET Core 3,0 ou ultérieur ciblant le kit de développement logiciel (SDK) Web |
| `RazorLangVersion` | Version du langage Razor à cibler. |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

| Propriété | Description |
| -------- | ----------- |
| `RazorTargetName` | Nom de fichier (sans extension) de l’assembly Razorgénéré par. |
| `RazorOutputPath` | Répertoire Razor de sortie. |
| `RazorCompileToolset` | Utilisé pour déterminer l’ensemble d’outils utilisé pour Razor générer l’assembly. Les valeurs correctes sont `Implicit`, `RazorSDK` et `PrecompilationTool`. |
| [EnableDefaultContentItems](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | La valeur par défaut est `true`. Lorsque `true`, comprend des fichiers *Web. config*, *. JSON*et *. cshtml* en tant que contenu dans le projet. Lorsqu’ils sont référencés via `Microsoft.NET.Sdk.Web`, les fichiers situés sous les fichiers *wwwroot* et config sont également inclus. |
| `EnableDefaultRazorGenerateItems` | Si la valeur est `true`, inclut les fichiers *.cshtml* des éléments `Content` dans les éléments `RazorGenerate`. |
| `GenerateRazorTargetAssemblyInfo` | Lorsque `true`, génère un fichier *. cs* contenant les attributs spécifiés par `RazorAssemblyAttribute` et comprend le fichier dans la sortie de compilation. |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | Si la valeur est `true`, ajoute un ensemble par défaut d’attributs d’assembly à `RazorAssemblyAttribute`. |
| `CopyRazorGenerateFilesToPublishDirectory` | Lorsque `true`la valeur `RazorGenerate` est, copie les fichiers d’éléments (*. cshtml*) dans le répertoire de publication. En règle Razor générale, les fichiers ne sont pas requis pour une application publiée s’ils participent à la compilation au moment de la génération ou de la publication. La valeur par défaut est `false`. |
| `CopyRefAssembliesToPublishDirectory` | Si la valeur est `true`, copie les éléments d’assembly de référence dans le répertoire de publication. En général, les assemblys de référence ne sont pas requis Razor pour une application publiée si la compilation se produit au moment de la génération ou de la publication. Affectez `true` la valeur si votre application publiée requiert la compilation du Runtime. Par exemple, définissez la valeur sur `true` si l’application modifie les fichiers *. cshtml* au moment de l’exécution ou utilise des vues incorporées. La valeur par défaut est `false`. |
| `IncludeRazorContentInPack` | Lorsque `true`, tous Razor les éléments de contenu (fichiers *. cshtml* ) sont marqués pour être inclus dans le package NuGet généré. La valeur par défaut est `false`. |
| `EmbedRazorGenerateSources` | Lorsque `true`la valeur est, ajoute des éléments RazorGenerate (*. cshtml*) comme fichiers Razor incorporés à l’assembly généré. La valeur par défaut est `false`. |
| `UseRazorBuildServer` | Si la valeur est `true`, utilise un processus de serveur de build persistant pour décharger le travail de génération de code. Utilise par défaut la valeur de `UseSharedCompilation`. |
| `GenerateMvcApplicationPartsAssemblyAttributes` | Lorsque `true`la valeur est, le kit de développement logiciel (SDK) génère des attributs supplémentaires utilisés par MVC au moment de l’exécution pour effectuer la découverte des parties |
| `DefaultWebContentItemExcludes` | Modèle globbing pour les éléments Item qui doivent être exclus du groupe d' `Content` éléments dans les projets ciblant le Web Razor ou le kit de développement logiciel (SDK) |
| `ExcludeConfigFilesFromBuildOutput` | Lorsque `true`les fichiers *. config* et *. JSON* ne sont pas copiés dans le répertoire de sortie de la génération. |
| `AddRazorSupportForMvc` | Lorsque `true`la valeur est, configure le Razor Kit de développement logiciel (SDK) pour ajouter la prise en charge de la configuration Razor MVC requise lors de la génération d’applications contenant des vues ou des pages Mvc. Cette propriété est implicitement définie pour les projets .NET Core 3,0 ou ultérieur ciblant le kit de développement logiciel (SDK) Web |
| `RazorLangVersion` | Version du Razor langage à cibler. |

::: moniker-end

Pour plus d’informations sur les propriétés, consultez [propriétés MSBuild](/visualstudio/msbuild/msbuild-properties).

### <a name="targets"></a>Cibles

Le Razor Kit de développement logiciel (SDK) définit deux cibles principales :

* `RazorGenerate`&ndash; Le code génère des fichiers *. cs* à partir d' `RazorGenerate` éléments Item. Utilisez la `RazorGenerateDependsOn` propriété pour spécifier des cibles supplémentaires qui peuvent s’exécuter avant ou après cette cible.
* `RazorCompile`&ndash; Compile les fichiers *. cs* générés dans dans un Razor assembly. Utilisez `RazorCompileDependsOn` pour spécifier des cibles supplémentaires qui peuvent s’exécuter avant ou après cette cible.
* `RazorComponentGenerate`&ndash; Le code génère des fichiers *. cs* pour les `RazorComponent` éléments Item. Utilisez la `RazorComponentGenerateDependsOn` propriété pour spécifier des cibles supplémentaires qui peuvent s’exécuter avant ou après cette cible.

### <a name="runtime-compilation-of-razor-views"></a>Compilation du runtime Razor des vues

* Par défaut, le Razor Kit de développement logiciel (SDK) ne publie pas les assemblys de référence requis pour effectuer la compilation du Runtime. Cela entraîne des échecs de compilation quand le modèle d’application s’appuie sur la compilation au moment du runtime&mdash;par exemple, l’application utilise des vues incorporées ou change les vues une fois l’application publiée. Définissez `CopyRefAssembliesToPublishDirectory` sur `true` pour continuer à publier les assemblys de référence.

* Pour une application Web, vérifiez que votre application cible le `Microsoft.NET.Sdk.Web` Kit de développement logiciel (SDK).

## <a name="razor-language-version"></a>Razorversion du langage

Lorsque vous ciblez le `Microsoft.NET.Sdk.Web` Kit Razor de développement logiciel (SDK), la version du langage est déduite de la version du Framework cible de l’application. Pour les projets ciblant le `Microsoft.NET.Sdk.Razor` Kit de développement logiciel (SDK) ou dans le Razor cas rare où l’application requiert une version de langue différente de la valeur déduite, une version peut être configurée en définissant la `<RazorLangVersion>` propriété dans le fichier projet de l’application :

```xml
<PropertyGroup>
  <RazorLangVersion>{VERSION}</RazorLangVersion>
</PropertyGroup>
```

Razorla version de langage de est étroitement intégrée à la version du runtime pour laquelle elle a été générée. Le ciblage d’une version de langage qui n’est pas conçue pour le runtime n’est pas pris en charge et génère probablement des erreurs de Build.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Ajouts au format csproj pour .NET Core](/dotnet/core/tools/csproj)
* [Éléments communs des projets MSBuild](/visualstudio/msbuild/common-msbuild-project-items)
