---
title: Autorisation basée sur la stratégie dans ASP.NET Core
author: rick-anderson
description: Découvrez comment créer et utiliser des gestionnaires de stratégie d’autorisation pour appliquer les exigences d’autorisation dans une application ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/policies
ms.openlocfilehash: 533bddc9c4499dad99cfdb3089045ea10aed4548
ms.sourcegitcommit: 4437f4c149f1ef6c28796dcfaa2863b4c088169c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85074160"
---
# <a name="policy-based-authorization-in-aspnet-core"></a><span data-ttu-id="67fcd-103">Autorisation basée sur la stratégie dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="67fcd-103">Policy-based authorization in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="67fcd-104">En coulisses, l’autorisation basée [sur les rôles](xref:security/authorization/roles) et l' [autorisation basée sur les revendications](xref:security/authorization/claims) utilisent une spécification, un gestionnaire de spécification et une stratégie préconfigurée.</span><span class="sxs-lookup"><span data-stu-id="67fcd-104">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="67fcd-105">Ces blocs de construction prennent en charge l’expression des évaluations d’autorisation dans le code.</span><span class="sxs-lookup"><span data-stu-id="67fcd-105">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="67fcd-106">Le résultat est une structure d’autorisation plus riche, réutilisable et testable.</span><span class="sxs-lookup"><span data-stu-id="67fcd-106">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="67fcd-107">Une stratégie d’autorisation se compose d’une ou de plusieurs exigences.</span><span class="sxs-lookup"><span data-stu-id="67fcd-107">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="67fcd-108">Elle est inscrite dans le cadre de la configuration du service d’autorisation, dans la `Startup.ConfigureServices` méthode :</span><span class="sxs-lookup"><span data-stu-id="67fcd-108">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53, 58)]

<span data-ttu-id="67fcd-109">Dans l’exemple précédent, une stratégie « AtLeast21 » est créée.</span><span class="sxs-lookup"><span data-stu-id="67fcd-109">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="67fcd-110">Il a une exigence unique &mdash; qui correspond à une ancienneté minimale, qui est fournie en tant que paramètre à la spécification.</span><span class="sxs-lookup"><span data-stu-id="67fcd-110">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="67fcd-111">IAuthorizationService</span><span class="sxs-lookup"><span data-stu-id="67fcd-111">IAuthorizationService</span></span> 

<span data-ttu-id="67fcd-112">Le service principal qui détermine si l’autorisation est réussie est le <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> suivant :</span><span class="sxs-lookup"><span data-stu-id="67fcd-112">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

