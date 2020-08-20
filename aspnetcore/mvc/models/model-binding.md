---
title: Liaison de données dans ASP.NET Core
author: rick-anderson
description: Découvrez comment fonctionne la liaison de modèle avec ASP.NET Core, et comment personnaliser son comportement.
ms.assetid: 0be164aa-1d72-4192-bd6b-192c9c301164
ms.author: riande
ms.date: 12/18/2019
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
uid: mvc/models/model-binding
ms.openlocfilehash: ec36ff6d646e0554550a4372389aed89aa267b1f
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88633979"
---
# <a name="model-binding-in-aspnet-core"></a><span data-ttu-id="1f3da-103">Liaison de données dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1f3da-103">Model Binding in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1f3da-104">Cet article explique ce qu’est la liaison de modèle, comment elle fonctionne et comment personnaliser son comportement.</span><span class="sxs-lookup"><span data-stu-id="1f3da-104">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="1f3da-105">[Affichez ou téléchargez un exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="1f3da-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="1f3da-106">Description de la liaison de modèle</span><span class="sxs-lookup"><span data-stu-id="1f3da-106">What is Model binding</span></span>

<span data-ttu-id="1f3da-107">Les contrôleurs et les Razor pages fonctionnent avec des données provenant de requêtes http.</span><span class="sxs-lookup"><span data-stu-id="1f3da-107">Controllers and Razor pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="1f3da-108">Par exemple, les données de routage peuvent fournir une clé d’enregistrement, et les champs de formulaire posté peuvent fournir des valeurs pour les propriétés du modèle.</span><span class="sxs-lookup"><span data-stu-id="1f3da-108">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="1f3da-109">L’écriture du code permettant de récupérer chacune de ces valeurs et de les convertir en types .NET à partir de chaînes est fastidieuse et source d’erreurs.</span><span class="sxs-lookup"><span data-stu-id="1f3da-109">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="1f3da-110">La liaison de modèle automatise ce processus.</span><span class="sxs-lookup"><span data-stu-id="1f3da-110">Model binding automates this process.</span></span> <span data-ttu-id="1f3da-111">Le système de liaison de modèle :</span><span class="sxs-lookup"><span data-stu-id="1f3da-111">The model binding system:</span></span>

* <span data-ttu-id="1f3da-112">Récupère les données de diverses sources telles que les données de routage, les champs de formulaire et les chaînes de requête</span><span class="sxs-lookup"><span data-stu-id="1f3da-112">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="1f3da-113">Fournit les données aux contrôleurs et aux Razor pages dans les paramètres de méthode et les propriétés publiques.</span><span class="sxs-lookup"><span data-stu-id="1f3da-113">Provides the data to controllers and Razor pages in method parameters and public properties.</span></span>
* <span data-ttu-id="1f3da-114">Convertit les données de chaîne en types .NET</span><span class="sxs-lookup"><span data-stu-id="1f3da-114">Converts string data to .NET types.</span></span>
* <span data-ttu-id="1f3da-115">Met à jour les propriétés des types complexes</span><span class="sxs-lookup"><span data-stu-id="1f3da-115">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="1f3da-116">Exemple</span><span class="sxs-lookup"><span data-stu-id="1f3da-116">Example</span></span>

<span data-ttu-id="1f3da-117">Supposons que vous ayez la méthode d’action suivante :</span><span class="sxs-lookup"><span data-stu-id="1f3da-117">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="1f3da-118">Et que l’application reçoive une requête avec l’URL suivante :</span><span class="sxs-lookup"><span data-stu-id="1f3da-118">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="1f3da-119">La liaison de modèle passe par les étapes suivantes après que le système de routage a sélectionné la méthode d’action :</span><span class="sxs-lookup"><span data-stu-id="1f3da-119">Model binding goes through the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="1f3da-120">Elle recherche le premier paramètre de `GetByID`, un entier nommé `id`.</span><span class="sxs-lookup"><span data-stu-id="1f3da-120">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="1f3da-121">Elle parcourt les sources disponibles dans la requête HTTP et trouve `id` = « 2 » dans les données de routage.</span><span class="sxs-lookup"><span data-stu-id="1f3da-121">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="1f3da-122">Elle convertit la chaîne « 2 » en entier 2.</span><span class="sxs-lookup"><span data-stu-id="1f3da-122">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="1f3da-123">Elle recherche le paramètre suivant de `GetByID`, un booléen nommé `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="1f3da-123">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="1f3da-124">Elle parcourt les sources et trouve « DogsOnly=true » dans la chaîne de requête.</span><span class="sxs-lookup"><span data-stu-id="1f3da-124">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="1f3da-125">La mise en correspondance des noms ne respecte pas la casse.</span><span class="sxs-lookup"><span data-stu-id="1f3da-125">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="1f3da-126">Elle convertit la chaîne « true » en booléen `true`.</span><span class="sxs-lookup"><span data-stu-id="1f3da-126">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="1f3da-127">Le framework appelle ensuite la méthode `GetById`, en passant 2 pour le paramètre `id`, et `true` pour le paramètre `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="1f3da-127">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="1f3da-128">Dans l’exemple précédent, les cibles de liaison de modèle sont des paramètres de méthode qui sont des types simples.</span><span class="sxs-lookup"><span data-stu-id="1f3da-128">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="1f3da-129">Les cibles peuvent être également les propriétés d’un type complexe.</span><span class="sxs-lookup"><span data-stu-id="1f3da-129">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="1f3da-130">Une fois chaque propriété correctement liée, la [validation du modèle](xref:mvc/models/validation) est effectuée pour la propriété concernée.</span><span class="sxs-lookup"><span data-stu-id="1f3da-130">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="1f3da-131">L’enregistrement des données liées au modèle (ainsi que des erreurs de liaison ou de validation) est stocké dans [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) ou [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span><span class="sxs-lookup"><span data-stu-id="1f3da-131">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="1f3da-132">Pour savoir si ce processus a abouti, l’application vérifie l’indicateur [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid).</span><span class="sxs-lookup"><span data-stu-id="1f3da-132">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="1f3da-133">Cibles</span><span class="sxs-lookup"><span data-stu-id="1f3da-133">Targets</span></span>

<span data-ttu-id="1f3da-134">La liaison de modèle tente de trouver des valeurs pour les genres de cible suivants :</span><span class="sxs-lookup"><span data-stu-id="1f3da-134">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="1f3da-135">Paramètres de la méthode d’action de contrôleur vers laquelle une requête est routée.</span><span class="sxs-lookup"><span data-stu-id="1f3da-135">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="1f3da-136">Paramètres de la Razor méthode de gestionnaire de pages vers laquelle une requête est routée.</span><span class="sxs-lookup"><span data-stu-id="1f3da-136">Parameters of the Razor Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="1f3da-137">Propriétés publiques d’un contrôleur ou d’une classe `PageModel`, si elles sont spécifiées par des attributs.</span><span class="sxs-lookup"><span data-stu-id="1f3da-137">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="1f3da-138">Attribut [BindProperty]</span><span class="sxs-lookup"><span data-stu-id="1f3da-138">[BindProperty] attribute</span></span>

<span data-ttu-id="1f3da-139">Peut être appliqué à une propriété publique d’un contrôleur ou à une classe `PageModel` pour que la liaison de modèle cible cette propriété :</span><span class="sxs-lookup"><span data-stu-id="1f3da-139">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindpropertiesattribute"></a><span data-ttu-id="1f3da-140">Attribut [BindProperties]</span><span class="sxs-lookup"><span data-stu-id="1f3da-140">[BindProperties] attribute</span></span>

<span data-ttu-id="1f3da-141">Disponible avec ASP.NET Core 2.1 et les versions ultérieures.</span><span class="sxs-lookup"><span data-stu-id="1f3da-141">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="1f3da-142">Peut être appliqué à un contrôleur ou à une classe `PageModel` pour indiquer à la liaison de modèle de cibler toutes les propriétés publiques de la classe :</span><span class="sxs-lookup"><span data-stu-id="1f3da-142">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="1f3da-143">Liaison de modèle pour les requêtes HTTP GET</span><span class="sxs-lookup"><span data-stu-id="1f3da-143">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="1f3da-144">Par défaut, les propriétés ne sont pas liées pour les requêtes HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="1f3da-144">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="1f3da-145">En règle générale, le paramètre ID d’un enregistrement est tout ce dont vous avez besoin pour une requête GET.</span><span class="sxs-lookup"><span data-stu-id="1f3da-145">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="1f3da-146">L’ID d’enregistrement est utilisé pour rechercher l’élément dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="1f3da-146">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="1f3da-147">Il n’est donc pas nécessaire de lier une propriété qui contient une instance du modèle.</span><span class="sxs-lookup"><span data-stu-id="1f3da-147">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="1f3da-148">Pour les scénarios dans lesquels vous souhaitez que les propriétés soient liées aux données provenant de requêtes GET, affectez à la propriété `SupportsGet` la valeur `true` :</span><span class="sxs-lookup"><span data-stu-id="1f3da-148">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="1f3da-149">Sources</span><span class="sxs-lookup"><span data-stu-id="1f3da-149">Sources</span></span>

<span data-ttu-id="1f3da-150">Par défaut, la liaison de modèle obtient des données sous la forme de paires clé-valeur à partir des sources suivantes dans une requête HTTP :</span><span class="sxs-lookup"><span data-stu-id="1f3da-150">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="1f3da-151">Champs de formulaire</span><span class="sxs-lookup"><span data-stu-id="1f3da-151">Form fields</span></span>
1. <span data-ttu-id="1f3da-152">Corps de la requête (pour les [contrôleurs ayant l’attribut [ApiController]](xref:web-api/index#binding-source-parameter-inference).)</span><span class="sxs-lookup"><span data-stu-id="1f3da-152">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="1f3da-153">Données de routage</span><span class="sxs-lookup"><span data-stu-id="1f3da-153">Route data</span></span>
1. <span data-ttu-id="1f3da-154">Paramètres de chaîne de requête</span><span class="sxs-lookup"><span data-stu-id="1f3da-154">Query string parameters</span></span>
1. <span data-ttu-id="1f3da-155">Fichiers chargés</span><span class="sxs-lookup"><span data-stu-id="1f3da-155">Uploaded files</span></span>

<span data-ttu-id="1f3da-156">Pour chaque paramètre ou propriété cible, les sources sont analysées dans l’ordre indiqué dans la liste précédente.</span><span class="sxs-lookup"><span data-stu-id="1f3da-156">For each target parameter or property, the sources are scanned in the order indicated in the preceding list.</span></span> <span data-ttu-id="1f3da-157">Il existe quelques exceptions :</span><span class="sxs-lookup"><span data-stu-id="1f3da-157">There are a few exceptions:</span></span>

* <span data-ttu-id="1f3da-158">Les données de routage et les valeurs de chaîne de requête sont utilisées uniquement pour les types simples.</span><span class="sxs-lookup"><span data-stu-id="1f3da-158">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="1f3da-159">Les fichiers chargés sont liés uniquement aux types cibles qui implémentent `IFormFile` ou `IEnumerable<IFormFile>`.</span><span class="sxs-lookup"><span data-stu-id="1f3da-159">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="1f3da-160">Si la source par défaut n’est pas correcte, utilisez l’un des attributs suivants pour spécifier la source :</span><span class="sxs-lookup"><span data-stu-id="1f3da-160">If the default source is not correct, use one of the following attributes to specify the source:</span></span>

* <span data-ttu-id="1f3da-161">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) -Obtient des valeurs à partir de la chaîne de requête.</span><span class="sxs-lookup"><span data-stu-id="1f3da-161">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="1f3da-162">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) -Obtient des valeurs à partir des données d’itinéraire.</span><span class="sxs-lookup"><span data-stu-id="1f3da-162">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="1f3da-163">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) -Obtient des valeurs à partir de champs de formulaire publiés.</span><span class="sxs-lookup"><span data-stu-id="1f3da-163">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="1f3da-164">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) -Obtient les valeurs du corps de la demande.</span><span class="sxs-lookup"><span data-stu-id="1f3da-164">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="1f3da-165">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) -Obtient des valeurs à partir des en-têtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="1f3da-165">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="1f3da-166">Ces attributs :</span><span class="sxs-lookup"><span data-stu-id="1f3da-166">These attributes:</span></span>

* <span data-ttu-id="1f3da-167">Sont ajoutés aux propriétés du modèle individuellement (et non à la classe de modèle), comme dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="1f3da-167">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="1f3da-168">Acceptent éventuellement une valeur de nom de modèle dans le constructeur.</span><span class="sxs-lookup"><span data-stu-id="1f3da-168">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="1f3da-169">Cette option est fournie au cas où le nom de propriété ne correspondrait pas à la valeur de la requête.</span><span class="sxs-lookup"><span data-stu-id="1f3da-169">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="1f3da-170">Par exemple, la valeur de la requête peut être un en-tête avec un trait d’union dans son nom, comme dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="1f3da-170">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="1f3da-171">Attribut [FromBody]</span><span class="sxs-lookup"><span data-stu-id="1f3da-171">[FromBody] attribute</span></span>

