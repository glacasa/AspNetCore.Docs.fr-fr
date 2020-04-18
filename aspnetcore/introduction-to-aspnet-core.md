---
title: Présentation d’ASP.NET Core
author: rick-anderson
description: Obtenez une introduction à ASP.NET Core, un cadre interplateforme, haute performance et open source pour la construction d’applications modernes, connectées au cloud et connectées à Internet.
ms.author: riande
ms.custom: mvc
ms.date: 04/17/2020
no-loc:
- Blazor
- SignalR
uid: index
ms.openlocfilehash: c5a5a0ada996d88cb9252da25b5580fe0cf46f0b
ms.sourcegitcommit: 636efd1afc0a1e6fd4b12ae3a542917b356abb93
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81615945"
---
# <a name="introduction-to-aspnet-core"></a>Présentation d’ASP.NET Core

Par [Daniel Roth](https://github.com/danroth27), [Rick Anderson](https://twitter.com/RickAndMSFT) et [Shaun Luttin](https://twitter.com/dicshaunary)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core est un cadre interplateforme, haute performance et [open source](https://github.com/dotnet/aspnetcore) pour la construction d’applications modernes, connectées au cloud et connectées à Internet. Avec ASP.NET Core, vous pouvez :

* Construisez des applications et des services Web, des applications [Internet des objets (IoT)](https://www.microsoft.com/internet-of-things/) et des backends mobiles.
* Utiliser vos outils de développement préférés sur Windows, macOS et Linux.
* Déployer dans le cloud ou localement.
* Exécuter sur [.NET Core](/dotnet/core/introduction).

## <a name="why-choose-aspnet-core"></a>Pourquoi utiliser ASP.NET Core ?

Des millions de développeurs utilisent ou ont utilisé [ASP.NET 4.x](/aspnet/overview) pour créer des applications Web. ASP.NET Core est une refonte de ASP.NET 4.x, y compris les changements architecturaux qui se traduisent par un cadre plus maigre, plus modulaire.

[!INCLUDE[](~/includes/benefits.md)]

## <a name="build-web-apis-and-web-ui-using-aspnet-core-mvc"></a>Créer des API web et une interface utilisateur web en utilisant le modèle MVC d’ASP.NET Core

Le modèle MVC d’ASP.NET Core fournit des fonctionnalités pour créer des [API web](xref:tutorials/first-web-api) et des [applications web](xref:tutorials/razor-pages/index) :

* Le [modèle MVC (Modèle-Vue-Contrôleur)](xref:mvc/overview) permet de rendre vos API web et vos applications web testables.
* [Razor Pages](xref:razor-pages/index) est un modèle de programmation basé sur des pages qui facilite et rend la construction d’interface utilisateur Web plus facile et plus productive.
* Le [balisage Razor](xref:mvc/views/razor) fournit une syntaxe efficace pour les [pages Razor](xref:razor-pages/index) et les [vues MVC](xref:mvc/views/overview).
* Les [Tag Helpers](xref:mvc/views/tag-helpers/intro) permettent au code côté serveur de participer à la création et au rendu des éléments HTML dans les fichiers Razor.
* La prise en charge intégrée de [plusieurs formats de données et de la négociation de contenu](xref:web-api/advanced/formatting) permet à vos API web d’être utilisées par un large éventail de clients, notamment des navigateurs et des appareils mobiles.
* Le principe de la [liaison de modèle](xref:mvc/models/model-binding) permet le mappage automatiquement des données des requêtes HTTP aux paramètres des méthodes d’action.
* La [validation de modèle](xref:mvc/models/validation) effectue automatiquement la validation côté client et côté serveur.

## <a name="client-side-development"></a>Développement côté client

ASP.NET Core s’intègre parfaitement avec les frameworks et les bibliothèques populaires côté client, notamment [Blazor](xref:blazor/index), [Angular](xref:spa/angular), [React](xref:spa/react) et [Bootstrap](https://getbootstrap.com/). Pour plus d’informations, consultez <xref:blazor/index> et les rubriques connexes sous *Développement côté client*.

<a name="target-framework"></a>

## <a name="aspnet-core-target-frameworks"></a>cadres cibles ASP.NET Core

ASP.NET Core 3.x et plus tard ne peut cibler .NET Core. En général, ASP.NET Core est composé de bibliothèques [.NET Standard.](/dotnet/standard/net-standard) Les bibliothèques écrites avec .NET Standard 2.0 s’exécutent sur [n’importe quelle plateforme .NET qui implémente .NET Standard 2.0](/dotnet/standard/net-standard#net-implementation-support).

Le ciblage de .NET Core présente plusieurs avantages, qui sont plus nombreux à chaque version. Voici certains avantages de .NET Core par rapport à .NET Framework :

* Multiplateforme. S’exécute sur Windows, macOS et Linux.
* performances améliorées
* [Version côte à côte](/dotnet/standard/choosing-core-framework-server#a-need-for-side-by-side-of-net-versions-per-application-level)
* Nouvelles API
* Open source

## <a name="recommended-learning-path"></a>Parcours d’apprentissage recommandé

Nous recommandons la séquence suivante de tutoriels pour une introduction au développement d’applications ASP.NET Core:

1. Suivez un tutoriel pour le type d’application que vous souhaitez développer ou maintenir.

   |Type d’application  |Scénario  |Didacticiel  |
   |----------|----------|----------|
   |Application web                   | Nouveau développement d’interface utilisateur web côté serveur |[Bien démarrer avec les pages Razor](xref:tutorials/razor-pages/razor-pages-start) |
   |Application web                   | Maintenir une application MVC |[Bien démarrer avec MVC](xref:tutorials/first-mvc-app/start-mvc)|
   |Application web                   | Développement de l’interface utilisateur Web côté client |[Commence avec Blazor](xref:tutorials/first-blazor-app) |
   |API web                   | Services HTTP RESTful |[Créer une API web](xref:tutorials/first-web-api)&dagger; |
   |Application d’appel de procédure à distance | Services de premier contrat utilisant Protocol Buffers |[Bien démarrer avec un service gRPC](xref:tutorials/grpc/grpc-start) |
   |Application en temps réel             | Communication bidirectionnelle entre serveurs et clients connectés |[Bien démarrer avec SignalR](xref:tutorials/signalr) |

1. Suivez un tutoriel qui montre comment faire l’accès aux données de base.

   |Scénario  |Didacticiel  |
   |----------|----------|
   |Nouveau développement        |[Pages Razor avec Entity Framework Core](xref:data/ef-rp/intro) |
   |Maintenir une application MVC |[MVC avec Entity Framework Core](xref:data/ef-mvc/intro) |

1. Lisez un aperçu des [principes fondamentaux](xref:fundamentals/index) ASP.NET Core qui s’appliquent à tous les types d’applications.

1. Parcourez la table du contenu pour d’autres sujets d’intérêt.

&dagger;Il ya aussi un [tutoriel interactif aPI web](/learn/modules/build-web-api-net-core). Aucune installation locale d’outils de développement n’est requise. Le code s’exécute dans une [coquille Cloud Azure](https://azure.microsoft.com/features/cloud-shell/) dans votre navigateur, et [curl](https://curl.haxx.se/) est utilisé pour les tests.

## <a name="migrate-from-net-framework"></a>Migrer à partir de .NET Framework

Pour un guide de référence sur la migration ASP.NET applications 4.x <xref:migration/proper-to-2x/index>pour ASP.NET Core, voir .

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core est un cadre interplateforme, haute performance et [open source](https://github.com/dotnet/aspnetcore) pour la construction d’applications modernes, connectées au cloud et connectées à Internet. Avec ASP.NET Core, vous pouvez :

* Construisez des applications et des services Web, des applications [Internet des objets (IoT)](https://www.microsoft.com/internet-of-things/) et des backends mobiles.
* Utiliser vos outils de développement préférés sur Windows, macOS et Linux.
* Déployer dans le cloud ou localement.
* Exécuter sur [.NET Core ou .NET Framework](/dotnet/articles/standard/choosing-core-framework-server).

## <a name="why-choose-aspnet-core"></a>Pourquoi utiliser ASP.NET Core ?

Des millions de développeurs utilisent ou ont utilisé [ASP.NET 4.x](/aspnet/overview) pour créer des applications Web. ASP.NET Core est une refonte d’ASP.NET 4.x, avec des modifications d’architecture qui aboutissent à un framework plus léger et modulaire.

[!INCLUDE[](~/includes/benefits.md)]

## <a name="build-web-apis-and-web-ui-using-aspnet-core-mvc"></a>Créer des API web et une interface utilisateur web en utilisant le modèle MVC d’ASP.NET Core

Le modèle MVC d’ASP.NET Core fournit des fonctionnalités pour créer des [API web](xref:tutorials/first-web-api) et des [applications web](xref:tutorials/razor-pages/index) :

* Le [modèle MVC (Modèle-Vue-Contrôleur)](xref:mvc/overview) permet de rendre vos API web et vos applications web testables.
* [Razor Pages](xref:razor-pages/index) est un modèle de programmation basé sur des pages qui facilite et rend la construction d’interface utilisateur Web plus facile et plus productive.
* Le [balisage Razor](xref:mvc/views/razor) fournit une syntaxe efficace pour les [pages Razor](xref:razor-pages/index) et les [vues MVC](xref:mvc/views/overview).
* Les [Tag Helpers](xref:mvc/views/tag-helpers/intro) permettent au code côté serveur de participer à la création et au rendu des éléments HTML dans les fichiers Razor.
* La prise en charge intégrée de [plusieurs formats de données et de la négociation de contenu](xref:web-api/advanced/formatting) permet à vos API web d’être utilisées par un large éventail de clients, notamment des navigateurs et des appareils mobiles.
* Le principe de la [liaison de modèle](xref:mvc/models/model-binding) permet le mappage automatiquement des données des requêtes HTTP aux paramètres des méthodes d’action.
* La [validation de modèle](xref:mvc/models/validation) effectue automatiquement la validation côté client et côté serveur.

## <a name="client-side-development"></a>Développement côté client

ASP.NET Core s’intègre parfaitement avec les frameworks et les bibliothèques populaires côté client, notamment [Blazor](xref:blazor/index), [Angular](xref:spa/angular), [React](xref:spa/react) et [Bootstrap](https://getbootstrap.com/). Pour plus d’informations, consultez <xref:blazor/index> et les rubriques connexes sous *Développement côté client*.

<a name="target-framework"></a>

## <a name="aspnet-core-targeting-net-framework"></a>ASP.NET Core ciblant .NET Framework

ASP.NET Core 2.x peut cibler .NET Core ou le .NET Framework. Les applications ASP.NET Core ciblant .NET Framework ne sont pas multiplateformes : elles s’exécutent seulement sur Windows. D’une façon générale, ASP.NET Core 2.x est constitué de bibliothèques [.NET Standard](/dotnet/standard/net-standard). Les bibliothèques écrites avec .NET Standard 2.0 s’exécutent sur [n’importe quelle plateforme .NET qui implémente .NET Standard 2.0](/dotnet/standard/net-standard#net-implementation-support).

ASP.NET Core 2.x est pris en charge sur les versions .NET Framework compatibles avec .NET Standard 2.0 :

* .NET Framework dernière version est recommandée.
* .NET Framework 4.6.1 et versions ultérieures.

ASP.NET Core 3.0 et ultérieur s’exécute uniquement sur .NET Core. Pour plus de détails concernant ce changement, consultez [A first look at changes coming in ASP.NET Core 3.0](https://blogs.msdn.microsoft.com/webdev/2018/10/29/a-first-look-at-changes-coming-in-asp-net-core-3-0/).

Le ciblage de .NET Core présente plusieurs avantages, qui sont plus nombreux à chaque version. Voici certains avantages de .NET Core par rapport à .NET Framework :

* Multiplateforme. S’exécute sur macOS, Linux et Windows
* performances améliorées
* [Version côte à côte](/dotnet/standard/choosing-core-framework-server#a-need-for-side-by-side-of-net-versions-per-application-level)
* Nouvelles API
* Open source

Nous nous efforçons de combler l’écart d’API qui existe entre .NET Framework et .NET Core. Le [Pack de compatibilité Windows](/dotnet/core/porting/windows-compat-pack) a rendu disponible dans .NET Core des milliers d’API fonctionnant seulement dans Windows. Ces API n’étaient pas disponibles dans .NET Core 1.x.

## <a name="recommended-learning-path"></a>Parcours d’apprentissage recommandé

Nous vous recommandons la séquence de tutoriels et d’articles suivante comme introduction au développement des applications ASP.NET Core :

1. Suivez un tutoriel pour le type d’application que vous souhaitez développer ou maintenir.

   |Type d’application  |Scénario  |Didacticiel  |
   |----------|----------|----------|
   |Application web                   | Pour un nouveau développement        |[Bien démarrer avec les pages Razor](xref:tutorials/razor-pages/razor-pages-start) |
   |Application web                   | Pour maintenir une application MVC |[Bien démarrer avec MVC](xref:tutorials/first-mvc-app/start-mvc)|
   |API web                   |                            |[Créer une API web](xref:tutorials/first-web-api)&dagger; |
   |Application en temps réel             |                            |[Bien démarrer avec SignalR](xref:tutorials/signalr) |

1. Suivez un tutoriel qui montre comment faire l’accès aux données de base.

   |Scénario  |Didacticiel  |
   |----------|----------|
   | Pour un nouveau développement        |[Pages Razor avec Entity Framework Core](xref:data/ef-rp/intro) |
   | Pour maintenir une application MVC |[MVC avec Entity Framework Core](xref:data/ef-mvc/intro) |

1. Lisez un aperçu des [principes fondamentaux](xref:fundamentals/index) ASP.NET Core qui s’appliquent à tous les types d’applications.

1. Parcourez la Table des matières pour d’autres rubriques qui vous intéressent.

&dagger;Il ya aussi un [tutoriel API web que vous suivez entièrement dans le navigateur](/learn/modules/build-web-api-net-core), aucune installation locale IDE nécessaire. Le code s’exécute dans [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/), et [curl](https://curl.haxx.se/) est utilisé à des fins de test.

## <a name="migrate-from-net-framework"></a>Migrer à partir de .NET Framework

Pour un guide de référence sur la migration des applications ASP.NET <xref:migration/proper-to-2x/index>pour ASP.NET Core, voir .

::: moniker-end

## <a name="how-to-download-a-sample"></a>Comment télécharger un exemple

La plupart des articles et tutoriels contiennent des liens vers des exemples de code.

1. [Téléchargez le fichier zip du référentiel ASP.NET](https://codeload.github.com/dotnet/AspNetCore.Docs/zip/master).
1. Décompressez le fichier *Docs-master.zip*.
1. Utilisez l’URL contenue dans l’exemple de lien pour vous aider à naviguer dans l’exemple de répertoire.

### <a name="preprocessor-directives-in-sample-code"></a>Directives de préprocesseur dans l’exemple de code

Pour démontrer plusieurs scénarios, les `#define` `#if-#else/#elif-#endif` applications d’échantillons utilisent les directives et les préprocesseurs pour compiler sélectivement et exécuter différentes sections de code d’échantillon. Pour les échantillons qui utilisent cette `#define` approche, définissez la directive en haut des fichiers C pour définir le symbole associé au scénario que vous souhaitez exécuter. Certains échantillons nécessitent la définition du symbole en haut de plusieurs fichiers afin d’exécuter un scénario.

Par exemple, la liste des symboles `#define` suivante indique que les quatre scénarios sont disponibles (un scénario par symbole). La configuration actuelle de l’exemple exécute le scénario `TemplateCode` :

```csharp
#define TemplateCode // or LogFromMain or ExpandDefault or FilterInCode
```

Pour que l’exemple exécute le scénario `ExpandDefault`, définissez le symbole `ExpandDefault` et laissez les symboles restants commentés :

```csharp
#define ExpandDefault // TemplateCode or LogFromMain or FilterInCode
```

Pour plus d’informations sur l’utilisation des [directives de préprocesseur C#](/dotnet/csharp/language-reference/preprocessor-directives/) pour compiler de façon sélective des sections de code, consultez [#define (Référence C#)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-define) et [#if (Référence C#) ](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if).

### <a name="regions-in-sample-code"></a>Régions dans l’exemple de code

Certains exemples d’applications contiennent des sections de code entourées [de #region](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region) et [de #endregion](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-endregion) directives C. Le système de génération de documentation injecte ces régions dans les rubriques de documentation affichées.  

Les noms des régions contiennent généralement le mot « snippet ». L’exemple suivant montre une région nommée `snippet_WebHostDefaults` :

```csharp
#region snippet_WebHostDefaults
Host.CreateDefaultBuilder(args)
    .ConfigureWebHostDefaults(webBuilder =>
    {
        webBuilder.UseStartup<Startup>();
    });
#endregion
```

L’extrait de code C# précédent est référencé dans le fichier Markdown de la rubrique avec la ligne suivante :

```md
[!code-csharp[](sample/SampleApp/Program.cs?name=snippet_WebHostDefaults)]
```

Vous pouvez ignorer (ou supprimer) en toute sécurité les `#region` directives et `#endregion` directives qui entourent le code. Ne modifiez pas le code dans ces directives si vous prévoyez d’exécuter les scénarios d’exemple décrits dans le sujet. N’hésitez pas à modifier le code quand vous testez d’autres scénarios.

Pour plus d’informations, consultez [Contribuer à la documentation ASP.NET : extraits de code](https://github.com/dotnet/AspNetCore.Docs/blob/master/CONTRIBUTING.md#code-snippets).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les ressources suivantes :

* <xref:getting-started>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [Notions de base d’ASP.NET Core](xref:fundamentals/index)
* [Le point hebdomadaire de la communauté ASP.NET](https://live.asp.net/) couvre l’avancement et les plans des équipes de développement. Il met en avant de nouveaux blogs et des logiciels de tiers.
