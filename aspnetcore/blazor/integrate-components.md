---
title: Intégrer les composants ASP.NET Core Razor dans les applications Razor Pages et MVC
author: guardrex
description: Renseignez-vous sur les scénarios de Blazor liaison de données pour les composants et les éléments DOM dans les applications.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/01/2020
no-loc:
- Blazor
- SignalR
uid: blazor/integrate-components
ms.openlocfilehash: 6efa84c550a4605bde5e1f2bca4f2d1aa4a2667b
ms.sourcegitcommit: e8dc30453af8bbefcb61857987090d79230a461d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/11/2020
ms.locfileid: "81123360"
---
# <a name="integrate-aspnet-core-razor-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="cf11d-103">Intégrer les composants ASP.NET Core Razor dans les applications Razor Pages et MVC</span><span class="sxs-lookup"><span data-stu-id="cf11d-103">Integrate ASP.NET Core Razor components into Razor Pages and MVC apps</span></span>

<span data-ttu-id="cf11d-104">Par [Luke Latham](https://github.com/guardrex) et [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="cf11d-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="cf11d-105">Les composants Razor peuvent être intégrés dans les pages Razor et les applications MVC.</span><span class="sxs-lookup"><span data-stu-id="cf11d-105">Razor components can be integrated into Razor Pages and MVC apps.</span></span> <span data-ttu-id="cf11d-106">Lorsque la page ou la vue est rendue, les composants peuvent être prédilués en même temps.</span><span class="sxs-lookup"><span data-stu-id="cf11d-106">When the page or view is rendered, components can be prerendered at the same time.</span></span>

## <a name="prepare-the-app-to-use-components-in-pages-and-views"></a><span data-ttu-id="cf11d-107">Préparer l’application à utiliser des composants dans les pages et les vues</span><span class="sxs-lookup"><span data-stu-id="cf11d-107">Prepare the app to use components in pages and views</span></span>

<span data-ttu-id="cf11d-108">Une application existante Razor Pages ou MVC peut intégrer des composants Razor dans les pages et les vues :</span><span class="sxs-lookup"><span data-stu-id="cf11d-108">An existing Razor Pages or MVC app can integrate Razor components into pages and views:</span></span>

1. <span data-ttu-id="cf11d-109">Dans le fichier de mise en page de l’application (*_Layout.cshtml )*:</span><span class="sxs-lookup"><span data-stu-id="cf11d-109">In the app's layout file (*_Layout.cshtml*):</span></span>

   * <span data-ttu-id="cf11d-110">Ajouter l’étiquette `<base>` `<head>` suivante à l’élément :</span><span class="sxs-lookup"><span data-stu-id="cf11d-110">Add the following `<base>` tag to the `<head>` element:</span></span>

     ```html
     <base href="~/" />
     ```

     <span data-ttu-id="cf11d-111">La `href` valeur (le *chemin de base de l’application*) dans l’exemple précédent suppose que l’application réside sur le chemin de l’URL racine (`/`).</span><span class="sxs-lookup"><span data-stu-id="cf11d-111">The `href` value (the *app base path*) in the preceding example assumes that the app resides at the root URL path (`/`).</span></span> <span data-ttu-id="cf11d-112">Si l’application est une sous-application, suivez les conseils <xref:host-and-deploy/blazor/index#app-base-path> dans la section du chemin de base de l’application de l’article. *App base path*</span><span class="sxs-lookup"><span data-stu-id="cf11d-112">If the app is a sub-application, follow the guidance in the *App base path* section of the <xref:host-and-deploy/blazor/index#app-base-path> article.</span></span>

     <span data-ttu-id="cf11d-113">Le fichier *_Layout.cshtml* est situé dans le dossier *Pages/Shared* dans une application Razor Pages ou un dossier *Vues/Partage* dans une application MVC.</span><span class="sxs-lookup"><span data-stu-id="cf11d-113">The *_Layout.cshtml* file is located in the *Pages/Shared* folder in a Razor Pages app or *Views/Shared* folder in an MVC app.</span></span>

   * <span data-ttu-id="cf11d-114">Ajoutez `<script>` une balise pour le script *blazor.server.js* immédiatement avant l’étiquette de clôture `</body>` :</span><span class="sxs-lookup"><span data-stu-id="cf11d-114">Add a `<script>` tag for the *blazor.server.js* script immediately before of the closing `</body>` tag:</span></span>

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     <span data-ttu-id="cf11d-115">Le cadre ajoute le script *blazor.server.js* à l’application.</span><span class="sxs-lookup"><span data-stu-id="cf11d-115">The framework adds the *blazor.server.js* script to the app.</span></span> <span data-ttu-id="cf11d-116">Il n’est pas nécessaire d’ajouter manuellement le script à l’application.</span><span class="sxs-lookup"><span data-stu-id="cf11d-116">There's no need to manually add the script to the app.</span></span>

1. <span data-ttu-id="cf11d-117">Ajoutez un fichier *_Imports.razor* au dossier racine du projet avec le contenu suivant `MyAppNamespace`(changer l’espace de nom de famille, , à l’espace nom de l’application):</span><span class="sxs-lookup"><span data-stu-id="cf11d-117">Add an *_Imports.razor* file to the root folder of the project with the following content (change the last namespace, `MyAppNamespace`, to the namespace of the app):</span></span>

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

1. <span data-ttu-id="cf11d-118">Dans `Startup.ConfigureServices`, enregistrez le service Blazor Server :</span><span class="sxs-lookup"><span data-stu-id="cf11d-118">In `Startup.ConfigureServices`, register the Blazor Server service:</span></span>

   ```csharp
   services.AddServerSideBlazor();
   ```

1. <span data-ttu-id="cf11d-119">Dans `Startup.Configure`, ajouter le point de `app.UseEndpoints`terminaison Blazor Hub à :</span><span class="sxs-lookup"><span data-stu-id="cf11d-119">In `Startup.Configure`, add the Blazor Hub endpoint to `app.UseEndpoints`:</span></span>

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. <span data-ttu-id="cf11d-120">Intégrer les composants dans n’importe quelle page ou vue.</span><span class="sxs-lookup"><span data-stu-id="cf11d-120">Integrate components into any page or view.</span></span> <span data-ttu-id="cf11d-121">Pour plus d’informations, consultez les [composants Render à partir d’une page ou d’une](#render-components-from-a-page-or-view) section de vue.</span><span class="sxs-lookup"><span data-stu-id="cf11d-121">For more information, see the [Render components from a page or view](#render-components-from-a-page-or-view) section.</span></span>

## <a name="use-routable-components-in-a-razor-pages-app"></a><span data-ttu-id="cf11d-122">Utilisez des composants routables dans une application Razor Pages</span><span class="sxs-lookup"><span data-stu-id="cf11d-122">Use routable components in a Razor Pages app</span></span>

<span data-ttu-id="cf11d-123">*Cette section concerne l’ajout de composants qui sont directement routables des demandes des utilisateurs.*</span><span class="sxs-lookup"><span data-stu-id="cf11d-123">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="cf11d-124">Pour prendre en charge les composants Razor routables dans les applications Razor Pages :</span><span class="sxs-lookup"><span data-stu-id="cf11d-124">To support routable Razor components in Razor Pages apps:</span></span>

1. <span data-ttu-id="cf11d-125">Suivez les conseils de [l’application Préparer à utiliser des composants dans la](#prepare-the-app-to-use-components-in-pages-and-views) section pages et vues.</span><span class="sxs-lookup"><span data-stu-id="cf11d-125">Follow the guidance in the [Prepare the app to use components in pages and views](#prepare-the-app-to-use-components-in-pages-and-views) section.</span></span>

1. <span data-ttu-id="cf11d-126">Ajoutez un fichier *App.razor* à la racine du projet avec le contenu suivant :</span><span class="sxs-lookup"><span data-stu-id="cf11d-126">Add an *App.razor* file to the project root with the following content:</span></span>

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

1. <span data-ttu-id="cf11d-127">Ajoutez un fichier *_Host.cshtml* au dossier *Pages* avec le contenu suivant :</span><span class="sxs-lookup"><span data-stu-id="cf11d-127">Add a *_Host.cshtml* file to the *Pages* folder with the following content:</span></span>

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="cf11d-128">Les composants utilisent le fichier *_Layout.cshtml* partagé pour leur mise en page.</span><span class="sxs-lookup"><span data-stu-id="cf11d-128">Components use the shared *_Layout.cshtml* file for their layout.</span></span>

   <span data-ttu-id="cf11d-129"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>configure si `App` le composant :</span><span class="sxs-lookup"><span data-stu-id="cf11d-129"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="cf11d-130">Est préditulé dans la page.</span><span class="sxs-lookup"><span data-stu-id="cf11d-130">Is prerendered into the page.</span></span>
   * <span data-ttu-id="cf11d-131">Est rendu comme HTML statique sur la page ou si elle inclut les informations nécessaires pour bootstrap une application Blazor de l’agent utilisateur.</span><span class="sxs-lookup"><span data-stu-id="cf11d-131">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   | <span data-ttu-id="cf11d-132">Mode Rendu</span><span class="sxs-lookup"><span data-stu-id="cf11d-132">Render Mode</span></span> | <span data-ttu-id="cf11d-133">Description</span><span class="sxs-lookup"><span data-stu-id="cf11d-133">Description</span></span> |
   | ----------- | ----------- |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="cf11d-134">Rend le `App` composant en HTML statique et Blazor inclut un marqueur pour une application Server.</span><span class="sxs-lookup"><span data-stu-id="cf11d-134">Renders the `App` component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="cf11d-135">Lorsque l’agent utilisateur démarre, ce marqueur Blazor est utilisé pour bootstrap une application.</span><span class="sxs-lookup"><span data-stu-id="cf11d-135">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="cf11d-136">Rend un marqueur Blazor pour une application Server.</span><span class="sxs-lookup"><span data-stu-id="cf11d-136">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="cf11d-137">La sortie `App` du composant n’est pas incluse.</span><span class="sxs-lookup"><span data-stu-id="cf11d-137">Output from the `App` component isn't included.</span></span> <span data-ttu-id="cf11d-138">Lorsque l’agent utilisateur démarre, ce marqueur Blazor est utilisé pour bootstrap une application.</span><span class="sxs-lookup"><span data-stu-id="cf11d-138">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="cf11d-139">Rend le `App` composant en HTML statique.</span><span class="sxs-lookup"><span data-stu-id="cf11d-139">Renders the `App` component into static HTML.</span></span> |

   <span data-ttu-id="cf11d-140">Pour plus d’informations sur l’aide à l’étiquette de composant, voir <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="cf11d-140">For more information on the Component Tag Helper, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="cf11d-141">Ajoutez un itinéraire de faible priorité pour la page *_Host.cshtml* à la configuration de point de terminaison dans `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="cf11d-141">Add a low-priority route for the *_Host.cshtml* page to endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. <span data-ttu-id="cf11d-142">Ajoutez des composants routables à l’application.</span><span class="sxs-lookup"><span data-stu-id="cf11d-142">Add routable components to the app.</span></span> <span data-ttu-id="cf11d-143">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="cf11d-143">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   <span data-ttu-id="cf11d-144">Pour plus d’informations sur les espaces de nom, consultez la section [Espaces de noms de composant.](#component-namespaces)</span><span class="sxs-lookup"><span data-stu-id="cf11d-144">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="use-routable-components-in-an-mvc-app"></a><span data-ttu-id="cf11d-145">Utilisez des composants routables dans une application MVC</span><span class="sxs-lookup"><span data-stu-id="cf11d-145">Use routable components in an MVC app</span></span>

<span data-ttu-id="cf11d-146">*Cette section concerne l’ajout de composants qui sont directement routables des demandes des utilisateurs.*</span><span class="sxs-lookup"><span data-stu-id="cf11d-146">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="cf11d-147">Pour prendre en charge les composants Razor routables dans les applications MVC :</span><span class="sxs-lookup"><span data-stu-id="cf11d-147">To support routable Razor components in MVC apps:</span></span>

1. <span data-ttu-id="cf11d-148">Suivez les conseils de [l’application Préparer à utiliser des composants dans la](#prepare-the-app-to-use-components-in-pages-and-views) section pages et vues.</span><span class="sxs-lookup"><span data-stu-id="cf11d-148">Follow the guidance in the [Prepare the app to use components in pages and views](#prepare-the-app-to-use-components-in-pages-and-views) section.</span></span>

1. <span data-ttu-id="cf11d-149">Ajoutez un fichier *App.razor* à la racine du projet avec le contenu suivant :</span><span class="sxs-lookup"><span data-stu-id="cf11d-149">Add an *App.razor* file to the root of the project with the following content:</span></span>

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

1. <span data-ttu-id="cf11d-150">Ajoutez un fichier *_Host.cshtml* au dossier *Vues/Accueil* avec le contenu suivant :</span><span class="sxs-lookup"><span data-stu-id="cf11d-150">Add a *_Host.cshtml* file to the *Views/Home* folder with the following content:</span></span>

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="cf11d-151">Les composants utilisent le fichier *_Layout.cshtml* partagé pour leur mise en page.</span><span class="sxs-lookup"><span data-stu-id="cf11d-151">Components use the shared *_Layout.cshtml* file for their layout.</span></span>

1. <span data-ttu-id="cf11d-152">Ajoutez une action au contrôleur à domicile :</span><span class="sxs-lookup"><span data-stu-id="cf11d-152">Add an action to the Home controller:</span></span>

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. <span data-ttu-id="cf11d-153">Ajoutez un itinéraire peu prioritaire pour l’action du contrôleur qui renvoie la vue `Startup.Configure` *_Host.cshtml* à la configuration du point de terminaison dans :</span><span class="sxs-lookup"><span data-stu-id="cf11d-153">Add a low-priority route for the controller action that returns the *_Host.cshtml* view to the endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. <span data-ttu-id="cf11d-154">Créez un dossier *Pages* et ajoutez des composants routables à l’application.</span><span class="sxs-lookup"><span data-stu-id="cf11d-154">Create a *Pages* folder and add routable components to the app.</span></span> <span data-ttu-id="cf11d-155">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="cf11d-155">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   <span data-ttu-id="cf11d-156">Pour plus d’informations sur les espaces de nom, consultez la section [Espaces de noms de composant.](#component-namespaces)</span><span class="sxs-lookup"><span data-stu-id="cf11d-156">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="component-namespaces"></a><span data-ttu-id="cf11d-157">Espaces de noms de composant</span><span class="sxs-lookup"><span data-stu-id="cf11d-157">Component namespaces</span></span>

<span data-ttu-id="cf11d-158">Lorsque vous utilisez un dossier personnalisé pour contenir les composants de l’application, ajoutez l’espace de nom représentant le dossier à la page/vue ou au fichier *_ViewImports.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="cf11d-158">When using a custom folder to hold the app's components, add the namespace representing the folder to either the page/view or to the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="cf11d-159">Dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="cf11d-159">In the following example:</span></span>

* <span data-ttu-id="cf11d-160">Changez `MyAppNamespace` pour l’espace nom de l’application.</span><span class="sxs-lookup"><span data-stu-id="cf11d-160">Change `MyAppNamespace` to the app's namespace.</span></span>
* <span data-ttu-id="cf11d-161">Si un dossier nommé *Composants* n’est pas utilisé `Components` pour contenir les composants, changez-vous au dossier où résident les composants.</span><span class="sxs-lookup"><span data-stu-id="cf11d-161">If a folder named *Components* isn't used to hold the components, change `Components` to the folder where the components reside.</span></span>

```cshtml
@using MyAppNamespace.Components
```

<span data-ttu-id="cf11d-162">Le fichier *_ViewImports.cshtml* est situé dans le dossier *Pages* d’une application Razor Pages ou dans le dossier *Vues* d’une application MVC.</span><span class="sxs-lookup"><span data-stu-id="cf11d-162">The *_ViewImports.cshtml* file is located in the *Pages* folder of a Razor Pages app or the *Views* folder of an MVC app.</span></span>

<span data-ttu-id="cf11d-163">Pour plus d’informations, consultez <xref:blazor/components#import-components>.</span><span class="sxs-lookup"><span data-stu-id="cf11d-163">For more information, see <xref:blazor/components#import-components>.</span></span>

## <a name="render-components-from-a-page-or-view"></a><span data-ttu-id="cf11d-164">Rendre les composants à partir d’une page ou d’une vue</span><span class="sxs-lookup"><span data-stu-id="cf11d-164">Render components from a page or view</span></span>

<span data-ttu-id="cf11d-165">*Cette section concerne l’ajout de composants aux pages ou aux vues, où les composants ne sont pas directement routables des demandes des utilisateurs.*</span><span class="sxs-lookup"><span data-stu-id="cf11d-165">*This section pertains to adding components to pages or views, where the components aren't directly routable from user requests.*</span></span>

<span data-ttu-id="cf11d-166">Pour rendre un composant à partir d’une page ou d’une vue, utilisez [l’aide à l’étiquette de composant](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="cf11d-166">To render a component from a page or view, use the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

<span data-ttu-id="cf11d-167">Pour plus d’informations sur la façon dont `Component` les composants sont rendus, l’état des composants et l’aide Tag, voir les articles suivants:</span><span class="sxs-lookup"><span data-stu-id="cf11d-167">For more information on how components are rendered, component state, and the `Component` Tag Helper, see the following articles:</span></span>

* <xref:blazor/hosting-models>
* <xref:blazor/hosting-model-configuration>
* <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>
