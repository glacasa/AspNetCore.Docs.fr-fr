---
<span data-ttu-id="807de-101">titre : Auteur : Description : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="807de-101">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="807de-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="807de-102">'Blazor'</span></span>
- <span data-ttu-id="807de-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="807de-103">'Identity'</span></span>
- <span data-ttu-id="807de-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="807de-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="807de-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="807de-105">'Razor'</span></span>
- <span data-ttu-id="807de-106">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="807de-106">'SignalR' uid:</span></span> 

---
# <a name="migrate-from-aspnet-web-api-to-aspnet-core"></a><span data-ttu-id="807de-107">Migrer de API Web ASP.NET vers ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="807de-107">Migrate from ASP.NET Web API to ASP.NET Core</span></span>

<span data-ttu-id="807de-108">Par [Scott Addie](https://twitter.com/scott_addie) et [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="807de-108">By [Scott Addie](https://twitter.com/scott_addie) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="807de-109">Une API Web ASP.NET 4. x est un service HTTP qui atteint un large éventail de clients, y compris des navigateurs et des appareils mobiles.</span><span class="sxs-lookup"><span data-stu-id="807de-109">An ASP.NET 4.x Web API is an HTTP service that reaches a broad range of clients, including browsers and mobile devices.</span></span> <span data-ttu-id="807de-110">ASP.NET Core associe des modèles d’application MVC et API Web ASP.NET 4. x dans un modèle de programmation unique appelé ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="807de-110">ASP.NET Core combines ASP.NET 4.x's MVC and Web API app models into a single programming model known as ASP.NET Core MVC.</span></span> <span data-ttu-id="807de-111">Cet article décrit les étapes nécessaires à la migration de l’API Web ASP.NET 4. x vers ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="807de-111">This article demonstrates the steps required to migrate from ASP.NET 4.x Web API to ASP.NET Core MVC.</span></span>

<span data-ttu-id="807de-112">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/migration/webapi/sample) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="807de-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/migration/webapi/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="807de-113">Prérequis</span><span class="sxs-lookup"><span data-stu-id="807de-113">Prerequisites</span></span>

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs-3.1.md)]

## <a name="review-aspnet-4x-web-api-project"></a><span data-ttu-id="807de-114">Examiner le projet d’API Web ASP.NET 4. x</span><span class="sxs-lookup"><span data-stu-id="807de-114">Review ASP.NET 4.x Web API project</span></span>

<span data-ttu-id="807de-115">Cet article utilise le projet *ProductsApp* créé dans [prise en main avec API Web ASP.NET 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).</span><span class="sxs-lookup"><span data-stu-id="807de-115">This article uses the *ProductsApp* project created in [Getting Started with ASP.NET Web API 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).</span></span> <span data-ttu-id="807de-116">Dans ce projet, un projet d’API Web ASP.NET 4. x de base est configuré comme suit.</span><span class="sxs-lookup"><span data-stu-id="807de-116">In that project, a basic ASP.NET 4.x Web API project is configured as follows.</span></span>

<span data-ttu-id="807de-117">Dans *global.asax.cs*, un appel est effectué à `WebApiConfig.Register` :</span><span class="sxs-lookup"><span data-stu-id="807de-117">In *Global.asax.cs*, a call is made to `WebApiConfig.Register`:</span></span>

[!code-csharp[](webapi/sample/3.x/ProductsApp/Global.asax.cs?highlight=14)]

<span data-ttu-id="807de-118">La `WebApiConfig` classe se trouve dans le dossier *App_Start* et a une `Register` méthode statique :</span><span class="sxs-lookup"><span data-stu-id="807de-118">The `WebApiConfig` class is found in the *App_Start* folder and has a static `Register` method:</span></span>

[!code-csharp[](webapi/sample/3.x/ProductsApp/App_Start/WebApiConfig.cs)]

<span data-ttu-id="807de-119">La classe précédente :</span><span class="sxs-lookup"><span data-stu-id="807de-119">The preceding class:</span></span>

* <span data-ttu-id="807de-120">Configure le [routage des attributs](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), bien qu’il ne soit pas réellement utilisé.</span><span class="sxs-lookup"><span data-stu-id="807de-120">Configures [attribute routing](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), although it's not actually being used.</span></span>
* <span data-ttu-id="807de-121">Configure la table de routage.</span><span class="sxs-lookup"><span data-stu-id="807de-121">Configures the routing table.</span></span>
<span data-ttu-id="807de-122">L’exemple de code s’attend à ce que les URL correspondent au format `/api/{controller}/{id}` , avec `{id}` facultatif.</span><span class="sxs-lookup"><span data-stu-id="807de-122">The sample code expects URLs to match the format `/api/{controller}/{id}`, with `{id}` being optional.</span></span>

<span data-ttu-id="807de-123">Les sections suivantes montrent comment migrer le projet d’API Web vers ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="807de-123">The following sections demonstrate migration of the Web API project to ASP.NET Core MVC.</span></span>

## <a name="create-the-destination-project"></a><span data-ttu-id="807de-124">Créer le projet de destination</span><span class="sxs-lookup"><span data-stu-id="807de-124">Create the destination project</span></span>

<span data-ttu-id="807de-125">Créez une nouvelle solution vide dans Visual Studio et ajoutez le projet d’API Web ASP.NET 4. x à migrer :</span><span class="sxs-lookup"><span data-stu-id="807de-125">Create a new blank solution in Visual Studio and add the ASP.NET 4.x Web API project to migrate:</span></span>