<span data-ttu-id="1f3da-172">Appliquez l' `[FromBody]` attribut à un paramètre pour remplir ses propriétés à partir du corps d’une requête http.</span><span class="sxs-lookup"><span data-stu-id="1f3da-172">Apply the `[FromBody]` attribute to a parameter to populate its properties from the body of an HTTP request.</span></span> <span data-ttu-id="1f3da-173">Le runtime ASP.NET Core délègue la responsabilité de lire le corps dans un formateur d’entrée.</span><span class="sxs-lookup"><span data-stu-id="1f3da-173">The ASP.NET Core runtime delegates the responsibility of reading the body to an input formatter.</span></span> <span data-ttu-id="1f3da-174">Les formateurs d’entrée sont décrits [plus loin dans cet article](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="1f3da-174">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="1f3da-175">Lorsque `[FromBody]` est appliqué à un paramètre de type complexe, tous les attributs de source de liaison appliqués à ses propriétés sont ignorés.</span><span class="sxs-lookup"><span data-stu-id="1f3da-175">When `[FromBody]` is applied to a complex type parameter, any binding source attributes applied to its properties are ignored.</span></span> <span data-ttu-id="1f3da-176">Par exemple, l' `Create` action suivante spécifie que son `pet` paramètre est rempli à partir du corps :</span><span class="sxs-lookup"><span data-stu-id="1f3da-176">For example, the following `Create` action specifies that its `pet` parameter is populated from the body:</span></span>

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

<span data-ttu-id="1f3da-177">La `Pet` classe spécifie que sa `Breed` propriété est remplie à partir d’un paramètre de chaîne de requête :</span><span class="sxs-lookup"><span data-stu-id="1f3da-177">The `Pet` class specifies that its `Breed` property is populated from a query string parameter:</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

<span data-ttu-id="1f3da-178">Dans l’exemple précédent :</span><span class="sxs-lookup"><span data-stu-id="1f3da-178">In the preceding example:</span></span>

* <span data-ttu-id="1f3da-179">L' `[FromQuery]` attribut est ignoré.</span><span class="sxs-lookup"><span data-stu-id="1f3da-179">The `[FromQuery]` attribute is ignored.</span></span>
* <span data-ttu-id="1f3da-180">La `Breed` propriété n’est pas remplie à partir d’un paramètre de chaîne de requête.</span><span class="sxs-lookup"><span data-stu-id="1f3da-180">The `Breed` property is not populated from a query string parameter.</span></span> 

<span data-ttu-id="1f3da-181">Les formateurs d’entrée lisent uniquement le corps et ne comprennent pas les attributs de source de liaison.</span><span class="sxs-lookup"><span data-stu-id="1f3da-181">Input formatters read only the body and don't understand binding source attributes.</span></span> <span data-ttu-id="1f3da-182">Si une valeur appropriée est trouvée dans le corps, cette valeur est utilisée pour remplir la `Breed` propriété.</span><span class="sxs-lookup"><span data-stu-id="1f3da-182">If a suitable value is found in the body, that value is used to populate the `Breed` property.</span></span>

<span data-ttu-id="1f3da-183">N’appliquez pas `[FromBody]` à plus d’un paramètre par méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="1f3da-183">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="1f3da-184">Une fois que le flux de requête est lu par un formateur d’entrée, il ne peut plus être lu pour la liaison d’autres `[FromBody]` paramètres.</span><span class="sxs-lookup"><span data-stu-id="1f3da-184">Once the request stream is read by an input formatter, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="1f3da-185">Sources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="1f3da-185">Additional sources</span></span>

<span data-ttu-id="1f3da-186">Les données sources sont fournies au système de liaison de modèle par les *fournisseurs de valeurs*.</span><span class="sxs-lookup"><span data-stu-id="1f3da-186">Source data is provided to the model binding system by *value providers*.</span></span> <span data-ttu-id="1f3da-187">Vous pouvez écrire et inscrire des fournisseurs de valeurs personnalisés qui obtiennent des données de liaison de modèle à partir d’autres sources.</span><span class="sxs-lookup"><span data-stu-id="1f3da-187">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="1f3da-188">Par exemple, vous pouvez obtenir des données à partir de ou de l' cookie État de session.</span><span class="sxs-lookup"><span data-stu-id="1f3da-188">For example, you might want data from cookies or session state.</span></span> <span data-ttu-id="1f3da-189">Pour obtenir des données provenant d’une nouvelle source :</span><span class="sxs-lookup"><span data-stu-id="1f3da-189">To get data from a new source:</span></span>

* <span data-ttu-id="1f3da-190">Créez une classe qui implémente `IValueProvider`.</span><span class="sxs-lookup"><span data-stu-id="1f3da-190">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="1f3da-191">Créez une classe qui implémente `IValueProviderFactory`.</span><span class="sxs-lookup"><span data-stu-id="1f3da-191">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="1f3da-192">Inscrivez la classe de fabrique dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="1f3da-192">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="1f3da-193">L’exemple d’application comprend un [fournisseur de valeur](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProvider.cs) et un exemple d' [usine](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProviderFactory.cs) qui obtient des valeurs de cookie s.</span><span class="sxs-lookup"><span data-stu-id="1f3da-193">The sample app includes a [value provider](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProvider.cs) and [factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProviderFactory.cs) example that gets values from cookies.</span></span> <span data-ttu-id="1f3da-194">Voici le code d’inscription dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="1f3da-194">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4)]

<span data-ttu-id="1f3da-195">Le code affiché place le fournisseur de valeurs personnalisé après tous les fournisseurs de valeurs intégrés.</span><span class="sxs-lookup"><span data-stu-id="1f3da-195">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="1f3da-196">Pour en faire le premier fournisseur de la liste, appelez `Insert(0, new CookieValueProviderFactory())` à la place de `Add`.</span><span class="sxs-lookup"><span data-stu-id="1f3da-196">To make it the first in the list, call `Insert(0, new CookieValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="1f3da-197">Aucune source pour une propriété de modèle</span><span class="sxs-lookup"><span data-stu-id="1f3da-197">No source for a model property</span></span>

<span data-ttu-id="1f3da-198">Par défaut, aucune erreur d’état de modèle n’est créée, s’il n’existe aucune valeur de propriété de modèle.</span><span class="sxs-lookup"><span data-stu-id="1f3da-198">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="1f3da-199">La propriété a une valeur null ou une valeur par défaut :</span><span class="sxs-lookup"><span data-stu-id="1f3da-199">The property is set to null or a default value:</span></span>

* <span data-ttu-id="1f3da-200">Les types simples Nullable ont une valeur `null`.</span><span class="sxs-lookup"><span data-stu-id="1f3da-200">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="1f3da-201">Les types valeur non Nullable ont la valeur `default(T)`.</span><span class="sxs-lookup"><span data-stu-id="1f3da-201">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="1f3da-202">Par exemple, un paramètre `int id` a la valeur 0.</span><span class="sxs-lookup"><span data-stu-id="1f3da-202">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="1f3da-203">Pour les types complexes, la liaison de modèle crée une instance à l’aide du constructeur par défaut, sans définir de propriétés.</span><span class="sxs-lookup"><span data-stu-id="1f3da-203">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="1f3da-204">Les tableaux ont la valeur `Array.Empty<T>()`, sauf les tableaux `byte[]` qui ont une valeur `null`.</span><span class="sxs-lookup"><span data-stu-id="1f3da-204">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="1f3da-205">Si l’état du modèle doit être invalidé lorsque rien n’est trouvé dans les champs de formulaire d’une propriété de modèle, utilisez l' [`[BindRequired]`](#bindrequired-attribute) attribut.</span><span class="sxs-lookup"><span data-stu-id="1f3da-205">If model state should be invalidated when nothing is found in form fields for a model property, use the [`[BindRequired]`](#bindrequired-attribute) attribute.</span></span>

<span data-ttu-id="1f3da-206">Notez que ce comportement de `[BindRequired]` s’applique à la liaison de modèle des données de formulaire postées, et non aux données JSON ou XML d’un corps de requête.</span><span class="sxs-lookup"><span data-stu-id="1f3da-206">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="1f3da-207">Les données du corps de requête sont prises en charge par les [formateurs d’entrée](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="1f3da-207">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="1f3da-208">Erreurs de conversion de type</span><span class="sxs-lookup"><span data-stu-id="1f3da-208">Type conversion errors</span></span>

<span data-ttu-id="1f3da-209">Si une source est localisée mais qu’elle ne peut pas être convertie vers le type cible, l’état du modèle est marqué comme étant non valide.</span><span class="sxs-lookup"><span data-stu-id="1f3da-209">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="1f3da-210">Le paramètre ou la propriété cible a une valeur null ou une valeur par défaut, comme indiqué dans la section précédente.</span><span class="sxs-lookup"><span data-stu-id="1f3da-210">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="1f3da-211">Dans un contrôleur d’API ayant l’attribut `[ApiController]`, un état de modèle non valide entraîne une réponse HTTP 400 automatique.</span><span class="sxs-lookup"><span data-stu-id="1f3da-211">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="1f3da-212">Dans une Razor page, réaffichez la page avec un message d’erreur :</span><span class="sxs-lookup"><span data-stu-id="1f3da-212">In a Razor page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="1f3da-213">La validation côté client intercepte la plupart des données incorrectes qui seraient autrement soumises à un Razor formulaire de pages.</span><span class="sxs-lookup"><span data-stu-id="1f3da-213">Client-side validation catches most bad data that would otherwise be submitted to a Razor Pages form.</span></span> <span data-ttu-id="1f3da-214">Cette validation rend difficile le déclenchement du code en surbrillance indiqué plus haut.</span><span class="sxs-lookup"><span data-stu-id="1f3da-214">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="1f3da-215">L’exemple d’application comprend un bouton **Submit with Invalid Date** (Envoyer avec une date non valide), qui place les données incorrectes dans le champ **Hire Date** (Date d’embauche) et envoie le formulaire.</span><span class="sxs-lookup"><span data-stu-id="1f3da-215">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="1f3da-216">Ce bouton montre comment fonctionne le code permettant de réafficher la page quand des erreurs de conversion de données se produisent.</span><span class="sxs-lookup"><span data-stu-id="1f3da-216">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="1f3da-217">Quand la page est réaffichée par le code précédent, l’entrée non valide n’est pas visible dans le champ de formulaire.</span><span class="sxs-lookup"><span data-stu-id="1f3da-217">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="1f3da-218">En effet, la propriété de modèle à une valeur null ou une valeur par défaut.</span><span class="sxs-lookup"><span data-stu-id="1f3da-218">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="1f3da-219">L’entrée non valide apparaît dans un message d’erreur.</span><span class="sxs-lookup"><span data-stu-id="1f3da-219">The invalid input does appear in an error message.</span></span> <span data-ttu-id="1f3da-220">Toutefois, si vous souhaitez réafficher les données incorrectes dans le champ de formulaire, transformez la propriété de modèle en chaîne et procédez à la conversion des données manuellement.</span><span class="sxs-lookup"><span data-stu-id="1f3da-220">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="1f3da-221">La même stratégie est recommandée si vous ne souhaitez pas que les erreurs de conversion de type entraînent des erreurs d’état de modèle.</span><span class="sxs-lookup"><span data-stu-id="1f3da-221">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="1f3da-222">Dans ce cas, transformez la propriété de modèle en chaîne.</span><span class="sxs-lookup"><span data-stu-id="1f3da-222">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="1f3da-223">Types simples</span><span class="sxs-lookup"><span data-stu-id="1f3da-223">Simple types</span></span>

<span data-ttu-id="1f3da-224">Les types simples que le lieur de modèle peut convertir en chaînes sources sont les suivants :</span><span class="sxs-lookup"><span data-stu-id="1f3da-224">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="1f3da-225">Booléen</span><span class="sxs-lookup"><span data-stu-id="1f3da-225">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="1f3da-226">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="1f3da-226">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="1f3da-227">Char</span><span class="sxs-lookup"><span data-stu-id="1f3da-227">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="1f3da-228">DateTime</span><span class="sxs-lookup"><span data-stu-id="1f3da-228">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="1f3da-229">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="1f3da-229">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="1f3da-230">Décimal</span><span class="sxs-lookup"><span data-stu-id="1f3da-230">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="1f3da-231">Double</span><span class="sxs-lookup"><span data-stu-id="1f3da-231">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="1f3da-232">Variables</span><span class="sxs-lookup"><span data-stu-id="1f3da-232">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="1f3da-233">Uniques</span><span class="sxs-lookup"><span data-stu-id="1f3da-233">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="1f3da-234">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="1f3da-234">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="1f3da-235">Unique</span><span class="sxs-lookup"><span data-stu-id="1f3da-235">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="1f3da-236">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="1f3da-236">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="1f3da-237">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="1f3da-237">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="1f3da-238">Uri</span><span class="sxs-lookup"><span data-stu-id="1f3da-238">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="1f3da-239">Version</span><span class="sxs-lookup"><span data-stu-id="1f3da-239">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="1f3da-240">Types complexes</span><span class="sxs-lookup"><span data-stu-id="1f3da-240">Complex types</span></span>

<span data-ttu-id="1f3da-241">Un type complexe doit avoir un constructeur public par défaut et des propriétés publiques accessibles en écriture à lier.</span><span class="sxs-lookup"><span data-stu-id="1f3da-241">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="1f3da-242">Quand la liaison de modèle se produit, la classe est instanciée à l’aide du constructeur public par défaut.</span><span class="sxs-lookup"><span data-stu-id="1f3da-242">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="1f3da-243">Pour chaque propriété du type complexe, la liaison de modèle recherche dans les sources le modèle de nom *préfixe.nom_propriété*.</span><span class="sxs-lookup"><span data-stu-id="1f3da-243">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name*.</span></span> <span data-ttu-id="1f3da-244">Si rien n’est trouvé, elle recherche uniquement *nom_propriété* sans le préfixe.</span><span class="sxs-lookup"><span data-stu-id="1f3da-244">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="1f3da-245">Dans le cas d’une liaison à un paramètre, le préfixe représente le nom du paramètre.</span><span class="sxs-lookup"><span data-stu-id="1f3da-245">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="1f3da-246">Dans le cas d’une liaison à une propriété publique `PageModel`, le préfixe représente le nom de la propriété publique.</span><span class="sxs-lookup"><span data-stu-id="1f3da-246">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="1f3da-247">Certains attributs ont une propriété `Prefix` qui vous permet de remplacer l’utilisation par défaut du nom de paramètre ou de propriété.</span><span class="sxs-lookup"><span data-stu-id="1f3da-247">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="1f3da-248">Par exemple, supposons que le type complexe corresponde à la classe `Instructor` suivante :</span><span class="sxs-lookup"><span data-stu-id="1f3da-248">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="1f3da-249">Préfixe = nom de paramètre</span><span class="sxs-lookup"><span data-stu-id="1f3da-249">Prefix = parameter name</span></span>

<span data-ttu-id="1f3da-250">Si le modèle à lier est un paramètre nommé `instructorToUpdate` :</span><span class="sxs-lookup"><span data-stu-id="1f3da-250">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="1f3da-251">La liaison de modèle commence par rechercher dans les sources la clé `instructorToUpdate.ID`.</span><span class="sxs-lookup"><span data-stu-id="1f3da-251">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="1f3da-252">Si elle est introuvable, elle recherche `ID` sans préfixe.</span><span class="sxs-lookup"><span data-stu-id="1f3da-252">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="1f3da-253">Préfixe = nom de propriété</span><span class="sxs-lookup"><span data-stu-id="1f3da-253">Prefix = property name</span></span>

<span data-ttu-id="1f3da-254">Si le modèle à lier est une propriété nommée `Instructor` du contrôleur ou de la classe `PageModel` :</span><span class="sxs-lookup"><span data-stu-id="1f3da-254">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="1f3da-255">La liaison de modèle commence par rechercher dans les sources la clé `Instructor.ID`.</span><span class="sxs-lookup"><span data-stu-id="1f3da-255">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="1f3da-256">Si elle est introuvable, elle recherche `ID` sans préfixe.</span><span class="sxs-lookup"><span data-stu-id="1f3da-256">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="1f3da-257">Préfixe personnalisé</span><span class="sxs-lookup"><span data-stu-id="1f3da-257">Custom prefix</span></span>

<span data-ttu-id="1f3da-258">Si le modèle à lier est un paramètre nommé `instructorToUpdate` et si un attribut `Bind` spécifie `Instructor` en tant que préfixe :</span><span class="sxs-lookup"><span data-stu-id="1f3da-258">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="1f3da-259">La liaison de modèle commence par rechercher dans les sources la clé `Instructor.ID`.</span><span class="sxs-lookup"><span data-stu-id="1f3da-259">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="1f3da-260">Si elle est introuvable, elle recherche `ID` sans préfixe.</span><span class="sxs-lookup"><span data-stu-id="1f3da-260">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="1f3da-261">Attributs des cibles de type complexe</span><span class="sxs-lookup"><span data-stu-id="1f3da-261">Attributes for complex type targets</span></span>

<span data-ttu-id="1f3da-262">Plusieurs attributs intégrés sont disponibles pour contrôler la liaison de modèle des types complexes :</span><span class="sxs-lookup"><span data-stu-id="1f3da-262">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[Bind]`
* `[BindRequired]`
* `[BindNever]`

