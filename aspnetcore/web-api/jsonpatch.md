---
<span data-ttu-id="4b1ee-101">titre : Auteur : Description : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-101">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4b1ee-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-102">'Blazor'</span></span>
- <span data-ttu-id="4b1ee-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-103">'Identity'</span></span>
- <span data-ttu-id="4b1ee-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="4b1ee-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-105">'Razor'</span></span>
- <span data-ttu-id="4b1ee-106">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-106">'SignalR' uid:</span></span> 

---

# <a name="jsonpatch-in-aspnet-core-web-api"></a><span data-ttu-id="4b1ee-107">JsonPatch dans l’API web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4b1ee-107">JsonPatch in ASP.NET Core web API</span></span>

<span data-ttu-id="4b1ee-108">Par [Tom Dykstra](https://github.com/tdykstra) et [Kirk Larkin](https://github.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="4b1ee-108">By [Tom Dykstra](https://github.com/tdykstra) and [Kirk Larkin](https://github.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4b1ee-109">Cet article explique comment gérer les demandes de correctifs JSON dans une API web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-109">This article explains how to handle JSON Patch requests in an ASP.NET Core web API.</span></span>

## <a name="package-installation"></a><span data-ttu-id="4b1ee-110">Installation de package</span><span class="sxs-lookup"><span data-stu-id="4b1ee-110">Package installation</span></span>

<span data-ttu-id="4b1ee-111">Pour activer la prise en charge des correctifs JSON dans votre application, procédez comme suit :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-111">To enable JSON Patch support in your app, complete the following steps:</span></span>

1. <span data-ttu-id="4b1ee-112">Installez le [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) package NuGet.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-112">Install the [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet package.</span></span>
1. <span data-ttu-id="4b1ee-113">Mettez à jour la `Startup.ConfigureServices` méthode du projet à appeler <xref:Microsoft.Extensions.DependencyInjection.NewtonsoftJsonMvcBuilderExtensions.AddNewtonsoftJson*> .</span><span class="sxs-lookup"><span data-stu-id="4b1ee-113">Update the project's `Startup.ConfigureServices` method to call <xref:Microsoft.Extensions.DependencyInjection.NewtonsoftJsonMvcBuilderExtensions.AddNewtonsoftJson*>.</span></span> <span data-ttu-id="4b1ee-114">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-114">For example:</span></span>

    ```csharp
    services
        .AddControllersWithViews()
        .AddNewtonsoftJson();
    ```

<span data-ttu-id="4b1ee-115">`AddNewtonsoftJson`est compatible avec les méthodes d’inscription du service MVC :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-115">`AddNewtonsoftJson` is compatible with the MVC service registration methods:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages*>
* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews*>
* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers*>

## <a name="json-patch-addnewtonsoftjson-and-systemtextjson"></a><span data-ttu-id="4b1ee-116">Correctif JSON, AddNewtonsoftJson et System. Text. JSON</span><span class="sxs-lookup"><span data-stu-id="4b1ee-116">JSON Patch, AddNewtonsoftJson, and System.Text.Json</span></span>

<span data-ttu-id="4b1ee-117">`AddNewtonsoftJson`remplace les `System.Text.Json` formateurs d’entrée et de sortie de base utilisés pour mettre en forme **tout** le contenu JSON.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-117">`AddNewtonsoftJson` replaces the `System.Text.Json`-based input and output formatters used for formatting **all** JSON content.</span></span> <span data-ttu-id="4b1ee-118">Pour ajouter la prise en charge du correctif JSON à l’aide de `Newtonsoft.Json` , tout en laissant les autres formateurs inchangés, mettez à jour la méthode du projet `Startup.ConfigureServices` comme suit :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-118">To add support for JSON Patch using `Newtonsoft.Json`, while leaving the other formatters unchanged, update the project's `Startup.ConfigureServices` method as follows:</span></span>

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet)]

<span data-ttu-id="4b1ee-119">Le code précédent requiert le `Microsoft.AspNetCore.Mvc.NewtonsoftJson` package et les `using` instructions suivantes :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-119">The preceding code requires the `Microsoft.AspNetCore.Mvc.NewtonsoftJson` package and the following `using` statements:</span></span>

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet1)]

## <a name="patch-http-request-method"></a><span data-ttu-id="4b1ee-120">Méthode de demande HTTP PATCH</span><span class="sxs-lookup"><span data-stu-id="4b1ee-120">PATCH HTTP request method</span></span>

