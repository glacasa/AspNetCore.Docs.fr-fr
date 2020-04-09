---
title: 'Tutorial: Appelez une ASP.NET’API Web Core avec JavaScript'
author: rick-anderson
description: Découvrez comment appeler une API web ASP.NET Core avec JavaScript.
ms.author: riande
ms.custom: mvc
ms.date: 11/26/2019
uid: tutorials/web-api-javascript
ms.openlocfilehash: 2a19a7d16ca8b8f5d6ac8eb99ad919b89f1e368b
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78655252"
---
# <a name="tutorial-call-an-aspnet-core-web-api-with-javascript"></a><span data-ttu-id="ac627-103">Tutorial: Appelez une ASP.NET’API Web Core avec JavaScript</span><span class="sxs-lookup"><span data-stu-id="ac627-103">Tutorial: Call an ASP.NET Core web API with JavaScript</span></span>

<span data-ttu-id="ac627-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="ac627-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="ac627-105">Ce tutoriel montre comment appeler une API web ASP.NET Core avec JavaScript à l’aide de l’[API Fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API).</span><span class="sxs-lookup"><span data-stu-id="ac627-105">This tutorial shows how to call an ASP.NET Core web API with JavaScript, using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API).</span></span>

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ac627-106">Pour ASP.NET Core 2.2, consultez la version 2.2 de [Appeler l’API web avec JavaScript](xref:tutorials/first-web-api#call-the-web-api-with-javascript).</span><span class="sxs-lookup"><span data-stu-id="ac627-106">For ASP.NET Core 2.2, see the 2.2 version of [Call the web API with JavaScript](xref:tutorials/first-web-api#call-the-web-api-with-javascript).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="ac627-107">Prérequis</span><span class="sxs-lookup"><span data-stu-id="ac627-107">Prerequisites</span></span>

* <span data-ttu-id="ac627-108">[Tutoriel complet: Créer une API web](xref:tutorials/first-web-api)</span><span class="sxs-lookup"><span data-stu-id="ac627-108">Complete [Tutorial: Create a web API](xref:tutorials/first-web-api)</span></span>
* <span data-ttu-id="ac627-109">Connaissance de CSS, HTML et JavaScript</span><span class="sxs-lookup"><span data-stu-id="ac627-109">Familiarity with CSS, HTML, and JavaScript</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="ac627-110">Appelez l’API web avec JavaScript</span><span class="sxs-lookup"><span data-stu-id="ac627-110">Call the web API with JavaScript</span></span>

<span data-ttu-id="ac627-111">Dans cette section, vous allez ajouter une page HTML contenant des formulaires permettant de créer et de gérer des éléments de tâche.</span><span class="sxs-lookup"><span data-stu-id="ac627-111">In this section, you'll add an HTML page containing forms for creating and managing to-do items.</span></span> <span data-ttu-id="ac627-112">Des gestionnaires d’événements sont joints aux éléments de la page.</span><span class="sxs-lookup"><span data-stu-id="ac627-112">Event handlers are attached to elements on the page.</span></span> <span data-ttu-id="ac627-113">Les gestionnaires d’événements génèrent des requêtes HTTP pour les méthodes d’action de l’API web.</span><span class="sxs-lookup"><span data-stu-id="ac627-113">The event handlers result in HTTP requests to the web API's action methods.</span></span> <span data-ttu-id="ac627-114">La fonction `fetch` de l’API Fetch lance chaque requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="ac627-114">The Fetch API's `fetch` function initiates each HTTP request.</span></span>

<span data-ttu-id="ac627-115">La `fetch` fonction renvoie un objet [Promise,](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) qui `Response` contient une réponse HTTP représentée comme objet.</span><span class="sxs-lookup"><span data-stu-id="ac627-115">The `fetch` function returns a [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) object, which contains an HTTP response represented as a `Response` object.</span></span> <span data-ttu-id="ac627-116">Un modèle courant consiste à extraire le corps de réponse JSON en appelant la fonction `json` sur l'objet `Response`.</span><span class="sxs-lookup"><span data-stu-id="ac627-116">A common pattern is to extract the JSON response body by invoking the `json` function on the `Response` object.</span></span> <span data-ttu-id="ac627-117">JavaScript met à jour la page avec les détails de la réponse de l’API Web.</span><span class="sxs-lookup"><span data-stu-id="ac627-117">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="ac627-118">L'appel `fetch` le plus simple accepte un seul paramètre représentant l’itinéraire.</span><span class="sxs-lookup"><span data-stu-id="ac627-118">The simplest `fetch` call accepts a single parameter representing the route.</span></span> <span data-ttu-id="ac627-119">Un deuxième paramètre, connu sous le nom d’objet `init`, est facultatif.</span><span class="sxs-lookup"><span data-stu-id="ac627-119">A second parameter, known as the `init` object, is optional.</span></span> <span data-ttu-id="ac627-120">`init` est utilisé pour configurer la requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="ac627-120">`init` is used to configure the HTTP request.</span></span>

