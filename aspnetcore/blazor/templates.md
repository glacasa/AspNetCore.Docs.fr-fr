---
title: Modèles de ASP.NET Core Blazor
author: guardrex
description: En savoir plus sur Blazor les modèles d’application ASP.net Core et la Blazor structure de projet.
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
uid: blazor/templates
ms.openlocfilehash: ce46d562285b95ff656ed43b3a63ca5e7315f4c8
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2020
ms.locfileid: "85243211"
---
# <a name="aspnet-core-blazor-templates"></a><span data-ttu-id="89e96-103">Modèles de ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="89e96-103">ASP.NET Core Blazor templates</span></span>

<span data-ttu-id="89e96-104">Par [Daniel Roth](https://github.com/danroth27) et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="89e96-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="89e96-105">L' Blazor infrastructure fournit des modèles pour développer des applications pour chacun des Blazor modèles d’hébergement :</span><span class="sxs-lookup"><span data-stu-id="89e96-105">The Blazor framework provides templates to develop apps for each of the Blazor hosting models:</span></span>

* Blazor<span data-ttu-id="89e96-106">Webassembly ( `blazorwasm` )</span><span class="sxs-lookup"><span data-stu-id="89e96-106"> WebAssembly (`blazorwasm`)</span></span>
* Blazor<span data-ttu-id="89e96-107">Server ( `blazorserver` )</span><span class="sxs-lookup"><span data-stu-id="89e96-107"> Server (`blazorserver`)</span></span>

<span data-ttu-id="89e96-108">Pour plus d’informations sur les Blazor modèles d’hébergement de, consultez <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="89e96-108">For more information on Blazor's hosting models, see <xref:blazor/hosting-models>.</span></span>

<span data-ttu-id="89e96-109">Pour obtenir des instructions pas à pas sur la création Blazor d’une application à partir d’un modèle, consultez <xref:blazor/get-started> .</span><span class="sxs-lookup"><span data-stu-id="89e96-109">For step-by-step instructions on creating a Blazor app from a template, see <xref:blazor/get-started>.</span></span>

<span data-ttu-id="89e96-110">Les options de modèle sont disponibles en passant l' `--help` option à la [`dotnet new`](/dotnet/core/tools/dotnet-new) commande CLI :</span><span class="sxs-lookup"><span data-stu-id="89e96-110">Template options are available by passing the `--help` option to the [`dotnet new`](/dotnet/core/tools/dotnet-new) CLI command:</span></span>

```dotnetcli
dotnet new blazorwasm --help
dotnet new blazorserver --help
```

## <a name="blazor-project-structure"></a>Blazor<span data-ttu-id="89e96-111">structure de projet</span><span class="sxs-lookup"><span data-stu-id="89e96-111"> project structure</span></span>

<span data-ttu-id="89e96-112">Les fichiers et dossiers suivants composent une Blazor application générée à partir d’un Blazor modèle :</span><span class="sxs-lookup"><span data-stu-id="89e96-112">The following files and folders make up a Blazor app generated from a Blazor template:</span></span>

* <span data-ttu-id="89e96-113">`Program.cs`: Le point d’entrée de l’application qui installe les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="89e96-113">`Program.cs`: The app's entry point that sets up the:</span></span>

  * <span data-ttu-id="89e96-114">[Hôte](xref:fundamentals/host/generic-host) ASP.net Core ( Blazor serveur)</span><span class="sxs-lookup"><span data-stu-id="89e96-114">ASP.NET Core [host](xref:fundamentals/host/generic-host) (Blazor Server)</span></span>
  * <span data-ttu-id="89e96-115">Hôte webassembly ( Blazor Webassembly) : le code de ce fichier est propre aux applications créées à partir du Blazor modèle webassembly ( `blazorwasm` ).</span><span class="sxs-lookup"><span data-stu-id="89e96-115">WebAssembly host (Blazor WebAssembly): The code in this file is unique to apps created from the Blazor WebAssembly template (`blazorwasm`).</span></span>
    * <span data-ttu-id="89e96-116">Le `App` composant, qui est le composant racine de l’application, est spécifié en tant qu' `app` élément DOM de la `Add` méthode.</span><span class="sxs-lookup"><span data-stu-id="89e96-116">The `App` component, which is the root component of the app, is specified as the `app` DOM element to the `Add` method.</span></span>
    * <span data-ttu-id="89e96-117">Les services peuvent être configurés avec la `ConfigureServices` méthode sur le générateur de l’hôte (par exemple, `builder.Services.AddSingleton<IMyDependency, MyDependency>();` ).</span><span class="sxs-lookup"><span data-stu-id="89e96-117">Services can be configured with the `ConfigureServices` method on the host builder (for example, `builder.Services.AddSingleton<IMyDependency, MyDependency>();`).</span></span>
    * <span data-ttu-id="89e96-118">La configuration peut être fournie par le biais du générateur d’ordinateur hôte ( `builder.Configuration` ).</span><span class="sxs-lookup"><span data-stu-id="89e96-118">Configuration can be supplied via the host builder (`builder.Configuration`).</span></span>

* <span data-ttu-id="89e96-119">`Startup.cs`( Blazor Serveur) : contient la logique de démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="89e96-119">`Startup.cs` (Blazor Server): Contains the app's startup logic.</span></span> <span data-ttu-id="89e96-120">La `Startup` classe définit deux méthodes :</span><span class="sxs-lookup"><span data-stu-id="89e96-120">The `Startup` class defines two methods:</span></span>

  * <span data-ttu-id="89e96-121">`ConfigureServices`: Configure les services d' [injection de dépendances](xref:fundamentals/dependency-injection) de l’application.</span><span class="sxs-lookup"><span data-stu-id="89e96-121">`ConfigureServices`: Configures the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) services.</span></span> <span data-ttu-id="89e96-122">Dans les Blazor applications serveur, les services sont ajoutés en appelant <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A> , et le `WeatherForecastService` est ajouté au conteneur de service pour une utilisation par l’exemple de `FetchData` composant.</span><span class="sxs-lookup"><span data-stu-id="89e96-122">In Blazor Server apps, services are added by calling <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A>, and the `WeatherForecastService` is added to the service container for use by the example `FetchData` component.</span></span>
  * <span data-ttu-id="89e96-123">`Configure`: Configure le pipeline de traitement des demandes de l’application :</span><span class="sxs-lookup"><span data-stu-id="89e96-123">`Configure`: Configures the app's request handling pipeline:</span></span>
    * <span data-ttu-id="89e96-124"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A>est appelé pour configurer un point de terminaison pour la connexion en temps réel avec le navigateur.</span><span class="sxs-lookup"><span data-stu-id="89e96-124"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> is called to set up an endpoint for the real-time connection with the browser.</span></span> <span data-ttu-id="89e96-125">La connexion est créée avec [SignalR](xref:signalr/introduction) , qui est une infrastructure permettant d’ajouter des fonctionnalités Web en temps réel aux applications.</span><span class="sxs-lookup"><span data-stu-id="89e96-125">The connection is created with [SignalR](xref:signalr/introduction), which is a framework for adding real-time web functionality to apps.</span></span>
    * <span data-ttu-id="89e96-126">[`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*)est appelé pour configurer la page racine de l’application ( `Pages/_Host.cshtml` ) et activer la navigation.</span><span class="sxs-lookup"><span data-stu-id="89e96-126">[`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) is called to set up the root page of the app (`Pages/_Host.cshtml`) and enable navigation.</span></span>

* <span data-ttu-id="89e96-127">`wwwroot/index.html`( Blazor Webassembly) : page racine de l’application implémentée en tant que page HTML :</span><span class="sxs-lookup"><span data-stu-id="89e96-127">`wwwroot/index.html` (Blazor WebAssembly): The root page of the app implemented as an HTML page:</span></span>
  * <span data-ttu-id="89e96-128">Quand une page de l’application est initialement demandée, cette page est rendue et renvoyée dans la réponse.</span><span class="sxs-lookup"><span data-stu-id="89e96-128">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
  * <span data-ttu-id="89e96-129">La page spécifie l’emplacement où le `App` composant racine est restitué.</span><span class="sxs-lookup"><span data-stu-id="89e96-129">The page specifies where the root `App` component is rendered.</span></span> <span data-ttu-id="89e96-130">Le `App` composant ( `App.razor` ) est spécifié en tant qu' `app` élément DOM de la `AddComponent` méthode dans `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="89e96-130">The `App` component (`App.razor`) is specified as the `app` DOM element to the `AddComponent` method in `Startup.Configure`.</span></span>
  * <span data-ttu-id="89e96-131">Le `_framework/blazor.webassembly.js` fichier JavaScript est chargé, ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="89e96-131">The `_framework/blazor.webassembly.js` JavaScript file is loaded, which:</span></span>
    * <span data-ttu-id="89e96-132">Télécharge le Runtime .NET, l’application et les dépendances de l’application.</span><span class="sxs-lookup"><span data-stu-id="89e96-132">Downloads the .NET runtime, the app, and the app's dependencies.</span></span>
    * <span data-ttu-id="89e96-133">Initialise le runtime pour exécuter l’application.</span><span class="sxs-lookup"><span data-stu-id="89e96-133">Initializes the runtime to run the app.</span></span>

* <span data-ttu-id="89e96-134">`App.razor`: Composant racine de l’application qui configure le routage côté client à l’aide du <xref:Microsoft.AspNetCore.Components.Routing.Router> composant.</span><span class="sxs-lookup"><span data-stu-id="89e96-134">`App.razor`: The root component of the app that sets up client-side routing using the <xref:Microsoft.AspNetCore.Components.Routing.Router> component.</span></span> <span data-ttu-id="89e96-135">Le <xref:Microsoft.AspNetCore.Components.Routing.Router> composant intercepte la navigation dans le navigateur et affiche la page qui correspond à l’adresse demandée.</span><span class="sxs-lookup"><span data-stu-id="89e96-135">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component intercepts browser navigation and renders the page that matches the requested address.</span></span>

* <span data-ttu-id="89e96-136">`Pages`dossier : contient les composants/pages routables ( `.razor` ) qui composent l' Blazor application et la Razor page racine d’une Blazor application serveur.</span><span class="sxs-lookup"><span data-stu-id="89e96-136">`Pages` folder: Contains the routable components/pages (`.razor`) that make up the Blazor app and the root Razor page of a Blazor Server app.</span></span> <span data-ttu-id="89e96-137">L’itinéraire de chaque page est spécifié à l’aide de la [`@page`](xref:mvc/views/razor#page) directive.</span><span class="sxs-lookup"><span data-stu-id="89e96-137">The route for each page is specified using the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="89e96-138">Le modèle comprend les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="89e96-138">The template includes the following:</span></span>
  * <span data-ttu-id="89e96-139">`_Host.cshtml`( Blazor Serveur) : page racine de l’application implémentée en tant que Razor Pagination</span><span class="sxs-lookup"><span data-stu-id="89e96-139">`_Host.cshtml` (Blazor Server): The root page of the app implemented as a Razor Page:</span></span>
    * <span data-ttu-id="89e96-140">Quand une page de l’application est initialement demandée, cette page est rendue et renvoyée dans la réponse.</span><span class="sxs-lookup"><span data-stu-id="89e96-140">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
    * <span data-ttu-id="89e96-141">Le `_framework/blazor.server.js` fichier JavaScript est chargé, ce qui configure la connexion en temps réel SignalR entre le navigateur et le serveur.</span><span class="sxs-lookup"><span data-stu-id="89e96-141">The `_framework/blazor.server.js` JavaScript file is loaded, which sets up the real-time SignalR connection between the browser and the server.</span></span>
    * <span data-ttu-id="89e96-142">La page hôte spécifie où le `App` composant racine ( `App.razor` ) est affiché.</span><span class="sxs-lookup"><span data-stu-id="89e96-142">The Host page specifies where the root `App` component (`App.razor`) is rendered.</span></span>
  * <span data-ttu-id="89e96-143">`Counter`( `Pages/Counter.razor` ) : Implémente la page de compteur.</span><span class="sxs-lookup"><span data-stu-id="89e96-143">`Counter` (`Pages/Counter.razor`): Implements the Counter page.</span></span>
  * <span data-ttu-id="89e96-144">`Error`( `Error.razor` , Blazor Application serveur uniquement) : rendu lorsqu’une exception non gérée se produit dans l’application.</span><span class="sxs-lookup"><span data-stu-id="89e96-144">`Error` (`Error.razor`, Blazor Server app only): Rendered when an unhandled exception occurs in the app.</span></span>
  * <span data-ttu-id="89e96-145">`FetchData`( `Pages/FetchData.razor` ) : Implémente la page FETCH Data.</span><span class="sxs-lookup"><span data-stu-id="89e96-145">`FetchData` (`Pages/FetchData.razor`): Implements the Fetch data page.</span></span>
  * <span data-ttu-id="89e96-146">`Index`( `Pages/Index.razor` ) : Implémente la page d’hébergement.</span><span class="sxs-lookup"><span data-stu-id="89e96-146">`Index` (`Pages/Index.razor`): Implements the Home page.</span></span>

* <span data-ttu-id="89e96-147">`Shared`dossier : contient d’autres composants d’interface utilisateur ( `.razor` ) utilisés par l’application :</span><span class="sxs-lookup"><span data-stu-id="89e96-147">`Shared` folder: Contains other UI components (`.razor`) used by the app:</span></span>
  * <span data-ttu-id="89e96-148">`MainLayout`( `MainLayout.razor` ) : Composant de disposition de l’application.</span><span class="sxs-lookup"><span data-stu-id="89e96-148">`MainLayout` (`MainLayout.razor`): The app's layout component.</span></span>
  * <span data-ttu-id="89e96-149">`NavMenu`( `NavMenu.razor` ) : Implémente la navigation dans l’encadré.</span><span class="sxs-lookup"><span data-stu-id="89e96-149">`NavMenu` (`NavMenu.razor`): Implements sidebar navigation.</span></span> <span data-ttu-id="89e96-150">Comprend le [ `NavLink` composant](xref:blazor/fundamentals/routing#navlink-component) ( <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ), qui affiche des liens de navigation vers d’autres Razor composants.</span><span class="sxs-lookup"><span data-stu-id="89e96-150">Includes the [`NavLink` component](xref:blazor/fundamentals/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="89e96-151">Le <xref:Microsoft.AspNetCore.Components.Routing.NavLink> composant indique automatiquement un état sélectionné quand son composant est chargé, ce qui aide l’utilisateur à comprendre quel composant est actuellement affiché.</span><span class="sxs-lookup"><span data-stu-id="89e96-151">The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>

* <span data-ttu-id="89e96-152">`_Imports.razor`: Inclut des Razor directives communes à inclure dans les composants de l’application ( `.razor` ), tels que les [`@using`](xref:mvc/views/razor#using) directives pour les espaces de noms.</span><span class="sxs-lookup"><span data-stu-id="89e96-152">`_Imports.razor`: Includes common Razor directives to include in the app's components (`.razor`), such as [`@using`](xref:mvc/views/razor#using) directives for namespaces.</span></span>

* <span data-ttu-id="89e96-153">`Data`dossier ( Blazor serveur) : contient la `WeatherForecast` classe et l’implémentation du `WeatherForecastService` qui fournissent des exemples de données météorologiques au composant de l’application `FetchData` .</span><span class="sxs-lookup"><span data-stu-id="89e96-153">`Data` folder (Blazor Server): Contains the `WeatherForecast` class and implementation of the `WeatherForecastService` that provide example weather data to the app's `FetchData` component.</span></span>

* <span data-ttu-id="89e96-154">`wwwroot`: Dossier [racine Web](xref:fundamentals/index#web-root) de l’application contenant les ressources statiques publiques de l’application.</span><span class="sxs-lookup"><span data-stu-id="89e96-154">`wwwroot`: The [Web Root](xref:fundamentals/index#web-root) folder for the app containing the app's public static assets.</span></span>

* <span data-ttu-id="89e96-155">`appsettings.json`( Blazor Serveur) : paramètres de configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="89e96-155">`appsettings.json` (Blazor Server): Configuration settings for the app.</span></span>