<span data-ttu-id="4b1ee-121">Les méthodes PUT et [PATCH](https://tools.ietf.org/html/rfc5789) sont utilisées pour mettre à jour une ressource existante.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-121">The PUT and [PATCH](https://tools.ietf.org/html/rfc5789) methods are used to update an existing resource.</span></span> <span data-ttu-id="4b1ee-122">La différence est que PUT remplace la ressource complète, tandis que PATCH spécifie uniquement les modifications.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-122">The difference between them is that PUT replaces the entire resource, while PATCH specifies only the changes.</span></span>

## <a name="json-patch"></a><span data-ttu-id="4b1ee-123">JSON Patch</span><span class="sxs-lookup"><span data-stu-id="4b1ee-123">JSON Patch</span></span>

<span data-ttu-id="4b1ee-124">[JSON Patch](https://tools.ietf.org/html/rfc6902) est un format qui spécifie des mises à jour à appliquer à une ressource.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-124">[JSON Patch](https://tools.ietf.org/html/rfc6902) is a format for specifying updates to be applied to a resource.</span></span> <span data-ttu-id="4b1ee-125">Un document JSON Patch possède un tableau des *opérations*.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-125">A JSON Patch document has an array of *operations*.</span></span> <span data-ttu-id="4b1ee-126">Chaque opération identifie un type particulier de modification.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-126">Each operation identifies a particular type of change.</span></span> <span data-ttu-id="4b1ee-127">Les exemples de ces modifications incluent l’ajout d’un élément de tableau ou le remplacement d’une valeur de propriété.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-127">Examples of such changes include adding an array element or replacing a property value.</span></span>

<span data-ttu-id="4b1ee-128">Par exemple, les documents JSON suivants représentent une ressource, un document de correctif JSON pour la ressource et le résultat de l’application des correctifs.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-128">For example, the following JSON documents represent a resource, a JSON Patch document for the resource, and the result of applying the Patch operations.</span></span>

### <a name="resource-example"></a><span data-ttu-id="4b1ee-129">Exemple de ressource</span><span class="sxs-lookup"><span data-stu-id="4b1ee-129">Resource example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a><span data-ttu-id="4b1ee-130">Exemple de correctif JSON</span><span class="sxs-lookup"><span data-stu-id="4b1ee-130">JSON patch example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

<span data-ttu-id="4b1ee-131">Dans le code JSON précédent :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-131">In the preceding JSON:</span></span>

* <span data-ttu-id="4b1ee-132">La propriété `op` indique le type d’opération.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-132">The `op` property indicates the type of operation.</span></span>
* <span data-ttu-id="4b1ee-133">La propriété `path` indique l’élément à mettre à jour.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-133">The `path` property indicates the element to update.</span></span>
* <span data-ttu-id="4b1ee-134">La propriété `value` fournit la nouvelle valeur.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-134">The `value` property provides the new value.</span></span>

### <a name="resource-after-patch"></a><span data-ttu-id="4b1ee-135">Ressources après correction</span><span class="sxs-lookup"><span data-stu-id="4b1ee-135">Resource after patch</span></span>

<span data-ttu-id="4b1ee-136">Voici la ressource après l’application du document JSON Patch précédent :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-136">Here's the resource after applying the preceding JSON Patch document:</span></span>

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

<span data-ttu-id="4b1ee-137">Les modifications apportées en appliquant un document de correctif JSON à une ressource sont atomiques.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-137">The changes made by applying a JSON Patch document to a resource are atomic.</span></span> <span data-ttu-id="4b1ee-138">Si une opération de la liste échoue, aucune opération de la liste n’est appliquée.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-138">If any operation in the list fails, no operation in the list is applied.</span></span>

## <a name="path-syntax"></a><span data-ttu-id="4b1ee-139">Syntaxe du chemin</span><span class="sxs-lookup"><span data-stu-id="4b1ee-139">Path syntax</span></span>

<span data-ttu-id="4b1ee-140">Les différents niveaux de la propriété [path](https://tools.ietf.org/html/rfc6901) d’un objet de l’opération sont séparés par des barres obliques.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-140">The [path](https://tools.ietf.org/html/rfc6901) property of an operation object has slashes between levels.</span></span> <span data-ttu-id="4b1ee-141">Par exemple : `"/address/zipCode"`.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-141">For example, `"/address/zipCode"`.</span></span>

<span data-ttu-id="4b1ee-142">Les index de base zéro sont utilisés pour spécifier les éléments du tableau.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-142">Zero-based indexes are used to specify array elements.</span></span> <span data-ttu-id="4b1ee-143">Le premier élément du tableau `addresses` serait à `/addresses/0`.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-143">The first element of the `addresses` array would be at `/addresses/0`.</span></span> <span data-ttu-id="4b1ee-144">À `add` la fin d’un tableau, utilisez un trait d’Union ( `-` ) au lieu d’un numéro d’index : `/addresses/-` .</span><span class="sxs-lookup"><span data-stu-id="4b1ee-144">To `add` to the end of an array, use a hyphen (`-`) rather than an index number: `/addresses/-`.</span></span>

### <a name="operations"></a><span data-ttu-id="4b1ee-145">Opérations</span><span class="sxs-lookup"><span data-stu-id="4b1ee-145">Operations</span></span>

<span data-ttu-id="4b1ee-146">Le tableau suivant mentionne les opérations prises en charge telles qu’elles sont définies dans la [spécification JSON Patch](https://tools.ietf.org/html/rfc6902) :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-146">The following table shows supported operations as defined in the [JSON Patch specification](https://tools.ietf.org/html/rfc6902):</span></span>

|<span data-ttu-id="4b1ee-147">Opération</span><span class="sxs-lookup"><span data-stu-id="4b1ee-147">Operation</span></span>  | <span data-ttu-id="4b1ee-148">Notes</span><span class="sxs-lookup"><span data-stu-id="4b1ee-148">Notes</span></span> |
|---
<span data-ttu-id="4b1ee-149">titre : Auteur : Description : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-149">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4b1ee-150">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-150">'Blazor'</span></span>
- <span data-ttu-id="4b1ee-151">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-151">'Identity'</span></span>
- <span data-ttu-id="4b1ee-152">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-152">'Let's Encrypt'</span></span>
- <span data-ttu-id="4b1ee-153">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-153">'Razor'</span></span>
- <span data-ttu-id="4b1ee-154">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-154">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4b1ee-155">titre : Auteur : Description : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-155">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4b1ee-156">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-156">'Blazor'</span></span>
- <span data-ttu-id="4b1ee-157">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-157">'Identity'</span></span>
- <span data-ttu-id="4b1ee-158">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-158">'Let's Encrypt'</span></span>
- <span data-ttu-id="4b1ee-159">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-159">'Razor'</span></span>
- <span data-ttu-id="4b1ee-160">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-160">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4b1ee-161">titre : Auteur : Description : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-161">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4b1ee-162">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-162">'Blazor'</span></span>
- <span data-ttu-id="4b1ee-163">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-163">'Identity'</span></span>
- <span data-ttu-id="4b1ee-164">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-164">'Let's Encrypt'</span></span>
- <span data-ttu-id="4b1ee-165">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-165">'Razor'</span></span>
- <span data-ttu-id="4b1ee-166">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-166">'SignalR' uid:</span></span> 

<span data-ttu-id="4b1ee-167">------|---
titre : Auteur : Description : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-167">------|---
title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4b1ee-168">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-168">'Blazor'</span></span>
- <span data-ttu-id="4b1ee-169">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-169">'Identity'</span></span>
- <span data-ttu-id="4b1ee-170">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-170">'Let's Encrypt'</span></span>
- <span data-ttu-id="4b1ee-171">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-171">'Razor'</span></span>
- <span data-ttu-id="4b1ee-172">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-172">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4b1ee-173">titre : Auteur : Description : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-173">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4b1ee-174">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-174">'Blazor'</span></span>
- <span data-ttu-id="4b1ee-175">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-175">'Identity'</span></span>
- <span data-ttu-id="4b1ee-176">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-176">'Let's Encrypt'</span></span>
- <span data-ttu-id="4b1ee-177">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-177">'Razor'</span></span>
- <span data-ttu-id="4b1ee-178">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-178">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4b1ee-179">titre : Auteur : Description : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-179">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4b1ee-180">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-180">'Blazor'</span></span>
- <span data-ttu-id="4b1ee-181">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-181">'Identity'</span></span>
- <span data-ttu-id="4b1ee-182">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-182">'Let's Encrypt'</span></span>
- <span data-ttu-id="4b1ee-183">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-183">'Razor'</span></span>
- <span data-ttu-id="4b1ee-184">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-184">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4b1ee-185">titre : Auteur : Description : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-185">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4b1ee-186">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-186">'Blazor'</span></span>
- <span data-ttu-id="4b1ee-187">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-187">'Identity'</span></span>
- <span data-ttu-id="4b1ee-188">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-188">'Let's Encrypt'</span></span>
- <span data-ttu-id="4b1ee-189">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-189">'Razor'</span></span>
- <span data-ttu-id="4b1ee-190">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-190">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4b1ee-191">titre : Auteur : Description : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-191">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4b1ee-192">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-192">'Blazor'</span></span>
- <span data-ttu-id="4b1ee-193">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-193">'Identity'</span></span>
- <span data-ttu-id="4b1ee-194">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-194">'Let's Encrypt'</span></span>
- <span data-ttu-id="4b1ee-195">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-195">'Razor'</span></span>
- <span data-ttu-id="4b1ee-196">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-196">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4b1ee-197">titre : Auteur : Description : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-197">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4b1ee-198">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-198">'Blazor'</span></span>
- <span data-ttu-id="4b1ee-199">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-199">'Identity'</span></span>
- <span data-ttu-id="4b1ee-200">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-200">'Let's Encrypt'</span></span>
- <span data-ttu-id="4b1ee-201">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-201">'Razor'</span></span>
- <span data-ttu-id="4b1ee-202">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-202">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4b1ee-203">titre : Auteur : Description : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-203">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4b1ee-204">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-204">'Blazor'</span></span>
- <span data-ttu-id="4b1ee-205">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-205">'Identity'</span></span>
- <span data-ttu-id="4b1ee-206">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-206">'Let's Encrypt'</span></span>
- <span data-ttu-id="4b1ee-207">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-207">'Razor'</span></span>
- <span data-ttu-id="4b1ee-208">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-208">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4b1ee-209">titre : Auteur : Description : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-209">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4b1ee-210">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-210">'Blazor'</span></span>
- <span data-ttu-id="4b1ee-211">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-211">'Identity'</span></span>
- <span data-ttu-id="4b1ee-212">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-212">'Let's Encrypt'</span></span>
- <span data-ttu-id="4b1ee-213">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-213">'Razor'</span></span>
- <span data-ttu-id="4b1ee-214">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-214">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4b1ee-215">titre : Auteur : Description : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-215">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4b1ee-216">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-216">'Blazor'</span></span>
- <span data-ttu-id="4b1ee-217">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-217">'Identity'</span></span>
- <span data-ttu-id="4b1ee-218">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-218">'Let's Encrypt'</span></span>
- <span data-ttu-id="4b1ee-219">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-219">'Razor'</span></span>
- <span data-ttu-id="4b1ee-220">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-220">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4b1ee-221">titre : Auteur : Description : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-221">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4b1ee-222">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-222">'Blazor'</span></span>
- <span data-ttu-id="4b1ee-223">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-223">'Identity'</span></span>
- <span data-ttu-id="4b1ee-224">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-224">'Let's Encrypt'</span></span>
- <span data-ttu-id="4b1ee-225">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-225">'Razor'</span></span>
- <span data-ttu-id="4b1ee-226">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-226">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4b1ee-227">titre : Auteur : Description : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-227">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4b1ee-228">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-228">'Blazor'</span></span>
- <span data-ttu-id="4b1ee-229">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-229">'Identity'</span></span>
- <span data-ttu-id="4b1ee-230">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-230">'Let's Encrypt'</span></span>
- <span data-ttu-id="4b1ee-231">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-231">'Razor'</span></span>
- <span data-ttu-id="4b1ee-232">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-232">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4b1ee-233">titre : Auteur : Description : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-233">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4b1ee-234">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-234">'Blazor'</span></span>
- <span data-ttu-id="4b1ee-235">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-235">'Identity'</span></span>
- <span data-ttu-id="4b1ee-236">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-236">'Let's Encrypt'</span></span>
- <span data-ttu-id="4b1ee-237">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-237">'Razor'</span></span>
- <span data-ttu-id="4b1ee-238">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-238">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4b1ee-239">titre : Auteur : Description : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-239">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4b1ee-240">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-240">'Blazor'</span></span>
- <span data-ttu-id="4b1ee-241">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-241">'Identity'</span></span>
- <span data-ttu-id="4b1ee-242">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-242">'Let's Encrypt'</span></span>
- <span data-ttu-id="4b1ee-243">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-243">'Razor'</span></span>
- <span data-ttu-id="4b1ee-244">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-244">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4b1ee-245">titre : Auteur : Description : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-245">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4b1ee-246">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-246">'Blazor'</span></span>
- <span data-ttu-id="4b1ee-247">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-247">'Identity'</span></span>
- <span data-ttu-id="4b1ee-248">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-248">'Let's Encrypt'</span></span>
- <span data-ttu-id="4b1ee-249">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-249">'Razor'</span></span>
- <span data-ttu-id="4b1ee-250">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-250">'SignalR' uid:</span></span> 

<span data-ttu-id="4b1ee-251">----------------| | `add`     | Ajoutez une propriété ou un élément de tableau.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-251">----------------| | `add`     | Add a property or array element.</span></span> <span data-ttu-id="4b1ee-252">Pour la propriété existante : valeur définie. | | `remove`  | Supprimez une propriété ou un élément de tableau.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-252">For existing property: set value.| | `remove`  | Remove a property or array element.</span></span> <span data-ttu-id="4b1ee-253">| | `replace` | Identique à `remove` suivi `add` du même emplacement.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-253">| | `replace` | Same as `remove` followed by `add` at same location.</span></span> <span data-ttu-id="4b1ee-254">| | `move`    | Identique à la `remove` source, puis `add` à la destination à l’aide de la valeur de la source.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-254">| | `move`    | Same as `remove` from source followed by `add` to destination using value from source.</span></span> <span data-ttu-id="4b1ee-255">| | `copy`    | Identique `add` à la destination à l’aide de la valeur de la source.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-255">| | `copy`    | Same as `add` to destination using value from source.</span></span> <span data-ttu-id="4b1ee-256">| | `test`    | Retourne le code d’état de réussite si la valeur est égale à `path` = fourni `value` . |</span><span class="sxs-lookup"><span data-stu-id="4b1ee-256">| | `test`    | Return success status code if value at `path` = provided `value`.|</span></span>

## <a name="json-patch-in-aspnet-core"></a><span data-ttu-id="4b1ee-257">Correctif JSON dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4b1ee-257">JSON Patch in ASP.NET Core</span></span>

<span data-ttu-id="4b1ee-258">L’implémentation ASP.NET Core de JSON Patch est fournie dans le package NuGet [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/).</span><span class="sxs-lookup"><span data-stu-id="4b1ee-258">The ASP.NET Core implementation of JSON Patch is provided in the [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet package.</span></span>

## <a name="action-method-code"></a><span data-ttu-id="4b1ee-259">Code de méthode d’action</span><span class="sxs-lookup"><span data-stu-id="4b1ee-259">Action method code</span></span>

<span data-ttu-id="4b1ee-260">Dans un contrôleur d’API, une méthode d’action pour JSON Patch :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-260">In an API controller, an action method for JSON Patch:</span></span>

* <span data-ttu-id="4b1ee-261">est annotée avec l’attribut `HttpPatch` ;</span><span class="sxs-lookup"><span data-stu-id="4b1ee-261">Is annotated with the `HttpPatch` attribute.</span></span>
* <span data-ttu-id="4b1ee-262">Accepte un `JsonPatchDocument<T>` , généralement avec `[FromBody]` .</span><span class="sxs-lookup"><span data-stu-id="4b1ee-262">Accepts a `JsonPatchDocument<T>`, typically with `[FromBody]`.</span></span>
* <span data-ttu-id="4b1ee-263">appelle `ApplyTo` sur le document de correctif pour appliquer les modifications.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-263">Calls `ApplyTo` on the patch document to apply the changes.</span></span>

<span data-ttu-id="4b1ee-264">Voici un exemple :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-264">Here's an example:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

<span data-ttu-id="4b1ee-265">Ce code de l’exemple d’application fonctionne avec le `Customer` modèle suivant :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-265">This code from the sample app works with the following `Customer` model:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

<span data-ttu-id="4b1ee-266">L’exemple de méthode d’action :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-266">The sample action method:</span></span>

* <span data-ttu-id="4b1ee-267">Construit un objet `Customer`.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-267">Constructs a `Customer`.</span></span>
* <span data-ttu-id="4b1ee-268">Applique le correctif.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-268">Applies the patch.</span></span>
* <span data-ttu-id="4b1ee-269">Retourne le résultat dans le corps de la réponse.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-269">Returns the result in the body of the response.</span></span>

<span data-ttu-id="4b1ee-270">Dans une application réelle, le code récupérerait les données dans un magasin tel qu’une base de données et mettrait à jour la base de données après avoir appliqué le correctif.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-270">In a real app, the code would retrieve the data from a store such as a database and update the database after applying the patch.</span></span>

### <a name="model-state"></a><span data-ttu-id="4b1ee-271">État du modèle</span><span class="sxs-lookup"><span data-stu-id="4b1ee-271">Model state</span></span>

<span data-ttu-id="4b1ee-272">L’exemple de méthode d’action précédent appelle une surcharge de `ApplyTo` qui prend l’état du modèle comme un de ses paramètres.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-272">The preceding action method example calls an overload of `ApplyTo` that takes model state as one of its parameters.</span></span> <span data-ttu-id="4b1ee-273">Avec cette option, vous pouvez obtenir des messages d’erreur dans les réponses.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-273">With this option, you can get error messages in responses.</span></span> <span data-ttu-id="4b1ee-274">L’exemple suivant montre le corps d’une demande-réponse incorrecte 400 pour une opération `test` :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-274">The following example shows the body of a 400 Bad Request response for a `test` operation:</span></span>

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a><span data-ttu-id="4b1ee-275">Objets dynamiques</span><span class="sxs-lookup"><span data-stu-id="4b1ee-275">Dynamic objects</span></span>

<span data-ttu-id="4b1ee-276">L’exemple de méthode d’action suivant montre comment appliquer un correctif à un objet dynamique :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-276">The following action method example shows how to apply a patch to a dynamic object:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a><span data-ttu-id="4b1ee-277">L’opération d’ajout</span><span class="sxs-lookup"><span data-stu-id="4b1ee-277">The add operation</span></span>

* <span data-ttu-id="4b1ee-278">Si `path` pointe vers un élément du tableau : insère un nouvel élément avant celui spécifié par `path`.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-278">If `path` points to an array element: inserts new element before the one specified by `path`.</span></span>
* <span data-ttu-id="4b1ee-279">Si `path` pointe vers une propriété : définit la valeur de propriété.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-279">If `path` points to a property: sets the property value.</span></span>
* <span data-ttu-id="4b1ee-280">Si `path` pointe vers un emplacement qui n’existe pas :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-280">If `path` points to a nonexistent location:</span></span>
  * <span data-ttu-id="4b1ee-281">Si la ressource à corriger est un objet dynamique : ajoute une propriété.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-281">If the resource to patch is a dynamic object: adds a property.</span></span>
  * <span data-ttu-id="4b1ee-282">Si la ressource à corriger est un objet statique : la demande échoue.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-282">If the resource to patch is a static object: the request fails.</span></span>

<span data-ttu-id="4b1ee-283">L’exemple de document de correctif suivant définit la valeur de `CustomerName` et ajoute un objet `Order` à la fin du tableau `Orders`.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-283">The following sample patch document sets the value of `CustomerName` and adds an `Order` object to the end of the `Orders` array.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a><span data-ttu-id="4b1ee-284">L’opération de suppression</span><span class="sxs-lookup"><span data-stu-id="4b1ee-284">The remove operation</span></span>

* <span data-ttu-id="4b1ee-285">Si `path` pointe vers un élément du tableau : supprime l’élément.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-285">If `path` points to an array element: removes the element.</span></span>
* <span data-ttu-id="4b1ee-286">Si `path` pointe vers une propriété :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-286">If `path` points to a property:</span></span>
  * <span data-ttu-id="4b1ee-287">Si la ressource à corriger est un objet dynamique : supprime la propriété.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-287">If resource to patch is a dynamic object: removes the property.</span></span>
  * <span data-ttu-id="4b1ee-288">Si la ressource à corriger est un objet statique :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-288">If resource to patch is a static object:</span></span>
    * <span data-ttu-id="4b1ee-289">Si la propriété est Nullable : met la valeur sur Null.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-289">If the property is nullable: sets it to null.</span></span>
    * <span data-ttu-id="4b1ee-290">Si la propriété n’est pas Nullable : met la valeur sur `default<T>`.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-290">If the property is non-nullable, sets it to `default<T>`.</span></span>

<span data-ttu-id="4b1ee-291">L’exemple de document de correctif suivant définit la `CustomerName` valeur null et supprime `Orders[0]` :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-291">The following sample patch document sets `CustomerName` to null and deletes `Orders[0]`:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a><span data-ttu-id="4b1ee-292">L’opération de remplacement</span><span class="sxs-lookup"><span data-stu-id="4b1ee-292">The replace operation</span></span>

<span data-ttu-id="4b1ee-293">Cette opération est fonctionnellement identique à `remove` suivi de `add`.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-293">This operation is functionally the same as a `remove` followed by an `add`.</span></span>

<span data-ttu-id="4b1ee-294">L’exemple de document de correctif suivant définit la valeur de `CustomerName` et remplace `Orders[0]` par un nouvel `Order` objet :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-294">The following sample patch document sets the value of `CustomerName` and replaces `Orders[0]`with a new `Order` object:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a><span data-ttu-id="4b1ee-295">L’opération de déplacement</span><span class="sxs-lookup"><span data-stu-id="4b1ee-295">The move operation</span></span>

* <span data-ttu-id="4b1ee-296">Si `path` pointe vers un élément du tableau : copie l’élément `from` à l’emplacement de l’élément `path`, puis exécute une opération `remove` sur l’élément `from`.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-296">If `path` points to an array element: copies `from` element to location of `path` element, then runs a `remove` operation on the `from` element.</span></span>
* <span data-ttu-id="4b1ee-297">Si `path` pointe vers une propriété : copie la valeur de la propriété `from` vers la propriété `path`, puis exécute une opération `remove` sur la propriété `from`.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-297">If `path` points to a property: copies value of `from` property to `path` property, then runs a `remove` operation on the `from` property.</span></span>
* <span data-ttu-id="4b1ee-298">Si `path` pointe vers une propriété qui n’existe pas :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-298">If `path` points to a nonexistent property:</span></span>
  * <span data-ttu-id="4b1ee-299">Si la ressource à corriger est un objet statique : la demande échoue.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-299">If the resource to patch is a static object: the request fails.</span></span>
  * <span data-ttu-id="4b1ee-300">Si la ressource à corriger est un objet dynamique : copie la propriété `from` à un emplacement indiqué par `path`, puis exécute une opération `remove` sur la propriété `from`.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-300">If the resource to patch is a dynamic object: copies `from` property to location indicated by `path`, then runs a `remove` operation on the `from` property.</span></span>

<span data-ttu-id="4b1ee-301">L’exemple de document de correctif suivant :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-301">The following sample patch document:</span></span>

* <span data-ttu-id="4b1ee-302">Copie la valeur de `Orders[0].OrderName` vers `CustomerName`.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-302">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="4b1ee-303">Met `Orders[0].OrderName` sur la valeur Null.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-303">Sets `Orders[0].OrderName` to null.</span></span>
* <span data-ttu-id="4b1ee-304">Déplace `Orders[1]` avant `Orders[0]`.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-304">Moves `Orders[1]` to before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a><span data-ttu-id="4b1ee-305">L’opération de copie</span><span class="sxs-lookup"><span data-stu-id="4b1ee-305">The copy operation</span></span>

<span data-ttu-id="4b1ee-306">Cette opération est fonctionnellement identique à une opération `move` sans l’étape `remove` finale.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-306">This operation is functionally the same as a `move` operation without the final `remove` step.</span></span>

<span data-ttu-id="4b1ee-307">L’exemple de document de correctif suivant :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-307">The following sample patch document:</span></span>

* <span data-ttu-id="4b1ee-308">Copie la valeur de `Orders[0].OrderName` vers `CustomerName`.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-308">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="4b1ee-309">Insère une copie de `Orders[1]` avant `Orders[0]`.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-309">Inserts a copy of `Orders[1]` before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a><span data-ttu-id="4b1ee-310">L’opération de test</span><span class="sxs-lookup"><span data-stu-id="4b1ee-310">The test operation</span></span>

<span data-ttu-id="4b1ee-311">Si la valeur à l’emplacement indiqué par `path` est différente de la valeur fournie dans `value`, la demande échoue.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-311">If the value at the location indicated by `path` is different from the value provided in `value`, the request fails.</span></span> <span data-ttu-id="4b1ee-312">Dans ce cas, toute la demande PATCH échoue, même si toutes les autres opérations dans le document de correctif réussiraient autrement.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-312">In that case, the whole PATCH request fails even if all other operations in the patch document would otherwise succeed.</span></span>

<span data-ttu-id="4b1ee-313">L’opération `test` est généralement utilisée pour empêcher une mise à jour lorsqu’il y a un conflit d’accès concurrentiel.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-313">The `test` operation is commonly used to prevent an update when there's a concurrency conflict.</span></span>

<span data-ttu-id="4b1ee-314">L’exemple de document de correctif suivant n’a aucun effet si la valeur initiale de `CustomerName` est « John », car le test échoue :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-314">The following sample patch document has no effect if the initial value of `CustomerName` is "John", because the test fails:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a><span data-ttu-id="4b1ee-315">Obtenir le code</span><span class="sxs-lookup"><span data-stu-id="4b1ee-315">Get the code</span></span>

<span data-ttu-id="4b1ee-316">[Affichez ou téléchargez l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples).</span><span class="sxs-lookup"><span data-stu-id="4b1ee-316">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples).</span></span> <span data-ttu-id="4b1ee-317">([Procédure de téléchargement](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="4b1ee-317">([How to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="4b1ee-318">Pour tester l’exemple, exécutez l’application et envoyez des demandes HTTP avec les paramètres suivants :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-318">To test the sample, run the app and send HTTP requests with the following settings:</span></span>

* <span data-ttu-id="4b1ee-319">URL : `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span><span class="sxs-lookup"><span data-stu-id="4b1ee-319">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span></span>
* <span data-ttu-id="4b1ee-320">Méthode HTTP : `PATCH`</span><span class="sxs-lookup"><span data-stu-id="4b1ee-320">HTTP method: `PATCH`</span></span>
* <span data-ttu-id="4b1ee-321">En-tête : `Content-Type: application/json-patch+json`</span><span class="sxs-lookup"><span data-stu-id="4b1ee-321">Header: `Content-Type: application/json-patch+json`</span></span>
* <span data-ttu-id="4b1ee-322">Corps : copiez et collez l’un des exemples de document de correctif JSON à partir du dossier de projet *JSON* .</span><span class="sxs-lookup"><span data-stu-id="4b1ee-322">Body: Copy and paste one of the JSON patch document samples from the *JSON* project folder.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4b1ee-323">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="4b1ee-323">Additional resources</span></span>

* [<span data-ttu-id="4b1ee-324">Spécification de méthode PATCH IETF RFC 5789 PATCH</span><span class="sxs-lookup"><span data-stu-id="4b1ee-324">IETF RFC 5789 PATCH method specification</span></span>](https://tools.ietf.org/html/rfc5789)
* [<span data-ttu-id="4b1ee-325">Spécification JSON Patch IETF RFC 6902</span><span class="sxs-lookup"><span data-stu-id="4b1ee-325">IETF RFC 6902 JSON Patch specification</span></span>](https://tools.ietf.org/html/rfc6902)
* [<span data-ttu-id="4b1ee-326">Spécification de format de chemin JSON Patch IETF RFC 6901</span><span class="sxs-lookup"><span data-stu-id="4b1ee-326">IETF RFC 6901 JSON Patch path format spec</span></span>](https://tools.ietf.org/html/rfc6901)
* <span data-ttu-id="4b1ee-327">[Documentation JSON Patch](https://jsonpatch.com/).</span><span class="sxs-lookup"><span data-stu-id="4b1ee-327">[JSON Patch documentation](https://jsonpatch.com/).</span></span> <span data-ttu-id="4b1ee-328">Inclut des liens vers des ressources pour la création de documents JSON Patch.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-328">Includes links to resources for creating JSON Patch documents.</span></span>
* [<span data-ttu-id="4b1ee-329">Code source ASP.NET Core JSON Patch</span><span class="sxs-lookup"><span data-stu-id="4b1ee-329">ASP.NET Core JSON Patch source code</span></span>](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4b1ee-330">Cet article explique comment gérer les demandes de correctifs JSON dans une API web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-330">This article explains how to handle JSON Patch requests in an ASP.NET Core web API.</span></span>

## <a name="patch-http-request-method"></a><span data-ttu-id="4b1ee-331">Méthode de demande HTTP PATCH</span><span class="sxs-lookup"><span data-stu-id="4b1ee-331">PATCH HTTP request method</span></span>

<span data-ttu-id="4b1ee-332">Les méthodes PUT et [PATCH](https://tools.ietf.org/html/rfc5789) sont utilisées pour mettre à jour une ressource existante.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-332">The PUT and [PATCH](https://tools.ietf.org/html/rfc5789) methods are used to update an existing resource.</span></span> <span data-ttu-id="4b1ee-333">La différence est que PUT remplace la ressource complète, tandis que PATCH spécifie uniquement les modifications.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-333">The difference between them is that PUT replaces the entire resource, while PATCH specifies only the changes.</span></span>

## <a name="json-patch"></a><span data-ttu-id="4b1ee-334">JSON Patch</span><span class="sxs-lookup"><span data-stu-id="4b1ee-334">JSON Patch</span></span>

<span data-ttu-id="4b1ee-335">[JSON Patch](https://tools.ietf.org/html/rfc6902) est un format qui spécifie des mises à jour à appliquer à une ressource.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-335">[JSON Patch](https://tools.ietf.org/html/rfc6902) is a format for specifying updates to be applied to a resource.</span></span> <span data-ttu-id="4b1ee-336">Un document JSON Patch possède un tableau des *opérations*.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-336">A JSON Patch document has an array of *operations*.</span></span> <span data-ttu-id="4b1ee-337">Chaque opération identifie un type particulier de modification, tel qu’ajouter un élément de tableau ou remplacer une valeur de propriété.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-337">Each operation identifies a particular type of change, such as add an array element or replace a property value.</span></span>

<span data-ttu-id="4b1ee-338">Par exemple, les documents JSON suivants représentent une ressource, un document de correctif JSON pour la ressource et le résultat de l’application de l’application des correctifs.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-338">For example, the following JSON documents represent a resource, a JSON patch document for the resource, and the result of applying the patch operations.</span></span>

### <a name="resource-example"></a><span data-ttu-id="4b1ee-339">Exemple de ressource</span><span class="sxs-lookup"><span data-stu-id="4b1ee-339">Resource example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a><span data-ttu-id="4b1ee-340">Exemple de correctif JSON</span><span class="sxs-lookup"><span data-stu-id="4b1ee-340">JSON patch example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

<span data-ttu-id="4b1ee-341">Dans le code JSON précédent :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-341">In the preceding JSON:</span></span>

* <span data-ttu-id="4b1ee-342">La propriété `op` indique le type d’opération.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-342">The `op` property indicates the type of operation.</span></span>
* <span data-ttu-id="4b1ee-343">La propriété `path` indique l’élément à mettre à jour.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-343">The `path` property indicates the element to update.</span></span>
* <span data-ttu-id="4b1ee-344">La propriété `value` fournit la nouvelle valeur.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-344">The `value` property provides the new value.</span></span>

### <a name="resource-after-patch"></a><span data-ttu-id="4b1ee-345">Ressources après correction</span><span class="sxs-lookup"><span data-stu-id="4b1ee-345">Resource after patch</span></span>

<span data-ttu-id="4b1ee-346">Voici la ressource après l’application du document JSON Patch précédent :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-346">Here's the resource after applying the preceding JSON Patch document:</span></span>

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

<span data-ttu-id="4b1ee-347">Les modifications apportées en appliquant un document JSON Patch à une ressource sont atomiques : si une opération de la liste échoue, aucune opération de la liste n’est appliquée.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-347">The changes made by applying a JSON Patch document to a resource are atomic: if any operation in the list fails, no operation in the list is applied.</span></span>

## <a name="path-syntax"></a><span data-ttu-id="4b1ee-348">Syntaxe du chemin</span><span class="sxs-lookup"><span data-stu-id="4b1ee-348">Path syntax</span></span>

<span data-ttu-id="4b1ee-349">Les différents niveaux de la propriété [path](https://tools.ietf.org/html/rfc6901) d’un objet de l’opération sont séparés par des barres obliques.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-349">The [path](https://tools.ietf.org/html/rfc6901) property of an operation object has slashes between levels.</span></span> <span data-ttu-id="4b1ee-350">Par exemple : `"/address/zipCode"`.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-350">For example, `"/address/zipCode"`.</span></span>

<span data-ttu-id="4b1ee-351">Les index de base zéro sont utilisés pour spécifier les éléments du tableau.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-351">Zero-based indexes are used to specify array elements.</span></span> <span data-ttu-id="4b1ee-352">Le premier élément du tableau `addresses` serait à `/addresses/0`.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-352">The first element of the `addresses` array would be at `/addresses/0`.</span></span> <span data-ttu-id="4b1ee-353">Pour `add` à la fin d’un tableau, utilisez un trait d’union (-) plutôt qu’un numéro d’index : `/addresses/-`.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-353">To `add` to the end of an array, use a hyphen (-) rather than an index number: `/addresses/-`.</span></span>

### <a name="operations"></a><span data-ttu-id="4b1ee-354">Opérations</span><span class="sxs-lookup"><span data-stu-id="4b1ee-354">Operations</span></span>

<span data-ttu-id="4b1ee-355">Le tableau suivant mentionne les opérations prises en charge telles qu’elles sont définies dans la [spécification JSON Patch](https://tools.ietf.org/html/rfc6902) :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-355">The following table shows supported operations as defined in the [JSON Patch specification](https://tools.ietf.org/html/rfc6902):</span></span>

|<span data-ttu-id="4b1ee-356">Opération</span><span class="sxs-lookup"><span data-stu-id="4b1ee-356">Operation</span></span>  | <span data-ttu-id="4b1ee-357">Notes</span><span class="sxs-lookup"><span data-stu-id="4b1ee-357">Notes</span></span> |
|---
<span data-ttu-id="4b1ee-358">titre : Auteur : Description : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-358">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4b1ee-359">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-359">'Blazor'</span></span>
- <span data-ttu-id="4b1ee-360">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-360">'Identity'</span></span>
- <span data-ttu-id="4b1ee-361">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-361">'Let's Encrypt'</span></span>
- <span data-ttu-id="4b1ee-362">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-362">'Razor'</span></span>
- <span data-ttu-id="4b1ee-363">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-363">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4b1ee-364">titre : Auteur : Description : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-364">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4b1ee-365">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-365">'Blazor'</span></span>
- <span data-ttu-id="4b1ee-366">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-366">'Identity'</span></span>
- <span data-ttu-id="4b1ee-367">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-367">'Let's Encrypt'</span></span>
- <span data-ttu-id="4b1ee-368">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-368">'Razor'</span></span>
- <span data-ttu-id="4b1ee-369">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-369">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4b1ee-370">titre : Auteur : Description : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-370">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4b1ee-371">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-371">'Blazor'</span></span>
- <span data-ttu-id="4b1ee-372">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-372">'Identity'</span></span>
- <span data-ttu-id="4b1ee-373">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-373">'Let's Encrypt'</span></span>
- <span data-ttu-id="4b1ee-374">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-374">'Razor'</span></span>
- <span data-ttu-id="4b1ee-375">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-375">'SignalR' uid:</span></span> 

<span data-ttu-id="4b1ee-376">------|---
titre : Auteur : Description : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-376">------|---
title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4b1ee-377">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-377">'Blazor'</span></span>
- <span data-ttu-id="4b1ee-378">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-378">'Identity'</span></span>
- <span data-ttu-id="4b1ee-379">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-379">'Let's Encrypt'</span></span>
- <span data-ttu-id="4b1ee-380">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-380">'Razor'</span></span>
- <span data-ttu-id="4b1ee-381">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-381">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4b1ee-382">titre : Auteur : Description : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-382">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4b1ee-383">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-383">'Blazor'</span></span>
- <span data-ttu-id="4b1ee-384">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-384">'Identity'</span></span>
- <span data-ttu-id="4b1ee-385">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-385">'Let's Encrypt'</span></span>
- <span data-ttu-id="4b1ee-386">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-386">'Razor'</span></span>
- <span data-ttu-id="4b1ee-387">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-387">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4b1ee-388">titre : Auteur : Description : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-388">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4b1ee-389">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-389">'Blazor'</span></span>
- <span data-ttu-id="4b1ee-390">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-390">'Identity'</span></span>
- <span data-ttu-id="4b1ee-391">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-391">'Let's Encrypt'</span></span>
- <span data-ttu-id="4b1ee-392">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-392">'Razor'</span></span>
- <span data-ttu-id="4b1ee-393">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-393">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4b1ee-394">titre : Auteur : Description : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-394">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4b1ee-395">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-395">'Blazor'</span></span>
- <span data-ttu-id="4b1ee-396">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-396">'Identity'</span></span>
- <span data-ttu-id="4b1ee-397">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-397">'Let's Encrypt'</span></span>
- <span data-ttu-id="4b1ee-398">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-398">'Razor'</span></span>
- <span data-ttu-id="4b1ee-399">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-399">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4b1ee-400">titre : Auteur : Description : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-400">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4b1ee-401">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-401">'Blazor'</span></span>
- <span data-ttu-id="4b1ee-402">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-402">'Identity'</span></span>
- <span data-ttu-id="4b1ee-403">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-403">'Let's Encrypt'</span></span>
- <span data-ttu-id="4b1ee-404">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-404">'Razor'</span></span>
- <span data-ttu-id="4b1ee-405">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-405">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4b1ee-406">titre : Auteur : Description : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-406">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4b1ee-407">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-407">'Blazor'</span></span>
- <span data-ttu-id="4b1ee-408">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-408">'Identity'</span></span>
- <span data-ttu-id="4b1ee-409">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-409">'Let's Encrypt'</span></span>
- <span data-ttu-id="4b1ee-410">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-410">'Razor'</span></span>
- <span data-ttu-id="4b1ee-411">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-411">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4b1ee-412">titre : Auteur : Description : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-412">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4b1ee-413">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-413">'Blazor'</span></span>
- <span data-ttu-id="4b1ee-414">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-414">'Identity'</span></span>
- <span data-ttu-id="4b1ee-415">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-415">'Let's Encrypt'</span></span>
- <span data-ttu-id="4b1ee-416">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-416">'Razor'</span></span>
- <span data-ttu-id="4b1ee-417">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-417">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4b1ee-418">titre : Auteur : Description : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-418">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4b1ee-419">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-419">'Blazor'</span></span>
- <span data-ttu-id="4b1ee-420">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-420">'Identity'</span></span>
- <span data-ttu-id="4b1ee-421">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-421">'Let's Encrypt'</span></span>
- <span data-ttu-id="4b1ee-422">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-422">'Razor'</span></span>
- <span data-ttu-id="4b1ee-423">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-423">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4b1ee-424">titre : Auteur : Description : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-424">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4b1ee-425">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-425">'Blazor'</span></span>
- <span data-ttu-id="4b1ee-426">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-426">'Identity'</span></span>
- <span data-ttu-id="4b1ee-427">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-427">'Let's Encrypt'</span></span>
- <span data-ttu-id="4b1ee-428">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-428">'Razor'</span></span>
- <span data-ttu-id="4b1ee-429">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-429">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4b1ee-430">titre : Auteur : Description : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-430">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4b1ee-431">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-431">'Blazor'</span></span>
- <span data-ttu-id="4b1ee-432">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-432">'Identity'</span></span>
- <span data-ttu-id="4b1ee-433">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-433">'Let's Encrypt'</span></span>
- <span data-ttu-id="4b1ee-434">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-434">'Razor'</span></span>
- <span data-ttu-id="4b1ee-435">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-435">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4b1ee-436">titre : Auteur : Description : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-436">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4b1ee-437">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-437">'Blazor'</span></span>
- <span data-ttu-id="4b1ee-438">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-438">'Identity'</span></span>
- <span data-ttu-id="4b1ee-439">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-439">'Let's Encrypt'</span></span>
- <span data-ttu-id="4b1ee-440">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-440">'Razor'</span></span>
- <span data-ttu-id="4b1ee-441">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-441">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4b1ee-442">titre : Auteur : Description : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-442">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4b1ee-443">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-443">'Blazor'</span></span>
- <span data-ttu-id="4b1ee-444">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-444">'Identity'</span></span>
- <span data-ttu-id="4b1ee-445">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-445">'Let's Encrypt'</span></span>
- <span data-ttu-id="4b1ee-446">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-446">'Razor'</span></span>
- <span data-ttu-id="4b1ee-447">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-447">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4b1ee-448">titre : Auteur : Description : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-448">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4b1ee-449">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-449">'Blazor'</span></span>
- <span data-ttu-id="4b1ee-450">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-450">'Identity'</span></span>
- <span data-ttu-id="4b1ee-451">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-451">'Let's Encrypt'</span></span>
- <span data-ttu-id="4b1ee-452">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-452">'Razor'</span></span>
- <span data-ttu-id="4b1ee-453">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-453">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4b1ee-454">titre : Auteur : Description : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-454">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4b1ee-455">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-455">'Blazor'</span></span>
- <span data-ttu-id="4b1ee-456">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-456">'Identity'</span></span>
- <span data-ttu-id="4b1ee-457">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-457">'Let's Encrypt'</span></span>
- <span data-ttu-id="4b1ee-458">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4b1ee-458">'Razor'</span></span>
- <span data-ttu-id="4b1ee-459">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-459">'SignalR' uid:</span></span> 

<span data-ttu-id="4b1ee-460">----------------| | `add`     | Ajoutez une propriété ou un élément de tableau.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-460">----------------| | `add`     | Add a property or array element.</span></span> <span data-ttu-id="4b1ee-461">Pour la propriété existante : valeur définie. | | `remove`  | Supprimez une propriété ou un élément de tableau.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-461">For existing property: set value.| | `remove`  | Remove a property or array element.</span></span> <span data-ttu-id="4b1ee-462">| | `replace` | Identique à `remove` suivi `add` du même emplacement.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-462">| | `replace` | Same as `remove` followed by `add` at same location.</span></span> <span data-ttu-id="4b1ee-463">| | `move`    | Identique à la `remove` source, puis `add` à la destination à l’aide de la valeur de la source.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-463">| | `move`    | Same as `remove` from source followed by `add` to destination using value from source.</span></span> <span data-ttu-id="4b1ee-464">| | `copy`    | Identique `add` à la destination à l’aide de la valeur de la source.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-464">| | `copy`    | Same as `add` to destination using value from source.</span></span> <span data-ttu-id="4b1ee-465">| | `test`    | Retourne le code d’état de réussite si la valeur est égale à `path` = fourni `value` . |</span><span class="sxs-lookup"><span data-stu-id="4b1ee-465">| | `test`    | Return success status code if value at `path` = provided `value`.|</span></span>

## <a name="jsonpatch-in-aspnet-core"></a><span data-ttu-id="4b1ee-466">JsonPatch dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4b1ee-466">JsonPatch in ASP.NET Core</span></span>

<span data-ttu-id="4b1ee-467">L’implémentation ASP.NET Core de JSON Patch est fournie dans le package NuGet [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/).</span><span class="sxs-lookup"><span data-stu-id="4b1ee-467">The ASP.NET Core implementation of JSON Patch is provided in the [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet package.</span></span> <span data-ttu-id="4b1ee-468">Le package est inclus dans le sous-package [Microsoft. AspnetCore. app](xref:fundamentals/metapackage-app) .</span><span class="sxs-lookup"><span data-stu-id="4b1ee-468">The package is included in the [Microsoft.AspnetCore.App](xref:fundamentals/metapackage-app) metapackage.</span></span>

## <a name="action-method-code"></a><span data-ttu-id="4b1ee-469">Code de méthode d’action</span><span class="sxs-lookup"><span data-stu-id="4b1ee-469">Action method code</span></span>

<span data-ttu-id="4b1ee-470">Dans un contrôleur d’API, une méthode d’action pour JSON Patch :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-470">In an API controller, an action method for JSON Patch:</span></span>

* <span data-ttu-id="4b1ee-471">est annotée avec l’attribut `HttpPatch` ;</span><span class="sxs-lookup"><span data-stu-id="4b1ee-471">Is annotated with the `HttpPatch` attribute.</span></span>
* <span data-ttu-id="4b1ee-472">Accepte un `JsonPatchDocument<T>` , généralement avec `[FromBody]` .</span><span class="sxs-lookup"><span data-stu-id="4b1ee-472">Accepts a `JsonPatchDocument<T>`, typically with `[FromBody]`.</span></span>
* <span data-ttu-id="4b1ee-473">appelle `ApplyTo` sur le document de correctif pour appliquer les modifications.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-473">Calls `ApplyTo` on the patch document to apply the changes.</span></span>

<span data-ttu-id="4b1ee-474">Voici un exemple :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-474">Here's an example:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

<span data-ttu-id="4b1ee-475">Ce code de l’exemple d’application fonctionne avec le modèle `Customer` suivant.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-475">This code from the sample app works with the following `Customer` model.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

<span data-ttu-id="4b1ee-476">L’exemple de méthode d’action :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-476">The sample action method:</span></span>

* <span data-ttu-id="4b1ee-477">Construit un objet `Customer`.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-477">Constructs a `Customer`.</span></span>
* <span data-ttu-id="4b1ee-478">Applique le correctif.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-478">Applies the patch.</span></span>
* <span data-ttu-id="4b1ee-479">Retourne le résultat dans le corps de la réponse.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-479">Returns the result in the body of the response.</span></span>

 <span data-ttu-id="4b1ee-480">Dans une application réelle, le code récupérerait les données dans un magasin tel qu’une base de données et mettrait à jour la base de données après avoir appliqué le correctif.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-480">In a real app, the code would retrieve the data from a store such as a database and update the database after applying the patch.</span></span>

### <a name="model-state"></a><span data-ttu-id="4b1ee-481">État du modèle</span><span class="sxs-lookup"><span data-stu-id="4b1ee-481">Model state</span></span>

<span data-ttu-id="4b1ee-482">L’exemple de méthode d’action précédent appelle une surcharge de `ApplyTo` qui prend l’état du modèle comme un de ses paramètres.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-482">The preceding action method example calls an overload of `ApplyTo` that takes model state as one of its parameters.</span></span> <span data-ttu-id="4b1ee-483">Avec cette option, vous pouvez obtenir des messages d’erreur dans les réponses.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-483">With this option, you can get error messages in responses.</span></span> <span data-ttu-id="4b1ee-484">L’exemple suivant montre le corps d’une demande-réponse incorrecte 400 pour une opération `test` :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-484">The following example shows the body of a 400 Bad Request response for a `test` operation:</span></span>

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a><span data-ttu-id="4b1ee-485">Objets dynamiques</span><span class="sxs-lookup"><span data-stu-id="4b1ee-485">Dynamic objects</span></span>

<span data-ttu-id="4b1ee-486">L’exemple de méthode d’action suivant montre comment appliquer un correctif à un objet dynamique.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-486">The following action method example shows how to apply a patch to a dynamic object.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a><span data-ttu-id="4b1ee-487">L’opération d’ajout</span><span class="sxs-lookup"><span data-stu-id="4b1ee-487">The add operation</span></span>

* <span data-ttu-id="4b1ee-488">Si `path` pointe vers un élément du tableau : insère un nouvel élément avant celui spécifié par `path`.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-488">If `path` points to an array element: inserts new element before the one specified by `path`.</span></span>
* <span data-ttu-id="4b1ee-489">Si `path` pointe vers une propriété : définit la valeur de propriété.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-489">If `path` points to a property: sets the property value.</span></span>
* <span data-ttu-id="4b1ee-490">Si `path` pointe vers un emplacement qui n’existe pas :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-490">If `path` points to a nonexistent location:</span></span>
  * <span data-ttu-id="4b1ee-491">Si la ressource à corriger est un objet dynamique : ajoute une propriété.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-491">If the resource to patch is a dynamic object: adds a property.</span></span>
  * <span data-ttu-id="4b1ee-492">Si la ressource à corriger est un objet statique : la demande échoue.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-492">If the resource to patch is a static object: the request fails.</span></span>

<span data-ttu-id="4b1ee-493">L’exemple de document de correctif suivant définit la valeur de `CustomerName` et ajoute un objet `Order` à la fin du tableau `Orders`.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-493">The following sample patch document sets the value of `CustomerName` and adds an `Order` object to the end of the `Orders` array.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a><span data-ttu-id="4b1ee-494">L’opération de suppression</span><span class="sxs-lookup"><span data-stu-id="4b1ee-494">The remove operation</span></span>

* <span data-ttu-id="4b1ee-495">Si `path` pointe vers un élément du tableau : supprime l’élément.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-495">If `path` points to an array element: removes the element.</span></span>
* <span data-ttu-id="4b1ee-496">Si `path` pointe vers une propriété :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-496">If `path` points to a property:</span></span>
  * <span data-ttu-id="4b1ee-497">Si la ressource à corriger est un objet dynamique : supprime la propriété.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-497">If resource to patch is a dynamic object: removes the property.</span></span>
  * <span data-ttu-id="4b1ee-498">Si la ressource à corriger est un objet statique :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-498">If resource to patch is a static object:</span></span>
    * <span data-ttu-id="4b1ee-499">Si la propriété est Nullable : met la valeur sur Null.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-499">If the property is nullable: sets it to null.</span></span>
    * <span data-ttu-id="4b1ee-500">Si la propriété n’est pas Nullable : met la valeur sur `default<T>`.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-500">If the property is non-nullable, sets it to `default<T>`.</span></span>

<span data-ttu-id="4b1ee-501">L’exemple suivant de document de correctif définit `CustomerName` sur Null et supprime `Orders[0]`.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-501">The following sample patch document sets `CustomerName` to null and deletes `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a><span data-ttu-id="4b1ee-502">L’opération de remplacement</span><span class="sxs-lookup"><span data-stu-id="4b1ee-502">The replace operation</span></span>

<span data-ttu-id="4b1ee-503">Cette opération est fonctionnellement identique à `remove` suivi de `add`.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-503">This operation is functionally the same as a `remove` followed by an `add`.</span></span>

<span data-ttu-id="4b1ee-504">L’exemple suivant de document de correctif définit la valeur de `CustomerName` et remplace `Orders[0]` avec un nouvel objet `Order`.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-504">The following sample patch document sets the value of `CustomerName` and replaces `Orders[0]`with a new `Order` object.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a><span data-ttu-id="4b1ee-505">L’opération de déplacement</span><span class="sxs-lookup"><span data-stu-id="4b1ee-505">The move operation</span></span>

* <span data-ttu-id="4b1ee-506">Si `path` pointe vers un élément du tableau : copie l’élément `from` à l’emplacement de l’élément `path`, puis exécute une opération `remove` sur l’élément `from`.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-506">If `path` points to an array element: copies `from` element to location of `path` element, then runs a `remove` operation on the `from` element.</span></span>
* <span data-ttu-id="4b1ee-507">Si `path` pointe vers une propriété : copie la valeur de la propriété `from` vers la propriété `path`, puis exécute une opération `remove` sur la propriété `from`.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-507">If `path` points to a property: copies value of `from` property to `path` property, then runs a `remove` operation on the `from` property.</span></span>
* <span data-ttu-id="4b1ee-508">Si `path` pointe vers une propriété qui n’existe pas :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-508">If `path` points to a nonexistent property:</span></span>
  * <span data-ttu-id="4b1ee-509">Si la ressource à corriger est un objet statique : la demande échoue.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-509">If the resource to patch is a static object: the request fails.</span></span>
  * <span data-ttu-id="4b1ee-510">Si la ressource à corriger est un objet dynamique : copie la propriété `from` à un emplacement indiqué par `path`, puis exécute une opération `remove` sur la propriété `from`.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-510">If the resource to patch is a dynamic object: copies `from` property to location indicated by `path`, then runs a `remove` operation on the `from` property.</span></span>

<span data-ttu-id="4b1ee-511">L’exemple de document de correctif suivant :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-511">The following sample patch document:</span></span>

* <span data-ttu-id="4b1ee-512">Copie la valeur de `Orders[0].OrderName` vers `CustomerName`.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-512">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="4b1ee-513">Met `Orders[0].OrderName` sur la valeur Null.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-513">Sets `Orders[0].OrderName` to null.</span></span>
* <span data-ttu-id="4b1ee-514">Déplace `Orders[1]` avant `Orders[0]`.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-514">Moves `Orders[1]` to before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a><span data-ttu-id="4b1ee-515">L’opération de copie</span><span class="sxs-lookup"><span data-stu-id="4b1ee-515">The copy operation</span></span>

<span data-ttu-id="4b1ee-516">Cette opération est fonctionnellement identique à une opération `move` sans l’étape `remove` finale.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-516">This operation is functionally the same as a `move` operation without the final `remove` step.</span></span>

<span data-ttu-id="4b1ee-517">L’exemple de document de correctif suivant :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-517">The following sample patch document:</span></span>

* <span data-ttu-id="4b1ee-518">Copie la valeur de `Orders[0].OrderName` vers `CustomerName`.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-518">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="4b1ee-519">Insère une copie de `Orders[1]` avant `Orders[0]`.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-519">Inserts a copy of `Orders[1]` before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a><span data-ttu-id="4b1ee-520">L’opération de test</span><span class="sxs-lookup"><span data-stu-id="4b1ee-520">The test operation</span></span>

<span data-ttu-id="4b1ee-521">Si la valeur à l’emplacement indiqué par `path` est différente de la valeur fournie dans `value`, la demande échoue.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-521">If the value at the location indicated by `path` is different from the value provided in `value`, the request fails.</span></span> <span data-ttu-id="4b1ee-522">Dans ce cas, toute la demande PATCH échoue, même si toutes les autres opérations dans le document de correctif réussiraient autrement.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-522">In that case, the whole PATCH request fails even if all other operations in the patch document would otherwise succeed.</span></span>

<span data-ttu-id="4b1ee-523">L’opération `test` est généralement utilisée pour empêcher une mise à jour lorsqu’il y a un conflit d’accès concurrentiel.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-523">The `test` operation is commonly used to prevent an update when there's a concurrency conflict.</span></span>

<span data-ttu-id="4b1ee-524">L’exemple de document de correctif suivant n’a aucun effet si la valeur initiale de `CustomerName` est « John », car le test échoue :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-524">The following sample patch document has no effect if the initial value of `CustomerName` is "John", because the test fails:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a><span data-ttu-id="4b1ee-525">Obtenir le code</span><span class="sxs-lookup"><span data-stu-id="4b1ee-525">Get the code</span></span>

<span data-ttu-id="4b1ee-526">[Affichez ou téléchargez l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2).</span><span class="sxs-lookup"><span data-stu-id="4b1ee-526">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2).</span></span> <span data-ttu-id="4b1ee-527">([Procédure de téléchargement](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="4b1ee-527">([How to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="4b1ee-528">Pour tester l’exemple, exécutez l’application et envoyez des demandes HTTP avec les paramètres suivants :</span><span class="sxs-lookup"><span data-stu-id="4b1ee-528">To test the sample, run the app and send HTTP requests with the following settings:</span></span>

* <span data-ttu-id="4b1ee-529">URL : `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span><span class="sxs-lookup"><span data-stu-id="4b1ee-529">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span></span>
* <span data-ttu-id="4b1ee-530">Méthode HTTP : `PATCH`</span><span class="sxs-lookup"><span data-stu-id="4b1ee-530">HTTP method: `PATCH`</span></span>
* <span data-ttu-id="4b1ee-531">En-tête : `Content-Type: application/json-patch+json`</span><span class="sxs-lookup"><span data-stu-id="4b1ee-531">Header: `Content-Type: application/json-patch+json`</span></span>
* <span data-ttu-id="4b1ee-532">Corps : copiez et collez l’un des exemples de document de correctif JSON à partir du dossier de projet *JSON* .</span><span class="sxs-lookup"><span data-stu-id="4b1ee-532">Body: Copy and paste one of the JSON patch document samples from the *JSON* project folder.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4b1ee-533">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="4b1ee-533">Additional resources</span></span>

* [<span data-ttu-id="4b1ee-534">Spécification de méthode PATCH IETF RFC 5789 PATCH</span><span class="sxs-lookup"><span data-stu-id="4b1ee-534">IETF RFC 5789 PATCH method specification</span></span>](https://tools.ietf.org/html/rfc5789)
* [<span data-ttu-id="4b1ee-535">Spécification JSON Patch IETF RFC 6902</span><span class="sxs-lookup"><span data-stu-id="4b1ee-535">IETF RFC 6902 JSON Patch specification</span></span>](https://tools.ietf.org/html/rfc6902)
* [<span data-ttu-id="4b1ee-536">Spécification de format de chemin JSON Patch IETF RFC 6901</span><span class="sxs-lookup"><span data-stu-id="4b1ee-536">IETF RFC 6901 JSON Patch path format spec</span></span>](https://tools.ietf.org/html/rfc6901)
* <span data-ttu-id="4b1ee-537">[Documentation JSON Patch](https://jsonpatch.com/).</span><span class="sxs-lookup"><span data-stu-id="4b1ee-537">[JSON Patch documentation](https://jsonpatch.com/).</span></span> <span data-ttu-id="4b1ee-538">Inclut des liens vers des ressources pour la création de documents JSON Patch.</span><span class="sxs-lookup"><span data-stu-id="4b1ee-538">Includes links to resources for creating JSON Patch documents.</span></span>
* [<span data-ttu-id="4b1ee-539">Code source ASP.NET Core JSON Patch</span><span class="sxs-lookup"><span data-stu-id="4b1ee-539">ASP.NET Core JSON Patch source code</span></span>](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end