1. <span data-ttu-id="807de-126">Dans le menu **Fichier**, sélectionnez **Nouveau** > **Projet**.</span><span class="sxs-lookup"><span data-stu-id="807de-126">From the **File** menu, select **New** > **Project**.</span></span>
1. <span data-ttu-id="807de-127">Sélectionnez le modèle de **solution vide** , puis cliquez sur **suivant**.</span><span class="sxs-lookup"><span data-stu-id="807de-127">Select the **Blank Solution** template and select **Next**.</span></span>
1. <span data-ttu-id="807de-128">Nommez la solution *WebAPIMigration*.</span><span class="sxs-lookup"><span data-stu-id="807de-128">Name the solution *WebAPIMigration*.</span></span> <span data-ttu-id="807de-129">Sélectionnez **Créer**.</span><span class="sxs-lookup"><span data-stu-id="807de-129">Select **Create**.</span></span>
1. <span data-ttu-id="807de-130">Ajoutez le projet *ProductsApp* existant à la solution.</span><span class="sxs-lookup"><span data-stu-id="807de-130">Add the existing *ProductsApp* project to the solution.</span></span>

<span data-ttu-id="807de-131">Ajoutez un nouveau projet d’API à migrer vers :</span><span class="sxs-lookup"><span data-stu-id="807de-131">Add a new API project to migrate to:</span></span>

1. <span data-ttu-id="807de-132">Ajoutez un nouveau **ASP.net Core projet d’application Web** à la solution.</span><span class="sxs-lookup"><span data-stu-id="807de-132">Add a new **ASP.NET Core Web Application** project to the solution.</span></span>
1. <span data-ttu-id="807de-133">Dans la boîte de dialogue **configurer votre nouveau projet** , nommez le projet *ProductsCore*, puis sélectionnez **créer**.</span><span class="sxs-lookup"><span data-stu-id="807de-133">In the **Configure your new project** dialog, Name the project *ProductsCore*, and select **Create**.</span></span>
1. <span data-ttu-id="807de-134">Dans la boîte de dialogue **créer une application Web ASP.net Core** , vérifiez que **.net Core** et **ASP.net Core 3,1** sont sélectionnés.</span><span class="sxs-lookup"><span data-stu-id="807de-134">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="807de-135">Sélectionnez le modèle de projet **API**, puis sélectionnez **Créer**.</span><span class="sxs-lookup"><span data-stu-id="807de-135">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="807de-136">Supprimez les fichiers d’exemple *WeatherForecast.cs* et *Controllers/WeatherForecastController. cs* du nouveau projet *ProductsCore* .</span><span class="sxs-lookup"><span data-stu-id="807de-136">Remove the *WeatherForecast.cs* and *Controllers/WeatherForecastController.cs* example files from the new *ProductsCore* project.</span></span>

<span data-ttu-id="807de-137">La solution contient maintenant deux projets.</span><span class="sxs-lookup"><span data-stu-id="807de-137">The solution now contains two projects.</span></span> <span data-ttu-id="807de-138">Les sections suivantes expliquent comment migrer le contenu du projet *ProductsApp* vers le projet *ProductsCore* .</span><span class="sxs-lookup"><span data-stu-id="807de-138">The following sections explain migrating the *ProductsApp* project's contents to the *ProductsCore* project.</span></span>

## <a name="migrate-configuration"></a><span data-ttu-id="807de-139">Migrer une configuration</span><span class="sxs-lookup"><span data-stu-id="807de-139">Migrate configuration</span></span>

<span data-ttu-id="807de-140">ASP.NET Core n’utilise pas le dossier *App_Start* ou le fichier *global. asax* .</span><span class="sxs-lookup"><span data-stu-id="807de-140">ASP.NET Core doesn't use the *App_Start* folder or the *Global.asax* file.</span></span> <span data-ttu-id="807de-141">En outre, le fichier *Web. config* est ajouté au moment de la publication.</span><span class="sxs-lookup"><span data-stu-id="807de-141">Additionally, the *web.config* file is added at publish time.</span></span>

<span data-ttu-id="807de-142">Classe `Startup` :</span><span class="sxs-lookup"><span data-stu-id="807de-142">The `Startup` class:</span></span>

* <span data-ttu-id="807de-143">Remplace *global. asax*.</span><span class="sxs-lookup"><span data-stu-id="807de-143">Replaces *Global.asax*.</span></span>
* <span data-ttu-id="807de-144">Gère toutes les tâches de démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="807de-144">Handles all app startup tasks.</span></span>

<span data-ttu-id="807de-145">Pour plus d'informations, consultez <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="807de-145">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="migrate-models-and-controllers"></a><span data-ttu-id="807de-146">Migrer les modèles et les contrôleurs</span><span class="sxs-lookup"><span data-stu-id="807de-146">Migrate models and controllers</span></span>

<span data-ttu-id="807de-147">Le code suivant montre le `ProductsController` à mettre à jour pour ASP.net Core :</span><span class="sxs-lookup"><span data-stu-id="807de-147">The following code shows the `ProductsController` to be updated for ASP.NET Core:</span></span>

[!code-csharp[](webapi/sample/3.x/ProductsApp/Controllers/ProductsController.cs)]

<span data-ttu-id="807de-148">Mettez à jour le `ProductsController` pour ASP.net Core :</span><span class="sxs-lookup"><span data-stu-id="807de-148">Update the `ProductsController` for ASP.NET Core:</span></span>