<span data-ttu-id="67fcd-113">Le code précédent met en évidence les deux méthodes de [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span><span class="sxs-lookup"><span data-stu-id="67fcd-113">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="67fcd-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>est un service de marqueur sans méthode, et le mécanisme de suivi de la réussite de l’autorisation.</span><span class="sxs-lookup"><span data-stu-id="67fcd-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="67fcd-115">Chaque <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> est responsable de la vérification de la satisfaction des exigences suivantes :</span><span class="sxs-lookup"><span data-stu-id="67fcd-115">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
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

<span data-ttu-id="67fcd-116">La <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> classe est ce que le gestionnaire utilise pour marquer si les spécifications ont été satisfaites :</span><span class="sxs-lookup"><span data-stu-id="67fcd-116">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="67fcd-117">Le code suivant illustre l’implémentation par défaut simplifiée (et annotée avec des commentaires) du service d’autorisation :</span><span class="sxs-lookup"><span data-stu-id="67fcd-117">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

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

<span data-ttu-id="67fcd-118">Le code suivant illustre un exemple typique `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="67fcd-118">The following code shows a typical `ConfigureServices`:</span></span>

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

<span data-ttu-id="67fcd-119">Utilisez <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> ou `[Authorize(Policy = "Something")]` pour l’autorisation.</span><span class="sxs-lookup"><span data-stu-id="67fcd-119">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="apply-policies-to-mvc-controllers"></a><span data-ttu-id="67fcd-120">Appliquer des stratégies à des contrôleurs MVC</span><span class="sxs-lookup"><span data-stu-id="67fcd-120">Apply policies to MVC controllers</span></span>

<span data-ttu-id="67fcd-121">Si vous utilisez des Razor pages, consultez [appliquer des stratégies à des Razor pages](#apply-policies-to-razor-pages) de ce document.</span><span class="sxs-lookup"><span data-stu-id="67fcd-121">If you're using Razor Pages, see [Apply policies to Razor Pages](#apply-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="67fcd-122">Les stratégies sont appliquées aux contrôleurs à l’aide de l' `[Authorize]` attribut avec le nom de la stratégie.</span><span class="sxs-lookup"><span data-stu-id="67fcd-122">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="67fcd-123">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="67fcd-123">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="apply-policies-to-razor-pages"></a><span data-ttu-id="67fcd-124">Appliquer des stratégies à des Razor pages</span><span class="sxs-lookup"><span data-stu-id="67fcd-124">Apply policies to Razor Pages</span></span>

<span data-ttu-id="67fcd-125">Les stratégies sont appliquées aux pages à l' Razor aide de l' `[Authorize]` attribut avec le nom de la stratégie.</span><span class="sxs-lookup"><span data-stu-id="67fcd-125">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="67fcd-126">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="67fcd-126">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="67fcd-127">Les stratégies peuvent également être appliquées aux Razor pages à l’aide d’une [Convention d’autorisation](xref:security/authorization/razor-pages-authorization).</span><span class="sxs-lookup"><span data-stu-id="67fcd-127">Policies can also be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="67fcd-128">Spécifications</span><span class="sxs-lookup"><span data-stu-id="67fcd-128">Requirements</span></span>

<span data-ttu-id="67fcd-129">Une spécification d’autorisation est une collection de paramètres de données qu’une stratégie peut utiliser pour évaluer le principal d’utilisateur actuel.</span><span class="sxs-lookup"><span data-stu-id="67fcd-129">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="67fcd-130">Dans notre stratégie « AtLeast21 », l’exigence est un paramètre unique de &mdash; l’âge minimal.</span><span class="sxs-lookup"><span data-stu-id="67fcd-130">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="67fcd-131">Une spécification implémente [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), qui est une interface de marqueur vide.</span><span class="sxs-lookup"><span data-stu-id="67fcd-131">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="67fcd-132">Une exigence d’ancienneté minimale paramétrable peut être implémentée comme suit :</span><span class="sxs-lookup"><span data-stu-id="67fcd-132">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="67fcd-133">Si une stratégie d’autorisation contient plusieurs exigences d’autorisation, toutes les spécifications doivent réussir pour que l’évaluation de la stratégie aboutisse.</span><span class="sxs-lookup"><span data-stu-id="67fcd-133">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="67fcd-134">En d’autres termes, plusieurs exigences d’autorisation ajoutées à une même stratégie d’autorisation sont traitées sur une base **et** .</span><span class="sxs-lookup"><span data-stu-id="67fcd-134">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="67fcd-135">Une exigence n’a pas besoin d’avoir des données ou des propriétés.</span><span class="sxs-lookup"><span data-stu-id="67fcd-135">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="67fcd-136">Gestionnaires d’autorisations</span><span class="sxs-lookup"><span data-stu-id="67fcd-136">Authorization handlers</span></span>

<span data-ttu-id="67fcd-137">Un gestionnaire d’autorisations est responsable de l’évaluation des propriétés d’une spécification.</span><span class="sxs-lookup"><span data-stu-id="67fcd-137">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="67fcd-138">Le gestionnaire d’autorisations évalue les spécifications par rapport à un [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) fourni pour déterminer si l’accès est autorisé.</span><span class="sxs-lookup"><span data-stu-id="67fcd-138">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="67fcd-139">Une spécification peut avoir [plusieurs gestionnaires](#security-authorization-policies-based-multiple-handlers).</span><span class="sxs-lookup"><span data-stu-id="67fcd-139">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="67fcd-140">Un gestionnaire peut hériter de [AuthorizationHandler \<TRequirement> ](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), où `TRequirement` est la spécification à gérer.</span><span class="sxs-lookup"><span data-stu-id="67fcd-140">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="67fcd-141">Un gestionnaire peut également implémenter [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) pour gérer plusieurs types d’exigences.</span><span class="sxs-lookup"><span data-stu-id="67fcd-141">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="67fcd-142">Utiliser un gestionnaire pour une spécification</span><span class="sxs-lookup"><span data-stu-id="67fcd-142">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="67fcd-143">Voici un exemple de relation un-à-un dans laquelle un gestionnaire d’âge minimal utilise une seule exigence :</span><span class="sxs-lookup"><span data-stu-id="67fcd-143">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="67fcd-144">Le code précédent détermine si le principal de l’utilisateur actuel possède une revendication de date de naissance qui a été émise par un émetteur connu et approuvé.</span><span class="sxs-lookup"><span data-stu-id="67fcd-144">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="67fcd-145">L’autorisation ne peut pas se produire lorsque la revendication est manquante, auquel cas une tâche terminée est retournée.</span><span class="sxs-lookup"><span data-stu-id="67fcd-145">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="67fcd-146">Lorsqu’une revendication est présente, l’âge de l’utilisateur est calculé.</span><span class="sxs-lookup"><span data-stu-id="67fcd-146">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="67fcd-147">Si l’utilisateur atteint l’ancienneté minimale définie par la spécification, l’autorisation est considérée comme réussie.</span><span class="sxs-lookup"><span data-stu-id="67fcd-147">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="67fcd-148">Lorsque l’autorisation est réussie, `context.Succeed` est appelée avec la spécification satisfaite comme paramètre unique.</span><span class="sxs-lookup"><span data-stu-id="67fcd-148">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="67fcd-149">Utiliser un gestionnaire pour plusieurs spécifications</span><span class="sxs-lookup"><span data-stu-id="67fcd-149">Use a handler for multiple requirements</span></span>

<span data-ttu-id="67fcd-150">Voici un exemple de relation un-à-plusieurs dans laquelle un gestionnaire d’autorisations peut gérer trois types différents d’exigences :</span><span class="sxs-lookup"><span data-stu-id="67fcd-150">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="67fcd-151">Le code précédent parcourt [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements) &mdash; une propriété contenant des exigences non marquées comme ayant réussi.</span><span class="sxs-lookup"><span data-stu-id="67fcd-151">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="67fcd-152">Pour une `ReadPermission` spécification, l’utilisateur doit être un propriétaire ou un sponsor pour accéder à la ressource demandée.</span><span class="sxs-lookup"><span data-stu-id="67fcd-152">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="67fcd-153">Dans le cas d’une `EditPermission` `DeletePermission` exigence ou, il doit être propriétaire de l’accès à la ressource demandée.</span><span class="sxs-lookup"><span data-stu-id="67fcd-153">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="67fcd-154">Inscription du gestionnaire</span><span class="sxs-lookup"><span data-stu-id="67fcd-154">Handler registration</span></span>

<span data-ttu-id="67fcd-155">Les gestionnaires sont inscrits dans la collection de services pendant la configuration.</span><span class="sxs-lookup"><span data-stu-id="67fcd-155">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="67fcd-156">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="67fcd-156">For example:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53-55, 58)]

<span data-ttu-id="67fcd-157">Le code précédent s’inscrit `MinimumAgeHandler` en tant que singleton en appelant `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();` .</span><span class="sxs-lookup"><span data-stu-id="67fcd-157">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="67fcd-158">Les gestionnaires peuvent être inscrits à l’aide de l’une des [durées de vie de service](xref:fundamentals/dependency-injection#service-lifetimes)intégrées.</span><span class="sxs-lookup"><span data-stu-id="67fcd-158">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="67fcd-159">Que doit retourner un gestionnaire ?</span><span class="sxs-lookup"><span data-stu-id="67fcd-159">What should a handler return?</span></span>

<span data-ttu-id="67fcd-160">Notez que la `Handle` méthode dans l' [exemple de gestionnaire](#security-authorization-handler-example) ne retourne aucune valeur.</span><span class="sxs-lookup"><span data-stu-id="67fcd-160">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="67fcd-161">Comment l’état de la réussite ou de l’échec est-il indiqué ?</span><span class="sxs-lookup"><span data-stu-id="67fcd-161">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="67fcd-162">Un gestionnaire indique la réussite en appelant `context.Succeed(IAuthorizationRequirement requirement)` , en passant la spécification qui a été validée avec succès.</span><span class="sxs-lookup"><span data-stu-id="67fcd-162">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="67fcd-163">Un gestionnaire n’A pas besoin de gérer les défaillances en général, car d’autres gestionnaires pour la même spécification peuvent être exécutés correctement.</span><span class="sxs-lookup"><span data-stu-id="67fcd-163">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="67fcd-164">Pour garantir la défaillance, même si d’autres gestionnaires de spécifications sont correctement exécutés, appelez `context.Fail` .</span><span class="sxs-lookup"><span data-stu-id="67fcd-164">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="67fcd-165">Si un gestionnaire appelle `context.Succeed` ou `context.Fail` , tous les autres gestionnaires sont toujours appelés.</span><span class="sxs-lookup"><span data-stu-id="67fcd-165">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="67fcd-166">Cela permet aux exigences de produire des effets secondaires, tels que la journalisation, qui se produit même si un autre gestionnaire a validé ou échoué avec succès.</span><span class="sxs-lookup"><span data-stu-id="67fcd-166">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="67fcd-167">Quand la valeur `false` est affectée à, la propriété [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) (disponible dans ASP.net Core 1,1 et versions ultérieures) réduit les courts-circuits de l’exécution des gestionnaires lorsque `context.Fail` est appelé.</span><span class="sxs-lookup"><span data-stu-id="67fcd-167">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="67fcd-168">`InvokeHandlersAfterFailure`la valeur par défaut `true` est, auquel cas tous les gestionnaires sont appelés.</span><span class="sxs-lookup"><span data-stu-id="67fcd-168">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="67fcd-169">Les gestionnaires d’autorisations sont appelés même si l’authentification échoue.</span><span class="sxs-lookup"><span data-stu-id="67fcd-169">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="67fcd-170">Pourquoi dois-je utiliser plusieurs gestionnaires pour une spécification ?</span><span class="sxs-lookup"><span data-stu-id="67fcd-170">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="67fcd-171">Dans les cas où vous souhaitez que l’évaluation soit sur une base **ou** , implémentez plusieurs gestionnaires pour une seule exigence.</span><span class="sxs-lookup"><span data-stu-id="67fcd-171">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="67fcd-172">Par exemple, Microsoft possède des portes qui s’ouvrent uniquement avec des cartes clés.</span><span class="sxs-lookup"><span data-stu-id="67fcd-172">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="67fcd-173">Si vous laissez votre carte clé chez vous, la réceptionniste imprime un autocollant temporaire et ouvre la porte pour vous.</span><span class="sxs-lookup"><span data-stu-id="67fcd-173">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="67fcd-174">Dans ce scénario, vous auriez besoin d’une seule exigence, *BuildingEntry*, mais plusieurs gestionnaires, chacun examinant une seule exigence.</span><span class="sxs-lookup"><span data-stu-id="67fcd-174">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="67fcd-175">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="67fcd-175">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="67fcd-176">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="67fcd-176">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="67fcd-177">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="67fcd-177">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="67fcd-178">Vérifiez que les deux gestionnaires sont [inscrits](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span><span class="sxs-lookup"><span data-stu-id="67fcd-178">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="67fcd-179">Si l’un des gestionnaires aboutit lorsqu’une stratégie évalue le `BuildingEntryRequirement` , l’évaluation de la stratégie aboutit.</span><span class="sxs-lookup"><span data-stu-id="67fcd-179">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="use-a-func-to-fulfill-a-policy"></a><span data-ttu-id="67fcd-180">Utiliser un Func pour accomplir une stratégie</span><span class="sxs-lookup"><span data-stu-id="67fcd-180">Use a func to fulfill a policy</span></span>

<span data-ttu-id="67fcd-181">Il peut arriver que la réalisation d’une stratégie soit simple à exprimer dans le code.</span><span class="sxs-lookup"><span data-stu-id="67fcd-181">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="67fcd-182">Il est possible de fournir un `Func<AuthorizationHandlerContext, bool>` lors de la configuration de votre stratégie avec le `RequireAssertion` Générateur de stratégie.</span><span class="sxs-lookup"><span data-stu-id="67fcd-182">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="67fcd-183">Par exemple, la précédente `BadgeEntryHandler` peut être réécrite comme suit :</span><span class="sxs-lookup"><span data-stu-id="67fcd-183">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=42-43,47-53)]

## <a name="access-mvc-request-context-in-handlers"></a><span data-ttu-id="67fcd-184">Accéder au contexte de requête MVC dans les gestionnaires</span><span class="sxs-lookup"><span data-stu-id="67fcd-184">Access MVC request context in handlers</span></span>

<span data-ttu-id="67fcd-185">La `HandleRequirementAsync` méthode que vous implémentez dans un gestionnaire d’autorisations a deux paramètres : un `AuthorizationHandlerContext` et le `TRequirement` que vous gérez.</span><span class="sxs-lookup"><span data-stu-id="67fcd-185">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="67fcd-186">Les infrastructures telles que MVC ou SignalR sont libres d’ajouter n’importe quel objet à la `Resource` propriété sur le `AuthorizationHandlerContext` pour passer des informations supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="67fcd-186">Frameworks such as MVC or SignalR are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="67fcd-187">Lorsque vous utilisez le routage de point de terminaison, l’autorisation est généralement gérée par l’intergiciel (middleware) d’autorisation.</span><span class="sxs-lookup"><span data-stu-id="67fcd-187">When using endpoint routing, authorization is typically handled by the Authorization Middleware.</span></span> <span data-ttu-id="67fcd-188">Dans ce cas, la `Resource` propriété est une instance de <xref:Microsoft.AspNetCore.Http.Endpoint> .</span><span class="sxs-lookup"><span data-stu-id="67fcd-188">In this case, the `Resource` property is an instance of <xref:Microsoft.AspNetCore.Http.Endpoint>.</span></span> <span data-ttu-id="67fcd-189">Le point de terminaison peut être utilisé pour sonder la ressource sous-jacente vers laquelle vous effectuez le routage.</span><span class="sxs-lookup"><span data-stu-id="67fcd-189">The endpoint can be used to probe the underlying resource to which you're routing.</span></span> <span data-ttu-id="67fcd-190">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="67fcd-190">For example:</span></span>

```csharp
if (context.Resource is Endpoint endpoint)
{
   var actionDescriptor = endpoint.Metadata.GetMetadata<ControllerActionDescriptor>();
   ...
}
```

<span data-ttu-id="67fcd-191">Le point de terminaison ne fournit pas l’accès à l’objet actuel `HttpContext` .</span><span class="sxs-lookup"><span data-stu-id="67fcd-191">The endpoint doesn't provide access to the current `HttpContext`.</span></span> <span data-ttu-id="67fcd-192">Lorsque vous utilisez le routage de point de terminaison, utilisez `IHttpContextAcessor` pour accéder à `HttpContext` l’intérieur d’un gestionnaire d’autorisations.</span><span class="sxs-lookup"><span data-stu-id="67fcd-192">When using endpoint routing, use `IHttpContextAcessor` to access `HttpContext` inside of an authorization handler.</span></span> <span data-ttu-id="67fcd-193">Pour plus d’informations, consultez [utiliser HttpContext à partir de composants personnalisés](xref:fundamentals/httpcontext#use-httpcontext-from-custom-components).</span><span class="sxs-lookup"><span data-stu-id="67fcd-193">For more information, see [Use HttpContext from custom components](xref:fundamentals/httpcontext#use-httpcontext-from-custom-components).</span></span>

<span data-ttu-id="67fcd-194">Avec le routage traditionnel, ou lorsque l’autorisation a lieu dans le cadre du filtre d’autorisation MVC, la valeur de `Resource` est une <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> instance.</span><span class="sxs-lookup"><span data-stu-id="67fcd-194">With traditional routing, or when authorization happens as part of MVC's authorization filter, the value of `Resource` is an <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> instance.</span></span> <span data-ttu-id="67fcd-195">Cette propriété fournit l’accès à `HttpContext` , `RouteData` et tout le reste fourni par MVC et les Razor pages.</span><span class="sxs-lookup"><span data-stu-id="67fcd-195">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="67fcd-196">L’utilisation de la `Resource` propriété est spécifique à l’infrastructure.</span><span class="sxs-lookup"><span data-stu-id="67fcd-196">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="67fcd-197">L’utilisation des informations de la `Resource` propriété limite vos stratégies d’autorisation à des infrastructures spécifiques.</span><span class="sxs-lookup"><span data-stu-id="67fcd-197">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="67fcd-198">Vous devez effectuer un cast de la `Resource` propriété à l’aide du `is` mot clé, puis confirmer que le cast a réussi pour s’assurer que votre code ne se bloque pas avec un lorsqu’il est `InvalidCastException` exécuté sur d’autres infrastructures :</span><span class="sxs-lookup"><span data-stu-id="67fcd-198">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

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

<span data-ttu-id="67fcd-199">En coulisses, l’autorisation basée [sur les rôles](xref:security/authorization/roles) et l' [autorisation basée sur les revendications](xref:security/authorization/claims) utilisent une spécification, un gestionnaire de spécification et une stratégie préconfigurée.</span><span class="sxs-lookup"><span data-stu-id="67fcd-199">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="67fcd-200">Ces blocs de construction prennent en charge l’expression des évaluations d’autorisation dans le code.</span><span class="sxs-lookup"><span data-stu-id="67fcd-200">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="67fcd-201">Le résultat est une structure d’autorisation plus riche, réutilisable et testable.</span><span class="sxs-lookup"><span data-stu-id="67fcd-201">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="67fcd-202">Une stratégie d’autorisation se compose d’une ou de plusieurs exigences.</span><span class="sxs-lookup"><span data-stu-id="67fcd-202">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="67fcd-203">Elle est inscrite dans le cadre de la configuration du service d’autorisation, dans la `Startup.ConfigureServices` méthode :</span><span class="sxs-lookup"><span data-stu-id="67fcd-203">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,66)]

<span data-ttu-id="67fcd-204">Dans l’exemple précédent, une stratégie « AtLeast21 » est créée.</span><span class="sxs-lookup"><span data-stu-id="67fcd-204">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="67fcd-205">Il a une exigence unique &mdash; qui correspond à une ancienneté minimale, qui est fournie en tant que paramètre à la spécification.</span><span class="sxs-lookup"><span data-stu-id="67fcd-205">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="67fcd-206">IAuthorizationService</span><span class="sxs-lookup"><span data-stu-id="67fcd-206">IAuthorizationService</span></span> 

<span data-ttu-id="67fcd-207">Le service principal qui détermine si l’autorisation est réussie est le <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> suivant :</span><span class="sxs-lookup"><span data-stu-id="67fcd-207">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

<span data-ttu-id="67fcd-208">Le code précédent met en évidence les deux méthodes de [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span><span class="sxs-lookup"><span data-stu-id="67fcd-208">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="67fcd-209"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>est un service de marqueur sans méthode, et le mécanisme de suivi de la réussite de l’autorisation.</span><span class="sxs-lookup"><span data-stu-id="67fcd-209"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="67fcd-210">Chaque <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> est responsable de la vérification de la satisfaction des exigences suivantes :</span><span class="sxs-lookup"><span data-stu-id="67fcd-210">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
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

<span data-ttu-id="67fcd-211">La <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> classe est ce que le gestionnaire utilise pour marquer si les spécifications ont été satisfaites :</span><span class="sxs-lookup"><span data-stu-id="67fcd-211">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="67fcd-212">Le code suivant illustre l’implémentation par défaut simplifiée (et annotée avec des commentaires) du service d’autorisation :</span><span class="sxs-lookup"><span data-stu-id="67fcd-212">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

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

<span data-ttu-id="67fcd-213">Le code suivant illustre un exemple typique `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="67fcd-213">The following code shows a typical `ConfigureServices`:</span></span>

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

<span data-ttu-id="67fcd-214">Utilisez <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> ou `[Authorize(Policy = "Something")]` pour l’autorisation.</span><span class="sxs-lookup"><span data-stu-id="67fcd-214">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="apply-policies-to-mvc-controllers"></a><span data-ttu-id="67fcd-215">Appliquer des stratégies à des contrôleurs MVC</span><span class="sxs-lookup"><span data-stu-id="67fcd-215">Apply policies to MVC controllers</span></span>

<span data-ttu-id="67fcd-216">Si vous utilisez des Razor pages, consultez [appliquer des stratégies à des Razor pages](#apply-policies-to-razor-pages) de ce document.</span><span class="sxs-lookup"><span data-stu-id="67fcd-216">If you're using Razor Pages, see [Apply policies to Razor Pages](#apply-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="67fcd-217">Les stratégies sont appliquées aux contrôleurs à l’aide de l' `[Authorize]` attribut avec le nom de la stratégie.</span><span class="sxs-lookup"><span data-stu-id="67fcd-217">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="67fcd-218">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="67fcd-218">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="apply-policies-to-razor-pages"></a><span data-ttu-id="67fcd-219">Appliquer des stratégies à des Razor pages</span><span class="sxs-lookup"><span data-stu-id="67fcd-219">Apply policies to Razor Pages</span></span>

<span data-ttu-id="67fcd-220">Les stratégies sont appliquées aux pages à l' Razor aide de l' `[Authorize]` attribut avec le nom de la stratégie.</span><span class="sxs-lookup"><span data-stu-id="67fcd-220">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="67fcd-221">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="67fcd-221">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="67fcd-222">Les stratégies peuvent également être appliquées aux Razor pages à l’aide d’une [Convention d’autorisation](xref:security/authorization/razor-pages-authorization).</span><span class="sxs-lookup"><span data-stu-id="67fcd-222">Policies can also be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="67fcd-223">Spécifications</span><span class="sxs-lookup"><span data-stu-id="67fcd-223">Requirements</span></span>

<span data-ttu-id="67fcd-224">Une spécification d’autorisation est une collection de paramètres de données qu’une stratégie peut utiliser pour évaluer le principal d’utilisateur actuel.</span><span class="sxs-lookup"><span data-stu-id="67fcd-224">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="67fcd-225">Dans notre stratégie « AtLeast21 », l’exigence est un paramètre unique de &mdash; l’âge minimal.</span><span class="sxs-lookup"><span data-stu-id="67fcd-225">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="67fcd-226">Une spécification implémente [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), qui est une interface de marqueur vide.</span><span class="sxs-lookup"><span data-stu-id="67fcd-226">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="67fcd-227">Une exigence d’ancienneté minimale paramétrable peut être implémentée comme suit :</span><span class="sxs-lookup"><span data-stu-id="67fcd-227">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="67fcd-228">Si une stratégie d’autorisation contient plusieurs exigences d’autorisation, toutes les spécifications doivent réussir pour que l’évaluation de la stratégie aboutisse.</span><span class="sxs-lookup"><span data-stu-id="67fcd-228">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="67fcd-229">En d’autres termes, plusieurs exigences d’autorisation ajoutées à une même stratégie d’autorisation sont traitées sur une base **et** .</span><span class="sxs-lookup"><span data-stu-id="67fcd-229">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="67fcd-230">Une exigence n’a pas besoin d’avoir des données ou des propriétés.</span><span class="sxs-lookup"><span data-stu-id="67fcd-230">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="67fcd-231">Gestionnaires d’autorisations</span><span class="sxs-lookup"><span data-stu-id="67fcd-231">Authorization handlers</span></span>

<span data-ttu-id="67fcd-232">Un gestionnaire d’autorisations est responsable de l’évaluation des propriétés d’une spécification.</span><span class="sxs-lookup"><span data-stu-id="67fcd-232">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="67fcd-233">Le gestionnaire d’autorisations évalue les spécifications par rapport à un [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) fourni pour déterminer si l’accès est autorisé.</span><span class="sxs-lookup"><span data-stu-id="67fcd-233">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="67fcd-234">Une spécification peut avoir [plusieurs gestionnaires](#security-authorization-policies-based-multiple-handlers).</span><span class="sxs-lookup"><span data-stu-id="67fcd-234">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="67fcd-235">Un gestionnaire peut hériter de [AuthorizationHandler \<TRequirement> ](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), où `TRequirement` est la spécification à gérer.</span><span class="sxs-lookup"><span data-stu-id="67fcd-235">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="67fcd-236">Un gestionnaire peut également implémenter [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) pour gérer plusieurs types d’exigences.</span><span class="sxs-lookup"><span data-stu-id="67fcd-236">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="67fcd-237">Utiliser un gestionnaire pour une spécification</span><span class="sxs-lookup"><span data-stu-id="67fcd-237">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="67fcd-238">Voici un exemple de relation un-à-un dans laquelle un gestionnaire d’âge minimal utilise une seule exigence :</span><span class="sxs-lookup"><span data-stu-id="67fcd-238">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="67fcd-239">Le code précédent détermine si le principal de l’utilisateur actuel possède une revendication de date de naissance qui a été émise par un émetteur connu et approuvé.</span><span class="sxs-lookup"><span data-stu-id="67fcd-239">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="67fcd-240">L’autorisation ne peut pas se produire lorsque la revendication est manquante, auquel cas une tâche terminée est retournée.</span><span class="sxs-lookup"><span data-stu-id="67fcd-240">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="67fcd-241">Lorsqu’une revendication est présente, l’âge de l’utilisateur est calculé.</span><span class="sxs-lookup"><span data-stu-id="67fcd-241">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="67fcd-242">Si l’utilisateur atteint l’ancienneté minimale définie par la spécification, l’autorisation est considérée comme réussie.</span><span class="sxs-lookup"><span data-stu-id="67fcd-242">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="67fcd-243">Lorsque l’autorisation est réussie, `context.Succeed` est appelée avec la spécification satisfaite comme paramètre unique.</span><span class="sxs-lookup"><span data-stu-id="67fcd-243">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="67fcd-244">Utiliser un gestionnaire pour plusieurs spécifications</span><span class="sxs-lookup"><span data-stu-id="67fcd-244">Use a handler for multiple requirements</span></span>

<span data-ttu-id="67fcd-245">Voici un exemple de relation un-à-plusieurs dans laquelle un gestionnaire d’autorisations peut gérer trois types différents d’exigences :</span><span class="sxs-lookup"><span data-stu-id="67fcd-245">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="67fcd-246">Le code précédent parcourt [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements) &mdash; une propriété contenant des exigences non marquées comme ayant réussi.</span><span class="sxs-lookup"><span data-stu-id="67fcd-246">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="67fcd-247">Pour une `ReadPermission` spécification, l’utilisateur doit être un propriétaire ou un sponsor pour accéder à la ressource demandée.</span><span class="sxs-lookup"><span data-stu-id="67fcd-247">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="67fcd-248">Dans le cas d’une `EditPermission` `DeletePermission` exigence ou, il doit être propriétaire de l’accès à la ressource demandée.</span><span class="sxs-lookup"><span data-stu-id="67fcd-248">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="67fcd-249">Inscription du gestionnaire</span><span class="sxs-lookup"><span data-stu-id="67fcd-249">Handler registration</span></span>

<span data-ttu-id="67fcd-250">Les gestionnaires sont inscrits dans la collection de services pendant la configuration.</span><span class="sxs-lookup"><span data-stu-id="67fcd-250">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="67fcd-251">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="67fcd-251">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,62-63,66)]

<span data-ttu-id="67fcd-252">Le code précédent s’inscrit `MinimumAgeHandler` en tant que singleton en appelant `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();` .</span><span class="sxs-lookup"><span data-stu-id="67fcd-252">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="67fcd-253">Les gestionnaires peuvent être inscrits à l’aide de l’une des [durées de vie de service](xref:fundamentals/dependency-injection#service-lifetimes)intégrées.</span><span class="sxs-lookup"><span data-stu-id="67fcd-253">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="67fcd-254">Que doit retourner un gestionnaire ?</span><span class="sxs-lookup"><span data-stu-id="67fcd-254">What should a handler return?</span></span>

<span data-ttu-id="67fcd-255">Notez que la `Handle` méthode dans l' [exemple de gestionnaire](#security-authorization-handler-example) ne retourne aucune valeur.</span><span class="sxs-lookup"><span data-stu-id="67fcd-255">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="67fcd-256">Comment l’état de la réussite ou de l’échec est-il indiqué ?</span><span class="sxs-lookup"><span data-stu-id="67fcd-256">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="67fcd-257">Un gestionnaire indique la réussite en appelant `context.Succeed(IAuthorizationRequirement requirement)` , en passant la spécification qui a été validée avec succès.</span><span class="sxs-lookup"><span data-stu-id="67fcd-257">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="67fcd-258">Un gestionnaire n’A pas besoin de gérer les défaillances en général, car d’autres gestionnaires pour la même spécification peuvent être exécutés correctement.</span><span class="sxs-lookup"><span data-stu-id="67fcd-258">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="67fcd-259">Pour garantir la défaillance, même si d’autres gestionnaires de spécifications sont correctement exécutés, appelez `context.Fail` .</span><span class="sxs-lookup"><span data-stu-id="67fcd-259">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="67fcd-260">Si un gestionnaire appelle `context.Succeed` ou `context.Fail` , tous les autres gestionnaires sont toujours appelés.</span><span class="sxs-lookup"><span data-stu-id="67fcd-260">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="67fcd-261">Cela permet aux exigences de produire des effets secondaires, tels que la journalisation, qui se produit même si un autre gestionnaire a validé ou échoué avec succès.</span><span class="sxs-lookup"><span data-stu-id="67fcd-261">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="67fcd-262">Quand la valeur `false` est affectée à, la propriété [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) (disponible dans ASP.net Core 1,1 et versions ultérieures) réduit les courts-circuits de l’exécution des gestionnaires lorsque `context.Fail` est appelé.</span><span class="sxs-lookup"><span data-stu-id="67fcd-262">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="67fcd-263">`InvokeHandlersAfterFailure`la valeur par défaut `true` est, auquel cas tous les gestionnaires sont appelés.</span><span class="sxs-lookup"><span data-stu-id="67fcd-263">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="67fcd-264">Les gestionnaires d’autorisations sont appelés même si l’authentification échoue.</span><span class="sxs-lookup"><span data-stu-id="67fcd-264">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="67fcd-265">Pourquoi dois-je utiliser plusieurs gestionnaires pour une spécification ?</span><span class="sxs-lookup"><span data-stu-id="67fcd-265">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="67fcd-266">Dans les cas où vous souhaitez que l’évaluation soit sur une base **ou** , implémentez plusieurs gestionnaires pour une seule exigence.</span><span class="sxs-lookup"><span data-stu-id="67fcd-266">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="67fcd-267">Par exemple, Microsoft possède des portes qui s’ouvrent uniquement avec des cartes clés.</span><span class="sxs-lookup"><span data-stu-id="67fcd-267">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="67fcd-268">Si vous laissez votre carte clé chez vous, la réceptionniste imprime un autocollant temporaire et ouvre la porte pour vous.</span><span class="sxs-lookup"><span data-stu-id="67fcd-268">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="67fcd-269">Dans ce scénario, vous auriez besoin d’une seule exigence, *BuildingEntry*, mais plusieurs gestionnaires, chacun examinant une seule exigence.</span><span class="sxs-lookup"><span data-stu-id="67fcd-269">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="67fcd-270">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="67fcd-270">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="67fcd-271">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="67fcd-271">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="67fcd-272">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="67fcd-272">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="67fcd-273">Vérifiez que les deux gestionnaires sont [inscrits](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span><span class="sxs-lookup"><span data-stu-id="67fcd-273">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="67fcd-274">Si l’un des gestionnaires aboutit lorsqu’une stratégie évalue le `BuildingEntryRequirement` , l’évaluation de la stratégie aboutit.</span><span class="sxs-lookup"><span data-stu-id="67fcd-274">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="use-a-func-to-fulfill-a-policy"></a><span data-ttu-id="67fcd-275">Utiliser un Func pour accomplir une stratégie</span><span class="sxs-lookup"><span data-stu-id="67fcd-275">Use a func to fulfill a policy</span></span>

<span data-ttu-id="67fcd-276">Il peut arriver que la réalisation d’une stratégie soit simple à exprimer dans le code.</span><span class="sxs-lookup"><span data-stu-id="67fcd-276">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="67fcd-277">Il est possible de fournir un `Func<AuthorizationHandlerContext, bool>` lors de la configuration de votre stratégie avec le `RequireAssertion` Générateur de stratégie.</span><span class="sxs-lookup"><span data-stu-id="67fcd-277">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="67fcd-278">Par exemple, la précédente `BadgeEntryHandler` peut être réécrite comme suit :</span><span class="sxs-lookup"><span data-stu-id="67fcd-278">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=50-51,55-61)]

