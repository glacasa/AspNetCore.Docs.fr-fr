---
title: Créer des API web avec ASP.NET Core
author: scottaddie
description: Découvrez les principes fondamentaux de la création d’une API web dans ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 02/02/2020
uid: web-api/index
ms.openlocfilehash: be88b8d58f1f660f3a815c395c210c05a7b4917c
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78666004"
---
# <a name="create-web-apis-with-aspnet-core"></a><span data-ttu-id="fee10-103">Créer des API web avec ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fee10-103">Create web APIs with ASP.NET Core</span></span>

<span data-ttu-id="fee10-104">De [Scott Addie](https://github.com/scottaddie) et [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="fee10-104">By [Scott Addie](https://github.com/scottaddie) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="fee10-105">ASP.NET Core prend en charge la création de services RESTful, également appelés API web, à l’aide de C#.</span><span class="sxs-lookup"><span data-stu-id="fee10-105">ASP.NET Core supports creating RESTful services, also known as web APIs, using C#.</span></span> <span data-ttu-id="fee10-106">Pour traiter les demandes, une API web utilise des contrôleurs.</span><span class="sxs-lookup"><span data-stu-id="fee10-106">To handle requests, a web API uses controllers.</span></span> <span data-ttu-id="fee10-107">Les *contrôleurs* dans une API web sont des classes qui dérivent de `ControllerBase`.</span><span class="sxs-lookup"><span data-stu-id="fee10-107">*Controllers* in a web API are classes that derive from `ControllerBase`.</span></span> <span data-ttu-id="fee10-108">Cet article montre comment utiliser les contrôleurs pour traiter les demandes d’API Web.</span><span class="sxs-lookup"><span data-stu-id="fee10-108">This article shows how to use controllers for handling web API requests.</span></span>

<span data-ttu-id="fee10-109">[Affichez ou téléchargez l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/index/samples).</span><span class="sxs-lookup"><span data-stu-id="fee10-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/index/samples).</span></span> <span data-ttu-id="fee10-110">([Comment télécharger](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="fee10-110">([How to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="controllerbase-class"></a><span data-ttu-id="fee10-111">Classe ControllerBase</span><span class="sxs-lookup"><span data-stu-id="fee10-111">ControllerBase class</span></span>

<span data-ttu-id="fee10-112">Une API web se compose d’une <xref:Microsoft.AspNetCore.Mvc.ControllerBase>ou plusieurs classes de contrôleur qui dérivent de .</span><span class="sxs-lookup"><span data-stu-id="fee10-112">A web API consists of one or more controller classes that derive from <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="fee10-113">Le modèle de projet web API fournit un contrôleur de démarrage :</span><span class="sxs-lookup"><span data-stu-id="fee10-113">The web API project template provides a starter controller:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=3)]

::: moniker-end

<span data-ttu-id="fee10-114">Ne créez pas de contrôleur d’API web en effectuant une dérivation de la classe <xref:Microsoft.AspNetCore.Mvc.Controller>.</span><span class="sxs-lookup"><span data-stu-id="fee10-114">Don't create a web API controller by deriving from the <xref:Microsoft.AspNetCore.Mvc.Controller> class.</span></span> <span data-ttu-id="fee10-115">`Controller` dérive de `ControllerBase` et ajoute la prise en charge pour les vues ; ainsi, il est destiné à la gestion des pages web, pas des demandes d’API web.</span><span class="sxs-lookup"><span data-stu-id="fee10-115">`Controller` derives from `ControllerBase` and adds support for views, so it's for handling web pages, not web API requests.</span></span> <span data-ttu-id="fee10-116">Il ya une exception à cette règle: si vous prévoyez d’utiliser le même `Controller`contrôleur pour les vues et les API web, le tirer de .</span><span class="sxs-lookup"><span data-stu-id="fee10-116">There's an exception to this rule: if you plan to use the same controller for both views and web APIs, derive it from `Controller`.</span></span>

<span data-ttu-id="fee10-117">La classe `ControllerBase` fournit de nombreuses propriétés et méthodes qui sont utiles pour gérer les requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="fee10-117">The `ControllerBase` class provides many properties and methods that are useful for handling HTTP requests.</span></span> <span data-ttu-id="fee10-118">Par exemple, `ControllerBase.CreatedAtAction` retourne un code d’état 201 :</span><span class="sxs-lookup"><span data-stu-id="fee10-118">For example, `ControllerBase.CreatedAtAction` returns a 201 status code:</span></span>

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_400And201&highlight=10)]

<span data-ttu-id="fee10-119">Voici d’autres exemples de méthodes fournies par `ControllerBase`.</span><span class="sxs-lookup"><span data-stu-id="fee10-119">Here are some more examples of methods that `ControllerBase` provides.</span></span>