1. <span data-ttu-id="807de-149">Copiez les *contrôleurs/ProductsController. cs* et le dossier *modèles* du projet d’origine vers le nouveau.</span><span class="sxs-lookup"><span data-stu-id="807de-149">Copy *Controllers/ProductsController.cs* and the *Models* folder from the original project to the new one.</span></span>
1. <span data-ttu-id="807de-150">Remplacez l’espace de noms racine des fichiers copiés par `ProductsCore` .</span><span class="sxs-lookup"><span data-stu-id="807de-150">Change the copied files' root namespace to `ProductsCore`.</span></span>
1. <span data-ttu-id="807de-151">Mettez à jour l' `using ProductsApp.Models;` instruction avec la valeur `using ProductsCore.Models;` .</span><span class="sxs-lookup"><span data-stu-id="807de-151">Update the `using ProductsApp.Models;` statement to `using ProductsCore.Models;`.</span></span>

<span data-ttu-id="807de-152">Les composants suivants n’existent pas dans ASP.NET Core :</span><span class="sxs-lookup"><span data-stu-id="807de-152">The following components don't exist in ASP.NET Core:</span></span>

* <span data-ttu-id="807de-153">Classe `ApiController`</span><span class="sxs-lookup"><span data-stu-id="807de-153">`ApiController` class</span></span>
* <span data-ttu-id="807de-154">Espace de noms `System.Web.Http`</span><span class="sxs-lookup"><span data-stu-id="807de-154">`System.Web.Http` namespace</span></span>
* <span data-ttu-id="807de-155">Interface `IHttpActionResult`</span><span class="sxs-lookup"><span data-stu-id="807de-155">`IHttpActionResult` interface</span></span>

<span data-ttu-id="807de-156">Effectuez les modifications suivantes :</span><span class="sxs-lookup"><span data-stu-id="807de-156">Make the following changes:</span></span>

1. <span data-ttu-id="807de-157">Remplacez `ApiController` par <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span><span class="sxs-lookup"><span data-stu-id="807de-157">Change `ApiController` to <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="807de-158">Ajoutez `using Microsoft.AspNetCore.Mvc;` pour résoudre la `ControllerBase` référence.</span><span class="sxs-lookup"><span data-stu-id="807de-158">Add `using Microsoft.AspNetCore.Mvc;` to resolve the `ControllerBase` reference.</span></span>
1. <span data-ttu-id="807de-159">Supprimez `using System.Web.Http;`.</span><span class="sxs-lookup"><span data-stu-id="807de-159">Delete `using System.Web.Http;`.</span></span>
1. <span data-ttu-id="807de-160">Remplacez le `GetProduct` type de retour de l’action par `IHttpActionResult` `ActionResult<Product>` .</span><span class="sxs-lookup"><span data-stu-id="807de-160">Change the `GetProduct` action's return type from `IHttpActionResult` to `ActionResult<Product>`.</span></span>
1. <span data-ttu-id="807de-161">Simplifiez l’instruction de l' `GetProduct` action comme `return` suit :</span><span class="sxs-lookup"><span data-stu-id="807de-161">Simplify the `GetProduct` action's `return` statement to the following:</span></span>

    ```csharp
    return product;
    ```

## <a name="configure-routing"></a><span data-ttu-id="807de-162">Configurer le routage</span><span class="sxs-lookup"><span data-stu-id="807de-162">Configure routing</span></span>

<span data-ttu-id="807de-163">Le modèle de projet d' *API* ASP.net core comprend la configuration du routage de point de terminaison dans le code généré.</span><span class="sxs-lookup"><span data-stu-id="807de-163">The ASP.NET Core *API* project template includes endpoint routing configuration in the generated code.</span></span>

<span data-ttu-id="807de-164">Les éléments suivants <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> et <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> appellent :</span><span class="sxs-lookup"><span data-stu-id="807de-164">The following <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> and <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> calls:</span></span>

* <span data-ttu-id="807de-165">Inscrire la correspondance d’itinéraire et l’exécution du point de terminaison dans le pipeline du [middleware](xref:fundamentals/middleware/index) .</span><span class="sxs-lookup"><span data-stu-id="807de-165">Register route matching and endpoint execution in the [middleware](xref:fundamentals/middleware/index) pipeline.</span></span>
* <span data-ttu-id="807de-166">Remplacez le fichier *App_Start/webapiconfig.cs* du projet *ProductsApp* .</span><span class="sxs-lookup"><span data-stu-id="807de-166">Replace the *ProductsApp* project's *App_Start/WebApiConfig.cs* file.</span></span>

[!code-csharp[](webapi/sample/3.x/ProductsCore/Startup.cs?name=snippet_Configure&highlight=10,14)]

<span data-ttu-id="807de-167">Configurez le routage comme suit :</span><span class="sxs-lookup"><span data-stu-id="807de-167">Configure routing as follows:</span></span>

1. <span data-ttu-id="807de-168">Marquez la `ProductsController` classe avec les attributs suivants :</span><span class="sxs-lookup"><span data-stu-id="807de-168">Mark the `ProductsController` class with the following attributes:</span></span>

    ```csharp
    [Route("api/[controller]")]
    [ApiController]
    ```

    <span data-ttu-id="807de-169">L' [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) attribut précédent configure le modèle de routage des attributs du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="807de-169">The preceding [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) attribute configures the controller's attribute routing pattern.</span></span> <span data-ttu-id="807de-170">L' [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribut rend l’acheminement des attributs obligatoire pour toutes les actions dans ce contrôleur.</span><span class="sxs-lookup"><span data-stu-id="807de-170">The [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute makes attribute routing a requirement for all actions in this controller.</span></span>

    <span data-ttu-id="807de-171">Le routage d’attributs prend en charge les jetons, tels que `[controller]` et `[action]` .</span><span class="sxs-lookup"><span data-stu-id="807de-171">Attribute routing supports tokens, such as `[controller]` and `[action]`.</span></span> <span data-ttu-id="807de-172">Lors de l’exécution, chaque jeton est remplacé par le nom du contrôleur ou de l’action, respectivement, auquel l’attribut a été appliqué.</span><span class="sxs-lookup"><span data-stu-id="807de-172">At runtime, each token is replaced with the name of the controller or action, respectively, to which the attribute has been applied.</span></span> <span data-ttu-id="807de-173">Les jetons :</span><span class="sxs-lookup"><span data-stu-id="807de-173">The tokens:</span></span>
    * <span data-ttu-id="807de-174">Réduisez le nombre de chaînes magiques dans le projet.</span><span class="sxs-lookup"><span data-stu-id="807de-174">Reduce the number of magic strings in the project.</span></span>
    * <span data-ttu-id="807de-175">Assurez-vous que les itinéraires restent synchronisés avec les contrôleurs et actions correspondants lorsque les refactorisations de renommage automatique sont appliquées.</span><span class="sxs-lookup"><span data-stu-id="807de-175">Ensure routes remain synchronized with the corresponding controllers and actions when automatic rename refactorings are applied.</span></span>
