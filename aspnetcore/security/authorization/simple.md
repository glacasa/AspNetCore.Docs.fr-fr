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
# <a name="simple-authorization-in-aspnet-core"></a>Autorisation simple dans ASP.NET Core

<a name="security-authorization-simple"></a>

L’autorisation dans ASP.NET Core est contrôlée par <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> et ses différents paramètres. Dans sa forme la plus simple, l’application `[Authorize]` de l’attribut à un contrôleur, une action ou une Razor page limite l’accès à ce composant à n’importe quel utilisateur authentifié.

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

Désormais, seuls les utilisateurs authentifiés peuvent accéder à la `Logout` fonction.

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

Cela autorise uniquement les utilisateurs authentifiés à, à l' `AccountController` exception de l' `Login` action, qui est accessible par tout le monde, indépendamment de leur état authentifié ou non authentifié/anonyme.

> [!WARNING]
> `[AllowAnonymous]`ignore toutes les instructions d’autorisation. Si vous combinez `[AllowAnonymous]` et n’importe quel `[Authorize]` attribut, les `[Authorize]` attributs sont ignorés. Par exemple, si vous appliquez `[AllowAnonymous]` au niveau du contrôleur, tous les `[Authorize]` attributs du même contrôleur (ou de toute action qu’il contient) sont ignorés.
