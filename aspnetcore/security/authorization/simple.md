---
title: Autorisation simple dans ASP.NET Core
author: rick-anderson
description: Découvrez comment utiliser l’attribut Authorize pour restreindre l’accès aux contrôleurs et aux actions de ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/simple
ms.openlocfilehash: 4ec31354d7fe11af75fd3a0045b4045f83721cb5
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/05/2020
ms.locfileid: "84272123"
---
# <a name="simple-authorization-in-aspnet-core"></a><span data-ttu-id="6ac7e-103">Autorisation simple dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6ac7e-103">Simple authorization in ASP.NET Core</span></span>

<a name="security-authorization-simple"></a>

<span data-ttu-id="6ac7e-104">L’autorisation dans ASP.NET Core est contrôlée par <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> et ses différents paramètres.</span><span class="sxs-lookup"><span data-stu-id="6ac7e-104">Authorization in ASP.NET Core is controlled with <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> and its various parameters.</span></span> <span data-ttu-id="6ac7e-105">Dans sa forme la plus simple, l’application `[Authorize]` de l’attribut à un contrôleur, une action ou une Razor page limite l’accès à ce composant à n’importe quel utilisateur authentifié.</span><span class="sxs-lookup"><span data-stu-id="6ac7e-105">In its simplest form, applying the `[Authorize]` attribute to a controller, action, or Razor Page, limits access to that component to any authenticated user.</span></span>

<span data-ttu-id="6ac7e-106">Par exemple, le code suivant limite l’accès à `AccountController` n’importe quel utilisateur authentifié.</span><span class="sxs-lookup"><span data-stu-id="6ac7e-106">For example, the following code limits access to the `AccountController` to any authenticated user.</span></span>

```csharp
[Authorize]
public class AccountController : Controller
{
    public ActionResult Login()
    {
    }

    public ActionResult Logout()
    {
    }
}
```

<span data-ttu-id="6ac7e-107">Si vous souhaitez appliquer une autorisation à une action plutôt qu’au contrôleur, appliquez l' `AuthorizeAttribute` attribut à l’action elle-même :</span><span class="sxs-lookup"><span data-stu-id="6ac7e-107">If you want to apply authorization to an action rather than the controller, apply the `AuthorizeAttribute` attribute to the action itself:</span></span>

```csharp
public class AccountController : Controller
{
   public ActionResult Login()
   {
   }

   [Authorize]
   public ActionResult Logout()
   {
   }
}
```

<span data-ttu-id="6ac7e-108">Désormais, seuls les utilisateurs authentifiés peuvent accéder à la `Logout` fonction.</span><span class="sxs-lookup"><span data-stu-id="6ac7e-108">Now only authenticated users can access the `Logout` function.</span></span>

<span data-ttu-id="6ac7e-109">Vous pouvez également utiliser l' `AllowAnonymous` attribut pour autoriser l’accès par des utilisateurs non authentifiés à des actions individuelles.</span><span class="sxs-lookup"><span data-stu-id="6ac7e-109">You can also use the `AllowAnonymous` attribute to allow access by non-authenticated users to individual actions.</span></span> <span data-ttu-id="6ac7e-110">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="6ac7e-110">For example:</span></span>

```csharp
[Authorize]
public class AccountController : Controller
{
    [AllowAnonymous]
    public ActionResult Login()
    {
    }

    public ActionResult Logout()
    {
    }
}
```

<span data-ttu-id="6ac7e-111">Cela autorise uniquement les utilisateurs authentifiés à, à l' `AccountController` exception de l' `Login` action, qui est accessible par tout le monde, indépendamment de leur état authentifié ou non authentifié/anonyme.</span><span class="sxs-lookup"><span data-stu-id="6ac7e-111">This would allow only authenticated users to the `AccountController`, except for the `Login` action, which is accessible by everyone, regardless of their authenticated or unauthenticated / anonymous status.</span></span>

> [!WARNING]
> <span data-ttu-id="6ac7e-112">`[AllowAnonymous]`ignore toutes les instructions d’autorisation.</span><span class="sxs-lookup"><span data-stu-id="6ac7e-112">`[AllowAnonymous]` bypasses all authorization statements.</span></span> <span data-ttu-id="6ac7e-113">Si vous combinez `[AllowAnonymous]` et n’importe quel `[Authorize]` attribut, les `[Authorize]` attributs sont ignorés.</span><span class="sxs-lookup"><span data-stu-id="6ac7e-113">If you combine `[AllowAnonymous]` and any `[Authorize]` attribute, the `[Authorize]` attributes are ignored.</span></span> <span data-ttu-id="6ac7e-114">Par exemple, si vous appliquez `[AllowAnonymous]` au niveau du contrôleur, tous les `[Authorize]` attributs du même contrôleur (ou de toute action qu’il contient) sont ignorés.</span><span class="sxs-lookup"><span data-stu-id="6ac7e-114">For example if you apply `[AllowAnonymous]` at the controller level, any `[Authorize]` attributes on the same controller (or on any action within it) is ignored.</span></span>