1. <span data-ttu-id="807de-176">Activez les demandes HTTP d’extraction aux `ProductController` actions :</span><span class="sxs-lookup"><span data-stu-id="807de-176">Enable HTTP Get requests to the `ProductController` actions:</span></span>
    * <span data-ttu-id="807de-177">Appliquez l' [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribut à l' `GetAllProducts` action.</span><span class="sxs-lookup"><span data-stu-id="807de-177">Apply the [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute to the `GetAllProducts` action.</span></span>
    * <span data-ttu-id="807de-178">Appliquez l' `[HttpGet("{id}")]` attribut à l' `GetProduct` action.</span><span class="sxs-lookup"><span data-stu-id="807de-178">Apply the `[HttpGet("{id}")]` attribute to the `GetProduct` action.</span></span>

<span data-ttu-id="807de-179">Exécutez le projet migré et accédez à `/api/products` .</span><span class="sxs-lookup"><span data-stu-id="807de-179">Run the migrated project, and browse to `/api/products`.</span></span> <span data-ttu-id="807de-180">La liste complète des trois produits s’affiche.</span><span class="sxs-lookup"><span data-stu-id="807de-180">A full list of three products appears.</span></span> <span data-ttu-id="807de-181">Accédez à `/api/products/1`.</span><span class="sxs-lookup"><span data-stu-id="807de-181">Browse to `/api/products/1`.</span></span> <span data-ttu-id="807de-182">Le premier produit s’affiche.</span><span class="sxs-lookup"><span data-stu-id="807de-182">The first product appears.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="807de-183">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="807de-183">Additional resources</span></span>

* <xref:web-api/index>
* <xref:web-api/action-return-types>
* <xref:mvc/compatibility-version>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"
## <a name="prerequisites"></a><span data-ttu-id="807de-184">Prérequis</span><span class="sxs-lookup"><span data-stu-id="807de-184">Prerequisites</span></span>

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs2019-2.2.md)]

## <a name="review-aspnet-4x-web-api-project"></a><span data-ttu-id="807de-185">Examiner le projet d’API Web ASP.NET 4. x</span><span class="sxs-lookup"><span data-stu-id="807de-185">Review ASP.NET 4.x Web API project</span></span>

<span data-ttu-id="807de-186">Cet article utilise le projet *ProductsApp* créé dans [prise en main avec API Web ASP.NET 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).</span><span class="sxs-lookup"><span data-stu-id="807de-186">This article uses the *ProductsApp* project created in [Getting Started with ASP.NET Web API 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).</span></span> <span data-ttu-id="807de-187">Dans ce projet, un projet d’API Web ASP.NET 4. x de base est configuré comme suit.</span><span class="sxs-lookup"><span data-stu-id="807de-187">In that project, a basic ASP.NET 4.x Web API project is configured as follows.</span></span>

<span data-ttu-id="807de-188">Dans *global.asax.cs*, un appel est effectué à `WebApiConfig.Register` :</span><span class="sxs-lookup"><span data-stu-id="807de-188">In *Global.asax.cs*, a call is made to `WebApiConfig.Register`:</span></span>

[!code-csharp[](webapi/sample/2.x/ProductsApp/Global.asax.cs?highlight=14)]

<span data-ttu-id="807de-189">La `WebApiConfig` classe se trouve dans le dossier *App_Start* et a une `Register` méthode statique :</span><span class="sxs-lookup"><span data-stu-id="807de-189">The `WebApiConfig` class is found in the *App_Start* folder and has a static `Register` method:</span></span>

[!code-csharp[](webapi/sample/2.x/ProductsApp/App_Start/WebApiConfig.cs)]

<span data-ttu-id="807de-190">Cette classe configure le [routage des attributs](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), bien qu’elle ne soit pas réellement utilisée dans le projet.</span><span class="sxs-lookup"><span data-stu-id="807de-190">This class configures [attribute routing](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), although it's not actually being used in the project.</span></span> <span data-ttu-id="807de-191">Elle configure également la table de routage, qui est utilisée par API Web ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="807de-191">It also configures the routing table, which is used by ASP.NET Web API.</span></span> <span data-ttu-id="807de-192">Dans ce cas, l’API Web ASP.NET 4. x s’attend à ce que les URL correspondent au format `/api/{controller}/{id}` , avec `{id}` facultatif.</span><span class="sxs-lookup"><span data-stu-id="807de-192">In this case, ASP.NET 4.x Web API expects URLs to match the format `/api/{controller}/{id}`, with `{id}` being optional.</span></span>

<span data-ttu-id="807de-193">Les sections suivantes montrent comment migrer le projet d’API Web vers ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="807de-193">The following sections demonstrate migration of the Web API project to ASP.NET Core MVC.</span></span>