> [!WARNING]
> <span data-ttu-id="1f3da-263">Ces attributs affectent la liaison de modèle quand les données de formulaire postées représentent la source des valeurs.</span><span class="sxs-lookup"><span data-stu-id="1f3da-263">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="1f3da-264">Ils n’affectent ***pas*** les formateurs d’entrée qui traitent les corps de requête JSON et XML publiés.</span><span class="sxs-lookup"><span data-stu-id="1f3da-264">They do ***not*** affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="1f3da-265">Les formateurs d’entrée sont décrits [plus loin dans cet article](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="1f3da-265">Input formatters are explained [later in this article](#input-formatters).</span></span>

### <a name="bind-attribute"></a><span data-ttu-id="1f3da-266">Attribut [Bind]</span><span class="sxs-lookup"><span data-stu-id="1f3da-266">[Bind] attribute</span></span>

<span data-ttu-id="1f3da-267">Il peut être appliqué à une classe ou à un paramètre de méthode.</span><span class="sxs-lookup"><span data-stu-id="1f3da-267">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="1f3da-268">Il spécifie les propriétés d’un modèle à inclure dans la liaison de modèle.</span><span class="sxs-lookup"><span data-stu-id="1f3da-268">Specifies which properties of a model should be included in model binding.</span></span> <span data-ttu-id="1f3da-269">`[Bind]` n’affecte ***pas*** les formateurs d’entrée.</span><span class="sxs-lookup"><span data-stu-id="1f3da-269">`[Bind]` does ***not*** affect input formatters.</span></span>

<span data-ttu-id="1f3da-270">Dans l’exemple suivant, seules les propriétés spécifiées du modèle `Instructor` sont liées quand une méthode de gestionnaire ou une méthode d’action est appelée :</span><span class="sxs-lookup"><span data-stu-id="1f3da-270">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="1f3da-271">Dans l’exemple suivant, seules les propriétés spécifiées du modèle `Instructor` sont liées quand la méthode `OnPost` est appelée :</span><span class="sxs-lookup"><span data-stu-id="1f3da-271">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="1f3da-272">Vous pouvez utiliser l’attribut `[Bind]` pour éviter le surpostage dans les scénarios de *création*.</span><span class="sxs-lookup"><span data-stu-id="1f3da-272">The `[Bind]` attribute can be used to protect against overposting in *create* scenarios.</span></span> <span data-ttu-id="1f3da-273">Il ne fonctionne pas bien dans les scénarios de modification, car les propriétés exclues ont une valeur null ou une valeur par défaut au lieu de rester inchangées.</span><span class="sxs-lookup"><span data-stu-id="1f3da-273">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="1f3da-274">Pour empêcher le surpostage, il est recommandé d’utiliser des modèles de vues à la place de l’attribut `[Bind]`.</span><span class="sxs-lookup"><span data-stu-id="1f3da-274">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="1f3da-275">Pour plus d’informations, consultez [Remarque sur la sécurité concernant le surpostage](xref:data/ef-mvc/crud#security-note-about-overposting).</span><span class="sxs-lookup"><span data-stu-id="1f3da-275">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

### <a name="bindrequired-attribute"></a><span data-ttu-id="1f3da-276">Attribut [BindRequired]</span><span class="sxs-lookup"><span data-stu-id="1f3da-276">[BindRequired] attribute</span></span>

<span data-ttu-id="1f3da-277">Il s’applique uniquement aux propriétés de modèle, pas aux paramètres de méthode.</span><span class="sxs-lookup"><span data-stu-id="1f3da-277">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="1f3da-278">Il oblige la liaison de modèle à ajouter une erreur d’état de modèle si la liaison est impossible pour la propriété d’un modèle.</span><span class="sxs-lookup"><span data-stu-id="1f3da-278">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="1f3da-279">Voici un exemple :</span><span class="sxs-lookup"><span data-stu-id="1f3da-279">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

<span data-ttu-id="1f3da-280">Consultez également la discussion sur l’attribut `[Required]` dans [Validation de modèle](xref:mvc/models/validation#required-attribute).</span><span class="sxs-lookup"><span data-stu-id="1f3da-280">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindnever-attribute"></a><span data-ttu-id="1f3da-281">Attribut [BindNever]</span><span class="sxs-lookup"><span data-stu-id="1f3da-281">[BindNever] attribute</span></span>

<span data-ttu-id="1f3da-282">Il s’applique uniquement aux propriétés de modèle, pas aux paramètres de méthode.</span><span class="sxs-lookup"><span data-stu-id="1f3da-282">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="1f3da-283">Il empêche la liaison de modèle de définir la propriété d’un modèle.</span><span class="sxs-lookup"><span data-stu-id="1f3da-283">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="1f3da-284">Voici un exemple :</span><span class="sxs-lookup"><span data-stu-id="1f3da-284">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

## <a name="collections"></a><span data-ttu-id="1f3da-285">Collections</span><span class="sxs-lookup"><span data-stu-id="1f3da-285">Collections</span></span>

<span data-ttu-id="1f3da-286">Pour les cibles qui sont des collections de types simples, la liaison de modèle recherche les correspondances avec *nom_paramètre* ou *nom_propriété*.</span><span class="sxs-lookup"><span data-stu-id="1f3da-286">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="1f3da-287">Si aucune correspondance n’est localisée, elle recherche l’un des formats pris en charge sans le préfixe.</span><span class="sxs-lookup"><span data-stu-id="1f3da-287">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="1f3da-288">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="1f3da-288">For example:</span></span>

* <span data-ttu-id="1f3da-289">Supposons que le paramètre à lier soit un tableau nommé `selectedCourses` :</span><span class="sxs-lookup"><span data-stu-id="1f3da-289">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="1f3da-290">Les données de formulaire ou de chaîne de requête peuvent avoir l’un des formats suivants :</span><span class="sxs-lookup"><span data-stu-id="1f3da-290">Form or query string data can be in one of the following formats:</span></span>
   
  ```
  selectedCourses=1050&selectedCourses=2000 
  ```

  ```
  selectedCourses[0]=1050&selectedCourses[1]=2000
  ```

  ```
  [0]=1050&[1]=2000
  ```

  ```
  selectedCourses[a]=1050&selectedCourses[b]=2000&selectedCourses.index=a&selectedCourses.index=b
  ```

  ```
  [a]=1050&[b]=2000&index=a&index=b
  ```

* <span data-ttu-id="1f3da-291">Le format suivant est pris en charge uniquement dans les données de formulaire :</span><span class="sxs-lookup"><span data-stu-id="1f3da-291">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="1f3da-292">Pour tous les exemples de formats précédents, la liaison de modèle passe un tableau de deux éléments au paramètre `selectedCourses` :</span><span class="sxs-lookup"><span data-stu-id="1f3da-292">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="1f3da-293">selectedCourses[0]=1050</span><span class="sxs-lookup"><span data-stu-id="1f3da-293">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="1f3da-294">selectedCourses[1]=2000</span><span class="sxs-lookup"><span data-stu-id="1f3da-294">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="1f3da-295">Les formats de données qui utilisent des nombres en indice (... [0] ... [1] ...) doivent être impérativement numérotés de manière séquentielle à partir de zéro.</span><span class="sxs-lookup"><span data-stu-id="1f3da-295">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="1f3da-296">S’il existe des vides dans la numérotation en indice, tous les éléments suivants sont ignorés.</span><span class="sxs-lookup"><span data-stu-id="1f3da-296">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="1f3da-297">Par exemple, si les indices sont 0 et 2 au lieu de 0 et 1, le second élément est ignoré.</span><span class="sxs-lookup"><span data-stu-id="1f3da-297">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="1f3da-298">Dictionnaires</span><span class="sxs-lookup"><span data-stu-id="1f3da-298">Dictionaries</span></span>

<span data-ttu-id="1f3da-299">Pour les cibles `Dictionary`, la liaison de modèle recherche les correspondances avec *nom_paramètre* ou *nom_propriété*.</span><span class="sxs-lookup"><span data-stu-id="1f3da-299">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="1f3da-300">Si aucune correspondance n’est localisée, elle recherche l’un des formats pris en charge sans le préfixe.</span><span class="sxs-lookup"><span data-stu-id="1f3da-300">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="1f3da-301">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="1f3da-301">For example:</span></span>

* <span data-ttu-id="1f3da-302">Supposons que le paramètre cible soit un `Dictionary<int, string>` nommé `selectedCourses` :</span><span class="sxs-lookup"><span data-stu-id="1f3da-302">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="1f3da-303">Les données de chaîne de requête ou de formulaire posté peuvent ressembler à l’un des exemples suivants :</span><span class="sxs-lookup"><span data-stu-id="1f3da-303">The posted form or query string data can look like one of the following examples:</span></span>

  ```
  selectedCourses[1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  [1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  selectedCourses[0].Key=1050&selectedCourses[0].Value=Chemistry&
  selectedCourses[1].Key=2000&selectedCourses[1].Value=Economics
  ```

  ```
  [0].Key=1050&[0].Value=Chemistry&[1].Key=2000&[1].Value=Economics
  ```

* <span data-ttu-id="1f3da-304">Pour tous les exemples de formats précédents, la liaison de modèle passe un dictionnaire de deux éléments au paramètre `selectedCourses` :</span><span class="sxs-lookup"><span data-stu-id="1f3da-304">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="1f3da-305">selectedCourses["1050"]="Chemistry"</span><span class="sxs-lookup"><span data-stu-id="1f3da-305">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="1f3da-306">selectedCourses["2000"]="Economics"</span><span class="sxs-lookup"><span data-stu-id="1f3da-306">selectedCourses["2000"]="Economics"</span></span>

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a><span data-ttu-id="1f3da-307">Comportement de globalisation des données de routage de liaison de modèle et des chaînes de requête</span><span class="sxs-lookup"><span data-stu-id="1f3da-307">Globalization behavior of model binding route data and query strings</span></span>

<span data-ttu-id="1f3da-308">Fournisseur de valeurs d’itinéraire ASP.NET Core et fournisseur de valeur de chaîne de requête :</span><span class="sxs-lookup"><span data-stu-id="1f3da-308">The ASP.NET Core route value provider and query string value provider:</span></span>

* <span data-ttu-id="1f3da-309">Traitez les valeurs comme une culture dite indifférente.</span><span class="sxs-lookup"><span data-stu-id="1f3da-309">Treat values as invariant culture.</span></span>
* <span data-ttu-id="1f3da-310">Attendez-vous à ce que les URL soient invariantes de culture.</span><span class="sxs-lookup"><span data-stu-id="1f3da-310">Expect that URLs are culture-invariant.</span></span>

<span data-ttu-id="1f3da-311">En revanche, les valeurs provenant des données de formulaire subissent une conversion dépendante de la culture.</span><span class="sxs-lookup"><span data-stu-id="1f3da-311">In contrast, values coming from form data undergo a culture-sensitive conversion.</span></span> <span data-ttu-id="1f3da-312">Cela est dû au fait que les URL peuvent être partagées entre les paramètres régionaux.</span><span class="sxs-lookup"><span data-stu-id="1f3da-312">This is by design so that URLs are shareable across locales.</span></span>

<span data-ttu-id="1f3da-313">Pour que le fournisseur de valeurs d’itinéraire ASP.NET Core et le fournisseur de valeurs de chaîne de requête soient soumis à une conversion dépendante de la culture :</span><span class="sxs-lookup"><span data-stu-id="1f3da-313">To make the ASP.NET Core route value provider and query string value provider undergo a culture-sensitive conversion:</span></span>

* <span data-ttu-id="1f3da-314">Héritent de <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span><span class="sxs-lookup"><span data-stu-id="1f3da-314">Inherit from <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span></span>
* <span data-ttu-id="1f3da-315">Copiez le code à partir de [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) ou [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span><span class="sxs-lookup"><span data-stu-id="1f3da-315">Copy the code from [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) or [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span></span>
* <span data-ttu-id="1f3da-316">Remplacer la [valeur de culture](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passée au constructeur de fournisseur de valeur par [CultureInfo. CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span><span class="sxs-lookup"><span data-stu-id="1f3da-316">Replace the [culture value](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passed to the value provider constructor with [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span></span>
* <span data-ttu-id="1f3da-317">Remplacez la fabrique de fournisseur de valeur par défaut dans les options MVC par la nouvelle.</span><span class="sxs-lookup"><span data-stu-id="1f3da-317">Replace the default value provider factory in MVC options with your new one:</span></span>

[!code-csharp[](model-binding/samples_snapshot/3.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/3.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a><span data-ttu-id="1f3da-318">Types de données spéciaux</span><span class="sxs-lookup"><span data-stu-id="1f3da-318">Special data types</span></span>

<span data-ttu-id="1f3da-319">Certains types de données spéciaux peuvent être pris en charge par la liaison de modèle.</span><span class="sxs-lookup"><span data-stu-id="1f3da-319">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="1f3da-320">IFormFile et IFormFileCollection</span><span class="sxs-lookup"><span data-stu-id="1f3da-320">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="1f3da-321">Fichier chargé inclus dans la requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="1f3da-321">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="1f3da-322">`IEnumerable<IFormFile>` est également pris en charge pour plusieurs fichiers.</span><span class="sxs-lookup"><span data-stu-id="1f3da-322">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="1f3da-323">CancellationToken</span><span class="sxs-lookup"><span data-stu-id="1f3da-323">CancellationToken</span></span>

<span data-ttu-id="1f3da-324">utilisé pour annuler l’activité dans les contrôleurs asynchrones.</span><span class="sxs-lookup"><span data-stu-id="1f3da-324">Used to cancel activity in asynchronous controllers.</span></span>

### <a name="formcollection"></a><span data-ttu-id="1f3da-325">FormCollection</span><span class="sxs-lookup"><span data-stu-id="1f3da-325">FormCollection</span></span>

<span data-ttu-id="1f3da-326">Permet de récupérer toutes les valeurs des données de formulaire posté.</span><span class="sxs-lookup"><span data-stu-id="1f3da-326">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="1f3da-327">Formateurs d’entrée</span><span class="sxs-lookup"><span data-stu-id="1f3da-327">Input formatters</span></span>

<span data-ttu-id="1f3da-328">Les données contenues dans le corps de la requête peuvent être au format JSON, XML ou tout autre format.</span><span class="sxs-lookup"><span data-stu-id="1f3da-328">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="1f3da-329">Pour analyser ces données, la liaison de modèle utilise un *formateur d’entrée* configuré pour prendre en charge un type de contenu particulier.</span><span class="sxs-lookup"><span data-stu-id="1f3da-329">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="1f3da-330">Par défaut, ASP.NET Core inclut des formateurs d’entrée basés sur le format JSON pour prendre en charge les données JSON.</span><span class="sxs-lookup"><span data-stu-id="1f3da-330">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="1f3da-331">Vous pouvez ajouter d’autres formateurs pour d’autres types de contenu.</span><span class="sxs-lookup"><span data-stu-id="1f3da-331">You can add other formatters for other content types.</span></span>

<span data-ttu-id="1f3da-332">ASP.NET Core sélectionne les formateurs d’entrée en fonction de l’attribut [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute).</span><span class="sxs-lookup"><span data-stu-id="1f3da-332">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="1f3da-333">Si aucun attribut n’est présent, il utilise l’[en-tête Content-Type](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span><span class="sxs-lookup"><span data-stu-id="1f3da-333">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="1f3da-334">Pour utiliser les formateurs d’entrée XML intégrés :</span><span class="sxs-lookup"><span data-stu-id="1f3da-334">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="1f3da-335">Installez le package NuGet `Microsoft.AspNetCore.Mvc.Formatters.Xml`.</span><span class="sxs-lookup"><span data-stu-id="1f3da-335">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="1f3da-336">Dans `Startup.ConfigureServices`, appelez <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> ou <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span><span class="sxs-lookup"><span data-stu-id="1f3da-336">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=10)]

* <span data-ttu-id="1f3da-337">Appliquez l’attribut `Consumes` aux classes de contrôleur ou aux méthodes d’action devant contenir des données XML dans le corps de la requête.</span><span class="sxs-lookup"><span data-stu-id="1f3da-337">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="1f3da-338">Pour plus d’informations, consultez [Introduction à la sérialisation XML](/dotnet/standard/serialization/introducing-xml-serialization).</span><span class="sxs-lookup"><span data-stu-id="1f3da-338">For more information, see [Introducing XML Serialization](/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

### <a name="customize-model-binding-with-input-formatters"></a><span data-ttu-id="1f3da-339">Personnaliser la liaison de modèle avec des formateurs d’entrée</span><span class="sxs-lookup"><span data-stu-id="1f3da-339">Customize model binding with input formatters</span></span>

<span data-ttu-id="1f3da-340">Un formateur d’entrée est entièrement chargé de lire les données dans le corps de la requête.</span><span class="sxs-lookup"><span data-stu-id="1f3da-340">An input formatter takes full responsibility for reading data from the request body.</span></span> <span data-ttu-id="1f3da-341">Pour personnaliser ce processus, configurez les API utilisées par le formateur d’entrée.</span><span class="sxs-lookup"><span data-stu-id="1f3da-341">To customize this process, configure the APIs used by the input formatter.</span></span> <span data-ttu-id="1f3da-342">Cette section décrit comment personnaliser le `System.Text.Json` formateur d’entrée basé sur pour comprendre un type personnalisé nommé `ObjectId` .</span><span class="sxs-lookup"><span data-stu-id="1f3da-342">This section describes how to customize the `System.Text.Json`-based input formatter to understand a custom type named `ObjectId`.</span></span> 

<span data-ttu-id="1f3da-343">Prenons le modèle suivant, qui contient une `ObjectId` propriété personnalisée nommée `Id` :</span><span class="sxs-lookup"><span data-stu-id="1f3da-343">Consider the following model, which contains a custom `ObjectId` property named `Id`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/ModelWithObjectId.cs?name=snippet_Class&highlight=3)]

<span data-ttu-id="1f3da-344">Pour personnaliser le processus de liaison de modèle lors de l’utilisation de `System.Text.Json` , créez une classe dérivée de <xref:System.Text.Json.Serialization.JsonConverter%601> :</span><span class="sxs-lookup"><span data-stu-id="1f3da-344">To customize the model binding process when using `System.Text.Json`, create a class derived from <xref:System.Text.Json.Serialization.JsonConverter%601>:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/JsonConverters/ObjectIdConverter.cs?name=snippet_Class)]

<span data-ttu-id="1f3da-345">Pour utiliser un convertisseur personnalisé, appliquez l' <xref:System.Text.Json.Serialization.JsonConverterAttribute> attribut au type.</span><span class="sxs-lookup"><span data-stu-id="1f3da-345">To use a custom converter, apply the <xref:System.Text.Json.Serialization.JsonConverterAttribute> attribute to the type.</span></span> <span data-ttu-id="1f3da-346">Dans l’exemple suivant, le `ObjectId` type est configuré avec `ObjectIdConverter` comme son convertisseur personnalisé :</span><span class="sxs-lookup"><span data-stu-id="1f3da-346">In the following example, the `ObjectId` type is configured with `ObjectIdConverter` as its custom converter:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/ObjectId.cs?name=snippet_Class&highlight=1)]

<span data-ttu-id="1f3da-347">Pour plus d’informations, consultez [Comment écrire des convertisseurs personnalisés](/dotnet/standard/serialization/system-text-json-converters-how-to).</span><span class="sxs-lookup"><span data-stu-id="1f3da-347">For more information, see [How to write custom converters](/dotnet/standard/serialization/system-text-json-converters-how-to).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="1f3da-348">Exclure les types spécifiés de la liaison de modèle</span><span class="sxs-lookup"><span data-stu-id="1f3da-348">Exclude specified types from model binding</span></span>

<span data-ttu-id="1f3da-349">Le comportement de la liaison de modèle et du système de validation est régi par [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span><span class="sxs-lookup"><span data-stu-id="1f3da-349">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="1f3da-350">Vous pouvez personnaliser `ModelMetadata` en ajoutant un fournisseur de détails à [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span><span class="sxs-lookup"><span data-stu-id="1f3da-350">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="1f3da-351">Des fournisseurs de détails intégrés sont disponibles pour désactiver la liaison de modèle ou la validation des types spécifiés.</span><span class="sxs-lookup"><span data-stu-id="1f3da-351">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="1f3da-352">Pour désactiver la liaison de modèle sur tous les modèles d’un type spécifique, ajoutez <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="1f3da-352">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="1f3da-353">Par exemple, pour désactiver la liaison de modèle sur tous les modèles de type `System.Version` :</span><span class="sxs-lookup"><span data-stu-id="1f3da-353">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=5-6)]

<span data-ttu-id="1f3da-354">Pour désactiver la validation des propriétés d’un type spécifique, ajoutez <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="1f3da-354">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="1f3da-355">Par exemple, pour désactiver la validation sur les propriétés de type `System.Guid` :</span><span class="sxs-lookup"><span data-stu-id="1f3da-355">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=7-8)]

## <a name="custom-model-binders"></a><span data-ttu-id="1f3da-356">Lieurs de modèles personnalisés</span><span class="sxs-lookup"><span data-stu-id="1f3da-356">Custom model binders</span></span>

<span data-ttu-id="1f3da-357">Vous pouvez étendre la liaison de modèle en écrivant un lieur de modèle personnalisé et en utilisant l’attribut `[ModelBinder]` afin de le sélectionner pour une cible donnée.</span><span class="sxs-lookup"><span data-stu-id="1f3da-357">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="1f3da-358">Découvrez plus d’informations sur la [liaison de modèle personnalisée](xref:mvc/advanced/custom-model-binding).</span><span class="sxs-lookup"><span data-stu-id="1f3da-358">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="1f3da-359">Liaison de modèle manuelle</span><span class="sxs-lookup"><span data-stu-id="1f3da-359">Manual model binding</span></span> 

<span data-ttu-id="1f3da-360">Vous pouvez appeler la liaison de modèle manuellement à l’aide de la méthode <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>.</span><span class="sxs-lookup"><span data-stu-id="1f3da-360">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="1f3da-361">La méthode est définie sur les classes `ControllerBase` et `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="1f3da-361">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="1f3da-362">Les surcharges de méthode vous permettent de spécifier le préfixe et le fournisseur de valeurs à utiliser.</span><span class="sxs-lookup"><span data-stu-id="1f3da-362">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="1f3da-363">La méthode retourne `false` en cas d’échec de la liaison de modèle.</span><span class="sxs-lookup"><span data-stu-id="1f3da-363">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="1f3da-364">Voici un exemple :</span><span class="sxs-lookup"><span data-stu-id="1f3da-364">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

<span data-ttu-id="1f3da-365"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>  utilise des fournisseurs de valeurs pour obtenir des données à partir du corps du formulaire, la chaîne de requête et les données d’itinéraire.</span><span class="sxs-lookup"><span data-stu-id="1f3da-365"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>  uses value providers to get data from the form body, query string, and route data.</span></span> <span data-ttu-id="1f3da-366">`TryUpdateModelAsync` est généralement :</span><span class="sxs-lookup"><span data-stu-id="1f3da-366">`TryUpdateModelAsync` is typically:</span></span> 

* <span data-ttu-id="1f3da-367">Utilisé avec les Razor pages et les applications MVC à l’aide de contrôleurs et de vues pour empêcher la survalidation.</span><span class="sxs-lookup"><span data-stu-id="1f3da-367">Used with Razor Pages and MVC apps using controllers and views to prevent over-posting.</span></span>
* <span data-ttu-id="1f3da-368">Non utilisé avec une API Web, sauf s’il est consommé à partir des données de formulaire, des chaînes de requête et des données de routage.</span><span class="sxs-lookup"><span data-stu-id="1f3da-368">Not used with a web API unless consumed from form data, query strings, and route data.</span></span> <span data-ttu-id="1f3da-369">Les points de terminaison de l’API Web qui utilisent JSON utilisent des [formateurs d’entrée](#input-formatters) pour désérialiser le corps de la requête dans un objet.</span><span class="sxs-lookup"><span data-stu-id="1f3da-369">Web API endpoints that consume JSON use [Input formatters](#input-formatters) to deserialize the request body into an object.</span></span>

<span data-ttu-id="1f3da-370">Pour plus d’informations, consultez [TryUpdateModelAsync](xref:data/ef-rp/crud#TryUpdateModelAsync).</span><span class="sxs-lookup"><span data-stu-id="1f3da-370">For more information, see [TryUpdateModelAsync](xref:data/ef-rp/crud#TryUpdateModelAsync).</span></span>

## <a name="fromservices-attribute"></a><span data-ttu-id="1f3da-371">Attribut [FromServices]</span><span class="sxs-lookup"><span data-stu-id="1f3da-371">[FromServices] attribute</span></span>

<span data-ttu-id="1f3da-372">Le nom de cet attribut suit le modèle des attributs de liaison de modèle qui spécifient une source de données.</span><span class="sxs-lookup"><span data-stu-id="1f3da-372">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="1f3da-373">Toutefois, il ne permet pas de lier les données d’un fournisseur de valeurs.</span><span class="sxs-lookup"><span data-stu-id="1f3da-373">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="1f3da-374">Il obtient une instance d’un type à partir du conteneur d’[injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="1f3da-374">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="1f3da-375">Son objectif est de fournir une alternative à l’injection de constructeurs quand vous avez besoin d’un service uniquement si une méthode particulière est appelée.</span><span class="sxs-lookup"><span data-stu-id="1f3da-375">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1f3da-376">Ressources complémentaires</span><span class="sxs-lookup"><span data-stu-id="1f3da-376">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1f3da-377">Cet article explique ce qu’est la liaison de modèle, comment elle fonctionne et comment personnaliser son comportement.</span><span class="sxs-lookup"><span data-stu-id="1f3da-377">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="1f3da-378">[Affichez ou téléchargez un exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="1f3da-378">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="1f3da-379">Description de la liaison de modèle</span><span class="sxs-lookup"><span data-stu-id="1f3da-379">What is Model binding</span></span>

<span data-ttu-id="1f3da-380">Les contrôleurs et les Razor pages fonctionnent avec des données provenant de requêtes http.</span><span class="sxs-lookup"><span data-stu-id="1f3da-380">Controllers and Razor pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="1f3da-381">Par exemple, les données de routage peuvent fournir une clé d’enregistrement, et les champs de formulaire posté peuvent fournir des valeurs pour les propriétés du modèle.</span><span class="sxs-lookup"><span data-stu-id="1f3da-381">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="1f3da-382">L’écriture du code permettant de récupérer chacune de ces valeurs et de les convertir en types .NET à partir de chaînes est fastidieuse et source d’erreurs.</span><span class="sxs-lookup"><span data-stu-id="1f3da-382">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="1f3da-383">La liaison de modèle automatise ce processus.</span><span class="sxs-lookup"><span data-stu-id="1f3da-383">Model binding automates this process.</span></span> <span data-ttu-id="1f3da-384">Le système de liaison de modèle :</span><span class="sxs-lookup"><span data-stu-id="1f3da-384">The model binding system:</span></span>

* <span data-ttu-id="1f3da-385">Récupère les données de diverses sources telles que les données de routage, les champs de formulaire et les chaînes de requête</span><span class="sxs-lookup"><span data-stu-id="1f3da-385">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="1f3da-386">Fournit les données aux contrôleurs et aux Razor pages dans les paramètres de méthode et les propriétés publiques.</span><span class="sxs-lookup"><span data-stu-id="1f3da-386">Provides the data to controllers and Razor pages in method parameters and public properties.</span></span>
* <span data-ttu-id="1f3da-387">Convertit les données de chaîne en types .NET</span><span class="sxs-lookup"><span data-stu-id="1f3da-387">Converts string data to .NET types.</span></span>
* <span data-ttu-id="1f3da-388">Met à jour les propriétés des types complexes</span><span class="sxs-lookup"><span data-stu-id="1f3da-388">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="1f3da-389">Exemple</span><span class="sxs-lookup"><span data-stu-id="1f3da-389">Example</span></span>

<span data-ttu-id="1f3da-390">Supposons que vous ayez la méthode d’action suivante :</span><span class="sxs-lookup"><span data-stu-id="1f3da-390">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="1f3da-391">Et que l’application reçoive une requête avec l’URL suivante :</span><span class="sxs-lookup"><span data-stu-id="1f3da-391">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="1f3da-392">La liaison de modèle passe par les étapes suivantes après que le système de routage a sélectionné la méthode d’action :</span><span class="sxs-lookup"><span data-stu-id="1f3da-392">Model binding goes through the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="1f3da-393">Elle recherche le premier paramètre de `GetByID`, un entier nommé `id`.</span><span class="sxs-lookup"><span data-stu-id="1f3da-393">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="1f3da-394">Elle parcourt les sources disponibles dans la requête HTTP et trouve `id` = « 2 » dans les données de routage.</span><span class="sxs-lookup"><span data-stu-id="1f3da-394">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="1f3da-395">Elle convertit la chaîne « 2 » en entier 2.</span><span class="sxs-lookup"><span data-stu-id="1f3da-395">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="1f3da-396">Elle recherche le paramètre suivant de `GetByID`, un booléen nommé `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="1f3da-396">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="1f3da-397">Elle parcourt les sources et trouve « DogsOnly=true » dans la chaîne de requête.</span><span class="sxs-lookup"><span data-stu-id="1f3da-397">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="1f3da-398">La mise en correspondance des noms ne respecte pas la casse.</span><span class="sxs-lookup"><span data-stu-id="1f3da-398">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="1f3da-399">Elle convertit la chaîne « true » en booléen `true`.</span><span class="sxs-lookup"><span data-stu-id="1f3da-399">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="1f3da-400">Le framework appelle ensuite la méthode `GetById`, en passant 2 pour le paramètre `id`, et `true` pour le paramètre `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="1f3da-400">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="1f3da-401">Dans l’exemple précédent, les cibles de liaison de modèle sont des paramètres de méthode qui sont des types simples.</span><span class="sxs-lookup"><span data-stu-id="1f3da-401">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="1f3da-402">Les cibles peuvent être également les propriétés d’un type complexe.</span><span class="sxs-lookup"><span data-stu-id="1f3da-402">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="1f3da-403">Une fois chaque propriété correctement liée, la [validation du modèle](xref:mvc/models/validation) est effectuée pour la propriété concernée.</span><span class="sxs-lookup"><span data-stu-id="1f3da-403">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="1f3da-404">L’enregistrement des données liées au modèle (ainsi que des erreurs de liaison ou de validation) est stocké dans [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) ou [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span><span class="sxs-lookup"><span data-stu-id="1f3da-404">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="1f3da-405">Pour savoir si ce processus a abouti, l’application vérifie l’indicateur [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid).</span><span class="sxs-lookup"><span data-stu-id="1f3da-405">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="1f3da-406">Cibles</span><span class="sxs-lookup"><span data-stu-id="1f3da-406">Targets</span></span>

<span data-ttu-id="1f3da-407">La liaison de modèle tente de trouver des valeurs pour les genres de cible suivants :</span><span class="sxs-lookup"><span data-stu-id="1f3da-407">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="1f3da-408">Paramètres de la méthode d’action de contrôleur vers laquelle une requête est routée.</span><span class="sxs-lookup"><span data-stu-id="1f3da-408">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="1f3da-409">Paramètres de la Razor méthode de gestionnaire de pages vers laquelle une requête est routée.</span><span class="sxs-lookup"><span data-stu-id="1f3da-409">Parameters of the Razor Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="1f3da-410">Propriétés publiques d’un contrôleur ou d’une classe `PageModel`, si elles sont spécifiées par des attributs.</span><span class="sxs-lookup"><span data-stu-id="1f3da-410">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="1f3da-411">Attribut [BindProperty]</span><span class="sxs-lookup"><span data-stu-id="1f3da-411">[BindProperty] attribute</span></span>

<span data-ttu-id="1f3da-412">Peut être appliqué à une propriété publique d’un contrôleur ou à une classe `PageModel` pour que la liaison de modèle cible cette propriété :</span><span class="sxs-lookup"><span data-stu-id="1f3da-412">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindpropertiesattribute"></a><span data-ttu-id="1f3da-413">Attribut [BindProperties]</span><span class="sxs-lookup"><span data-stu-id="1f3da-413">[BindProperties] attribute</span></span>

<span data-ttu-id="1f3da-414">Disponible avec ASP.NET Core 2.1 et les versions ultérieures.</span><span class="sxs-lookup"><span data-stu-id="1f3da-414">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="1f3da-415">Peut être appliqué à un contrôleur ou à une classe `PageModel` pour indiquer à la liaison de modèle de cibler toutes les propriétés publiques de la classe :</span><span class="sxs-lookup"><span data-stu-id="1f3da-415">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="1f3da-416">Liaison de modèle pour les requêtes HTTP GET</span><span class="sxs-lookup"><span data-stu-id="1f3da-416">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="1f3da-417">Par défaut, les propriétés ne sont pas liées pour les requêtes HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="1f3da-417">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="1f3da-418">En règle générale, le paramètre ID d’un enregistrement est tout ce dont vous avez besoin pour une requête GET.</span><span class="sxs-lookup"><span data-stu-id="1f3da-418">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="1f3da-419">L’ID d’enregistrement est utilisé pour rechercher l’élément dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="1f3da-419">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="1f3da-420">Il n’est donc pas nécessaire de lier une propriété qui contient une instance du modèle.</span><span class="sxs-lookup"><span data-stu-id="1f3da-420">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="1f3da-421">Pour les scénarios dans lesquels vous souhaitez que les propriétés soient liées aux données provenant de requêtes GET, affectez à la propriété `SupportsGet` la valeur `true` :</span><span class="sxs-lookup"><span data-stu-id="1f3da-421">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="1f3da-422">Sources</span><span class="sxs-lookup"><span data-stu-id="1f3da-422">Sources</span></span>

<span data-ttu-id="1f3da-423">Par défaut, la liaison de modèle obtient des données sous la forme de paires clé-valeur à partir des sources suivantes dans une requête HTTP :</span><span class="sxs-lookup"><span data-stu-id="1f3da-423">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="1f3da-424">Champs de formulaire</span><span class="sxs-lookup"><span data-stu-id="1f3da-424">Form fields</span></span>
1. <span data-ttu-id="1f3da-425">Corps de la requête (pour les [contrôleurs ayant l’attribut [ApiController]](xref:web-api/index#binding-source-parameter-inference).)</span><span class="sxs-lookup"><span data-stu-id="1f3da-425">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="1f3da-426">Données de routage</span><span class="sxs-lookup"><span data-stu-id="1f3da-426">Route data</span></span>
1. <span data-ttu-id="1f3da-427">Paramètres de chaîne de requête</span><span class="sxs-lookup"><span data-stu-id="1f3da-427">Query string parameters</span></span>
1. <span data-ttu-id="1f3da-428">Fichiers chargés</span><span class="sxs-lookup"><span data-stu-id="1f3da-428">Uploaded files</span></span>

<span data-ttu-id="1f3da-429">Pour chaque paramètre ou propriété cible, les sources sont analysées dans l’ordre indiqué dans la liste précédente.</span><span class="sxs-lookup"><span data-stu-id="1f3da-429">For each target parameter or property, the sources are scanned in the order indicated in the preceding list.</span></span> <span data-ttu-id="1f3da-430">Il existe quelques exceptions :</span><span class="sxs-lookup"><span data-stu-id="1f3da-430">There are a few exceptions:</span></span>

* <span data-ttu-id="1f3da-431">Les données de routage et les valeurs de chaîne de requête sont utilisées uniquement pour les types simples.</span><span class="sxs-lookup"><span data-stu-id="1f3da-431">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="1f3da-432">Les fichiers chargés sont liés uniquement aux types cibles qui implémentent `IFormFile` ou `IEnumerable<IFormFile>`.</span><span class="sxs-lookup"><span data-stu-id="1f3da-432">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="1f3da-433">Si la source par défaut n’est pas correcte, utilisez l’un des attributs suivants pour spécifier la source :</span><span class="sxs-lookup"><span data-stu-id="1f3da-433">If the default source is not correct, use one of the following attributes to specify the source:</span></span>

* <span data-ttu-id="1f3da-434">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) -Obtient des valeurs à partir de la chaîne de requête.</span><span class="sxs-lookup"><span data-stu-id="1f3da-434">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="1f3da-435">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) -Obtient des valeurs à partir des données d’itinéraire.</span><span class="sxs-lookup"><span data-stu-id="1f3da-435">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="1f3da-436">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) -Obtient des valeurs à partir de champs de formulaire publiés.</span><span class="sxs-lookup"><span data-stu-id="1f3da-436">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="1f3da-437">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) -Obtient les valeurs du corps de la demande.</span><span class="sxs-lookup"><span data-stu-id="1f3da-437">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="1f3da-438">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) -Obtient des valeurs à partir des en-têtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="1f3da-438">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="1f3da-439">Ces attributs :</span><span class="sxs-lookup"><span data-stu-id="1f3da-439">These attributes:</span></span>

* <span data-ttu-id="1f3da-440">Sont ajoutés aux propriétés du modèle individuellement (et non à la classe de modèle), comme dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="1f3da-440">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="1f3da-441">Acceptent éventuellement une valeur de nom de modèle dans le constructeur.</span><span class="sxs-lookup"><span data-stu-id="1f3da-441">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="1f3da-442">Cette option est fournie au cas où le nom de propriété ne correspondrait pas à la valeur de la requête.</span><span class="sxs-lookup"><span data-stu-id="1f3da-442">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="1f3da-443">Par exemple, la valeur de la requête peut être un en-tête avec un trait d’union dans son nom, comme dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="1f3da-443">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="1f3da-444">Attribut [FromBody]</span><span class="sxs-lookup"><span data-stu-id="1f3da-444">[FromBody] attribute</span></span>

<span data-ttu-id="1f3da-445">Appliquez l' `[FromBody]` attribut à un paramètre pour remplir ses propriétés à partir du corps d’une requête http.</span><span class="sxs-lookup"><span data-stu-id="1f3da-445">Apply the `[FromBody]` attribute to a parameter to populate its properties from the body of an HTTP request.</span></span> <span data-ttu-id="1f3da-446">Le runtime ASP.NET Core délègue la responsabilité de lire le corps dans un formateur d’entrée.</span><span class="sxs-lookup"><span data-stu-id="1f3da-446">The ASP.NET Core runtime delegates the responsibility of reading the body to an input formatter.</span></span> <span data-ttu-id="1f3da-447">Les formateurs d’entrée sont décrits [plus loin dans cet article](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="1f3da-447">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="1f3da-448">Lorsque `[FromBody]` est appliqué à un paramètre de type complexe, tous les attributs de source de liaison appliqués à ses propriétés sont ignorés.</span><span class="sxs-lookup"><span data-stu-id="1f3da-448">When `[FromBody]` is applied to a complex type parameter, any binding source attributes applied to its properties are ignored.</span></span> <span data-ttu-id="1f3da-449">Par exemple, l' `Create` action suivante spécifie que son `pet` paramètre est rempli à partir du corps :</span><span class="sxs-lookup"><span data-stu-id="1f3da-449">For example, the following `Create` action specifies that its `pet` parameter is populated from the body:</span></span>

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

<span data-ttu-id="1f3da-450">La `Pet` classe spécifie que sa `Breed` propriété est remplie à partir d’un paramètre de chaîne de requête :</span><span class="sxs-lookup"><span data-stu-id="1f3da-450">The `Pet` class specifies that its `Breed` property is populated from a query string parameter:</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

<span data-ttu-id="1f3da-451">Dans l’exemple précédent :</span><span class="sxs-lookup"><span data-stu-id="1f3da-451">In the preceding example:</span></span>

* <span data-ttu-id="1f3da-452">L' `[FromQuery]` attribut est ignoré.</span><span class="sxs-lookup"><span data-stu-id="1f3da-452">The `[FromQuery]` attribute is ignored.</span></span>
* <span data-ttu-id="1f3da-453">La `Breed` propriété n’est pas remplie à partir d’un paramètre de chaîne de requête.</span><span class="sxs-lookup"><span data-stu-id="1f3da-453">The `Breed` property is not populated from a query string parameter.</span></span> 

<span data-ttu-id="1f3da-454">Les formateurs d’entrée lisent uniquement le corps et ne comprennent pas les attributs de source de liaison.</span><span class="sxs-lookup"><span data-stu-id="1f3da-454">Input formatters read only the body and don't understand binding source attributes.</span></span> <span data-ttu-id="1f3da-455">Si une valeur appropriée est trouvée dans le corps, cette valeur est utilisée pour remplir la `Breed` propriété.</span><span class="sxs-lookup"><span data-stu-id="1f3da-455">If a suitable value is found in the body, that value is used to populate the `Breed` property.</span></span>

<span data-ttu-id="1f3da-456">N’appliquez pas `[FromBody]` à plus d’un paramètre par méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="1f3da-456">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="1f3da-457">Une fois que le flux de requête est lu par un formateur d’entrée, il ne peut plus être lu pour la liaison d’autres `[FromBody]` paramètres.</span><span class="sxs-lookup"><span data-stu-id="1f3da-457">Once the request stream is read by an input formatter, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="1f3da-458">Sources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="1f3da-458">Additional sources</span></span>

<span data-ttu-id="1f3da-459">Les données sources sont fournies au système de liaison de modèle par les *fournisseurs de valeurs*.</span><span class="sxs-lookup"><span data-stu-id="1f3da-459">Source data is provided to the model binding system by *value providers*.</span></span> <span data-ttu-id="1f3da-460">Vous pouvez écrire et inscrire des fournisseurs de valeurs personnalisés qui obtiennent des données de liaison de modèle à partir d’autres sources.</span><span class="sxs-lookup"><span data-stu-id="1f3da-460">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="1f3da-461">Par exemple, vous pouvez obtenir des données à partir de ou de l' cookie État de session.</span><span class="sxs-lookup"><span data-stu-id="1f3da-461">For example, you might want data from cookies or session state.</span></span> <span data-ttu-id="1f3da-462">Pour obtenir des données provenant d’une nouvelle source :</span><span class="sxs-lookup"><span data-stu-id="1f3da-462">To get data from a new source:</span></span>

* <span data-ttu-id="1f3da-463">Créez une classe qui implémente `IValueProvider`.</span><span class="sxs-lookup"><span data-stu-id="1f3da-463">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="1f3da-464">Créez une classe qui implémente `IValueProviderFactory`.</span><span class="sxs-lookup"><span data-stu-id="1f3da-464">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="1f3da-465">Inscrivez la classe de fabrique dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="1f3da-465">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="1f3da-466">L’exemple d’application comprend un [fournisseur de valeur](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProvider.cs) et un exemple d' [usine](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProviderFactory.cs) qui obtient des valeurs de cookie s.</span><span class="sxs-lookup"><span data-stu-id="1f3da-466">The sample app includes a [value provider](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProvider.cs) and [factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProviderFactory.cs) example that gets values from cookies.</span></span> <span data-ttu-id="1f3da-467">Voici le code d’inscription dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="1f3da-467">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=3)]

<span data-ttu-id="1f3da-468">Le code affiché place le fournisseur de valeurs personnalisé après tous les fournisseurs de valeurs intégrés.</span><span class="sxs-lookup"><span data-stu-id="1f3da-468">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="1f3da-469">Pour en faire le premier fournisseur de la liste, appelez `Insert(0, new CookieValueProviderFactory())` à la place de `Add`.</span><span class="sxs-lookup"><span data-stu-id="1f3da-469">To make it the first in the list, call `Insert(0, new CookieValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="1f3da-470">Aucune source pour une propriété de modèle</span><span class="sxs-lookup"><span data-stu-id="1f3da-470">No source for a model property</span></span>

<span data-ttu-id="1f3da-471">Par défaut, aucune erreur d’état de modèle n’est créée, s’il n’existe aucune valeur de propriété de modèle.</span><span class="sxs-lookup"><span data-stu-id="1f3da-471">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="1f3da-472">La propriété a une valeur null ou une valeur par défaut :</span><span class="sxs-lookup"><span data-stu-id="1f3da-472">The property is set to null or a default value:</span></span>

* <span data-ttu-id="1f3da-473">Les types simples Nullable ont une valeur `null`.</span><span class="sxs-lookup"><span data-stu-id="1f3da-473">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="1f3da-474">Les types valeur non Nullable ont la valeur `default(T)`.</span><span class="sxs-lookup"><span data-stu-id="1f3da-474">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="1f3da-475">Par exemple, un paramètre `int id` a la valeur 0.</span><span class="sxs-lookup"><span data-stu-id="1f3da-475">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="1f3da-476">Pour les types complexes, la liaison de modèle crée une instance à l’aide du constructeur par défaut, sans définir de propriétés.</span><span class="sxs-lookup"><span data-stu-id="1f3da-476">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="1f3da-477">Les tableaux ont la valeur `Array.Empty<T>()`, sauf les tableaux `byte[]` qui ont une valeur `null`.</span><span class="sxs-lookup"><span data-stu-id="1f3da-477">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="1f3da-478">Si l’état du modèle doit être invalidé lorsque rien n’est trouvé dans les champs de formulaire d’une propriété de modèle, utilisez l' [`[BindRequired]`](#bindrequired-attribute) attribut.</span><span class="sxs-lookup"><span data-stu-id="1f3da-478">If model state should be invalidated when nothing is found in form fields for a model property, use the [`[BindRequired]`](#bindrequired-attribute) attribute.</span></span>

<span data-ttu-id="1f3da-479">Notez que ce comportement de `[BindRequired]` s’applique à la liaison de modèle des données de formulaire postées, et non aux données JSON ou XML d’un corps de requête.</span><span class="sxs-lookup"><span data-stu-id="1f3da-479">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="1f3da-480">Les données du corps de requête sont prises en charge par les [formateurs d’entrée](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="1f3da-480">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="1f3da-481">Erreurs de conversion de type</span><span class="sxs-lookup"><span data-stu-id="1f3da-481">Type conversion errors</span></span>

<span data-ttu-id="1f3da-482">Si une source est localisée mais qu’elle ne peut pas être convertie vers le type cible, l’état du modèle est marqué comme étant non valide.</span><span class="sxs-lookup"><span data-stu-id="1f3da-482">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="1f3da-483">Le paramètre ou la propriété cible a une valeur null ou une valeur par défaut, comme indiqué dans la section précédente.</span><span class="sxs-lookup"><span data-stu-id="1f3da-483">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="1f3da-484">Dans un contrôleur d’API ayant l’attribut `[ApiController]`, un état de modèle non valide entraîne une réponse HTTP 400 automatique.</span><span class="sxs-lookup"><span data-stu-id="1f3da-484">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="1f3da-485">Dans une Razor page, réaffichez la page avec un message d’erreur :</span><span class="sxs-lookup"><span data-stu-id="1f3da-485">In a Razor page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="1f3da-486">La validation côté client intercepte la plupart des données incorrectes qui seraient autrement soumises à un Razor formulaire de pages.</span><span class="sxs-lookup"><span data-stu-id="1f3da-486">Client-side validation catches most bad data that would otherwise be submitted to a Razor Pages form.</span></span> <span data-ttu-id="1f3da-487">Cette validation rend difficile le déclenchement du code en surbrillance indiqué plus haut.</span><span class="sxs-lookup"><span data-stu-id="1f3da-487">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="1f3da-488">L’exemple d’application comprend un bouton **Submit with Invalid Date** (Envoyer avec une date non valide), qui place les données incorrectes dans le champ **Hire Date** (Date d’embauche) et envoie le formulaire.</span><span class="sxs-lookup"><span data-stu-id="1f3da-488">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="1f3da-489">Ce bouton montre comment fonctionne le code permettant de réafficher la page quand des erreurs de conversion de données se produisent.</span><span class="sxs-lookup"><span data-stu-id="1f3da-489">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="1f3da-490">Quand la page est réaffichée par le code précédent, l’entrée non valide n’est pas visible dans le champ de formulaire.</span><span class="sxs-lookup"><span data-stu-id="1f3da-490">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="1f3da-491">En effet, la propriété de modèle à une valeur null ou une valeur par défaut.</span><span class="sxs-lookup"><span data-stu-id="1f3da-491">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="1f3da-492">L’entrée non valide apparaît dans un message d’erreur.</span><span class="sxs-lookup"><span data-stu-id="1f3da-492">The invalid input does appear in an error message.</span></span> <span data-ttu-id="1f3da-493">Toutefois, si vous souhaitez réafficher les données incorrectes dans le champ de formulaire, transformez la propriété de modèle en chaîne et procédez à la conversion des données manuellement.</span><span class="sxs-lookup"><span data-stu-id="1f3da-493">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="1f3da-494">La même stratégie est recommandée si vous ne souhaitez pas que les erreurs de conversion de type entraînent des erreurs d’état de modèle.</span><span class="sxs-lookup"><span data-stu-id="1f3da-494">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="1f3da-495">Dans ce cas, transformez la propriété de modèle en chaîne.</span><span class="sxs-lookup"><span data-stu-id="1f3da-495">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="1f3da-496">Types simples</span><span class="sxs-lookup"><span data-stu-id="1f3da-496">Simple types</span></span>

<span data-ttu-id="1f3da-497">Les types simples que le lieur de modèle peut convertir en chaînes sources sont les suivants :</span><span class="sxs-lookup"><span data-stu-id="1f3da-497">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="1f3da-498">Booléen</span><span class="sxs-lookup"><span data-stu-id="1f3da-498">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="1f3da-499">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="1f3da-499">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="1f3da-500">Char</span><span class="sxs-lookup"><span data-stu-id="1f3da-500">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="1f3da-501">DateTime</span><span class="sxs-lookup"><span data-stu-id="1f3da-501">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="1f3da-502">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="1f3da-502">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="1f3da-503">Décimal</span><span class="sxs-lookup"><span data-stu-id="1f3da-503">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="1f3da-504">Double</span><span class="sxs-lookup"><span data-stu-id="1f3da-504">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="1f3da-505">Variables</span><span class="sxs-lookup"><span data-stu-id="1f3da-505">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="1f3da-506">Uniques</span><span class="sxs-lookup"><span data-stu-id="1f3da-506">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="1f3da-507">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="1f3da-507">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="1f3da-508">Unique</span><span class="sxs-lookup"><span data-stu-id="1f3da-508">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="1f3da-509">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="1f3da-509">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="1f3da-510">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="1f3da-510">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="1f3da-511">Uri</span><span class="sxs-lookup"><span data-stu-id="1f3da-511">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="1f3da-512">Version</span><span class="sxs-lookup"><span data-stu-id="1f3da-512">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="1f3da-513">Types complexes</span><span class="sxs-lookup"><span data-stu-id="1f3da-513">Complex types</span></span>

<span data-ttu-id="1f3da-514">Un type complexe doit avoir un constructeur public par défaut et des propriétés publiques accessibles en écriture à lier.</span><span class="sxs-lookup"><span data-stu-id="1f3da-514">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="1f3da-515">Quand la liaison de modèle se produit, la classe est instanciée à l’aide du constructeur public par défaut.</span><span class="sxs-lookup"><span data-stu-id="1f3da-515">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="1f3da-516">Pour chaque propriété du type complexe, la liaison de modèle recherche dans les sources le modèle de nom *préfixe.nom_propriété*.</span><span class="sxs-lookup"><span data-stu-id="1f3da-516">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name*.</span></span> <span data-ttu-id="1f3da-517">Si rien n’est trouvé, elle recherche uniquement *nom_propriété* sans le préfixe.</span><span class="sxs-lookup"><span data-stu-id="1f3da-517">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="1f3da-518">Dans le cas d’une liaison à un paramètre, le préfixe représente le nom du paramètre.</span><span class="sxs-lookup"><span data-stu-id="1f3da-518">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="1f3da-519">Dans le cas d’une liaison à une propriété publique `PageModel`, le préfixe représente le nom de la propriété publique.</span><span class="sxs-lookup"><span data-stu-id="1f3da-519">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="1f3da-520">Certains attributs ont une propriété `Prefix` qui vous permet de remplacer l’utilisation par défaut du nom de paramètre ou de propriété.</span><span class="sxs-lookup"><span data-stu-id="1f3da-520">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="1f3da-521">Par exemple, supposons que le type complexe corresponde à la classe `Instructor` suivante :</span><span class="sxs-lookup"><span data-stu-id="1f3da-521">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="1f3da-522">Préfixe = nom de paramètre</span><span class="sxs-lookup"><span data-stu-id="1f3da-522">Prefix = parameter name</span></span>

<span data-ttu-id="1f3da-523">Si le modèle à lier est un paramètre nommé `instructorToUpdate` :</span><span class="sxs-lookup"><span data-stu-id="1f3da-523">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="1f3da-524">La liaison de modèle commence par rechercher dans les sources la clé `instructorToUpdate.ID`.</span><span class="sxs-lookup"><span data-stu-id="1f3da-524">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="1f3da-525">Si elle est introuvable, elle recherche `ID` sans préfixe.</span><span class="sxs-lookup"><span data-stu-id="1f3da-525">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="1f3da-526">Préfixe = nom de propriété</span><span class="sxs-lookup"><span data-stu-id="1f3da-526">Prefix = property name</span></span>

<span data-ttu-id="1f3da-527">Si le modèle à lier est une propriété nommée `Instructor` du contrôleur ou de la classe `PageModel` :</span><span class="sxs-lookup"><span data-stu-id="1f3da-527">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="1f3da-528">La liaison de modèle commence par rechercher dans les sources la clé `Instructor.ID`.</span><span class="sxs-lookup"><span data-stu-id="1f3da-528">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="1f3da-529">Si elle est introuvable, elle recherche `ID` sans préfixe.</span><span class="sxs-lookup"><span data-stu-id="1f3da-529">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="1f3da-530">Préfixe personnalisé</span><span class="sxs-lookup"><span data-stu-id="1f3da-530">Custom prefix</span></span>

<span data-ttu-id="1f3da-531">Si le modèle à lier est un paramètre nommé `instructorToUpdate` et si un attribut `Bind` spécifie `Instructor` en tant que préfixe :</span><span class="sxs-lookup"><span data-stu-id="1f3da-531">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="1f3da-532">La liaison de modèle commence par rechercher dans les sources la clé `Instructor.ID`.</span><span class="sxs-lookup"><span data-stu-id="1f3da-532">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="1f3da-533">Si elle est introuvable, elle recherche `ID` sans préfixe.</span><span class="sxs-lookup"><span data-stu-id="1f3da-533">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="1f3da-534">Attributs des cibles de type complexe</span><span class="sxs-lookup"><span data-stu-id="1f3da-534">Attributes for complex type targets</span></span>

<span data-ttu-id="1f3da-535">Plusieurs attributs intégrés sont disponibles pour contrôler la liaison de modèle des types complexes :</span><span class="sxs-lookup"><span data-stu-id="1f3da-535">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[BindRequired]`
* `[BindNever]`
* `[Bind]`

> [!NOTE]
> <span data-ttu-id="1f3da-536">Ces attributs affectent la liaison de modèle quand les données de formulaire postées représentent la source des valeurs.</span><span class="sxs-lookup"><span data-stu-id="1f3da-536">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="1f3da-537">Ils n’affectent pas les formateurs d’entrée, qui traitent les corps de requête JSON et XML postés.</span><span class="sxs-lookup"><span data-stu-id="1f3da-537">They do not affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="1f3da-538">Les formateurs d’entrée sont décrits [plus loin dans cet article](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="1f3da-538">Input formatters are explained [later in this article](#input-formatters).</span></span>
>
> <span data-ttu-id="1f3da-539">Consultez également la discussion sur l’attribut `[Required]` dans [Validation de modèle](xref:mvc/models/validation#required-attribute).</span><span class="sxs-lookup"><span data-stu-id="1f3da-539">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindrequired-attribute"></a><span data-ttu-id="1f3da-540">Attribut [BindRequired]</span><span class="sxs-lookup"><span data-stu-id="1f3da-540">[BindRequired] attribute</span></span>

<span data-ttu-id="1f3da-541">Il s’applique uniquement aux propriétés de modèle, pas aux paramètres de méthode.</span><span class="sxs-lookup"><span data-stu-id="1f3da-541">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="1f3da-542">Il oblige la liaison de modèle à ajouter une erreur d’état de modèle si la liaison est impossible pour la propriété d’un modèle.</span><span class="sxs-lookup"><span data-stu-id="1f3da-542">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="1f3da-543">Voici un exemple :</span><span class="sxs-lookup"><span data-stu-id="1f3da-543">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

### <a name="bindnever-attribute"></a><span data-ttu-id="1f3da-544">Attribut [BindNever]</span><span class="sxs-lookup"><span data-stu-id="1f3da-544">[BindNever] attribute</span></span>

<span data-ttu-id="1f3da-545">Il s’applique uniquement aux propriétés de modèle, pas aux paramètres de méthode.</span><span class="sxs-lookup"><span data-stu-id="1f3da-545">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="1f3da-546">Il empêche la liaison de modèle de définir la propriété d’un modèle.</span><span class="sxs-lookup"><span data-stu-id="1f3da-546">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="1f3da-547">Voici un exemple :</span><span class="sxs-lookup"><span data-stu-id="1f3da-547">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

### <a name="bind-attribute"></a><span data-ttu-id="1f3da-548">Attribut [Bind]</span><span class="sxs-lookup"><span data-stu-id="1f3da-548">[Bind] attribute</span></span>

<span data-ttu-id="1f3da-549">Il peut être appliqué à une classe ou à un paramètre de méthode.</span><span class="sxs-lookup"><span data-stu-id="1f3da-549">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="1f3da-550">Il spécifie les propriétés d’un modèle à inclure dans la liaison de modèle.</span><span class="sxs-lookup"><span data-stu-id="1f3da-550">Specifies which properties of a model should be included in model binding.</span></span>

<span data-ttu-id="1f3da-551">Dans l’exemple suivant, seules les propriétés spécifiées du modèle `Instructor` sont liées quand une méthode de gestionnaire ou une méthode d’action est appelée :</span><span class="sxs-lookup"><span data-stu-id="1f3da-551">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="1f3da-552">Dans l’exemple suivant, seules les propriétés spécifiées du modèle `Instructor` sont liées quand la méthode `OnPost` est appelée :</span><span class="sxs-lookup"><span data-stu-id="1f3da-552">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="1f3da-553">Vous pouvez utiliser l’attribut `[Bind]` pour éviter le surpostage dans les scénarios de *création*.</span><span class="sxs-lookup"><span data-stu-id="1f3da-553">The `[Bind]` attribute can be used to protect against overposting in *create* scenarios.</span></span> <span data-ttu-id="1f3da-554">Il ne fonctionne pas bien dans les scénarios de modification, car les propriétés exclues ont une valeur null ou une valeur par défaut au lieu de rester inchangées.</span><span class="sxs-lookup"><span data-stu-id="1f3da-554">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="1f3da-555">Pour empêcher le surpostage, il est recommandé d’utiliser des modèles de vues à la place de l’attribut `[Bind]`.</span><span class="sxs-lookup"><span data-stu-id="1f3da-555">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="1f3da-556">Pour plus d’informations, consultez [Remarque sur la sécurité concernant le surpostage](xref:data/ef-mvc/crud#security-note-about-overposting).</span><span class="sxs-lookup"><span data-stu-id="1f3da-556">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

## <a name="collections"></a><span data-ttu-id="1f3da-557">Collections</span><span class="sxs-lookup"><span data-stu-id="1f3da-557">Collections</span></span>

<span data-ttu-id="1f3da-558">Pour les cibles qui sont des collections de types simples, la liaison de modèle recherche les correspondances avec *nom_paramètre* ou *nom_propriété*.</span><span class="sxs-lookup"><span data-stu-id="1f3da-558">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="1f3da-559">Si aucune correspondance n’est localisée, elle recherche l’un des formats pris en charge sans le préfixe.</span><span class="sxs-lookup"><span data-stu-id="1f3da-559">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="1f3da-560">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="1f3da-560">For example:</span></span>

* <span data-ttu-id="1f3da-561">Supposons que le paramètre à lier soit un tableau nommé `selectedCourses` :</span><span class="sxs-lookup"><span data-stu-id="1f3da-561">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="1f3da-562">Les données de formulaire ou de chaîne de requête peuvent avoir l’un des formats suivants :</span><span class="sxs-lookup"><span data-stu-id="1f3da-562">Form or query string data can be in one of the following formats:</span></span>
   
  ```
  selectedCourses=1050&selectedCourses=2000 
  ```

  ```
  selectedCourses[0]=1050&selectedCourses[1]=2000
  ```

  ```
  [0]=1050&[1]=2000
  ```

  ```
  selectedCourses[a]=1050&selectedCourses[b]=2000&selectedCourses.index=a&selectedCourses.index=b
  ```

  ```
  [a]=1050&[b]=2000&index=a&index=b
  ```

* <span data-ttu-id="1f3da-563">Le format suivant est pris en charge uniquement dans les données de formulaire :</span><span class="sxs-lookup"><span data-stu-id="1f3da-563">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="1f3da-564">Pour tous les exemples de formats précédents, la liaison de modèle passe un tableau de deux éléments au paramètre `selectedCourses` :</span><span class="sxs-lookup"><span data-stu-id="1f3da-564">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="1f3da-565">selectedCourses[0]=1050</span><span class="sxs-lookup"><span data-stu-id="1f3da-565">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="1f3da-566">selectedCourses[1]=2000</span><span class="sxs-lookup"><span data-stu-id="1f3da-566">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="1f3da-567">Les formats de données qui utilisent des nombres en indice (... [0] ... [1] ...) doivent être impérativement numérotés de manière séquentielle à partir de zéro.</span><span class="sxs-lookup"><span data-stu-id="1f3da-567">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="1f3da-568">S’il existe des vides dans la numérotation en indice, tous les éléments suivants sont ignorés.</span><span class="sxs-lookup"><span data-stu-id="1f3da-568">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="1f3da-569">Par exemple, si les indices sont 0 et 2 au lieu de 0 et 1, le second élément est ignoré.</span><span class="sxs-lookup"><span data-stu-id="1f3da-569">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="1f3da-570">Dictionnaires</span><span class="sxs-lookup"><span data-stu-id="1f3da-570">Dictionaries</span></span>

<span data-ttu-id="1f3da-571">Pour les cibles `Dictionary`, la liaison de modèle recherche les correspondances avec *nom_paramètre* ou *nom_propriété*.</span><span class="sxs-lookup"><span data-stu-id="1f3da-571">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="1f3da-572">Si aucune correspondance n’est localisée, elle recherche l’un des formats pris en charge sans le préfixe.</span><span class="sxs-lookup"><span data-stu-id="1f3da-572">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="1f3da-573">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="1f3da-573">For example:</span></span>

* <span data-ttu-id="1f3da-574">Supposons que le paramètre cible soit un `Dictionary<int, string>` nommé `selectedCourses` :</span><span class="sxs-lookup"><span data-stu-id="1f3da-574">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="1f3da-575">Les données de chaîne de requête ou de formulaire posté peuvent ressembler à l’un des exemples suivants :</span><span class="sxs-lookup"><span data-stu-id="1f3da-575">The posted form or query string data can look like one of the following examples:</span></span>

  ```
  selectedCourses[1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  [1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  selectedCourses[0].Key=1050&selectedCourses[0].Value=Chemistry&
  selectedCourses[1].Key=2000&selectedCourses[1].Value=Economics
  ```

  ```
  [0].Key=1050&[0].Value=Chemistry&[1].Key=2000&[1].Value=Economics
  ```

* <span data-ttu-id="1f3da-576">Pour tous les exemples de formats précédents, la liaison de modèle passe un dictionnaire de deux éléments au paramètre `selectedCourses` :</span><span class="sxs-lookup"><span data-stu-id="1f3da-576">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="1f3da-577">selectedCourses["1050"]="Chemistry"</span><span class="sxs-lookup"><span data-stu-id="1f3da-577">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="1f3da-578">selectedCourses["2000"]="Economics"</span><span class="sxs-lookup"><span data-stu-id="1f3da-578">selectedCourses["2000"]="Economics"</span></span>

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a><span data-ttu-id="1f3da-579">Comportement de globalisation des données de routage de liaison de modèle et des chaînes de requête</span><span class="sxs-lookup"><span data-stu-id="1f3da-579">Globalization behavior of model binding route data and query strings</span></span>

<span data-ttu-id="1f3da-580">Fournisseur de valeurs d’itinéraire ASP.NET Core et fournisseur de valeur de chaîne de requête :</span><span class="sxs-lookup"><span data-stu-id="1f3da-580">The ASP.NET Core route value provider and query string value provider:</span></span>

* <span data-ttu-id="1f3da-581">Traitez les valeurs comme une culture dite indifférente.</span><span class="sxs-lookup"><span data-stu-id="1f3da-581">Treat values as invariant culture.</span></span>
* <span data-ttu-id="1f3da-582">Attendez-vous à ce que les URL soient invariantes de culture.</span><span class="sxs-lookup"><span data-stu-id="1f3da-582">Expect that URLs are culture-invariant.</span></span>

<span data-ttu-id="1f3da-583">En revanche, les valeurs provenant des données de formulaire subissent une conversion dépendante de la culture.</span><span class="sxs-lookup"><span data-stu-id="1f3da-583">In contrast, values coming from form data undergo a culture-sensitive conversion.</span></span> <span data-ttu-id="1f3da-584">Cela est dû au fait que les URL peuvent être partagées entre les paramètres régionaux.</span><span class="sxs-lookup"><span data-stu-id="1f3da-584">This is by design so that URLs are shareable across locales.</span></span>

<span data-ttu-id="1f3da-585">Pour que le fournisseur de valeurs d’itinéraire ASP.NET Core et le fournisseur de valeurs de chaîne de requête soient soumis à une conversion dépendante de la culture :</span><span class="sxs-lookup"><span data-stu-id="1f3da-585">To make the ASP.NET Core route value provider and query string value provider undergo a culture-sensitive conversion:</span></span>

* <span data-ttu-id="1f3da-586">Héritent de <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span><span class="sxs-lookup"><span data-stu-id="1f3da-586">Inherit from <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span></span>
* <span data-ttu-id="1f3da-587">Copiez le code à partir de [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) ou [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span><span class="sxs-lookup"><span data-stu-id="1f3da-587">Copy the code from [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) or [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span></span>
* <span data-ttu-id="1f3da-588">Remplacer la [valeur de culture](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passée au constructeur de fournisseur de valeur par [CultureInfo. CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span><span class="sxs-lookup"><span data-stu-id="1f3da-588">Replace the [culture value](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passed to the value provider constructor with [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span></span>
* <span data-ttu-id="1f3da-589">Remplacez la fabrique de fournisseur de valeur par défaut dans les options MVC par la nouvelle.</span><span class="sxs-lookup"><span data-stu-id="1f3da-589">Replace the default value provider factory in MVC options with your new one:</span></span>

[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a><span data-ttu-id="1f3da-590">Types de données spéciaux</span><span class="sxs-lookup"><span data-stu-id="1f3da-590">Special data types</span></span>

<span data-ttu-id="1f3da-591">Certains types de données spéciaux peuvent être pris en charge par la liaison de modèle.</span><span class="sxs-lookup"><span data-stu-id="1f3da-591">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="1f3da-592">IFormFile et IFormFileCollection</span><span class="sxs-lookup"><span data-stu-id="1f3da-592">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="1f3da-593">Fichier chargé inclus dans la requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="1f3da-593">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="1f3da-594">`IEnumerable<IFormFile>` est également pris en charge pour plusieurs fichiers.</span><span class="sxs-lookup"><span data-stu-id="1f3da-594">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="1f3da-595">CancellationToken</span><span class="sxs-lookup"><span data-stu-id="1f3da-595">CancellationToken</span></span>

<span data-ttu-id="1f3da-596">utilisé pour annuler l’activité dans les contrôleurs asynchrones.</span><span class="sxs-lookup"><span data-stu-id="1f3da-596">Used to cancel activity in asynchronous controllers.</span></span>

### <a name="formcollection"></a><span data-ttu-id="1f3da-597">FormCollection</span><span class="sxs-lookup"><span data-stu-id="1f3da-597">FormCollection</span></span>

<span data-ttu-id="1f3da-598">Permet de récupérer toutes les valeurs des données de formulaire posté.</span><span class="sxs-lookup"><span data-stu-id="1f3da-598">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="1f3da-599">Formateurs d’entrée</span><span class="sxs-lookup"><span data-stu-id="1f3da-599">Input formatters</span></span>

<span data-ttu-id="1f3da-600">Les données contenues dans le corps de la requête peuvent être au format JSON, XML ou tout autre format.</span><span class="sxs-lookup"><span data-stu-id="1f3da-600">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="1f3da-601">Pour analyser ces données, la liaison de modèle utilise un *formateur d’entrée* configuré pour prendre en charge un type de contenu particulier.</span><span class="sxs-lookup"><span data-stu-id="1f3da-601">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="1f3da-602">Par défaut, ASP.NET Core inclut des formateurs d’entrée basés sur le format JSON pour prendre en charge les données JSON.</span><span class="sxs-lookup"><span data-stu-id="1f3da-602">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="1f3da-603">Vous pouvez ajouter d’autres formateurs pour d’autres types de contenu.</span><span class="sxs-lookup"><span data-stu-id="1f3da-603">You can add other formatters for other content types.</span></span>

<span data-ttu-id="1f3da-604">ASP.NET Core sélectionne les formateurs d’entrée en fonction de l’attribut [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute).</span><span class="sxs-lookup"><span data-stu-id="1f3da-604">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="1f3da-605">Si aucun attribut n’est présent, il utilise l’[en-tête Content-Type](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span><span class="sxs-lookup"><span data-stu-id="1f3da-605">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="1f3da-606">Pour utiliser les formateurs d’entrée XML intégrés :</span><span class="sxs-lookup"><span data-stu-id="1f3da-606">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="1f3da-607">Installez le package NuGet `Microsoft.AspNetCore.Mvc.Formatters.Xml`.</span><span class="sxs-lookup"><span data-stu-id="1f3da-607">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="1f3da-608">Dans `Startup.ConfigureServices`, appelez <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> ou <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span><span class="sxs-lookup"><span data-stu-id="1f3da-608">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=9)]

* <span data-ttu-id="1f3da-609">Appliquez l’attribut `Consumes` aux classes de contrôleur ou aux méthodes d’action devant contenir des données XML dans le corps de la requête.</span><span class="sxs-lookup"><span data-stu-id="1f3da-609">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="1f3da-610">Pour plus d’informations, consultez [Introduction à la sérialisation XML](/dotnet/standard/serialization/introducing-xml-serialization).</span><span class="sxs-lookup"><span data-stu-id="1f3da-610">For more information, see [Introducing XML Serialization](/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="1f3da-611">Exclure les types spécifiés de la liaison de modèle</span><span class="sxs-lookup"><span data-stu-id="1f3da-611">Exclude specified types from model binding</span></span>

<span data-ttu-id="1f3da-612">Le comportement de la liaison de modèle et du système de validation est régi par [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span><span class="sxs-lookup"><span data-stu-id="1f3da-612">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="1f3da-613">Vous pouvez personnaliser `ModelMetadata` en ajoutant un fournisseur de détails à [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span><span class="sxs-lookup"><span data-stu-id="1f3da-613">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="1f3da-614">Des fournisseurs de détails intégrés sont disponibles pour désactiver la liaison de modèle ou la validation des types spécifiés.</span><span class="sxs-lookup"><span data-stu-id="1f3da-614">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="1f3da-615">Pour désactiver la liaison de modèle sur tous les modèles d’un type spécifique, ajoutez <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="1f3da-615">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="1f3da-616">Par exemple, pour désactiver la liaison de modèle sur tous les modèles de type `System.Version` :</span><span class="sxs-lookup"><span data-stu-id="1f3da-616">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4-5)]

<span data-ttu-id="1f3da-617">Pour désactiver la validation des propriétés d’un type spécifique, ajoutez <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="1f3da-617">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="1f3da-618">Par exemple, pour désactiver la validation sur les propriétés de type `System.Guid` :</span><span class="sxs-lookup"><span data-stu-id="1f3da-618">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=6-7)]

## <a name="custom-model-binders"></a><span data-ttu-id="1f3da-619">Lieurs de modèles personnalisés</span><span class="sxs-lookup"><span data-stu-id="1f3da-619">Custom model binders</span></span>

<span data-ttu-id="1f3da-620">Vous pouvez étendre la liaison de modèle en écrivant un lieur de modèle personnalisé et en utilisant l’attribut `[ModelBinder]` afin de le sélectionner pour une cible donnée.</span><span class="sxs-lookup"><span data-stu-id="1f3da-620">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="1f3da-621">Découvrez plus d’informations sur la [liaison de modèle personnalisée](xref:mvc/advanced/custom-model-binding).</span><span class="sxs-lookup"><span data-stu-id="1f3da-621">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="1f3da-622">Liaison de modèle manuelle</span><span class="sxs-lookup"><span data-stu-id="1f3da-622">Manual model binding</span></span>

<span data-ttu-id="1f3da-623">Vous pouvez appeler la liaison de modèle manuellement à l’aide de la méthode <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>.</span><span class="sxs-lookup"><span data-stu-id="1f3da-623">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="1f3da-624">La méthode est définie sur les classes `ControllerBase` et `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="1f3da-624">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="1f3da-625">Les surcharges de méthode vous permettent de spécifier le préfixe et le fournisseur de valeurs à utiliser.</span><span class="sxs-lookup"><span data-stu-id="1f3da-625">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="1f3da-626">La méthode retourne `false` en cas d’échec de la liaison de modèle.</span><span class="sxs-lookup"><span data-stu-id="1f3da-626">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="1f3da-627">Voici un exemple :</span><span class="sxs-lookup"><span data-stu-id="1f3da-627">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

## <a name="fromservices-attribute"></a><span data-ttu-id="1f3da-628">Attribut [FromServices]</span><span class="sxs-lookup"><span data-stu-id="1f3da-628">[FromServices] attribute</span></span>

<span data-ttu-id="1f3da-629">Le nom de cet attribut suit le modèle des attributs de liaison de modèle qui spécifient une source de données.</span><span class="sxs-lookup"><span data-stu-id="1f3da-629">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="1f3da-630">Toutefois, il ne permet pas de lier les données d’un fournisseur de valeurs.</span><span class="sxs-lookup"><span data-stu-id="1f3da-630">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="1f3da-631">Il obtient une instance d’un type à partir du conteneur d’[injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="1f3da-631">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="1f3da-632">Son objectif est de fournir une alternative à l’injection de constructeurs quand vous avez besoin d’un service uniquement si une méthode particulière est appelée.</span><span class="sxs-lookup"><span data-stu-id="1f3da-632">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1f3da-633">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="1f3da-633">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end
