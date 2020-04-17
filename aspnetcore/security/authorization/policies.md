---
title: Autorisation fondée sur la politique dans ASP.NET Core
author: rick-anderson
description: Découvrez comment créer et utiliser les gestionnaires de stratégies d’autorisation pour l’application des exigences d’autorisation dans une application ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2020
uid: security/authorization/policies
ms.openlocfilehash: 66412a6020ea8f12427c8c5b466e1b2eedccf0f9
ms.sourcegitcommit: 77c046331f3d633d7cc247ba77e58b89e254f487
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81488759"
---
# <a name="policy-based-authorization-in-aspnet-core"></a><span data-ttu-id="6f8c8-103">Autorisation fondée sur la politique dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6f8c8-103">Policy-based authorization in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6f8c8-104">Sous les couvertures, [l’autorisation basée sur](xref:security/authorization/roles) les rôles et [l’autorisation fondée sur les revendications](xref:security/authorization/claims) utilisent une exigence, un gestionnaire d’exigence et une stratégie préconfigurée.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-104">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="6f8c8-105">Ces éléments de base appuient l’expression des évaluations d’autorisation dans le code.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-105">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="6f8c8-106">Il en résulte une structure d’autorisation plus riche, réutilisable et testable.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-106">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="6f8c8-107">Une politique d’autorisation se compose d’une ou plusieurs exigences.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-107">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="6f8c8-108">Il est enregistré dans le cadre de `Startup.ConfigureServices` la configuration du service d’autorisation, dans la méthode:</span><span class="sxs-lookup"><span data-stu-id="6f8c8-108">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53, 58)]

<span data-ttu-id="6f8c8-109">Dans l’exemple précédent, une politique « AtLeast21 » est créée.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-109">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="6f8c8-110">Il a une&mdash;seule exigence qui d’un âge minimum, qui est fourni comme un paramètre à l’exigence.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-110">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="6f8c8-111">IAuthorizationService (en)</span><span class="sxs-lookup"><span data-stu-id="6f8c8-111">IAuthorizationService</span></span> 

<span data-ttu-id="6f8c8-112">Le service principal qui détermine si <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>l’autorisation est réussie est :</span><span class="sxs-lookup"><span data-stu-id="6f8c8-112">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

