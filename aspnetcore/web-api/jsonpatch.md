---
title: JsonPatch dans l’API web ASP.NET Core
author: rick-anderson
description: Découvrez comment gérer les demandes de correctifs JSON dans une API web ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/02/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: web-api/jsonpatch
ms.openlocfilehash: e57c5185323305ccbef7960653c9174931e45d75
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88635396"
---
# <a name="jsonpatch-in-aspnet-core-web-api"></a><span data-ttu-id="038d5-103">JsonPatch dans l’API web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="038d5-103">JsonPatch in ASP.NET Core web API</span></span>

<span data-ttu-id="038d5-104">Par [Tom Dykstra](https://github.com/tdykstra) et [Kirk Larkin](https://github.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="038d5-104">By [Tom Dykstra](https://github.com/tdykstra) and [Kirk Larkin](https://github.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="038d5-105">Cet article explique comment gérer les demandes de correctifs JSON dans une API web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="038d5-105">This article explains how to handle JSON Patch requests in an ASP.NET Core web API.</span></span>

## <a name="package-installation"></a><span data-ttu-id="038d5-106">Installation de package</span><span class="sxs-lookup"><span data-stu-id="038d5-106">Package installation</span></span>

<span data-ttu-id="038d5-107">Pour activer la prise en charge des correctifs JSON dans votre application, procédez comme suit :</span><span class="sxs-lookup"><span data-stu-id="038d5-107">To enable JSON Patch support in your app, complete the following steps:</span></span>

1. <span data-ttu-id="038d5-108">Installez le [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) package NuGet.</span><span class="sxs-lookup"><span data-stu-id="038d5-108">Install the [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet package.</span></span>
1. <span data-ttu-id="038d5-109">Mettez à jour la `Startup.ConfigureServices` méthode du projet à appeler <xref:Microsoft.Extensions.DependencyInjection.NewtonsoftJsonMvcBuilderExtensions.AddNewtonsoftJson*> .</span><span class="sxs-lookup"><span data-stu-id="038d5-109">Update the project's `Startup.ConfigureServices` method to call <xref:Microsoft.Extensions.DependencyInjection.NewtonsoftJsonMvcBuilderExtensions.AddNewtonsoftJson*>.</span></span> <span data-ttu-id="038d5-110">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="038d5-110">For example:</span></span>

    ```csharp
    services
        .AddControllersWithViews()
        .AddNewtonsoftJson();
    ```

<span data-ttu-id="038d5-111">`AddNewtonsoftJson` est compatible avec les méthodes d’inscription du service MVC :</span><span class="sxs-lookup"><span data-stu-id="038d5-111">`AddNewtonsoftJson` is compatible with the MVC service registration methods:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages*>
* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews*>
* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers*>

## <a name="json-patch-addnewtonsoftjson-and-systemtextjson"></a><span data-ttu-id="038d5-112">Patch JSON, AddNewtonsoftJson et System.Text.Jssur</span><span class="sxs-lookup"><span data-stu-id="038d5-112">JSON Patch, AddNewtonsoftJson, and System.Text.Json</span></span>

<span data-ttu-id="038d5-113">`AddNewtonsoftJson` remplace les `System.Text.Json` formateurs d’entrée et de sortie de base utilisés pour mettre en forme **tout** le contenu JSON.</span><span class="sxs-lookup"><span data-stu-id="038d5-113">`AddNewtonsoftJson` replaces the `System.Text.Json`-based input and output formatters used for formatting **all** JSON content.</span></span> <span data-ttu-id="038d5-114">Pour ajouter la prise en charge du correctif JSON à l’aide de `Newtonsoft.Json` , tout en laissant les autres formateurs inchangés, mettez à jour la méthode du projet `Startup.ConfigureServices` comme suit :</span><span class="sxs-lookup"><span data-stu-id="038d5-114">To add support for JSON Patch using `Newtonsoft.Json`, while leaving the other formatters unchanged, update the project's `Startup.ConfigureServices` method as follows:</span></span>

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet)]

<span data-ttu-id="038d5-115">Le code précédent requiert le `Microsoft.AspNetCore.Mvc.NewtonsoftJson` package et les `using` instructions suivantes :</span><span class="sxs-lookup"><span data-stu-id="038d5-115">The preceding code requires the `Microsoft.AspNetCore.Mvc.NewtonsoftJson` package and the following `using` statements:</span></span>

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet1)]

## <a name="patch-http-request-method"></a><span data-ttu-id="038d5-116">Méthode de demande HTTP PATCH</span><span class="sxs-lookup"><span data-stu-id="038d5-116">PATCH HTTP request method</span></span>