|<span data-ttu-id="fee10-120">Méthode</span><span class="sxs-lookup"><span data-stu-id="fee10-120">Method</span></span>   |<span data-ttu-id="fee10-121">Notes</span><span class="sxs-lookup"><span data-stu-id="fee10-121">Notes</span></span>    |
|---------|---------|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest%2A>| <span data-ttu-id="fee10-122">Retourne le code d’état 400.</span><span class="sxs-lookup"><span data-stu-id="fee10-122">Returns 400 status code.</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A>|<span data-ttu-id="fee10-123">Retourne le code d’état 404.</span><span class="sxs-lookup"><span data-stu-id="fee10-123">Returns 404 status code.</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.PhysicalFile%2A>|<span data-ttu-id="fee10-124">Retourne un fichier.</span><span class="sxs-lookup"><span data-stu-id="fee10-124">Returns a file.</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync%2A>|<span data-ttu-id="fee10-125">Appelle la [liaison de modèle](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="fee10-125">Invokes [model binding](xref:mvc/models/model-binding).</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryValidateModel%2A>|<span data-ttu-id="fee10-126">Appelle la [validation de modèle](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="fee10-126">Invokes [model validation](xref:mvc/models/validation).</span></span>|

<span data-ttu-id="fee10-127">Pour obtenir la liste complète des méthodes et propriétés disponibles, consultez <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span><span class="sxs-lookup"><span data-stu-id="fee10-127">For a list of all available methods and properties, see <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span>

## <a name="attributes"></a><span data-ttu-id="fee10-128">Attributs</span><span class="sxs-lookup"><span data-stu-id="fee10-128">Attributes</span></span>

<span data-ttu-id="fee10-129">L’espace de noms <xref:Microsoft.AspNetCore.Mvc> fournit des attributs qui peuvent être utilisés pour configurer le comportement des contrôleurs d’API web et des méthodes d’action.</span><span class="sxs-lookup"><span data-stu-id="fee10-129">The <xref:Microsoft.AspNetCore.Mvc> namespace provides attributes that can be used to configure the behavior of web API controllers and action methods.</span></span> <span data-ttu-id="fee10-130">L’exemple suivant utilise des attributs pour spécifier le verbe d’action HTTP pris en charge et tous les codes de statut HTTP connus qui pourraient être retournés :</span><span class="sxs-lookup"><span data-stu-id="fee10-130">The following example uses attributes to specify the supported HTTP action verb and any known HTTP status codes that could be returned:</span></span>

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_400And201&highlight=1-3)]

<span data-ttu-id="fee10-131">Voici d’autres exemples d’attributs disponibles.</span><span class="sxs-lookup"><span data-stu-id="fee10-131">Here are some more examples of attributes that are available.</span></span>

|<span data-ttu-id="fee10-132">Attribut</span><span class="sxs-lookup"><span data-stu-id="fee10-132">Attribute</span></span>|<span data-ttu-id="fee10-133">Notes</span><span class="sxs-lookup"><span data-stu-id="fee10-133">Notes</span></span>|
|---------|-----|
|[`[Route]`](<xref:Microsoft.AspNetCore.Mvc.RouteAttribute>)      |<span data-ttu-id="fee10-134">Spécifie le modèle d’URL pour un contrôleur ou une action.</span><span class="sxs-lookup"><span data-stu-id="fee10-134">Specifies URL pattern for a controller or action.</span></span>|
|[`[Bind]`](<xref:Microsoft.AspNetCore.Mvc.BindAttribute>)        |<span data-ttu-id="fee10-135">Spécifie le préfixe et les propriétés à inclure pour la liaison de modèle.</span><span class="sxs-lookup"><span data-stu-id="fee10-135">Specifies prefix and properties to include for model binding.</span></span>|
|[`[HttpGet]`](<xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute>)  |<span data-ttu-id="fee10-136">Identifie une action qui prend en charge le verbe d’action HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="fee10-136">Identifies an action that supports the HTTP GET action verb.</span></span>|
|[`[Consumes]`](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>)|<span data-ttu-id="fee10-137">Spécifie les types de données acceptés par une action.</span><span class="sxs-lookup"><span data-stu-id="fee10-137">Specifies data types that an action accepts.</span></span>|
|[`[Produces]`](<xref:Microsoft.AspNetCore.Mvc.ProducesAttribute>)|<span data-ttu-id="fee10-138">Spécifie les types de données retournés par une action.</span><span class="sxs-lookup"><span data-stu-id="fee10-138">Specifies data types that an action returns.</span></span>|

<span data-ttu-id="fee10-139">Pour obtenir la liste des attributs disponibles, consultez l’espace de noms <xref:Microsoft.AspNetCore.Mvc>.</span><span class="sxs-lookup"><span data-stu-id="fee10-139">For a list that includes the available attributes, see the <xref:Microsoft.AspNetCore.Mvc> namespace.</span></span>

## <a name="apicontroller-attribute"></a><span data-ttu-id="fee10-140">Attribut ApiController</span><span class="sxs-lookup"><span data-stu-id="fee10-140">ApiController attribute</span></span>

