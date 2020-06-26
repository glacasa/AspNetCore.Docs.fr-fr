---
title: Autorisation basée sur la stratégie dans ASP.NET Core
author: rick-anderson
description: Découvrez comment créer et utiliser des gestionnaires de stratégie d’autorisation pour appliquer les exigences d’autorisation dans une application ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/policies
ms.openlocfilehash: 8c68f2a15d07909d4576a2426d92f9beaa91fbb7
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408069"
---
# <a name="policy-based-authorization-in-aspnet-core"></a><span data-ttu-id="87149-103">Autorisation basée sur la stratégie dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="87149-103">Policy-based authorization in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="87149-104">En coulisses, l’autorisation basée [sur les rôles](xref:security/authorization/roles) et l' [autorisation basée sur les revendications](xref:security/authorization/claims) utilisent une spécification, un gestionnaire de spécification et une stratégie préconfigurée.</span><span class="sxs-lookup"><span data-stu-id="87149-104">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="87149-105">Ces blocs de construction prennent en charge l’expression des évaluations d’autorisation dans le code.</span><span class="sxs-lookup"><span data-stu-id="87149-105">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="87149-106">Le résultat est une structure d’autorisation plus riche, réutilisable et testable.</span><span class="sxs-lookup"><span data-stu-id="87149-106">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="87149-107">Une stratégie d’autorisation se compose d’une ou de plusieurs exigences.</span><span class="sxs-lookup"><span data-stu-id="87149-107">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="87149-108">Elle est inscrite dans le cadre de la configuration du service d’autorisation, dans la `Startup.ConfigureServices` méthode :</span><span class="sxs-lookup"><span data-stu-id="87149-108">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53, 58)]

<span data-ttu-id="87149-109">Dans l’exemple précédent, une stratégie « AtLeast21 » est créée.</span><span class="sxs-lookup"><span data-stu-id="87149-109">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="87149-110">Il a une exigence unique &mdash; qui correspond à une ancienneté minimale, qui est fournie en tant que paramètre à la spécification.</span><span class="sxs-lookup"><span data-stu-id="87149-110">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="87149-111">IAuthorizationService</span><span class="sxs-lookup"><span data-stu-id="87149-111">IAuthorizationService</span></span> 

<span data-ttu-id="87149-112">Le service principal qui détermine si l’autorisation est réussie est le <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> suivant :</span><span class="sxs-lookup"><span data-stu-id="87149-112">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