<span data-ttu-id="038d5-117">Les méthodes PUT et [PATCH](https://tools.ietf.org/html/rfc5789) sont utilisées pour mettre à jour une ressource existante.</span><span class="sxs-lookup"><span data-stu-id="038d5-117">The PUT and [PATCH](https://tools.ietf.org/html/rfc5789) methods are used to update an existing resource.</span></span> <span data-ttu-id="038d5-118">La différence est que PUT remplace la ressource complète, tandis que PATCH spécifie uniquement les modifications.</span><span class="sxs-lookup"><span data-stu-id="038d5-118">The difference between them is that PUT replaces the entire resource, while PATCH specifies only the changes.</span></span>

## <a name="json-patch"></a><span data-ttu-id="038d5-119">JSON Patch</span><span class="sxs-lookup"><span data-stu-id="038d5-119">JSON Patch</span></span>

<span data-ttu-id="038d5-120">[JSON Patch](https://tools.ietf.org/html/rfc6902) est un format qui spécifie des mises à jour à appliquer à une ressource.</span><span class="sxs-lookup"><span data-stu-id="038d5-120">[JSON Patch](https://tools.ietf.org/html/rfc6902) is a format for specifying updates to be applied to a resource.</span></span> <span data-ttu-id="038d5-121">Un document JSON Patch possède un tableau des *opérations*.</span><span class="sxs-lookup"><span data-stu-id="038d5-121">A JSON Patch document has an array of *operations*.</span></span> <span data-ttu-id="038d5-122">Chaque opération identifie un type particulier de modification.</span><span class="sxs-lookup"><span data-stu-id="038d5-122">Each operation identifies a particular type of change.</span></span> <span data-ttu-id="038d5-123">Les exemples de ces modifications incluent l’ajout d’un élément de tableau ou le remplacement d’une valeur de propriété.</span><span class="sxs-lookup"><span data-stu-id="038d5-123">Examples of such changes include adding an array element or replacing a property value.</span></span>

<span data-ttu-id="038d5-124">Par exemple, les documents JSON suivants représentent une ressource, un document de correctif JSON pour la ressource et le résultat de l’application des correctifs.</span><span class="sxs-lookup"><span data-stu-id="038d5-124">For example, the following JSON documents represent a resource, a JSON Patch document for the resource, and the result of applying the Patch operations.</span></span>

### <a name="resource-example"></a><span data-ttu-id="038d5-125">Exemple de ressource</span><span class="sxs-lookup"><span data-stu-id="038d5-125">Resource example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a><span data-ttu-id="038d5-126">Exemple de correctif JSON</span><span class="sxs-lookup"><span data-stu-id="038d5-126">JSON patch example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

<span data-ttu-id="038d5-127">Dans le code JSON précédent :</span><span class="sxs-lookup"><span data-stu-id="038d5-127">In the preceding JSON:</span></span>

* <span data-ttu-id="038d5-128">La propriété `op` indique le type d’opération.</span><span class="sxs-lookup"><span data-stu-id="038d5-128">The `op` property indicates the type of operation.</span></span>
* <span data-ttu-id="038d5-129">La propriété `path` indique l’élément à mettre à jour.</span><span class="sxs-lookup"><span data-stu-id="038d5-129">The `path` property indicates the element to update.</span></span>
* <span data-ttu-id="038d5-130">La propriété `value` fournit la nouvelle valeur.</span><span class="sxs-lookup"><span data-stu-id="038d5-130">The `value` property provides the new value.</span></span>

### <a name="resource-after-patch"></a><span data-ttu-id="038d5-131">Ressources après correction</span><span class="sxs-lookup"><span data-stu-id="038d5-131">Resource after patch</span></span>

<span data-ttu-id="038d5-132">Voici la ressource après l’application du document JSON Patch précédent :</span><span class="sxs-lookup"><span data-stu-id="038d5-132">Here's the resource after applying the preceding JSON Patch document:</span></span>

```json
{
  "customerName": "Barry",
  "orders": [
    {
      "orderName": "Order0",
      "orderType": null
    },
    {
      "orderName": "Order1",
      "orderType": null
    },
    {
      "orderName": "Order2",
      "orderType": null
    }
  ]
}
```

<span data-ttu-id="038d5-133">Les modifications apportées en appliquant un document de correctif JSON à une ressource sont atomiques.</span><span class="sxs-lookup"><span data-stu-id="038d5-133">The changes made by applying a JSON Patch document to a resource are atomic.</span></span> <span data-ttu-id="038d5-134">Si une opération de la liste échoue, aucune opération de la liste n’est appliquée.</span><span class="sxs-lookup"><span data-stu-id="038d5-134">If any operation in the list fails, no operation in the list is applied.</span></span>

## <a name="path-syntax"></a><span data-ttu-id="038d5-135">Syntaxe du chemin</span><span class="sxs-lookup"><span data-stu-id="038d5-135">Path syntax</span></span>

<span data-ttu-id="038d5-136">Les différents niveaux de la propriété [path](https://tools.ietf.org/html/rfc6901) d’un objet de l’opération sont séparés par des barres obliques.</span><span class="sxs-lookup"><span data-stu-id="038d5-136">The [path](https://tools.ietf.org/html/rfc6901) property of an operation object has slashes between levels.</span></span> <span data-ttu-id="038d5-137">Par exemple : `"/address/zipCode"`.</span><span class="sxs-lookup"><span data-stu-id="038d5-137">For example, `"/address/zipCode"`.</span></span>

<span data-ttu-id="038d5-138">Les index de base zéro sont utilisés pour spécifier les éléments du tableau.</span><span class="sxs-lookup"><span data-stu-id="038d5-138">Zero-based indexes are used to specify array elements.</span></span> <span data-ttu-id="038d5-139">Le premier élément du tableau `addresses` serait à `/addresses/0`.</span><span class="sxs-lookup"><span data-stu-id="038d5-139">The first element of the `addresses` array would be at `/addresses/0`.</span></span> <span data-ttu-id="038d5-140">À `add` la fin d’un tableau, utilisez un trait d’Union ( `-` ) au lieu d’un numéro d’index : `/addresses/-` .</span><span class="sxs-lookup"><span data-stu-id="038d5-140">To `add` to the end of an array, use a hyphen (`-`) rather than an index number: `/addresses/-`.</span></span>

### <a name="operations"></a><span data-ttu-id="038d5-141">Opérations</span><span class="sxs-lookup"><span data-stu-id="038d5-141">Operations</span></span>

<span data-ttu-id="038d5-142">Le tableau suivant mentionne les opérations prises en charge telles qu’elles sont définies dans la [spécification JSON Patch](https://tools.ietf.org/html/rfc6902) :</span><span class="sxs-lookup"><span data-stu-id="038d5-142">The following table shows supported operations as defined in the [JSON Patch specification](https://tools.ietf.org/html/rfc6902):</span></span>

|<span data-ttu-id="038d5-143">Opération</span><span class="sxs-lookup"><span data-stu-id="038d5-143">Operation</span></span>  | <span data-ttu-id="038d5-144">Notes</span><span class="sxs-lookup"><span data-stu-id="038d5-144">Notes</span></span> |
|-----------|--------------------------------|
| `add`     | <span data-ttu-id="038d5-145">Ajouter une propriété ou élément de tableau.</span><span class="sxs-lookup"><span data-stu-id="038d5-145">Add a property or array element.</span></span> <span data-ttu-id="038d5-146">Pour la propriété existante : définir la valeur.</span><span class="sxs-lookup"><span data-stu-id="038d5-146">For existing property: set value.</span></span>|
| `remove`  | <span data-ttu-id="038d5-147">Supprimer une propriété ou un élément de tableau.</span><span class="sxs-lookup"><span data-stu-id="038d5-147">Remove a property or array element.</span></span> |
| `replace` | <span data-ttu-id="038d5-148">Identique à `remove` suivi de `add` au même emplacement.</span><span class="sxs-lookup"><span data-stu-id="038d5-148">Same as `remove` followed by `add` at same location.</span></span> |
| `move`    | <span data-ttu-id="038d5-149">Identique à `remove` de la source suivi de `add` à la destination à l’aide de la valeur de la source.</span><span class="sxs-lookup"><span data-stu-id="038d5-149">Same as `remove` from source followed by `add` to destination using value from source.</span></span> |
| `copy`    | <span data-ttu-id="038d5-150">Identique à `add` à la destination à l’aide de la valeur de la source.</span><span class="sxs-lookup"><span data-stu-id="038d5-150">Same as `add` to destination using value from source.</span></span> |
| `test`    | <span data-ttu-id="038d5-151">Retourne le code d’état de réussite si la valeur à `path` = `value` fournie.</span><span class="sxs-lookup"><span data-stu-id="038d5-151">Return success status code if value at `path` = provided `value`.</span></span>|

## <a name="json-patch-in-aspnet-core"></a><span data-ttu-id="038d5-152">Correctif JSON dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="038d5-152">JSON Patch in ASP.NET Core</span></span>

<span data-ttu-id="038d5-153">L’implémentation ASP.NET Core de JSON Patch est fournie dans le package NuGet [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/).</span><span class="sxs-lookup"><span data-stu-id="038d5-153">The ASP.NET Core implementation of JSON Patch is provided in the [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet package.</span></span>

## <a name="action-method-code"></a><span data-ttu-id="038d5-154">Code de méthode d’action</span><span class="sxs-lookup"><span data-stu-id="038d5-154">Action method code</span></span>

<span data-ttu-id="038d5-155">Dans un contrôleur d’API, une méthode d’action pour JSON Patch :</span><span class="sxs-lookup"><span data-stu-id="038d5-155">In an API controller, an action method for JSON Patch:</span></span>

* <span data-ttu-id="038d5-156">est annotée avec l’attribut `HttpPatch` ;</span><span class="sxs-lookup"><span data-stu-id="038d5-156">Is annotated with the `HttpPatch` attribute.</span></span>
* <span data-ttu-id="038d5-157">Accepte un `JsonPatchDocument<T>` , généralement avec `[FromBody]` .</span><span class="sxs-lookup"><span data-stu-id="038d5-157">Accepts a `JsonPatchDocument<T>`, typically with `[FromBody]`.</span></span>
* <span data-ttu-id="038d5-158">appelle `ApplyTo` sur le document de correctif pour appliquer les modifications.</span><span class="sxs-lookup"><span data-stu-id="038d5-158">Calls `ApplyTo` on the patch document to apply the changes.</span></span>

<span data-ttu-id="038d5-159">Voici un exemple :</span><span class="sxs-lookup"><span data-stu-id="038d5-159">Here's an example:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

<span data-ttu-id="038d5-160">Ce code de l’exemple d’application fonctionne avec le `Customer` modèle suivant :</span><span class="sxs-lookup"><span data-stu-id="038d5-160">This code from the sample app works with the following `Customer` model:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

<span data-ttu-id="038d5-161">L’exemple de méthode d’action :</span><span class="sxs-lookup"><span data-stu-id="038d5-161">The sample action method:</span></span>

* <span data-ttu-id="038d5-162">Construit un objet `Customer`.</span><span class="sxs-lookup"><span data-stu-id="038d5-162">Constructs a `Customer`.</span></span>
* <span data-ttu-id="038d5-163">Applique le correctif.</span><span class="sxs-lookup"><span data-stu-id="038d5-163">Applies the patch.</span></span>
* <span data-ttu-id="038d5-164">Retourne le résultat dans le corps de la réponse.</span><span class="sxs-lookup"><span data-stu-id="038d5-164">Returns the result in the body of the response.</span></span>

<span data-ttu-id="038d5-165">Dans une application réelle, le code récupérerait les données dans un magasin tel qu’une base de données et mettrait à jour la base de données après avoir appliqué le correctif.</span><span class="sxs-lookup"><span data-stu-id="038d5-165">In a real app, the code would retrieve the data from a store such as a database and update the database after applying the patch.</span></span>

### <a name="model-state"></a><span data-ttu-id="038d5-166">État du modèle</span><span class="sxs-lookup"><span data-stu-id="038d5-166">Model state</span></span>

<span data-ttu-id="038d5-167">L’exemple de méthode d’action précédent appelle une surcharge de `ApplyTo` qui prend l’état du modèle comme un de ses paramètres.</span><span class="sxs-lookup"><span data-stu-id="038d5-167">The preceding action method example calls an overload of `ApplyTo` that takes model state as one of its parameters.</span></span> <span data-ttu-id="038d5-168">Avec cette option, vous pouvez obtenir des messages d’erreur dans les réponses.</span><span class="sxs-lookup"><span data-stu-id="038d5-168">With this option, you can get error messages in responses.</span></span> <span data-ttu-id="038d5-169">L’exemple suivant montre le corps d’une demande-réponse incorrecte 400 pour une opération `test` :</span><span class="sxs-lookup"><span data-stu-id="038d5-169">The following example shows the body of a 400 Bad Request response for a `test` operation:</span></span>

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a><span data-ttu-id="038d5-170">Objets dynamiques</span><span class="sxs-lookup"><span data-stu-id="038d5-170">Dynamic objects</span></span>

<span data-ttu-id="038d5-171">L’exemple de méthode d’action suivant montre comment appliquer un correctif à un objet dynamique :</span><span class="sxs-lookup"><span data-stu-id="038d5-171">The following action method example shows how to apply a patch to a dynamic object:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a><span data-ttu-id="038d5-172">L’opération d’ajout</span><span class="sxs-lookup"><span data-stu-id="038d5-172">The add operation</span></span>

* <span data-ttu-id="038d5-173">Si `path` pointe vers un élément du tableau : insère un nouvel élément avant celui spécifié par `path`.</span><span class="sxs-lookup"><span data-stu-id="038d5-173">If `path` points to an array element: inserts new element before the one specified by `path`.</span></span>
* <span data-ttu-id="038d5-174">Si `path` pointe vers une propriété : définit la valeur de propriété.</span><span class="sxs-lookup"><span data-stu-id="038d5-174">If `path` points to a property: sets the property value.</span></span>
* <span data-ttu-id="038d5-175">Si `path` pointe vers un emplacement qui n’existe pas :</span><span class="sxs-lookup"><span data-stu-id="038d5-175">If `path` points to a nonexistent location:</span></span>
  * <span data-ttu-id="038d5-176">Si la ressource à corriger est un objet dynamique : ajoute une propriété.</span><span class="sxs-lookup"><span data-stu-id="038d5-176">If the resource to patch is a dynamic object: adds a property.</span></span>
  * <span data-ttu-id="038d5-177">Si la ressource à corriger est un objet statique : la demande échoue.</span><span class="sxs-lookup"><span data-stu-id="038d5-177">If the resource to patch is a static object: the request fails.</span></span>

<span data-ttu-id="038d5-178">L’exemple de document de correctif suivant définit la valeur de `CustomerName` et ajoute un objet `Order` à la fin du tableau `Orders`.</span><span class="sxs-lookup"><span data-stu-id="038d5-178">The following sample patch document sets the value of `CustomerName` and adds an `Order` object to the end of the `Orders` array.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a><span data-ttu-id="038d5-179">L’opération de suppression</span><span class="sxs-lookup"><span data-stu-id="038d5-179">The remove operation</span></span>

* <span data-ttu-id="038d5-180">Si `path` pointe vers un élément du tableau : supprime l’élément.</span><span class="sxs-lookup"><span data-stu-id="038d5-180">If `path` points to an array element: removes the element.</span></span>
* <span data-ttu-id="038d5-181">Si `path` pointe vers une propriété :</span><span class="sxs-lookup"><span data-stu-id="038d5-181">If `path` points to a property:</span></span>
  * <span data-ttu-id="038d5-182">Si la ressource à corriger est un objet dynamique : supprime la propriété.</span><span class="sxs-lookup"><span data-stu-id="038d5-182">If resource to patch is a dynamic object: removes the property.</span></span>
  * <span data-ttu-id="038d5-183">Si la ressource à corriger est un objet statique :</span><span class="sxs-lookup"><span data-stu-id="038d5-183">If resource to patch is a static object:</span></span>
    * <span data-ttu-id="038d5-184">Si la propriété est Nullable : met la valeur sur Null.</span><span class="sxs-lookup"><span data-stu-id="038d5-184">If the property is nullable: sets it to null.</span></span>
    * <span data-ttu-id="038d5-185">Si la propriété n’est pas Nullable : met la valeur sur `default<T>`.</span><span class="sxs-lookup"><span data-stu-id="038d5-185">If the property is non-nullable, sets it to `default<T>`.</span></span>

<span data-ttu-id="038d5-186">L’exemple de document de correctif suivant définit la `CustomerName` valeur null et supprime `Orders[0]` :</span><span class="sxs-lookup"><span data-stu-id="038d5-186">The following sample patch document sets `CustomerName` to null and deletes `Orders[0]`:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a><span data-ttu-id="038d5-187">L’opération de remplacement</span><span class="sxs-lookup"><span data-stu-id="038d5-187">The replace operation</span></span>

<span data-ttu-id="038d5-188">Cette opération est fonctionnellement identique à `remove` suivi de `add`.</span><span class="sxs-lookup"><span data-stu-id="038d5-188">This operation is functionally the same as a `remove` followed by an `add`.</span></span>

<span data-ttu-id="038d5-189">L’exemple de document de correctif suivant définit la valeur de `CustomerName` et remplace `Orders[0]` par un nouvel `Order` objet :</span><span class="sxs-lookup"><span data-stu-id="038d5-189">The following sample patch document sets the value of `CustomerName` and replaces `Orders[0]`with a new `Order` object:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a><span data-ttu-id="038d5-190">L’opération de déplacement</span><span class="sxs-lookup"><span data-stu-id="038d5-190">The move operation</span></span>

* <span data-ttu-id="038d5-191">Si `path` pointe vers un élément du tableau : copie l’élément `from` à l’emplacement de l’élément `path`, puis exécute une opération `remove` sur l’élément `from`.</span><span class="sxs-lookup"><span data-stu-id="038d5-191">If `path` points to an array element: copies `from` element to location of `path` element, then runs a `remove` operation on the `from` element.</span></span>
* <span data-ttu-id="038d5-192">Si `path` pointe vers une propriété : copie la valeur de la propriété `from` vers la propriété `path`, puis exécute une opération `remove` sur la propriété `from`.</span><span class="sxs-lookup"><span data-stu-id="038d5-192">If `path` points to a property: copies value of `from` property to `path` property, then runs a `remove` operation on the `from` property.</span></span>
* <span data-ttu-id="038d5-193">Si `path` pointe vers une propriété qui n’existe pas :</span><span class="sxs-lookup"><span data-stu-id="038d5-193">If `path` points to a nonexistent property:</span></span>
  * <span data-ttu-id="038d5-194">Si la ressource à corriger est un objet statique : la demande échoue.</span><span class="sxs-lookup"><span data-stu-id="038d5-194">If the resource to patch is a static object: the request fails.</span></span>
  * <span data-ttu-id="038d5-195">Si la ressource à corriger est un objet dynamique : copie la propriété `from` à un emplacement indiqué par `path`, puis exécute une opération `remove` sur la propriété `from`.</span><span class="sxs-lookup"><span data-stu-id="038d5-195">If the resource to patch is a dynamic object: copies `from` property to location indicated by `path`, then runs a `remove` operation on the `from` property.</span></span>

<span data-ttu-id="038d5-196">L’exemple de document de correctif suivant :</span><span class="sxs-lookup"><span data-stu-id="038d5-196">The following sample patch document:</span></span>

* <span data-ttu-id="038d5-197">Copie la valeur de `Orders[0].OrderName` vers `CustomerName`.</span><span class="sxs-lookup"><span data-stu-id="038d5-197">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="038d5-198">Met `Orders[0].OrderName` sur la valeur Null.</span><span class="sxs-lookup"><span data-stu-id="038d5-198">Sets `Orders[0].OrderName` to null.</span></span>
* <span data-ttu-id="038d5-199">Déplace `Orders[1]` avant `Orders[0]`.</span><span class="sxs-lookup"><span data-stu-id="038d5-199">Moves `Orders[1]` to before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a><span data-ttu-id="038d5-200">L’opération de copie</span><span class="sxs-lookup"><span data-stu-id="038d5-200">The copy operation</span></span>

<span data-ttu-id="038d5-201">Cette opération est fonctionnellement identique à une opération `move` sans l’étape `remove` finale.</span><span class="sxs-lookup"><span data-stu-id="038d5-201">This operation is functionally the same as a `move` operation without the final `remove` step.</span></span>

<span data-ttu-id="038d5-202">L’exemple de document de correctif suivant :</span><span class="sxs-lookup"><span data-stu-id="038d5-202">The following sample patch document:</span></span>

* <span data-ttu-id="038d5-203">Copie la valeur de `Orders[0].OrderName` vers `CustomerName`.</span><span class="sxs-lookup"><span data-stu-id="038d5-203">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="038d5-204">Insère une copie de `Orders[1]` avant `Orders[0]`.</span><span class="sxs-lookup"><span data-stu-id="038d5-204">Inserts a copy of `Orders[1]` before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a><span data-ttu-id="038d5-205">L’opération de test</span><span class="sxs-lookup"><span data-stu-id="038d5-205">The test operation</span></span>

<span data-ttu-id="038d5-206">Si la valeur à l’emplacement indiqué par `path` est différente de la valeur fournie dans `value`, la demande échoue.</span><span class="sxs-lookup"><span data-stu-id="038d5-206">If the value at the location indicated by `path` is different from the value provided in `value`, the request fails.</span></span> <span data-ttu-id="038d5-207">Dans ce cas, toute la demande PATCH échoue, même si toutes les autres opérations dans le document de correctif réussiraient autrement.</span><span class="sxs-lookup"><span data-stu-id="038d5-207">In that case, the whole PATCH request fails even if all other operations in the patch document would otherwise succeed.</span></span>

<span data-ttu-id="038d5-208">L’opération `test` est généralement utilisée pour empêcher une mise à jour lorsqu’il y a un conflit d’accès concurrentiel.</span><span class="sxs-lookup"><span data-stu-id="038d5-208">The `test` operation is commonly used to prevent an update when there's a concurrency conflict.</span></span>

<span data-ttu-id="038d5-209">L’exemple de document de correctif suivant n’a aucun effet si la valeur initiale de `CustomerName` est « John », car le test échoue :</span><span class="sxs-lookup"><span data-stu-id="038d5-209">The following sample patch document has no effect if the initial value of `CustomerName` is "John", because the test fails:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a><span data-ttu-id="038d5-210">Obtenir le code</span><span class="sxs-lookup"><span data-stu-id="038d5-210">Get the code</span></span>

<span data-ttu-id="038d5-211">[Affichez ou téléchargez l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples).</span><span class="sxs-lookup"><span data-stu-id="038d5-211">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples).</span></span> <span data-ttu-id="038d5-212">([Procédure de téléchargement](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="038d5-212">([How to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="038d5-213">Pour tester l’exemple, exécutez l’application et envoyez des demandes HTTP avec les paramètres suivants :</span><span class="sxs-lookup"><span data-stu-id="038d5-213">To test the sample, run the app and send HTTP requests with the following settings:</span></span>

* <span data-ttu-id="038d5-214">URL : `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span><span class="sxs-lookup"><span data-stu-id="038d5-214">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span></span>
* <span data-ttu-id="038d5-215">Méthode HTTP : `PATCH`</span><span class="sxs-lookup"><span data-stu-id="038d5-215">HTTP method: `PATCH`</span></span>
* <span data-ttu-id="038d5-216">En-tête : `Content-Type: application/json-patch+json`</span><span class="sxs-lookup"><span data-stu-id="038d5-216">Header: `Content-Type: application/json-patch+json`</span></span>
* <span data-ttu-id="038d5-217">Corps : copiez et collez l’un des exemples de document de correctif JSON à partir du dossier de projet *JSON* .</span><span class="sxs-lookup"><span data-stu-id="038d5-217">Body: Copy and paste one of the JSON patch document samples from the *JSON* project folder.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="038d5-218">Ressources complémentaires</span><span class="sxs-lookup"><span data-stu-id="038d5-218">Additional resources</span></span>

* [<span data-ttu-id="038d5-219">Spécification de méthode PATCH IETF RFC 5789 PATCH</span><span class="sxs-lookup"><span data-stu-id="038d5-219">IETF RFC 5789 PATCH method specification</span></span>](https://tools.ietf.org/html/rfc5789)
* [<span data-ttu-id="038d5-220">Spécification JSON Patch IETF RFC 6902</span><span class="sxs-lookup"><span data-stu-id="038d5-220">IETF RFC 6902 JSON Patch specification</span></span>](https://tools.ietf.org/html/rfc6902)
* [<span data-ttu-id="038d5-221">Spécification de format de chemin JSON Patch IETF RFC 6901</span><span class="sxs-lookup"><span data-stu-id="038d5-221">IETF RFC 6901 JSON Patch path format spec</span></span>](https://tools.ietf.org/html/rfc6901)
* <span data-ttu-id="038d5-222">[Documentation JSON Patch](https://jsonpatch.com/).</span><span class="sxs-lookup"><span data-stu-id="038d5-222">[JSON Patch documentation](https://jsonpatch.com/).</span></span> <span data-ttu-id="038d5-223">Inclut des liens vers des ressources pour la création de documents JSON Patch.</span><span class="sxs-lookup"><span data-stu-id="038d5-223">Includes links to resources for creating JSON Patch documents.</span></span>
* [<span data-ttu-id="038d5-224">Code source ASP.NET Core JSON Patch</span><span class="sxs-lookup"><span data-stu-id="038d5-224">ASP.NET Core JSON Patch source code</span></span>](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="038d5-225">Cet article explique comment gérer les demandes de correctifs JSON dans une API web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="038d5-225">This article explains how to handle JSON Patch requests in an ASP.NET Core web API.</span></span>

## <a name="patch-http-request-method"></a><span data-ttu-id="038d5-226">Méthode de demande HTTP PATCH</span><span class="sxs-lookup"><span data-stu-id="038d5-226">PATCH HTTP request method</span></span>

<span data-ttu-id="038d5-227">Les méthodes PUT et [PATCH](https://tools.ietf.org/html/rfc5789) sont utilisées pour mettre à jour une ressource existante.</span><span class="sxs-lookup"><span data-stu-id="038d5-227">The PUT and [PATCH](https://tools.ietf.org/html/rfc5789) methods are used to update an existing resource.</span></span> <span data-ttu-id="038d5-228">La différence est que PUT remplace la ressource complète, tandis que PATCH spécifie uniquement les modifications.</span><span class="sxs-lookup"><span data-stu-id="038d5-228">The difference between them is that PUT replaces the entire resource, while PATCH specifies only the changes.</span></span>

## <a name="json-patch"></a><span data-ttu-id="038d5-229">JSON Patch</span><span class="sxs-lookup"><span data-stu-id="038d5-229">JSON Patch</span></span>

<span data-ttu-id="038d5-230">[JSON Patch](https://tools.ietf.org/html/rfc6902) est un format qui spécifie des mises à jour à appliquer à une ressource.</span><span class="sxs-lookup"><span data-stu-id="038d5-230">[JSON Patch](https://tools.ietf.org/html/rfc6902) is a format for specifying updates to be applied to a resource.</span></span> <span data-ttu-id="038d5-231">Un document JSON Patch possède un tableau des *opérations*.</span><span class="sxs-lookup"><span data-stu-id="038d5-231">A JSON Patch document has an array of *operations*.</span></span> <span data-ttu-id="038d5-232">Chaque opération identifie un type particulier de modification, tel qu’ajouter un élément de tableau ou remplacer une valeur de propriété.</span><span class="sxs-lookup"><span data-stu-id="038d5-232">Each operation identifies a particular type of change, such as add an array element or replace a property value.</span></span>

<span data-ttu-id="038d5-233">Par exemple, les documents JSON suivants représentent une ressource, un document de correctif JSON pour la ressource et le résultat de l’application de l’application des correctifs.</span><span class="sxs-lookup"><span data-stu-id="038d5-233">For example, the following JSON documents represent a resource, a JSON patch document for the resource, and the result of applying the patch operations.</span></span>

### <a name="resource-example"></a><span data-ttu-id="038d5-234">Exemple de ressource</span><span class="sxs-lookup"><span data-stu-id="038d5-234">Resource example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a><span data-ttu-id="038d5-235">Exemple de correctif JSON</span><span class="sxs-lookup"><span data-stu-id="038d5-235">JSON patch example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

<span data-ttu-id="038d5-236">Dans le code JSON précédent :</span><span class="sxs-lookup"><span data-stu-id="038d5-236">In the preceding JSON:</span></span>

* <span data-ttu-id="038d5-237">La propriété `op` indique le type d’opération.</span><span class="sxs-lookup"><span data-stu-id="038d5-237">The `op` property indicates the type of operation.</span></span>
* <span data-ttu-id="038d5-238">La propriété `path` indique l’élément à mettre à jour.</span><span class="sxs-lookup"><span data-stu-id="038d5-238">The `path` property indicates the element to update.</span></span>
* <span data-ttu-id="038d5-239">La propriété `value` fournit la nouvelle valeur.</span><span class="sxs-lookup"><span data-stu-id="038d5-239">The `value` property provides the new value.</span></span>

### <a name="resource-after-patch"></a><span data-ttu-id="038d5-240">Ressources après correction</span><span class="sxs-lookup"><span data-stu-id="038d5-240">Resource after patch</span></span>

<span data-ttu-id="038d5-241">Voici la ressource après l’application du document JSON Patch précédent :</span><span class="sxs-lookup"><span data-stu-id="038d5-241">Here's the resource after applying the preceding JSON Patch document:</span></span>

```json
{
  "customerName": "Barry",
  "orders": [
    {
      "orderName": "Order0",
      "orderType": null
    },
    {
      "orderName": "Order1",
      "orderType": null
    },
    {
      "orderName": "Order2",
      "orderType": null
    }
  ]
}
```

<span data-ttu-id="038d5-242">Les modifications apportées en appliquant un document JSON Patch à une ressource sont atomiques : si une opération de la liste échoue, aucune opération de la liste n’est appliquée.</span><span class="sxs-lookup"><span data-stu-id="038d5-242">The changes made by applying a JSON Patch document to a resource are atomic: if any operation in the list fails, no operation in the list is applied.</span></span>

## <a name="path-syntax"></a><span data-ttu-id="038d5-243">Syntaxe du chemin</span><span class="sxs-lookup"><span data-stu-id="038d5-243">Path syntax</span></span>

<span data-ttu-id="038d5-244">Les différents niveaux de la propriété [path](https://tools.ietf.org/html/rfc6901) d’un objet de l’opération sont séparés par des barres obliques.</span><span class="sxs-lookup"><span data-stu-id="038d5-244">The [path](https://tools.ietf.org/html/rfc6901) property of an operation object has slashes between levels.</span></span> <span data-ttu-id="038d5-245">Par exemple : `"/address/zipCode"`.</span><span class="sxs-lookup"><span data-stu-id="038d5-245">For example, `"/address/zipCode"`.</span></span>

<span data-ttu-id="038d5-246">Les index de base zéro sont utilisés pour spécifier les éléments du tableau.</span><span class="sxs-lookup"><span data-stu-id="038d5-246">Zero-based indexes are used to specify array elements.</span></span> <span data-ttu-id="038d5-247">Le premier élément du tableau `addresses` serait à `/addresses/0`.</span><span class="sxs-lookup"><span data-stu-id="038d5-247">The first element of the `addresses` array would be at `/addresses/0`.</span></span> <span data-ttu-id="038d5-248">Pour `add` à la fin d’un tableau, utilisez un trait d’union (-) plutôt qu’un numéro d’index : `/addresses/-`.</span><span class="sxs-lookup"><span data-stu-id="038d5-248">To `add` to the end of an array, use a hyphen (-) rather than an index number: `/addresses/-`.</span></span>

### <a name="operations"></a><span data-ttu-id="038d5-249">Opérations</span><span class="sxs-lookup"><span data-stu-id="038d5-249">Operations</span></span>

<span data-ttu-id="038d5-250">Le tableau suivant mentionne les opérations prises en charge telles qu’elles sont définies dans la [spécification JSON Patch](https://tools.ietf.org/html/rfc6902) :</span><span class="sxs-lookup"><span data-stu-id="038d5-250">The following table shows supported operations as defined in the [JSON Patch specification](https://tools.ietf.org/html/rfc6902):</span></span>

|<span data-ttu-id="038d5-251">Opération</span><span class="sxs-lookup"><span data-stu-id="038d5-251">Operation</span></span>  | <span data-ttu-id="038d5-252">Notes</span><span class="sxs-lookup"><span data-stu-id="038d5-252">Notes</span></span> |
|-----------|--------------------------------|
| `add`     | <span data-ttu-id="038d5-253">Ajouter une propriété ou élément de tableau.</span><span class="sxs-lookup"><span data-stu-id="038d5-253">Add a property or array element.</span></span> <span data-ttu-id="038d5-254">Pour la propriété existante : définir la valeur.</span><span class="sxs-lookup"><span data-stu-id="038d5-254">For existing property: set value.</span></span>|
| `remove`  | <span data-ttu-id="038d5-255">Supprimer une propriété ou un élément de tableau.</span><span class="sxs-lookup"><span data-stu-id="038d5-255">Remove a property or array element.</span></span> |
| `replace` | <span data-ttu-id="038d5-256">Identique à `remove` suivi de `add` au même emplacement.</span><span class="sxs-lookup"><span data-stu-id="038d5-256">Same as `remove` followed by `add` at same location.</span></span> |
| `move`    | <span data-ttu-id="038d5-257">Identique à `remove` de la source suivi de `add` à la destination à l’aide de la valeur de la source.</span><span class="sxs-lookup"><span data-stu-id="038d5-257">Same as `remove` from source followed by `add` to destination using value from source.</span></span> |
| `copy`    | <span data-ttu-id="038d5-258">Identique à `add` à la destination à l’aide de la valeur de la source.</span><span class="sxs-lookup"><span data-stu-id="038d5-258">Same as `add` to destination using value from source.</span></span> |
| `test`    | <span data-ttu-id="038d5-259">Retourne le code d’état de réussite si la valeur à `path` = `value` fournie.</span><span class="sxs-lookup"><span data-stu-id="038d5-259">Return success status code if value at `path` = provided `value`.</span></span>|

## <a name="jsonpatch-in-aspnet-core"></a><span data-ttu-id="038d5-260">JsonPatch dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="038d5-260">JsonPatch in ASP.NET Core</span></span>

<span data-ttu-id="038d5-261">L’implémentation ASP.NET Core de JSON Patch est fournie dans le package NuGet [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/).</span><span class="sxs-lookup"><span data-stu-id="038d5-261">The ASP.NET Core implementation of JSON Patch is provided in the [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet package.</span></span> <span data-ttu-id="038d5-262">Le package est inclus dans le sous-package [Microsoft. AspnetCore. app](xref:fundamentals/metapackage-app) .</span><span class="sxs-lookup"><span data-stu-id="038d5-262">The package is included in the [Microsoft.AspnetCore.App](xref:fundamentals/metapackage-app) metapackage.</span></span>

## <a name="action-method-code"></a><span data-ttu-id="038d5-263">Code de méthode d’action</span><span class="sxs-lookup"><span data-stu-id="038d5-263">Action method code</span></span>

<span data-ttu-id="038d5-264">Dans un contrôleur d’API, une méthode d’action pour JSON Patch :</span><span class="sxs-lookup"><span data-stu-id="038d5-264">In an API controller, an action method for JSON Patch:</span></span>

* <span data-ttu-id="038d5-265">est annotée avec l’attribut `HttpPatch` ;</span><span class="sxs-lookup"><span data-stu-id="038d5-265">Is annotated with the `HttpPatch` attribute.</span></span>
* <span data-ttu-id="038d5-266">Accepte un `JsonPatchDocument<T>` , généralement avec `[FromBody]` .</span><span class="sxs-lookup"><span data-stu-id="038d5-266">Accepts a `JsonPatchDocument<T>`, typically with `[FromBody]`.</span></span>
* <span data-ttu-id="038d5-267">appelle `ApplyTo` sur le document de correctif pour appliquer les modifications.</span><span class="sxs-lookup"><span data-stu-id="038d5-267">Calls `ApplyTo` on the patch document to apply the changes.</span></span>

<span data-ttu-id="038d5-268">Voici un exemple :</span><span class="sxs-lookup"><span data-stu-id="038d5-268">Here's an example:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

<span data-ttu-id="038d5-269">Ce code de l’exemple d’application fonctionne avec le modèle `Customer` suivant.</span><span class="sxs-lookup"><span data-stu-id="038d5-269">This code from the sample app works with the following `Customer` model.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

<span data-ttu-id="038d5-270">L’exemple de méthode d’action :</span><span class="sxs-lookup"><span data-stu-id="038d5-270">The sample action method:</span></span>

* <span data-ttu-id="038d5-271">Construit un objet `Customer`.</span><span class="sxs-lookup"><span data-stu-id="038d5-271">Constructs a `Customer`.</span></span>
* <span data-ttu-id="038d5-272">Applique le correctif.</span><span class="sxs-lookup"><span data-stu-id="038d5-272">Applies the patch.</span></span>
* <span data-ttu-id="038d5-273">Retourne le résultat dans le corps de la réponse.</span><span class="sxs-lookup"><span data-stu-id="038d5-273">Returns the result in the body of the response.</span></span>

 <span data-ttu-id="038d5-274">Dans une application réelle, le code récupérerait les données dans un magasin tel qu’une base de données et mettrait à jour la base de données après avoir appliqué le correctif.</span><span class="sxs-lookup"><span data-stu-id="038d5-274">In a real app, the code would retrieve the data from a store such as a database and update the database after applying the patch.</span></span>

### <a name="model-state"></a><span data-ttu-id="038d5-275">État du modèle</span><span class="sxs-lookup"><span data-stu-id="038d5-275">Model state</span></span>

<span data-ttu-id="038d5-276">L’exemple de méthode d’action précédent appelle une surcharge de `ApplyTo` qui prend l’état du modèle comme un de ses paramètres.</span><span class="sxs-lookup"><span data-stu-id="038d5-276">The preceding action method example calls an overload of `ApplyTo` that takes model state as one of its parameters.</span></span> <span data-ttu-id="038d5-277">Avec cette option, vous pouvez obtenir des messages d’erreur dans les réponses.</span><span class="sxs-lookup"><span data-stu-id="038d5-277">With this option, you can get error messages in responses.</span></span> <span data-ttu-id="038d5-278">L’exemple suivant montre le corps d’une demande-réponse incorrecte 400 pour une opération `test` :</span><span class="sxs-lookup"><span data-stu-id="038d5-278">The following example shows the body of a 400 Bad Request response for a `test` operation:</span></span>

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a><span data-ttu-id="038d5-279">Objets dynamiques</span><span class="sxs-lookup"><span data-stu-id="038d5-279">Dynamic objects</span></span>

<span data-ttu-id="038d5-280">L’exemple de méthode d’action suivant montre comment appliquer un correctif à un objet dynamique.</span><span class="sxs-lookup"><span data-stu-id="038d5-280">The following action method example shows how to apply a patch to a dynamic object.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a><span data-ttu-id="038d5-281">L’opération d’ajout</span><span class="sxs-lookup"><span data-stu-id="038d5-281">The add operation</span></span>

* <span data-ttu-id="038d5-282">Si `path` pointe vers un élément du tableau : insère un nouvel élément avant celui spécifié par `path`.</span><span class="sxs-lookup"><span data-stu-id="038d5-282">If `path` points to an array element: inserts new element before the one specified by `path`.</span></span>
* <span data-ttu-id="038d5-283">Si `path` pointe vers une propriété : définit la valeur de propriété.</span><span class="sxs-lookup"><span data-stu-id="038d5-283">If `path` points to a property: sets the property value.</span></span>
* <span data-ttu-id="038d5-284">Si `path` pointe vers un emplacement qui n’existe pas :</span><span class="sxs-lookup"><span data-stu-id="038d5-284">If `path` points to a nonexistent location:</span></span>
  * <span data-ttu-id="038d5-285">Si la ressource à corriger est un objet dynamique : ajoute une propriété.</span><span class="sxs-lookup"><span data-stu-id="038d5-285">If the resource to patch is a dynamic object: adds a property.</span></span>
  * <span data-ttu-id="038d5-286">Si la ressource à corriger est un objet statique : la demande échoue.</span><span class="sxs-lookup"><span data-stu-id="038d5-286">If the resource to patch is a static object: the request fails.</span></span>

<span data-ttu-id="038d5-287">L’exemple de document de correctif suivant définit la valeur de `CustomerName` et ajoute un objet `Order` à la fin du tableau `Orders`.</span><span class="sxs-lookup"><span data-stu-id="038d5-287">The following sample patch document sets the value of `CustomerName` and adds an `Order` object to the end of the `Orders` array.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a><span data-ttu-id="038d5-288">L’opération de suppression</span><span class="sxs-lookup"><span data-stu-id="038d5-288">The remove operation</span></span>

* <span data-ttu-id="038d5-289">Si `path` pointe vers un élément du tableau : supprime l’élément.</span><span class="sxs-lookup"><span data-stu-id="038d5-289">If `path` points to an array element: removes the element.</span></span>
* <span data-ttu-id="038d5-290">Si `path` pointe vers une propriété :</span><span class="sxs-lookup"><span data-stu-id="038d5-290">If `path` points to a property:</span></span>
  * <span data-ttu-id="038d5-291">Si la ressource à corriger est un objet dynamique : supprime la propriété.</span><span class="sxs-lookup"><span data-stu-id="038d5-291">If resource to patch is a dynamic object: removes the property.</span></span>
  * <span data-ttu-id="038d5-292">Si la ressource à corriger est un objet statique :</span><span class="sxs-lookup"><span data-stu-id="038d5-292">If resource to patch is a static object:</span></span>
    * <span data-ttu-id="038d5-293">Si la propriété est Nullable : met la valeur sur Null.</span><span class="sxs-lookup"><span data-stu-id="038d5-293">If the property is nullable: sets it to null.</span></span>
    * <span data-ttu-id="038d5-294">Si la propriété n’est pas Nullable : met la valeur sur `default<T>`.</span><span class="sxs-lookup"><span data-stu-id="038d5-294">If the property is non-nullable, sets it to `default<T>`.</span></span>

<span data-ttu-id="038d5-295">L’exemple suivant de document de correctif définit `CustomerName` sur Null et supprime `Orders[0]`.</span><span class="sxs-lookup"><span data-stu-id="038d5-295">The following sample patch document sets `CustomerName` to null and deletes `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a><span data-ttu-id="038d5-296">L’opération de remplacement</span><span class="sxs-lookup"><span data-stu-id="038d5-296">The replace operation</span></span>

<span data-ttu-id="038d5-297">Cette opération est fonctionnellement identique à `remove` suivi de `add`.</span><span class="sxs-lookup"><span data-stu-id="038d5-297">This operation is functionally the same as a `remove` followed by an `add`.</span></span>

<span data-ttu-id="038d5-298">L’exemple suivant de document de correctif définit la valeur de `CustomerName` et remplace `Orders[0]` avec un nouvel objet `Order`.</span><span class="sxs-lookup"><span data-stu-id="038d5-298">The following sample patch document sets the value of `CustomerName` and replaces `Orders[0]`with a new `Order` object.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a><span data-ttu-id="038d5-299">L’opération de déplacement</span><span class="sxs-lookup"><span data-stu-id="038d5-299">The move operation</span></span>

* <span data-ttu-id="038d5-300">Si `path` pointe vers un élément du tableau : copie l’élément `from` à l’emplacement de l’élément `path`, puis exécute une opération `remove` sur l’élément `from`.</span><span class="sxs-lookup"><span data-stu-id="038d5-300">If `path` points to an array element: copies `from` element to location of `path` element, then runs a `remove` operation on the `from` element.</span></span>
* <span data-ttu-id="038d5-301">Si `path` pointe vers une propriété : copie la valeur de la propriété `from` vers la propriété `path`, puis exécute une opération `remove` sur la propriété `from`.</span><span class="sxs-lookup"><span data-stu-id="038d5-301">If `path` points to a property: copies value of `from` property to `path` property, then runs a `remove` operation on the `from` property.</span></span>
* <span data-ttu-id="038d5-302">Si `path` pointe vers une propriété qui n’existe pas :</span><span class="sxs-lookup"><span data-stu-id="038d5-302">If `path` points to a nonexistent property:</span></span>
  * <span data-ttu-id="038d5-303">Si la ressource à corriger est un objet statique : la demande échoue.</span><span class="sxs-lookup"><span data-stu-id="038d5-303">If the resource to patch is a static object: the request fails.</span></span>
  * <span data-ttu-id="038d5-304">Si la ressource à corriger est un objet dynamique : copie la propriété `from` à un emplacement indiqué par `path`, puis exécute une opération `remove` sur la propriété `from`.</span><span class="sxs-lookup"><span data-stu-id="038d5-304">If the resource to patch is a dynamic object: copies `from` property to location indicated by `path`, then runs a `remove` operation on the `from` property.</span></span>

<span data-ttu-id="038d5-305">L’exemple de document de correctif suivant :</span><span class="sxs-lookup"><span data-stu-id="038d5-305">The following sample patch document:</span></span>

* <span data-ttu-id="038d5-306">Copie la valeur de `Orders[0].OrderName` vers `CustomerName`.</span><span class="sxs-lookup"><span data-stu-id="038d5-306">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="038d5-307">Met `Orders[0].OrderName` sur la valeur Null.</span><span class="sxs-lookup"><span data-stu-id="038d5-307">Sets `Orders[0].OrderName` to null.</span></span>
* <span data-ttu-id="038d5-308">Déplace `Orders[1]` avant `Orders[0]`.</span><span class="sxs-lookup"><span data-stu-id="038d5-308">Moves `Orders[1]` to before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a><span data-ttu-id="038d5-309">L’opération de copie</span><span class="sxs-lookup"><span data-stu-id="038d5-309">The copy operation</span></span>

<span data-ttu-id="038d5-310">Cette opération est fonctionnellement identique à une opération `move` sans l’étape `remove` finale.</span><span class="sxs-lookup"><span data-stu-id="038d5-310">This operation is functionally the same as a `move` operation without the final `remove` step.</span></span>

<span data-ttu-id="038d5-311">L’exemple de document de correctif suivant :</span><span class="sxs-lookup"><span data-stu-id="038d5-311">The following sample patch document:</span></span>

* <span data-ttu-id="038d5-312">Copie la valeur de `Orders[0].OrderName` vers `CustomerName`.</span><span class="sxs-lookup"><span data-stu-id="038d5-312">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="038d5-313">Insère une copie de `Orders[1]` avant `Orders[0]`.</span><span class="sxs-lookup"><span data-stu-id="038d5-313">Inserts a copy of `Orders[1]` before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a><span data-ttu-id="038d5-314">L’opération de test</span><span class="sxs-lookup"><span data-stu-id="038d5-314">The test operation</span></span>

<span data-ttu-id="038d5-315">Si la valeur à l’emplacement indiqué par `path` est différente de la valeur fournie dans `value`, la demande échoue.</span><span class="sxs-lookup"><span data-stu-id="038d5-315">If the value at the location indicated by `path` is different from the value provided in `value`, the request fails.</span></span> <span data-ttu-id="038d5-316">Dans ce cas, toute la demande PATCH échoue, même si toutes les autres opérations dans le document de correctif réussiraient autrement.</span><span class="sxs-lookup"><span data-stu-id="038d5-316">In that case, the whole PATCH request fails even if all other operations in the patch document would otherwise succeed.</span></span>

<span data-ttu-id="038d5-317">L’opération `test` est généralement utilisée pour empêcher une mise à jour lorsqu’il y a un conflit d’accès concurrentiel.</span><span class="sxs-lookup"><span data-stu-id="038d5-317">The `test` operation is commonly used to prevent an update when there's a concurrency conflict.</span></span>

<span data-ttu-id="038d5-318">L’exemple de document de correctif suivant n’a aucun effet si la valeur initiale de `CustomerName` est « John », car le test échoue :</span><span class="sxs-lookup"><span data-stu-id="038d5-318">The following sample patch document has no effect if the initial value of `CustomerName` is "John", because the test fails:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a><span data-ttu-id="038d5-319">Obtenir le code</span><span class="sxs-lookup"><span data-stu-id="038d5-319">Get the code</span></span>

<span data-ttu-id="038d5-320">[Affichez ou téléchargez l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2).</span><span class="sxs-lookup"><span data-stu-id="038d5-320">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2).</span></span> <span data-ttu-id="038d5-321">([Procédure de téléchargement](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="038d5-321">([How to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="038d5-322">Pour tester l’exemple, exécutez l’application et envoyez des demandes HTTP avec les paramètres suivants :</span><span class="sxs-lookup"><span data-stu-id="038d5-322">To test the sample, run the app and send HTTP requests with the following settings:</span></span>

* <span data-ttu-id="038d5-323">URL : `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span><span class="sxs-lookup"><span data-stu-id="038d5-323">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span></span>
* <span data-ttu-id="038d5-324">Méthode HTTP : `PATCH`</span><span class="sxs-lookup"><span data-stu-id="038d5-324">HTTP method: `PATCH`</span></span>
* <span data-ttu-id="038d5-325">En-tête : `Content-Type: application/json-patch+json`</span><span class="sxs-lookup"><span data-stu-id="038d5-325">Header: `Content-Type: application/json-patch+json`</span></span>
* <span data-ttu-id="038d5-326">Corps : copiez et collez l’un des exemples de document de correctif JSON à partir du dossier de projet *JSON* .</span><span class="sxs-lookup"><span data-stu-id="038d5-326">Body: Copy and paste one of the JSON patch document samples from the *JSON* project folder.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="038d5-327">Ressources complémentaires</span><span class="sxs-lookup"><span data-stu-id="038d5-327">Additional resources</span></span>

* [<span data-ttu-id="038d5-328">Spécification de méthode PATCH IETF RFC 5789 PATCH</span><span class="sxs-lookup"><span data-stu-id="038d5-328">IETF RFC 5789 PATCH method specification</span></span>](https://tools.ietf.org/html/rfc5789)
* [<span data-ttu-id="038d5-329">Spécification JSON Patch IETF RFC 6902</span><span class="sxs-lookup"><span data-stu-id="038d5-329">IETF RFC 6902 JSON Patch specification</span></span>](https://tools.ietf.org/html/rfc6902)
* [<span data-ttu-id="038d5-330">Spécification de format de chemin JSON Patch IETF RFC 6901</span><span class="sxs-lookup"><span data-stu-id="038d5-330">IETF RFC 6901 JSON Patch path format spec</span></span>](https://tools.ietf.org/html/rfc6901)
* <span data-ttu-id="038d5-331">[Documentation JSON Patch](https://jsonpatch.com/).</span><span class="sxs-lookup"><span data-stu-id="038d5-331">[JSON Patch documentation](https://jsonpatch.com/).</span></span> <span data-ttu-id="038d5-332">Inclut des liens vers des ressources pour la création de documents JSON Patch.</span><span class="sxs-lookup"><span data-stu-id="038d5-332">Includes links to resources for creating JSON Patch documents.</span></span>
* [<span data-ttu-id="038d5-333">Code source ASP.NET Core JSON Patch</span><span class="sxs-lookup"><span data-stu-id="038d5-333">ASP.NET Core JSON Patch source code</span></span>](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end