<span data-ttu-id="fee10-141">L’attribut [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) peut être appliqué à une classe de contrôleur pour activer les comportements suivants, spécifiques à l’API :</span><span class="sxs-lookup"><span data-stu-id="fee10-141">The [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute can be applied to a controller class to enable the following opinionated, API-specific behaviors:</span></span>

::: moniker range=">= aspnetcore-2.2"

* [<span data-ttu-id="fee10-142">Exigence du routage d’attribut</span><span class="sxs-lookup"><span data-stu-id="fee10-142">Attribute routing requirement</span></span>](#attribute-routing-requirement)
* [<span data-ttu-id="fee10-143">Réponses HTTP 400 automatiques</span><span class="sxs-lookup"><span data-stu-id="fee10-143">Automatic HTTP 400 responses</span></span>](#automatic-http-400-responses)
* [<span data-ttu-id="fee10-144">Inférence de paramètre de source de liaison</span><span class="sxs-lookup"><span data-stu-id="fee10-144">Binding source parameter inference</span></span>](#binding-source-parameter-inference)
* [<span data-ttu-id="fee10-145">Inférence de demande multipart/form-data</span><span class="sxs-lookup"><span data-stu-id="fee10-145">Multipart/form-data request inference</span></span>](#multipartform-data-request-inference)
* [<span data-ttu-id="fee10-146">Fonctionnalité Détails du problème pour les codes d’état erreur</span><span class="sxs-lookup"><span data-stu-id="fee10-146">Problem details for error status codes</span></span>](#problem-details-for-error-status-codes)

<span data-ttu-id="fee10-147">Les détails du problème pour la fonction *de codes d’état d’erreur* nécessitent une version de [compatibilité](xref:mvc/compatibility-version) de 2.2 ou plus tard.</span><span class="sxs-lookup"><span data-stu-id="fee10-147">The *Problem details for error status codes* feature requires a [compatibility version](xref:mvc/compatibility-version) of 2.2 or later.</span></span> <span data-ttu-id="fee10-148">Les autres fonctionnalités nécessitent une version de compatibilité de 2.1 ou plus tard.</span><span class="sxs-lookup"><span data-stu-id="fee10-148">The other features require a compatibility version of 2.1 or later.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

* [<span data-ttu-id="fee10-149">Exigence du routage d’attribut</span><span class="sxs-lookup"><span data-stu-id="fee10-149">Attribute routing requirement</span></span>](#attribute-routing-requirement)
* [<span data-ttu-id="fee10-150">Réponses HTTP 400 automatiques</span><span class="sxs-lookup"><span data-stu-id="fee10-150">Automatic HTTP 400 responses</span></span>](#automatic-http-400-responses)
* [<span data-ttu-id="fee10-151">Inférence de paramètre de source de liaison</span><span class="sxs-lookup"><span data-stu-id="fee10-151">Binding source parameter inference</span></span>](#binding-source-parameter-inference)
* [<span data-ttu-id="fee10-152">Inférence de demande multipart/form-data</span><span class="sxs-lookup"><span data-stu-id="fee10-152">Multipart/form-data request inference</span></span>](#multipartform-data-request-inference)

<span data-ttu-id="fee10-153">Ces fonctionnalités nécessitent une [version de compatibilité](xref:mvc/compatibility-version) 2.1 ou ultérieure.</span><span class="sxs-lookup"><span data-stu-id="fee10-153">These features require a [compatibility version](xref:mvc/compatibility-version) of 2.1 or later.</span></span>

::: moniker-end

### <a name="attribute-on-specific-controllers"></a><span data-ttu-id="fee10-154">Attribut sur des contrôleurs spécifiques</span><span class="sxs-lookup"><span data-stu-id="fee10-154">Attribute on specific controllers</span></span>

<span data-ttu-id="fee10-155">L’attribut `[ApiController]` peut être appliqué à des contrôleurs spécifiques, comme dans l’exemple suivant à partir du modèle de projet :</span><span class="sxs-lookup"><span data-stu-id="fee10-155">The `[ApiController]` attribute can be applied to specific controllers, as in the following example from the project template:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=2])]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=2)]

::: moniker-end

### <a name="attribute-on-multiple-controllers"></a><span data-ttu-id="fee10-156">Attribut sur plusieurs contrôleurs</span><span class="sxs-lookup"><span data-stu-id="fee10-156">Attribute on multiple controllers</span></span>

<span data-ttu-id="fee10-157">Une façon d’utiliser l’attribut sur plusieurs contrôleurs consiste à créer une classe de contrôleur de base personnalisée annotée avec l’attribut `[ApiController]`.</span><span class="sxs-lookup"><span data-stu-id="fee10-157">One approach to using the attribute on more than one controller is to create a custom base controller class annotated with the `[ApiController]` attribute.</span></span> <span data-ttu-id="fee10-158">L’exemple suivant montre une classe de base personnalisée et un contrôleur qui en dérive :</span><span class="sxs-lookup"><span data-stu-id="fee10-158">The following example shows a custom base class and a controller that derives from it:</span></span>

[!code-csharp[](index/samples/2.x/2.2/Controllers/MyControllerBase.cs?name=snippet_MyControllerBase)]

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/PetsController.cs?name=snippet_Inherit)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_Inherit)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

### <a name="attribute-on-an-assembly"></a><span data-ttu-id="fee10-159">Attribut sur une assemblée</span><span class="sxs-lookup"><span data-stu-id="fee10-159">Attribute on an assembly</span></span>

<span data-ttu-id="fee10-160">Si la [version de compatibilité](xref:mvc/compatibility-version) est définie sur 2.2 ou une version ultérieure, l’attribut `[ApiController]` peut être appliqué à un assembly.</span><span class="sxs-lookup"><span data-stu-id="fee10-160">If [compatibility version](xref:mvc/compatibility-version) is set to 2.2 or later, the `[ApiController]` attribute can be applied to an assembly.</span></span> <span data-ttu-id="fee10-161">De cette manière, l’annotation applique le comportement de l’API web à tous les contrôleurs de l’assembly.</span><span class="sxs-lookup"><span data-stu-id="fee10-161">Annotation in this manner applies web API behavior to all controllers in the assembly.</span></span> <span data-ttu-id="fee10-162">Les contrôleurs individuels n’ont aucun moyen de refuser.</span><span class="sxs-lookup"><span data-stu-id="fee10-162">There's no way to opt out for individual controllers.</span></span> <span data-ttu-id="fee10-163">Appliquer l’attribut de niveau d’assemblage `Startup` à la déclaration d’espace de nom entourant la classe :</span><span class="sxs-lookup"><span data-stu-id="fee10-163">Apply the assembly-level attribute to the namespace declaration surrounding the `Startup` class:</span></span>

```csharp
[assembly: ApiController]
namespace WebApiSample
{
    public class Startup
    {
        ...
    }
}
```

::: moniker-end

## <a name="attribute-routing-requirement"></a><span data-ttu-id="fee10-164">Exigence du routage d’attribut</span><span class="sxs-lookup"><span data-stu-id="fee10-164">Attribute routing requirement</span></span>

<span data-ttu-id="fee10-165">L’attribut `[ApiController]` rend nécessaire le routage d’attributs.</span><span class="sxs-lookup"><span data-stu-id="fee10-165">The `[ApiController]` attribute makes attribute routing a requirement.</span></span> <span data-ttu-id="fee10-166">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="fee10-166">For example:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=2)]

<span data-ttu-id="fee10-167">Les actions sont inaccessibles `UseEndpoints` <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A>via <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> des `Startup.Configure` [itinéraires conventionnels définis](xref:mvc/controllers/routing#conventional-routing) par , , ou en .</span><span class="sxs-lookup"><span data-stu-id="fee10-167">Actions are inaccessible via [conventional routes](xref:mvc/controllers/routing#conventional-routing) defined by `UseEndpoints`, <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A>, or <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> in `Startup.Configure`.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=1)]

<span data-ttu-id="fee10-168">Les actions sont inaccessibles par le biais de [routes conventionnelles](xref:mvc/controllers/routing#conventional-routing) définies par <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A> ou <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> dans `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="fee10-168">Actions are inaccessible via [conventional routes](xref:mvc/controllers/routing#conventional-routing) defined by <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A> or <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> in `Startup.Configure`.</span></span>

::: moniker-end

## <a name="automatic-http-400-responses"></a><span data-ttu-id="fee10-169">Réponses HTTP 400 automatiques</span><span class="sxs-lookup"><span data-stu-id="fee10-169">Automatic HTTP 400 responses</span></span>

<span data-ttu-id="fee10-170">Grâce à l’attribut `[ApiController]`, les erreurs de validation de modèles déclenchent automatiquement une réponse HTTP 400.</span><span class="sxs-lookup"><span data-stu-id="fee10-170">The `[ApiController]` attribute makes model validation errors automatically trigger an HTTP 400 response.</span></span> <span data-ttu-id="fee10-171">Ainsi, le code suivant est inutile dans une méthode d’action :</span><span class="sxs-lookup"><span data-stu-id="fee10-171">Consequently, the following code is unnecessary in an action method:</span></span>

```csharp
if (!ModelState.IsValid)
{
    return BadRequest(ModelState);
}
```

<span data-ttu-id="fee10-172">ASP.NET Core MVC utilise <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ModelStateInvalidFilter> le filtre d’action pour effectuer la vérification précédente.</span><span class="sxs-lookup"><span data-stu-id="fee10-172">ASP.NET Core MVC uses the <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ModelStateInvalidFilter> action filter to do the preceding check.</span></span>

### <a name="default-badrequest-response"></a><span data-ttu-id="fee10-173">Réponse BadRequest par défaut</span><span class="sxs-lookup"><span data-stu-id="fee10-173">Default BadRequest response</span></span>

<span data-ttu-id="fee10-174">Avec une version de compatibilité de 2.1, le type de <xref:Microsoft.AspNetCore.Mvc.SerializableError>réponse par défaut pour une réponse HTTP 400 est .</span><span class="sxs-lookup"><span data-stu-id="fee10-174">With a compatibility version of 2.1, the default response type for an HTTP 400 response is <xref:Microsoft.AspNetCore.Mvc.SerializableError>.</span></span> <span data-ttu-id="fee10-175">Le corps de demande suivant est un exemple du type sérialisé :</span><span class="sxs-lookup"><span data-stu-id="fee10-175">The following request body is an example of the serialized type:</span></span>

```json
{
  "": [
    "A non-empty request body is required."
  ]
}
```

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="fee10-176">Avec une version de compatibilité de 2.2 ou plus tard, le <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>type de réponse par défaut pour une réponse HTTP 400 est .</span><span class="sxs-lookup"><span data-stu-id="fee10-176">With a compatibility version of 2.2 or later, the default response type for an HTTP 400 response is <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>.</span></span> <span data-ttu-id="fee10-177">Le corps de demande suivant est un exemple du type sérialisé :</span><span class="sxs-lookup"><span data-stu-id="fee10-177">The following request body is an example of the serialized type:</span></span>

```json
{
  "type": "https://tools.ietf.org/html/rfc7231#section-6.5.1",
  "title": "One or more validation errors occurred.",
  "status": 400,
  "traceId": "|7fb5e16a-4c8f23bbfc974667.",
  "errors": {
    "": [
      "A non-empty request body is required."
    ]
  }
}
```

<span data-ttu-id="fee10-178">Le `ValidationProblemDetails` type:</span><span class="sxs-lookup"><span data-stu-id="fee10-178">The `ValidationProblemDetails` type:</span></span>

* <span data-ttu-id="fee10-179">Fournit un format lisible à la machine pour spécifier les erreurs dans les réponses de l’API Web.</span><span class="sxs-lookup"><span data-stu-id="fee10-179">Provides a machine-readable format for specifying errors in web API responses.</span></span>
* <span data-ttu-id="fee10-180">Conforme aux [spécifications du RFC 7807](https://tools.ietf.org/html/rfc7807).</span><span class="sxs-lookup"><span data-stu-id="fee10-180">Complies with the [RFC 7807 specification](https://tools.ietf.org/html/rfc7807).</span></span>

::: moniker-end

### <a name="log-automatic-400-responses"></a><span data-ttu-id="fee10-181">Consigner automatiquement 400 réponses</span><span class="sxs-lookup"><span data-stu-id="fee10-181">Log automatic 400 responses</span></span>

<span data-ttu-id="fee10-182">Voir Guide pratique pour consigner automatiquement 400 réponses en cas d’erreurs de validation de modèle (aspnet/AspNetCore.Docs no 12157).</span><span class="sxs-lookup"><span data-stu-id="fee10-182">See [How to log automatic 400 responses on model validation errors (aspnet/AspNetCore.Docs #12157)](https://github.com/dotnet/AspNetCore.Docs/issues/12157).</span></span>

### <a name="disable-automatic-400-response"></a><span data-ttu-id="fee10-183">Désactiver la réponse automatique 400</span><span class="sxs-lookup"><span data-stu-id="fee10-183">Disable automatic 400 response</span></span>

<span data-ttu-id="fee10-184">Pour désactiver le comportement 400 automatique, définissez la propriété <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressModelStateInvalidFilter> sur `true`.</span><span class="sxs-lookup"><span data-stu-id="fee10-184">To disable the automatic 400 behavior, set the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressModelStateInvalidFilter> property to `true`.</span></span> <span data-ttu-id="fee10-185">Ajoutez le code en surbrillance suivant dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="fee10-185">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,6)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,7)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](index/samples/2.x/2.1/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=1,5)]

::: moniker-end

## <a name="binding-source-parameter-inference"></a><span data-ttu-id="fee10-186">Inférence de paramètre de source de liaison</span><span class="sxs-lookup"><span data-stu-id="fee10-186">Binding source parameter inference</span></span>

<span data-ttu-id="fee10-187">Un attribut de source de liaison définit l’emplacement auquel se trouve la valeur d’un paramètre d’action.</span><span class="sxs-lookup"><span data-stu-id="fee10-187">A binding source attribute defines the location at which an action parameter's value is found.</span></span> <span data-ttu-id="fee10-188">Les attributs de source de liaison suivants existent :</span><span class="sxs-lookup"><span data-stu-id="fee10-188">The following binding source attributes exist:</span></span>

|<span data-ttu-id="fee10-189">Attribut</span><span class="sxs-lookup"><span data-stu-id="fee10-189">Attribute</span></span>|<span data-ttu-id="fee10-190">Source de liaison</span><span class="sxs-lookup"><span data-stu-id="fee10-190">Binding source</span></span> |
|---------|---------|
|[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute)     | <span data-ttu-id="fee10-191">Corps de la demande</span><span class="sxs-lookup"><span data-stu-id="fee10-191">Request body</span></span> |
|[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute)     | <span data-ttu-id="fee10-192">Données de formulaire dans le corps de la demande</span><span class="sxs-lookup"><span data-stu-id="fee10-192">Form data in the request body</span></span> |
|[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) | <span data-ttu-id="fee10-193">En-tête de requête</span><span class="sxs-lookup"><span data-stu-id="fee10-193">Request header</span></span> |
|[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute)   | <span data-ttu-id="fee10-194">Paramètre de la chaîne de requête de la demande</span><span class="sxs-lookup"><span data-stu-id="fee10-194">Request query string parameter</span></span> |
|[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute)   | <span data-ttu-id="fee10-195">Données d’itinéraire à partir de la demande actuelle</span><span class="sxs-lookup"><span data-stu-id="fee10-195">Route data from the current request</span></span> |
|[`[FromServices]`](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices) | <span data-ttu-id="fee10-196">Service de demande injecté comme paramètre d’action</span><span class="sxs-lookup"><span data-stu-id="fee10-196">The request service injected as an action parameter</span></span> |

> [!WARNING]
> <span data-ttu-id="fee10-197">N’utilisez pas `[FromRoute]` lorsque les valeurs risquent de contenir `%2f` (c’est-à-dire `/`).</span><span class="sxs-lookup"><span data-stu-id="fee10-197">Don't use `[FromRoute]` when values might contain `%2f` (that is `/`).</span></span> <span data-ttu-id="fee10-198">`%2f` ne sera pas sans la séquence d’échappement `/`.</span><span class="sxs-lookup"><span data-stu-id="fee10-198">`%2f` won't be unescaped to `/`.</span></span> <span data-ttu-id="fee10-199">Utilisez `[FromQuery]` si la valeur peut contenir `%2f`.</span><span class="sxs-lookup"><span data-stu-id="fee10-199">Use `[FromQuery]` if the value might contain `%2f`.</span></span>

<span data-ttu-id="fee10-200">Sans l’attribut `[ApiController]` ou des attributs de source de liaison comme `[FromQuery]`, le runtime ASP.NET Core tente d’utiliser le classeur de modèles objet complexe.</span><span class="sxs-lookup"><span data-stu-id="fee10-200">Without the `[ApiController]` attribute or binding source attributes like `[FromQuery]`, the ASP.NET Core runtime attempts to use the complex object model binder.</span></span> <span data-ttu-id="fee10-201">Le classeur de modèles objet complexe extrait des données à partir de fournisseurs de valeurs dans un ordre défini.</span><span class="sxs-lookup"><span data-stu-id="fee10-201">The complex object model binder pulls data from value providers in a defined order.</span></span>

<span data-ttu-id="fee10-202">Dans l’exemple suivant, l’attribut `[FromQuery]` indique que la valeur du paramètre `discontinuedOnly` est fournie dans la chaîne de requête de l’URL de demande :</span><span class="sxs-lookup"><span data-stu-id="fee10-202">In the following example, the `[FromQuery]` attribute indicates that the `discontinuedOnly` parameter value is provided in the request URL's query string:</span></span>

[!code-csharp[](index/samples/2.x/2.2/Controllers/ProductsController.cs?name=snippet_BindingSourceAttributes&highlight=3)]

<span data-ttu-id="fee10-203">L’attribut `[ApiController]` applique des règles d’inférence pour les sources de données par défaut des paramètres d’action.</span><span class="sxs-lookup"><span data-stu-id="fee10-203">The `[ApiController]` attribute applies inference rules for the default data sources of action parameters.</span></span> <span data-ttu-id="fee10-204">Ces règles vous évitent d’avoir à identifier les sources de liaison manuellement en appliquant des attributs aux paramètres d’action.</span><span class="sxs-lookup"><span data-stu-id="fee10-204">These rules save you from having to identify binding sources manually by applying attributes to the action parameters.</span></span> <span data-ttu-id="fee10-205">Les règles d’inférence de source de liaison se comportent comme suit :</span><span class="sxs-lookup"><span data-stu-id="fee10-205">The binding source inference rules behave as follows:</span></span>

* <span data-ttu-id="fee10-206">`[FromBody]` est déduit des paramètres de type complexe.</span><span class="sxs-lookup"><span data-stu-id="fee10-206">`[FromBody]` is inferred for complex type parameters.</span></span> <span data-ttu-id="fee10-207">Une exception à cette règle d’inférence `[FromBody]` est tout type complexe intégré ayant une signification spéciale, comme <xref:Microsoft.AspNetCore.Http.IFormCollection> et <xref:System.Threading.CancellationToken>.</span><span class="sxs-lookup"><span data-stu-id="fee10-207">An exception to the `[FromBody]` inference rule is any complex, built-in type with a special meaning, such as <xref:Microsoft.AspNetCore.Http.IFormCollection> and <xref:System.Threading.CancellationToken>.</span></span> <span data-ttu-id="fee10-208">Le code de l’inférence de la source de liaison ignore ces types spéciaux.</span><span class="sxs-lookup"><span data-stu-id="fee10-208">The binding source inference code ignores those special types.</span></span>
* <span data-ttu-id="fee10-209">`[FromForm]` est déduit pour les paramètres d’action de type <xref:Microsoft.AspNetCore.Http.IFormFile> et <xref:Microsoft.AspNetCore.Http.IFormFileCollection>.</span><span class="sxs-lookup"><span data-stu-id="fee10-209">`[FromForm]` is inferred for action parameters of type <xref:Microsoft.AspNetCore.Http.IFormFile> and <xref:Microsoft.AspNetCore.Http.IFormFileCollection>.</span></span> <span data-ttu-id="fee10-210">Il n’est pas déduit pour les types simples ou définis par l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="fee10-210">It's not inferred for any simple or user-defined types.</span></span>
* <span data-ttu-id="fee10-211">`[FromRoute]` est déduit pour tout nom de paramètre d’action correspondant à un paramètre dans le modèle d’itinéraire.</span><span class="sxs-lookup"><span data-stu-id="fee10-211">`[FromRoute]` is inferred for any action parameter name matching a parameter in the route template.</span></span> <span data-ttu-id="fee10-212">Quand plus d’un itinéraire correspond à un paramètre d’action, toute valeur d’itinéraire est considérée comme `[FromRoute]`.</span><span class="sxs-lookup"><span data-stu-id="fee10-212">When more than one route matches an action parameter, any route value is considered `[FromRoute]`.</span></span>
* <span data-ttu-id="fee10-213">`[FromQuery]` est déduit pour tous les autres paramètres d’action.</span><span class="sxs-lookup"><span data-stu-id="fee10-213">`[FromQuery]` is inferred for any other action parameters.</span></span>

### <a name="frombody-inference-notes"></a><span data-ttu-id="fee10-214">Notes sur l’inférence FromBody</span><span class="sxs-lookup"><span data-stu-id="fee10-214">FromBody inference notes</span></span>

<span data-ttu-id="fee10-215">`[FromBody]` n’est pas déduit pour les types simples tels que `string` ou `int`.</span><span class="sxs-lookup"><span data-stu-id="fee10-215">`[FromBody]` isn't inferred for simple types such as `string` or `int`.</span></span> <span data-ttu-id="fee10-216">Vous devez donc utiliser l’attribut `[FromBody]` pour les types simples quand cette fonctionnalité est nécessaire.</span><span class="sxs-lookup"><span data-stu-id="fee10-216">Therefore, the `[FromBody]` attribute should be used for simple types when that functionality is needed.</span></span>

<span data-ttu-id="fee10-217">Quand une action a plusieurs paramètres liés à partir du corps de la demande, une exception est levée.</span><span class="sxs-lookup"><span data-stu-id="fee10-217">When an action has more than one parameter bound from the request body, an exception is thrown.</span></span> <span data-ttu-id="fee10-218">Par exemple, toutes les signatures de méthode d’action suivantes génèrent une exception :</span><span class="sxs-lookup"><span data-stu-id="fee10-218">For example, all of the following action method signatures cause an exception:</span></span>

* <span data-ttu-id="fee10-219">`[FromBody]` est déduit sur les deux paramètres, car ce sont des types complexes.</span><span class="sxs-lookup"><span data-stu-id="fee10-219">`[FromBody]` inferred on both because they're complex types.</span></span>

  ```csharp
  [HttpPost]
  public IActionResult Action1(Product product, Order order)
  ```

* <span data-ttu-id="fee10-220">Attribut `[FromBody]` sur un paramètre, déduit sur l’autre, car c’est un type complexe.</span><span class="sxs-lookup"><span data-stu-id="fee10-220">`[FromBody]` attribute on one, inferred on the other because it's a complex type.</span></span>

  ```csharp
  [HttpPost]
  public IActionResult Action2(Product product, [FromBody] Order order)
  ```

* <span data-ttu-id="fee10-221">Attribut `[FromBody]` sur les deux paramètres.</span><span class="sxs-lookup"><span data-stu-id="fee10-221">`[FromBody]` attribute on both.</span></span>

  ```csharp
  [HttpPost]
  public IActionResult Action3([FromBody] Product product, [FromBody] Order order)
  ```

::: moniker range="= aspnetcore-2.1"

> [!NOTE]
> <span data-ttu-id="fee10-222">Dans ASP.NET Core 2.1, les paramètres de type collection comme les listes et les tableaux sont déduits de manière incorrecte en tant que `[FromQuery]`.</span><span class="sxs-lookup"><span data-stu-id="fee10-222">In ASP.NET Core 2.1, collection type parameters such as lists and arrays are incorrectly inferred as `[FromQuery]`.</span></span> <span data-ttu-id="fee10-223">L’attribut `[FromBody]` doit être utilisé pour ces paramètres s’ils doivent être liés à partir du corps de la demande.</span><span class="sxs-lookup"><span data-stu-id="fee10-223">The `[FromBody]` attribute should be used for these parameters if they are to be bound from the request body.</span></span> <span data-ttu-id="fee10-224">Ce problème est corrigé dans ASP.NET Core version 2.2 ou ultérieure, où les paramètres de type collection sont déduits pour être liés à partir du corps par défaut.</span><span class="sxs-lookup"><span data-stu-id="fee10-224">This behavior is corrected in ASP.NET Core 2.2 or later, where collection type parameters are inferred to be bound from the body by default.</span></span>

::: moniker-end

### <a name="disable-inference-rules"></a><span data-ttu-id="fee10-225">Désactiver les règles d’inférence</span><span class="sxs-lookup"><span data-stu-id="fee10-225">Disable inference rules</span></span>

<span data-ttu-id="fee10-226">Pour désactiver l’inférence de la source de liaison, définissez <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressInferBindingSourcesForParameters> sur `true`.</span><span class="sxs-lookup"><span data-stu-id="fee10-226">To disable binding source inference, set <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressInferBindingSourcesForParameters> to `true`.</span></span> <span data-ttu-id="fee10-227">Ajoutez le code suivant dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="fee10-227">Add the following code in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,5)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,6)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](index/samples/2.x/2.1/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=1,4)]