1. <span data-ttu-id="ac627-121">Configurez l’application pour [servir des fichiers statiques](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) et [activez la cartographie par défaut des fichiers](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="ac627-121">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="ac627-122">Le code en surbrillance suivant est nécessaire dans la méthode `Configure` de *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="ac627-122">The following highlighted code is needed in the `Configure` method of *Startup.cs*:</span></span>

    [!code-csharp[](first-web-api/samples/3.0/TodoApi/StartupJavaScript.cs?highlight=8-9&name=snippet_configure)]

1. <span data-ttu-id="ac627-123">Créez un dossier *wwwroot* dans la racine du projet.</span><span class="sxs-lookup"><span data-stu-id="ac627-123">Create a *wwwroot* folder in the project root.</span></span>

1. <span data-ttu-id="ac627-124">Créez un dossier *js* à l’intérieur du dossier *wwwroot.*</span><span class="sxs-lookup"><span data-stu-id="ac627-124">Create a *js* folder inside of the *wwwroot* folder.</span></span>

1. <span data-ttu-id="ac627-125">Ajoutez un fichier HTML nommé *index.html* au dossier *wwwroot.*</span><span class="sxs-lookup"><span data-stu-id="ac627-125">Add an HTML file named *index.html* to the *wwwroot* folder.</span></span> <span data-ttu-id="ac627-126">Remplacer le contenu de *index.html* par la balisage suivante :</span><span class="sxs-lookup"><span data-stu-id="ac627-126">Replace the contents of *index.html* with the following markup:</span></span>

    [!code-html[](first-web-api/samples/3.0/TodoApi/wwwroot/index.html)]

1. <span data-ttu-id="ac627-127">Ajoutez un fichier JavaScript nommé *site.js* au dossier *wwwroot/js.*</span><span class="sxs-lookup"><span data-stu-id="ac627-127">Add a JavaScript file named *site.js* to the *wwwroot/js* folder.</span></span> <span data-ttu-id="ac627-128">Remplacer le contenu de *site.js* par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="ac627-128">Replace the contents of *site.js* with the following code:</span></span>

    [!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_SiteJs)]

<span data-ttu-id="ac627-129">Vous devrez peut-être changer les paramètres de lancement du projet ASP.NET Core pour tester la page HTML localement :</span><span class="sxs-lookup"><span data-stu-id="ac627-129">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

1. <span data-ttu-id="ac627-130">Ouvrez *Properties\launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="ac627-130">Open *Properties\launchSettings.json*.</span></span>
1. <span data-ttu-id="ac627-131">Supprimer `launchUrl` la propriété pour forcer l’application à ouvrir à *index.html*&mdash;le fichier par défaut du projet.</span><span class="sxs-lookup"><span data-stu-id="ac627-131">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="ac627-132">Cet exemple appelle toutes les méthodes CRUD de l’API web.</span><span class="sxs-lookup"><span data-stu-id="ac627-132">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="ac627-133">Les explications suivantes traitent des demandes de l’API web.</span><span class="sxs-lookup"><span data-stu-id="ac627-133">Following are explanations of the web API requests.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="ac627-134">Obtenir une liste de tâches</span><span class="sxs-lookup"><span data-stu-id="ac627-134">Get a list of to-do items</span></span>

<span data-ttu-id="ac627-135">Dans le code suivant, une requête HTTP GET est envoyée à l'itinéraire *api/TodoItems* :</span><span class="sxs-lookup"><span data-stu-id="ac627-135">In the following code, an HTTP GET request is sent to the *api/TodoItems* route:</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_GetItems)]

<span data-ttu-id="ac627-136">Quand l’API web retourne un code d’état de réussite, la fonction `_displayItems` est appelée.</span><span class="sxs-lookup"><span data-stu-id="ac627-136">When the web API returns a successful status code, the `_displayItems` function is invoked.</span></span> <span data-ttu-id="ac627-137">Chaque élément de tâche du paramètre de tableau accepté par `_displayItems` est ajouté à une table avec les boutons **Modifier** et **Supprimer**.</span><span class="sxs-lookup"><span data-stu-id="ac627-137">Each to-do item in the array parameter accepted by `_displayItems` is added to a table with **Edit** and **Delete** buttons.</span></span> <span data-ttu-id="ac627-138">Si la demande de l’API Web échoue, une erreur est consignée dans la console du navigateur.</span><span class="sxs-lookup"><span data-stu-id="ac627-138">If the web API request fails, an error is logged to the browser's console.</span></span>

### <a name="add-a-to-do-item"></a><span data-ttu-id="ac627-139">Ajouter une tâche</span><span class="sxs-lookup"><span data-stu-id="ac627-139">Add a to-do item</span></span>

<span data-ttu-id="ac627-140">Dans le code suivant :</span><span class="sxs-lookup"><span data-stu-id="ac627-140">In the following code:</span></span>