## <a name="create-the-destination-project"></a><span data-ttu-id="807de-194">Créer le projet de destination</span><span class="sxs-lookup"><span data-stu-id="807de-194">Create the destination project</span></span>

<span data-ttu-id="807de-195">Effectuez les étapes suivantes dans Visual Studio :</span><span class="sxs-lookup"><span data-stu-id="807de-195">Complete the following steps in Visual Studio:</span></span>

* <span data-ttu-id="807de-196">Accédez à **fichier**  >  **nouveau**  >  **projet**  >  **autres types de projets**  >  **Visual Studio solutions**.</span><span class="sxs-lookup"><span data-stu-id="807de-196">Go to **File** > **New** > **Project** > **Other Project Types** > **Visual Studio Solutions**.</span></span> <span data-ttu-id="807de-197">Sélectionnez **solution vide**et nommez la solution *WebAPIMigration*.</span><span class="sxs-lookup"><span data-stu-id="807de-197">Select **Blank Solution**, and name the solution *WebAPIMigration*.</span></span> <span data-ttu-id="807de-198">Cliquez sur le bouton **OK** .</span><span class="sxs-lookup"><span data-stu-id="807de-198">Click the **OK** button.</span></span>
* <span data-ttu-id="807de-199">Ajoutez le projet *ProductsApp* existant à la solution.</span><span class="sxs-lookup"><span data-stu-id="807de-199">Add the existing *ProductsApp* project to the solution.</span></span>
* <span data-ttu-id="807de-200">Ajoutez un nouveau **ASP.net Core projet d’application Web** à la solution.</span><span class="sxs-lookup"><span data-stu-id="807de-200">Add a new **ASP.NET Core Web Application** project to the solution.</span></span> <span data-ttu-id="807de-201">Sélectionnez le Framework cible **.net Core** dans la liste déroulante, puis sélectionnez le modèle de projet **API** .</span><span class="sxs-lookup"><span data-stu-id="807de-201">Select the **.NET Core** target framework from the drop-down, and select the **API** project template.</span></span> <span data-ttu-id="807de-202">Nommez le projet *ProductsCore*, puis cliquez sur le bouton **OK** .</span><span class="sxs-lookup"><span data-stu-id="807de-202">Name the project *ProductsCore*, and click the **OK** button.</span></span>

<span data-ttu-id="807de-203">La solution contient maintenant deux projets.</span><span class="sxs-lookup"><span data-stu-id="807de-203">The solution now contains two projects.</span></span> <span data-ttu-id="807de-204">Les sections suivantes expliquent comment migrer le contenu du projet *ProductsApp* vers le projet *ProductsCore* .</span><span class="sxs-lookup"><span data-stu-id="807de-204">The following sections explain migrating the *ProductsApp* project's contents to the *ProductsCore* project.</span></span>

## <a name="migrate-configuration"></a><span data-ttu-id="807de-205">Migrer une configuration</span><span class="sxs-lookup"><span data-stu-id="807de-205">Migrate configuration</span></span>

<span data-ttu-id="807de-206">ASP.NET Core n’utilise pas :</span><span class="sxs-lookup"><span data-stu-id="807de-206">ASP.NET Core doesn't use:</span></span>

* <span data-ttu-id="807de-207">*App_Start* dossier ou le fichier *global. asax*</span><span class="sxs-lookup"><span data-stu-id="807de-207">*App_Start* folder or the *Global.asax* file</span></span>
* <span data-ttu-id="807de-208">le fichier *Web. config* est ajouté au moment de la publication.</span><span class="sxs-lookup"><span data-stu-id="807de-208">*web.config* file is added at publish time.</span></span>

<span data-ttu-id="807de-209">Classe `Startup` :</span><span class="sxs-lookup"><span data-stu-id="807de-209">The `Startup` class:</span></span>

* <span data-ttu-id="807de-210">Remplace *global. asax*.</span><span class="sxs-lookup"><span data-stu-id="807de-210">Replaces *Global.asax*.</span></span>
* <span data-ttu-id="807de-211">Gère toutes les tâches de démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="807de-211">Handles all app startup tasks.</span></span>

<span data-ttu-id="807de-212">Pour plus d'informations, consultez <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="807de-212">For more information, see <xref:fundamentals/startup>.</span></span>

