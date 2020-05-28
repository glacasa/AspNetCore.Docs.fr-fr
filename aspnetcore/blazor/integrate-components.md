---
<span data-ttu-id="49a6d-101">title : 'intégrer Razor les composants ASP.net core dans Razor les pages et les applications MVC’auteur : Description : 'en savoir plus sur les scénarios de liaison de données pour les composants et les éléments DOM dans les Blazor applications. '</span><span class="sxs-lookup"><span data-stu-id="49a6d-101">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="49a6d-102">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="49a6d-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="49a6d-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="49a6d-103">'Blazor'</span></span>
- <span data-ttu-id="49a6d-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="49a6d-104">'Identity'</span></span>
- <span data-ttu-id="49a6d-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="49a6d-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="49a6d-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="49a6d-106">'Razor'</span></span>
- <span data-ttu-id="49a6d-107">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="49a6d-107">'SignalR' uid:</span></span> 

---
# <a name="integrate-aspnet-core-razor-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="49a6d-108">Intégrer Razor des composants de ASP.net core dans Razor des pages et des applications MVC</span><span class="sxs-lookup"><span data-stu-id="49a6d-108">Integrate ASP.NET Core Razor components into Razor Pages and MVC apps</span></span>

<span data-ttu-id="49a6d-109">Par [Luke Latham](https://github.com/guardrex) et [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="49a6d-109">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

Razor<span data-ttu-id="49a6d-110">les composants peuvent être intégrés dans Razor des pages et des applications MVC.</span><span class="sxs-lookup"><span data-stu-id="49a6d-110"> components can be integrated into Razor Pages and MVC apps.</span></span> <span data-ttu-id="49a6d-111">Lorsque la page ou la vue est restituée, les composants peuvent être prérendus en même temps.</span><span class="sxs-lookup"><span data-stu-id="49a6d-111">When the page or view is rendered, components can be prerendered at the same time.</span></span>

<span data-ttu-id="49a6d-112">Après avoir [préparé l’application](#prepare-the-app), suivez les instructions des sections suivantes en fonction des exigences de l’application :</span><span class="sxs-lookup"><span data-stu-id="49a6d-112">After [preparing the app](#prepare-the-app), use the guidance in the following sections depending on the app's requirements:</span></span>

* <span data-ttu-id="49a6d-113">Composants routables : pour les composants qui sont directement routables à partir des demandes de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="49a6d-113">Routable components: For components that are directly routable from user requests.</span></span> <span data-ttu-id="49a6d-114">Suivez ces instructions lorsque les visiteurs doivent être en mesure de faire une requête HTTP dans leur navigateur pour un composant avec une [`@page`](xref:mvc/views/razor#page) directive.</span><span class="sxs-lookup"><span data-stu-id="49a6d-114">Follow this guidance when visitors should be able to make an HTTP request in their browser for a component with an [`@page`](xref:mvc/views/razor#page) directive.</span></span>
  * <span data-ttu-id="49a6d-115">[Utiliser des composants routables dans une Razor application pages](#use-routable-components-in-a-razor-pages-app)</span><span class="sxs-lookup"><span data-stu-id="49a6d-115">[Use routable components in a Razor Pages app](#use-routable-components-in-a-razor-pages-app)</span></span>
  * [<span data-ttu-id="49a6d-116">Utiliser des composants routables dans une application MVC</span><span class="sxs-lookup"><span data-stu-id="49a6d-116">Use routable components in an MVC app</span></span>](#use-routable-components-in-an-mvc-app)
* <span data-ttu-id="49a6d-117">[Rendez les composants à partir d’une page ou d’une vue](#render-components-from-a-page-or-view): pour les composants qui ne sont pas directement routables à partir des demandes de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="49a6d-117">[Render components from a page or view](#render-components-from-a-page-or-view): For components that aren't directly routable from user requests.</span></span> <span data-ttu-id="49a6d-118">Suivez ces instructions lorsque l’application incorpore des composants dans des pages et des vues existantes avec le [tag Helper Component](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="49a6d-118">Follow this guidance when the app embeds components into existing pages and views with the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

## <a name="prepare-the-app"></a><span data-ttu-id="49a6d-119">Préparer l’application</span><span class="sxs-lookup"><span data-stu-id="49a6d-119">Prepare the app</span></span>

<span data-ttu-id="49a6d-120">Une Razor application de pages ou MVC existante peut intégrer Razor des composants dans des pages et des vues :</span><span class="sxs-lookup"><span data-stu-id="49a6d-120">An existing Razor Pages or MVC app can integrate Razor components into pages and views:</span></span>

1. <span data-ttu-id="49a6d-121">Dans le fichier de disposition de l’application (*_Layout. cshtml*) :</span><span class="sxs-lookup"><span data-stu-id="49a6d-121">In the app's layout file (*_Layout.cshtml*):</span></span>

   * <span data-ttu-id="49a6d-122">Ajoutez la `<base>` balise suivante à l' `<head>` élément :</span><span class="sxs-lookup"><span data-stu-id="49a6d-122">Add the following `<base>` tag to the `<head>` element:</span></span>

     ```html
     <base href="~/" />
     ```

     <span data-ttu-id="49a6d-123">La `href` valeur (le *chemin d’accès de base*de l’application) dans l’exemple précédent suppose que l’application se trouve dans le chemin d’URL racine ( `/` ).</span><span class="sxs-lookup"><span data-stu-id="49a6d-123">The `href` value (the *app base path*) in the preceding example assumes that the app resides at the root URL path (`/`).</span></span> <span data-ttu-id="49a6d-124">Si l’application est une sous-application, suivez les instructions de la section *chemin d’accès de base* de l’application de l' <xref:host-and-deploy/blazor/index#app-base-path> article.</span><span class="sxs-lookup"><span data-stu-id="49a6d-124">If the app is a sub-application, follow the guidance in the *App base path* section of the <xref:host-and-deploy/blazor/index#app-base-path> article.</span></span>

     <span data-ttu-id="49a6d-125">Le fichier *_Layout. cshtml* se trouve dans le dossier *pages/Shared* dans une Razor application pages ou un dossier *Views/Shared* dans une application MVC.</span><span class="sxs-lookup"><span data-stu-id="49a6d-125">The *_Layout.cshtml* file is located in the *Pages/Shared* folder in a Razor Pages app or *Views/Shared* folder in an MVC app.</span></span>

   * <span data-ttu-id="49a6d-126">Ajoutez une `<script>` balise pour le script *éblouissant. Server. js* immédiatement avant la `</body>` balise de fermeture :</span><span class="sxs-lookup"><span data-stu-id="49a6d-126">Add a `<script>` tag for the *blazor.server.js* script immediately before of the closing `</body>` tag:</span></span>

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     <span data-ttu-id="49a6d-127">L’infrastructure ajoute le script *éblouissant. Server. js* à l’application.</span><span class="sxs-lookup"><span data-stu-id="49a6d-127">The framework adds the *blazor.server.js* script to the app.</span></span> <span data-ttu-id="49a6d-128">Il n’est pas nécessaire d’ajouter manuellement le script à l’application.</span><span class="sxs-lookup"><span data-stu-id="49a6d-128">There's no need to manually add the script to the app.</span></span>

1. <span data-ttu-id="49a6d-129">Ajoutez un fichier *_Imports. Razor* au dossier racine du projet avec le contenu suivant (remplacez le dernier espace de noms, `MyAppNamespace` , par l’espace de noms de l’application) :</span><span class="sxs-lookup"><span data-stu-id="49a6d-129">Add an *_Imports.razor* file to the root folder of the project with the following content (change the last namespace, `MyAppNamespace`, to the namespace of the app):</span></span>

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

1. <span data-ttu-id="49a6d-130">Dans `Startup.ConfigureServices` , inscrivez le Blazor service serveur :</span><span class="sxs-lookup"><span data-stu-id="49a6d-130">In `Startup.ConfigureServices`, register the Blazor Server service:</span></span>

   ```csharp
   services.AddServerSideBlazor();
   ```

1. <span data-ttu-id="49a6d-131">Dans `Startup.Configure` , ajoutez le Blazor point de terminaison Hub à `app.UseEndpoints` :</span><span class="sxs-lookup"><span data-stu-id="49a6d-131">In `Startup.Configure`, add the Blazor Hub endpoint to `app.UseEndpoints`:</span></span>

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. <span data-ttu-id="49a6d-132">Intégrer des composants dans n’importe quelle page ou vue.</span><span class="sxs-lookup"><span data-stu-id="49a6d-132">Integrate components into any page or view.</span></span> <span data-ttu-id="49a6d-133">Pour plus d’informations, consultez la section [rendre les composants à partir d’une page ou d’une vue](#render-components-from-a-page-or-view) .</span><span class="sxs-lookup"><span data-stu-id="49a6d-133">For more information, see the [Render components from a page or view](#render-components-from-a-page-or-view) section.</span></span>

## <a name="use-routable-components-in-a-razor-pages-app"></a><span data-ttu-id="49a6d-134">Utiliser des composants routables dans une Razor application pages</span><span class="sxs-lookup"><span data-stu-id="49a6d-134">Use routable components in a Razor Pages app</span></span>

<span data-ttu-id="49a6d-135">*Cette section concerne l’ajout de composants qui sont directement routables à partir des demandes des utilisateurs.*</span><span class="sxs-lookup"><span data-stu-id="49a6d-135">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="49a6d-136">Pour prendre en charge les composants routables Razor dans les Razor applications pages :</span><span class="sxs-lookup"><span data-stu-id="49a6d-136">To support routable Razor components in Razor Pages apps:</span></span>

1. <span data-ttu-id="49a6d-137">Suivez les instructions de la section [préparer l’application](#prepare-the-app) .</span><span class="sxs-lookup"><span data-stu-id="49a6d-137">Follow the guidance in the [Prepare the app](#prepare-the-app) section.</span></span>

1. <span data-ttu-id="49a6d-138">Ajoutez un fichier *app. Razor* à la racine du projet avec le contenu suivant :</span><span class="sxs-lookup"><span data-stu-id="49a6d-138">Add an *App.razor* file to the project root with the following content:</span></span>

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

1. <span data-ttu-id="49a6d-139">Ajoutez un fichier *_Host. cshtml* au dossier *pages* avec le contenu suivant :</span><span class="sxs-lookup"><span data-stu-id="49a6d-139">Add a *_Host.cshtml* file to the *Pages* folder with the following content:</span></span>

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="49a6d-140">Les composants utilisent le fichier *_Layout. cshtml* partagé pour leur disposition.</span><span class="sxs-lookup"><span data-stu-id="49a6d-140">Components use the shared *_Layout.cshtml* file for their layout.</span></span>

   <span data-ttu-id="49a6d-141"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>Configure si le `App` composant :</span><span class="sxs-lookup"><span data-stu-id="49a6d-141"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="49a6d-142">Est prérendu dans la page.</span><span class="sxs-lookup"><span data-stu-id="49a6d-142">Is prerendered into the page.</span></span>
   * <span data-ttu-id="49a6d-143">Est rendu en HTML statique sur la page ou s’il contient les informations nécessaires pour démarrer une Blazor application à partir de l’agent utilisateur.</span><span class="sxs-lookup"><span data-stu-id="49a6d-143">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   | <span data-ttu-id="49a6d-144">Mode de rendu</span><span class="sxs-lookup"><span data-stu-id="49a6d-144">Render Mode</span></span> | <span data-ttu-id="49a6d-145">Description</span><span class="sxs-lookup"><span data-stu-id="49a6d-145">Description</span></span> |
   | ---
<span data-ttu-id="49a6d-146">title : 'intégrer Razor les composants ASP.net core dans Razor les pages et les applications MVC’auteur : Description : 'en savoir plus sur les scénarios de liaison de données pour les composants et les éléments DOM dans les Blazor applications. '</span><span class="sxs-lookup"><span data-stu-id="49a6d-146">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="49a6d-147">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="49a6d-147">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="49a6d-148">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="49a6d-148">'Blazor'</span></span>
- <span data-ttu-id="49a6d-149">'Identity'</span><span class="sxs-lookup"><span data-stu-id="49a6d-149">'Identity'</span></span>
- <span data-ttu-id="49a6d-150">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="49a6d-150">'Let's Encrypt'</span></span>
- <span data-ttu-id="49a6d-151">'Razor'</span><span class="sxs-lookup"><span data-stu-id="49a6d-151">'Razor'</span></span>
- <span data-ttu-id="49a6d-152">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="49a6d-152">'SignalR' uid:</span></span> 

-
<span data-ttu-id="49a6d-153">title : 'intégrer Razor les composants ASP.net core dans Razor les pages et les applications MVC’auteur : Description : 'en savoir plus sur les scénarios de liaison de données pour les composants et les éléments DOM dans les Blazor applications. '</span><span class="sxs-lookup"><span data-stu-id="49a6d-153">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="49a6d-154">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="49a6d-154">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="49a6d-155">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="49a6d-155">'Blazor'</span></span>
- <span data-ttu-id="49a6d-156">'Identity'</span><span class="sxs-lookup"><span data-stu-id="49a6d-156">'Identity'</span></span>
- <span data-ttu-id="49a6d-157">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="49a6d-157">'Let's Encrypt'</span></span>
- <span data-ttu-id="49a6d-158">'Razor'</span><span class="sxs-lookup"><span data-stu-id="49a6d-158">'Razor'</span></span>
- <span data-ttu-id="49a6d-159">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="49a6d-159">'SignalR' uid:</span></span> 

-
<span data-ttu-id="49a6d-160">title : 'intégrer Razor les composants ASP.net core dans Razor les pages et les applications MVC’auteur : Description : 'en savoir plus sur les scénarios de liaison de données pour les composants et les éléments DOM dans les Blazor applications. '</span><span class="sxs-lookup"><span data-stu-id="49a6d-160">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="49a6d-161">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="49a6d-161">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="49a6d-162">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="49a6d-162">'Blazor'</span></span>
- <span data-ttu-id="49a6d-163">'Identity'</span><span class="sxs-lookup"><span data-stu-id="49a6d-163">'Identity'</span></span>
- <span data-ttu-id="49a6d-164">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="49a6d-164">'Let's Encrypt'</span></span>
- <span data-ttu-id="49a6d-165">'Razor'</span><span class="sxs-lookup"><span data-stu-id="49a6d-165">'Razor'</span></span>
- <span data-ttu-id="49a6d-166">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="49a6d-166">'SignalR' uid:</span></span> 

<span data-ttu-id="49a6d-167">------ | ---titre : «intégrer Razor les composants ASP.net core dans Razor les pages et les applications MVC’auteur : Description : 'en savoir plus sur les scénarios de liaison de données pour les composants et les éléments DOM dans les Blazor applications. '</span><span class="sxs-lookup"><span data-stu-id="49a6d-167">------ | --- title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="49a6d-168">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="49a6d-168">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="49a6d-169">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="49a6d-169">'Blazor'</span></span>
- <span data-ttu-id="49a6d-170">'Identity'</span><span class="sxs-lookup"><span data-stu-id="49a6d-170">'Identity'</span></span>
- <span data-ttu-id="49a6d-171">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="49a6d-171">'Let's Encrypt'</span></span>
- <span data-ttu-id="49a6d-172">'Razor'</span><span class="sxs-lookup"><span data-stu-id="49a6d-172">'Razor'</span></span>
- <span data-ttu-id="49a6d-173">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="49a6d-173">'SignalR' uid:</span></span> 

-
<span data-ttu-id="49a6d-174">title : 'intégrer Razor les composants ASP.net core dans Razor les pages et les applications MVC’auteur : Description : 'en savoir plus sur les scénarios de liaison de données pour les composants et les éléments DOM dans les Blazor applications. '</span><span class="sxs-lookup"><span data-stu-id="49a6d-174">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="49a6d-175">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="49a6d-175">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="49a6d-176">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="49a6d-176">'Blazor'</span></span>
- <span data-ttu-id="49a6d-177">'Identity'</span><span class="sxs-lookup"><span data-stu-id="49a6d-177">'Identity'</span></span>
- <span data-ttu-id="49a6d-178">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="49a6d-178">'Let's Encrypt'</span></span>
- <span data-ttu-id="49a6d-179">'Razor'</span><span class="sxs-lookup"><span data-stu-id="49a6d-179">'Razor'</span></span>
- <span data-ttu-id="49a6d-180">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="49a6d-180">'SignalR' uid:</span></span> 

-
<span data-ttu-id="49a6d-181">title : 'intégrer Razor les composants ASP.net core dans Razor les pages et les applications MVC’auteur : Description : 'en savoir plus sur les scénarios de liaison de données pour les composants et les éléments DOM dans les Blazor applications. '</span><span class="sxs-lookup"><span data-stu-id="49a6d-181">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="49a6d-182">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="49a6d-182">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="49a6d-183">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="49a6d-183">'Blazor'</span></span>
- <span data-ttu-id="49a6d-184">'Identity'</span><span class="sxs-lookup"><span data-stu-id="49a6d-184">'Identity'</span></span>
- <span data-ttu-id="49a6d-185">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="49a6d-185">'Let's Encrypt'</span></span>
- <span data-ttu-id="49a6d-186">'Razor'</span><span class="sxs-lookup"><span data-stu-id="49a6d-186">'Razor'</span></span>
- <span data-ttu-id="49a6d-187">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="49a6d-187">'SignalR' uid:</span></span> 

<span data-ttu-id="49a6d-188">------ | | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Génère le rendu du `App` composant en HTML statique et comprend un marqueur pour une Blazor application serveur.</span><span class="sxs-lookup"><span data-stu-id="49a6d-188">------ | | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Renders the `App` component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="49a6d-189">Au démarrage de l’agent utilisateur, ce marqueur est utilisé pour démarrer une Blazor application.</span><span class="sxs-lookup"><span data-stu-id="49a6d-189">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="49a6d-190">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Restitue un marqueur pour une Blazor application serveur.</span><span class="sxs-lookup"><span data-stu-id="49a6d-190">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="49a6d-191">La sortie du `App` composant n’est pas incluse.</span><span class="sxs-lookup"><span data-stu-id="49a6d-191">Output from the `App` component isn't included.</span></span> <span data-ttu-id="49a6d-192">Au démarrage de l’agent utilisateur, ce marqueur est utilisé pour démarrer une Blazor application.</span><span class="sxs-lookup"><span data-stu-id="49a6d-192">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="49a6d-193">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Génère le rendu du `App` composant en HTML statique.</span><span class="sxs-lookup"><span data-stu-id="49a6d-193">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Renders the `App` component into static HTML.</span></span> |

   <span data-ttu-id="49a6d-194">Pour plus d’informations sur le tag Helper composant, consultez <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper> .</span><span class="sxs-lookup"><span data-stu-id="49a6d-194">For more information on the Component Tag Helper, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="49a6d-195">Ajoutez un itinéraire de priorité basse pour la page *_Host. cshtml* à la configuration de point de terminaison dans `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="49a6d-195">Add a low-priority route for the *_Host.cshtml* page to endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. <span data-ttu-id="49a6d-196">Ajoutez des composants routables à l’application.</span><span class="sxs-lookup"><span data-stu-id="49a6d-196">Add routable components to the app.</span></span> <span data-ttu-id="49a6d-197">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="49a6d-197">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="49a6d-198">Pour plus d’informations sur les espaces de noms, consultez la section [espaces de noms de composants](#component-namespaces) .</span><span class="sxs-lookup"><span data-stu-id="49a6d-198">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="use-routable-components-in-an-mvc-app"></a><span data-ttu-id="49a6d-199">Utiliser des composants routables dans une application MVC</span><span class="sxs-lookup"><span data-stu-id="49a6d-199">Use routable components in an MVC app</span></span>

<span data-ttu-id="49a6d-200">*Cette section concerne l’ajout de composants qui sont directement routables à partir des demandes des utilisateurs.*</span><span class="sxs-lookup"><span data-stu-id="49a6d-200">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="49a6d-201">Pour prendre en charge les composants routables Razor dans les applications MVC :</span><span class="sxs-lookup"><span data-stu-id="49a6d-201">To support routable Razor components in MVC apps:</span></span>

1. <span data-ttu-id="49a6d-202">Suivez les instructions de la section [préparer l’application](#prepare-the-app) .</span><span class="sxs-lookup"><span data-stu-id="49a6d-202">Follow the guidance in the [Prepare the app](#prepare-the-app) section.</span></span>

1. <span data-ttu-id="49a6d-203">Ajoutez un fichier *app. Razor* à la racine du projet avec le contenu suivant :</span><span class="sxs-lookup"><span data-stu-id="49a6d-203">Add an *App.razor* file to the root of the project with the following content:</span></span>

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

1. <span data-ttu-id="49a6d-204">Ajoutez un fichier *_Host. cshtml* au dossier *views/de démarrage* avec le contenu suivant :</span><span class="sxs-lookup"><span data-stu-id="49a6d-204">Add a *_Host.cshtml* file to the *Views/Home* folder with the following content:</span></span>

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="49a6d-205">Les composants utilisent le fichier *_Layout. cshtml* partagé pour leur disposition.</span><span class="sxs-lookup"><span data-stu-id="49a6d-205">Components use the shared *_Layout.cshtml* file for their layout.</span></span>
   
   <span data-ttu-id="49a6d-206"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>Configure si le `App` composant :</span><span class="sxs-lookup"><span data-stu-id="49a6d-206"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="49a6d-207">Est prérendu dans la page.</span><span class="sxs-lookup"><span data-stu-id="49a6d-207">Is prerendered into the page.</span></span>
   * <span data-ttu-id="49a6d-208">Est rendu en HTML statique sur la page ou s’il contient les informations nécessaires pour démarrer une Blazor application à partir de l’agent utilisateur.</span><span class="sxs-lookup"><span data-stu-id="49a6d-208">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   | <span data-ttu-id="49a6d-209">Mode de rendu</span><span class="sxs-lookup"><span data-stu-id="49a6d-209">Render Mode</span></span> | <span data-ttu-id="49a6d-210">Description</span><span class="sxs-lookup"><span data-stu-id="49a6d-210">Description</span></span> |
   | ---
<span data-ttu-id="49a6d-211">title : 'intégrer Razor les composants ASP.net core dans Razor les pages et les applications MVC’auteur : Description : 'en savoir plus sur les scénarios de liaison de données pour les composants et les éléments DOM dans les Blazor applications. '</span><span class="sxs-lookup"><span data-stu-id="49a6d-211">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="49a6d-212">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="49a6d-212">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="49a6d-213">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="49a6d-213">'Blazor'</span></span>
- <span data-ttu-id="49a6d-214">'Identity'</span><span class="sxs-lookup"><span data-stu-id="49a6d-214">'Identity'</span></span>
- <span data-ttu-id="49a6d-215">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="49a6d-215">'Let's Encrypt'</span></span>
- <span data-ttu-id="49a6d-216">'Razor'</span><span class="sxs-lookup"><span data-stu-id="49a6d-216">'Razor'</span></span>
- <span data-ttu-id="49a6d-217">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="49a6d-217">'SignalR' uid:</span></span> 

-
<span data-ttu-id="49a6d-218">title : 'intégrer Razor les composants ASP.net core dans Razor les pages et les applications MVC’auteur : Description : 'en savoir plus sur les scénarios de liaison de données pour les composants et les éléments DOM dans les Blazor applications. '</span><span class="sxs-lookup"><span data-stu-id="49a6d-218">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="49a6d-219">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="49a6d-219">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="49a6d-220">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="49a6d-220">'Blazor'</span></span>
- <span data-ttu-id="49a6d-221">'Identity'</span><span class="sxs-lookup"><span data-stu-id="49a6d-221">'Identity'</span></span>
- <span data-ttu-id="49a6d-222">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="49a6d-222">'Let's Encrypt'</span></span>
- <span data-ttu-id="49a6d-223">'Razor'</span><span class="sxs-lookup"><span data-stu-id="49a6d-223">'Razor'</span></span>
- <span data-ttu-id="49a6d-224">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="49a6d-224">'SignalR' uid:</span></span> 

-
<span data-ttu-id="49a6d-225">title : 'intégrer Razor les composants ASP.net core dans Razor les pages et les applications MVC’auteur : Description : 'en savoir plus sur les scénarios de liaison de données pour les composants et les éléments DOM dans les Blazor applications. '</span><span class="sxs-lookup"><span data-stu-id="49a6d-225">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="49a6d-226">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="49a6d-226">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="49a6d-227">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="49a6d-227">'Blazor'</span></span>
- <span data-ttu-id="49a6d-228">'Identity'</span><span class="sxs-lookup"><span data-stu-id="49a6d-228">'Identity'</span></span>
- <span data-ttu-id="49a6d-229">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="49a6d-229">'Let's Encrypt'</span></span>
- <span data-ttu-id="49a6d-230">'Razor'</span><span class="sxs-lookup"><span data-stu-id="49a6d-230">'Razor'</span></span>
- <span data-ttu-id="49a6d-231">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="49a6d-231">'SignalR' uid:</span></span> 

<span data-ttu-id="49a6d-232">------ | ---titre : «intégrer Razor les composants ASP.net core dans Razor les pages et les applications MVC’auteur : Description : 'en savoir plus sur les scénarios de liaison de données pour les composants et les éléments DOM dans les Blazor applications. '</span><span class="sxs-lookup"><span data-stu-id="49a6d-232">------ | --- title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="49a6d-233">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="49a6d-233">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="49a6d-234">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="49a6d-234">'Blazor'</span></span>
- <span data-ttu-id="49a6d-235">'Identity'</span><span class="sxs-lookup"><span data-stu-id="49a6d-235">'Identity'</span></span>
- <span data-ttu-id="49a6d-236">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="49a6d-236">'Let's Encrypt'</span></span>
- <span data-ttu-id="49a6d-237">'Razor'</span><span class="sxs-lookup"><span data-stu-id="49a6d-237">'Razor'</span></span>
- <span data-ttu-id="49a6d-238">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="49a6d-238">'SignalR' uid:</span></span> 

-
<span data-ttu-id="49a6d-239">title : 'intégrer Razor les composants ASP.net core dans Razor les pages et les applications MVC’auteur : Description : 'en savoir plus sur les scénarios de liaison de données pour les composants et les éléments DOM dans les Blazor applications. '</span><span class="sxs-lookup"><span data-stu-id="49a6d-239">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="49a6d-240">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="49a6d-240">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="49a6d-241">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="49a6d-241">'Blazor'</span></span>
- <span data-ttu-id="49a6d-242">'Identity'</span><span class="sxs-lookup"><span data-stu-id="49a6d-242">'Identity'</span></span>
- <span data-ttu-id="49a6d-243">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="49a6d-243">'Let's Encrypt'</span></span>
- <span data-ttu-id="49a6d-244">'Razor'</span><span class="sxs-lookup"><span data-stu-id="49a6d-244">'Razor'</span></span>
- <span data-ttu-id="49a6d-245">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="49a6d-245">'SignalR' uid:</span></span> 

-
<span data-ttu-id="49a6d-246">title : 'intégrer Razor les composants ASP.net core dans Razor les pages et les applications MVC’auteur : Description : 'en savoir plus sur les scénarios de liaison de données pour les composants et les éléments DOM dans les Blazor applications. '</span><span class="sxs-lookup"><span data-stu-id="49a6d-246">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="49a6d-247">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="49a6d-247">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="49a6d-248">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="49a6d-248">'Blazor'</span></span>
- <span data-ttu-id="49a6d-249">'Identity'</span><span class="sxs-lookup"><span data-stu-id="49a6d-249">'Identity'</span></span>
- <span data-ttu-id="49a6d-250">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="49a6d-250">'Let's Encrypt'</span></span>
- <span data-ttu-id="49a6d-251">'Razor'</span><span class="sxs-lookup"><span data-stu-id="49a6d-251">'Razor'</span></span>
- <span data-ttu-id="49a6d-252">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="49a6d-252">'SignalR' uid:</span></span> 

<span data-ttu-id="49a6d-253">------ | | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Génère le rendu du `App` composant en HTML statique et comprend un marqueur pour une Blazor application serveur.</span><span class="sxs-lookup"><span data-stu-id="49a6d-253">------ | | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Renders the `App` component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="49a6d-254">Au démarrage de l’agent utilisateur, ce marqueur est utilisé pour démarrer une Blazor application.</span><span class="sxs-lookup"><span data-stu-id="49a6d-254">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="49a6d-255">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Restitue un marqueur pour une Blazor application serveur.</span><span class="sxs-lookup"><span data-stu-id="49a6d-255">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="49a6d-256">La sortie du `App` composant n’est pas incluse.</span><span class="sxs-lookup"><span data-stu-id="49a6d-256">Output from the `App` component isn't included.</span></span> <span data-ttu-id="49a6d-257">Au démarrage de l’agent utilisateur, ce marqueur est utilisé pour démarrer une Blazor application.</span><span class="sxs-lookup"><span data-stu-id="49a6d-257">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="49a6d-258">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Génère le rendu du `App` composant en HTML statique.</span><span class="sxs-lookup"><span data-stu-id="49a6d-258">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Renders the `App` component into static HTML.</span></span> |

   <span data-ttu-id="49a6d-259">Pour plus d’informations sur le tag Helper composant, consultez <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper> .</span><span class="sxs-lookup"><span data-stu-id="49a6d-259">For more information on the Component Tag Helper, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="49a6d-260">Ajoutez une action au contrôleur d’hébergement :</span><span class="sxs-lookup"><span data-stu-id="49a6d-260">Add an action to the Home controller:</span></span>

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. <span data-ttu-id="49a6d-261">Ajoutez un itinéraire de faible priorité pour l’action de contrôleur qui retourne la vue *_Host. cshtml* à la configuration du point de terminaison dans `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="49a6d-261">Add a low-priority route for the controller action that returns the *_Host.cshtml* view to the endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. <span data-ttu-id="49a6d-262">Créez un dossier *pages* et ajoutez des composants routables à l’application.</span><span class="sxs-lookup"><span data-stu-id="49a6d-262">Create a *Pages* folder and add routable components to the app.</span></span> <span data-ttu-id="49a6d-263">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="49a6d-263">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="49a6d-264">Pour plus d’informations sur les espaces de noms, consultez la section [espaces de noms de composants](#component-namespaces) .</span><span class="sxs-lookup"><span data-stu-id="49a6d-264">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="render-components-from-a-page-or-view"></a><span data-ttu-id="49a6d-265">Rendre les composants à partir d’une page ou d’une vue</span><span class="sxs-lookup"><span data-stu-id="49a6d-265">Render components from a page or view</span></span>

<span data-ttu-id="49a6d-266">*Cette section se rapporte à l’ajout de composants à des pages ou à des vues, où les composants ne sont pas directement routés à partir des demandes de l’utilisateur.*</span><span class="sxs-lookup"><span data-stu-id="49a6d-266">*This section pertains to adding components to pages or views, where the components aren't directly routable from user requests.*</span></span>

<span data-ttu-id="49a6d-267">Pour afficher un composant à partir d’une page ou d’une vue, utilisez le [tag Helper Component](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="49a6d-267">To render a component from a page or view, use the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

### <a name="render-stateful-interactive-components"></a><span data-ttu-id="49a6d-268">Rendu des composants interactifs avec état</span><span class="sxs-lookup"><span data-stu-id="49a6d-268">Render stateful interactive components</span></span>

<span data-ttu-id="49a6d-269">Les composants interactifs avec état peuvent être ajoutés à une Razor page ou à une vue.</span><span class="sxs-lookup"><span data-stu-id="49a6d-269">Stateful interactive components can be added to a Razor page or view.</span></span>

<span data-ttu-id="49a6d-270">Lors du rendu de la page ou de la vue :</span><span class="sxs-lookup"><span data-stu-id="49a6d-270">When the page or view renders:</span></span>

* <span data-ttu-id="49a6d-271">Le composant est prérendu avec la page ou la vue.</span><span class="sxs-lookup"><span data-stu-id="49a6d-271">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="49a6d-272">L’état initial du composant utilisé pour le prérendu est perdu.</span><span class="sxs-lookup"><span data-stu-id="49a6d-272">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="49a6d-273">Un nouvel état de composant est créé lorsque la SignalR connexion est établie.</span><span class="sxs-lookup"><span data-stu-id="49a6d-273">New component state is created when the SignalR connection is established.</span></span>

<span data-ttu-id="49a6d-274">La Razor page suivante affiche un `Counter` composant :</span><span class="sxs-lookup"><span data-stu-id="49a6d-274">The following Razor page renders a `Counter` component:</span></span>

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

<span data-ttu-id="49a6d-275">Pour plus d'informations, consultez <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="49a6d-275">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

### <a name="render-noninteractive-components"></a><span data-ttu-id="49a6d-276">Rendre les composants non interactifs</span><span class="sxs-lookup"><span data-stu-id="49a6d-276">Render noninteractive components</span></span>

<span data-ttu-id="49a6d-277">Dans la Razor page suivante, le `Counter` composant est rendu statiquement avec une valeur initiale spécifiée à l’aide d’un formulaire.</span><span class="sxs-lookup"><span data-stu-id="49a6d-277">In the following Razor page, the `Counter` component is statically rendered with an initial value that's specified using a form.</span></span> <span data-ttu-id="49a6d-278">Étant donné que le composant est rendu statiquement, le composant n’est pas interactif :</span><span class="sxs-lookup"><span data-stu-id="49a6d-278">Since the component is statically rendered, the component isn't interactive:</span></span>

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

<span data-ttu-id="49a6d-279">Pour plus d'informations, consultez <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="49a6d-279">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

## <a name="component-namespaces"></a><span data-ttu-id="49a6d-280">Espaces de noms de composants</span><span class="sxs-lookup"><span data-stu-id="49a6d-280">Component namespaces</span></span>

<span data-ttu-id="49a6d-281">Lorsque vous utilisez un dossier personnalisé pour stocker les composants de l’application, ajoutez l’espace de noms qui représente le dossier à la page/la vue ou au fichier *_ViewImports. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="49a6d-281">When using a custom folder to hold the app's components, add the namespace representing the folder to either the page/view or to the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="49a6d-282">Dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="49a6d-282">In the following example:</span></span>

* <span data-ttu-id="49a6d-283">Accédez `MyAppNamespace` à l’espace de noms de l’application.</span><span class="sxs-lookup"><span data-stu-id="49a6d-283">Change `MyAppNamespace` to the app's namespace.</span></span>
* <span data-ttu-id="49a6d-284">Si un dossier nommé *Components* n’est pas utilisé pour contenir les composants, accédez `Components` au dossier dans lequel se trouvent les composants.</span><span class="sxs-lookup"><span data-stu-id="49a6d-284">If a folder named *Components* isn't used to hold the components, change `Components` to the folder where the components reside.</span></span>

```cshtml
@using MyAppNamespace.Components
```

<span data-ttu-id="49a6d-285">Le fichier *_ViewImports. cshtml* se trouve dans le dossier *pages* d’une Razor application pages ou du dossier *views* d’une application MVC.</span><span class="sxs-lookup"><span data-stu-id="49a6d-285">The *_ViewImports.cshtml* file is located in the *Pages* folder of a Razor Pages app or the *Views* folder of an MVC app.</span></span>

<span data-ttu-id="49a6d-286">Pour plus d'informations, consultez <xref:blazor/components#import-components>.</span><span class="sxs-lookup"><span data-stu-id="49a6d-286">For more information, see <xref:blazor/components#import-components>.</span></span>
