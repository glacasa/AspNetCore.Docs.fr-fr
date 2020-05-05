---
title: Migrer de API Web ASP.NET vers ASP.NET Core
author: ardalis
description: Découvrez comment migrer une implémentation d’API Web à partir de l’API Web ASP.NET 4. x vers ASP.NET Core MVC.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/webapi
ms.openlocfilehash: dda457daa0cb8a59ccd4c326a601e375fe4a81bb
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82766587"
---
# <a name="migrate-from-aspnet-web-api-to-aspnet-core"></a><span data-ttu-id="1f2a4-103">Migrer de API Web ASP.NET vers ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1f2a4-103">Migrate from ASP.NET Web API to ASP.NET Core</span></span>

<span data-ttu-id="1f2a4-104">Par [Scott Addie](https://twitter.com/scott_addie) et [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="1f2a4-104">By [Scott Addie](https://twitter.com/scott_addie) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="1f2a4-105">Une API Web ASP.NET 4. x est un service HTTP qui atteint un large éventail de clients, y compris des navigateurs et des appareils mobiles.</span><span class="sxs-lookup"><span data-stu-id="1f2a4-105">An ASP.NET 4.x Web API is an HTTP service that reaches a broad range of clients, including browsers and mobile devices.</span></span> <span data-ttu-id="1f2a4-106">ASP.NET Core unifie les modèles d’application MVC et API Web de ASP.NET 4. x en un modèle de programmation plus simple, connu sous le nom de ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="1f2a4-106">ASP.NET Core unifies ASP.NET 4.x's MVC and Web API app models into a simpler programming model known as ASP.NET Core MVC.</span></span> <span data-ttu-id="1f2a4-107">Cet article décrit les étapes nécessaires à la migration de l’API Web ASP.NET 4. x vers ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="1f2a4-107">This article demonstrates the steps required to migrate from ASP.NET 4.x Web API to ASP.NET Core MVC.</span></span>

<span data-ttu-id="1f2a4-108">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/migration/webapi/sample) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1f2a4-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/migration/webapi/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="1f2a4-109">Prérequis</span><span class="sxs-lookup"><span data-stu-id="1f2a4-109">Prerequisites</span></span>

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs2019-2.2.md)]

## <a name="review-aspnet-4x-web-api-project"></a><span data-ttu-id="1f2a4-110">Examiner le projet d’API Web ASP.NET 4. x</span><span class="sxs-lookup"><span data-stu-id="1f2a4-110">Review ASP.NET 4.x Web API project</span></span>

<span data-ttu-id="1f2a4-111">En guise de point de départ, cet article utilise le projet *ProductsApp* créé dans [prise en main avec API Web ASP.NET 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).</span><span class="sxs-lookup"><span data-stu-id="1f2a4-111">As a starting point, this article uses the *ProductsApp* project created in [Getting Started with ASP.NET Web API 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).</span></span> <span data-ttu-id="1f2a4-112">Dans ce projet, un simple projet d’API Web ASP.NET 4. x est configuré comme suit.</span><span class="sxs-lookup"><span data-stu-id="1f2a4-112">In that project, a simple ASP.NET 4.x Web API project is configured as follows.</span></span>

<span data-ttu-id="1f2a4-113">Dans *global.asax.cs*, un appel est effectué à `WebApiConfig.Register`:</span><span class="sxs-lookup"><span data-stu-id="1f2a4-113">In *Global.asax.cs*, a call is made to `WebApiConfig.Register`:</span></span>

[!code-csharp[](webapi/sample/ProductsApp/Global.asax.cs?highlight=14)]

<span data-ttu-id="1f2a4-114">La `WebApiConfig` classe se trouve dans le dossier *App_Start* et a une méthode `Register` statique :</span><span class="sxs-lookup"><span data-stu-id="1f2a4-114">The `WebApiConfig` class is found in the *App_Start* folder and has a static `Register` method:</span></span>

[!code-csharp[](webapi/sample/ProductsApp/App_Start/WebApiConfig.cs)]