<span data-ttu-id="807de-213">Dans ASP.NET Core MVC, le routage des attributs est inclus par défaut quand <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> est appelé dans `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="807de-213">In ASP.NET Core MVC, attribute routing is included by default when <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> is called in `Startup.Configure`.</span></span> <span data-ttu-id="807de-214">L' `UseMvc` appel suivant remplace le fichier *App_Start/webapiconfig.cs* du projet *ProductsApp* :</span><span class="sxs-lookup"><span data-stu-id="807de-214">The following `UseMvc` call replaces the *ProductsApp* project's *App_Start/WebApiConfig.cs* file:</span></span>

[!code-csharp[](webapi/sample/2.x/ProductsCore/Startup.cs?name=snippet_Configure&highlight=13)]

## <a name="migrate-models-and-controllers"></a><span data-ttu-id="807de-215">Migrer les modèles et les contrôleurs</span><span class="sxs-lookup"><span data-stu-id="807de-215">Migrate models and controllers</span></span>

<span data-ttu-id="807de-216">Le code suivant illustre la `ProductsController` mise à jour de ASP.net Core :[!code-csharp[](webapi/sample/2.x/ProductsApp/Controllers/ProductsController.cs)]</span><span class="sxs-lookup"><span data-stu-id="807de-216">The following code shows the `ProductsController` update for ASP.NET Core: [!code-csharp[](webapi/sample/2.x/ProductsApp/Controllers/ProductsController.cs)]</span></span>

<span data-ttu-id="807de-217">Mettez à jour le `ProductsController` pour ASP.net Core :</span><span class="sxs-lookup"><span data-stu-id="807de-217">Update the `ProductsController` for ASP.NET Core:</span></span>

1. <span data-ttu-id="807de-218">Copiez les *contrôleurs/ProductsController. cs* du projet d’origine vers le nouveau.</span><span class="sxs-lookup"><span data-stu-id="807de-218">Copy *Controllers/ProductsController.cs* from the original project to the new one.</span></span>
1. <span data-ttu-id="807de-219">Copiez le dossier *Models* du projet d’origine vers le nouveau.</span><span class="sxs-lookup"><span data-stu-id="807de-219">Copy the *Models* folder from the original project to the new one.</span></span>
1. <span data-ttu-id="807de-220">Remplacez l’espace de noms racine des fichiers copiés par `ProductsCore` .</span><span class="sxs-lookup"><span data-stu-id="807de-220">Change the copied files' root namespace to `ProductsCore`.</span></span>
1. <span data-ttu-id="807de-221">Mettez à jour l' `using ProductsApp.Models;` instruction avec la valeur `using ProductsCore.Models;` .</span><span class="sxs-lookup"><span data-stu-id="807de-221">Update the `using ProductsApp.Models;` statement to `using ProductsCore.Models;`.</span></span>

<span data-ttu-id="807de-222">Les composants suivants n’existent pas dans ASP.NET Core :</span><span class="sxs-lookup"><span data-stu-id="807de-222">The following components don't exist in ASP.NET Core:</span></span>

* <span data-ttu-id="807de-223">Classe `ApiController`</span><span class="sxs-lookup"><span data-stu-id="807de-223">`ApiController` class</span></span>
* <span data-ttu-id="807de-224">Espace de noms `System.Web.Http`</span><span class="sxs-lookup"><span data-stu-id="807de-224">`System.Web.Http` namespace</span></span>
* <span data-ttu-id="807de-225">Interface `IHttpActionResult`</span><span class="sxs-lookup"><span data-stu-id="807de-225">`IHttpActionResult` interface</span></span>

<span data-ttu-id="807de-226">Effectuez les modifications suivantes :</span><span class="sxs-lookup"><span data-stu-id="807de-226">Make the following changes:</span></span>

1. <span data-ttu-id="807de-227">Remplacez `ApiController` par <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span><span class="sxs-lookup"><span data-stu-id="807de-227">Change `ApiController` to <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="807de-228">Ajoutez `using Microsoft.AspNetCore.Mvc;` pour résoudre la `ControllerBase` référence.</span><span class="sxs-lookup"><span data-stu-id="807de-228">Add `using Microsoft.AspNetCore.Mvc;` to resolve the `ControllerBase` reference.</span></span>
1. <span data-ttu-id="807de-229">Supprimez `using System.Web.Http;`.</span><span class="sxs-lookup"><span data-stu-id="807de-229">Delete `using System.Web.Http;`.</span></span>
1. <span data-ttu-id="807de-230">Remplacez le `GetProduct` type de retour de l’action par `IHttpActionResult` `ActionResult<Product>` .</span><span class="sxs-lookup"><span data-stu-id="807de-230">Change the `GetProduct` action's return type from `IHttpActionResult` to `ActionResult<Product>`.</span></span>
1. <span data-ttu-id="807de-231">Simplifiez l’instruction de l' `GetProduct` action comme `return` suit :</span><span class="sxs-lookup"><span data-stu-id="807de-231">Simplify the `GetProduct` action's `return` statement to the following:</span></span>

    ```csharp
    return product;
    ```

## <a name="configure-routing"></a><span data-ttu-id="807de-232">Configurer le routage</span><span class="sxs-lookup"><span data-stu-id="807de-232">Configure routing</span></span>

<span data-ttu-id="807de-233">Configurez le routage comme suit :</span><span class="sxs-lookup"><span data-stu-id="807de-233">Configure routing as follows:</span></span>

1. <span data-ttu-id="807de-234">Marquez la `ProductsController` classe avec les attributs suivants :</span><span class="sxs-lookup"><span data-stu-id="807de-234">Mark the `ProductsController` class with the following attributes:</span></span>

    ```csharp
    [Route("api/[controller]")]
    [ApiController]
    ```

    <span data-ttu-id="807de-235">L' [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) attribut précédent configure le modèle de routage des attributs du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="807de-235">The preceding [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) attribute configures the controller's attribute routing pattern.</span></span> <span data-ttu-id="807de-236">L' [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribut rend l’acheminement des attributs obligatoire pour toutes les actions dans ce contrôleur.</span><span class="sxs-lookup"><span data-stu-id="807de-236">The [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute makes attribute routing a requirement for all actions in this controller.</span></span>

    <span data-ttu-id="807de-237">Le routage d’attributs prend en charge les jetons, tels que `[controller]` et `[action]` .</span><span class="sxs-lookup"><span data-stu-id="807de-237">Attribute routing supports tokens, such as `[controller]` and `[action]`.</span></span> <span data-ttu-id="807de-238">Lors de l’exécution, chaque jeton est remplacé par le nom du contrôleur ou de l’action, respectivement, auquel l’attribut a été appliqué.</span><span class="sxs-lookup"><span data-stu-id="807de-238">At runtime, each token is replaced with the name of the controller or action, respectively, to which the attribute has been applied.</span></span> <span data-ttu-id="807de-239">Les jetons réduisent le nombre de chaînes magiques dans le projet.</span><span class="sxs-lookup"><span data-stu-id="807de-239">The tokens reduce the number of magic strings in the project.</span></span> <span data-ttu-id="807de-240">Les jetons garantissent également que les itinéraires restent synchronisés avec les contrôleurs et actions correspondants lorsque les refactorisations de renommage automatique sont appliquées.</span><span class="sxs-lookup"><span data-stu-id="807de-240">The tokens also ensure routes remain synchronized with the corresponding controllers and actions when automatic rename refactorings are applied.</span></span>
1. <span data-ttu-id="807de-241">Définissez le mode de compatibilité du projet sur ASP.NET Core 2,2 :</span><span class="sxs-lookup"><span data-stu-id="807de-241">Set the project's compatibility mode to ASP.NET Core 2.2:</span></span>

    [!code-csharp[](webapi/sample/2.x/ProductsCore/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

    <span data-ttu-id="807de-242">Modification précédente :</span><span class="sxs-lookup"><span data-stu-id="807de-242">The preceding change:</span></span>

    * <span data-ttu-id="807de-243">Est requis pour utiliser l' `[ApiController]` attribut au niveau du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="807de-243">Is required to use the `[ApiController]` attribute at the controller level.</span></span>
    * <span data-ttu-id="807de-244">Opte pour l’interruption potentielle des comportements introduits dans ASP.NET Core 2,2.</span><span class="sxs-lookup"><span data-stu-id="807de-244">Opts in to potentially breaking behaviors introduced in ASP.NET Core 2.2.</span></span>
1. <span data-ttu-id="807de-245">Activez les demandes HTTP d’extraction aux `ProductController` actions :</span><span class="sxs-lookup"><span data-stu-id="807de-245">Enable HTTP Get requests to the `ProductController` actions:</span></span>
    * <span data-ttu-id="807de-246">Appliquez l' [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribut à l' `GetAllProducts` action.</span><span class="sxs-lookup"><span data-stu-id="807de-246">Apply the [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute to the `GetAllProducts` action.</span></span>
    * <span data-ttu-id="807de-247">Appliquez l' `[HttpGet("{id}")]` attribut à l' `GetProduct` action.</span><span class="sxs-lookup"><span data-stu-id="807de-247">Apply the `[HttpGet("{id}")]` attribute to the `GetProduct` action.</span></span>

<span data-ttu-id="807de-248">Exécutez le projet migré et accédez à `/api/products` .</span><span class="sxs-lookup"><span data-stu-id="807de-248">Run the migrated project, and browse to `/api/products`.</span></span> <span data-ttu-id="807de-249">La liste complète des trois produits s’affiche.</span><span class="sxs-lookup"><span data-stu-id="807de-249">A full list of three products appears.</span></span> <span data-ttu-id="807de-250">Accédez à `/api/products/1`.</span><span class="sxs-lookup"><span data-stu-id="807de-250">Browse to `/api/products/1`.</span></span> <span data-ttu-id="807de-251">Le premier produit s’affiche.</span><span class="sxs-lookup"><span data-stu-id="807de-251">The first product appears.</span></span>

## <a name="compatibility-shim"></a><span data-ttu-id="807de-252">Shim de compatibilité</span><span class="sxs-lookup"><span data-stu-id="807de-252">Compatibility shim</span></span>

<span data-ttu-id="807de-253">La bibliothèque [Microsoft. AspNetCore. Mvc. WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) fournit un shim de compatibilité pour déplacer des projets d’API Web ASP.net 4. x vers ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="807de-253">The [Microsoft.AspNetCore.Mvc.WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) library provides a compatibility shim to move ASP.NET 4.x Web API projects to ASP.NET Core.</span></span> <span data-ttu-id="807de-254">Le shim de compatibilité étend ASP.NET Core pour prendre en charge un certain nombre de conventions de l’API Web 2 de ASP.NET 4. x.</span><span class="sxs-lookup"><span data-stu-id="807de-254">The compatibility shim extends ASP.NET Core to support a number of conventions from ASP.NET 4.x Web API 2.</span></span> <span data-ttu-id="807de-255">L’exemple porté précédemment dans ce document est suffisamment basique pour que le shim de compatibilité soit inutile.</span><span class="sxs-lookup"><span data-stu-id="807de-255">The sample ported previously in this document is basic enough that the compatibility shim was unnecessary.</span></span> <span data-ttu-id="807de-256">Pour les projets de grande taille, l’utilisation du shim de compatibilité peut être utile pour combler temporairement le fossé entre les API ASP.NET Core et ASP.NET 4. x Web API 2.</span><span class="sxs-lookup"><span data-stu-id="807de-256">For larger projects, using the compatibility shim can be useful for temporarily bridging the API gap between ASP.NET Core and ASP.NET 4.x Web API 2.</span></span>

<span data-ttu-id="807de-257">Le shim de compatibilité de l’API Web est destiné à être utilisé comme mesure temporaire pour prendre en charge la migration de grands projets d’API Web ASP.NET 4. x vers ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="807de-257">The Web API compatibility shim is meant to be used as a temporary measure to support migrating large ASP.NET 4.x Web API projects to ASP.NET Core.</span></span> <span data-ttu-id="807de-258">Au fil du temps, les projets doivent être mis à jour pour utiliser des modèles de ASP.NET Core au lieu de s’appuyer sur le shim de compatibilité.</span><span class="sxs-lookup"><span data-stu-id="807de-258">Over time, projects should be updated to use ASP.NET Core patterns instead of relying on the compatibility shim.</span></span>

<span data-ttu-id="807de-259">Les fonctionnalités de compatibilité incluses dans `Microsoft.AspNetCore.Mvc.WebApiCompatShim` sont les suivantes :</span><span class="sxs-lookup"><span data-stu-id="807de-259">Compatibility features included in `Microsoft.AspNetCore.Mvc.WebApiCompatShim` include:</span></span>

* <span data-ttu-id="807de-260">Ajoute un `ApiController` type afin que les types de base des contrôleurs n’aient pas besoin d’être mis à jour.</span><span class="sxs-lookup"><span data-stu-id="807de-260">Adds an `ApiController` type so that controllers' base types don't need to be updated.</span></span>
* <span data-ttu-id="807de-261">Active la liaison de modèle de style API Web.</span><span class="sxs-lookup"><span data-stu-id="807de-261">Enables Web API-style model binding.</span></span> <span data-ttu-id="807de-262">ASP.NET Core liaison de modèle MVC fonctionne de façon similaire à celle de ASP.NET 4. x MVC 5, par défaut.</span><span class="sxs-lookup"><span data-stu-id="807de-262">ASP.NET Core MVC model binding functions similarly to that of ASP.NET 4.x MVC 5, by default.</span></span> <span data-ttu-id="807de-263">Le shim de compatibilité modifie la liaison de modèle pour qu’elle soit plus semblable aux conventions de liaison de modèle d’API Web 2 ASP.NET 4. x.</span><span class="sxs-lookup"><span data-stu-id="807de-263">The compatibility shim changes model binding to be more similar to ASP.NET 4.x Web API 2 model binding conventions.</span></span> <span data-ttu-id="807de-264">Par exemple, les types complexes sont automatiquement liés à partir du corps de la demande.</span><span class="sxs-lookup"><span data-stu-id="807de-264">For example, complex types are automatically bound from the request body.</span></span>
* <span data-ttu-id="807de-265">Étend la liaison de modèle afin que les actions du contrôleur puissent prendre des paramètres de type `HttpRequestMessage` .</span><span class="sxs-lookup"><span data-stu-id="807de-265">Extends model binding so that controller actions can take parameters of type `HttpRequestMessage`.</span></span>
* <span data-ttu-id="807de-266">Ajoute des formateurs de message permettant aux actions de retourner des résultats de type `HttpResponseMessage` .</span><span class="sxs-lookup"><span data-stu-id="807de-266">Adds message formatters allowing actions to return results of type `HttpResponseMessage`.</span></span>
* <span data-ttu-id="807de-267">Ajoute des méthodes de réponse supplémentaires que les actions de l’API Web 2 peuvent avoir utilisées pour répondre aux réponses :</span><span class="sxs-lookup"><span data-stu-id="807de-267">Adds additional response methods that Web API 2 actions may have used to serve responses:</span></span>
  * <span data-ttu-id="807de-268">`HttpResponseMessage`générateurs</span><span class="sxs-lookup"><span data-stu-id="807de-268">`HttpResponseMessage` generators:</span></span>
    * `CreateResponse<T>`
    * `CreateErrorResponse`
  * <span data-ttu-id="807de-269">Méthodes de résultat d’action :</span><span class="sxs-lookup"><span data-stu-id="807de-269">Action result methods:</span></span>
    * `BadRequestErrorMessageResult`
    * `ExceptionResult`
    * `InternalServerErrorResult`
    * `InvalidModelStateResult`
    * `NegotiatedContentResult`
    * `ResponseMessageResult`
* <span data-ttu-id="807de-270">Ajoute une instance de `IContentNegotiator` au conteneur d’injection de dépendances de l’application et rend disponibles les types liés à la négociation de contenu à partir de [Microsoft. Aspnet. WebApi. client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/).</span><span class="sxs-lookup"><span data-stu-id="807de-270">Adds an instance of `IContentNegotiator` to the app's dependency injection (DI) container and makes available the content negotiation-related types from [Microsoft.AspNet.WebApi.Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/).</span></span> <span data-ttu-id="807de-271">Voici quelques exemples de ces types : `DefaultContentNegotiator` et `MediaTypeFormatter` .</span><span class="sxs-lookup"><span data-stu-id="807de-271">Examples of such types include `DefaultContentNegotiator` and `MediaTypeFormatter`.</span></span>

<span data-ttu-id="807de-272">Pour utiliser le shim de compatibilité :</span><span class="sxs-lookup"><span data-stu-id="807de-272">To use the compatibility shim:</span></span>

1. <span data-ttu-id="807de-273">Installez le package NuGet [Microsoft. AspNetCore. Mvc. WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) .</span><span class="sxs-lookup"><span data-stu-id="807de-273">Install the [Microsoft.AspNetCore.Mvc.WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) NuGet package.</span></span>
1. <span data-ttu-id="807de-274">Enregistrez les services du shim de compatibilité avec le conteneur DI de l’application en appelant `services.AddMvc().AddWebApiConventions()` dans `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="807de-274">Register the compatibility shim's services with the app's DI container by calling `services.AddMvc().AddWebApiConventions()` in `Startup.ConfigureServices`.</span></span>
1. <span data-ttu-id="807de-275">Définissez des itinéraires spécifiques à l’API Web à l’aide `MapWebApiRoute` de sur le `IRouteBuilder` dans l’appel de l’application `IApplicationBuilder.UseMvc` .</span><span class="sxs-lookup"><span data-stu-id="807de-275">Define web API-specific routes using `MapWebApiRoute` on the `IRouteBuilder` in the app's `IApplicationBuilder.UseMvc` call.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="807de-276">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="807de-276">Additional resources</span></span>

* <xref:web-api/index>
* <xref:web-api/action-return-types>
* <xref:mvc/compatibility-version>
::: moniker-end
