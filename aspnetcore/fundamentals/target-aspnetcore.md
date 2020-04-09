---
title: Utiliser ASP.NET API de base dans une bibliothèque de classe
author: scottaddie
description: Apprenez à utiliser ASP.NET API de base dans une bibliothèque de classe.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/16/2019
no-loc:
- Blazor
uid: fundamentals/target-aspnetcore
ms.openlocfilehash: 5374d7eec4334223a4bba7ee26cb6e2f208ed20b
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977195"
---
# <a name="use-aspnet-core-apis-in-a-class-library"></a>Utiliser ASP.NET API de base dans une bibliothèque de classe

Par [Scott Addie](https://github.com/scottaddie)

Ce document fournit des conseils pour l’utilisation des API de base ASP.NET dans une bibliothèque de classe. Pour tous les autres conseils de bibliothèque, voir [les conseils de bibliothèque Open-source](/dotnet/standard/library-guidance/).

## <a name="determine-which-aspnet-core-versions-to-support"></a>Déterminer quelles ASP.NET versions Core à prendre en charge

ASP.NET Core adhère à la [politique de soutien de base .NET](https://dotnet.microsoft.com/platform/support/policy/dotnet-core). Consultez la politique de support pour déterminer quelles ASP.NET versions Core à prendre en charge dans une bibliothèque. Une bibliothèque doit :

* Faites un effort pour prendre en charge toutes les versions ASP.NET Core classées comme *support à long terme* (LTS).
* Ne vous sentez pas obligé de prendre en charge ASP.NET versions Core classées *en fin de vie* (EOL).

Au fur et à mesure que les versions préliminaires de ASP.NET Core sont disponibles, des modifications de rupture sont affichées dans le référentiel [GitHub aspnet/Announcements.](https://github.com/aspnet/Announcements/issues) Les tests de compatibilité des bibliothèques peuvent être effectués au fur et à mesure que des fonctionnalités-cadres sont en cours d’élaboration.

## <a name="use-the-aspnet-core-shared-framework"></a>Utiliser le cadre partagé ASP.NET Core

Avec la sortie de .NET Core 3.0, de nombreux assemblages ASP.NET Core ne sont plus publiés sur NuGet sous forme de paquets. Au lieu de cela, `Microsoft.AspNetCore.App` les assemblages sont inclus dans le cadre partagé, qui est installé avec le .NET Core SDK et les installateurs de temps d’exécution. Pour une liste de paquets qui ne sont plus publiés, voir [Supprimer les références de paquets obsolètes](xref:migration/22-to-30#remove-obsolete-package-references).

À partir de .NET Core 3.0, les projets utilisant le `Microsoft.NET.Sdk.Web` MSBuild SDK renvoie implicitement le cadre partagé. Les projets utilisant le `Microsoft.NET.Sdk` SDK ou `Microsoft.NET.Sdk.Razor` SDK doivent faire référence ASP.NET Core pour utiliser ASP.NET API de base dans le cadre partagé.

Pour faire référence ASP.NET Core, `<FrameworkReference>` ajoutez l’élément suivant à votre dossier de projet :

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj?highlight=8)]

Référencement ASP.NET Core de cette manière n’est pris en charge que pour les projets ciblant .NET Core 3.x.

## <a name="include-blazor-extensibility"></a>Inclure l’extéabilité blazor

Blazor prend en charge les modèles [d’hébergement](xref:blazor/hosting-models)WebAssembly (WASM) et Server . À moins qu’il n’y ait une raison spécifique de ne pas le faire, une bibliothèque [de composants Razor](xref:blazor/components) devrait prendre en charge les deux modèles d’hébergement. Une bibliothèque de composants Razor doit utiliser le [Microsoft.NET.Sdk.Razor SDK](xref:razor-pages/sdk).

### <a name="support-both-hosting-models"></a>Prendre en charge les deux modèles d’hébergement

Pour prendre en charge la consommation de composants Razor provenant des projets [Blazor Server](xref:blazor/hosting-models#blazor-server) et [Blazor WASM,](xref:blazor/hosting-models#blazor-webassembly) utilisez les instructions suivantes pour votre éditeur.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Utilisez le modèle de projet **Razor Class Library.** Les pages de support et la case **ci-contre vues** du modèle doivent être désélectionnés.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Exécuter la commande suivante dans le terminal intégré :

```dotnetcli
dotnet new razorclasslib
```

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

Utilisez le modèle de projet **Razor Class Library.**

---

Le projet généré à partir du modèle fait les choses suivantes :

* Cibles .NET Standard 2.0.
* Affecte la valeur `RazorLangVersion` à la propriété `3.0`. `3.0`est la valeur par défaut pour .NET Core 3.x.
* Ajoute les références de paquets suivantes :
  * [Microsoft.AspNetCore.Composants](https://www.nuget.org/packages/Microsoft.AspNetCore.Components)
  * [Microsoft.AspNetCore.Components.Web (en anglais seulement)](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Web)

Par exemple :

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-razor-components-library.csproj)]

### <a name="support-a-specific-hosting-model"></a>Prendre en charge un modèle d’hébergement spécifique

Il est beaucoup moins fréquent de prendre en charge un seul modèle d’hébergement Blazor. À titre d’exemple, pour soutenir la consommation de composants Razor des projets [Blazor Server](xref:blazor/hosting-models#blazor-server) seulement :

* Cible .NET Core 3.x.
* Ajoutez `<FrameworkReference>` un élément pour le cadre partagé.

Par exemple :

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-razor-components-library.csproj)]

Pour plus d’informations sur les bibliothèques contenant des composants Razor, voir [ASP.NET core Razor composants classe bibliothèques](xref:blazor/class-libraries).

## <a name="include-mvc-extensibility"></a>Inclure l’extéabilité de MVC

Cette section présente les recommandations à l’égard des bibliothèques qui comprennent :

* [Vues de rasoir ou pages de rasoir](#razor-views-or-razor-pages)
* [Tag Helpers](#tag-helpers)
* [Composants de vues](#view-components)

Cette section ne traite pas de multi-ciblage pour prendre en charge plusieurs versions de MVC. Pour obtenir des conseils sur le support de plusieurs versions ASP.NET Core, voir [Support plusieurs versions ASP.NET Core](#support-multiple-aspnet-core-versions).

### <a name="razor-views-or-razor-pages"></a>Vues de rasoir ou pages de rasoir

Un projet qui comprend [des vues Razor](xref:mvc/views/overview) ou Des pages [Razor](xref:razor-pages/index) doit utiliser le [Microsoft.NET.Sdk.Razor SDK](xref:razor-pages/sdk).

Si le projet cible .NET Core 3.x, il nécessite :

* Une `AddRazorSupportForMvc` propriété MSBuild `true`réglé à .
* Un `<FrameworkReference>` élément pour le cadre partagé.

Le modèle de projet **Razor Class Library** répond aux exigences précédentes pour les projets ciblant .NET Core 3.x. Utilisez les instructions suivantes pour votre éditeur.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Utilisez le modèle de projet **Razor Class Library.** Les pages **de support et** la case ci-pour-vue du modèle doivent être sélectionnées.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Exécuter la commande suivante dans le terminal intégré :

```dotnetcli
dotnet new razorclasslib -s
```

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

Aucun modèle de projet de support pour le moment.

---

Par exemple :

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-razor-views-pages-library.csproj)]

Si le projet cible .NET Standard à la place, une référence de paquet [Microsoft.AspNetCore.Mvc](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc) est nécessaire. Le `Microsoft.AspNetCore.Mvc` paquet est passé dans le cadre commun dans ASP.NET Core 3.0 et n’est donc plus publié. Par exemple :

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-razor-views-pages-library.csproj?highlight=8)]

### <a name="tag-helpers"></a>Tag Helpers

Un projet qui comprend Tag `Microsoft.NET.Sdk` [Helpers](xref:mvc/views/tag-helpers/intro) devrait utiliser le SDK. Si vous ciblez .NET Core 3.x, ajoutez un `<FrameworkReference>` élément pour le cadre partagé. Par exemple :

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj)]

