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
# <a name="simple-authorization-in-aspnet-core"></a>Autorisation simple dans ASP.NET Core

<a name="security-authorization-simple"></a>

L’autorisation dans MVC est contrôlée par `AuthorizeAttribute` le biais de l’attribut et de ses différents paramètres. Au plus simple, l’application de `AuthorizeAttribute` l’attribut à un contrôleur ou une action limite l’accès au contrôleur ou à l’action à n’importe quel utilisateur authentifié.

Par exemple, le code suivant limite l’accès à `AccountController` n’importe quel utilisateur authentifié.

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

Si vous souhaitez appliquer une autorisation à une action plutôt qu’au contrôleur, appliquez l' `AuthorizeAttribute` attribut à l’action elle-même :

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

Désormais, seuls les utilisateurs authentifiés peuvent `Logout` accéder à la fonction.

Vous pouvez également utiliser l' `AllowAnonymous` attribut pour autoriser l’accès par des utilisateurs non authentifiés à des actions individuelles. Par exemple :

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

Cela autorise uniquement les utilisateurs authentifiés à `AccountController`, à l’exception de `Login` l’action, qui est accessible par tout le monde, indépendamment de leur état authentifié ou non authentifié/anonyme.

> [!WARNING]
> `[AllowAnonymous]`ignore toutes les instructions d’autorisation. Si vous combinez `[AllowAnonymous]` et `[Authorize]` n’importe quel `[Authorize]` attribut, les attributs sont ignorés. Par exemple, si vous `[AllowAnonymous]` appliquez au niveau du contrôleur, `[Authorize]` tous les attributs du même contrôleur (ou de toute action qu’il contient) sont ignorés.
