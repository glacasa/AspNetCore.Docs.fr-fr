---
title: Sécuriser Blazor ASP.net Core webassembly
author: guardrex
description: Découvrez comment sécuriser Blazor des applications WebAssemlby en tant qu’applications à page unique (spas).
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/22/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/index
ms.openlocfilehash: dba70afdb7a0a50da7ded38437860b52d2ce5f88
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82110892"
---
# <a name="secure-aspnet-core-opno-locblazor-webassembly"></a>Sécuriser Blazor ASP.net Core webassembly

Par [Javier Calvarro Nelson](https://github.com/javiercn)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

> [!NOTE]
> Les instructions de cet article s’appliquent à ASP.NET Core 3,2 Preview 4. Cette rubrique sera mise à jour pour couvrir l’aperçu 5 le vendredi 24 avril.

BlazorLes applications webassembly sont sécurisées de la même façon que les applications à page unique (SPAs). Il existe plusieurs approches pour authentifier les utilisateurs dans la base de code, mais l’approche la plus courante et la plus complète consiste à utiliser une implémentation basée sur le [protocole OAuth 2,0](https://oauth.net/), par exemple [Open ID Connect (OIDC)](https://openid.net/connect/).

## <a name="authentication-library"></a>Bibliothèque d’authentification

BlazorWebassembly prend en charge l’authentification et l’autorisation d' `Microsoft.AspNetCore.Components.WebAssembly.Authentication` applications à l’aide de OIDC via la bibliothèque. La bibliothèque fournit un ensemble de primitives pour l’authentification en toute transparence par rapport aux principaux de ASP.NET Core. La bibliothèque intègre ASP.NET Core identité avec la prise en charge des autorisations d’API basée sur le [serveur d’identité](https://identityserver.io/). La bibliothèque peut s’authentifier auprès d’un fournisseur d’identité (IP) tiers qui prend en charge OIDC, qui sont appelés fournisseurs OpenID.

La prise en charge Blazor de l’authentification dans webassembly repose sur la bibliothèque *OIDC-client. js* , qui est utilisée pour gérer les détails du protocole d’authentification sous-jacent.

D’autres options d’authentification de la fonction de l’interauthentification existent, telles que l’utilisation de cookies SameSite. Toutefois, la conception de l' Blazor ingénierie de webassembly est réglée sur OAUTH et OIDC comme meilleure option pour Blazor l’authentification dans les applications webassembly. [L’authentification basée sur les jetons](xref:security/anti-request-forgery#token-based-authentication) basée sur des [jetons Web JSON (jetons JWT)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) a été choisie via [l’authentification basée](xref:security/anti-request-forgery#cookie-based-authentication) sur les cookies pour des raisons fonctionnelles et de sécurité :

* L’utilisation d’un protocole basé sur les jetons offre une surface d’attaque plus réduite, car les jetons ne sont pas envoyés dans toutes les demandes.
* Les points de terminaison de serveur ne nécessitent pas de protection contre la [falsification de requête intersites (CSRF)](xref:security/anti-request-forgery) , car les jetons sont envoyés explicitement. Cela vous permet d’héberger Blazor des applications webassembly avec des applications MVC ou des pages Razor.
* Les jetons ont des autorisations plus étroites que les cookies. Par exemple, les jetons ne peuvent pas être utilisés pour gérer le compte d’utilisateur ou modifier le mot de passe d’un utilisateur, sauf si une telle fonctionnalité est implémentée de manière explicite.
* Les jetons ont une durée de vie brève, par défaut d’une heure, qui limite la fenêtre d’attaque. Les jetons peuvent également être révoqués à tout moment.
* Les jetons JWT autonomes offrent des garanties au client et au serveur sur le processus d’authentification. Par exemple, un client a la possibilité de détecter et de valider que les jetons qu’il reçoit sont légitimes et ont été émis dans le cadre d’un processus d’authentification donné. Si un tiers tente de basculer un jeton au milieu du processus d’authentification, le client peut détecter le jeton commuté et éviter de l’utiliser.
* Les jetons avec OAuth et OIDC ne reposent pas sur l’agent utilisateur qui se comporte correctement pour garantir la sécurité de l’application.
* Les protocoles basés sur les jetons, tels que OAuth et OIDC, permettent d’authentifier et d’autoriser des applications hébergées et autonomes avec le même ensemble de caractéristiques de sécurité.

## <a name="authentication-process-with-oidc"></a>Processus d’authentification avec OIDC

La `Microsoft.AspNetCore.Components.WebAssembly.Authentication` bibliothèque propose plusieurs primitives pour implémenter l’authentification et l’autorisation à l’aide de OIDC. En général, l’authentification fonctionne comme suit :

* Lorsqu’un utilisateur anonyme sélectionne le bouton de connexion ou demande une page pour [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) laquelle l’attribut est appliqué, l’utilisateur est redirigé vers la page de connexion`/authentication/login`de l’application ().
* Dans la page de connexion, la bibliothèque d’authentification prépare une redirection vers le point de terminaison d’autorisation. Le point de terminaison d’autorisation est Blazor en dehors de l’application webassembly et peut être hébergé à une origine distincte. Le point de terminaison est chargé de déterminer si l’utilisateur est authentifié et d’émettre un ou plusieurs jetons en réponse. La bibliothèque d’authentification fournit un rappel de connexion pour recevoir la réponse d’authentification.
  * Si l’utilisateur n’est pas authentifié, l’utilisateur est redirigé vers le système d’authentification sous-jacent, qui est généralement ASP.NET Core identité.
  * Si l’utilisateur a déjà été authentifié, le point de terminaison d’autorisation génère les jetons appropriés et redirige le navigateur vers le point de terminaison de`/authentication/login-callback`rappel de connexion ().
* Lorsque l' Blazor application webassembly charge le point de terminaison`/authentication/login-callback`de rappel de connexion (), la réponse d’authentification est traitée.
  * Si le processus d’authentification se termine correctement, l’utilisateur est authentifié et éventuellement renvoyé à l’URL protégée d’origine que l’utilisateur a demandée.
  * Si le processus d’authentification échoue pour une raison quelconque, l’utilisateur est envoyé à la page échec`/authentication/login-failed`de la connexion () et une erreur s’affiche.

## <a name="additional-resources"></a>Ressources supplémentaires

* Les Articles de cette *vue d’ensemble* fournissent des informations Blazor sur l’authentification des utilisateurs dans les applications webassembly sur des fournisseurs spécifiques.
* <xref:security/blazor/webassembly/additional-scenarios>
