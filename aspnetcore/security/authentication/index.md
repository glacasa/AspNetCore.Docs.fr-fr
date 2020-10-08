---
title: Vue d’ensemble de l’authentification ASP.NET Core
author: mjrousos
description: En savoir plus sur l’authentification dans ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 03/03/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/index
ms.openlocfilehash: 2511d68dd049ca47dea002d8960e7d2fd0207383
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88632640"
---
# <a name="overview-of-aspnet-core-authentication"></a>Vue d’ensemble de l’authentification ASP.NET Core

Par [Mike Rousos](https://github.com/mjrousos)

L’authentification est le processus qui consiste à déterminer l’identité d’un utilisateur. L' [autorisation](xref:security/authorization/introduction) est le processus qui consiste à déterminer si un utilisateur a accès à une ressource. Dans ASP.NET Core, l’authentification est gérée par le `IAuthenticationService` , qui est utilisé par l' [intergiciel (middleware](xref:fundamentals/middleware/index)) d’authentification. Le service d’authentification utilise des gestionnaires d’authentification inscrits pour effectuer des actions liées à l’authentification. Voici quelques exemples d’actions liées à l’authentification :

* Authentification d’un utilisateur.
* Réponse quand un utilisateur non authentifié tente d’accéder à une ressource restreinte.

Les gestionnaires d’authentification inscrits et leurs options de configuration sont appelés « schémas ».

Les schémas d’authentification sont spécifiés en inscrivant les services d’authentification dans `Startup.ConfigureServices` :

* En appelant une méthode d’extension spécifique au schéma après un appel à `services.AddAuthentication` (tel que `AddJwtBearer` ou `AddCookie` , par exemple). Ces méthodes d’extension utilisent [AuthenticationBuilder. AddScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*) pour inscrire des schémas avec les paramètres appropriés.
* Moins fréquemment, en appelant [AuthenticationBuilder. AddScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*) directement.

Par exemple, le code suivant inscrit les services d’authentification et les gestionnaires pour cookie et les schémas d’authentification du porteur JWT :

```csharp
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddJwtBearer(JwtBearerDefaults.AuthenticationScheme, options => Configuration.Bind("JwtSettings", options))
    .AddCookie(CookieAuthenticationDefaults.AuthenticationScheme, options => Configuration.Bind("CookieSettings", options));
```

Le `AddAuthentication` paramètre `JwtBearerDefaults.AuthenticationScheme` est le nom du schéma à utiliser par défaut lorsqu’un schéma spécifique n’est pas demandé.

Si plusieurs schémas sont utilisés, les stratégies d’autorisation (ou attributs d’autorisation) peuvent [spécifier le schéma d’authentification (ou les schémas)](xref:security/authorization/limitingidentitybyscheme) dont ils dépendent pour authentifier l’utilisateur. Dans l’exemple ci-dessus, le cookie schéma d’authentification peut être utilisé en spécifiant son nom ( `CookieAuthenticationDefaults.AuthenticationScheme` par défaut, bien qu’un autre nom puisse être fourni lors de l’appel de `AddCookie` ).

Dans certains cas, l’appel à `AddAuthentication` est automatiquement effectué par d’autres méthodes d’extension. Par exemple, lors de l’utilisation de [ASP.NET Core Identity](xref:security/authentication/identity) , `AddAuthentication` est appelé en interne.

L’intergiciel d’authentification est ajouté dans `Startup.Configure` en appelant la <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> méthode d’extension sur le de l’application `IApplicationBuilder` . `UseAuthentication`L’appel de enregistre l’intergiciel (middleware) qui utilise les schémas d’authentification précédemment enregistrés. Appelez `UseAuthentication` avant tout middleware dépendant des utilisateurs authentifiés. Lorsque vous utilisez le routage de point de terminaison, l’appel à `UseAuthentication` doit atteindre :

* Après `UseRouting` , afin que les informations de routage soient disponibles pour les décisions d’authentification.
* Avant `UseEndpoints` , afin que les utilisateurs soient authentifiés avant d’accéder aux points de terminaison.

## <a name="authentication-concepts"></a>Concepts d’authentification

### <a name="authentication-scheme"></a>Schéma d'authentification

Un schéma d’authentification est un nom qui correspond à :

* Gestionnaire d'authentifications.
* Options de configuration de cette instance spécifique du gestionnaire.

Les schémas sont utiles comme un mécanisme permettant de faire référence aux comportements d’authentification, de stimulation et d’interdiction du gestionnaire associé. Par exemple, une stratégie d’autorisation peut utiliser des noms de schémas pour spécifier le (s) schéma (s) d’authentification à utiliser pour authentifier l’utilisateur. Lors de la configuration de l’authentification, il est courant de spécifier le schéma d’authentification par défaut. Le schéma par défaut est utilisé, sauf si une ressource demande un schéma spécifique. Il est également possible d’effectuer les opérations suivantes :

* Spécifiez différents schémas par défaut à utiliser pour les actions Authenticate, Challenge et interdire.
* Combiner plusieurs schémas en un seul à l’aide de [modèles de stratégie](xref:security/authentication/policyschemes).

### <a name="authentication-handler"></a>Gestionnaire d’authentification

Un gestionnaire d’authentification :

* Est un type qui implémente le comportement d’un schéma.
* Est dérivée de <xref:Microsoft.AspNetCore.Authentication.IAuthenticationHandler> ou <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> .
* A la responsabilité principale d’authentifier les utilisateurs.

En fonction de la configuration du schéma d’authentification et du contexte de la requête entrante, les gestionnaires d’authentification :

* Construisez <xref:Microsoft.AspNetCore.Authentication.AuthenticationTicket> des objets représentant l’identité de l’utilisateur si l’authentification réussit.
* Retourne’no result’ou’Failure’si l’authentification échoue.
* Utilisez des méthodes pour les actions de stimulation et de interdiction lorsque les utilisateurs tentent d’accéder aux ressources :
  * Ils ne sont pas autorisés à accéder (interdire).
  * Lorsqu’ils ne sont pas authentifiés (Challenge).

### <a name="authenticate"></a>Authenticate

L’action d’authentification d’un schéma d’authentification est responsable de la construction de l’identité de l’utilisateur en fonction du contexte de la requête. Elle retourne un <xref:Microsoft.AspNetCore.Authentication.AuthenticateResult> qui indique si l’authentification a réussi et, le cas échéant, l’identité de l’utilisateur dans un ticket d’authentification. Consultez <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync%2A>. Les exemples d’authentification sont les suivants :

* Un cookie schéma d’authentification qui construit l’identité de l’utilisateur à partir de cookie s.
* Un modèle de porteur JWT qui désérialise et valide un jeton de porteur JWT pour construire l’identité de l’utilisateur.

### <a name="challenge"></a>Problème

Une demande d’authentification est appelée par l’autorisation lorsqu’un utilisateur non authentifié demande un point de terminaison qui requiert une authentification. Une demande d’authentification est émise, par exemple, lorsqu’un utilisateur anonyme demande une ressource restreinte ou clique sur un lien de connexion. L’autorisation appelle un défi à l’aide du ou des schémas d’authentification spécifiés, ou la valeur par défaut si aucun n’est spécifié. Consultez <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync%2A>. Les exemples de demande d’authentification sont les suivants :

* cookieSchéma d’authentification redirigeant l’utilisateur vers une page de connexion.
* Un modèle de porteur JWT renvoyant un résultat 401 avec un `www-authenticate: bearer` en-tête.

Une action de stimulation doit permettre à l’utilisateur de connaître le mécanisme d’authentification à utiliser pour accéder à la ressource demandée.

### <a name="forbid"></a>Disant

L’action interdire d’un schéma d’authentification est appelée par l’autorisation lorsqu’un utilisateur authentifié tente d’accéder à une ressource à laquelle il n’est pas autorisé à accéder. Consultez <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync%2A>. Les exemples d’authentifications interdits sont les suivants :
* cookieSchéma d’authentification redirigeant l’utilisateur vers une page indiquant que l’accès a été interdit.
* Un schéma de porteur JWT renvoyant un résultat 403.
* Schéma d’authentification personnalisé redirigeant vers une page dans laquelle l’utilisateur peut demander l’accès à la ressource.

Une action interdire peut informer l’utilisateur :

* Ils sont authentifiés.
* Ils ne sont pas autorisés à accéder à la ressource demandée.

Consultez les liens suivants pour connaître les différences entre la stimulation et l’interdiction :

* [Défier et interdire l’utilisation d’un gestionnaire de ressources opérationnelles](xref:security/authorization/resourcebased#challenge-and-forbid-with-an-operational-resource-handler).
* [Différences entre la stimulation et](xref:security/authorization/secure-data#challenge)l’interdiction.

## <a name="authentication-providers-per-tenant"></a>Fournisseurs d’authentification par locataire

ASP.NET Core Framework n’a pas de solution intégrée pour l’authentification mutualisée.
Bien qu’il soit possible pour les clients d’en écrire un, à l’aide des fonctionnalités intégrées, nous recommandons aux clients d’envisager l’utilisation d'[Orchard Core](https://www.orchardcore.net/) à cet effet.

Orchard Core est :

* Une infrastructure d’application modulaire et mutualisée Open source générée avec ASP.NET Core.
* Un système de gestion de contenu (CMS) basé sur ce Framework d’application.

Consultez le code source d’[Orchard Core](https://github.com/OrchardCMS/OrchardCore) pour obtenir un exemple de fournisseurs d’authentification par client.

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authentication/policyschemes>
* <xref:security/authorization/secure-data>
* [Exiger globalement des utilisateurs authentifiés](xref:security/authorization/secure-data#rau)