Si vous ciblez .NET Standard (pour prendre en charge les versions plus tôt que ASP.NET Core 3.x), ajoutez une référence de paquet à [Microsoft.AspNetCore.Mvc.Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor). Le `Microsoft.AspNetCore.Mvc.Razor` paquet est entré dans le cadre commun et n’est donc plus publié. Par exemple :

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-tag-helpers-library.csproj)]

### <a name="view-components"></a>Composants de vue

Un projet qui comprend des `Microsoft.NET.Sdk` composants [View](xref:mvc/views/view-components) devrait utiliser le SDK. Si vous ciblez .NET Core 3.x, ajoutez un `<FrameworkReference>` élément pour le cadre partagé. Par exemple :

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj)]

Si vous ciblez .NET Standard (pour prendre en charge les versions plus tôt que ASP.NET Core 3.x), ajoutez une référence de paquet à [Microsoft.AspNetCore.Mvc.ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures). Le `Microsoft.AspNetCore.Mvc.ViewFeatures` paquet est entré dans le cadre commun et n’est donc plus publié. Par exemple :

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-view-components-library.csproj)]

## <a name="support-multiple-aspnet-core-versions"></a>Prendre en charge plusieurs versions ASP.NET Core

Multi-ciblage est nécessaire pour l’auteur d’une bibliothèque qui prend en charge plusieurs variantes de ASP.NET Core. Considérez un scénario dans lequel une bibliothèque Tag Helpers doit prendre en charge les variantes suivantes ASP.NET Core :

