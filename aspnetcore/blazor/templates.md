---
title: Modèles Blazor de ASP.net Core
author: guardrex
description: En savoir plus Blazor sur les modèles Blazor d’application ASP.net Core et la structure de projet.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/templates
ms.openlocfilehash: c84d6415728bf56836d98cfa66d1b9d46d2eadc8
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82770899"
---
# <a name="aspnet-core-blazor-templates"></a>Modèles Blazor de ASP.net Core

Par [Daniel Roth](https://github.com/danroth27) et [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

L' Blazor infrastructure fournit des modèles pour développer des applications pour chacun Blazor des modèles d’hébergement :

* BlazorWebassembly`blazorwasm`()
* BlazorServer (`blazorserver`)

Pour plus d’informations Blazorsur les modèles d’hébergement <xref:blazor/hosting-models>de, consultez.

Pour obtenir des instructions pas à pas sur la création Blazor d’une application à partir d' <xref:blazor/get-started>un modèle, consultez.

Les options de modèle sont disponibles en `--help` passant l’option à la commande [dotnet New](/dotnet/core/tools/dotnet-new) CLI :

```dotnetcli
dotnet new blazorwasm --help
dotnet new blazorserver --help
```

## <a name="blazor-project-structure"></a>Blazorstructure de projet

Les fichiers et dossiers suivants composent Blazor une application générée à Blazor partir d’un modèle :

* *Program.cs* &ndash; le point d’entrée de l’application qui installe les éléments suivants :

  * [Hôte](xref:fundamentals/host/generic-host) ASP.net Core (Blazor serveur)
  * Hôte webassemblyBlazor (webassembly) &ndash; le code de ce fichier est propre aux applications créées Blazor à partir du modèle`blazorwasm`webassembly ().
    * Le `App` composant, qui est le composant racine de l’application, est spécifié en tant `app` qu’élément DOM de `Add` la méthode.
    * Les services peuvent être configurés `ConfigureServices` avec la méthode sur le générateur de l’hôte `builder.Services.AddSingleton<IMyDependency, MyDependency>();`(par exemple,).
    * La configuration peut être fournie par le biais du`builder.Configuration`générateur d’ordinateur hôte ().

* *Startup.cs* (Blazor Server) &ndash; contient la logique de démarrage de l’application. La `Startup` classe définit deux méthodes :

  * `ConfigureServices`&ndash; Configure les services d' [injection de dépendances](xref:fundamentals/dependency-injection) de l’application. Dans Blazor les applications serveur, les services sont ajoutés <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor*>en appelant, `WeatherForecastService` et le est ajouté au conteneur de service pour une utilisation `FetchData` par l’exemple de composant.
  * `Configure`&ndash; Configure le pipeline de traitement des demandes de l’application :
    * <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub*>est appelé pour configurer un point de terminaison pour la connexion en temps réel avec le navigateur. La connexion est créée avec [SignalR](xref:signalr/introduction), qui est une infrastructure permettant d’ajouter des fonctionnalités Web en temps réel aux applications.
    * [MapFallbackToPage (« /_Host »)](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) est appelé pour configurer la page racine de l’application (*pages/_Host. cshtml*) et activer la navigation.

* *wwwroot/index.html* (Blazor webassembly &ndash; ) page racine de l’application implémentée en tant que page HTML :
  * Quand une page de l’application est initialement demandée, cette page est rendue et renvoyée dans la réponse.
  * La page spécifie l’emplacement `App` où le composant racine est restitué. Le `App` composant (*app. Razor*) est spécifié en tant `app` qu’élément DOM de `AddComponent` la méthode `Startup.Configure`dans.
  * Le `_framework/blazor.webassembly.js` fichier JavaScript est chargé, ce qui suit :
    * Télécharge le Runtime .NET, l’application et les dépendances de l’application.
    * Initialise le runtime pour exécuter l’application.

* *App. Razor* &ndash; le composant racine de l’application qui configure le routage côté client à l' <xref:Microsoft.AspNetCore.Components.Routing.Router> aide du composant. Le `Router` composant intercepte la navigation dans le navigateur et affiche la page qui correspond à l’adresse demandée.

* *Pages* Le dossier &ndash; pages contient les composants/pages routables *(. Razor*) qui composent Blazor l’application et Razor la page racine Blazor d’une application serveur. L’itinéraire de chaque page est spécifié à l' [`@page`](xref:mvc/views/razor#page) aide de la directive. Le modèle comprend les éléments suivants :
  * *_Host. cshtml* (Blazor Server) &ndash; la page racine de l’application est implémentée Razor en tant que page :
    * Quand une page de l’application est initialement demandée, cette page est rendue et renvoyée dans la réponse.
    * Le `_framework/blazor.server.js` fichier JavaScript est chargé, ce qui configure la SignalR connexion en temps réel entre le navigateur et le serveur.
    * La page hôte spécifie où le `App` composant racine (*app. Razor*) est affiché.
  * `Counter`(*Counter. Razor*) &ndash; implémente la page de compteur.
  * `Error`(*Error. Razor*, Blazor application serveur uniquement) &ndash; Rendu lorsqu’une exception non gérée se produit dans l’application.
  * `FetchData`(*FetchData. Razor*) &ndash; implémente la page FETCH Data.
  * `Index`(*Index. Razor*) &ndash; implémente la page d’hébergement.

* *Shared* Le dossier &ndash; partagé contient d’autres composants d’interface utilisateur (*. Razor*) utilisés par l’application :
  * `MainLayout`(*MainLayout. Razor*) &ndash; composant de disposition de l’application.
  * `NavMenu`(*NavMenu. Razor*) &ndash; implémente la navigation dans l’encadré. Comprend le [composant NavLink](xref:blazor/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), qui affiche des liens de navigation vers Razor d’autres composants. Le `NavLink` composant indique automatiquement un état sélectionné quand son composant est chargé, ce qui aide l’utilisateur à comprendre quel composant est actuellement affiché.

* *_Imports. Razor* &ndash; inclut des Razor directives communes à inclure dans les composants de l’application (*. Razor*), [`@using`](xref:mvc/views/razor#using) tels que les directives pour les espaces de noms.

* *Data* Dossier de donnéesBlazor (serveur &ndash; ) contient `WeatherForecast` la classe et l’implémentation `WeatherForecastService` du qui fournissent des exemples de données météorologiques `FetchData` au composant de l’application.

* *wwwroot* &ndash; répertoire [racine Web](xref:fundamentals/index#web-root) de l’application contenant les ressources statiques publiques de l’application.

* paramètres de configuration *appSettings. JSON* (Blazor serveur) &ndash; pour l’application.
