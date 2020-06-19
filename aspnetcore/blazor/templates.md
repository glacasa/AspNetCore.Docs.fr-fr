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
ms.openlocfilehash: 3a491e43aec8291fcf03696b53240bd33e0deda1
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85102496"
---
# <a name="aspnet-core-blazor-templates"></a><span data-ttu-id="a46f5-103">Modèles de ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="a46f5-103">ASP.NET Core Blazor templates</span></span>

<span data-ttu-id="a46f5-104">Par [Daniel Roth](https://github.com/danroth27) et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="a46f5-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="a46f5-105">L' Blazor infrastructure fournit des modèles pour développer des applications pour chacun des Blazor modèles d’hébergement :</span><span class="sxs-lookup"><span data-stu-id="a46f5-105">The Blazor framework provides templates to develop apps for each of the Blazor hosting models:</span></span>

* Blazor<span data-ttu-id="a46f5-106">Webassembly ( `blazorwasm` )</span><span class="sxs-lookup"><span data-stu-id="a46f5-106"> WebAssembly (`blazorwasm`)</span></span>
* Blazor<span data-ttu-id="a46f5-107">Server ( `blazorserver` )</span><span class="sxs-lookup"><span data-stu-id="a46f5-107"> Server (`blazorserver`)</span></span>

<span data-ttu-id="a46f5-108">Pour plus d’informations sur les Blazor modèles d’hébergement de, consultez <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="a46f5-108">For more information on Blazor's hosting models, see <xref:blazor/hosting-models>.</span></span>

<span data-ttu-id="a46f5-109">Pour obtenir des instructions pas à pas sur la création Blazor d’une application à partir d’un modèle, consultez <xref:blazor/get-started> .</span><span class="sxs-lookup"><span data-stu-id="a46f5-109">For step-by-step instructions on creating a Blazor app from a template, see <xref:blazor/get-started>.</span></span>

<span data-ttu-id="a46f5-110">Les options de modèle sont disponibles en passant l' `--help` option à la commande [dotnet New](/dotnet/core/tools/dotnet-new) CLI :</span><span class="sxs-lookup"><span data-stu-id="a46f5-110">Template options are available by passing the `--help` option to the [dotnet new](/dotnet/core/tools/dotnet-new) CLI command:</span></span>

```dotnetcli
dotnet new blazorwasm --help
dotnet new blazorserver --help
```

## <a name="blazor-project-structure"></a>Blazor<span data-ttu-id="a46f5-111">structure de projet</span><span class="sxs-lookup"><span data-stu-id="a46f5-111"> project structure</span></span>

<span data-ttu-id="a46f5-112">Les fichiers et dossiers suivants composent une Blazor application générée à partir d’un Blazor modèle :</span><span class="sxs-lookup"><span data-stu-id="a46f5-112">The following files and folders make up a Blazor app generated from a Blazor template:</span></span>

* <span data-ttu-id="a46f5-113">*Program.cs*: point d’entrée de l’application qui installe les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="a46f5-113">*Program.cs*: The app's entry point that sets up the:</span></span>

  * <span data-ttu-id="a46f5-114">[Hôte](xref:fundamentals/host/generic-host) ASP.net Core ( Blazor serveur)</span><span class="sxs-lookup"><span data-stu-id="a46f5-114">ASP.NET Core [host](xref:fundamentals/host/generic-host) (Blazor Server)</span></span>
  * <span data-ttu-id="a46f5-115">Hôte webassembly ( Blazor Webassembly) : le code de ce fichier est propre aux applications créées à partir du Blazor modèle webassembly ( `blazorwasm` ).</span><span class="sxs-lookup"><span data-stu-id="a46f5-115">WebAssembly host (Blazor WebAssembly): The code in this file is unique to apps created from the Blazor WebAssembly template (`blazorwasm`).</span></span>
    * <span data-ttu-id="a46f5-116">Le `App` composant, qui est le composant racine de l’application, est spécifié en tant qu' `app` élément DOM de la `Add` méthode.</span><span class="sxs-lookup"><span data-stu-id="a46f5-116">The `App` component, which is the root component of the app, is specified as the `app` DOM element to the `Add` method.</span></span>
    * <span data-ttu-id="a46f5-117">Les services peuvent être configurés avec la `ConfigureServices` méthode sur le générateur de l’hôte (par exemple, `builder.Services.AddSingleton<IMyDependency, MyDependency>();` ).</span><span class="sxs-lookup"><span data-stu-id="a46f5-117">Services can be configured with the `ConfigureServices` method on the host builder (for example, `builder.Services.AddSingleton<IMyDependency, MyDependency>();`).</span></span>
    * <span data-ttu-id="a46f5-118">La configuration peut être fournie par le biais du générateur d’ordinateur hôte ( `builder.Configuration` ).</span><span class="sxs-lookup"><span data-stu-id="a46f5-118">Configuration can be supplied via the host builder (`builder.Configuration`).</span></span>

* <span data-ttu-id="a46f5-119">*Startup.cs* ( Blazor serveur) : contient la logique de démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="a46f5-119">*Startup.cs* (Blazor Server): Contains the app's startup logic.</span></span> <span data-ttu-id="a46f5-120">La `Startup` classe définit deux méthodes :</span><span class="sxs-lookup"><span data-stu-id="a46f5-120">The `Startup` class defines two methods:</span></span>

  * <span data-ttu-id="a46f5-121">`ConfigureServices`: Configure les services d' [injection de dépendances](xref:fundamentals/dependency-injection) de l’application.</span><span class="sxs-lookup"><span data-stu-id="a46f5-121">`ConfigureServices`: Configures the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) services.</span></span> <span data-ttu-id="a46f5-122">Dans les Blazor applications serveur, les services sont ajoutés en appelant <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A> , et le `WeatherForecastService` est ajouté au conteneur de service pour une utilisation par l’exemple de `FetchData` composant.</span><span class="sxs-lookup"><span data-stu-id="a46f5-122">In Blazor Server apps, services are added by calling <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A>, and the `WeatherForecastService` is added to the service container for use by the example `FetchData` component.</span></span>
  * <span data-ttu-id="a46f5-123">`Configure`: Configure le pipeline de traitement des demandes de l’application :</span><span class="sxs-lookup"><span data-stu-id="a46f5-123">`Configure`: Configures the app's request handling pipeline:</span></span>
    * <span data-ttu-id="a46f5-124"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A>est appelé pour configurer un point de terminaison pour la connexion en temps réel avec le navigateur.</span><span class="sxs-lookup"><span data-stu-id="a46f5-124"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> is called to set up an endpoint for the real-time connection with the browser.</span></span> <span data-ttu-id="a46f5-125">La connexion est créée avec [SignalR](xref:signalr/introduction) , qui est une infrastructure permettant d’ajouter des fonctionnalités Web en temps réel aux applications.</span><span class="sxs-lookup"><span data-stu-id="a46f5-125">The connection is created with [SignalR](xref:signalr/introduction), which is a framework for adding real-time web functionality to apps.</span></span>
    * <span data-ttu-id="a46f5-126">[MapFallbackToPage (« /_Host »)](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) est appelé pour configurer la page racine de l’application (*pages/_Host. cshtml*) et activer la navigation.</span><span class="sxs-lookup"><span data-stu-id="a46f5-126">[MapFallbackToPage("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) is called to set up the root page of the app (*Pages/_Host.cshtml*) and enable navigation.</span></span>

* <span data-ttu-id="a46f5-127">*wwwroot/index.html* ( Blazor webassembly) : page racine de l’application implémentée en tant que page HTML :</span><span class="sxs-lookup"><span data-stu-id="a46f5-127">*wwwroot/index.html* (Blazor WebAssembly): The root page of the app implemented as an HTML page:</span></span>
  * <span data-ttu-id="a46f5-128">Quand une page de l’application est initialement demandée, cette page est rendue et renvoyée dans la réponse.</span><span class="sxs-lookup"><span data-stu-id="a46f5-128">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
  * <span data-ttu-id="a46f5-129">La page spécifie l’emplacement où le `App` composant racine est restitué.</span><span class="sxs-lookup"><span data-stu-id="a46f5-129">The page specifies where the root `App` component is rendered.</span></span> <span data-ttu-id="a46f5-130">Le `App` composant (*app. Razor*) est spécifié en tant qu' `app` élément DOM de la `AddComponent` méthode dans `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="a46f5-130">The `App` component (*App.razor*) is specified as the `app` DOM element to the `AddComponent` method in `Startup.Configure`.</span></span>
  * <span data-ttu-id="a46f5-131">Le `_framework/blazor.webassembly.js` fichier JavaScript est chargé, ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="a46f5-131">The `_framework/blazor.webassembly.js` JavaScript file is loaded, which:</span></span>
    * <span data-ttu-id="a46f5-132">Télécharge le Runtime .NET, l’application et les dépendances de l’application.</span><span class="sxs-lookup"><span data-stu-id="a46f5-132">Downloads the .NET runtime, the app, and the app's dependencies.</span></span>
    * <span data-ttu-id="a46f5-133">Initialise le runtime pour exécuter l’application.</span><span class="sxs-lookup"><span data-stu-id="a46f5-133">Initializes the runtime to run the app.</span></span>

* <span data-ttu-id="a46f5-134">*App. Razor*: composant racine de l’application qui configure le routage côté client à l’aide du <xref:Microsoft.AspNetCore.Components.Routing.Router> composant.</span><span class="sxs-lookup"><span data-stu-id="a46f5-134">*App.razor*: The root component of the app that sets up client-side routing using the <xref:Microsoft.AspNetCore.Components.Routing.Router> component.</span></span> <span data-ttu-id="a46f5-135">Le <xref:Microsoft.AspNetCore.Components.Routing.Router> composant intercepte la navigation dans le navigateur et affiche la page qui correspond à l’adresse demandée.</span><span class="sxs-lookup"><span data-stu-id="a46f5-135">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component intercepts browser navigation and renders the page that matches the requested address.</span></span>

* <span data-ttu-id="a46f5-136">Dossier *pages* : contient les composants/pages routables (*. Razor*) qui composent l' Blazor application et la Razor page racine d’une Blazor application serveur.</span><span class="sxs-lookup"><span data-stu-id="a46f5-136">*Pages* folder: Contains the routable components/pages (*.razor*) that make up the Blazor app and the root Razor page of a Blazor Server app.</span></span> <span data-ttu-id="a46f5-137">L’itinéraire de chaque page est spécifié à l’aide de la [`@page`](xref:mvc/views/razor#page) directive.</span><span class="sxs-lookup"><span data-stu-id="a46f5-137">The route for each page is specified using the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="a46f5-138">Le modèle comprend les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="a46f5-138">The template includes the following:</span></span>
  * <span data-ttu-id="a46f5-139">*_Host. cshtml* ( Blazor Server) : la page racine de l’application est implémentée en tant que Razor page :</span><span class="sxs-lookup"><span data-stu-id="a46f5-139">*_Host.cshtml* (Blazor Server): The root page of the app implemented as a Razor Page:</span></span>
    * <span data-ttu-id="a46f5-140">Quand une page de l’application est initialement demandée, cette page est rendue et renvoyée dans la réponse.</span><span class="sxs-lookup"><span data-stu-id="a46f5-140">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
    * <span data-ttu-id="a46f5-141">Le `_framework/blazor.server.js` fichier JavaScript est chargé, ce qui configure la connexion en temps réel SignalR entre le navigateur et le serveur.</span><span class="sxs-lookup"><span data-stu-id="a46f5-141">The `_framework/blazor.server.js` JavaScript file is loaded, which sets up the real-time SignalR connection between the browser and the server.</span></span>
    * <span data-ttu-id="a46f5-142">La page hôte spécifie où le `App` composant racine (*app. Razor*) est affiché.</span><span class="sxs-lookup"><span data-stu-id="a46f5-142">The Host page specifies where the root `App` component (*App.razor*) is rendered.</span></span>
  * <span data-ttu-id="a46f5-143">`Counter`(*Counter. Razor*) : implémente la page de compteur.</span><span class="sxs-lookup"><span data-stu-id="a46f5-143">`Counter` (*Counter.razor*): Implements the Counter page.</span></span>
  * <span data-ttu-id="a46f5-144">`Error`(*Error. Razor*, Blazor Application serveur uniquement) : rendu lorsqu’une exception non gérée se produit dans l’application.</span><span class="sxs-lookup"><span data-stu-id="a46f5-144">`Error` (*Error.razor*, Blazor Server app only): Rendered when an unhandled exception occurs in the app.</span></span>
  * <span data-ttu-id="a46f5-145">`FetchData`(*FetchData. Razor*) : implémente la page FETCH Data.</span><span class="sxs-lookup"><span data-stu-id="a46f5-145">`FetchData` (*FetchData.razor*): Implements the Fetch data page.</span></span>
  * <span data-ttu-id="a46f5-146">`Index`(*Index. Razor*) : implémente la page d’hébergement.</span><span class="sxs-lookup"><span data-stu-id="a46f5-146">`Index` (*Index.razor*): Implements the Home page.</span></span>

* <span data-ttu-id="a46f5-147">Dossier *partagé* : contient d’autres composants d’interface utilisateur (*. Razor*) utilisés par l’application :</span><span class="sxs-lookup"><span data-stu-id="a46f5-147">*Shared* folder: Contains other UI components (*.razor*) used by the app:</span></span>
  * <span data-ttu-id="a46f5-148">`MainLayout`(*MainLayout. Razor*) : composant de disposition de l’application.</span><span class="sxs-lookup"><span data-stu-id="a46f5-148">`MainLayout` (*MainLayout.razor*): The app's layout component.</span></span>
  * <span data-ttu-id="a46f5-149">`NavMenu`(*NavMenu. Razor*) : implémente la navigation dans l’encadré.</span><span class="sxs-lookup"><span data-stu-id="a46f5-149">`NavMenu` (*NavMenu.razor*): Implements sidebar navigation.</span></span> <span data-ttu-id="a46f5-150">Comprend le [composant NavLink](xref:blazor/fundamentals/routing#navlink-component) ( <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ), qui affiche des liens de navigation vers d’autres Razor composants.</span><span class="sxs-lookup"><span data-stu-id="a46f5-150">Includes the [NavLink component](xref:blazor/fundamentals/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="a46f5-151">Le <xref:Microsoft.AspNetCore.Components.Routing.NavLink> composant indique automatiquement un état sélectionné quand son composant est chargé, ce qui aide l’utilisateur à comprendre quel composant est actuellement affiché.</span><span class="sxs-lookup"><span data-stu-id="a46f5-151">The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>

* <span data-ttu-id="a46f5-152">*_Imports. Razor*: inclut des Razor directives communes à inclure dans les composants de l’application (*. Razor*), tels que les [`@using`](xref:mvc/views/razor#using) directives pour les espaces de noms.</span><span class="sxs-lookup"><span data-stu-id="a46f5-152">*_Imports.razor*: Includes common Razor directives to include in the app's components (*.razor*), such as [`@using`](xref:mvc/views/razor#using) directives for namespaces.</span></span>

* <span data-ttu-id="a46f5-153">Dossier de *données* ( Blazor serveur) : contient la `WeatherForecast` classe et l’implémentation du `WeatherForecastService` qui fournissent des exemples de données météorologiques au composant de l’application `FetchData` .</span><span class="sxs-lookup"><span data-stu-id="a46f5-153">*Data* folder (Blazor Server): Contains the `WeatherForecast` class and implementation of the `WeatherForecastService` that provide example weather data to the app's `FetchData` component.</span></span>

* <span data-ttu-id="a46f5-154">*wwwroot*: dossier [racine Web](xref:fundamentals/index#web-root) de l’application contenant les ressources statiques publiques de l’application.</span><span class="sxs-lookup"><span data-stu-id="a46f5-154">*wwwroot*: The [Web Root](xref:fundamentals/index#web-root) folder for the app containing the app's public static assets.</span></span>

* <span data-ttu-id="a46f5-155">*appsettings.jssur* ( Blazor serveur) : paramètres de configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="a46f5-155">*appsettings.json* (Blazor Server): Configuration settings for the app.</span></span>
