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
# <a name="policy-based-authorization-in-aspnet-core"></a>Autorisation fondée sur la politique dans ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Sous les couvertures, [l’autorisation basée sur](xref:security/authorization/roles) les rôles et [l’autorisation fondée sur les revendications](xref:security/authorization/claims) utilisent une exigence, un gestionnaire d’exigence et une stratégie préconfigurée. Ces éléments de base appuient l’expression des évaluations d’autorisation dans le code. Il en résulte une structure d’autorisation plus riche, réutilisable et testable.

Une politique d’autorisation se compose d’une ou plusieurs exigences. Il est enregistré dans le cadre de `Startup.ConfigureServices` la configuration du service d’autorisation, dans la méthode:

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53, 58)]

Dans l’exemple précédent, une politique « AtLeast21 » est créée. Il a une&mdash;seule exigence qui d’un âge minimum, qui est fourni comme un paramètre à l’exigence.

## <a name="iauthorizationservice"></a>IAuthorizationService (en) 

Le service principal qui détermine si <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>l’autorisation est réussie est :

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

Le code précédent met en évidence les deux méthodes de [l’IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).

<xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>est un service de marqueurs sans méthodes, et le mécanisme de suivi de l’autorisation est réussie.

Chacun <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> est responsable de vérifier si les exigences sont remplies :
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

La <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> classe est ce que le gestionnaire utilise pour marquer si les exigences ont été remplies :

```csharp
 context.Succeed(requirement)
```

Le code suivant montre la mise en œuvre par défaut simplifiée (et annotée avec commentaires) du service d’autorisation :

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

Le code suivant `ConfigureServices`montre un typique :

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

Utiliser <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> `[Authorize(Policy = "Something")]` ou pour l’autorisation.

## <a name="applying-policies-to-mvc-controllers"></a>Appliquer des politiques aux contrôleurs MVC

