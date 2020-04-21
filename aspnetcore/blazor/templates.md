---
title: modèles ASP.NET Core Blazor
author: guardrex
description: Renseignez-vous sur Blazor les modèles Blazor d’applications ASP.NET Core et la structure du projet.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/19/2020
no-loc:
- Blazor
- SignalR
uid: blazor/templates
ms.openlocfilehash: 0a4a508beeae3d7bc665372d925989aa4e34ad52
ms.sourcegitcommit: 5547d920f322e5a823575c031529e4755ab119de
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661727"
---
# <a name="aspnet-core-opno-locblazor-templates"></a>modèles ASP.NET Core Blazor

Par [Daniel Roth](https://github.com/danroth27) et [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Le Blazor cadre fournit des modèles pour Blazor développer des applications pour chacun des modèles d’hébergement :

* BlazorWebAssembly`blazorwasm`( )
* BlazorServeur`blazorserver`( )

Pour plus Blazord’informations sur <xref:blazor/hosting-models>les modèles d’hébergement de ', voir .

Pour obtenir des instructions étape Blazor par étape sur <xref:blazor/get-started>la création d’une application à partir d’un modèle, voir .

Les options de gabarit sont disponibles en passant l’option `--help` à la nouvelle commande CLI [dotnet](/dotnet/core/tools/dotnet-new) :

```dotnetcli
dotnet new blazorwasm --help
dotnet new blazorserver --help
```

## <a name="opno-locblazor-project-structure"></a>Blazorstructure du projet

Les fichiers et dossiers Blazor suivants constituent Blazor une application générée à partir d’un modèle :

* *Program.cs* &ndash; Le point d’entrée de l’application qui met en place le:

  * [ASP.NET’hôte](xref:fundamentals/host/generic-host) deBlazor base ( Serveur)
  * HébergezBlazor WebAssembly (WebAssembly) &ndash; Le code de ce Blazor fichier est unique`blazorwasm`aux applications créées à partir du modèle WebAssembly ().
    * Le `App` composant, qui est le composant racine de `app` l’application, est spécifié comme l’élément DOM de la `Add` méthode.
    * Les services peuvent être `ConfigureServices` configurés avec la `builder.Services.AddSingleton<IMyDependency, MyDependency>();`méthode sur le constructeur hôte (par exemple, ).
    * La configuration peut être fournie`builder.Configuration`par l’intermédiaire du constructeur hôte ( ).

* *Startup.cs* (Serveur)Blazor &ndash; contient la logique de démarrage de l’application. La `Startup` classe définit deux méthodes :

  * `ConfigureServices`&ndash; Configure les services d’injection de [dépendance (DI)](xref:fundamentals/dependency-injection) de l’application. Dans Blazor les applications Server, <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor*>les services `WeatherForecastService` sont ajoutés en appelant, `FetchData` et le est ajouté au conteneur de service pour une utilisation par le composant d’exemple.
  * `Configure`&ndash; Configure le pipeline de traitement des demandes de l’application :
    * <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub*>est appelé à configurer un point de terminaison pour la connexion en temps réel avec le navigateur. La connexion est [SignalR](xref:signalr/introduction)créée avec , qui est un cadre pour ajouter des fonctionnalités Web en temps réel aux applications.
    * [MapFallbackToPage ("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) est appelé à configurer la page racine de l’application *(Pages/_Host.cshtml*) et à activer la navigation.

* *wwwroot/index.html* (WebAssembly)Blazor &ndash; La page racine de l’application implémentée sous forme de page HTML :
  * Lorsque n’importe quelle page de l’application est initialement demandée, cette page est rendue et retournée dans la réponse.
  * La page précise où `App` le composant racine est rendu. Le `App` composant (*App.razor*) `app` est spécifié `AddComponent` comme `Startup.Configure`l’élément DOM à la méthode dans .
  * Le `_framework/blazor.webassembly.js` fichier JavaScript est chargé, qui :
    * Télécharge l’heure d’exécution .NET, l’application et les dépendances de l’application.
    * Initialise l’heure d’exécution pour exécuter l’application.

* *App.razor* &ndash; Le composant racine de l’application qui définit <xref:Microsoft.AspNetCore.Components.Routing.Router> le routage côté client à l’aide du composant. Le `Router` composant intercepte la navigation du navigateur et rend la page qui correspond à l’adresse demandée.

* *Dossier* pages &ndash; Contient les composants routables / pages (*.razor*) qui composent l’application Blazor et la racine Razor page d’une Blazor application Server. L’itinéraire de chaque page [`@page`](xref:mvc/views/razor#page) est spécifié à l’aide de la directive. Le modèle comprend ce qui suit :
  * *_Host.cshtml* (Blazor Serveur) &ndash; La page racine de l’application implémentée sous forme de page Razor :
    * Lorsque n’importe quelle page de l’application est initialement demandée, cette page est rendue et retournée dans la réponse.
    * Le `_framework/blazor.server.js` fichier JavaScript est chargé, qui SignalR définit la connexion en temps réel entre le navigateur et le serveur.
    * La page Host précise où le composant racine `App` *(App.razor*) est rendu.
  * `Counter`(*Counter.razor*) &ndash; Implémente la page Compteur.
  * `Error`(*Error.razor*, Blazor Application Serveur seulement) &ndash; Rendu lorsqu’une exception non gérée se produit dans l’application.
  * `FetchData`(*FetchData.razor*) &ndash; Implémente la page de données Fetch.
  * `Index`(*Index.razor*) &ndash; Implémente la page d’accueil.

* *Dossier* partagé &ndash; Contient d’autres composants d’interface utilisateur (*.razor)* utilisés par l’application:
  * `MainLayout`(*MainLayout.razor*) &ndash; Le composant de mise en page de l’application.
  * `NavMenu`(*NavMenu.razor*) &ndash; Implémente la navigation par barre latérale. Inclut le composant<xref:Microsoft.AspNetCore.Components.Routing.NavLink> [NavLink](xref:blazor/routing#navlink-component) ( ), qui rend les liaisons de navigation vers d’autres composants Razor. Le `NavLink` composant indique automatiquement un état sélectionné lorsque son composant est chargé, ce qui aide l’utilisateur à comprendre quel composant est actuellement affiché.

* *_Imports.razor* &ndash; Inclut les directives de razor communes à inclure dans les composants [`@using`](xref:mvc/views/razor#using) de l’application (*.razor*), telles que les directives pour les espaces de nom.

* *Dossier de* Blazor données &ndash; (serveur) Contient la classe et la `WeatherForecast` mise en œuvre de ce `WeatherForecastService` qui fournit des exemples de données météorologiques au composant de `FetchData` l’application.

* *wwwroot* &ndash; Le dossier [Web Root](xref:fundamentals/index#web-root) pour l’application contenant les actifs statiques publics de l’application.

* paramètres de configuration *appsettings.json* (Server)Blazor &ndash; pour l’application.
