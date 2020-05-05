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
ms.openlocfilehash: f273c3e9db74fa63de85c65d94223d0ef7326036
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775633"
---
# <a name="simple-authorization-in-aspnet-core"></a><span data-ttu-id="a4e66-103">Autorisation simple dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a4e66-103">Simple authorization in ASP.NET Core</span></span>

<a name="security-authorization-simple"></a>

<span data-ttu-id="a4e66-104">L’autorisation dans MVC est contrôlée par `AuthorizeAttribute` le biais de l’attribut et de ses différents paramètres.</span><span class="sxs-lookup"><span data-stu-id="a4e66-104">Authorization in MVC is controlled through the `AuthorizeAttribute` attribute and its various parameters.</span></span> <span data-ttu-id="a4e66-105">Au plus simple, l’application de `AuthorizeAttribute` l’attribut à un contrôleur ou une action limite l’accès au contrôleur ou à l’action à n’importe quel utilisateur authentifié.</span><span class="sxs-lookup"><span data-stu-id="a4e66-105">At its simplest, applying the `AuthorizeAttribute` attribute to a controller or action limits access to the controller or action to any authenticated user.</span></span>

<span data-ttu-id="a4e66-106">Par exemple, le code suivant limite l’accès à `AccountController` n’importe quel utilisateur authentifié.</span><span class="sxs-lookup"><span data-stu-id="a4e66-106">For example, the following code limits access to the `AccountController` to any authenticated user.</span></span>

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

<span data-ttu-id="a4e66-107">Si vous souhaitez appliquer une autorisation à une action plutôt qu’au contrôleur, appliquez l' `AuthorizeAttribute` attribut à l’action elle-même :</span><span class="sxs-lookup"><span data-stu-id="a4e66-107">If you want to apply authorization to an action rather than the controller, apply the `AuthorizeAttribute` attribute to the action itself:</span></span>

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

<span data-ttu-id="a4e66-108">Désormais, seuls les utilisateurs authentifiés peuvent `Logout` accéder à la fonction.</span><span class="sxs-lookup"><span data-stu-id="a4e66-108">Now only authenticated users can access the `Logout` function.</span></span>

<span data-ttu-id="a4e66-109">Vous pouvez également utiliser l' `AllowAnonymous` attribut pour autoriser l’accès par des utilisateurs non authentifiés à des actions individuelles.</span><span class="sxs-lookup"><span data-stu-id="a4e66-109">You can also use the `AllowAnonymous` attribute to allow access by non-authenticated users to individual actions.</span></span> <span data-ttu-id="a4e66-110">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="a4e66-110">For example:</span></span>

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

<span data-ttu-id="a4e66-111">Cela autorise uniquement les utilisateurs authentifiés à `AccountController`, à l’exception de `Login` l’action, qui est accessible par tout le monde, indépendamment de leur état authentifié ou non authentifié/anonyme.</span><span class="sxs-lookup"><span data-stu-id="a4e66-111">This would allow only authenticated users to the `AccountController`, except for the `Login` action, which is accessible by everyone, regardless of their authenticated or unauthenticated / anonymous status.</span></span>

> [!WARNING]
> <span data-ttu-id="a4e66-112">`[AllowAnonymous]`ignore toutes les instructions d’autorisation.</span><span class="sxs-lookup"><span data-stu-id="a4e66-112">`[AllowAnonymous]` bypasses all authorization statements.</span></span> <span data-ttu-id="a4e66-113">Si vous combinez `[AllowAnonymous]` et `[Authorize]` n’importe quel `[Authorize]` attribut, les attributs sont ignorés.</span><span class="sxs-lookup"><span data-stu-id="a4e66-113">If you combine `[AllowAnonymous]` and any `[Authorize]` attribute, the `[Authorize]` attributes are ignored.</span></span> <span data-ttu-id="a4e66-114">Par exemple, si vous `[AllowAnonymous]` appliquez au niveau du contrôleur, `[Authorize]` tous les attributs du même contrôleur (ou de toute action qu’il contient) sont ignorés.</span><span class="sxs-lookup"><span data-stu-id="a4e66-114">For example if you apply `[AllowAnonymous]` at the controller level, any `[Authorize]` attributes on the same controller (or on any action within it) is ignored.</span></span>