Si vous utilisez Razor Pages, consultez [les stratégies d’application des pages Razor](#applying-policies-to-razor-pages) dans ce document.

Les stratégies sont appliquées `[Authorize]` aux contrôleurs en utilisant l’attribut avec le nom de la stratégie. Par exemple :

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a>Appliquer des politiques aux pages Razor

Les stratégies sont appliquées `[Authorize]` aux Pages Razor en utilisant l’attribut avec le nom de la stratégie. Par exemple :

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

Les politiques peuvent également être appliquées aux Pages Razor en utilisant une [convention d’autorisation](xref:security/authorization/razor-pages-authorization).

## <a name="requirements"></a>Spécifications

Une exigence d’autorisation est une collecte de paramètres de données qu’une stratégie peut utiliser pour évaluer le principal utilisateur actuel. Dans notre politique « AtLeast21 », l’exigence est un paramètre&mdash;unique de l’âge minimum. Une exigence implémente [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), qui est une interface de marqueur vide. Une exigence d’âge minimum paramétrée pourrait être mise en œuvre comme suit :

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

Si une politique d’autorisation contient plusieurs exigences d’autorisation, toutes les exigences doivent être adoptées pour que l’évaluation de la politique réussisse. En d’autres termes, les exigences multiples d’autorisation ajoutées à une seule politique d’autorisation sont traitées sur une base **ET.**

> [!NOTE]
> Une exigence n’a pas besoin d’avoir des données ou des propriétés.

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a>Gestionnaires d’autorisation

Un gestionnaire d’autorisation est responsable de l’évaluation des propriétés d’une exigence. Le gestionnaire d’autorisation évalue les exigences par rapport à un [texte autorisationhandlercontexte](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) fourni pour déterminer si l’accès est autorisé.

Une exigence peut avoir [plusieurs gestionnaires](#security-authorization-policies-based-multiple-handlers). Un gestionnaire peut hériter [de AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), où `TRequirement` est l’exigence à traiter. Alternativement, un gestionnaire peut implémenter [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) pour gérer plus d’un type d’exigence.

### <a name="use-a-handler-for-one-requirement"></a>Utiliser un gestionnaire pour une exigence

<a name="security-authorization-handler-example"></a>

Voici un exemple d’une relation individuelles dans laquelle un gestionnaire d’âge minimum utilise une seule exigence :

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

Le code précédent détermine si le principal utilisateur actuel a une demande de date de naissance qui a été émise par un émetteur connu et digne de confiance. L’autorisation ne peut pas se produire lorsque la réclamation est manquante, auquel cas une tâche terminée est retournée. Lorsqu’une réclamation est présente, l’âge de l’utilisateur est calculé. Si l’utilisateur satisfait à l’âge minimum défini par l’exigence, l’autorisation est considérée comme réussie. Lorsque l’autorisation `context.Succeed` est acceptée, est invoquée avec l’exigence satisfaite comme seul paramètre.

### <a name="use-a-handler-for-multiple-requirements"></a>Utiliser un gestionnaire pour plusieurs exigences

Ce qui suit est un exemple d’une relation unique dans laquelle un gestionnaire d’autorisation peut traiter trois types d’exigences différentes :

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

Le code précédent traverse [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;une propriété contenant des exigences non marquées comme réussies. Pour `ReadPermission` une exigence, l’utilisateur doit être soit un propriétaire ou un commanditaire pour accéder à la ressource demandée. En cas d’exigence, `EditPermission` `DeletePermission` il doit être propriétaire de la ressource demandée.

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a>Enregistrement des gestionnaires

Les manutentionnaires sont inscrits à la collection de services pendant la configuration. Par exemple :

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53-55, 58)]

Le code précédent `MinimumAgeHandler` s’inscrit comme un `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`singleton en invoquant . Les manutentionnaires peuvent être enregistrés à l’aide de l’une des durées de [vie intégrées](xref:fundamentals/dependency-injection#service-lifetimes)du service.

## <a name="what-should-a-handler-return"></a>Qu’est-ce qu’un gestionnaire devrait revenir?

Notez `Handle` que la méthode dans l’exemple [du gestionnaire](#security-authorization-handler-example) ne rapporte aucune valeur. Comment un statut de succès ou d’échec est-il indiqué?

* Un gestionnaire indique `context.Succeed(IAuthorizationRequirement requirement)`le succès en appelant, en passant l’exigence qui a été validée avec succès.

* Un gestionnaire n’a pas besoin de gérer les défaillances en général, car d’autres gestionnaires pour la même exigence peuvent réussir.

* Pour garantir l’échec, même si `context.Fail`d’autres gestionnaires d’exigences réussissent, appelez .

Si un `context.Succeed` gestionnaire `context.Fail`appelle ou, tous les autres gestionnaires sont toujours appelés. Cela permet aux exigences de produire des effets secondaires, tels que l’enregistrement, qui a lieu même si un autre gestionnaire a validé ou échoué avec succès une exigence. Lorsqu’il `false`est configuré, la propriété [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) (disponible en ASP.NET Core 1.1 `context.Fail` et plus tard) court-circuite l’exécution des manutentionnaires lorsqu’il est appelé. `InvokeHandlersAfterFailure`par défaut `true`à , auquel cas tous les gestionnaires sont appelés.

> [!NOTE]
> Les préposés à l’autorisation sont appelés même si l’authentification échoue.

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a>Pourquoi voudrais-je plusieurs gestionnaires pour une exigence?

Dans les cas où vous souhaitez que l’évaluation soit sur une base **DE OU,** implémentez plusieurs gestionnaires pour une seule exigence. Par exemple, Microsoft a des portes qui ne s’ouvrent qu’avec des cartes clés. Si vous laissez votre carte-clé à la maison, la réceptionniste imprime un autocollant temporaire et vous ouvre la porte. Dans ce scénario, vous auriez une seule exigence, *BuildingEntry*, mais plusieurs gestionnaires, chacun examinant une seule exigence.

*BuildingEntryRequirement.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

*BadgeEntryHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

*TemporaryStickerHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

Assurez-vous que les deux gestionnaires sont [enregistrés](xref:security/authorization/policies#security-authorization-policies-based-handler-registration). Si l’un ou l’autre `BuildingEntryRequirement`gestionnaire réussit lorsqu’une politique évalue le, l’évaluation des politiques réussit.

## <a name="using-a-func-to-fulfill-a-policy"></a>Utilisation d’un func pour remplir une politique

Il peut y avoir des situations dans lesquelles l’exécution d’une politique est simple à exprimer dans le code. Il est possible de `Func<AuthorizationHandlerContext, bool>` fournir un lors de `RequireAssertion` la configuration de votre politique avec le constructeur de police.

Par exemple, `BadgeEntryHandler` le précédent pourrait être réécrit comme suit :

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=42-43,47-53)]

## <a name="accessing-mvc-request-context-in-handlers"></a>Accès au contexte de demande MVC chez les gestionnaires

La `HandleRequirementAsync` méthode que vous implémentez dans `AuthorizationHandlerContext` un `TRequirement` gestionnaire d’autorisation comporte deux paramètres : un et le que vous manipulez. Des cadres tels que MVC ou Jabbr sont `Resource` libres `AuthorizationHandlerContext` d’ajouter n’importe quel objet à la propriété sur les informations supplémentaires de passer.

Par exemple, MVC passe une instance de `Resource` [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) dans la propriété. Cette propriété donne `HttpContext` `RouteData`accès à , , et tout le reste fourni par MVC et Razor Pages.

L’utilisation `Resource` de la propriété est spécifique au cadre. L’utilisation `Resource` de l’information dans la propriété limite vos politiques d’autorisation à des cadres particuliers. Vous devez `Resource` lancer la `is` propriété à l’aide du mot clé, puis confirmer `InvalidCastException` le casting a réussi à s’assurer que votre code ne se bloque pas avec un moment exécuté sur d’autres cadres:

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

Sous les couvertures, [l’autorisation basée sur](xref:security/authorization/roles) les rôles et [l’autorisation fondée sur les revendications](xref:security/authorization/claims) utilisent une exigence, un gestionnaire d’exigence et une stratégie préconfigurée. Ces éléments de base appuient l’expression des évaluations d’autorisation dans le code. Il en résulte une structure d’autorisation plus riche, réutilisable et testable.

Une politique d’autorisation se compose d’une ou plusieurs exigences. Il est enregistré dans le cadre de `Startup.ConfigureServices` la configuration du service d’autorisation, dans la méthode:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,66)]