## <a name="access-mvc-request-context-in-handlers"></a><span data-ttu-id="67fcd-279">Accéder au contexte de requête MVC dans les gestionnaires</span><span class="sxs-lookup"><span data-stu-id="67fcd-279">Access MVC request context in handlers</span></span>

<span data-ttu-id="67fcd-280">La `HandleRequirementAsync` méthode que vous implémentez dans un gestionnaire d’autorisations a deux paramètres : un `AuthorizationHandlerContext` et le `TRequirement` que vous gérez.</span><span class="sxs-lookup"><span data-stu-id="67fcd-280">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="67fcd-281">Les infrastructures telles que MVC ou SignalR sont libres d’ajouter n’importe quel objet à la `Resource` propriété sur le `AuthorizationHandlerContext` pour passer des informations supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="67fcd-281">Frameworks such as MVC or SignalR are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="67fcd-282">Par exemple, MVC passe une instance de [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) dans la `Resource` propriété.</span><span class="sxs-lookup"><span data-stu-id="67fcd-282">For example, MVC passes an instance of [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) in the `Resource` property.</span></span> <span data-ttu-id="67fcd-283">Cette propriété fournit l’accès à `HttpContext` , `RouteData` et tout le reste fourni par MVC et les Razor pages.</span><span class="sxs-lookup"><span data-stu-id="67fcd-283">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="67fcd-284">L’utilisation de la `Resource` propriété est spécifique à l’infrastructure.</span><span class="sxs-lookup"><span data-stu-id="67fcd-284">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="67fcd-285">L’utilisation des informations de la `Resource` propriété limite vos stratégies d’autorisation à des infrastructures spécifiques.</span><span class="sxs-lookup"><span data-stu-id="67fcd-285">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="67fcd-286">Vous devez effectuer un cast de la `Resource` propriété à l’aide du `is` mot clé, puis confirmer que le cast a réussi pour s’assurer que votre code ne se bloque pas avec un lorsqu’il est `InvalidCastException` exécuté sur d’autres infrastructures :</span><span class="sxs-lookup"><span data-stu-id="67fcd-286">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end