* <span data-ttu-id="ac627-141">Une variable `item` est déclarée pour construire une représentation littérale d’objet de l’élément de tâche.</span><span class="sxs-lookup"><span data-stu-id="ac627-141">An `item` variable is declared to construct an object literal representation of the to-do item.</span></span>
* <span data-ttu-id="ac627-142">Une requête Fetch est configurée avec les options suivantes :</span><span class="sxs-lookup"><span data-stu-id="ac627-142">A Fetch request is configured with the following options:</span></span>
  * <span data-ttu-id="ac627-143">`method`&mdash; spécifie le verbe d’action POST HTTP.</span><span class="sxs-lookup"><span data-stu-id="ac627-143">`method`&mdash;specifies the POST HTTP action verb.</span></span>
  * <span data-ttu-id="ac627-144">`body`&mdash; spécifie la représentation JSON du corps de la demande.</span><span class="sxs-lookup"><span data-stu-id="ac627-144">`body`&mdash;specifies the JSON representation of the request body.</span></span> <span data-ttu-id="ac627-145">Le JSON est généré en passant le littéral d’objet stocké dans `item` à la fonction [JSON. stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span><span class="sxs-lookup"><span data-stu-id="ac627-145">The JSON is produced by passing the object literal stored in `item` to the [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) function.</span></span>
  * <span data-ttu-id="ac627-146">`headers`&mdash; spécifie les en-têtes de requête HTTP `Accept` et `Content-Type`.</span><span class="sxs-lookup"><span data-stu-id="ac627-146">`headers`&mdash;specifies the `Accept` and `Content-Type` HTTP request headers.</span></span> <span data-ttu-id="ac627-147">Les deux en-têtes sont définies sur `application/json` pour spécifier le type de média respectivement reçu et envoyé.</span><span class="sxs-lookup"><span data-stu-id="ac627-147">Both headers are set to `application/json` to specify the media type being received and sent, respectively.</span></span>
* <span data-ttu-id="ac627-148">Une requête HTTP POST est envoyée à l’itinéraire *api/TodoItems*.</span><span class="sxs-lookup"><span data-stu-id="ac627-148">An HTTP POST request is sent to the *api/TodoItems* route.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_AddItem)]

<span data-ttu-id="ac627-149">Quand l’API web retourne un code d’état de réussite, la fonction `getItems` est appelée pour mettre à jour la table HTML.</span><span class="sxs-lookup"><span data-stu-id="ac627-149">When the web API returns a successful status code, the `getItems` function is invoked to update the HTML table.</span></span> <span data-ttu-id="ac627-150">Si la demande de l’API Web échoue, une erreur est consignée dans la console du navigateur.</span><span class="sxs-lookup"><span data-stu-id="ac627-150">If the web API request fails, an error is logged to the browser's console.</span></span>

### <a name="update-a-to-do-item"></a><span data-ttu-id="ac627-151">Mettre à jour une tâche</span><span class="sxs-lookup"><span data-stu-id="ac627-151">Update a to-do item</span></span>

<span data-ttu-id="ac627-152">La mise à jour d’un élément de tâche est semblable à l’ajout d’un élément. Toutefois, il y a deux différences importantes :</span><span class="sxs-lookup"><span data-stu-id="ac627-152">Updating a to-do item is similar to adding one; however, there are two significant differences:</span></span>

* <span data-ttu-id="ac627-153">L’itinéraire est suivi de l’identificateur unique de l’élément à mettre à jour.</span><span class="sxs-lookup"><span data-stu-id="ac627-153">The route is suffixed with the unique identifier of the item to update.</span></span> <span data-ttu-id="ac627-154">Par exemple, *api/TodoItems/1*.</span><span class="sxs-lookup"><span data-stu-id="ac627-154">For example, *api/TodoItems/1*.</span></span>
* <span data-ttu-id="ac627-155">Le verbe d’action HTTP est PUT, comme indiqué par l’option `method`.</span><span class="sxs-lookup"><span data-stu-id="ac627-155">The HTTP action verb is PUT, as indicated by the `method` option.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_UpdateItem)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="ac627-156">Supprimer une tâche</span><span class="sxs-lookup"><span data-stu-id="ac627-156">Delete a to-do item</span></span>

<span data-ttu-id="ac627-157">Pour supprimer un élément de tâche, définissez l’option de la demande `method` sur `DELETE` et spécifiez l’identificateur unique de l’élément dans l’URL.</span><span class="sxs-lookup"><span data-stu-id="ac627-157">To delete a to-do item, set the request's `method` option to `DELETE` and specify the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_DeleteItem)]

<span data-ttu-id="ac627-158">Passez au tutoriel suivant pour apprendre à générer des pages d’aide d’API web :</span><span class="sxs-lookup"><span data-stu-id="ac627-158">Advance to the next tutorial to learn how to generate web API help pages:</span></span>

> [!div class="nextstepaction"]
> <xref:tutorials/get-started-with-swashbuckle>

::: moniker-end