<span data-ttu-id="6f8c8-113">Le code précédent met en évidence les deux méthodes de [l’IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span><span class="sxs-lookup"><span data-stu-id="6f8c8-113">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="6f8c8-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>est un service de marqueurs sans méthodes, et le mécanisme de suivi de l’autorisation est réussie.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="6f8c8-115">Chacun <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> est responsable de vérifier si les exigences sont remplies :</span><span class="sxs-lookup"><span data-stu-id="6f8c8-115">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
<!--The following code is a copy/paste from 
https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationHandler.cs -->

```csharp
/// <summary>
/// Classes implementing this interface are able to make a decision if authorization
/// is allowed.
/// </summary>
public interface IAuthorizationHandler
{
    /// <summary>
    /// Makes a decision if authorization is allowed.
    /// </summary>
    /// <param name="context">The authorization information.</param>
    Task HandleAsync(AuthorizationHandlerContext context);
}
```

<span data-ttu-id="6f8c8-116">La <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> classe est ce que le gestionnaire utilise pour marquer si les exigences ont été remplies :</span><span class="sxs-lookup"><span data-stu-id="6f8c8-116">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="6f8c8-117">Le code suivant montre la mise en œuvre par défaut simplifiée (et annotée avec commentaires) du service d’autorisation :</span><span class="sxs-lookup"><span data-stu-id="6f8c8-117">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

```csharp
public async Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user, 
             object resource, IEnumerable<IAuthorizationRequirement> requirements)
{
    // Create a tracking context from the authorization inputs.
    var authContext = _contextFactory.CreateContext(requirements, user, resource);

    // By default this returns an IEnumerable<IAuthorizationHandlers> from DI.
    var handlers = await _handlers.GetHandlersAsync(authContext);

    // Invoke all handlers.
    foreach (var handler in handlers)
    {
        await handler.HandleAsync(authContext);
    }

    // Check the context, by default success is when all requirements have been met.
    return _evaluator.Evaluate(authContext);
}
```

<span data-ttu-id="6f8c8-118">Le code suivant `ConfigureServices`montre un typique :</span><span class="sxs-lookup"><span data-stu-id="6f8c8-118">The following code shows a typical `ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add all of your handlers to DI.
    services.AddSingleton<IAuthorizationHandler, MyHandler1>();
    // MyHandler2, ...

    services.AddSingleton<IAuthorizationHandler, MyHandlerN>();

    // Configure your policies
    services.AddAuthorization(options =>
          options.AddPolicy("Something",
          policy => policy.RequireClaim("Permission", "CanViewPage", "CanViewAnything")));


    services.AddControllersWithViews();
    services.AddRazorPages();
}
```

<span data-ttu-id="6f8c8-119">Utiliser <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> `[Authorize(Policy = "Something")]` ou pour l’autorisation.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-119">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="applying-policies-to-mvc-controllers"></a><span data-ttu-id="6f8c8-120">Appliquer des politiques aux contrôleurs MVC</span><span class="sxs-lookup"><span data-stu-id="6f8c8-120">Applying policies to MVC controllers</span></span>

<span data-ttu-id="6f8c8-121">Si vous utilisez Razor Pages, consultez [les stratégies d’application des pages Razor](#applying-policies-to-razor-pages) dans ce document.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-121">If you're using Razor Pages, see [Applying policies to Razor Pages](#applying-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="6f8c8-122">Les stratégies sont appliquées `[Authorize]` aux contrôleurs en utilisant l’attribut avec le nom de la stratégie.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-122">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="6f8c8-123">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="6f8c8-123">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a><span data-ttu-id="6f8c8-124">Appliquer des politiques aux pages Razor</span><span class="sxs-lookup"><span data-stu-id="6f8c8-124">Applying policies to Razor Pages</span></span>

<span data-ttu-id="6f8c8-125">Les stratégies sont appliquées `[Authorize]` aux Pages Razor en utilisant l’attribut avec le nom de la stratégie.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-125">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="6f8c8-126">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="6f8c8-126">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="6f8c8-127">Les politiques peuvent également être appliquées aux Pages Razor en utilisant une [convention d’autorisation](xref:security/authorization/razor-pages-authorization).</span><span class="sxs-lookup"><span data-stu-id="6f8c8-127">Policies can also be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="6f8c8-128">Spécifications</span><span class="sxs-lookup"><span data-stu-id="6f8c8-128">Requirements</span></span>

<span data-ttu-id="6f8c8-129">Une exigence d’autorisation est une collecte de paramètres de données qu’une stratégie peut utiliser pour évaluer le principal utilisateur actuel.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-129">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="6f8c8-130">Dans notre politique « AtLeast21 », l’exigence est un paramètre&mdash;unique de l’âge minimum.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-130">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="6f8c8-131">Une exigence implémente [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), qui est une interface de marqueur vide.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-131">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="6f8c8-132">Une exigence d’âge minimum paramétrée pourrait être mise en œuvre comme suit :</span><span class="sxs-lookup"><span data-stu-id="6f8c8-132">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="6f8c8-133">Si une politique d’autorisation contient plusieurs exigences d’autorisation, toutes les exigences doivent être adoptées pour que l’évaluation de la politique réussisse.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-133">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="6f8c8-134">En d’autres termes, les exigences multiples d’autorisation ajoutées à une seule politique d’autorisation sont traitées sur une base **ET.**</span><span class="sxs-lookup"><span data-stu-id="6f8c8-134">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="6f8c8-135">Une exigence n’a pas besoin d’avoir des données ou des propriétés.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-135">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="6f8c8-136">Gestionnaires d’autorisation</span><span class="sxs-lookup"><span data-stu-id="6f8c8-136">Authorization handlers</span></span>

<span data-ttu-id="6f8c8-137">Un gestionnaire d’autorisation est responsable de l’évaluation des propriétés d’une exigence.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-137">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="6f8c8-138">Le gestionnaire d’autorisation évalue les exigences par rapport à un [texte autorisationhandlercontexte](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) fourni pour déterminer si l’accès est autorisé.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-138">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="6f8c8-139">Une exigence peut avoir [plusieurs gestionnaires](#security-authorization-policies-based-multiple-handlers).</span><span class="sxs-lookup"><span data-stu-id="6f8c8-139">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="6f8c8-140">Un gestionnaire peut hériter [de AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), où `TRequirement` est l’exigence à traiter.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-140">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="6f8c8-141">Alternativement, un gestionnaire peut implémenter [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) pour gérer plus d’un type d’exigence.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-141">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="6f8c8-142">Utiliser un gestionnaire pour une exigence</span><span class="sxs-lookup"><span data-stu-id="6f8c8-142">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="6f8c8-143">Voici un exemple d’une relation individuelles dans laquelle un gestionnaire d’âge minimum utilise une seule exigence :</span><span class="sxs-lookup"><span data-stu-id="6f8c8-143">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="6f8c8-144">Le code précédent détermine si le principal utilisateur actuel a une demande de date de naissance qui a été émise par un émetteur connu et digne de confiance.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-144">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="6f8c8-145">L’autorisation ne peut pas se produire lorsque la réclamation est manquante, auquel cas une tâche terminée est retournée.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-145">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="6f8c8-146">Lorsqu’une réclamation est présente, l’âge de l’utilisateur est calculé.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-146">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="6f8c8-147">Si l’utilisateur satisfait à l’âge minimum défini par l’exigence, l’autorisation est considérée comme réussie.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-147">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="6f8c8-148">Lorsque l’autorisation `context.Succeed` est acceptée, est invoquée avec l’exigence satisfaite comme seul paramètre.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-148">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="6f8c8-149">Utiliser un gestionnaire pour plusieurs exigences</span><span class="sxs-lookup"><span data-stu-id="6f8c8-149">Use a handler for multiple requirements</span></span>

<span data-ttu-id="6f8c8-150">Ce qui suit est un exemple d’une relation unique dans laquelle un gestionnaire d’autorisation peut traiter trois types d’exigences différentes :</span><span class="sxs-lookup"><span data-stu-id="6f8c8-150">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="6f8c8-151">Le code précédent traverse [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;une propriété contenant des exigences non marquées comme réussies.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-151">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="6f8c8-152">Pour `ReadPermission` une exigence, l’utilisateur doit être soit un propriétaire ou un commanditaire pour accéder à la ressource demandée.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-152">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="6f8c8-153">En cas d’exigence, `EditPermission` `DeletePermission` il doit être propriétaire de la ressource demandée.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-153">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="6f8c8-154">Enregistrement des gestionnaires</span><span class="sxs-lookup"><span data-stu-id="6f8c8-154">Handler registration</span></span>

<span data-ttu-id="6f8c8-155">Les manutentionnaires sont inscrits à la collection de services pendant la configuration.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-155">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="6f8c8-156">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="6f8c8-156">For example:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53-55, 58)]

<span data-ttu-id="6f8c8-157">Le code précédent `MinimumAgeHandler` s’inscrit comme un `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`singleton en invoquant .</span><span class="sxs-lookup"><span data-stu-id="6f8c8-157">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="6f8c8-158">Les manutentionnaires peuvent être enregistrés à l’aide de l’une des durées de [vie intégrées](xref:fundamentals/dependency-injection#service-lifetimes)du service.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-158">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="6f8c8-159">Qu’est-ce qu’un gestionnaire devrait revenir?</span><span class="sxs-lookup"><span data-stu-id="6f8c8-159">What should a handler return?</span></span>

<span data-ttu-id="6f8c8-160">Notez `Handle` que la méthode dans l’exemple [du gestionnaire](#security-authorization-handler-example) ne rapporte aucune valeur.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-160">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="6f8c8-161">Comment un statut de succès ou d’échec est-il indiqué?</span><span class="sxs-lookup"><span data-stu-id="6f8c8-161">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="6f8c8-162">Un gestionnaire indique `context.Succeed(IAuthorizationRequirement requirement)`le succès en appelant, en passant l’exigence qui a été validée avec succès.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-162">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="6f8c8-163">Un gestionnaire n’a pas besoin de gérer les défaillances en général, car d’autres gestionnaires pour la même exigence peuvent réussir.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-163">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="6f8c8-164">Pour garantir l’échec, même si `context.Fail`d’autres gestionnaires d’exigences réussissent, appelez .</span><span class="sxs-lookup"><span data-stu-id="6f8c8-164">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="6f8c8-165">Si un `context.Succeed` gestionnaire `context.Fail`appelle ou, tous les autres gestionnaires sont toujours appelés.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-165">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="6f8c8-166">Cela permet aux exigences de produire des effets secondaires, tels que l’enregistrement, qui a lieu même si un autre gestionnaire a validé ou échoué avec succès une exigence.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-166">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="6f8c8-167">Lorsqu’il `false`est configuré, la propriété [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) (disponible en ASP.NET Core 1.1 `context.Fail` et plus tard) court-circuite l’exécution des manutentionnaires lorsqu’il est appelé.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-167">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="6f8c8-168">`InvokeHandlersAfterFailure`par défaut `true`à , auquel cas tous les gestionnaires sont appelés.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-168">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="6f8c8-169">Les préposés à l’autorisation sont appelés même si l’authentification échoue.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-169">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="6f8c8-170">Pourquoi voudrais-je plusieurs gestionnaires pour une exigence?</span><span class="sxs-lookup"><span data-stu-id="6f8c8-170">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="6f8c8-171">Dans les cas où vous souhaitez que l’évaluation soit sur une base **DE OU,** implémentez plusieurs gestionnaires pour une seule exigence.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-171">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="6f8c8-172">Par exemple, Microsoft a des portes qui ne s’ouvrent qu’avec des cartes clés.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-172">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="6f8c8-173">Si vous laissez votre carte-clé à la maison, la réceptionniste imprime un autocollant temporaire et vous ouvre la porte.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-173">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="6f8c8-174">Dans ce scénario, vous auriez une seule exigence, *BuildingEntry*, mais plusieurs gestionnaires, chacun examinant une seule exigence.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-174">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="6f8c8-175">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="6f8c8-175">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="6f8c8-176">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="6f8c8-176">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="6f8c8-177">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="6f8c8-177">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="6f8c8-178">Assurez-vous que les deux gestionnaires sont [enregistrés](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span><span class="sxs-lookup"><span data-stu-id="6f8c8-178">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="6f8c8-179">Si l’un ou l’autre `BuildingEntryRequirement`gestionnaire réussit lorsqu’une politique évalue le, l’évaluation des politiques réussit.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-179">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="using-a-func-to-fulfill-a-policy"></a><span data-ttu-id="6f8c8-180">Utilisation d’un func pour remplir une politique</span><span class="sxs-lookup"><span data-stu-id="6f8c8-180">Using a func to fulfill a policy</span></span>

<span data-ttu-id="6f8c8-181">Il peut y avoir des situations dans lesquelles l’exécution d’une politique est simple à exprimer dans le code.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-181">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="6f8c8-182">Il est possible de `Func<AuthorizationHandlerContext, bool>` fournir un lors de `RequireAssertion` la configuration de votre politique avec le constructeur de police.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-182">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="6f8c8-183">Par exemple, `BadgeEntryHandler` le précédent pourrait être réécrit comme suit :</span><span class="sxs-lookup"><span data-stu-id="6f8c8-183">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=42-43,47-53)]

## <a name="accessing-mvc-request-context-in-handlers"></a><span data-ttu-id="6f8c8-184">Accès au contexte de demande MVC chez les gestionnaires</span><span class="sxs-lookup"><span data-stu-id="6f8c8-184">Accessing MVC request context in handlers</span></span>

<span data-ttu-id="6f8c8-185">La `HandleRequirementAsync` méthode que vous implémentez dans `AuthorizationHandlerContext` un `TRequirement` gestionnaire d’autorisation comporte deux paramètres : un et le que vous manipulez.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-185">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="6f8c8-186">Des cadres tels que MVC ou Jabbr sont `Resource` libres `AuthorizationHandlerContext` d’ajouter n’importe quel objet à la propriété sur les informations supplémentaires de passer.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-186">Frameworks such as MVC or Jabbr are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="6f8c8-187">Par exemple, MVC passe une instance de `Resource` [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) dans la propriété.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-187">For example, MVC passes an instance of [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) in the `Resource` property.</span></span> <span data-ttu-id="6f8c8-188">Cette propriété donne `HttpContext` `RouteData`accès à , , et tout le reste fourni par MVC et Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-188">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="6f8c8-189">L’utilisation `Resource` de la propriété est spécifique au cadre.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-189">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="6f8c8-190">L’utilisation `Resource` de l’information dans la propriété limite vos politiques d’autorisation à des cadres particuliers.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-190">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="6f8c8-191">Vous devez `Resource` lancer la `is` propriété à l’aide du mot clé, puis confirmer `InvalidCastException` le casting a réussi à s’assurer que votre code ne se bloque pas avec un moment exécuté sur d’autres cadres:</span><span class="sxs-lookup"><span data-stu-id="6f8c8-191">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end


::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="6f8c8-192">Sous les couvertures, [l’autorisation basée sur](xref:security/authorization/roles) les rôles et [l’autorisation fondée sur les revendications](xref:security/authorization/claims) utilisent une exigence, un gestionnaire d’exigence et une stratégie préconfigurée.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-192">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="6f8c8-193">Ces éléments de base appuient l’expression des évaluations d’autorisation dans le code.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-193">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="6f8c8-194">Il en résulte une structure d’autorisation plus riche, réutilisable et testable.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-194">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="6f8c8-195">Une politique d’autorisation se compose d’une ou plusieurs exigences.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-195">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="6f8c8-196">Il est enregistré dans le cadre de `Startup.ConfigureServices` la configuration du service d’autorisation, dans la méthode:</span><span class="sxs-lookup"><span data-stu-id="6f8c8-196">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,66)]

<span data-ttu-id="6f8c8-197">Dans l’exemple précédent, une politique « AtLeast21 » est créée.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-197">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="6f8c8-198">Il a une&mdash;seule exigence qui d’un âge minimum, qui est fourni comme un paramètre à l’exigence.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-198">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="6f8c8-199">IAuthorizationService (en)</span><span class="sxs-lookup"><span data-stu-id="6f8c8-199">IAuthorizationService</span></span> 

<span data-ttu-id="6f8c8-200">Le service principal qui détermine si <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>l’autorisation est réussie est :</span><span class="sxs-lookup"><span data-stu-id="6f8c8-200">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

<span data-ttu-id="6f8c8-201">Le code précédent met en évidence les deux méthodes de [l’IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span><span class="sxs-lookup"><span data-stu-id="6f8c8-201">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="6f8c8-202"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>est un service de marqueurs sans méthodes, et le mécanisme de suivi de l’autorisation est réussie.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-202"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="6f8c8-203">Chacun <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> est responsable de vérifier si les exigences sont remplies :</span><span class="sxs-lookup"><span data-stu-id="6f8c8-203">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
<!--The following code is a copy/paste from 
https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationHandler.cs -->

```csharp
/// <summary>
/// Classes implementing this interface are able to make a decision if authorization
/// is allowed.
/// </summary>
public interface IAuthorizationHandler
{
    /// <summary>
    /// Makes a decision if authorization is allowed.
    /// </summary>
    /// <param name="context">The authorization information.</param>
    Task HandleAsync(AuthorizationHandlerContext context);
}
```

<span data-ttu-id="6f8c8-204">La <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> classe est ce que le gestionnaire utilise pour marquer si les exigences ont été remplies :</span><span class="sxs-lookup"><span data-stu-id="6f8c8-204">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="6f8c8-205">Le code suivant montre la mise en œuvre par défaut simplifiée (et annotée avec commentaires) du service d’autorisation :</span><span class="sxs-lookup"><span data-stu-id="6f8c8-205">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

```csharp
public async Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user, 
             object resource, IEnumerable<IAuthorizationRequirement> requirements)
{
    // Create a tracking context from the authorization inputs.
    var authContext = _contextFactory.CreateContext(requirements, user, resource);

    // By default this returns an IEnumerable<IAuthorizationHandlers> from DI.
    var handlers = await _handlers.GetHandlersAsync(authContext);

    // Invoke all handlers.
    foreach (var handler in handlers)
    {
        await handler.HandleAsync(authContext);
    }

    // Check the context, by default success is when all requirements have been met.
    return _evaluator.Evaluate(authContext);
}
```

<span data-ttu-id="6f8c8-206">Le code suivant `ConfigureServices`montre un typique :</span><span class="sxs-lookup"><span data-stu-id="6f8c8-206">The following code shows a typical `ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add all of your handlers to DI.
    services.AddSingleton<IAuthorizationHandler, MyHandler1>();
    // MyHandler2, ...

    services.AddSingleton<IAuthorizationHandler, MyHandlerN>();

    // Configure your policies
    services.AddAuthorization(options =>
          options.AddPolicy("Something",
          policy => policy.RequireClaim("Permission", "CanViewPage", "CanViewAnything")));


    services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
}
```

<span data-ttu-id="6f8c8-207">Utiliser <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> `[Authorize(Policy = "Something")]` ou pour l’autorisation.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-207">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="applying-policies-to-mvc-controllers"></a><span data-ttu-id="6f8c8-208">Appliquer des politiques aux contrôleurs MVC</span><span class="sxs-lookup"><span data-stu-id="6f8c8-208">Applying policies to MVC controllers</span></span>

<span data-ttu-id="6f8c8-209">Si vous utilisez Razor Pages, consultez [les stratégies d’application des pages Razor](#applying-policies-to-razor-pages) dans ce document.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-209">If you're using Razor Pages, see [Applying policies to Razor Pages](#applying-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="6f8c8-210">Les stratégies sont appliquées `[Authorize]` aux contrôleurs en utilisant l’attribut avec le nom de la stratégie.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-210">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="6f8c8-211">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="6f8c8-211">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a><span data-ttu-id="6f8c8-212">Appliquer des politiques aux pages Razor</span><span class="sxs-lookup"><span data-stu-id="6f8c8-212">Applying policies to Razor Pages</span></span>

<span data-ttu-id="6f8c8-213">Les stratégies sont appliquées `[Authorize]` aux Pages Razor en utilisant l’attribut avec le nom de la stratégie.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-213">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="6f8c8-214">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="6f8c8-214">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="6f8c8-215">Les politiques peuvent également être appliquées aux Pages Razor en utilisant une [convention d’autorisation](xref:security/authorization/razor-pages-authorization).</span><span class="sxs-lookup"><span data-stu-id="6f8c8-215">Policies can also be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="6f8c8-216">Spécifications</span><span class="sxs-lookup"><span data-stu-id="6f8c8-216">Requirements</span></span>

<span data-ttu-id="6f8c8-217">Une exigence d’autorisation est une collecte de paramètres de données qu’une stratégie peut utiliser pour évaluer le principal utilisateur actuel.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-217">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="6f8c8-218">Dans notre politique « AtLeast21 », l’exigence est un paramètre&mdash;unique de l’âge minimum.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-218">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="6f8c8-219">Une exigence implémente [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), qui est une interface de marqueur vide.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-219">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="6f8c8-220">Une exigence d’âge minimum paramétrée pourrait être mise en œuvre comme suit :</span><span class="sxs-lookup"><span data-stu-id="6f8c8-220">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="6f8c8-221">Si une politique d’autorisation contient plusieurs exigences d’autorisation, toutes les exigences doivent être adoptées pour que l’évaluation de la politique réussisse.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-221">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="6f8c8-222">En d’autres termes, les exigences multiples d’autorisation ajoutées à une seule politique d’autorisation sont traitées sur une base **ET.**</span><span class="sxs-lookup"><span data-stu-id="6f8c8-222">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="6f8c8-223">Une exigence n’a pas besoin d’avoir des données ou des propriétés.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-223">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="6f8c8-224">Gestionnaires d’autorisation</span><span class="sxs-lookup"><span data-stu-id="6f8c8-224">Authorization handlers</span></span>

<span data-ttu-id="6f8c8-225">Un gestionnaire d’autorisation est responsable de l’évaluation des propriétés d’une exigence.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-225">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="6f8c8-226">Le gestionnaire d’autorisation évalue les exigences par rapport à un [texte autorisationhandlercontexte](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) fourni pour déterminer si l’accès est autorisé.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-226">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="6f8c8-227">Une exigence peut avoir [plusieurs gestionnaires](#security-authorization-policies-based-multiple-handlers).</span><span class="sxs-lookup"><span data-stu-id="6f8c8-227">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="6f8c8-228">Un gestionnaire peut hériter [de AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), où `TRequirement` est l’exigence à traiter.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-228">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="6f8c8-229">Alternativement, un gestionnaire peut implémenter [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) pour gérer plus d’un type d’exigence.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-229">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="6f8c8-230">Utiliser un gestionnaire pour une exigence</span><span class="sxs-lookup"><span data-stu-id="6f8c8-230">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="6f8c8-231">Voici un exemple d’une relation individuelles dans laquelle un gestionnaire d’âge minimum utilise une seule exigence :</span><span class="sxs-lookup"><span data-stu-id="6f8c8-231">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="6f8c8-232">Le code précédent détermine si le principal utilisateur actuel a une demande de date de naissance qui a été émise par un émetteur connu et digne de confiance.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-232">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="6f8c8-233">L’autorisation ne peut pas se produire lorsque la réclamation est manquante, auquel cas une tâche terminée est retournée.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-233">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="6f8c8-234">Lorsqu’une réclamation est présente, l’âge de l’utilisateur est calculé.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-234">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="6f8c8-235">Si l’utilisateur satisfait à l’âge minimum défini par l’exigence, l’autorisation est considérée comme réussie.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-235">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="6f8c8-236">Lorsque l’autorisation `context.Succeed` est acceptée, est invoquée avec l’exigence satisfaite comme seul paramètre.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-236">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="6f8c8-237">Utiliser un gestionnaire pour plusieurs exigences</span><span class="sxs-lookup"><span data-stu-id="6f8c8-237">Use a handler for multiple requirements</span></span>

<span data-ttu-id="6f8c8-238">Ce qui suit est un exemple d’une relation unique dans laquelle un gestionnaire d’autorisation peut traiter trois types d’exigences différentes :</span><span class="sxs-lookup"><span data-stu-id="6f8c8-238">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="6f8c8-239">Le code précédent traverse [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;une propriété contenant des exigences non marquées comme réussies.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-239">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="6f8c8-240">Pour `ReadPermission` une exigence, l’utilisateur doit être soit un propriétaire ou un commanditaire pour accéder à la ressource demandée.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-240">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="6f8c8-241">En cas d’exigence, `EditPermission` `DeletePermission` il doit être propriétaire de la ressource demandée.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-241">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="6f8c8-242">Enregistrement des gestionnaires</span><span class="sxs-lookup"><span data-stu-id="6f8c8-242">Handler registration</span></span>

<span data-ttu-id="6f8c8-243">Les manutentionnaires sont inscrits à la collection de services pendant la configuration.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-243">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="6f8c8-244">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="6f8c8-244">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,62-63,66)]

<span data-ttu-id="6f8c8-245">Le code précédent `MinimumAgeHandler` s’inscrit comme un `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`singleton en invoquant .</span><span class="sxs-lookup"><span data-stu-id="6f8c8-245">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="6f8c8-246">Les manutentionnaires peuvent être enregistrés à l’aide de l’une des durées de [vie intégrées](xref:fundamentals/dependency-injection#service-lifetimes)du service.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-246">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="6f8c8-247">Qu’est-ce qu’un gestionnaire devrait revenir?</span><span class="sxs-lookup"><span data-stu-id="6f8c8-247">What should a handler return?</span></span>

<span data-ttu-id="6f8c8-248">Notez `Handle` que la méthode dans l’exemple [du gestionnaire](#security-authorization-handler-example) ne rapporte aucune valeur.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-248">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="6f8c8-249">Comment un statut de succès ou d’échec est-il indiqué?</span><span class="sxs-lookup"><span data-stu-id="6f8c8-249">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="6f8c8-250">Un gestionnaire indique `context.Succeed(IAuthorizationRequirement requirement)`le succès en appelant, en passant l’exigence qui a été validée avec succès.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-250">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="6f8c8-251">Un gestionnaire n’a pas besoin de gérer les défaillances en général, car d’autres gestionnaires pour la même exigence peuvent réussir.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-251">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="6f8c8-252">Pour garantir l’échec, même si `context.Fail`d’autres gestionnaires d’exigences réussissent, appelez .</span><span class="sxs-lookup"><span data-stu-id="6f8c8-252">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="6f8c8-253">Si un `context.Succeed` gestionnaire `context.Fail`appelle ou, tous les autres gestionnaires sont toujours appelés.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-253">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="6f8c8-254">Cela permet aux exigences de produire des effets secondaires, tels que l’enregistrement, qui a lieu même si un autre gestionnaire a validé ou échoué avec succès une exigence.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-254">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="6f8c8-255">Lorsqu’il `false`est configuré, la propriété [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) (disponible en ASP.NET Core 1.1 `context.Fail` et plus tard) court-circuite l’exécution des manutentionnaires lorsqu’il est appelé.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-255">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="6f8c8-256">`InvokeHandlersAfterFailure`par défaut `true`à , auquel cas tous les gestionnaires sont appelés.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-256">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="6f8c8-257">Les préposés à l’autorisation sont appelés même si l’authentification échoue.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-257">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="6f8c8-258">Pourquoi voudrais-je plusieurs gestionnaires pour une exigence?</span><span class="sxs-lookup"><span data-stu-id="6f8c8-258">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="6f8c8-259">Dans les cas où vous souhaitez que l’évaluation soit sur une base **DE OU,** implémentez plusieurs gestionnaires pour une seule exigence.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-259">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="6f8c8-260">Par exemple, Microsoft a des portes qui ne s’ouvrent qu’avec des cartes clés.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-260">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="6f8c8-261">Si vous laissez votre carte-clé à la maison, la réceptionniste imprime un autocollant temporaire et vous ouvre la porte.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-261">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="6f8c8-262">Dans ce scénario, vous auriez une seule exigence, *BuildingEntry*, mais plusieurs gestionnaires, chacun examinant une seule exigence.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-262">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="6f8c8-263">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="6f8c8-263">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="6f8c8-264">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="6f8c8-264">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="6f8c8-265">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="6f8c8-265">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="6f8c8-266">Assurez-vous que les deux gestionnaires sont [enregistrés](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span><span class="sxs-lookup"><span data-stu-id="6f8c8-266">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="6f8c8-267">Si l’un ou l’autre `BuildingEntryRequirement`gestionnaire réussit lorsqu’une politique évalue le, l’évaluation des politiques réussit.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-267">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="using-a-func-to-fulfill-a-policy"></a><span data-ttu-id="6f8c8-268">Utilisation d’un func pour remplir une politique</span><span class="sxs-lookup"><span data-stu-id="6f8c8-268">Using a func to fulfill a policy</span></span>

<span data-ttu-id="6f8c8-269">Il peut y avoir des situations dans lesquelles l’exécution d’une politique est simple à exprimer dans le code.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-269">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="6f8c8-270">Il est possible de `Func<AuthorizationHandlerContext, bool>` fournir un lors de `RequireAssertion` la configuration de votre politique avec le constructeur de police.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-270">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="6f8c8-271">Par exemple, `BadgeEntryHandler` le précédent pourrait être réécrit comme suit :</span><span class="sxs-lookup"><span data-stu-id="6f8c8-271">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=50-51,55-61)]

## <a name="accessing-mvc-request-context-in-handlers"></a><span data-ttu-id="6f8c8-272">Accès au contexte de demande MVC chez les gestionnaires</span><span class="sxs-lookup"><span data-stu-id="6f8c8-272">Accessing MVC request context in handlers</span></span>

<span data-ttu-id="6f8c8-273">La `HandleRequirementAsync` méthode que vous implémentez dans `AuthorizationHandlerContext` un `TRequirement` gestionnaire d’autorisation comporte deux paramètres : un et le que vous manipulez.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-273">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="6f8c8-274">Les cadres tels que MVC ou SignalR sont `Resource` libres `AuthorizationHandlerContext` d’ajouter n’importe quel objet à la propriété sur les informations supplémentaires de passer.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-274">Frameworks such as MVC or SignalR are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="6f8c8-275">Lors de l’utilisation du routage de point final, l’autorisation est généralement gérée par l’autorisation Middleware.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-275">When using endpoint routing, authorization is typically handled by the Authorization Middleware.</span></span> <span data-ttu-id="6f8c8-276">Dans ce cas, la `Resource` propriété <xref:Microsoft.AspNetCore.Http.Endpoint>est un cas de .</span><span class="sxs-lookup"><span data-stu-id="6f8c8-276">In this case, the `Resource` property is an instance of <xref:Microsoft.AspNetCore.Http.Endpoint>.</span></span> <span data-ttu-id="6f8c8-277">Le point de terminaison peut être utilisé pour sonder la ressource sous-jacente à laquelle vous routez.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-277">The endpoint can be used to probe the underlying the resource to which you're routing.</span></span> <span data-ttu-id="6f8c8-278">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="6f8c8-278">For example:</span></span>

```csharp
if (context.Resource is Endpoint endpoint)
{
   var actionDescriptor = endpoint.Metadata.GetMetadata<ControllerActionDescriptor>();
   ...
}
```

<span data-ttu-id="6f8c8-279">Avec le routage traditionnel, ou lorsque l’autorisation se fait `Resource` dans <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> le cadre du filtre d’autorisation de MVC, la valeur de est un cas.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-279">With traditional routing, or when authorization happens as part of MVC's authorization filter, the value of `Resource` is an <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> instance.</span></span> <span data-ttu-id="6f8c8-280">Cette propriété donne `HttpContext` `RouteData`accès à , , et tout le reste fourni par MVC et Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-280">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="6f8c8-281">L’utilisation `Resource` de la propriété est spécifique au cadre.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-281">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="6f8c8-282">L’utilisation `Resource` de l’information dans la propriété limite vos politiques d’autorisation à des cadres particuliers.</span><span class="sxs-lookup"><span data-stu-id="6f8c8-282">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="6f8c8-283">Vous devez `Resource` lancer la `is` propriété à l’aide du mot clé, puis confirmer `InvalidCastException` le casting a réussi à s’assurer que votre code ne se bloque pas avec un moment exécuté sur d’autres cadres:</span><span class="sxs-lookup"><span data-stu-id="6f8c8-283">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end