<span data-ttu-id="1f2a4-115">Cette classe configure le [routage des attributs](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), bien qu’elle ne soit pas réellement utilisée dans le projet.</span><span class="sxs-lookup"><span data-stu-id="1f2a4-115">This class configures [attribute routing](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), although it's not actually being used in the project.</span></span> <span data-ttu-id="1f2a4-116">Elle configure également la table de routage, qui est utilisée par API Web ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="1f2a4-116">It also configures the routing table, which is used by ASP.NET Web API.</span></span> <span data-ttu-id="1f2a4-117">Dans ce cas, l’API Web ASP.NET 4. x s’attend à ce que les `/api/{controller}/{id}`URL correspondent `{id}` au format, avec facultatif.</span><span class="sxs-lookup"><span data-stu-id="1f2a4-117">In this case, ASP.NET 4.x Web API expects URLs to match the format `/api/{controller}/{id}`, with `{id}` being optional.</span></span>

<span data-ttu-id="1f2a4-118">Le projet *ProductsApp* comprend un contrôleur.</span><span class="sxs-lookup"><span data-stu-id="1f2a4-118">The *ProductsApp* project includes one controller.</span></span> <span data-ttu-id="1f2a4-119">Le contrôleur hérite de `ApiController` et contient deux actions :</span><span class="sxs-lookup"><span data-stu-id="1f2a4-119">The controller inherits from `ApiController` and contains two actions:</span></span>

[!code-csharp[](webapi/sample/ProductsApp/Controllers/ProductsController.cs?highlight=28,33)]

<span data-ttu-id="1f2a4-120">Le `Product` modèle utilisé par `ProductsController` est une classe simple :</span><span class="sxs-lookup"><span data-stu-id="1f2a4-120">The `Product` model used by `ProductsController` is a simple class:</span></span>

[!code-csharp[](webapi/sample/ProductsApp/Models/Product.cs)]

<span data-ttu-id="1f2a4-121">Les sections suivantes montrent comment migrer le projet d’API Web vers ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="1f2a4-121">The following sections demonstrate migration of the Web API project to ASP.NET Core MVC.</span></span>

## <a name="create-destination-project"></a><span data-ttu-id="1f2a4-122">Créer un projet de destination</span><span class="sxs-lookup"><span data-stu-id="1f2a4-122">Create destination project</span></span>

<span data-ttu-id="1f2a4-123">Effectuez les étapes suivantes dans Visual Studio :</span><span class="sxs-lookup"><span data-stu-id="1f2a4-123">Complete the following steps in Visual Studio:</span></span>

* <span data-ttu-id="1f2a4-124">Accédez à **fichier** > **nouveau** > **projet** > **autres types** > de projets**Visual Studio solutions**.</span><span class="sxs-lookup"><span data-stu-id="1f2a4-124">Go to **File** > **New** > **Project** > **Other Project Types** > **Visual Studio Solutions**.</span></span> <span data-ttu-id="1f2a4-125">Sélectionnez **solution vide**et nommez la solution *WebAPIMigration*.</span><span class="sxs-lookup"><span data-stu-id="1f2a4-125">Select **Blank Solution**, and name the solution *WebAPIMigration*.</span></span> <span data-ttu-id="1f2a4-126">Cliquez sur le bouton **OK** .</span><span class="sxs-lookup"><span data-stu-id="1f2a4-126">Click the **OK** button.</span></span>
* <span data-ttu-id="1f2a4-127">Ajoutez le projet *ProductsApp* existant à la solution.</span><span class="sxs-lookup"><span data-stu-id="1f2a4-127">Add the existing *ProductsApp* project to the solution.</span></span>
* <span data-ttu-id="1f2a4-128">Ajoutez un nouveau **ASP.net Core projet d’application Web** à la solution.</span><span class="sxs-lookup"><span data-stu-id="1f2a4-128">Add a new **ASP.NET Core Web Application** project to the solution.</span></span> <span data-ttu-id="1f2a4-129">Sélectionnez le Framework cible **.net Core** dans la liste déroulante, puis sélectionnez le modèle de projet **API** .</span><span class="sxs-lookup"><span data-stu-id="1f2a4-129">Select the **.NET Core** target framework from the drop-down, and select the **API** project template.</span></span> <span data-ttu-id="1f2a4-130">Nommez le projet *ProductsCore*, puis cliquez sur le bouton **OK** .</span><span class="sxs-lookup"><span data-stu-id="1f2a4-130">Name the project *ProductsCore*, and click the **OK** button.</span></span>

