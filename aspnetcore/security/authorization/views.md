---
title: Autorisation basée sur les vues dans ASP.NET Core MVC
author: rick-anderson
description: Ce document montre comment injecter et utiliser le service d’autorisation à l’intérieur Razor d’une vue de ASP.net core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 11/08/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/views
ms.openlocfilehash: 1a3b4a92d270844fa99fc9fb0283226e94edb22d
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775620"
---
# <a name="view-based-authorization-in-aspnet-core-mvc"></a>Autorisation basée sur les vues dans ASP.NET Core MVC

Un développeur souhaite souvent afficher, masquer ou modifier une interface utilisateur en fonction de l’identité de l’utilisateur actuel. Vous pouvez accéder au service d’autorisation dans les vues MVC via l' [injection de dépendances](xref:fundamentals/dependency-injection). Pour injecter le service d’autorisation Razor dans une vue, `@inject` utilisez la directive :

```cshtml
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService
```

Si vous souhaitez utiliser le service d’autorisation dans chaque vue, `@inject` Placez la directive dans le fichier *_ViewImports. cshtml* du répertoire *views* . Pour plus d’informations, consultez [Injection de dépendances dans les vues](xref:mvc/views/dependency-injection).

Utilisez le service d’autorisation injecté pour appeler `AuthorizeAsync` exactement de la même façon que lors de l' [autorisation basée sur les ressources](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative):

```cshtml
@if ((await AuthorizationService.AuthorizeAsync(User, "PolicyName")).Succeeded)
{
    <p>This paragraph is displayed because you fulfilled PolicyName.</p>
}
```

Dans certains cas, la ressource sera votre modèle de vue. Appelez `AuthorizeAsync` exactement de la même façon que vous le feriez lors de l' [autorisation basée sur les ressources](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative):

```cshtml
@if ((await AuthorizationService.AuthorizeAsync(User, Model, Operations.Edit)).Succeeded)
{
    <p><a class="btn btn-default" role="button"
        href="@Url.Action("Edit", "Document", new { id = Model.Id })">Edit</a></p>
}
```

Dans le code précédent, le modèle est passé en tant que ressource que l’évaluation de la stratégie doit prendre en compte.

> [!WARNING]
> Ne vous fiez pas au basculement de la visibilité des éléments d’interface utilisateur de votre application en tant que seul contrôle d’autorisation. Le masquage d’un élément d’interface utilisateur peut ne pas empêcher complètement l’accès à son action de contrôleur associée. Par exemple, considérez le bouton dans l’extrait de code précédent. Un utilisateur peut appeler la `Edit` méthode d’action s’il connaît l’URL de ressource relative */document/Edit/1*. Pour cette raison, la `Edit` méthode d’action doit effectuer sa propre vérification d’autorisation.
