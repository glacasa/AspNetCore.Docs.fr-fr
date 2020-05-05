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
ms.openlocfilehash: 3b6fcef91355bf22e5aa185652d9489a44998db0
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777499"
---
# <a name="policy-based-authorization-in-aspnet-core"></a><span data-ttu-id="8df28-103">Autorisation basée sur la stratégie dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8df28-103">Policy-based authorization in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8df28-104">En coulisses, l’autorisation basée [sur les rôles](xref:security/authorization/roles) et l' [autorisation basée sur les revendications](xref:security/authorization/claims) utilisent une spécification, un gestionnaire de spécification et une stratégie préconfigurée.</span><span class="sxs-lookup"><span data-stu-id="8df28-104">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="8df28-105">Ces blocs de construction prennent en charge l’expression des évaluations d’autorisation dans le code.</span><span class="sxs-lookup"><span data-stu-id="8df28-105">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="8df28-106">Le résultat est une structure d’autorisation plus riche, réutilisable et testable.</span><span class="sxs-lookup"><span data-stu-id="8df28-106">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="8df28-107">Une stratégie d’autorisation se compose d’une ou de plusieurs exigences.</span><span class="sxs-lookup"><span data-stu-id="8df28-107">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="8df28-108">Elle est inscrite dans le cadre de la configuration du service d' `Startup.ConfigureServices` autorisation, dans la méthode :</span><span class="sxs-lookup"><span data-stu-id="8df28-108">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53, 58)]

<span data-ttu-id="8df28-109">Dans l’exemple précédent, une stratégie « AtLeast21 » est créée.</span><span class="sxs-lookup"><span data-stu-id="8df28-109">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="8df28-110">Il a une exigence&mdash;unique qui correspond à une ancienneté minimale, qui est fournie en tant que paramètre à la spécification.</span><span class="sxs-lookup"><span data-stu-id="8df28-110">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="8df28-111">IAuthorizationService</span><span class="sxs-lookup"><span data-stu-id="8df28-111">IAuthorizationService</span></span> 

<span data-ttu-id="8df28-112">Le service principal qui détermine si l’autorisation est réussie <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>est le suivant :</span><span class="sxs-lookup"><span data-stu-id="8df28-112">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