* ASP.NET Core 2.1 targeting .NET Framework 4.6.1
* ASP.NET Cœur 2.x ciblage .NET Core 2.x
* ASP.NET Cœur 3.x ciblage .NET Core 3.x

Le dossier de projet suivant `TargetFrameworks` prend en charge ces variantes via la propriété :

[!code-xml[](target-aspnetcore/samples/multi-tfm/recommended-tag-helpers-library.csproj)]

Avec le fichier de projet précédent :

* Le `Markdig` forfait est ajouté pour tous les consommateurs.
* Une référence à [Microsoft.AspNetCore.Mvc.Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor) est ajoutée pour les consommateurs ciblant .NET Framework 4.6.1 ou plus tard ou .NET Core 2.x. La version 2.1.0 du paquet fonctionne avec ASP.NET Core 2.2 en raison de la compatibilité vers l’arrière.
* Le cadre partagé est référencé pour les consommateurs ciblant .NET Core 3.x. Le `Microsoft.AspNetCore.Mvc.Razor` paquet est inclus dans le cadre partagé.

Alternativement, .NET Standard 2.0 pourrait être ciblé au lieu de cibler à la fois .NET Core 2.1 et .NET Framework 4.6.1:

[!code-xml[](target-aspnetcore/samples/multi-tfm/alternative-tag-helpers-library.csproj?highlight=4)]

Avec le dossier de projet précédent, les mises en garde suivantes existent :

* Étant donné que la bibliothèque ne contient que Tag Helpers, il est plus simple de cibler les plates-formes spécifiques sur lesquelles ASP.NET Core fonctionne: .NET Core et .NET Framework. Tag Helpers ne peut pas être utilisé par d’autres cadres cibles compatibles .NET Standard 2.0 tels que Unity, UWP et Xamarin.
* L’utilisation de .NET Standard 2.0 dans .NET Framework entraîne certains problèmes qui ont été résolus dans .NET Framework 4.7.2. Vous pouvez améliorer l’expérience pour les consommateurs en utilisant .NET Framework 4.6.1 à 4.7.1 en ciblant .NET Framework 4.6.1.

