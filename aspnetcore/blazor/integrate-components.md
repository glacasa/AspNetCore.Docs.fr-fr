---
title: Intégrer des composants ASP.NET Core Razor dans des applications Razor Pages et MVC
author: guardrex
description: En savoir plus sur les scénarios de liaison de données pour Blazor les composants et les éléments DOM dans les applications.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/25/2020
no-loc:
- Blazor
- SignalR
uid: blazor/integrate-components
ms.openlocfilehash: 4e2103b7e8b65478808093d7a31e8cfe29b04984
ms.sourcegitcommit: f9a5069577e8f7c53f8bcec9e13e117950f4f033
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82558918"
---
# <a name="integrate-aspnet-core-razor-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="cc1ce-103">Intégrer des composants ASP.NET Core Razor dans des applications Razor Pages et MVC</span><span class="sxs-lookup"><span data-stu-id="cc1ce-103">Integrate ASP.NET Core Razor components into Razor Pages and MVC apps</span></span>

<span data-ttu-id="cc1ce-104">Par [Luke Latham](https://github.com/guardrex) et [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="cc1ce-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="cc1ce-105">Les composants Razor peuvent être intégrés dans des applications Razor Pages et MVC.</span><span class="sxs-lookup"><span data-stu-id="cc1ce-105">Razor components can be integrated into Razor Pages and MVC apps.</span></span> <span data-ttu-id="cc1ce-106">Lorsque la page ou la vue est restituée, les composants peuvent être prérendus en même temps.</span><span class="sxs-lookup"><span data-stu-id="cc1ce-106">When the page or view is rendered, components can be prerendered at the same time.</span></span>

<span data-ttu-id="cc1ce-107">Après avoir [préparé l’application](#prepare-the-app), suivez les instructions des sections suivantes en fonction des exigences de l’application :</span><span class="sxs-lookup"><span data-stu-id="cc1ce-107">After [preparing the app](#prepare-the-app), use the guidance in the following sections depending on the app's requirements:</span></span>

* <span data-ttu-id="cc1ce-108">Composants &ndash; routables pour les composants qui sont directement routables à partir des demandes de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="cc1ce-108">Routable components &ndash; For components that are directly routable from user requests.</span></span> <span data-ttu-id="cc1ce-109">Suivez ces instructions lorsque les visiteurs doivent être en mesure de faire une requête HTTP dans leur navigateur pour un composant [`@page`](xref:mvc/views/razor#page) avec une directive.</span><span class="sxs-lookup"><span data-stu-id="cc1ce-109">Follow this guidance when visitors should be able to make an HTTP request in their browser for a component with an [`@page`](xref:mvc/views/razor#page) directive.</span></span>
  * [<span data-ttu-id="cc1ce-110">Utiliser des composants routables dans une application Razor Pages</span><span class="sxs-lookup"><span data-stu-id="cc1ce-110">Use routable components in a Razor Pages app</span></span>](#use-routable-components-in-a-razor-pages-app)
  * [<span data-ttu-id="cc1ce-111">Utiliser des composants routables dans une application MVC</span><span class="sxs-lookup"><span data-stu-id="cc1ce-111">Use routable components in an MVC app</span></span>](#use-routable-components-in-an-mvc-app)
* <span data-ttu-id="cc1ce-112">[Rendez les composants à partir d’une page ou d’une vue](#render-components-from-a-page-or-view) &ndash; pour les composants qui ne sont pas directement routables à partir des demandes de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="cc1ce-112">[Render components from a page or view](#render-components-from-a-page-or-view) &ndash; For components that aren't directly routable from user requests.</span></span> <span data-ttu-id="cc1ce-113">Suivez ces instructions lorsque l’application incorpore des composants dans des pages et des vues existantes avec le [tag Helper Component](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="cc1ce-113">Follow this guidance when the app embeds components into existing pages and views with the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

## <a name="prepare-the-app"></a><span data-ttu-id="cc1ce-114">Préparer l’application</span><span class="sxs-lookup"><span data-stu-id="cc1ce-114">Prepare the app</span></span>

<span data-ttu-id="cc1ce-115">Une Razor Pages ou une application MVC existante peut intégrer des composants Razor dans des pages et des vues :</span><span class="sxs-lookup"><span data-stu-id="cc1ce-115">An existing Razor Pages or MVC app can integrate Razor components into pages and views:</span></span>

1. <span data-ttu-id="cc1ce-116">Dans le fichier de disposition de l’application (*_Layout. cshtml*) :</span><span class="sxs-lookup"><span data-stu-id="cc1ce-116">In the app's layout file (*_Layout.cshtml*):</span></span>

   * <span data-ttu-id="cc1ce-117">Ajoutez la balise suivante `<base>` à `<head>` l’élément :</span><span class="sxs-lookup"><span data-stu-id="cc1ce-117">Add the following `<base>` tag to the `<head>` element:</span></span>

     ```html
     <base href="~/" />
     ```

     <span data-ttu-id="cc1ce-118">La `href` valeur (le *chemin d’accès de base*de l’application) dans l’exemple précédent suppose que l’application se trouve dans le chemin`/`d’URL racine ().</span><span class="sxs-lookup"><span data-stu-id="cc1ce-118">The `href` value (the *app base path*) in the preceding example assumes that the app resides at the root URL path (`/`).</span></span> <span data-ttu-id="cc1ce-119">Si l’application est une sous-application, suivez les instructions de la section *chemin d’accès* de base <xref:host-and-deploy/blazor/index#app-base-path> de l’application de l’article.</span><span class="sxs-lookup"><span data-stu-id="cc1ce-119">If the app is a sub-application, follow the guidance in the *App base path* section of the <xref:host-and-deploy/blazor/index#app-base-path> article.</span></span>

     <span data-ttu-id="cc1ce-120">Le fichier *_Layout. cshtml* se trouve dans le dossier *pages/Shared* d’une application Razor pages ou d’un dossier *Views/Shared* dans une application MVC.</span><span class="sxs-lookup"><span data-stu-id="cc1ce-120">The *_Layout.cshtml* file is located in the *Pages/Shared* folder in a Razor Pages app or *Views/Shared* folder in an MVC app.</span></span>

   * <span data-ttu-id="cc1ce-121">Ajoutez une `<script>` balise pour le script *éblouissant. Server. js* immédiatement avant la balise `</body>` de fermeture :</span><span class="sxs-lookup"><span data-stu-id="cc1ce-121">Add a `<script>` tag for the *blazor.server.js* script immediately before of the closing `</body>` tag:</span></span>

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     <span data-ttu-id="cc1ce-122">L’infrastructure ajoute le script *éblouissant. Server. js* à l’application.</span><span class="sxs-lookup"><span data-stu-id="cc1ce-122">The framework adds the *blazor.server.js* script to the app.</span></span> <span data-ttu-id="cc1ce-123">Il n’est pas nécessaire d’ajouter manuellement le script à l’application.</span><span class="sxs-lookup"><span data-stu-id="cc1ce-123">There's no need to manually add the script to the app.</span></span>

1. <span data-ttu-id="cc1ce-124">Ajoutez un fichier *_Imports. Razor* au dossier racine du projet avec le contenu suivant (remplacez le dernier espace de noms, `MyAppNamespace`, par l’espace de noms de l’application) :</span><span class="sxs-lookup"><span data-stu-id="cc1ce-124">Add an *_Imports.razor* file to the root folder of the project with the following content (change the last namespace, `MyAppNamespace`, to the namespace of the app):</span></span>

   ```razor
   @using System.Net.Http
   @using Microsoft.AspNetCore.Authorization
   @using Microsoft.AspNetCore.Components.Authorization
   @using Microsoft.AspNetCore.Components.Forms
   @using Microsoft.AspNetCore.Components.Routing
   @using Microsoft.AspNetCore.Components.Web
   @using Microsoft.JSInterop
   @using MyAppNamespace
   ```

1. <span data-ttu-id="cc1ce-125">Dans `Startup.ConfigureServices`, inscrivez le service du serveur éblouissant :</span><span class="sxs-lookup"><span data-stu-id="cc1ce-125">In `Startup.ConfigureServices`, register the Blazor Server service:</span></span>

   ```csharp
   services.AddServerSideBlazor();
   ```

1. <span data-ttu-id="cc1ce-126">Dans `Startup.Configure`, ajoutez le point de terminaison du Hub `app.UseEndpoints`éblouissant à :</span><span class="sxs-lookup"><span data-stu-id="cc1ce-126">In `Startup.Configure`, add the Blazor Hub endpoint to `app.UseEndpoints`:</span></span>

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. <span data-ttu-id="cc1ce-127">Intégrer des composants dans n’importe quelle page ou vue.</span><span class="sxs-lookup"><span data-stu-id="cc1ce-127">Integrate components into any page or view.</span></span> <span data-ttu-id="cc1ce-128">Pour plus d’informations, consultez la section [rendre les composants à partir d’une page ou d’une vue](#render-components-from-a-page-or-view) .</span><span class="sxs-lookup"><span data-stu-id="cc1ce-128">For more information, see the [Render components from a page or view](#render-components-from-a-page-or-view) section.</span></span>

## <a name="use-routable-components-in-a-razor-pages-app"></a><span data-ttu-id="cc1ce-129">Utiliser des composants routables dans une application Razor Pages</span><span class="sxs-lookup"><span data-stu-id="cc1ce-129">Use routable components in a Razor Pages app</span></span>

<span data-ttu-id="cc1ce-130">*Cette section concerne l’ajout de composants qui sont directement routables à partir des demandes des utilisateurs.*</span><span class="sxs-lookup"><span data-stu-id="cc1ce-130">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="cc1ce-131">Pour prendre en charge les composants Razor routables dans les applications Razor Pages :</span><span class="sxs-lookup"><span data-stu-id="cc1ce-131">To support routable Razor components in Razor Pages apps:</span></span>

1. <span data-ttu-id="cc1ce-132">Suivez les instructions de la section [préparer l’application](#prepare-the-app) .</span><span class="sxs-lookup"><span data-stu-id="cc1ce-132">Follow the guidance in the [Prepare the app](#prepare-the-app) section.</span></span>

1. <span data-ttu-id="cc1ce-133">Ajoutez un fichier *app. Razor* à la racine du projet avec le contenu suivant :</span><span class="sxs-lookup"><span data-stu-id="cc1ce-133">Add an *App.razor* file to the project root with the following content:</span></span>

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

1. <span data-ttu-id="cc1ce-134">Ajoutez un fichier *_Host. cshtml* au dossier *pages* avec le contenu suivant :</span><span class="sxs-lookup"><span data-stu-id="cc1ce-134">Add a *_Host.cshtml* file to the *Pages* folder with the following content:</span></span>

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="cc1ce-135">Les composants utilisent le fichier *_Layout. cshtml* partagé pour leur disposition.</span><span class="sxs-lookup"><span data-stu-id="cc1ce-135">Components use the shared *_Layout.cshtml* file for their layout.</span></span>

   <span data-ttu-id="cc1ce-136"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>Configure si le `App` composant :</span><span class="sxs-lookup"><span data-stu-id="cc1ce-136"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="cc1ce-137">Est prérendu dans la page.</span><span class="sxs-lookup"><span data-stu-id="cc1ce-137">Is prerendered into the page.</span></span>
   * <span data-ttu-id="cc1ce-138">Est rendu en HTML statique sur la page ou s’il contient les informations nécessaires pour démarrer une application éblouissant à partir de l’agent utilisateur.</span><span class="sxs-lookup"><span data-stu-id="cc1ce-138">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   | <span data-ttu-id="cc1ce-139">Mode de rendu</span><span class="sxs-lookup"><span data-stu-id="cc1ce-139">Render Mode</span></span> | <span data-ttu-id="cc1ce-140">Description</span><span class="sxs-lookup"><span data-stu-id="cc1ce-140">Description</span></span> |
   | ----------- | ----------- |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="cc1ce-141">Génère le rendu `App` du composant en HTML statique et comprend un marqueur pour une application de serveur éblouissante.</span><span class="sxs-lookup"><span data-stu-id="cc1ce-141">Renders the `App` component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="cc1ce-142">Au démarrage de l’agent utilisateur, ce marqueur est utilisé pour démarrer une application éblouissante.</span><span class="sxs-lookup"><span data-stu-id="cc1ce-142">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="cc1ce-143">Restitue un marqueur pour une application de serveur éblouissante.</span><span class="sxs-lookup"><span data-stu-id="cc1ce-143">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="cc1ce-144">La `App` sortie du composant n’est pas incluse.</span><span class="sxs-lookup"><span data-stu-id="cc1ce-144">Output from the `App` component isn't included.</span></span> <span data-ttu-id="cc1ce-145">Au démarrage de l’agent utilisateur, ce marqueur est utilisé pour démarrer une application éblouissante.</span><span class="sxs-lookup"><span data-stu-id="cc1ce-145">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="cc1ce-146">Génère le rendu `App` du composant en HTML statique.</span><span class="sxs-lookup"><span data-stu-id="cc1ce-146">Renders the `App` component into static HTML.</span></span> |

   <span data-ttu-id="cc1ce-147">Pour plus d’informations sur le tag Helper composant, consultez <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="cc1ce-147">For more information on the Component Tag Helper, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="cc1ce-148">Ajoutez un itinéraire de priorité basse pour la page *_Host. cshtml* à la configuration de `Startup.Configure`point de terminaison dans :</span><span class="sxs-lookup"><span data-stu-id="cc1ce-148">Add a low-priority route for the *_Host.cshtml* page to endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. <span data-ttu-id="cc1ce-149">Ajoutez des composants routables à l’application.</span><span class="sxs-lookup"><span data-stu-id="cc1ce-149">Add routable components to the app.</span></span> <span data-ttu-id="cc1ce-150">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="cc1ce-150">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="cc1ce-151">Pour plus d’informations sur les espaces de noms, consultez la section [espaces de noms de composants](#component-namespaces) .</span><span class="sxs-lookup"><span data-stu-id="cc1ce-151">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="use-routable-components-in-an-mvc-app"></a><span data-ttu-id="cc1ce-152">Utiliser des composants routables dans une application MVC</span><span class="sxs-lookup"><span data-stu-id="cc1ce-152">Use routable components in an MVC app</span></span>

<span data-ttu-id="cc1ce-153">*Cette section concerne l’ajout de composants qui sont directement routables à partir des demandes des utilisateurs.*</span><span class="sxs-lookup"><span data-stu-id="cc1ce-153">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="cc1ce-154">Pour prendre en charge les composants Razor routables dans les applications MVC :</span><span class="sxs-lookup"><span data-stu-id="cc1ce-154">To support routable Razor components in MVC apps:</span></span>

1. <span data-ttu-id="cc1ce-155">Suivez les instructions de la section [préparer l’application](#prepare-the-app) .</span><span class="sxs-lookup"><span data-stu-id="cc1ce-155">Follow the guidance in the [Prepare the app](#prepare-the-app) section.</span></span>

1. <span data-ttu-id="cc1ce-156">Ajoutez un fichier *app. Razor* à la racine du projet avec le contenu suivant :</span><span class="sxs-lookup"><span data-stu-id="cc1ce-156">Add an *App.razor* file to the root of the project with the following content:</span></span>

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

1. <span data-ttu-id="cc1ce-157">Ajoutez un fichier *_Host. cshtml* au dossier *views/de démarrage* avec le contenu suivant :</span><span class="sxs-lookup"><span data-stu-id="cc1ce-157">Add a *_Host.cshtml* file to the *Views/Home* folder with the following content:</span></span>

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="cc1ce-158">Les composants utilisent le fichier *_Layout. cshtml* partagé pour leur disposition.</span><span class="sxs-lookup"><span data-stu-id="cc1ce-158">Components use the shared *_Layout.cshtml* file for their layout.</span></span>
   
   <span data-ttu-id="cc1ce-159"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>Configure si le `App` composant :</span><span class="sxs-lookup"><span data-stu-id="cc1ce-159"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="cc1ce-160">Est prérendu dans la page.</span><span class="sxs-lookup"><span data-stu-id="cc1ce-160">Is prerendered into the page.</span></span>
   * <span data-ttu-id="cc1ce-161">Est rendu en HTML statique sur la page ou s’il contient les informations nécessaires pour démarrer une application éblouissant à partir de l’agent utilisateur.</span><span class="sxs-lookup"><span data-stu-id="cc1ce-161">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   | <span data-ttu-id="cc1ce-162">Mode de rendu</span><span class="sxs-lookup"><span data-stu-id="cc1ce-162">Render Mode</span></span> | <span data-ttu-id="cc1ce-163">Description</span><span class="sxs-lookup"><span data-stu-id="cc1ce-163">Description</span></span> |
   | ----------- | ----------- |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="cc1ce-164">Génère le rendu `App` du composant en HTML statique et comprend un marqueur Blazor pour une application serveur.</span><span class="sxs-lookup"><span data-stu-id="cc1ce-164">Renders the `App` component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="cc1ce-165">Au démarrage de l’agent utilisateur, ce marqueur est utilisé pour démarrer Blazor une application.</span><span class="sxs-lookup"><span data-stu-id="cc1ce-165">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="cc1ce-166">Restitue un marqueur pour Blazor une application serveur.</span><span class="sxs-lookup"><span data-stu-id="cc1ce-166">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="cc1ce-167">La `App` sortie du composant n’est pas incluse.</span><span class="sxs-lookup"><span data-stu-id="cc1ce-167">Output from the `App` component isn't included.</span></span> <span data-ttu-id="cc1ce-168">Au démarrage de l’agent utilisateur, ce marqueur est utilisé pour démarrer Blazor une application.</span><span class="sxs-lookup"><span data-stu-id="cc1ce-168">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="cc1ce-169">Génère le rendu `App` du composant en HTML statique.</span><span class="sxs-lookup"><span data-stu-id="cc1ce-169">Renders the `App` component into static HTML.</span></span> |

   <span data-ttu-id="cc1ce-170">Pour plus d’informations sur le tag Helper composant, consultez <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="cc1ce-170">For more information on the Component Tag Helper, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="cc1ce-171">Ajoutez une action au contrôleur d’hébergement :</span><span class="sxs-lookup"><span data-stu-id="cc1ce-171">Add an action to the Home controller:</span></span>

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. <span data-ttu-id="cc1ce-172">Ajoutez un itinéraire de faible priorité pour l’action de contrôleur qui retourne la vue *_Host. cshtml* à la configuration du `Startup.Configure`point de terminaison dans :</span><span class="sxs-lookup"><span data-stu-id="cc1ce-172">Add a low-priority route for the controller action that returns the *_Host.cshtml* view to the endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. <span data-ttu-id="cc1ce-173">Créez un dossier *pages* et ajoutez des composants routables à l’application.</span><span class="sxs-lookup"><span data-stu-id="cc1ce-173">Create a *Pages* folder and add routable components to the app.</span></span> <span data-ttu-id="cc1ce-174">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="cc1ce-174">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="cc1ce-175">Pour plus d’informations sur les espaces de noms, consultez la section [espaces de noms de composants](#component-namespaces) .</span><span class="sxs-lookup"><span data-stu-id="cc1ce-175">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="render-components-from-a-page-or-view"></a><span data-ttu-id="cc1ce-176">Rendre les composants à partir d’une page ou d’une vue</span><span class="sxs-lookup"><span data-stu-id="cc1ce-176">Render components from a page or view</span></span>

<span data-ttu-id="cc1ce-177">*Cette section se rapporte à l’ajout de composants à des pages ou à des vues, où les composants ne sont pas directement routés à partir des demandes de l’utilisateur.*</span><span class="sxs-lookup"><span data-stu-id="cc1ce-177">*This section pertains to adding components to pages or views, where the components aren't directly routable from user requests.*</span></span>

<span data-ttu-id="cc1ce-178">Pour afficher un composant à partir d’une page ou d’une vue, utilisez le [tag Helper Component](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="cc1ce-178">To render a component from a page or view, use the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

### <a name="render-stateful-interactive-components"></a><span data-ttu-id="cc1ce-179">Rendu des composants interactifs avec état</span><span class="sxs-lookup"><span data-stu-id="cc1ce-179">Render stateful interactive components</span></span>

<span data-ttu-id="cc1ce-180">Les composants interactifs avec état peuvent être ajoutés à une page ou à une vue Razor.</span><span class="sxs-lookup"><span data-stu-id="cc1ce-180">Stateful interactive components can be added to a Razor page or view.</span></span>

<span data-ttu-id="cc1ce-181">Lors du rendu de la page ou de la vue :</span><span class="sxs-lookup"><span data-stu-id="cc1ce-181">When the page or view renders:</span></span>

* <span data-ttu-id="cc1ce-182">Le composant est prérendu avec la page ou la vue.</span><span class="sxs-lookup"><span data-stu-id="cc1ce-182">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="cc1ce-183">L’état initial du composant utilisé pour le prérendu est perdu.</span><span class="sxs-lookup"><span data-stu-id="cc1ce-183">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="cc1ce-184">Un nouvel état de composant est créé SignalR lorsque la connexion est établie.</span><span class="sxs-lookup"><span data-stu-id="cc1ce-184">New component state is created when the SignalR connection is established.</span></span>

<span data-ttu-id="cc1ce-185">La page Razor suivante affiche un `Counter` composant :</span><span class="sxs-lookup"><span data-stu-id="cc1ce-185">The following Razor page renders a `Counter` component:</span></span>

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

<span data-ttu-id="cc1ce-186">Pour plus d’informations, consultez <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="cc1ce-186">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

### <a name="render-noninteractive-components"></a><span data-ttu-id="cc1ce-187">Rendre les composants non interactifs</span><span class="sxs-lookup"><span data-stu-id="cc1ce-187">Render noninteractive components</span></span>

<span data-ttu-id="cc1ce-188">Dans la page Razor suivante, le `Counter` composant est rendu statiquement avec une valeur initiale spécifiée à l’aide d’un formulaire.</span><span class="sxs-lookup"><span data-stu-id="cc1ce-188">In the following Razor page, the `Counter` component is statically rendered with an initial value that's specified using a form.</span></span> <span data-ttu-id="cc1ce-189">Étant donné que le composant est rendu statiquement, le composant n’est pas interactif :</span><span class="sxs-lookup"><span data-stu-id="cc1ce-189">Since the component is statically rendered, the component isn't interactive:</span></span>

```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialValue" />
    <button type="submit">Set initial value</button>
</form>

<component type="typeof(Counter)" render-mode="Static" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

<span data-ttu-id="cc1ce-190">Pour plus d’informations, consultez <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="cc1ce-190">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

## <a name="component-namespaces"></a><span data-ttu-id="cc1ce-191">Espaces de noms de composants</span><span class="sxs-lookup"><span data-stu-id="cc1ce-191">Component namespaces</span></span>

<span data-ttu-id="cc1ce-192">Lorsque vous utilisez un dossier personnalisé pour stocker les composants de l’application, ajoutez l’espace de noms qui représente le dossier à la page/la vue ou au fichier *_ViewImports. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="cc1ce-192">When using a custom folder to hold the app's components, add the namespace representing the folder to either the page/view or to the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="cc1ce-193">Dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="cc1ce-193">In the following example:</span></span>

* <span data-ttu-id="cc1ce-194">Accédez `MyAppNamespace` à l’espace de noms de l’application.</span><span class="sxs-lookup"><span data-stu-id="cc1ce-194">Change `MyAppNamespace` to the app's namespace.</span></span>
* <span data-ttu-id="cc1ce-195">Si un dossier nommé *Components* n’est pas utilisé pour contenir les `Components` composants, accédez au dossier dans lequel se trouvent les composants.</span><span class="sxs-lookup"><span data-stu-id="cc1ce-195">If a folder named *Components* isn't used to hold the components, change `Components` to the folder where the components reside.</span></span>

```cshtml
@using MyAppNamespace.Components
```

<span data-ttu-id="cc1ce-196">Le fichier *_ViewImports. cshtml* se trouve dans le dossier *pages* d’une application Razor pages ou du dossier *views* d’une application MVC.</span><span class="sxs-lookup"><span data-stu-id="cc1ce-196">The *_ViewImports.cshtml* file is located in the *Pages* folder of a Razor Pages app or the *Views* folder of an MVC app.</span></span>

<span data-ttu-id="cc1ce-197">Pour plus d’informations, consultez <xref:blazor/components#import-components>.</span><span class="sxs-lookup"><span data-stu-id="cc1ce-197">For more information, see <xref:blazor/components#import-components>.</span></span>