<span data-ttu-id="87149-113">Le code précédent met en évidence les deux méthodes de [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span><span class="sxs-lookup"><span data-stu-id="87149-113">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="87149-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>est un service de marqueur sans méthode, et le mécanisme de suivi de la réussite de l’autorisation.</span><span class="sxs-lookup"><span data-stu-id="87149-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="87149-115">Chaque <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> est responsable de la vérification de la satisfaction des exigences suivantes :</span><span class="sxs-lookup"><span data-stu-id="87149-115">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
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

<span data-ttu-id="87149-116">La <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> classe est ce que le gestionnaire utilise pour marquer si les spécifications ont été satisfaites :</span><span class="sxs-lookup"><span data-stu-id="87149-116">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="87149-117">Le code suivant illustre l’implémentation par défaut simplifiée (et annotée avec des commentaires) du service d’autorisation :</span><span class="sxs-lookup"><span data-stu-id="87149-117">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

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

<span data-ttu-id="87149-118">Le code suivant illustre un exemple typique `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="87149-118">The following code shows a typical `ConfigureServices`:</span></span>

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

<span data-ttu-id="87149-119">Utilisez <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> ou `[Authorize(Policy = "Something")]` pour l’autorisation.</span><span class="sxs-lookup"><span data-stu-id="87149-119">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="apply-policies-to-mvc-controllers"></a><span data-ttu-id="87149-120">Appliquer des stratégies à des contrôleurs MVC</span><span class="sxs-lookup"><span data-stu-id="87149-120">Apply policies to MVC controllers</span></span>

<span data-ttu-id="87149-121">Si vous utilisez des Razor pages, consultez [appliquer des stratégies à des Razor pages](#apply-policies-to-razor-pages) de ce document.</span><span class="sxs-lookup"><span data-stu-id="87149-121">If you're using Razor Pages, see [Apply policies to Razor Pages](#apply-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="87149-122">Les stratégies sont appliquées aux contrôleurs à l’aide de l' `[Authorize]` attribut avec le nom de la stratégie.</span><span class="sxs-lookup"><span data-stu-id="87149-122">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="87149-123">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="87149-123">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="apply-policies-to-razor-pages"></a><span data-ttu-id="87149-124">Appliquer des stratégies à des Razor pages</span><span class="sxs-lookup"><span data-stu-id="87149-124">Apply policies to Razor Pages</span></span>

<span data-ttu-id="87149-125">Les stratégies sont appliquées aux pages à l' Razor aide de l' `[Authorize]` attribut avec le nom de la stratégie.</span><span class="sxs-lookup"><span data-stu-id="87149-125">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="87149-126">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="87149-126">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="87149-127">Les stratégies ***ne peuvent pas*** être appliquées au Razor niveau du gestionnaire de page, elles doivent être appliquées à la page.</span><span class="sxs-lookup"><span data-stu-id="87149-127">Policies can ***not*** be applied at the Razor Page handler level, they must be applied to the Page.</span></span>

<span data-ttu-id="87149-128">Les stratégies peuvent être appliquées à Razor des pages à l’aide d’une [Convention d’autorisation](xref:security/authorization/razor-pages-authorization).</span><span class="sxs-lookup"><span data-stu-id="87149-128">Policies can be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="87149-129">Configuration requise</span><span class="sxs-lookup"><span data-stu-id="87149-129">Requirements</span></span>

<span data-ttu-id="87149-130">Une spécification d’autorisation est une collection de paramètres de données qu’une stratégie peut utiliser pour évaluer le principal d’utilisateur actuel.</span><span class="sxs-lookup"><span data-stu-id="87149-130">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="87149-131">Dans notre stratégie « AtLeast21 », l’exigence est un paramètre unique de &mdash; l’âge minimal.</span><span class="sxs-lookup"><span data-stu-id="87149-131">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="87149-132">Une spécification implémente [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), qui est une interface de marqueur vide.</span><span class="sxs-lookup"><span data-stu-id="87149-132">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="87149-133">Une exigence d’ancienneté minimale paramétrable peut être implémentée comme suit :</span><span class="sxs-lookup"><span data-stu-id="87149-133">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="87149-134">Si une stratégie d’autorisation contient plusieurs exigences d’autorisation, toutes les spécifications doivent réussir pour que l’évaluation de la stratégie aboutisse.</span><span class="sxs-lookup"><span data-stu-id="87149-134">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="87149-135">En d’autres termes, plusieurs exigences d’autorisation ajoutées à une même stratégie d’autorisation sont traitées sur une base **et** .</span><span class="sxs-lookup"><span data-stu-id="87149-135">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="87149-136">Une exigence n’a pas besoin d’avoir des données ou des propriétés.</span><span class="sxs-lookup"><span data-stu-id="87149-136">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="87149-137">Gestionnaires d’autorisations</span><span class="sxs-lookup"><span data-stu-id="87149-137">Authorization handlers</span></span>

<span data-ttu-id="87149-138">Un gestionnaire d’autorisations est responsable de l’évaluation des propriétés d’une spécification.</span><span class="sxs-lookup"><span data-stu-id="87149-138">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="87149-139">Le gestionnaire d’autorisations évalue les spécifications par rapport à un [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) fourni pour déterminer si l’accès est autorisé.</span><span class="sxs-lookup"><span data-stu-id="87149-139">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="87149-140">Une spécification peut avoir [plusieurs gestionnaires](#security-authorization-policies-based-multiple-handlers).</span><span class="sxs-lookup"><span data-stu-id="87149-140">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="87149-141">Un gestionnaire peut hériter de [AuthorizationHandler \<TRequirement> ](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), où `TRequirement` est la spécification à gérer.</span><span class="sxs-lookup"><span data-stu-id="87149-141">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="87149-142">Un gestionnaire peut également implémenter [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) pour gérer plusieurs types d’exigences.</span><span class="sxs-lookup"><span data-stu-id="87149-142">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="87149-143">Utiliser un gestionnaire pour une spécification</span><span class="sxs-lookup"><span data-stu-id="87149-143">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="87149-144">Voici un exemple de relation un-à-un dans laquelle un gestionnaire d’âge minimal utilise une seule exigence :</span><span class="sxs-lookup"><span data-stu-id="87149-144">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="87149-145">Le code précédent détermine si le principal de l’utilisateur actuel possède une revendication de date de naissance qui a été émise par un émetteur connu et approuvé.</span><span class="sxs-lookup"><span data-stu-id="87149-145">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="87149-146">L’autorisation ne peut pas se produire lorsque la revendication est manquante, auquel cas une tâche terminée est retournée.</span><span class="sxs-lookup"><span data-stu-id="87149-146">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="87149-147">Lorsqu’une revendication est présente, l’âge de l’utilisateur est calculé.</span><span class="sxs-lookup"><span data-stu-id="87149-147">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="87149-148">Si l’utilisateur atteint l’ancienneté minimale définie par la spécification, l’autorisation est considérée comme réussie.</span><span class="sxs-lookup"><span data-stu-id="87149-148">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="87149-149">Lorsque l’autorisation est réussie, `context.Succeed` est appelée avec la spécification satisfaite comme paramètre unique.</span><span class="sxs-lookup"><span data-stu-id="87149-149">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="87149-150">Utiliser un gestionnaire pour plusieurs spécifications</span><span class="sxs-lookup"><span data-stu-id="87149-150">Use a handler for multiple requirements</span></span>

<span data-ttu-id="87149-151">Voici un exemple de relation un-à-plusieurs dans laquelle un gestionnaire d’autorisations peut gérer trois types différents d’exigences :</span><span class="sxs-lookup"><span data-stu-id="87149-151">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="87149-152">Le code précédent parcourt [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements) &mdash; une propriété contenant des exigences non marquées comme ayant réussi.</span><span class="sxs-lookup"><span data-stu-id="87149-152">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="87149-153">Pour une `ReadPermission` spécification, l’utilisateur doit être un propriétaire ou un sponsor pour accéder à la ressource demandée.</span><span class="sxs-lookup"><span data-stu-id="87149-153">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="87149-154">Dans le cas d’une `EditPermission` `DeletePermission` exigence ou, il doit être propriétaire de l’accès à la ressource demandée.</span><span class="sxs-lookup"><span data-stu-id="87149-154">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="87149-155">Inscription du gestionnaire</span><span class="sxs-lookup"><span data-stu-id="87149-155">Handler registration</span></span>

<span data-ttu-id="87149-156">Les gestionnaires sont inscrits dans la collection de services pendant la configuration.</span><span class="sxs-lookup"><span data-stu-id="87149-156">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="87149-157">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="87149-157">For example:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53-55, 58)]

<span data-ttu-id="87149-158">Le code précédent s’inscrit `MinimumAgeHandler` en tant que singleton en appelant `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();` .</span><span class="sxs-lookup"><span data-stu-id="87149-158">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="87149-159">Les gestionnaires peuvent être inscrits à l’aide de l’une des [durées de vie de service](xref:fundamentals/dependency-injection#service-lifetimes)intégrées.</span><span class="sxs-lookup"><span data-stu-id="87149-159">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="87149-160">Que doit retourner un gestionnaire ?</span><span class="sxs-lookup"><span data-stu-id="87149-160">What should a handler return?</span></span>

<span data-ttu-id="87149-161">Notez que la `Handle` méthode dans l' [exemple de gestionnaire](#security-authorization-handler-example) ne retourne aucune valeur.</span><span class="sxs-lookup"><span data-stu-id="87149-161">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="87149-162">Comment l’état de la réussite ou de l’échec est-il indiqué ?</span><span class="sxs-lookup"><span data-stu-id="87149-162">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="87149-163">Un gestionnaire indique la réussite en appelant `context.Succeed(IAuthorizationRequirement requirement)` , en passant la spécification qui a été validée avec succès.</span><span class="sxs-lookup"><span data-stu-id="87149-163">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="87149-164">Un gestionnaire n’A pas besoin de gérer les défaillances en général, car d’autres gestionnaires pour la même spécification peuvent être exécutés correctement.</span><span class="sxs-lookup"><span data-stu-id="87149-164">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="87149-165">Pour garantir la défaillance, même si d’autres gestionnaires de spécifications sont correctement exécutés, appelez `context.Fail` .</span><span class="sxs-lookup"><span data-stu-id="87149-165">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="87149-166">Si un gestionnaire appelle `context.Succeed` ou `context.Fail` , tous les autres gestionnaires sont toujours appelés.</span><span class="sxs-lookup"><span data-stu-id="87149-166">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="87149-167">Cela permet aux exigences de produire des effets secondaires, tels que la journalisation, qui se produit même si un autre gestionnaire a validé ou échoué avec succès.</span><span class="sxs-lookup"><span data-stu-id="87149-167">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="87149-168">Quand la valeur `false` est affectée à, la propriété [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) (disponible dans ASP.net Core 1,1 et versions ultérieures) réduit les courts-circuits de l’exécution des gestionnaires lorsque `context.Fail` est appelé.</span><span class="sxs-lookup"><span data-stu-id="87149-168">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="87149-169">`InvokeHandlersAfterFailure`la valeur par défaut `true` est, auquel cas tous les gestionnaires sont appelés.</span><span class="sxs-lookup"><span data-stu-id="87149-169">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="87149-170">Les gestionnaires d’autorisations sont appelés même si l’authentification échoue.</span><span class="sxs-lookup"><span data-stu-id="87149-170">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="87149-171">Pourquoi dois-je utiliser plusieurs gestionnaires pour une spécification ?</span><span class="sxs-lookup"><span data-stu-id="87149-171">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="87149-172">Dans les cas où vous souhaitez que l’évaluation soit sur une base **ou** , implémentez plusieurs gestionnaires pour une seule exigence.</span><span class="sxs-lookup"><span data-stu-id="87149-172">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="87149-173">Par exemple, Microsoft possède des portes qui s’ouvrent uniquement avec des cartes clés.</span><span class="sxs-lookup"><span data-stu-id="87149-173">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="87149-174">Si vous laissez votre carte clé chez vous, la réceptionniste imprime un autocollant temporaire et ouvre la porte pour vous.</span><span class="sxs-lookup"><span data-stu-id="87149-174">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="87149-175">Dans ce scénario, vous auriez besoin d’une seule exigence, *BuildingEntry*, mais plusieurs gestionnaires, chacun examinant une seule exigence.</span><span class="sxs-lookup"><span data-stu-id="87149-175">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="87149-176">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="87149-176">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="87149-177">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="87149-177">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="87149-178">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="87149-178">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="87149-179">Vérifiez que les deux gestionnaires sont [inscrits](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span><span class="sxs-lookup"><span data-stu-id="87149-179">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="87149-180">Si l’un des gestionnaires aboutit lorsqu’une stratégie évalue le `BuildingEntryRequirement` , l’évaluation de la stratégie aboutit.</span><span class="sxs-lookup"><span data-stu-id="87149-180">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="use-a-func-to-fulfill-a-policy"></a><span data-ttu-id="87149-181">Utiliser un Func pour accomplir une stratégie</span><span class="sxs-lookup"><span data-stu-id="87149-181">Use a func to fulfill a policy</span></span>

<span data-ttu-id="87149-182">Il peut arriver que la réalisation d’une stratégie soit simple à exprimer dans le code.</span><span class="sxs-lookup"><span data-stu-id="87149-182">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="87149-183">Il est possible de fournir un `Func<AuthorizationHandlerContext, bool>` lors de la configuration de votre stratégie avec le `RequireAssertion` Générateur de stratégie.</span><span class="sxs-lookup"><span data-stu-id="87149-183">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="87149-184">Par exemple, la précédente `BadgeEntryHandler` peut être réécrite comme suit :</span><span class="sxs-lookup"><span data-stu-id="87149-184">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=42-43,47-53)]

## <a name="access-mvc-request-context-in-handlers"></a><span data-ttu-id="87149-185">Accéder au contexte de requête MVC dans les gestionnaires</span><span class="sxs-lookup"><span data-stu-id="87149-185">Access MVC request context in handlers</span></span>

<span data-ttu-id="87149-186">La `HandleRequirementAsync` méthode que vous implémentez dans un gestionnaire d’autorisations a deux paramètres : un `AuthorizationHandlerContext` et le `TRequirement` que vous gérez.</span><span class="sxs-lookup"><span data-stu-id="87149-186">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="87149-187">Les infrastructures telles que MVC ou SignalR sont libres d’ajouter n’importe quel objet à la `Resource` propriété sur le `AuthorizationHandlerContext` pour passer des informations supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="87149-187">Frameworks such as MVC or SignalR are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="87149-188">Lorsque vous utilisez le routage de point de terminaison, l’autorisation est généralement gérée par l’intergiciel (middleware) d’autorisation.</span><span class="sxs-lookup"><span data-stu-id="87149-188">When using endpoint routing, authorization is typically handled by the Authorization Middleware.</span></span> <span data-ttu-id="87149-189">Dans ce cas, la `Resource` propriété est une instance de <xref:Microsoft.AspNetCore.Http.Endpoint> .</span><span class="sxs-lookup"><span data-stu-id="87149-189">In this case, the `Resource` property is an instance of <xref:Microsoft.AspNetCore.Http.Endpoint>.</span></span> <span data-ttu-id="87149-190">Le point de terminaison peut être utilisé pour sonder la ressource sous-jacente vers laquelle vous effectuez le routage.</span><span class="sxs-lookup"><span data-stu-id="87149-190">The endpoint can be used to probe the underlying resource to which you're routing.</span></span> <span data-ttu-id="87149-191">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="87149-191">For example:</span></span>

```csharp
if (context.Resource is Endpoint endpoint)
{
   var actionDescriptor = endpoint.Metadata.GetMetadata<ControllerActionDescriptor>();
   ...
}
```

<span data-ttu-id="87149-192">Le point de terminaison ne fournit pas l’accès à l’objet actuel `HttpContext` .</span><span class="sxs-lookup"><span data-stu-id="87149-192">The endpoint doesn't provide access to the current `HttpContext`.</span></span> <span data-ttu-id="87149-193">Lorsque vous utilisez le routage de point de terminaison, utilisez `IHttpContextAcessor` pour accéder à `HttpContext` l’intérieur d’un gestionnaire d’autorisations.</span><span class="sxs-lookup"><span data-stu-id="87149-193">When using endpoint routing, use `IHttpContextAcessor` to access `HttpContext` inside of an authorization handler.</span></span> <span data-ttu-id="87149-194">Pour plus d’informations, consultez [utiliser HttpContext à partir de composants personnalisés](xref:fundamentals/httpcontext#use-httpcontext-from-custom-components).</span><span class="sxs-lookup"><span data-stu-id="87149-194">For more information, see [Use HttpContext from custom components](xref:fundamentals/httpcontext#use-httpcontext-from-custom-components).</span></span>

<span data-ttu-id="87149-195">Avec le routage traditionnel, ou lorsque l’autorisation a lieu dans le cadre du filtre d’autorisation MVC, la valeur de `Resource` est une <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> instance.</span><span class="sxs-lookup"><span data-stu-id="87149-195">With traditional routing, or when authorization happens as part of MVC's authorization filter, the value of `Resource` is an <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> instance.</span></span> <span data-ttu-id="87149-196">Cette propriété fournit l’accès à `HttpContext` , `RouteData` et tout le reste fourni par MVC et les Razor pages.</span><span class="sxs-lookup"><span data-stu-id="87149-196">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="87149-197">L’utilisation de la `Resource` propriété est spécifique à l’infrastructure.</span><span class="sxs-lookup"><span data-stu-id="87149-197">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="87149-198">L’utilisation des informations de la `Resource` propriété limite vos stratégies d’autorisation à des infrastructures spécifiques.</span><span class="sxs-lookup"><span data-stu-id="87149-198">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="87149-199">Vous devez effectuer un cast de la `Resource` propriété à l’aide du `is` mot clé, puis confirmer que le cast a réussi pour s’assurer que votre code ne se bloque pas avec un lorsqu’il est `InvalidCastException` exécuté sur d’autres infrastructures :</span><span class="sxs-lookup"><span data-stu-id="87149-199">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

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

<span data-ttu-id="87149-200">En coulisses, l’autorisation basée [sur les rôles](xref:security/authorization/roles) et l' [autorisation basée sur les revendications](xref:security/authorization/claims) utilisent une spécification, un gestionnaire de spécification et une stratégie préconfigurée.</span><span class="sxs-lookup"><span data-stu-id="87149-200">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="87149-201">Ces blocs de construction prennent en charge l’expression des évaluations d’autorisation dans le code.</span><span class="sxs-lookup"><span data-stu-id="87149-201">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="87149-202">Le résultat est une structure d’autorisation plus riche, réutilisable et testable.</span><span class="sxs-lookup"><span data-stu-id="87149-202">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="87149-203">Une stratégie d’autorisation se compose d’une ou de plusieurs exigences.</span><span class="sxs-lookup"><span data-stu-id="87149-203">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="87149-204">Elle est inscrite dans le cadre de la configuration du service d’autorisation, dans la `Startup.ConfigureServices` méthode :</span><span class="sxs-lookup"><span data-stu-id="87149-204">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,66)]

<span data-ttu-id="87149-205">Dans l’exemple précédent, une stratégie « AtLeast21 » est créée.</span><span class="sxs-lookup"><span data-stu-id="87149-205">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="87149-206">Il a une exigence unique &mdash; qui correspond à une ancienneté minimale, qui est fournie en tant que paramètre à la spécification.</span><span class="sxs-lookup"><span data-stu-id="87149-206">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="87149-207">IAuthorizationService</span><span class="sxs-lookup"><span data-stu-id="87149-207">IAuthorizationService</span></span> 

<span data-ttu-id="87149-208">Le service principal qui détermine si l’autorisation est réussie est le <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> suivant :</span><span class="sxs-lookup"><span data-stu-id="87149-208">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

<span data-ttu-id="87149-209">Le code précédent met en évidence les deux méthodes de [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span><span class="sxs-lookup"><span data-stu-id="87149-209">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="87149-210"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>est un service de marqueur sans méthode, et le mécanisme de suivi de la réussite de l’autorisation.</span><span class="sxs-lookup"><span data-stu-id="87149-210"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="87149-211">Chaque <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> est responsable de la vérification de la satisfaction des exigences suivantes :</span><span class="sxs-lookup"><span data-stu-id="87149-211">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
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

<span data-ttu-id="87149-212">La <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> classe est ce que le gestionnaire utilise pour marquer si les spécifications ont été satisfaites :</span><span class="sxs-lookup"><span data-stu-id="87149-212">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="87149-213">Le code suivant illustre l’implémentation par défaut simplifiée (et annotée avec des commentaires) du service d’autorisation :</span><span class="sxs-lookup"><span data-stu-id="87149-213">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

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

<span data-ttu-id="87149-214">Le code suivant illustre un exemple typique `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="87149-214">The following code shows a typical `ConfigureServices`:</span></span>

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

<span data-ttu-id="87149-215">Utilisez <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> ou `[Authorize(Policy = "Something")]` pour l’autorisation.</span><span class="sxs-lookup"><span data-stu-id="87149-215">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="apply-policies-to-mvc-controllers"></a><span data-ttu-id="87149-216">Appliquer des stratégies à des contrôleurs MVC</span><span class="sxs-lookup"><span data-stu-id="87149-216">Apply policies to MVC controllers</span></span>

<span data-ttu-id="87149-217">Si vous utilisez des Razor pages, consultez [appliquer des stratégies à des Razor pages](#apply-policies-to-razor-pages) de ce document.</span><span class="sxs-lookup"><span data-stu-id="87149-217">If you're using Razor Pages, see [Apply policies to Razor Pages](#apply-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="87149-218">Les stratégies sont appliquées aux contrôleurs à l’aide de l' `[Authorize]` attribut avec le nom de la stratégie.</span><span class="sxs-lookup"><span data-stu-id="87149-218">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="87149-219">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="87149-219">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="apply-policies-to-razor-pages"></a><span data-ttu-id="87149-220">Appliquer des stratégies à des Razor pages</span><span class="sxs-lookup"><span data-stu-id="87149-220">Apply policies to Razor Pages</span></span>

<span data-ttu-id="87149-221">Les stratégies sont appliquées aux pages à l' Razor aide de l' `[Authorize]` attribut avec le nom de la stratégie.</span><span class="sxs-lookup"><span data-stu-id="87149-221">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="87149-222">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="87149-222">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="87149-223">Les stratégies peuvent également être appliquées aux Razor pages à l’aide d’une [Convention d’autorisation](xref:security/authorization/razor-pages-authorization).</span><span class="sxs-lookup"><span data-stu-id="87149-223">Policies can also be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="87149-224">Configuration requise</span><span class="sxs-lookup"><span data-stu-id="87149-224">Requirements</span></span>

<span data-ttu-id="87149-225">Une spécification d’autorisation est une collection de paramètres de données qu’une stratégie peut utiliser pour évaluer le principal d’utilisateur actuel.</span><span class="sxs-lookup"><span data-stu-id="87149-225">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="87149-226">Dans notre stratégie « AtLeast21 », l’exigence est un paramètre unique de &mdash; l’âge minimal.</span><span class="sxs-lookup"><span data-stu-id="87149-226">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="87149-227">Une spécification implémente [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), qui est une interface de marqueur vide.</span><span class="sxs-lookup"><span data-stu-id="87149-227">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="87149-228">Une exigence d’ancienneté minimale paramétrable peut être implémentée comme suit :</span><span class="sxs-lookup"><span data-stu-id="87149-228">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="87149-229">Si une stratégie d’autorisation contient plusieurs exigences d’autorisation, toutes les spécifications doivent réussir pour que l’évaluation de la stratégie aboutisse.</span><span class="sxs-lookup"><span data-stu-id="87149-229">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="87149-230">En d’autres termes, plusieurs exigences d’autorisation ajoutées à une même stratégie d’autorisation sont traitées sur une base **et** .</span><span class="sxs-lookup"><span data-stu-id="87149-230">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="87149-231">Une exigence n’a pas besoin d’avoir des données ou des propriétés.</span><span class="sxs-lookup"><span data-stu-id="87149-231">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="87149-232">Gestionnaires d’autorisations</span><span class="sxs-lookup"><span data-stu-id="87149-232">Authorization handlers</span></span>

<span data-ttu-id="87149-233">Un gestionnaire d’autorisations est responsable de l’évaluation des propriétés d’une spécification.</span><span class="sxs-lookup"><span data-stu-id="87149-233">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="87149-234">Le gestionnaire d’autorisations évalue les spécifications par rapport à un [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) fourni pour déterminer si l’accès est autorisé.</span><span class="sxs-lookup"><span data-stu-id="87149-234">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="87149-235">Une spécification peut avoir [plusieurs gestionnaires](#security-authorization-policies-based-multiple-handlers).</span><span class="sxs-lookup"><span data-stu-id="87149-235">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="87149-236">Un gestionnaire peut hériter de [AuthorizationHandler \<TRequirement> ](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), où `TRequirement` est la spécification à gérer.</span><span class="sxs-lookup"><span data-stu-id="87149-236">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="87149-237">Un gestionnaire peut également implémenter [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) pour gérer plusieurs types d’exigences.</span><span class="sxs-lookup"><span data-stu-id="87149-237">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="87149-238">Utiliser un gestionnaire pour une spécification</span><span class="sxs-lookup"><span data-stu-id="87149-238">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="87149-239">Voici un exemple de relation un-à-un dans laquelle un gestionnaire d’âge minimal utilise une seule exigence :</span><span class="sxs-lookup"><span data-stu-id="87149-239">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="87149-240">Le code précédent détermine si le principal de l’utilisateur actuel possède une revendication de date de naissance qui a été émise par un émetteur connu et approuvé.</span><span class="sxs-lookup"><span data-stu-id="87149-240">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="87149-241">L’autorisation ne peut pas se produire lorsque la revendication est manquante, auquel cas une tâche terminée est retournée.</span><span class="sxs-lookup"><span data-stu-id="87149-241">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="87149-242">Lorsqu’une revendication est présente, l’âge de l’utilisateur est calculé.</span><span class="sxs-lookup"><span data-stu-id="87149-242">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="87149-243">Si l’utilisateur atteint l’ancienneté minimale définie par la spécification, l’autorisation est considérée comme réussie.</span><span class="sxs-lookup"><span data-stu-id="87149-243">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="87149-244">Lorsque l’autorisation est réussie, `context.Succeed` est appelée avec la spécification satisfaite comme paramètre unique.</span><span class="sxs-lookup"><span data-stu-id="87149-244">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="87149-245">Utiliser un gestionnaire pour plusieurs spécifications</span><span class="sxs-lookup"><span data-stu-id="87149-245">Use a handler for multiple requirements</span></span>

<span data-ttu-id="87149-246">Voici un exemple de relation un-à-plusieurs dans laquelle un gestionnaire d’autorisations peut gérer trois types différents d’exigences :</span><span class="sxs-lookup"><span data-stu-id="87149-246">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="87149-247">Le code précédent parcourt [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements) &mdash; une propriété contenant des exigences non marquées comme ayant réussi.</span><span class="sxs-lookup"><span data-stu-id="87149-247">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="87149-248">Pour une `ReadPermission` spécification, l’utilisateur doit être un propriétaire ou un sponsor pour accéder à la ressource demandée.</span><span class="sxs-lookup"><span data-stu-id="87149-248">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="87149-249">Dans le cas d’une `EditPermission` `DeletePermission` exigence ou, il doit être propriétaire de l’accès à la ressource demandée.</span><span class="sxs-lookup"><span data-stu-id="87149-249">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="87149-250">Inscription du gestionnaire</span><span class="sxs-lookup"><span data-stu-id="87149-250">Handler registration</span></span>

<span data-ttu-id="87149-251">Les gestionnaires sont inscrits dans la collection de services pendant la configuration.</span><span class="sxs-lookup"><span data-stu-id="87149-251">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="87149-252">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="87149-252">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,62-63,66)]

<span data-ttu-id="87149-253">Le code précédent s’inscrit `MinimumAgeHandler` en tant que singleton en appelant `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();` .</span><span class="sxs-lookup"><span data-stu-id="87149-253">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="87149-254">Les gestionnaires peuvent être inscrits à l’aide de l’une des [durées de vie de service](xref:fundamentals/dependency-injection#service-lifetimes)intégrées.</span><span class="sxs-lookup"><span data-stu-id="87149-254">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="87149-255">Que doit retourner un gestionnaire ?</span><span class="sxs-lookup"><span data-stu-id="87149-255">What should a handler return?</span></span>

<span data-ttu-id="87149-256">Notez que la `Handle` méthode dans l' [exemple de gestionnaire](#security-authorization-handler-example) ne retourne aucune valeur.</span><span class="sxs-lookup"><span data-stu-id="87149-256">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="87149-257">Comment l’état de la réussite ou de l’échec est-il indiqué ?</span><span class="sxs-lookup"><span data-stu-id="87149-257">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="87149-258">Un gestionnaire indique la réussite en appelant `context.Succeed(IAuthorizationRequirement requirement)` , en passant la spécification qui a été validée avec succès.</span><span class="sxs-lookup"><span data-stu-id="87149-258">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="87149-259">Un gestionnaire n’A pas besoin de gérer les défaillances en général, car d’autres gestionnaires pour la même spécification peuvent être exécutés correctement.</span><span class="sxs-lookup"><span data-stu-id="87149-259">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="87149-260">Pour garantir la défaillance, même si d’autres gestionnaires de spécifications sont correctement exécutés, appelez `context.Fail` .</span><span class="sxs-lookup"><span data-stu-id="87149-260">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="87149-261">Si un gestionnaire appelle `context.Succeed` ou `context.Fail` , tous les autres gestionnaires sont toujours appelés.</span><span class="sxs-lookup"><span data-stu-id="87149-261">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="87149-262">Cela permet aux exigences de produire des effets secondaires, tels que la journalisation, qui se produit même si un autre gestionnaire a validé ou échoué avec succès.</span><span class="sxs-lookup"><span data-stu-id="87149-262">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="87149-263">Quand la valeur `false` est affectée à, la propriété [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) (disponible dans ASP.net Core 1,1 et versions ultérieures) réduit les courts-circuits de l’exécution des gestionnaires lorsque `context.Fail` est appelé.</span><span class="sxs-lookup"><span data-stu-id="87149-263">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="87149-264">`InvokeHandlersAfterFailure`la valeur par défaut `true` est, auquel cas tous les gestionnaires sont appelés.</span><span class="sxs-lookup"><span data-stu-id="87149-264">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="87149-265">Les gestionnaires d’autorisations sont appelés même si l’authentification échoue.</span><span class="sxs-lookup"><span data-stu-id="87149-265">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="87149-266">Pourquoi dois-je utiliser plusieurs gestionnaires pour une spécification ?</span><span class="sxs-lookup"><span data-stu-id="87149-266">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="87149-267">Dans les cas où vous souhaitez que l’évaluation soit sur une base **ou** , implémentez plusieurs gestionnaires pour une seule exigence.</span><span class="sxs-lookup"><span data-stu-id="87149-267">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="87149-268">Par exemple, Microsoft possède des portes qui s’ouvrent uniquement avec des cartes clés.</span><span class="sxs-lookup"><span data-stu-id="87149-268">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="87149-269">Si vous laissez votre carte clé chez vous, la réceptionniste imprime un autocollant temporaire et ouvre la porte pour vous.</span><span class="sxs-lookup"><span data-stu-id="87149-269">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="87149-270">Dans ce scénario, vous auriez besoin d’une seule exigence, *BuildingEntry*, mais plusieurs gestionnaires, chacun examinant une seule exigence.</span><span class="sxs-lookup"><span data-stu-id="87149-270">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="87149-271">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="87149-271">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="87149-272">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="87149-272">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="87149-273">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="87149-273">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="87149-274">Vérifiez que les deux gestionnaires sont [inscrits](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span><span class="sxs-lookup"><span data-stu-id="87149-274">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="87149-275">Si l’un des gestionnaires aboutit lorsqu’une stratégie évalue le `BuildingEntryRequirement` , l’évaluation de la stratégie aboutit.</span><span class="sxs-lookup"><span data-stu-id="87149-275">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="use-a-func-to-fulfill-a-policy"></a><span data-ttu-id="87149-276">Utiliser un Func pour accomplir une stratégie</span><span class="sxs-lookup"><span data-stu-id="87149-276">Use a func to fulfill a policy</span></span>

<span data-ttu-id="87149-277">Il peut arriver que la réalisation d’une stratégie soit simple à exprimer dans le code.</span><span class="sxs-lookup"><span data-stu-id="87149-277">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="87149-278">Il est possible de fournir un `Func<AuthorizationHandlerContext, bool>` lors de la configuration de votre stratégie avec le `RequireAssertion` Générateur de stratégie.</span><span class="sxs-lookup"><span data-stu-id="87149-278">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="87149-279">Par exemple, la précédente `BadgeEntryHandler` peut être réécrite comme suit :</span><span class="sxs-lookup"><span data-stu-id="87149-279">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=50-51,55-61)]