Si votre bibliothèque doit appeler des API spécifiques à la plate-forme, ciblez des implémentations .NET spécifiques au lieu de .NET Standard. Pour plus d’informations, voir [Multi-targeting](/dotnet/standard/library-guidance/cross-platform-targeting#multi-targeting).

## <a name="use-an-api-that-hasnt-changed"></a>Utilisez une API qui n’a pas changé

Imaginez un scénario dans lequel vous améliorez une bibliothèque middleware de .NET Core 2.2 à 3.0. Les API de middleware core ASP.NET utilisées dans la bibliothèque n’ont pas changé entre ASP.NET Core 2.2 et 3.0. Pour continuer à soutenir la bibliothèque middleware en .NET Core 3.0, prenez les mesures suivantes :

* Suivez les [conseils standard de la bibliothèque](/dotnet/standard/library-guidance/).
* Ajoutez une référence de paquet pour le paquet NuGet de chaque API si l’assemblage correspondant n’existe pas dans le cadre partagé.

## <a name="use-an-api-that-changed"></a>Utilisez une API qui a changé

Imaginez un scénario dans lequel vous améliorez une bibliothèque de .NET Core 2.2 à .NET Core 3.0. Une ASP.NET’API de base utilisée dans la bibliothèque a un [changement de rupture](/dotnet/core/compatibility/breaking-changes) dans ASP.NET Core 3.0. Examinez si la bibliothèque peut être réécrite pour ne pas utiliser l’API cassée dans toutes les versions.

Si vous pouvez réécrire la bibliothèque, faites-le et continuez à cibler un cadre cible antérieur (par exemple, .NET Standard 2.0 ou .NET Framework 4.6.1) avec des références de paquet.

Si vous ne pouvez pas réécrire la bibliothèque, prenez les mesures suivantes :

* Ajoutez une cible pour .NET Core 3.0.
* Ajoutez `<FrameworkReference>` un élément pour le cadre partagé.
* Utilisez la [directive #if préprocesseur](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) avec le symbole du cadre cible approprié pour compiler sous condition le code.

Par exemple, les lectures synchrones et les écritures sur les flux de demande et de réponse HTTP sont désactivées par défaut à partir de ASP.NET Core 3.0. ASP.NET Core 2.2 prend en charge le comportement synchrone par défaut. Considérez une bibliothèque middleware dans laquelle les lectures synchrones et les écrits devraient être activés où I / O se produit. La bibliothèque doit inclure le code pour activer les caractéristiques synchrones dans la directive préprocesseur appropriée. Par exemple :

[!code-csharp[](target-aspnetcore/samples/middleware.cs?highlight=9-24)]

## <a name="use-an-api-introduced-in-30"></a>Utiliser une API introduite dans 3.0

Imaginez que vous souhaitez utiliser une API de base ASP.NET qui a été introduite dans ASP.NET Core 3.0. Considérez les questions suivantes :

1. La bibliothèque a-t-elle besoin fonctionnellement de la nouvelle API?
1. La bibliothèque peut-elle implémenter cette fonctionnalité d’une manière différente ?

Si la bibliothèque nécessite fonctionnellement l’API et qu’il n’y a aucun moyen de la mettre en œuvre à un niveau bas :

* Cible .NET Core 3.x seulement.
* Ajoutez `<FrameworkReference>` un élément pour le cadre partagé.

Si la bibliothèque peut implémenter la fonctionnalité d’une manière différente :

* Ajouter .NET Core 3.x comme cadre cible.
* Ajoutez `<FrameworkReference>` un élément pour le cadre partagé.
* Utilisez la [directive #if préprocesseur](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) avec le symbole du cadre cible approprié pour compiler sous condition le code.

Par exemple, l’aide Tag <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> suivante utilise l’interface introduite dans ASP.NET Core 3.0. Les consommateurs ciblant .NET Core 3.0 `NETCOREAPP3_0` exécutent la trajectoire de code définie par le symbole du cadre cible. Le type de paramètre constructeur de <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> Tag Helper passe à .NET Core 2.1 et .NET Framework 4.6.1 consommateurs. Ce changement était nécessaire parce que ASP.NET Core `IHostingEnvironment` 3.0 a été qualifié d’obsolète et recommandé `IWebHostEnvironment` comme remplaçant.

```csharp
[HtmlTargetElement("script", Attributes = "asp-inline")]
public class ScriptInliningTagHelper : TagHelper
{
    private readonly IFileProvider _wwwroot;

#if NETCOREAPP3_0
    public ScriptInliningTagHelper(IWebHostEnvironment env)
#else
    public ScriptInliningTagHelper(IHostingEnvironment env)
#endif
    {
        _wwwroot = env.WebRootFileProvider;
    }

    // code omitted for brevity
}
```

Le fichier de projet multi-ciblés suivant prend en charge ce scénario Tag Helper :

[!code-xml[](target-aspnetcore/samples/multi-tfm/recommended-tag-helpers-library.csproj)]

## <a name="use-an-api-removed-from-the-shared-framework"></a>Utiliser une API supprimée du cadre partagé

Pour utiliser un assemblage ASP.NET Core qui a été retiré du cadre partagé, ajoutez la référence de paquet appropriée. Pour une liste de paquets supprimés du cadre partagé dans ASP.NET Core 3.0, voir [Supprimer les références de paquets obsolètes](xref:migration/22-to-30#remove-obsolete-package-references).

Par exemple, pour ajouter le client web API:

```xml
<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <TargetFramework>netcoreapp3.0</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <FrameworkReference Include="Microsoft.AspNetCore.App" />
  </ItemGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNet.WebApi.Client" Version="5.2.7" />
  </ItemGroup>

</Project>
```

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:razor-pages/ui-class>
* <xref:blazor/class-libraries>
* [Prise en charge des implémentations de .NET](/dotnet/standard/net-standard#net-implementation-support)
* [.NET politiques de soutien](https://dotnet.microsoft.com/platform/support/policy)