<span data-ttu-id="1f2a4-131">La solution contient maintenant deux projets.</span><span class="sxs-lookup"><span data-stu-id="1f2a4-131">The solution now contains two projects.</span></span> <span data-ttu-id="1f2a4-132">Les sections suivantes expliquent comment migrer le contenu du projet *ProductsApp* vers le projet *ProductsCore* .</span><span class="sxs-lookup"><span data-stu-id="1f2a4-132">The following sections explain migrating the *ProductsApp* project's contents to the *ProductsCore* project.</span></span>

## <a name="migrate-configuration"></a><span data-ttu-id="1f2a4-133">Migrer une configuration</span><span class="sxs-lookup"><span data-stu-id="1f2a4-133">Migrate configuration</span></span>

<span data-ttu-id="1f2a4-134">ASP.NET Core n’utilise pas le dossier *App_Start* ou le fichier *global. asax* , et le fichier *Web. config* est ajouté au moment de la publication.</span><span class="sxs-lookup"><span data-stu-id="1f2a4-134">ASP.NET Core doesn't use the *App_Start* folder or the *Global.asax* file, and the *web.config* file is added at publish time.</span></span> <span data-ttu-id="1f2a4-135">*Startup.cs* est le remplacement de *global. asax* et se trouve à la racine du projet.</span><span class="sxs-lookup"><span data-stu-id="1f2a4-135">*Startup.cs* is the replacement for *Global.asax* and is located in the project root.</span></span> <span data-ttu-id="1f2a4-136">La `Startup` classe gère toutes les tâches de démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="1f2a4-136">The `Startup` class handles all app startup tasks.</span></span> <span data-ttu-id="1f2a4-137">Pour plus d’informations, consultez <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="1f2a4-137">For more information, see <xref:fundamentals/startup>.</span></span>

<span data-ttu-id="1f2a4-138">Dans ASP.NET Core MVC, le routage des attributs est inclus par <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> défaut quand est `Startup.Configure`appelé dans.</span><span class="sxs-lookup"><span data-stu-id="1f2a4-138">In ASP.NET Core MVC, attribute routing is included by default when <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> is called in `Startup.Configure`.</span></span> <span data-ttu-id="1f2a4-139">L’appel `UseMvc` suivant remplace le fichier *App_Start/Webapiconfig.cs* du projet *ProductsApp* :</span><span class="sxs-lookup"><span data-stu-id="1f2a4-139">The following `UseMvc` call replaces the *ProductsApp* project's *App_Start/WebApiConfig.cs* file:</span></span>

[!code-csharp[](webapi/sample/ProductsCore/Startup.cs?name=snippet_Configure&highlight=13])]

## <a name="migrate-models-and-controllers"></a><span data-ttu-id="1f2a4-140">Migrer les modèles et les contrôleurs</span><span class="sxs-lookup"><span data-stu-id="1f2a4-140">Migrate models and controllers</span></span>

<span data-ttu-id="1f2a4-141">Copiez sur le contrôleur du projet *ProductApp* et le modèle qu’il utilise.</span><span class="sxs-lookup"><span data-stu-id="1f2a4-141">Copy over the *ProductApp* project's controller and the model it uses.</span></span> <span data-ttu-id="1f2a4-142">Procédez comme suit :</span><span class="sxs-lookup"><span data-stu-id="1f2a4-142">Follow these steps:</span></span>

1. <span data-ttu-id="1f2a4-143">Copiez les *contrôleurs/ProductsController. cs* du projet d’origine vers le nouveau.</span><span class="sxs-lookup"><span data-stu-id="1f2a4-143">Copy *Controllers/ProductsController.cs* from the original project to the new one.</span></span>
1. <span data-ttu-id="1f2a4-144">Copiez le dossier *Models* entier du projet d’origine vers le nouveau.</span><span class="sxs-lookup"><span data-stu-id="1f2a4-144">Copy the entire *Models* folder from the original project to the new one.</span></span>
1. <span data-ttu-id="1f2a4-145">Modifiez les espaces de noms des fichiers copiés pour qu’ils correspondent au nouveau nom du projet (*ProductsCore*).</span><span class="sxs-lookup"><span data-stu-id="1f2a4-145">Change the copied files' namespaces to match the new project name (*ProductsCore*).</span></span> <span data-ttu-id="1f2a4-146">Ajustez `using ProductsApp.Models;` également l’instruction dans *ProductsController.cs* .</span><span class="sxs-lookup"><span data-stu-id="1f2a4-146">Adjust the `using ProductsApp.Models;` statement in *ProductsController.cs* too.</span></span>