::: moniker-end

## <a name="multipartform-data-request-inference"></a><span data-ttu-id="fee10-228">Inférence de demande multipart/form-data</span><span class="sxs-lookup"><span data-stu-id="fee10-228">Multipart/form-data request inference</span></span>

<span data-ttu-id="fee10-229">L’attribut `[ApiController]` applique une règle d’inférence lorsqu’un paramètre d’action est annoté avec l’attribut. [`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute)</span><span class="sxs-lookup"><span data-stu-id="fee10-229">The `[ApiController]` attribute applies an inference rule when an action parameter is annotated with the [`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) attribute.</span></span> <span data-ttu-id="fee10-230">Le `multipart/form-data` type de contenu de demande est déduit.</span><span class="sxs-lookup"><span data-stu-id="fee10-230">The `multipart/form-data` request content type is inferred.</span></span>

<span data-ttu-id="fee10-231">Pour désactiver le comportement par <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressConsumesConstraintForFormFileParameters> défaut, définissez la propriété `true` dans `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="fee10-231">To disable the default behavior, set the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressConsumesConstraintForFormFileParameters> property to `true` in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,4)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,5)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](index/samples/2.x/2.1/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=1,3)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

## <a name="problem-details-for-error-status-codes"></a><span data-ttu-id="fee10-232">Fonctionnalité Détails du problème pour les codes d’état erreur</span><span class="sxs-lookup"><span data-stu-id="fee10-232">Problem details for error status codes</span></span>

<span data-ttu-id="fee10-233">Quand la version de compatibilité est 2.2 ou ultérieure, MVC transforme un résultat d’erreur (résultat avec un code d’état égal ou supérieur à 400) en résultat avec <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>.</span><span class="sxs-lookup"><span data-stu-id="fee10-233">When the compatibility version is 2.2 or later, MVC transforms an error result (a result with status code 400 or higher) to a result with <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>.</span></span> <span data-ttu-id="fee10-234">Le type `ProblemDetails` est basé sur la [spécification RFC 7807](https://tools.ietf.org/html/rfc7807) pour fournir des détails de l’erreur lisibles par machine dans une réponse HTTP.</span><span class="sxs-lookup"><span data-stu-id="fee10-234">The `ProblemDetails` type is based on the [RFC 7807 specification](https://tools.ietf.org/html/rfc7807) for providing machine-readable error details in an HTTP response.</span></span>

<span data-ttu-id="fee10-235">Prenons le code suivant dans une action de contrôleur :</span><span class="sxs-lookup"><span data-stu-id="fee10-235">Consider the following code in a controller action:</span></span>

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_ProblemDetailsStatusCode)]

<span data-ttu-id="fee10-236">La `NotFound` méthode produit un code d’état `ProblemDetails` HTTP 404 avec un corps.</span><span class="sxs-lookup"><span data-stu-id="fee10-236">The `NotFound` method produces an HTTP 404 status code with a `ProblemDetails` body.</span></span> <span data-ttu-id="fee10-237">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="fee10-237">For example:</span></span>

```json
{
  type: "https://tools.ietf.org/html/rfc7231#section-6.5.4",
  title: "Not Found",
  status: 404,
  traceId: "0HLHLV31KRN83:00000001"
}
```

### <a name="disable-problemdetails-response"></a><span data-ttu-id="fee10-238">Désactiver la réponse ProblemDetails</span><span class="sxs-lookup"><span data-stu-id="fee10-238">Disable ProblemDetails response</span></span>

<span data-ttu-id="fee10-239">La création automatique `ProblemDetails` d’un code de <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressMapClientErrors%2A> statut d’erreur est désactivée lorsque la propriété est réglée à `true`.</span><span class="sxs-lookup"><span data-stu-id="fee10-239">The automatic creation of a `ProblemDetails` for error status codes is disabled when the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressMapClientErrors%2A> property is set to `true`.</span></span> <span data-ttu-id="fee10-240">Ajoutez le code suivant dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="fee10-240">Add the following code in `Startup.ConfigureServices`:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,7)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,8)]

::: moniker-end

<a name="consumes"></a>

## <a name="define-supported-request-content-types-with-the-consumes-attribute"></a><span data-ttu-id="fee10-241">Définir les types de contenu de demande pris en charge avec l’attribut [Consumes]</span><span class="sxs-lookup"><span data-stu-id="fee10-241">Define supported request content types with the [Consumes] attribute</span></span>

<span data-ttu-id="fee10-242">Par défaut, une action prend en charge tous les types de contenu de demande disponibles.</span><span class="sxs-lookup"><span data-stu-id="fee10-242">By default, an action supports all available request content types.</span></span> <span data-ttu-id="fee10-243">Par exemple, si une application est configurée pour prendre en charge les [formateurs d’entrée](xref:mvc/models/model-binding#input-formatters)JSON et XML, une action prend en charge plusieurs types de contenu, y compris `application/json` et `application/xml`.</span><span class="sxs-lookup"><span data-stu-id="fee10-243">For example, if an app is configured to support both JSON and XML [input formatters](xref:mvc/models/model-binding#input-formatters), an action supports multiple content types, including `application/json` and `application/xml`.</span></span>

<span data-ttu-id="fee10-244">[L’attribut [Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) permet une action pour limiter les types de contenu de demande pris en charge.</span><span class="sxs-lookup"><span data-stu-id="fee10-244">The [[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) attribute allows an action to limit the supported request content types.</span></span> <span data-ttu-id="fee10-245">Appliquer `[Consumes]` l’attribut à une action ou à un contrôleur, en spécifiant un ou plusieurs types de contenu :</span><span class="sxs-lookup"><span data-stu-id="fee10-245">Apply the `[Consumes]` attribute to an action or controller, specifying one or more content types:</span></span>

```csharp
[HttpPost]
[Consumes("application/xml")]
public IActionResult CreateProduct(Product product)
```

<span data-ttu-id="fee10-246">Dans le code `CreateProduct` précédent, l’action `application/xml`spécifie le type de contenu .</span><span class="sxs-lookup"><span data-stu-id="fee10-246">In the preceding code, the `CreateProduct` action specifies the content type `application/xml`.</span></span> <span data-ttu-id="fee10-247">Les demandes acheminées à `Content-Type` cette `application/xml`action doivent spécifier un en-tête de .</span><span class="sxs-lookup"><span data-stu-id="fee10-247">Requests routed to this action must specify a `Content-Type` header of `application/xml`.</span></span> <span data-ttu-id="fee10-248">Demandes qui ne spécifient pas un `Content-Type` en-tête d’aboutir `application/xml` à une réponse de type média [415 non étayée.](https://developer.mozilla.org/docs/Web/HTTP/Status/415)</span><span class="sxs-lookup"><span data-stu-id="fee10-248">Requests that don't specify a `Content-Type` header of `application/xml` result in a [415 Unsupported Media Type](https://developer.mozilla.org/docs/Web/HTTP/Status/415) response.</span></span>

<span data-ttu-id="fee10-249">L’attribut `[Consumes]` permet également à une action d’influencer sa sélection en fonction du type de contenu d’une demande entrante en appliquant une contrainte de type.</span><span class="sxs-lookup"><span data-stu-id="fee10-249">The `[Consumes]` attribute also allows an action to influence its selection based on an incoming request's content type by applying a type constraint.</span></span> <span data-ttu-id="fee10-250">Prenons l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="fee10-250">Consider the following example:</span></span>

[!code-csharp[](index/samples/3.x/Controllers/ConsumesController.cs?name=snippet_Class)]

<span data-ttu-id="fee10-251">Dans le code `ConsumesController` précédent, est configuré `https://localhost:5001/api/Consumes` pour traiter les demandes envoyées à l’URL.</span><span class="sxs-lookup"><span data-stu-id="fee10-251">In the preceding code, `ConsumesController` is configured to handle requests sent to the `https://localhost:5001/api/Consumes` URL.</span></span> <span data-ttu-id="fee10-252">Les deux actions du `PostJson` contrôleur, et `PostForm`, gérer les demandes POST avec la même URL.</span><span class="sxs-lookup"><span data-stu-id="fee10-252">Both of the controller's actions, `PostJson` and `PostForm`, handle POST requests with the same URL.</span></span> <span data-ttu-id="fee10-253">Sans `[Consumes]` l’attribut appliquant une contrainte de type, une exception de match ambigue est jetée.</span><span class="sxs-lookup"><span data-stu-id="fee10-253">Without the `[Consumes]` attribute applying a type constraint, an ambiguous match exception is thrown.</span></span>

<span data-ttu-id="fee10-254">L’attribut `[Consumes]` est appliqué aux deux actions.</span><span class="sxs-lookup"><span data-stu-id="fee10-254">The `[Consumes]` attribute is applied to both actions.</span></span> <span data-ttu-id="fee10-255">L’action `PostJson` gère les `Content-Type` demandes `application/json`envoyées avec un en-tête de .</span><span class="sxs-lookup"><span data-stu-id="fee10-255">The `PostJson` action handles requests sent with a `Content-Type` header of `application/json`.</span></span> <span data-ttu-id="fee10-256">L’action `PostForm` gère les `Content-Type` demandes `application/x-www-form-urlencoded`envoyées avec un en-tête de .</span><span class="sxs-lookup"><span data-stu-id="fee10-256">The `PostForm` action handles requests sent with a `Content-Type` header of `application/x-www-form-urlencoded`.</span></span> 

## <a name="additional-resources"></a><span data-ttu-id="fee10-257">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="fee10-257">Additional resources</span></span>

* <xref:web-api/action-return-types>
* <xref:web-api/handle-errors>
* <xref:web-api/advanced/custom-formatters>
* <xref:web-api/advanced/formatting>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:mvc/controllers/routing>