Dans l’exemple précédent, une politique « AtLeast21 » est créée. Il a une&mdash;seule exigence qui d’un âge minimum, qui est fourni comme un paramètre à l’exigence.

## <a name="iauthorizationservice"></a>IAuthorizationService (en) 

Le service principal qui détermine si <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>l’autorisation est réussie est :

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

Le code précédent met en évidence les deux méthodes de [l’IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).

<xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>est un service de marqueurs sans méthodes, et le mécanisme de suivi de l’autorisation est réussie.

Chacun <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> est responsable de vérifier si les exigences sont remplies :
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

La <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> classe est ce que le gestionnaire utilise pour marquer si les exigences ont été remplies :

```csharp
 context.Succeed(requirement)
```

Le code suivant montre la mise en œuvre par défaut simplifiée (et annotée avec commentaires) du service d’autorisation :

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

Le code suivant `ConfigureServices`montre un typique :

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

Utiliser <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> `[Authorize(Policy = "Something")]` ou pour l’autorisation.

## <a name="applying-policies-to-mvc-controllers"></a>Appliquer des politiques aux contrôleurs MVC

Si vous utilisez Razor Pages, consultez [les stratégies d’application des pages Razor](#applying-policies-to-razor-pages) dans ce document.

Les stratégies sont appliquées `[Authorize]` aux contrôleurs en utilisant l’attribut avec le nom de la stratégie. Par exemple :

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a>Appliquer des politiques aux pages Razor

Les stratégies sont appliquées `[Authorize]` aux Pages Razor en utilisant l’attribut avec le nom de la stratégie. Par exemple :

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

Les politiques peuvent également être appliquées aux Pages Razor en utilisant une [convention d’autorisation](xref:security/authorization/razor-pages-authorization).

## <a name="requirements"></a>Spécifications

Une exigence d’autorisation est une collecte de paramètres de données qu’une stratégie peut utiliser pour évaluer le principal utilisateur actuel. Dans notre politique « AtLeast21 », l’exigence est un paramètre&mdash;unique de l’âge minimum. Une exigence implémente [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), qui est une interface de marqueur vide. Une exigence d’âge minimum paramétrée pourrait être mise en œuvre comme suit :

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

Si une politique d’autorisation contient plusieurs exigences d’autorisation, toutes les exigences doivent être adoptées pour que l’évaluation de la politique réussisse. En d’autres termes, les exigences multiples d’autorisation ajoutées à une seule politique d’autorisation sont traitées sur une base **ET.**

> [!NOTE]
> Une exigence n’a pas besoin d’avoir des données ou des propriétés.

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a>Gestionnaires d’autorisation

Un gestionnaire d’autorisation est responsable de l’évaluation des propriétés d’une exigence. Le gestionnaire d’autorisation évalue les exigences par rapport à un [texte autorisationhandlercontexte](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) fourni pour déterminer si l’accès est autorisé.

Une exigence peut avoir [plusieurs gestionnaires](#security-authorization-policies-based-multiple-handlers). Un gestionnaire peut hériter [de AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), où `TRequirement` est l’exigence à traiter. Alternativement, un gestionnaire peut implémenter [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) pour gérer plus d’un type d’exigence.

### <a name="use-a-handler-for-one-requirement"></a>Utiliser un gestionnaire pour une exigence

<a name="security-authorization-handler-example"></a>

Voici un exemple d’une relation individuelles dans laquelle un gestionnaire d’âge minimum utilise une seule exigence :

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

Le code précédent détermine si le principal utilisateur actuel a une demande de date de naissance qui a été émise par un émetteur connu et digne de confiance. L’autorisation ne peut pas se produire lorsque la réclamation est manquante, auquel cas une tâche terminée est retournée. Lorsqu’une réclamation est présente, l’âge de l’utilisateur est calculé. Si l’utilisateur satisfait à l’âge minimum défini par l’exigence, l’autorisation est considérée comme réussie. Lorsque l’autorisation `context.Succeed` est acceptée, est invoquée avec l’exigence satisfaite comme seul paramètre.

### <a name="use-a-handler-for-multiple-requirements"></a>Utiliser un gestionnaire pour plusieurs exigences

Ce qui suit est un exemple d’une relation unique dans laquelle un gestionnaire d’autorisation peut traiter trois types d’exigences différentes :

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

Le code précédent traverse [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;une propriété contenant des exigences non marquées comme réussies. Pour `ReadPermission` une exigence, l’utilisateur doit être soit un propriétaire ou un commanditaire pour accéder à la ressource demandée. En cas d’exigence, `EditPermission` `DeletePermission` il doit être propriétaire de la ressource demandée.

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a>Enregistrement des gestionnaires

Les manutentionnaires sont inscrits à la collection de services pendant la configuration. Par exemple :

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,62-63,66)]

Le code précédent `MinimumAgeHandler` s’inscrit comme un `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`singleton en invoquant . Les manutentionnaires peuvent être enregistrés à l’aide de l’une des durées de [vie intégrées](xref:fundamentals/dependency-injection#service-lifetimes)du service.

## <a name="what-should-a-handler-return"></a>Qu’est-ce qu’un gestionnaire devrait revenir?

Notez `Handle` que la méthode dans l’exemple [du gestionnaire](#security-authorization-handler-example) ne rapporte aucune valeur. Comment un statut de succès ou d’échec est-il indiqué?

* Un gestionnaire indique `context.Succeed(IAuthorizationRequirement requirement)`le succès en appelant, en passant l’exigence qui a été validée avec succès.

* Un gestionnaire n’a pas besoin de gérer les défaillances en général, car d’autres gestionnaires pour la même exigence peuvent réussir.

* Pour garantir l’échec, même si `context.Fail`d’autres gestionnaires d’exigences réussissent, appelez .

Si un `context.Succeed` gestionnaire `context.Fail`appelle ou, tous les autres gestionnaires sont toujours appelés. Cela permet aux exigences de produire des effets secondaires, tels que l’enregistrement, qui a lieu même si un autre gestionnaire a validé ou échoué avec succès une exigence. Lorsqu’il `false`est configuré, la propriété [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) (disponible en ASP.NET Core 1.1 `context.Fail` et plus tard) court-circuite l’exécution des manutentionnaires lorsqu’il est appelé. `InvokeHandlersAfterFailure`par défaut `true`à , auquel cas tous les gestionnaires sont appelés.

> [!NOTE]
> Les préposés à l’autorisation sont appelés même si l’authentification échoue.

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a>Pourquoi voudrais-je plusieurs gestionnaires pour une exigence?

Dans les cas où vous souhaitez que l’évaluation soit sur une base **DE OU,** implémentez plusieurs gestionnaires pour une seule exigence. Par exemple, Microsoft a des portes qui ne s’ouvrent qu’avec des cartes clés. Si vous laissez votre carte-clé à la maison, la réceptionniste imprime un autocollant temporaire et vous ouvre la porte. Dans ce scénario, vous auriez une seule exigence, *BuildingEntry*, mais plusieurs gestionnaires, chacun examinant une seule exigence.

*BuildingEntryRequirement.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

*BadgeEntryHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

*TemporaryStickerHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

Assurez-vous que les deux gestionnaires sont [enregistrés](xref:security/authorization/policies#security-authorization-policies-based-handler-registration). Si l’un ou l’autre `BuildingEntryRequirement`gestionnaire réussit lorsqu’une politique évalue le, l’évaluation des politiques réussit.

## <a name="using-a-func-to-fulfill-a-policy"></a>Utilisation d’un func pour remplir une politique

Il peut y avoir des situations dans lesquelles l’exécution d’une politique est simple à exprimer dans le code. Il est possible de `Func<AuthorizationHandlerContext, bool>` fournir un lors de `RequireAssertion` la configuration de votre politique avec le constructeur de police.

Par exemple, `BadgeEntryHandler` le précédent pourrait être réécrit comme suit :

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=50-51,55-61)]

## <a name="accessing-mvc-request-context-in-handlers"></a>Accès au contexte de demande MVC chez les gestionnaires

La `HandleRequirementAsync` méthode que vous implémentez dans `AuthorizationHandlerContext` un `TRequirement` gestionnaire d’autorisation comporte deux paramètres : un et le que vous manipulez. Les cadres tels que MVC ou SignalR sont `Resource` libres `AuthorizationHandlerContext` d’ajouter n’importe quel objet à la propriété sur les informations supplémentaires de passer.

Lors de l’utilisation du routage de point final, l’autorisation est généralement gérée par l’autorisation Middleware. Dans ce cas, la `Resource` propriété <xref:Microsoft.AspNetCore.Http.Endpoint>est un cas de . Le point de terminaison peut être utilisé pour sonder la ressource sous-jacente à laquelle vous routez. Par exemple :

```csharp
if (context.Resource is Endpoint endpoint)
{
   var actionDescriptor = endpoint.Metadata.GetMetadata<ControllerActionDescriptor>();
   ...
}
```

Avec le routage traditionnel, ou lorsque l’autorisation se fait `Resource` dans <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> le cadre du filtre d’autorisation de MVC, la valeur de est un cas. Cette propriété donne `HttpContext` `RouteData`accès à , , et tout le reste fourni par MVC et Razor Pages.

L’utilisation `Resource` de la propriété est spécifique au cadre. L’utilisation `Resource` de l’information dans la propriété limite vos politiques d’autorisation à des cadres particuliers. Vous devez `Resource` lancer la `is` propriété à l’aide du mot clé, puis confirmer `InvalidCastException` le casting a réussi à s’assurer que votre code ne se bloque pas avec un moment exécuté sur d’autres cadres:

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end