## <a name="access-mvc-request-context-in-handlers"></a><span data-ttu-id="87149-280">Accéder au contexte de requête MVC dans les gestionnaires</span><span class="sxs-lookup"><span data-stu-id="87149-280">Access MVC request context in handlers</span></span>

<span data-ttu-id="87149-281">La `HandleRequirementAsync` méthode que vous implémentez dans un gestionnaire d’autorisations a deux paramètres : un `AuthorizationHandlerContext` et le `TRequirement` que vous gérez.</span><span class="sxs-lookup"><span data-stu-id="87149-281">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="87149-282">Les infrastructures telles que MVC ou SignalR sont libres d’ajouter n’importe quel objet à la `Resource` propriété sur le `AuthorizationHandlerContext` pour passer des informations supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="87149-282">Frameworks such as MVC or SignalR are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="87149-283">Par exemple, MVC passe une instance de [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) dans la `Resource` propriété.</span><span class="sxs-lookup"><span data-stu-id="87149-283">For example, MVC passes an instance of [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) in the `Resource` property.</span></span> <span data-ttu-id="87149-284">Cette propriété fournit l’accès à `HttpContext` , `RouteData` et tout le reste fourni par MVC et les Razor pages.</span><span class="sxs-lookup"><span data-stu-id="87149-284">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="87149-285">L’utilisation de la `Resource` propriété est spécifique à l’infrastructure.</span><span class="sxs-lookup"><span data-stu-id="87149-285">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="87149-286">L’utilisation des informations de la `Resource` propriété limite vos stratégies d’autorisation à des infrastructures spécifiques.</span><span class="sxs-lookup"><span data-stu-id="87149-286">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="87149-287">Vous devez effectuer un cast de la `Resource` propriété à l’aide du `is` mot clé, puis confirmer que le cast a réussi pour s’assurer que votre code ne se bloque pas avec un lorsqu’il est `InvalidCastException` exécuté sur d’autres infrastructures :</span><span class="sxs-lookup"><span data-stu-id="87149-287">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end
