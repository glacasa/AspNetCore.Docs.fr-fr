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
# <a name="aspnet-core-blazor-templates"></a><span data-ttu-id="63950-103">Modèles Blazor de ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="63950-103">ASP.NET Core Blazor templates</span></span>

<span data-ttu-id="63950-104">Par [Daniel Roth](https://github.com/danroth27) et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="63950-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="63950-105">L' Blazor infrastructure fournit des modèles pour développer des applications pour chacun Blazor des modèles d’hébergement :</span><span class="sxs-lookup"><span data-stu-id="63950-105">The Blazor framework provides templates to develop apps for each of the Blazor hosting models:</span></span>

* Blazor<span data-ttu-id="63950-106">Webassembly`blazorwasm`()</span><span class="sxs-lookup"><span data-stu-id="63950-106"> WebAssembly (`blazorwasm`)</span></span>
* Blazor<span data-ttu-id="63950-107">Server (`blazorserver`)</span><span class="sxs-lookup"><span data-stu-id="63950-107"> Server (`blazorserver`)</span></span>

<span data-ttu-id="63950-108">Pour plus d’informations Blazorsur les modèles d’hébergement <xref:blazor/hosting-models>de, consultez.</span><span class="sxs-lookup"><span data-stu-id="63950-108">For more information on Blazor's hosting models, see <xref:blazor/hosting-models>.</span></span>

<span data-ttu-id="63950-109">Pour obtenir des instructions pas à pas sur la création Blazor d’une application à partir d' <xref:blazor/get-started>un modèle, consultez.</span><span class="sxs-lookup"><span data-stu-id="63950-109">For step-by-step instructions on creating a Blazor app from a template, see <xref:blazor/get-started>.</span></span>

<span data-ttu-id="63950-110">Les options de modèle sont disponibles en `--help` passant l’option à la commande [dotnet New](/dotnet/core/tools/dotnet-new) CLI :</span><span class="sxs-lookup"><span data-stu-id="63950-110">Template options are available by passing the `--help` option to the [dotnet new](/dotnet/core/tools/dotnet-new) CLI command:</span></span>

```dotnetcli
dotnet new blazorwasm --help
dotnet new blazorserver --help
```

## <a name="blazor-project-structure"></a>Blazor<span data-ttu-id="63950-111">structure de projet</span><span class="sxs-lookup"><span data-stu-id="63950-111"> project structure</span></span>

<span data-ttu-id="63950-112">Les fichiers et dossiers suivants composent Blazor une application générée à Blazor partir d’un modèle :</span><span class="sxs-lookup"><span data-stu-id="63950-112">The following files and folders make up a Blazor app generated from a Blazor template:</span></span>

* <span data-ttu-id="63950-113">*Program.cs* &ndash; le point d’entrée de l’application qui installe les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="63950-113">*Program.cs* &ndash; The app's entry point that sets up the:</span></span>

  * <span data-ttu-id="63950-114">[Hôte](xref:fundamentals/host/generic-host) ASP.net Core (Blazor serveur)</span><span class="sxs-lookup"><span data-stu-id="63950-114">ASP.NET Core [host](xref:fundamentals/host/generic-host) (Blazor Server)</span></span>
  * <span data-ttu-id="63950-115">Hôte webassemblyBlazor (webassembly) &ndash; le code de ce fichier est propre aux applications créées Blazor à partir du modèle`blazorwasm`webassembly ().</span><span class="sxs-lookup"><span data-stu-id="63950-115">WebAssembly host (Blazor WebAssembly) &ndash; The code in this file is unique to apps created from the Blazor WebAssembly template (`blazorwasm`).</span></span>
    * <span data-ttu-id="63950-116">Le `App` composant, qui est le composant racine de l’application, est spécifié en tant `app` qu’élément DOM de `Add` la méthode.</span><span class="sxs-lookup"><span data-stu-id="63950-116">The `App` component, which is the root component of the app, is specified as the `app` DOM element to the `Add` method.</span></span>
    * <span data-ttu-id="63950-117">Les services peuvent être configurés `ConfigureServices` avec la méthode sur le générateur de l’hôte `builder.Services.AddSingleton<IMyDependency, MyDependency>();`(par exemple,).</span><span class="sxs-lookup"><span data-stu-id="63950-117">Services can be configured with the `ConfigureServices` method on the host builder (for example, `builder.Services.AddSingleton<IMyDependency, MyDependency>();`).</span></span>
    * <span data-ttu-id="63950-118">La configuration peut être fournie par le biais du`builder.Configuration`générateur d’ordinateur hôte ().</span><span class="sxs-lookup"><span data-stu-id="63950-118">Configuration can be supplied via the host builder (`builder.Configuration`).</span></span>

* <span data-ttu-id="63950-119">*Startup.cs* (Blazor Server) &ndash; contient la logique de démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="63950-119">*Startup.cs* (Blazor Server) &ndash; Contains the app's startup logic.</span></span> <span data-ttu-id="63950-120">La `Startup` classe définit deux méthodes :</span><span class="sxs-lookup"><span data-stu-id="63950-120">The `Startup` class defines two methods:</span></span>

  * <span data-ttu-id="63950-121">`ConfigureServices`&ndash; Configure les services d' [injection de dépendances](xref:fundamentals/dependency-injection) de l’application.</span><span class="sxs-lookup"><span data-stu-id="63950-121">`ConfigureServices` &ndash; Configures the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) services.</span></span> <span data-ttu-id="63950-122">Dans Blazor les applications serveur, les services sont ajoutés <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor*>en appelant, `WeatherForecastService` et le est ajouté au conteneur de service pour une utilisation `FetchData` par l’exemple de composant.</span><span class="sxs-lookup"><span data-stu-id="63950-122">In Blazor Server apps, services are added by calling <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor*>, and the `WeatherForecastService` is added to the service container for use by the example `FetchData` component.</span></span>
  * <span data-ttu-id="63950-123">`Configure`&ndash; Configure le pipeline de traitement des demandes de l’application :</span><span class="sxs-lookup"><span data-stu-id="63950-123">`Configure` &ndash; Configures the app's request handling pipeline:</span></span>
    * <span data-ttu-id="63950-124"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub*>est appelé pour configurer un point de terminaison pour la connexion en temps réel avec le navigateur.</span><span class="sxs-lookup"><span data-stu-id="63950-124"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub*> is called to set up an endpoint for the real-time connection with the browser.</span></span> <span data-ttu-id="63950-125">La connexion est créée avec [SignalR](xref:signalr/introduction), qui est une infrastructure permettant d’ajouter des fonctionnalités Web en temps réel aux applications.</span><span class="sxs-lookup"><span data-stu-id="63950-125">The connection is created with [SignalR](xref:signalr/introduction), which is a framework for adding real-time web functionality to apps.</span></span>
    * <span data-ttu-id="63950-126">[MapFallbackToPage (« /_Host »)](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) est appelé pour configurer la page racine de l’application (*pages/_Host. cshtml*) et activer la navigation.</span><span class="sxs-lookup"><span data-stu-id="63950-126">[MapFallbackToPage("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) is called to set up the root page of the app (*Pages/_Host.cshtml*) and enable navigation.</span></span>

* <span data-ttu-id="63950-127">*wwwroot/index.html* (Blazor webassembly &ndash; ) page racine de l’application implémentée en tant que page HTML :</span><span class="sxs-lookup"><span data-stu-id="63950-127">*wwwroot/index.html* (Blazor WebAssembly) &ndash; The root page of the app implemented as an HTML page:</span></span>
  * <span data-ttu-id="63950-128">Quand une page de l’application est initialement demandée, cette page est rendue et renvoyée dans la réponse.</span><span class="sxs-lookup"><span data-stu-id="63950-128">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
  * <span data-ttu-id="63950-129">La page spécifie l’emplacement `App` où le composant racine est restitué.</span><span class="sxs-lookup"><span data-stu-id="63950-129">The page specifies where the root `App` component is rendered.</span></span> <span data-ttu-id="63950-130">Le `App` composant (*app. Razor*) est spécifié en tant `app` qu’élément DOM de `AddComponent` la méthode `Startup.Configure`dans.</span><span class="sxs-lookup"><span data-stu-id="63950-130">The `App` component (*App.razor*) is specified as the `app` DOM element to the `AddComponent` method in `Startup.Configure`.</span></span>
  * <span data-ttu-id="63950-131">Le `_framework/blazor.webassembly.js` fichier JavaScript est chargé, ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="63950-131">The `_framework/blazor.webassembly.js` JavaScript file is loaded, which:</span></span>
    * <span data-ttu-id="63950-132">Télécharge le Runtime .NET, l’application et les dépendances de l’application.</span><span class="sxs-lookup"><span data-stu-id="63950-132">Downloads the .NET runtime, the app, and the app's dependencies.</span></span>
    * <span data-ttu-id="63950-133">Initialise le runtime pour exécuter l’application.</span><span class="sxs-lookup"><span data-stu-id="63950-133">Initializes the runtime to run the app.</span></span>

* <span data-ttu-id="63950-134">*App. Razor* &ndash; le composant racine de l’application qui configure le routage côté client à l' <xref:Microsoft.AspNetCore.Components.Routing.Router> aide du composant.</span><span class="sxs-lookup"><span data-stu-id="63950-134">*App.razor* &ndash; The root component of the app that sets up client-side routing using the <xref:Microsoft.AspNetCore.Components.Routing.Router> component.</span></span> <span data-ttu-id="63950-135">Le `Router` composant intercepte la navigation dans le navigateur et affiche la page qui correspond à l’adresse demandée.</span><span class="sxs-lookup"><span data-stu-id="63950-135">The `Router` component intercepts browser navigation and renders the page that matches the requested address.</span></span>

* <span data-ttu-id="63950-136">*Pages* Le dossier &ndash; pages contient les composants/pages routables *(. Razor*) qui composent Blazor l’application et Razor la page racine Blazor d’une application serveur.</span><span class="sxs-lookup"><span data-stu-id="63950-136">*Pages* folder &ndash; Contains the routable components/pages (*.razor*) that make up the Blazor app and the root Razor page of a Blazor Server app.</span></span> <span data-ttu-id="63950-137">L’itinéraire de chaque page est spécifié à l' [`@page`](xref:mvc/views/razor#page) aide de la directive.</span><span class="sxs-lookup"><span data-stu-id="63950-137">The route for each page is specified using the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="63950-138">Le modèle comprend les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="63950-138">The template includes the following:</span></span>
  * <span data-ttu-id="63950-139">*_Host. cshtml* (Blazor Server) &ndash; la page racine de l’application est implémentée Razor en tant que page :</span><span class="sxs-lookup"><span data-stu-id="63950-139">*_Host.cshtml* (Blazor Server) &ndash; The root page of the app implemented as a Razor Page:</span></span>
    * <span data-ttu-id="63950-140">Quand une page de l’application est initialement demandée, cette page est rendue et renvoyée dans la réponse.</span><span class="sxs-lookup"><span data-stu-id="63950-140">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
    * <span data-ttu-id="63950-141">Le `_framework/blazor.server.js` fichier JavaScript est chargé, ce qui configure la SignalR connexion en temps réel entre le navigateur et le serveur.</span><span class="sxs-lookup"><span data-stu-id="63950-141">The `_framework/blazor.server.js` JavaScript file is loaded, which sets up the real-time SignalR connection between the browser and the server.</span></span>
    * <span data-ttu-id="63950-142">La page hôte spécifie où le `App` composant racine (*app. Razor*) est affiché.</span><span class="sxs-lookup"><span data-stu-id="63950-142">The Host page specifies where the root `App` component (*App.razor*) is rendered.</span></span>
  * <span data-ttu-id="63950-143">`Counter`(*Counter. Razor*) &ndash; implémente la page de compteur.</span><span class="sxs-lookup"><span data-stu-id="63950-143">`Counter` (*Counter.razor*) &ndash; Implements the Counter page.</span></span>
  * <span data-ttu-id="63950-144">`Error`(*Error. Razor*, Blazor application serveur uniquement) &ndash; Rendu lorsqu’une exception non gérée se produit dans l’application.</span><span class="sxs-lookup"><span data-stu-id="63950-144">`Error` (*Error.razor*, Blazor Server app only) &ndash; Rendered when an unhandled exception occurs in the app.</span></span>
  * <span data-ttu-id="63950-145">`FetchData`(*FetchData. Razor*) &ndash; implémente la page FETCH Data.</span><span class="sxs-lookup"><span data-stu-id="63950-145">`FetchData` (*FetchData.razor*) &ndash; Implements the Fetch data page.</span></span>
  * <span data-ttu-id="63950-146">`Index`(*Index. Razor*) &ndash; implémente la page d’hébergement.</span><span class="sxs-lookup"><span data-stu-id="63950-146">`Index` (*Index.razor*) &ndash; Implements the Home page.</span></span>

* <span data-ttu-id="63950-147">*Shared* Le dossier &ndash; partagé contient d’autres composants d’interface utilisateur (*. Razor*) utilisés par l’application :</span><span class="sxs-lookup"><span data-stu-id="63950-147">*Shared* folder &ndash; Contains other UI components (*.razor*) used by the app:</span></span>
  * <span data-ttu-id="63950-148">`MainLayout`(*MainLayout. Razor*) &ndash; composant de disposition de l’application.</span><span class="sxs-lookup"><span data-stu-id="63950-148">`MainLayout` (*MainLayout.razor*) &ndash; The app's layout component.</span></span>
  * <span data-ttu-id="63950-149">`NavMenu`(*NavMenu. Razor*) &ndash; implémente la navigation dans l’encadré.</span><span class="sxs-lookup"><span data-stu-id="63950-149">`NavMenu` (*NavMenu.razor*) &ndash; Implements sidebar navigation.</span></span> <span data-ttu-id="63950-150">Comprend le [composant NavLink](xref:blazor/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), qui affiche des liens de navigation vers Razor d’autres composants.</span><span class="sxs-lookup"><span data-stu-id="63950-150">Includes the [NavLink component](xref:blazor/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="63950-151">Le `NavLink` composant indique automatiquement un état sélectionné quand son composant est chargé, ce qui aide l’utilisateur à comprendre quel composant est actuellement affiché.</span><span class="sxs-lookup"><span data-stu-id="63950-151">The `NavLink` component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>

* <span data-ttu-id="63950-152">*_Imports. Razor* &ndash; inclut des Razor directives communes à inclure dans les composants de l’application (*. Razor*), [`@using`](xref:mvc/views/razor#using) tels que les directives pour les espaces de noms.</span><span class="sxs-lookup"><span data-stu-id="63950-152">*_Imports.razor* &ndash; Includes common Razor directives to include in the app's components (*.razor*), such as [`@using`](xref:mvc/views/razor#using) directives for namespaces.</span></span>

* <span data-ttu-id="63950-153">*Data* Dossier de donnéesBlazor (serveur &ndash; ) contient `WeatherForecast` la classe et l’implémentation `WeatherForecastService` du qui fournissent des exemples de données météorologiques `FetchData` au composant de l’application.</span><span class="sxs-lookup"><span data-stu-id="63950-153">*Data* folder (Blazor Server) &ndash; Contains the `WeatherForecast` class and implementation of the `WeatherForecastService` that provide example weather data to the app's `FetchData` component.</span></span>

* <span data-ttu-id="63950-154">*wwwroot* &ndash; répertoire [racine Web](xref:fundamentals/index#web-root) de l’application contenant les ressources statiques publiques de l’application.</span><span class="sxs-lookup"><span data-stu-id="63950-154">*wwwroot* &ndash; The [Web Root](xref:fundamentals/index#web-root) folder for the app containing the app's public static assets.</span></span>

* <span data-ttu-id="63950-155">paramètres de configuration *appSettings. JSON* (Blazor serveur) &ndash; pour l’application.</span><span class="sxs-lookup"><span data-stu-id="63950-155">*appsettings.json* (Blazor Server) &ndash; Configuration settings for the app.</span></span>
