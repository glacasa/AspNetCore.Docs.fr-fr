---
title: Aperçu de ASP.NET’authentification de base
author: mjrousos
description: Découvrez l’authentification dans ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 03/03/2020
uid: security/authentication/index
ms.openlocfilehash: 404904ecfa30d1fe7e47f0daaa423ddd6f1b06e8
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "79434328"
---
# <a name="overview-of-aspnet-core-authentication"></a>Aperçu de ASP.NET authentification de base

Par [Mike Rousos](https://github.com/mjrousos)

L’authentification est le processus de détermination de l’identité d’un utilisateur. [L’autorisation](xref:security/authorization/introduction) est le processus de déterminer si un utilisateur a accès à une ressource. Dans ASP.NET Core, l’authentification `IAuthenticationService`est gérée par le , qui est utilisé par l’authentification [middleware](xref:fundamentals/middleware/index). Le service d’authentification utilise des gestionnaires d’authentification enregistrés pour mener à bien des actions liées à l’authentification. Voici quelques exemples d’actions liées à l’authentification :

* Authentifier un utilisateur.
* Répondre lorsqu’un utilisateur non athéné tente d’accéder à une ressource restreinte.

Les gestionnaires d’authentification enregistrés et leurs options de configuration sont appelés «schémas».

Les systèmes d’authentification sont `Startup.ConfigureServices`spécifiés en enregistrant des services d’authentification dans :

* En appelant une méthode d’extension `services.AddAuthentication` spécifique au `AddJwtBearer` `AddCookie`régime après un appel à (comme ou, par exemple). Ces méthodes d’extension utilisent [AuthenticationBuilder.AddScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*) pour enregistrer les schémas avec des paramètres appropriés.
* Moins souvent, en appelant [AuthenticationBuilder.AddScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*) directement.

Par exemple, le code suivant enregistre les services d’authentification et les gestionnaires pour les systèmes d’authentification des biscuits et des porteurs JWT :

```csharp
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddJwtBearer(JwtBearerDefaults.AuthenticationScheme, options => Configuration.Bind("JwtSettings", options))
    .AddCookie(CookieAuthenticationDefaults.AuthenticationScheme, options => Configuration.Bind("CookieSettings", options));
```

Le `AddAuthentication` `JwtBearerDefaults.AuthenticationScheme` paramètre est le nom du régime à utiliser par défaut lorsqu’un système spécifique n’est pas demandé.

Si plusieurs schémas sont utilisés, les stratégies d’autorisation (ou attributs d’autorisation) peuvent [spécifier le système d’authentification (ou les schémas)](xref:security/authorization/limitingidentitybyscheme) dont ils dépendent pour authentifier l’utilisateur. Dans l’exemple ci-dessus, le système d’authentification des cookies pourrait être utilisé en spécifiant son nom (par`CookieAuthenticationDefaults.AuthenticationScheme` défaut, bien qu’un nom différent puisse être fourni lors de l’appel). `AddCookie`

Dans certains cas, `AddAuthentication` l’appel est automatiquement effectué par d’autres méthodes d’extension. Par exemple, lors de [l’utilisation de ASP.NET’identité de base](xref:security/authentication/identity), `AddAuthentication` est appelé interne.

Le middleware d’authentification est ajouté en `Startup.Configure` appelant la <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> méthode d’extension sur l’application `IApplicationBuilder`. Calling `UseAuthentication` enregistre le middleware qui utilise les systèmes d’authentification précédemment enregistrés. Appelez `UseAuthentication` avant tout middleware qui dépend des utilisateurs étant authentifiés. Lors de l’utilisation du `UseAuthentication` routage de point de terminaison, l’appel doit aller :

* Après `UseRouting`, de sorte que les informations d’itinéraire sont disponibles pour les décisions d’authentification.
* Avant, `UseEndpoints`de sorte que les utilisateurs sont authentifiés avant d’accéder aux points de terminaison.

## <a name="authentication-concepts"></a>Concepts d’authentification

### <a name="authentication-scheme"></a>Schéma d'authentification

Un schéma d’authentification est un nom qui correspond à :

* Gestionnaire d'authentifications.
* Options pour configurer cet exemple spécifique du gestionnaire.

Les schémas sont utiles comme mécanisme pour se référer à l’authentification, le défi, et interdire les comportements du gestionnaire associé. Par exemple, une politique d’autorisation peut utiliser les noms de régime pour spécifier quel système d’authentification (ou les schémas) doivent être utilisés pour authentifier l’utilisateur. Lors de la configuration de l’authentification, il est courant de spécifier le schéma d’authentification par défaut. Le régime par défaut est utilisé à moins qu’une ressource ne demande un régime spécifique. Il est également possible de :

* Spécifiez différents schémas par défaut à utiliser pour authentifier, contester et interdire les actions.
* Combinez plusieurs schémas en un seul recours à [des schémas politiques](xref:security/authentication/policyschemes).

### <a name="authentication-handler"></a>Gestionnaire d’authentification

Un gestionnaire d’authentification :

* Est un type qui met en œuvre le comportement d’un régime.
* Est dérivé <xref:Microsoft.AspNetCore.Authentication.IAuthenticationHandler> <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>de ou .
* A la responsabilité première d’authentifier les utilisateurs.

Basé sur la configuration du schéma d’authentification et le contexte de demande entrant, les gestionnaires d’authentification :

* Construire <xref:Microsoft.AspNetCore.Authentication.AuthenticationTicket> des objets représentant l’identité de l’utilisateur si l’authentification est réussie.
* Déclaration 'aucun résultat' ou 'échec' si l’authentification est infructueuse.
* Avoir des méthodes pour contester et interdire les actions pour quand les utilisateurs tentent d’accéder aux ressources:
  * Ils ne sont pas autorisés à accéder (interdit).
  * Quand ils ne sont pas athénés (défi).

### <a name="authenticate"></a>Authenticate

L’action authentifiée d’un système d’authentification est responsable de la construction de l’identité de l’utilisateur en fonction du contexte de la demande. Il renvoie un <xref:Microsoft.AspNetCore.Authentication.AuthenticateResult> indiquant si l’authentification a été réussie et, dans l’affirmative, l’identité de l’utilisateur dans un billet d’authentification. Consultez <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync%2A>. Les exemples d’authentification incluent :

* Un schéma d’authentification des cookies construisant l’identité de l’utilisateur à partir de cookies.
* Un système de porteur JWT déséialisant et validant un jeton JWT porte-à-l’œuvre pour construire l’identité de l’utilisateur.

### <a name="challenge"></a>Test

Un défi d’authentification est invoqué par Autorisation lorsqu’un utilisateur non authentique demande un critère de terminaison qui nécessite une authentification. Un défi d’authentification est émis, par exemple, lorsqu’un utilisateur anonyme demande une ressource restreinte ou clique sur un lien de connexion. L’autorisation invoque une contestation à l’aide du schéma d’authentification spécifié ou le défaut s’il n’y en a pas. Consultez <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync%2A>. Les exemples de défi d’authentification incluent :

* Un schéma d’authentification des cookies redirigeant l’utilisateur vers une page de connexion.
* Un système de porteur JWT retour d’un résultat 401 avec un `www-authenticate: bearer` en-tête.

Une action de défi doit permettre à l’utilisateur de savoir quel mécanisme d’authentification utiliser pour accéder à la ressource demandée.

### <a name="forbid"></a>Interdire

L’action interdite d’un système d’authentification est appelée par Autorisation lorsqu’un utilisateur authentifié tente d’accéder à une ressource à laquelle il n’est pas autorisé à accéder. Consultez <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync%2A>. L’authentification interdit des exemples incluent :
* Un système d’authentification des cookies redirigeant l’utilisateur vers une page indiquant que l’accès était interdit.
* Un système de porteur JWT retour d’un résultat 403.
* Un schéma d’authentification personnalisé redirigeant vers une page où l’utilisateur peut demander l’accès à la ressource.

Une action interdite peut faire savoir à l’utilisateur :

* Ils sont authentifiés.
* Ils ne sont pas autorisés à accéder à la ressource demandée.

Voir les liens suivants pour les différences entre défi et interdire:

* [Défi et interdire avec un gestionnaire de ressources opérationnelles](xref:security/authorization/resourcebased#challenge-and-forbid-with-an-operational-resource-handler).
* [Différences entre le défi et l’interdiction](xref:security/authorization/secure-data#challenge).

## <a name="authentication-providers-per-tenant"></a>Fournisseurs d’authentification par locataire

ASP.NET cadre de base n’a pas de solution intégrée pour l’authentification multi-locataires.
Bien qu’il soit certainement possible pour les clients d’en écrire un, en utilisant les fonctionnalités intégrées, nous recommandons aux clients d’examiner [Orchard Core](https://www.orchardcore.net/) à cette fin.

Orchard Core est :

* Un cadre d’application modulaire et multi-locataire open source construit avec ASP.NET Core.
* Un système de gestion de contenu (CMS) construit au-dessus de ce cadre d’application.

Consultez la source [Orchard Core](https://github.com/OrchardCMS/OrchardCore) pour un exemple de fournisseurs d’authentification par locataire.

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authentication/policyschemes>
* <xref:security/authorization/secure-data>