<span data-ttu-id="1f2a4-147">À ce stade, la génération de l’application génère un certain nombre d’erreurs de compilation.</span><span class="sxs-lookup"><span data-stu-id="1f2a4-147">At this point, building the app results in a number of compilation errors.</span></span> <span data-ttu-id="1f2a4-148">Les erreurs se produisent parce que les composants suivants n’existent pas dans ASP.NET Core :</span><span class="sxs-lookup"><span data-stu-id="1f2a4-148">The errors occur because the following components don't exist in ASP.NET Core:</span></span>

* <span data-ttu-id="1f2a4-149">Classe `ApiController`</span><span class="sxs-lookup"><span data-stu-id="1f2a4-149">`ApiController` class</span></span>
* <span data-ttu-id="1f2a4-150">Espace de noms `System.Web.Http`</span><span class="sxs-lookup"><span data-stu-id="1f2a4-150">`System.Web.Http` namespace</span></span>
* <span data-ttu-id="1f2a4-151">Interface `IHttpActionResult`</span><span class="sxs-lookup"><span data-stu-id="1f2a4-151">`IHttpActionResult` interface</span></span>

<span data-ttu-id="1f2a4-152">Corrigez les erreurs comme suit :</span><span class="sxs-lookup"><span data-stu-id="1f2a4-152">Fix the errors as follows:</span></span>

1. <span data-ttu-id="1f2a4-153">Remplacez `ApiController` par <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span><span class="sxs-lookup"><span data-stu-id="1f2a4-153">Change `ApiController` to <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="1f2a4-154">Ajoutez `using Microsoft.AspNetCore.Mvc;` pour résoudre la `ControllerBase` référence.</span><span class="sxs-lookup"><span data-stu-id="1f2a4-154">Add `using Microsoft.AspNetCore.Mvc;` to resolve the `ControllerBase` reference.</span></span>
1. <span data-ttu-id="1f2a4-155">Supprimez `using System.Web.Http;`.</span><span class="sxs-lookup"><span data-stu-id="1f2a4-155">Delete `using System.Web.Http;`.</span></span>
1. <span data-ttu-id="1f2a4-156">Remplacez le `GetProduct` type de retour de l' `IHttpActionResult` action `ActionResult<Product>`par.</span><span class="sxs-lookup"><span data-stu-id="1f2a4-156">Change the `GetProduct` action's return type from `IHttpActionResult` to `ActionResult<Product>`.</span></span>

<span data-ttu-id="1f2a4-157">Simplifiez `GetProduct` l' `return` instruction de l’action comme suit :</span><span class="sxs-lookup"><span data-stu-id="1f2a4-157">Simplify the `GetProduct` action's `return` statement to the following:</span></span>

```csharp
return product;
```

## <a name="configure-routing"></a><span data-ttu-id="1f2a4-158">Configurer le routage</span><span class="sxs-lookup"><span data-stu-id="1f2a4-158">Configure routing</span></span>

<span data-ttu-id="1f2a4-159">Configurez le routage comme suit :</span><span class="sxs-lookup"><span data-stu-id="1f2a4-159">Configure routing as follows:</span></span>