<span data-ttu-id="8df28-113">Le code précédent met en évidence les deux méthodes de [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span><span class="sxs-lookup"><span data-stu-id="8df28-113">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="8df28-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>est un service de marqueur sans méthode, et le mécanisme de suivi de la réussite de l’autorisation.</span><span class="sxs-lookup"><span data-stu-id="8df28-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="8df28-115">Chaque <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> est responsable de la vérification de la satisfaction des exigences suivantes :</span><span class="sxs-lookup"><span data-stu-id="8df28-115">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
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

<span data-ttu-id="8df28-116">La <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> classe est ce que le gestionnaire utilise pour marquer si les spécifications ont été satisfaites :</span><span class="sxs-lookup"><span data-stu-id="8df28-116">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="8df28-117">Le code suivant illustre l’implémentation par défaut simplifiée (et annotée avec des commentaires) du service d’autorisation :</span><span class="sxs-lookup"><span data-stu-id="8df28-117">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

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

<span data-ttu-id="8df28-118">Le code suivant illustre un exemple `ConfigureServices`typique :</span><span class="sxs-lookup"><span data-stu-id="8df28-118">The following code shows a typical `ConfigureServices`:</span></span>

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

<span data-ttu-id="8df28-119">Utilisez <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> ou `[Authorize(Policy = "Something")]` pour l’autorisation.</span><span class="sxs-lookup"><span data-stu-id="8df28-119">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="applying-policies-to-mvc-controllers"></a><span data-ttu-id="8df28-120">Application de stratégies à des contrôleurs MVC</span><span class="sxs-lookup"><span data-stu-id="8df28-120">Applying policies to MVC controllers</span></span>

<span data-ttu-id="8df28-121">Si vous utilisez Razor des pages, consultez [application de stratégies Razor à des pages](#applying-policies-to-razor-pages) dans ce document.</span><span class="sxs-lookup"><span data-stu-id="8df28-121">If you're using Razor Pages, see [Applying policies to Razor Pages](#applying-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="8df28-122">Les stratégies sont appliquées aux contrôleurs à `[Authorize]` l’aide de l’attribut avec le nom de la stratégie.</span><span class="sxs-lookup"><span data-stu-id="8df28-122">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="8df28-123">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="8df28-123">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a><span data-ttu-id="8df28-124">Application de stratégies Razor à des pages</span><span class="sxs-lookup"><span data-stu-id="8df28-124">Applying policies to Razor Pages</span></span>

<span data-ttu-id="8df28-125">Les stratégies sont appliquées Razor aux pages à l' `[Authorize]` aide de l’attribut avec le nom de la stratégie.</span><span class="sxs-lookup"><span data-stu-id="8df28-125">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="8df28-126">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="8df28-126">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="8df28-127">Les stratégies peuvent également être appliquées Razor aux pages à l’aide d’une [Convention d’autorisation](xref:security/authorization/razor-pages-authorization).</span><span class="sxs-lookup"><span data-stu-id="8df28-127">Policies can also be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="8df28-128">Spécifications</span><span class="sxs-lookup"><span data-stu-id="8df28-128">Requirements</span></span>

<span data-ttu-id="8df28-129">Une spécification d’autorisation est une collection de paramètres de données qu’une stratégie peut utiliser pour évaluer le principal d’utilisateur actuel.</span><span class="sxs-lookup"><span data-stu-id="8df28-129">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="8df28-130">Dans notre stratégie « AtLeast21 », l’exigence est un paramètre&mdash;unique de l’âge minimal.</span><span class="sxs-lookup"><span data-stu-id="8df28-130">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="8df28-131">Une spécification implémente [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), qui est une interface de marqueur vide.</span><span class="sxs-lookup"><span data-stu-id="8df28-131">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="8df28-132">Une exigence d’ancienneté minimale paramétrable peut être implémentée comme suit :</span><span class="sxs-lookup"><span data-stu-id="8df28-132">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="8df28-133">Si une stratégie d’autorisation contient plusieurs exigences d’autorisation, toutes les spécifications doivent réussir pour que l’évaluation de la stratégie aboutisse.</span><span class="sxs-lookup"><span data-stu-id="8df28-133">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="8df28-134">En d’autres termes, plusieurs exigences d’autorisation ajoutées à une même stratégie d’autorisation sont traitées sur une base **et** .</span><span class="sxs-lookup"><span data-stu-id="8df28-134">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="8df28-135">Une exigence n’a pas besoin d’avoir des données ou des propriétés.</span><span class="sxs-lookup"><span data-stu-id="8df28-135">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="8df28-136">Gestionnaires d’autorisations</span><span class="sxs-lookup"><span data-stu-id="8df28-136">Authorization handlers</span></span>

<span data-ttu-id="8df28-137">Un gestionnaire d’autorisations est responsable de l’évaluation des propriétés d’une spécification.</span><span class="sxs-lookup"><span data-stu-id="8df28-137">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="8df28-138">Le gestionnaire d’autorisations évalue les spécifications par rapport à un [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) fourni pour déterminer si l’accès est autorisé.</span><span class="sxs-lookup"><span data-stu-id="8df28-138">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="8df28-139">Une spécification peut avoir [plusieurs gestionnaires](#security-authorization-policies-based-multiple-handlers).</span><span class="sxs-lookup"><span data-stu-id="8df28-139">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="8df28-140">Un gestionnaire peut hériter de [\<AuthorizationHandler TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), où `TRequirement` est la spécification à gérer.</span><span class="sxs-lookup"><span data-stu-id="8df28-140">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="8df28-141">Un gestionnaire peut également implémenter [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) pour gérer plusieurs types d’exigences.</span><span class="sxs-lookup"><span data-stu-id="8df28-141">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="8df28-142">Utiliser un gestionnaire pour une spécification</span><span class="sxs-lookup"><span data-stu-id="8df28-142">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="8df28-143">Voici un exemple de relation un-à-un dans laquelle un gestionnaire d’âge minimal utilise une seule exigence :</span><span class="sxs-lookup"><span data-stu-id="8df28-143">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="8df28-144">Le code précédent détermine si le principal de l’utilisateur actuel possède une revendication de date de naissance qui a été émise par un émetteur connu et approuvé.</span><span class="sxs-lookup"><span data-stu-id="8df28-144">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="8df28-145">L’autorisation ne peut pas se produire lorsque la revendication est manquante, auquel cas une tâche terminée est retournée.</span><span class="sxs-lookup"><span data-stu-id="8df28-145">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="8df28-146">Lorsqu’une revendication est présente, l’âge de l’utilisateur est calculé.</span><span class="sxs-lookup"><span data-stu-id="8df28-146">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="8df28-147">Si l’utilisateur atteint l’ancienneté minimale définie par la spécification, l’autorisation est considérée comme réussie.</span><span class="sxs-lookup"><span data-stu-id="8df28-147">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="8df28-148">Lorsque l’autorisation est réussie `context.Succeed` , est appelée avec la spécification satisfaite comme paramètre unique.</span><span class="sxs-lookup"><span data-stu-id="8df28-148">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="8df28-149">Utiliser un gestionnaire pour plusieurs spécifications</span><span class="sxs-lookup"><span data-stu-id="8df28-149">Use a handler for multiple requirements</span></span>

<span data-ttu-id="8df28-150">Voici un exemple de relation un-à-plusieurs dans laquelle un gestionnaire d’autorisations peut gérer trois types différents d’exigences :</span><span class="sxs-lookup"><span data-stu-id="8df28-150">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="8df28-151">Le code précédent parcourt [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;une propriété contenant des exigences non marquées comme ayant réussi.</span><span class="sxs-lookup"><span data-stu-id="8df28-151">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="8df28-152">Pour une `ReadPermission` spécification, l’utilisateur doit être un propriétaire ou un sponsor pour accéder à la ressource demandée.</span><span class="sxs-lookup"><span data-stu-id="8df28-152">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="8df28-153">Dans le cas d’une `EditPermission` exigence `DeletePermission` ou, il doit être propriétaire de l’accès à la ressource demandée.</span><span class="sxs-lookup"><span data-stu-id="8df28-153">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="8df28-154">Inscription du gestionnaire</span><span class="sxs-lookup"><span data-stu-id="8df28-154">Handler registration</span></span>

<span data-ttu-id="8df28-155">Les gestionnaires sont inscrits dans la collection de services pendant la configuration.</span><span class="sxs-lookup"><span data-stu-id="8df28-155">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="8df28-156">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="8df28-156">For example:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53-55, 58)]

<span data-ttu-id="8df28-157">Le code précédent s’inscrit `MinimumAgeHandler` en tant que singleton en appelant `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span><span class="sxs-lookup"><span data-stu-id="8df28-157">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="8df28-158">Les gestionnaires peuvent être inscrits à l’aide de l’une des [durées de vie de service](xref:fundamentals/dependency-injection#service-lifetimes)intégrées.</span><span class="sxs-lookup"><span data-stu-id="8df28-158">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="8df28-159">Que doit retourner un gestionnaire ?</span><span class="sxs-lookup"><span data-stu-id="8df28-159">What should a handler return?</span></span>

<span data-ttu-id="8df28-160">Notez que la `Handle` méthode dans l' [exemple de gestionnaire](#security-authorization-handler-example) ne retourne aucune valeur.</span><span class="sxs-lookup"><span data-stu-id="8df28-160">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="8df28-161">Comment l’état de la réussite ou de l’échec est-il indiqué ?</span><span class="sxs-lookup"><span data-stu-id="8df28-161">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="8df28-162">Un gestionnaire indique la réussite en `context.Succeed(IAuthorizationRequirement requirement)`appelant, en passant la spécification qui a été validée avec succès.</span><span class="sxs-lookup"><span data-stu-id="8df28-162">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="8df28-163">Un gestionnaire n’A pas besoin de gérer les défaillances en général, car d’autres gestionnaires pour la même spécification peuvent être exécutés correctement.</span><span class="sxs-lookup"><span data-stu-id="8df28-163">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="8df28-164">Pour garantir la défaillance, même si d’autres gestionnaires de spécifications sont `context.Fail`correctement exécutés, appelez.</span><span class="sxs-lookup"><span data-stu-id="8df28-164">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="8df28-165">Si un gestionnaire appelle `context.Succeed` ou `context.Fail`, tous les autres gestionnaires sont toujours appelés.</span><span class="sxs-lookup"><span data-stu-id="8df28-165">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="8df28-166">Cela permet aux exigences de produire des effets secondaires, tels que la journalisation, qui se produit même si un autre gestionnaire a validé ou échoué avec succès.</span><span class="sxs-lookup"><span data-stu-id="8df28-166">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="8df28-167">Quand la valeur `false`est affectée à, la propriété [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) (disponible dans ASP.net Core 1,1 et versions ultérieures) réduit les courts- `context.Fail` circuits de l’exécution des gestionnaires lorsque est appelé.</span><span class="sxs-lookup"><span data-stu-id="8df28-167">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="8df28-168">`InvokeHandlersAfterFailure`la valeur par `true`défaut est, auquel cas tous les gestionnaires sont appelés.</span><span class="sxs-lookup"><span data-stu-id="8df28-168">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="8df28-169">Les gestionnaires d’autorisations sont appelés même si l’authentification échoue.</span><span class="sxs-lookup"><span data-stu-id="8df28-169">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="8df28-170">Pourquoi dois-je utiliser plusieurs gestionnaires pour une spécification ?</span><span class="sxs-lookup"><span data-stu-id="8df28-170">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="8df28-171">Dans les cas où vous souhaitez que l’évaluation soit sur une base **ou** , implémentez plusieurs gestionnaires pour une seule exigence.</span><span class="sxs-lookup"><span data-stu-id="8df28-171">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="8df28-172">Par exemple, Microsoft possède des portes qui s’ouvrent uniquement avec des cartes clés.</span><span class="sxs-lookup"><span data-stu-id="8df28-172">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="8df28-173">Si vous laissez votre carte clé chez vous, la réceptionniste imprime un autocollant temporaire et ouvre la porte pour vous.</span><span class="sxs-lookup"><span data-stu-id="8df28-173">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="8df28-174">Dans ce scénario, vous auriez besoin d’une seule exigence, *BuildingEntry*, mais plusieurs gestionnaires, chacun examinant une seule exigence.</span><span class="sxs-lookup"><span data-stu-id="8df28-174">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="8df28-175">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="8df28-175">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="8df28-176">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="8df28-176">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="8df28-177">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="8df28-177">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="8df28-178">Vérifiez que les deux gestionnaires sont [inscrits](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span><span class="sxs-lookup"><span data-stu-id="8df28-178">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="8df28-179">Si l’un des gestionnaires aboutit lorsqu’une stratégie évalue le `BuildingEntryRequirement`, l’évaluation de la stratégie aboutit.</span><span class="sxs-lookup"><span data-stu-id="8df28-179">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="using-a-func-to-fulfill-a-policy"></a><span data-ttu-id="8df28-180">Utilisation d’un Func pour accomplir une stratégie</span><span class="sxs-lookup"><span data-stu-id="8df28-180">Using a func to fulfill a policy</span></span>

<span data-ttu-id="8df28-181">Il peut arriver que la réalisation d’une stratégie soit simple à exprimer dans le code.</span><span class="sxs-lookup"><span data-stu-id="8df28-181">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="8df28-182">Il est possible de fournir un `Func<AuthorizationHandlerContext, bool>` lors de la configuration de votre stratégie `RequireAssertion` avec le générateur de stratégie.</span><span class="sxs-lookup"><span data-stu-id="8df28-182">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="8df28-183">Par exemple, la précédente `BadgeEntryHandler` peut être réécrite comme suit :</span><span class="sxs-lookup"><span data-stu-id="8df28-183">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=42-43,47-53)]

## <a name="accessing-mvc-request-context-in-handlers"></a><span data-ttu-id="8df28-184">Accès au contexte de requête MVC dans les gestionnaires</span><span class="sxs-lookup"><span data-stu-id="8df28-184">Accessing MVC request context in handlers</span></span>

<span data-ttu-id="8df28-185">La `HandleRequirementAsync` méthode que vous implémentez dans un gestionnaire d’autorisations a deux `AuthorizationHandlerContext` paramètres : `TRequirement` un et le que vous gérez.</span><span class="sxs-lookup"><span data-stu-id="8df28-185">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="8df28-186">Les infrastructures telles que MVC ou Jabbr sont libres d’ajouter n’importe quel objet `Resource` à la propriété `AuthorizationHandlerContext` sur le pour passer des informations supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="8df28-186">Frameworks such as MVC or Jabbr are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="8df28-187">Par exemple, MVC passe une instance de [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) dans la `Resource` propriété.</span><span class="sxs-lookup"><span data-stu-id="8df28-187">For example, MVC passes an instance of [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) in the `Resource` property.</span></span> <span data-ttu-id="8df28-188">Cette propriété fournit l’accès `HttpContext`à `RouteData`, et tout le reste fourni par MVC Razor et les pages.</span><span class="sxs-lookup"><span data-stu-id="8df28-188">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="8df28-189">L’utilisation de la `Resource` propriété est spécifique à l’infrastructure.</span><span class="sxs-lookup"><span data-stu-id="8df28-189">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="8df28-190">L’utilisation des informations `Resource` de la propriété limite vos stratégies d’autorisation à des infrastructures spécifiques.</span><span class="sxs-lookup"><span data-stu-id="8df28-190">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="8df28-191">Vous devez effectuer un `Resource` cast de la `is` propriété à l’aide du mot clé, puis confirmer que le cast a réussi pour s' `InvalidCastException` assurer que votre code ne se bloque pas avec un lorsqu’il est exécuté sur d’autres infrastructures :</span><span class="sxs-lookup"><span data-stu-id="8df28-191">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

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

<span data-ttu-id="8df28-192">En coulisses, l’autorisation basée [sur les rôles](xref:security/authorization/roles) et l' [autorisation basée sur les revendications](xref:security/authorization/claims) utilisent une spécification, un gestionnaire de spécification et une stratégie préconfigurée.</span><span class="sxs-lookup"><span data-stu-id="8df28-192">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="8df28-193">Ces blocs de construction prennent en charge l’expression des évaluations d’autorisation dans le code.</span><span class="sxs-lookup"><span data-stu-id="8df28-193">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="8df28-194">Le résultat est une structure d’autorisation plus riche, réutilisable et testable.</span><span class="sxs-lookup"><span data-stu-id="8df28-194">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="8df28-195">Une stratégie d’autorisation se compose d’une ou de plusieurs exigences.</span><span class="sxs-lookup"><span data-stu-id="8df28-195">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="8df28-196">Elle est inscrite dans le cadre de la configuration du service d' `Startup.ConfigureServices` autorisation, dans la méthode :</span><span class="sxs-lookup"><span data-stu-id="8df28-196">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,66)]

<span data-ttu-id="8df28-197">Dans l’exemple précédent, une stratégie « AtLeast21 » est créée.</span><span class="sxs-lookup"><span data-stu-id="8df28-197">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="8df28-198">Il a une exigence&mdash;unique qui correspond à une ancienneté minimale, qui est fournie en tant que paramètre à la spécification.</span><span class="sxs-lookup"><span data-stu-id="8df28-198">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="8df28-199">IAuthorizationService</span><span class="sxs-lookup"><span data-stu-id="8df28-199">IAuthorizationService</span></span> 

<span data-ttu-id="8df28-200">Le service principal qui détermine si l’autorisation est réussie <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>est le suivant :</span><span class="sxs-lookup"><span data-stu-id="8df28-200">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

<span data-ttu-id="8df28-201">Le code précédent met en évidence les deux méthodes de [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span><span class="sxs-lookup"><span data-stu-id="8df28-201">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="8df28-202"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>est un service de marqueur sans méthode, et le mécanisme de suivi de la réussite de l’autorisation.</span><span class="sxs-lookup"><span data-stu-id="8df28-202"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="8df28-203">Chaque <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> est responsable de la vérification de la satisfaction des exigences suivantes :</span><span class="sxs-lookup"><span data-stu-id="8df28-203">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
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

<span data-ttu-id="8df28-204">La <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> classe est ce que le gestionnaire utilise pour marquer si les spécifications ont été satisfaites :</span><span class="sxs-lookup"><span data-stu-id="8df28-204">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="8df28-205">Le code suivant illustre l’implémentation par défaut simplifiée (et annotée avec des commentaires) du service d’autorisation :</span><span class="sxs-lookup"><span data-stu-id="8df28-205">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

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

<span data-ttu-id="8df28-206">Le code suivant illustre un exemple `ConfigureServices`typique :</span><span class="sxs-lookup"><span data-stu-id="8df28-206">The following code shows a typical `ConfigureServices`:</span></span>

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

<span data-ttu-id="8df28-207">Utilisez <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> ou `[Authorize(Policy = "Something")]` pour l’autorisation.</span><span class="sxs-lookup"><span data-stu-id="8df28-207">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="applying-policies-to-mvc-controllers"></a><span data-ttu-id="8df28-208">Application de stratégies à des contrôleurs MVC</span><span class="sxs-lookup"><span data-stu-id="8df28-208">Applying policies to MVC controllers</span></span>

<span data-ttu-id="8df28-209">Si vous utilisez Razor des pages, consultez [application de stratégies Razor à des pages](#applying-policies-to-razor-pages) dans ce document.</span><span class="sxs-lookup"><span data-stu-id="8df28-209">If you're using Razor Pages, see [Applying policies to Razor Pages](#applying-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="8df28-210">Les stratégies sont appliquées aux contrôleurs à `[Authorize]` l’aide de l’attribut avec le nom de la stratégie.</span><span class="sxs-lookup"><span data-stu-id="8df28-210">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="8df28-211">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="8df28-211">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a><span data-ttu-id="8df28-212">Application de stratégies Razor à des pages</span><span class="sxs-lookup"><span data-stu-id="8df28-212">Applying policies to Razor Pages</span></span>

<span data-ttu-id="8df28-213">Les stratégies sont appliquées Razor aux pages à l' `[Authorize]` aide de l’attribut avec le nom de la stratégie.</span><span class="sxs-lookup"><span data-stu-id="8df28-213">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="8df28-214">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="8df28-214">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="8df28-215">Les stratégies peuvent également être appliquées Razor aux pages à l’aide d’une [Convention d’autorisation](xref:security/authorization/razor-pages-authorization).</span><span class="sxs-lookup"><span data-stu-id="8df28-215">Policies can also be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="8df28-216">Spécifications</span><span class="sxs-lookup"><span data-stu-id="8df28-216">Requirements</span></span>

<span data-ttu-id="8df28-217">Une spécification d’autorisation est une collection de paramètres de données qu’une stratégie peut utiliser pour évaluer le principal d’utilisateur actuel.</span><span class="sxs-lookup"><span data-stu-id="8df28-217">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="8df28-218">Dans notre stratégie « AtLeast21 », l’exigence est un paramètre&mdash;unique de l’âge minimal.</span><span class="sxs-lookup"><span data-stu-id="8df28-218">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="8df28-219">Une spécification implémente [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), qui est une interface de marqueur vide.</span><span class="sxs-lookup"><span data-stu-id="8df28-219">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="8df28-220">Une exigence d’ancienneté minimale paramétrable peut être implémentée comme suit :</span><span class="sxs-lookup"><span data-stu-id="8df28-220">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="8df28-221">Si une stratégie d’autorisation contient plusieurs exigences d’autorisation, toutes les spécifications doivent réussir pour que l’évaluation de la stratégie aboutisse.</span><span class="sxs-lookup"><span data-stu-id="8df28-221">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="8df28-222">En d’autres termes, plusieurs exigences d’autorisation ajoutées à une même stratégie d’autorisation sont traitées sur une base **et** .</span><span class="sxs-lookup"><span data-stu-id="8df28-222">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="8df28-223">Une exigence n’a pas besoin d’avoir des données ou des propriétés.</span><span class="sxs-lookup"><span data-stu-id="8df28-223">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="8df28-224">Gestionnaires d’autorisations</span><span class="sxs-lookup"><span data-stu-id="8df28-224">Authorization handlers</span></span>

<span data-ttu-id="8df28-225">Un gestionnaire d’autorisations est responsable de l’évaluation des propriétés d’une spécification.</span><span class="sxs-lookup"><span data-stu-id="8df28-225">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="8df28-226">Le gestionnaire d’autorisations évalue les spécifications par rapport à un [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) fourni pour déterminer si l’accès est autorisé.</span><span class="sxs-lookup"><span data-stu-id="8df28-226">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="8df28-227">Une spécification peut avoir [plusieurs gestionnaires](#security-authorization-policies-based-multiple-handlers).</span><span class="sxs-lookup"><span data-stu-id="8df28-227">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="8df28-228">Un gestionnaire peut hériter de [\<AuthorizationHandler TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), où `TRequirement` est la spécification à gérer.</span><span class="sxs-lookup"><span data-stu-id="8df28-228">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="8df28-229">Un gestionnaire peut également implémenter [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) pour gérer plusieurs types d’exigences.</span><span class="sxs-lookup"><span data-stu-id="8df28-229">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="8df28-230">Utiliser un gestionnaire pour une spécification</span><span class="sxs-lookup"><span data-stu-id="8df28-230">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="8df28-231">Voici un exemple de relation un-à-un dans laquelle un gestionnaire d’âge minimal utilise une seule exigence :</span><span class="sxs-lookup"><span data-stu-id="8df28-231">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="8df28-232">Le code précédent détermine si le principal de l’utilisateur actuel possède une revendication de date de naissance qui a été émise par un émetteur connu et approuvé.</span><span class="sxs-lookup"><span data-stu-id="8df28-232">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="8df28-233">L’autorisation ne peut pas se produire lorsque la revendication est manquante, auquel cas une tâche terminée est retournée.</span><span class="sxs-lookup"><span data-stu-id="8df28-233">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="8df28-234">Lorsqu’une revendication est présente, l’âge de l’utilisateur est calculé.</span><span class="sxs-lookup"><span data-stu-id="8df28-234">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="8df28-235">Si l’utilisateur atteint l’ancienneté minimale définie par la spécification, l’autorisation est considérée comme réussie.</span><span class="sxs-lookup"><span data-stu-id="8df28-235">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="8df28-236">Lorsque l’autorisation est réussie `context.Succeed` , est appelée avec la spécification satisfaite comme paramètre unique.</span><span class="sxs-lookup"><span data-stu-id="8df28-236">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="8df28-237">Utiliser un gestionnaire pour plusieurs spécifications</span><span class="sxs-lookup"><span data-stu-id="8df28-237">Use a handler for multiple requirements</span></span>

<span data-ttu-id="8df28-238">Voici un exemple de relation un-à-plusieurs dans laquelle un gestionnaire d’autorisations peut gérer trois types différents d’exigences :</span><span class="sxs-lookup"><span data-stu-id="8df28-238">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="8df28-239">Le code précédent parcourt [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;une propriété contenant des exigences non marquées comme ayant réussi.</span><span class="sxs-lookup"><span data-stu-id="8df28-239">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="8df28-240">Pour une `ReadPermission` spécification, l’utilisateur doit être un propriétaire ou un sponsor pour accéder à la ressource demandée.</span><span class="sxs-lookup"><span data-stu-id="8df28-240">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="8df28-241">Dans le cas d’une `EditPermission` exigence `DeletePermission` ou, il doit être propriétaire de l’accès à la ressource demandée.</span><span class="sxs-lookup"><span data-stu-id="8df28-241">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="8df28-242">Inscription du gestionnaire</span><span class="sxs-lookup"><span data-stu-id="8df28-242">Handler registration</span></span>

<span data-ttu-id="8df28-243">Les gestionnaires sont inscrits dans la collection de services pendant la configuration.</span><span class="sxs-lookup"><span data-stu-id="8df28-243">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="8df28-244">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="8df28-244">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,62-63,66)]

<span data-ttu-id="8df28-245">Le code précédent s’inscrit `MinimumAgeHandler` en tant que singleton en appelant `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span><span class="sxs-lookup"><span data-stu-id="8df28-245">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="8df28-246">Les gestionnaires peuvent être inscrits à l’aide de l’une des [durées de vie de service](xref:fundamentals/dependency-injection#service-lifetimes)intégrées.</span><span class="sxs-lookup"><span data-stu-id="8df28-246">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="8df28-247">Que doit retourner un gestionnaire ?</span><span class="sxs-lookup"><span data-stu-id="8df28-247">What should a handler return?</span></span>

<span data-ttu-id="8df28-248">Notez que la `Handle` méthode dans l' [exemple de gestionnaire](#security-authorization-handler-example) ne retourne aucune valeur.</span><span class="sxs-lookup"><span data-stu-id="8df28-248">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="8df28-249">Comment l’état de la réussite ou de l’échec est-il indiqué ?</span><span class="sxs-lookup"><span data-stu-id="8df28-249">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="8df28-250">Un gestionnaire indique la réussite en `context.Succeed(IAuthorizationRequirement requirement)`appelant, en passant la spécification qui a été validée avec succès.</span><span class="sxs-lookup"><span data-stu-id="8df28-250">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="8df28-251">Un gestionnaire n’A pas besoin de gérer les défaillances en général, car d’autres gestionnaires pour la même spécification peuvent être exécutés correctement.</span><span class="sxs-lookup"><span data-stu-id="8df28-251">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="8df28-252">Pour garantir la défaillance, même si d’autres gestionnaires de spécifications sont `context.Fail`correctement exécutés, appelez.</span><span class="sxs-lookup"><span data-stu-id="8df28-252">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="8df28-253">Si un gestionnaire appelle `context.Succeed` ou `context.Fail`, tous les autres gestionnaires sont toujours appelés.</span><span class="sxs-lookup"><span data-stu-id="8df28-253">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="8df28-254">Cela permet aux exigences de produire des effets secondaires, tels que la journalisation, qui se produit même si un autre gestionnaire a validé ou échoué avec succès.</span><span class="sxs-lookup"><span data-stu-id="8df28-254">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="8df28-255">Quand la valeur `false`est affectée à, la propriété [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) (disponible dans ASP.net Core 1,1 et versions ultérieures) réduit les courts- `context.Fail` circuits de l’exécution des gestionnaires lorsque est appelé.</span><span class="sxs-lookup"><span data-stu-id="8df28-255">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="8df28-256">`InvokeHandlersAfterFailure`la valeur par `true`défaut est, auquel cas tous les gestionnaires sont appelés.</span><span class="sxs-lookup"><span data-stu-id="8df28-256">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="8df28-257">Les gestionnaires d’autorisations sont appelés même si l’authentification échoue.</span><span class="sxs-lookup"><span data-stu-id="8df28-257">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="8df28-258">Pourquoi dois-je utiliser plusieurs gestionnaires pour une spécification ?</span><span class="sxs-lookup"><span data-stu-id="8df28-258">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="8df28-259">Dans les cas où vous souhaitez que l’évaluation soit sur une base **ou** , implémentez plusieurs gestionnaires pour une seule exigence.</span><span class="sxs-lookup"><span data-stu-id="8df28-259">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="8df28-260">Par exemple, Microsoft possède des portes qui s’ouvrent uniquement avec des cartes clés.</span><span class="sxs-lookup"><span data-stu-id="8df28-260">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="8df28-261">Si vous laissez votre carte clé chez vous, la réceptionniste imprime un autocollant temporaire et ouvre la porte pour vous.</span><span class="sxs-lookup"><span data-stu-id="8df28-261">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="8df28-262">Dans ce scénario, vous auriez besoin d’une seule exigence, *BuildingEntry*, mais plusieurs gestionnaires, chacun examinant une seule exigence.</span><span class="sxs-lookup"><span data-stu-id="8df28-262">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="8df28-263">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="8df28-263">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="8df28-264">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="8df28-264">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="8df28-265">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="8df28-265">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="8df28-266">Vérifiez que les deux gestionnaires sont [inscrits](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span><span class="sxs-lookup"><span data-stu-id="8df28-266">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="8df28-267">Si l’un des gestionnaires aboutit lorsqu’une stratégie évalue le `BuildingEntryRequirement`, l’évaluation de la stratégie aboutit.</span><span class="sxs-lookup"><span data-stu-id="8df28-267">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="using-a-func-to-fulfill-a-policy"></a><span data-ttu-id="8df28-268">Utilisation d’un Func pour accomplir une stratégie</span><span class="sxs-lookup"><span data-stu-id="8df28-268">Using a func to fulfill a policy</span></span>

<span data-ttu-id="8df28-269">Il peut arriver que la réalisation d’une stratégie soit simple à exprimer dans le code.</span><span class="sxs-lookup"><span data-stu-id="8df28-269">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="8df28-270">Il est possible de fournir un `Func<AuthorizationHandlerContext, bool>` lors de la configuration de votre stratégie `RequireAssertion` avec le générateur de stratégie.</span><span class="sxs-lookup"><span data-stu-id="8df28-270">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="8df28-271">Par exemple, la précédente `BadgeEntryHandler` peut être réécrite comme suit :</span><span class="sxs-lookup"><span data-stu-id="8df28-271">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=50-51,55-61)]

## <a name="accessing-mvc-request-context-in-handlers"></a><span data-ttu-id="8df28-272">Accès au contexte de requête MVC dans les gestionnaires</span><span class="sxs-lookup"><span data-stu-id="8df28-272">Accessing MVC request context in handlers</span></span>

<span data-ttu-id="8df28-273">La `HandleRequirementAsync` méthode que vous implémentez dans un gestionnaire d’autorisations a deux `AuthorizationHandlerContext` paramètres : `TRequirement` un et le que vous gérez.</span><span class="sxs-lookup"><span data-stu-id="8df28-273">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="8df28-274">Les infrastructures telles que MVC ou SignalR sont libres d’ajouter n’importe quel objet `Resource` à la propriété `AuthorizationHandlerContext` sur le pour passer des informations supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="8df28-274">Frameworks such as MVC or SignalR are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="8df28-275">Lorsque vous utilisez le routage de point de terminaison, l’autorisation est généralement gérée par l’intergiciel (middleware) d’autorisation.</span><span class="sxs-lookup"><span data-stu-id="8df28-275">When using endpoint routing, authorization is typically handled by the Authorization Middleware.</span></span> <span data-ttu-id="8df28-276">Dans ce cas, la `Resource` propriété est une instance de <xref:Microsoft.AspNetCore.Http.Endpoint>.</span><span class="sxs-lookup"><span data-stu-id="8df28-276">In this case, the `Resource` property is an instance of <xref:Microsoft.AspNetCore.Http.Endpoint>.</span></span> <span data-ttu-id="8df28-277">Le point de terminaison peut être utilisé pour sonder le sous-jacent de la ressource vers laquelle vous effectuez le routage.</span><span class="sxs-lookup"><span data-stu-id="8df28-277">The endpoint can be used to probe the underlying the resource to which you're routing.</span></span> <span data-ttu-id="8df28-278">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="8df28-278">For example:</span></span>

```csharp
if (context.Resource is Endpoint endpoint)
{
   var actionDescriptor = endpoint.Metadata.GetMetadata<ControllerActionDescriptor>();
   ...
}
```

<span data-ttu-id="8df28-279">Avec le routage traditionnel, ou lorsque l’autorisation a lieu dans le cadre du filtre d’autorisation MVC `Resource` , la <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> valeur de est une instance.</span><span class="sxs-lookup"><span data-stu-id="8df28-279">With traditional routing, or when authorization happens as part of MVC's authorization filter, the value of `Resource` is an <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> instance.</span></span> <span data-ttu-id="8df28-280">Cette propriété fournit l’accès `HttpContext`à `RouteData`, et tout le reste fourni par MVC Razor et les pages.</span><span class="sxs-lookup"><span data-stu-id="8df28-280">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="8df28-281">L’utilisation de la `Resource` propriété est spécifique à l’infrastructure.</span><span class="sxs-lookup"><span data-stu-id="8df28-281">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="8df28-282">L’utilisation des informations `Resource` de la propriété limite vos stratégies d’autorisation à des infrastructures spécifiques.</span><span class="sxs-lookup"><span data-stu-id="8df28-282">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="8df28-283">Vous devez effectuer un `Resource` cast de la `is` propriété à l’aide du mot clé, puis confirmer que le cast a réussi pour s' `InvalidCastException` assurer que votre code ne se bloque pas avec un lorsqu’il est exécuté sur d’autres infrastructures :</span><span class="sxs-lookup"><span data-stu-id="8df28-283">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end