1. <span data-ttu-id="1f2a4-160">Marquez `ProductsController` la classe avec les attributs suivants :</span><span class="sxs-lookup"><span data-stu-id="1f2a4-160">Mark the `ProductsController` class with the following attributes:</span></span>

    ```csharp
    [Route("api/[controller]")]
    [ApiController]
    ```

    <span data-ttu-id="1f2a4-161">L’attribut [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) précédent configure le modèle de routage des attributs du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="1f2a4-161">The preceding [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) attribute configures the controller's attribute routing pattern.</span></span> <span data-ttu-id="1f2a4-162">L' [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribut rend l’acheminement des attributs obligatoire pour toutes les actions dans ce contrôleur.</span><span class="sxs-lookup"><span data-stu-id="1f2a4-162">The [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute makes attribute routing a requirement for all actions in this controller.</span></span>

    <span data-ttu-id="1f2a4-163">Le routage d’attributs prend en charge les `[controller]` jetons `[action]`, tels que et.</span><span class="sxs-lookup"><span data-stu-id="1f2a4-163">Attribute routing supports tokens, such as `[controller]` and `[action]`.</span></span> <span data-ttu-id="1f2a4-164">Lors de l’exécution, chaque jeton est remplacé par le nom du contrôleur ou de l’action, respectivement, auquel l’attribut a été appliqué.</span><span class="sxs-lookup"><span data-stu-id="1f2a4-164">At runtime, each token is replaced with the name of the controller or action, respectively, to which the attribute has been applied.</span></span> <span data-ttu-id="1f2a4-165">Les jetons réduisent le nombre de chaînes magiques dans le projet.</span><span class="sxs-lookup"><span data-stu-id="1f2a4-165">The tokens reduce the number of magic strings in the project.</span></span> <span data-ttu-id="1f2a4-166">Les jetons garantissent également que les itinéraires restent synchronisés avec les contrôleurs et actions correspondants lorsque les refactorisations de renommage automatique sont appliquées.</span><span class="sxs-lookup"><span data-stu-id="1f2a4-166">The tokens also ensure routes remain synchronized with the corresponding controllers and actions when automatic rename refactorings are applied.</span></span>
1. <span data-ttu-id="1f2a4-167">Définissez le mode de compatibilité du projet sur ASP.NET Core 2,2 :</span><span class="sxs-lookup"><span data-stu-id="1f2a4-167">Set the project's compatibility mode to ASP.NET Core 2.2:</span></span>

    [!code-csharp[](webapi/sample/ProductsCore/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

    <span data-ttu-id="1f2a4-168">Modification précédente :</span><span class="sxs-lookup"><span data-stu-id="1f2a4-168">The preceding change:</span></span>

    * <span data-ttu-id="1f2a4-169">Est requis pour utiliser l' `[ApiController]` attribut au niveau du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="1f2a4-169">Is required to use the `[ApiController]` attribute at the controller level.</span></span>
    * <span data-ttu-id="1f2a4-170">Opte pour l’interruption potentielle des comportements introduits dans ASP.NET Core 2,2.</span><span class="sxs-lookup"><span data-stu-id="1f2a4-170">Opts in to potentially breaking behaviors introduced in ASP.NET Core 2.2.</span></span>
1. <span data-ttu-id="1f2a4-171">Activez les `ProductController` demandes HTTP d’extraction aux actions :</span><span class="sxs-lookup"><span data-stu-id="1f2a4-171">Enable HTTP Get requests to the `ProductController` actions:</span></span>
    * <span data-ttu-id="1f2a4-172">Appliquez l' [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribut à l' `GetAllProducts` action.</span><span class="sxs-lookup"><span data-stu-id="1f2a4-172">Apply the [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute to the `GetAllProducts` action.</span></span>
    * <span data-ttu-id="1f2a4-173">Appliquez l' `[HttpGet("{id}")]` attribut à l' `GetProduct` action.</span><span class="sxs-lookup"><span data-stu-id="1f2a4-173">Apply the `[HttpGet("{id}")]` attribute to the `GetProduct` action.</span></span>

<span data-ttu-id="1f2a4-174">Après les modifications précédentes et la suppression des instructions inutilisées `using` , le fichier *ProductsController.cs* ressemble à ceci :</span><span class="sxs-lookup"><span data-stu-id="1f2a4-174">After the preceding changes and the removal of unused `using` statements, *ProductsController.cs* file looks like this:</span></span>

[!code-csharp[](webapi/sample/ProductsCore/Controllers/ProductsController.cs)]

<span data-ttu-id="1f2a4-175">Exécutez le projet migré et accédez à `/api/products`.</span><span class="sxs-lookup"><span data-stu-id="1f2a4-175">Run the migrated project, and browse to `/api/products`.</span></span> <span data-ttu-id="1f2a4-176">La liste complète des trois produits s’affiche.</span><span class="sxs-lookup"><span data-stu-id="1f2a4-176">A full list of three products appears.</span></span> <span data-ttu-id="1f2a4-177">Accédez à `/api/products/1`.</span><span class="sxs-lookup"><span data-stu-id="1f2a4-177">Browse to `/api/products/1`.</span></span> <span data-ttu-id="1f2a4-178">Le premier produit s’affiche.</span><span class="sxs-lookup"><span data-stu-id="1f2a4-178">The first product appears.</span></span>

## <a name="compatibility-shim"></a><span data-ttu-id="1f2a4-179">Shim de compatibilité</span><span class="sxs-lookup"><span data-stu-id="1f2a4-179">Compatibility shim</span></span>

<span data-ttu-id="1f2a4-180">La bibliothèque [Microsoft. AspNetCore. Mvc. WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) fournit un shim de compatibilité pour déplacer des projets d’API Web ASP.net 4. x vers ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="1f2a4-180">The [Microsoft.AspNetCore.Mvc.WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) library provides a compatibility shim to move ASP.NET 4.x Web API projects to ASP.NET Core.</span></span> <span data-ttu-id="1f2a4-181">Le shim de compatibilité étend ASP.NET Core pour prendre en charge un certain nombre de conventions de l’API Web 2 de ASP.NET 4. x.</span><span class="sxs-lookup"><span data-stu-id="1f2a4-181">The compatibility shim extends ASP.NET Core to support a number of conventions from ASP.NET 4.x Web API 2.</span></span> <span data-ttu-id="1f2a4-182">L’exemple porté précédemment dans ce document est suffisamment basique pour que le shim de compatibilité soit inutile.</span><span class="sxs-lookup"><span data-stu-id="1f2a4-182">The sample ported previously in this document is basic enough that the compatibility shim was unnecessary.</span></span> <span data-ttu-id="1f2a4-183">Pour les projets de grande taille, l’utilisation du shim de compatibilité peut être utile pour combler temporairement le fossé entre les API ASP.NET Core et ASP.NET 4. x Web API 2.</span><span class="sxs-lookup"><span data-stu-id="1f2a4-183">For larger projects, using the compatibility shim can be useful for temporarily bridging the API gap between ASP.NET Core and ASP.NET 4.x Web API 2.</span></span>

<span data-ttu-id="1f2a4-184">Le shim de compatibilité de l’API Web est destiné à être utilisé comme mesure temporaire pour prendre en charge la migration de grands projets d’API Web ASP.NET 4. x vers ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1f2a4-184">The Web API compatibility shim is meant to be used as a temporary measure to support migrating large ASP.NET 4.x Web API projects to ASP.NET Core.</span></span> <span data-ttu-id="1f2a4-185">Au fil du temps, les projets doivent être mis à jour pour utiliser des modèles de ASP.NET Core au lieu de s’appuyer sur le shim de compatibilité.</span><span class="sxs-lookup"><span data-stu-id="1f2a4-185">Over time, projects should be updated to use ASP.NET Core patterns instead of relying on the compatibility shim.</span></span>

<span data-ttu-id="1f2a4-186">Les fonctionnalités de compatibilité `Microsoft.AspNetCore.Mvc.WebApiCompatShim` incluses dans sont les suivantes :</span><span class="sxs-lookup"><span data-stu-id="1f2a4-186">Compatibility features included in `Microsoft.AspNetCore.Mvc.WebApiCompatShim` include:</span></span>

* <span data-ttu-id="1f2a4-187">Ajoute un `ApiController` type afin que les types de base des contrôleurs n’aient pas besoin d’être mis à jour.</span><span class="sxs-lookup"><span data-stu-id="1f2a4-187">Adds an `ApiController` type so that controllers' base types don't need to be updated.</span></span>
* <span data-ttu-id="1f2a4-188">Active la liaison de modèle de style API Web.</span><span class="sxs-lookup"><span data-stu-id="1f2a4-188">Enables Web API-style model binding.</span></span> <span data-ttu-id="1f2a4-189">ASP.NET Core liaison de modèle MVC fonctionne de façon similaire à celle de ASP.NET 4. x MVC 5, par défaut.</span><span class="sxs-lookup"><span data-stu-id="1f2a4-189">ASP.NET Core MVC model binding functions similarly to that of ASP.NET 4.x MVC 5, by default.</span></span> <span data-ttu-id="1f2a4-190">Le shim de compatibilité modifie la liaison de modèle pour qu’elle soit plus semblable aux conventions de liaison de modèle d’API Web 2 ASP.NET 4. x.</span><span class="sxs-lookup"><span data-stu-id="1f2a4-190">The compatibility shim changes model binding to be more similar to ASP.NET 4.x Web API 2 model binding conventions.</span></span> <span data-ttu-id="1f2a4-191">Par exemple, les types complexes sont automatiquement liés à partir du corps de la demande.</span><span class="sxs-lookup"><span data-stu-id="1f2a4-191">For example, complex types are automatically bound from the request body.</span></span>
* <span data-ttu-id="1f2a4-192">Étend la liaison de modèle afin que les actions du contrôleur puissent `HttpRequestMessage`prendre des paramètres de type.</span><span class="sxs-lookup"><span data-stu-id="1f2a4-192">Extends model binding so that controller actions can take parameters of type `HttpRequestMessage`.</span></span>
* <span data-ttu-id="1f2a4-193">Ajoute des formateurs de message permettant aux actions de retourner `HttpResponseMessage`des résultats de type.</span><span class="sxs-lookup"><span data-stu-id="1f2a4-193">Adds message formatters allowing actions to return results of type `HttpResponseMessage`.</span></span>
* <span data-ttu-id="1f2a4-194">Ajoute des méthodes de réponse supplémentaires que les actions de l’API Web 2 peuvent avoir utilisées pour répondre aux réponses :</span><span class="sxs-lookup"><span data-stu-id="1f2a4-194">Adds additional response methods that Web API 2 actions may have used to serve responses:</span></span>
  * <span data-ttu-id="1f2a4-195">`HttpResponseMessage`générateurs</span><span class="sxs-lookup"><span data-stu-id="1f2a4-195">`HttpResponseMessage` generators:</span></span>
    * `CreateResponse<T>`
    * `CreateErrorResponse`
  * <span data-ttu-id="1f2a4-196">Méthodes de résultat d’action :</span><span class="sxs-lookup"><span data-stu-id="1f2a4-196">Action result methods:</span></span>
    * `BadRequestErrorMessageResult`
    * `ExceptionResult`
    * `InternalServerErrorResult`
    * `InvalidModelStateResult`
    * `NegotiatedContentResult`
    * `ResponseMessageResult`
* <span data-ttu-id="1f2a4-197">Ajoute une instance de `IContentNegotiator` au conteneur d’injection de dépendances de l’application et rend disponibles les types liés à la négociation de contenu à partir de [Microsoft. Aspnet. WebApi. client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/).</span><span class="sxs-lookup"><span data-stu-id="1f2a4-197">Adds an instance of `IContentNegotiator` to the app's dependency injection (DI) container and makes available the content negotiation-related types from [Microsoft.AspNet.WebApi.Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/).</span></span> <span data-ttu-id="1f2a4-198">Voici quelques exemples de ces `DefaultContentNegotiator` types `MediaTypeFormatter`: et.</span><span class="sxs-lookup"><span data-stu-id="1f2a4-198">Examples of such types include `DefaultContentNegotiator` and `MediaTypeFormatter`.</span></span>

<span data-ttu-id="1f2a4-199">Pour utiliser le shim de compatibilité :</span><span class="sxs-lookup"><span data-stu-id="1f2a4-199">To use the compatibility shim:</span></span>

1. <span data-ttu-id="1f2a4-200">Installez le package NuGet [Microsoft. AspNetCore. Mvc. WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) .</span><span class="sxs-lookup"><span data-stu-id="1f2a4-200">Install the [Microsoft.AspNetCore.Mvc.WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) NuGet package.</span></span>
1. <span data-ttu-id="1f2a4-201">Enregistrez les services du shim de compatibilité avec le conteneur DI de l’application `services.AddMvc().AddWebApiConventions()` en `Startup.ConfigureServices`appelant dans.</span><span class="sxs-lookup"><span data-stu-id="1f2a4-201">Register the compatibility shim's services with the app's DI container by calling `services.AddMvc().AddWebApiConventions()` in `Startup.ConfigureServices`.</span></span>
1. <span data-ttu-id="1f2a4-202">Définissez des itinéraires spécifiques à l’API `MapWebApiRoute` Web à `IRouteBuilder` l’aide de sur `IApplicationBuilder.UseMvc` le dans l’appel de l’application.</span><span class="sxs-lookup"><span data-stu-id="1f2a4-202">Define web API-specific routes using `MapWebApiRoute` on the `IRouteBuilder` in the app's `IApplicationBuilder.UseMvc` call.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1f2a4-203">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="1f2a4-203">Additional resources</span></span>

* <xref:web-api/index>
* <xref:web-api/action-return-types>
* <xref:mvc/compatibility-version>
