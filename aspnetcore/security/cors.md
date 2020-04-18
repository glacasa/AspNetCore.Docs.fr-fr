---
title: Activer les demandes d’origine croisée (CORS) dans ASP.NET Core
author: rick-anderson
description: Découvrez comment CORS en tant que norme pour autoriser ou rejeter les demandes d’origine croisée dans une application ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/17/2020
uid: security/cors
ms.openlocfilehash: 56a339d9018f619af38aecc6f4c2ff40c3c43d2f
ms.sourcegitcommit: 3d07e21868dafc503530ecae2cfa18a7490b58a6
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2020
ms.locfileid: "81642696"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a>Activer les demandes d’origine croisée (CORS) dans ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Par [Rick Anderson](https://twitter.com/RickAndMSFT) et Kirk [Larkin](https://twitter.com/serpent5)

Cet article montre comment activer CORS dans une application ASP.NET Core.

La sécurité du navigateur empêche une page Web de faire des demandes à un domaine différent de celui qui a servi la page Web. Cette restriction est appelée la *même politique d’origine*. La politique de même origine empêche un site malveillant de lire des données sensibles à partir d’un autre site. Parfois, vous pouvez autoriser d’autres sites à faire des demandes d’origine croisée à votre application. Pour plus d’informations, voir [l’article de Mozilla CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS).

[Partage des ressources d’origine croisée](https://www.w3.org/TR/cors/) (CORS) :

* Est une norme W3C qui permet à un serveur de détendre la même stratégie d’origine.
* N’est **pas** une fonction de sécurité, CORS détend la sécurité. Une API n’est pas plus sûre en permettant CORS. Pour plus d’informations, voir [Comment fonctionne CORS](#how-cors).
* Permet à un serveur d’autoriser explicitement certaines demandes d’origine croisée tout en rejetant d’autres.
* Est plus sûr et plus flexible que les techniques antérieures, telles que [JSONP](/dotnet/framework/wcf/samples/jsonp).

[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([comment télécharger](xref:index#how-to-download-a-sample))

## <a name="same-origin"></a>Même origine

Deux URL ont la même origine si elles ont des schémas identiques, hôtes et ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).

Ces deux URL ont la même origine :

* `https://example.com/foo.html`
* `https://example.com/bar.html`

Ces URL ont des origines différentes des deux URL précédentes :

* `https://example.net`&ndash; Domaine différent
* `https://www.example.com/foo.html`&ndash; Sous-comté différent
* `http://example.com/foo.html`&ndash; Différents schémas
* `https://example.com:9000/foo.html`&ndash; Port différent

## <a name="enable-cors"></a>Activez CORS

Il y a trois façons de permettre à CORS :

* Dans middleware en utilisant une [politique nommée](#np) ou la politique [par défaut](#dp).
* Utilisation [du routage de point de terminaison](#ecors).
* Avec l’attribut [[EnableCors].](#attr)

L’utilisation [de l’attribut [EnableCors]](#attr) avec une stratégie nommée fournit le meilleur contrôle en limitant les points de terminaison qui prennent en charge CORS.

Chaque approche est détaillée dans les sections suivantes.

<a name="np"></a>

## <a name="cors-with-named-policy-and-middleware"></a>CORS avec la politique nommée et middleware

CORS Middleware traite les demandes d’origine croisée. Le code suivant applique une stratégie CORS à tous les points de terminaison de l’application avec les origines spécifiées :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=3,9,31)]

Le code précédent :

* Définit le nom `_myAllowSpecificOrigins`de la politique à . Le nom de la politique est arbitraire.
* Appelle <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> la méthode d’extension `_myAllowSpecificOrigins` et spécifie la politique corS. `UseCors`ajoute le middleware CORS. L’appel `UseCors` doit être `UseRouting`placé `UseAuthorization`après, mais avant . Pour plus d’informations, voir [Middleware commande](xref:fundamentals/middleware/index#middleware-order).
* Appels <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> avec une [expression lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions). Le lambda <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> prend un objet. [Les options de configuration,](#cors-policy-options)telles que `WithOrigins`, sont décrites plus tard dans cet article.
* Permet `_myAllowSpecificOrigins` la politique CORS pour tous les points de terminaison du contrôleur. Voir [l’itinéraire de point de terminaison](#ecors) pour appliquer une stratégie CORS à des points de terminaison spécifiques.

Avec le routage de point de terminaison, le middleware `UseEndpoints`CORS ***doit*** être configuré pour exécuter entre les appels vers `UseRouting` et .

Consultez [Test CORS](#testc) pour les instructions sur le code de test similaire au code précédent.

L’appel <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> de méthode ajoute des services CORS au conteneur de service de l’application :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet2)]

Pour plus d’informations, consultez [les options de politique du CORS](#cpo) dans ce document.

Les <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> méthodes peuvent être enchaînées, comme le montre le code suivant :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup2.cs?name=snippet)]

Remarque : L’URL spécifiée **ne** `/`doit pas contenir une barre oblique de fuite (). Si l’URL `/`se termine `false` par , la comparaison revient et aucun en-tête n’est retourné.

<a name="dp"></a>

### <a name="cors-with-default-policy-and-middleware"></a>CORS avec la politique par défaut et middleware

Le code mis en évidence suivant permet la stratégie CORS par défaut :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupDefaultPolicy.cs?name=snippet2&highlight=7,29)]

Le code précédent applique la stratégie CORS par défaut à tous les points de terminaison du contrôleur.

<a name="ecors"></a>

## <a name="enable-cors-with-endpoint-routing"></a>Activez Les Cors avec le routage de point de terminaison

Permettre à CORS sur une `RequireCors` base par point de terminaison à l’aide de l’instant ne prend ***pas*** en charge les [demandes automatiques de prévol](#apf). Pour plus d’informations, voir [ce numéro GitHub](https://github.com/dotnet/aspnetcore/issues/20709) et [Test CORS avec itinéraire de point de terminaison et [HttpOptions]](#tcer).

Avec le routage de point de terminaison, CORS <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> peut être activé sur une base par point de terminaison à l’aide de l’ensemble des méthodes d’extension :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPt.cs?name=snippet2&highlight=3,7-15,32,40,43)]

Dans le code précédent :

* `app.UseCors`permet au middleware CORS. Parce qu’une stratégie par défaut `app.UseCors()` n’a pas été configurée, elle seule n’active pas CORS.
* Les `/echo` points de terminaison et les points de terminaison du contrôleur permettent des demandes d’origine croisée à l’aide de la stratégie spécifiée.
* Les `/echo2` points de terminaison et les pages Razor n’autorisent ***pas*** les demandes d’origine croisée parce qu’aucune stratégie de défaut n’a été spécifiée.

[L’attribut [DisableCors]](#dc) ne désactive ***pas*** corS qui `RequireCors`a été activé par l’itinéraire de point de terminaison avec .

Voir [Test CORS avec itinéraire de point de terminaison et [HttpOptions]](#tcer) pour les instructions sur le code de test similaire à la précédente.

<a name="attr"></a>

## <a name="enable-cors-with-attributes"></a>Activez CORS avec des attributs

Permettre à CORS [l’attribut [EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) et appliquer une stratégie nommée uniquement aux points de terminaison qui nécessitent corS fournit le meilleur contrôle.

[L’attribut [EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) offre une alternative à l’application de CORS à l’échelle mondiale. L’attribut `[EnableCors]` permet CORS pour certains points de terminaison, plutôt que tous les points de terminaison :

* `[EnableCors]`spécifie la stratégie par défaut.
* `[EnableCors("{Policy String}")]`spécifie une politique nommée.

L’attribut `[EnableCors]` peut être appliqué à :

* Razor Page`PageModel`
* Contrôleur
* Méthode d’action du contrôleur

Différentes stratégies peuvent être appliquées aux contrôleurs, `[EnableCors]` aux modèles de pages ou aux méthodes d’action avec l’attribut. Lorsque `[EnableCors]` l’attribut est appliqué à un contrôleur, un modèle de page ou une méthode d’action, et que corS est activé dans middleware, ***les deux*** stratégies sont appliquées. ***Nous vous recommandons de ne pas combiner les politiques. Utilisez l’attribut*** `[EnableCors]` ***ou middleware, pas les deux dans la même application.***

Le code suivant applique une stratégie différente à chaque méthode :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

Le code suivant crée deux stratégies CORS :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup3.cs?name=snippet&highlight=12-28,44)]

Pour le meilleur contrôle de limiter les demandes corS :

* Utiliser `[EnableCors("MyPolicy")]` avec une politique nommée.
* Ne définissez pas une stratégie par défaut.
* N’utilisez pas [le routage de point final](#ecors).

Le code de la section suivante répond à la liste précédente.

Consultez [Test CORS](#testc) pour les instructions sur le code de test similaire au code précédent.

<a name="dc"></a>

### <a name="disable-cors"></a>Désactiver le CORS

[L’attribut [DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) ne désactive ***pas*** le CORS qui a été activé par [l’itinéraire de point final](#ecors).

Le code suivant définit la `"MyPolicy"`politique corS :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTestMyPolicy.cs?name=snippet)]

Le code suivant désactive `GetValues2` CORS pour l’action :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet&highlight=1,23)]

Le code précédent :

* Ne permet pas CORS avec [itinéraire de point de terminaison](#ecors).
* Ne définit pas une [stratégie CORS par défaut](#dp).
* Utilise [[EnableCors("MyPolicy")]](#attr) pour `"MyPolicy"` activer la politique CORS pour le contrôleur.
* Désactive le CORS pour la `GetValues2` méthode.

Consultez [Test CORS](#testc) pour obtenir des instructions sur le test du code précédent.

<a name="cpo"></a>

## <a name="cors-policy-options"></a>Options de politique CORS

Cette section décrit les différentes options qui peuvent être définies dans une politique du SSC :

* [Définir les origines autorisées](#set-the-allowed-origins)
* [Définir les méthodes HTTP autorisées](#set-the-allowed-http-methods)
* [Définir les en-têtes de demande autorisés](#set-the-allowed-request-headers)
* [Définir les en-têtes de réponse exposés](#set-the-exposed-response-headers)
* [Informations d’identification dans les demandes d’origine croisée](#credentials-in-cross-origin-requests)
* [Définissez l’heure d’expiration avant le vol](#set-the-preflight-expiration-time)

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>est appelé `Startup.ConfigureServices`dans . Pour certaines options, il peut être utile de lire la section [Comment CORS fonctionne](#how-cors) en premier.

## <a name="set-the-allowed-origins"></a>Définir les origines autorisées

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>&ndash; Permet les demandes CORS de`http` toutes `https`origines avec n’importe quel régime ( ou ). `AllowAnyOrigin`n’est pas sûr parce que *n’importe quel site Web* peut faire des demandes d’origine croisée à l’application.

> [!NOTE]
> Spécifier `AllowAnyOrigin` et `AllowCredentials` est une configuration non sécurisée et peut entraîner des faux de demande de site transversal. Le service CORS renvoie une réponse CORS invalide lorsqu’une application est configurée avec les deux méthodes.

`AllowAnyOrigin`affecte les demandes de `Access-Control-Allow-Origin` prévol et l’en-tête. Pour plus d’informations, consultez la section [des demandes de prévol.](#preflight-requests)

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>&ndash; Définit <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> la propriété de la stratégie pour être une fonction qui permet aux origines de correspondre à un domaine de wildcard configuré lors de l’évaluation si l’origine est autorisée.

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet)]

### <a name="set-the-allowed-http-methods"></a>Définir les méthodes HTTP autorisées

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:

* Permet n’importe quelle méthode HTTP :
* Affecte les demandes de `Access-Control-Allow-Methods` prévol et l’en-tête. Pour plus d’informations, consultez la section [des demandes de prévol.](#preflight-requests)

### <a name="set-the-allowed-request-headers"></a>Définir les en-têtes de demande autorisés

Pour permettre l’envoi d’en-têtes spécifiques dans une demande <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> CORS, appelé auteur demande [en-têtes](https://xhr.spec.whatwg.org/#request), appelez et spécifier les en-têtes autorisés:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

Pour permettre à tous les <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> [auteurs de demander des en-têtes](https://www.w3.org/TR/cors/#author-request-headers), appelez :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

`AllowAnyHeader`affecte les demandes de prévol et l’en-tête [Access-Control-Request-Headers.](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) Pour plus d’informations, consultez la section [des demandes de prévol.](#preflight-requests)

Un match de politique CORS Middleware `WithHeaders` à des en-têtes `Access-Control-Request-Headers` spécifiques spécifiés par `WithHeaders`n’est possible que lorsque les en-têtes envoyés exactement correspondre les en-têtes indiqués dans .

Par exemple, considérez une application configurée comme suit :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet4)]

CORS Middleware décline une demande de prévol `Content-Language` avec l’en-tête de demande suivant `WithHeaders`parce que ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) n’est pas répertorié dans :

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

L’application renvoie une réponse *200 OK,* mais ne renvoie pas les en-têtes CORS. Par conséquent, le navigateur ne tente pas la demande d’origine croisée.

### <a name="set-the-exposed-response-headers"></a>Définir les en-têtes de réponse exposés

Par défaut, le navigateur n’expose pas tous les en-têtes de réponse à l’application. Pour plus d’informations, voir [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).

Les en-têtes de réponse qui sont disponibles par défaut sont les :

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

La spécification CORS appelle ces en-têtes *simples en-têtes*de réponse . Pour mettre d’autres en-têtes <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>à la disposition de l’application, appelez :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet5)]
### <a name="credentials-in-cross-origin-requests"></a>Informations d’identification dans les demandes d’origine croisée

Les informations d’identification nécessitent une manipulation spéciale dans une demande de SSR. Par défaut, le navigateur n’envoie pas d’informations d’identification avec une demande d’origine croisée. Les informations d’identification comprennent les cookies et les systèmes d’authentification HTTP. Pour envoyer des informations d’identification avec une `XMLHttpRequest.withCredentials` `true`demande d’origine croisée, le client doit s’y mettre.

Utilisation `XMLHttpRequest` directe :

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

Utilisation de jQuery:

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

Utilisation de [l’API Fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API):

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

Le serveur doit autoriser les informations d’identification. Pour permettre les informations d’identification d’origine croisée, appelez <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet6)]

La réponse HTTP `Access-Control-Allow-Credentials` comprend un en-tête, qui indique au navigateur que le serveur permet des informations d’identification pour une demande d’origine croisée.

Si le navigateur envoie des informations d’identification `Access-Control-Allow-Credentials` mais que la réponse n’inclut pas d’en-tête valide, le navigateur n’expose pas la réponse à l’application, et la demande d’origine croisée échoue.

Autoriser les informations d’identification inter-origine est un risque pour la sécurité. Un site Web d’un autre domaine peut envoyer les informations d’identification d’un utilisateur connecté à l’application au nom de l’utilisateur à l’insu de l’utilisateur. <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

La spécification CORS indique également `"*"` que l’établissement des `Access-Control-Allow-Credentials` origines (toutes origines) est invalide si l’en-tête est présent.

<a name="pref"></a>

## <a name="preflight-requests"></a>Demandes de prévol

Pour certaines demandes CORS, le navigateur envoie une demande [options](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) supplémentaire avant de faire la demande réelle. Cette demande est appelée une [demande de prévol](https://developer.mozilla.org/docs/Glossary/Preflight_request). Le navigateur peut sauter la demande de prévol si ***toutes les*** conditions suivantes sont vraies:

* La méthode de demande est GET, HEAD ou POST.
* L’application ne fixe pas les `Accept` `Accept-Language`en-têtes de demande autres que , , `Content-Language`, `Content-Type`, ou `Last-Event-ID`.
* L’en-tête, `Content-Type` s’il est défini, a l’une des valeurs suivantes :
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

La règle sur les en-têtes de demande définie pour la `setRequestHeader` demande `XMLHttpRequest` du client s’applique aux en-têtes que l’application définit en appelant l’objet. La spécification CORS appelle ces en-têtes [auteur demande en-têtes](https://www.w3.org/TR/cors/#author-request-headers). La règle ne s’applique pas aux en-têtes `Host`que `Content-Length`le navigateur peut définir, tels que `User-Agent`, , ou .

Ce qui suit est une réponse d’exemple similaire à la demande de prévol faite à partir du bouton **[Mettre le test]** dans la section [Test CORS](#testc) de ce document.

```
General:
Request URL: https://cors3.azurewebsites.net/api/values/5
Request Method: OPTIONS
Status Code: 204 No Content

Response Headers:
Access-Control-Allow-Methods: PUT,DELETE,GET
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f8...8;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Vary: Origin

Request Headers:
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Access-Control-Request-Method: PUT
Connection: keep-alive
Host: cors3.azurewebsites.net
Origin: https://cors1.azurewebsites.net
Referer: https://cors1.azurewebsites.net/
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0
```

La demande de prévol utilise la méthode [HTTP OPTIONS.](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) Il peut inclure les en-têtes suivants:

* [Access-Control-Request-Méthode](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): Méthode HTTP qui sera utilisée pour la demande réelle.
* [Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): Liste des en-têtes de demande que l’application fixe sur la demande réelle. Comme indiqué précédemment, cela n’inclut pas les en-têtes que le navigateur définit, tels que `User-Agent`.
* [Accès-Contrôle-Autoriser-Méthodes](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Methods)

Si la demande de prévol est refusée, l’application renvoie une `200 OK` réponse mais ne résout pas les en-têtes CORS. Par conséquent, le navigateur ne tente pas la demande d’origine croisée. Par exemple, consultez la section [Test CORS](#testc) de ce document.

À l’aide des outils F12, l’application console affiche une erreur similaire à celle de ce qui suit, selon le navigateur :

* Firefox: Cross-Origin Request Blocked: The Same Origin Policy refuse `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`la lecture de la ressource à distance à . (Raison : La demande de CORS n’a pas été accueillie). [En savoir plus](https://developer.mozilla.org/docs/Web/HTTP/CORS/Errors/CORSDidNotSucceed)
* Basé sur le chrome :https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5L’accèshttps://cors3.azurewebsites.netà aller chercher à ' d’origine ' a été bloqué par la politique de CORS : la réponse à la demande de prévol ne passe pas le contrôle d’accès : aucun en-tête ' Access-Control-Allow-Origin' n’est présent sur la ressource demandée. Si une réponse opaque répond à vos besoins, définissez le mode de la requête sur « no-cors » pour extraire la ressource avec CORS désactivé.

Pour permettre des en-têtes spécifiques, appelez <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

Pour permettre à tous les <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> [auteurs de demander des en-têtes](https://www.w3.org/TR/cors/#author-request-headers), appelez :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

Les navigateurs ne sont pas `Access-Control-Request-Headers`cohérents dans la façon dont ils se définissent . Si l’un ou l’autre:

* Les en-têtes sont réglés à autre chose que`"*"`
* <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>est appelé: Inclure `Accept` `Content-Type`au `Origin`moins , et , plus tous les en-têtes personnalisés que vous voulez prendre en charge.

<a name="apf"></a>

### <a name="automatic-preflight-request-code"></a>Code automatique de demande de prévol

Lorsque la politique corS est appliquée soit :

* Globalement `app.UseCors` en `Startup.Configure`appelant dans .
* Utilisation `[EnableCors]` de l’attribut.

ASP.NET Core répond à la demande options avant le vol.

L’activation de LA COR sur `RequireCors` une base par point de terminaison à l’aide de l’instant ne prend ***pas*** en charge les demandes automatiques de prévol.

La section [Test CORS](#testc) de ce document démontre ce comportement.

<a name="pro"></a>

### <a name="httpoptions-attribute-for-preflight-requests"></a>[HttpOptions] attribut pour les demandes de prévol

Lorsque corS est activé avec la stratégie appropriée, ASP.NET Core répond généralement automatiquement aux demandes de vol prévol de CORS. Dans certains scénarios, ce n’est peut-être pas le cas. Par exemple, utiliser [CORS avec itinéraire de point de terminaison](#ecors).

Le code suivant utilise l’attribut [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) pour créer des paramètres pour les demandes OPTIONS :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet&highlight=5-17)]

Voir [Test CORS avec itinéraire de point de terminaison et [HttpOptions]](#tcer) pour les instructions sur le test du code précédent.

### <a name="set-the-preflight-expiration-time"></a>Définissez l’heure d’expiration avant le vol

L’en-tête `Access-Control-Max-Age` précise combien de temps la réponse à la demande de prévol peut être mise en cache. Pour définir cet <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>en-tête, appelez :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet7)]
<a name="how-cors"></a>

## <a name="how-cors-works"></a>Fonctionnement du CORS

Cette section décrit ce qui se passe dans une demande [corS](https://developer.mozilla.org/docs/Web/HTTP/CORS) au niveau des messages HTTP.

* CORS **n’est pas** une fonction de sécurité. CORS est une norme W3C qui permet à un serveur d’assouplir la même stratégie d’origine.
  * Par exemple, un acteur malveillant peut utiliser [le script cross-Site (XSS)](xref:security/cross-site-scripting) contre votre site et exécuter une demande de site transversal à son site compatible CORS pour voler des informations.
* Une API n’est pas plus sûre en permettant CORS.
  * C’est au client (navigateur) d’appliquer CORS. Le serveur exécute la demande et renvoie la réponse, c’est le client qui renvoie une erreur et bloque la réponse. Par exemple, l’un des outils suivants affichera la réponse du serveur :
    * [Fiddler](https://www.telerik.com/fiddler)
    * [Postman](https://www.getpostman.com/)
    * [.NET HttpClient (en)](/dotnet/csharp/tutorials/console-webapiclient)
    * Un navigateur Web en entrant l’URL dans la barre d’adresse.
* C’est un moyen pour un serveur de permettre aux navigateurs d’exécuter une demande [d’AHR d’origine](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) croisée ou [d’API fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API) qui autrement serait interdite.
  * Les navigateurs sans CORS ne peuvent pas faire de demandes d’origine croisée. Avant CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) a été utilisé pour contourner cette restriction. JSONP n’utilise pas XHR, `<script>` il utilise l’étiquette pour recevoir la réponse. Les scripts sont autorisés à être chargés à l’origine croisée.

Les [spécifications CORS](https://www.w3.org/TR/cors/) ont introduit plusieurs nouveaux en-têtes HTTP qui permettent des demandes d’origine croisée. Si un navigateur prend en charge CORS, il définit automatiquement ces en-têtes pour les demandes d’origine croisée. Le code JavaScript personnalisé n’est pas nécessaire pour activer CORS.

Le [bouton de test PUT](https://cors3.azurewebsites.net/test) sur l’échantillon déployé [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)

Ce qui suit est un exemple d’une demande `https://cors1.azurewebsites.net/api/values`d’origine croisée du bouton de test [Valeurs](https://cors3.azurewebsites.net/) à . L’en-tête: `Origin`

* Fournit le domaine du site qui fait la demande.
* Est nécessaire et doit être différent de l’hôte.

**En-têtes généraux**

```
Request URL: https://cors1.azurewebsites.net/api/values
Request Method: GET
Status Code: 200 OK
```

**En-têtes de réponse**

```
Content-Encoding: gzip
Content-Type: text/plain; charset=utf-8
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Transfer-Encoding: chunked
Vary: Accept-Encoding
X-Powered-By: ASP.NET
```

**En-têtes de requête**

```
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Connection: keep-alive
Host: cors1.azurewebsites.net
Origin: https://cors3.azurewebsites.net
Referer: https://cors3.azurewebsites.net/
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0 ...
```

Dans `OPTIONS` les demandes, le serveur définit `Access-Control-Allow-Origin: {allowed origin}` l’en-tête **de réponse** dans la réponse. Par exemple, [l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)déployé, Supprimer `OPTIONS` la demande de bouton [[EnableCors]](https://cors1.azurewebsites.net/test?number=2) contient les en-têtes suivants :

**En-têtes généraux**

```
Request URL: https://cors3.azurewebsites.net/api/TodoItems2/MyDelete2/5
Request Method: OPTIONS
Status Code: 204 No Content
```

**En-têtes de réponse**

```
Access-Control-Allow-Headers: Content-Type,x-custom-header
Access-Control-Allow-Methods: PUT,DELETE,GET,OPTIONS
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors3.azurewebsites.net
Vary: Origin
X-Powered-By: ASP.NET
```

**En-têtes de requête**

```
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Access-Control-Request-Headers: content-type
Access-Control-Request-Method: DELETE
Connection: keep-alive
Host: cors3.azurewebsites.net
Origin: https://cors1.azurewebsites.net
Referer: https://cors1.azurewebsites.net/test?number=2
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0
```

Dans les **en-têtes de réponse**précédents, le serveur définit l’en-tête [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) dans la réponse. La `https://cors1.azurewebsites.net` valeur de cet `Origin` en-tête correspond à l’en-tête de la demande.

Si <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> on appelle, la `Access-Control-Allow-Origin: *`valeur , la wildcard, est retournée. `AllowAnyOrigin`permet n’importe quelle origine.

Si la réponse n’inclut pas l’en-tête, `Access-Control-Allow-Origin` la demande d’origine croisée échoue. Plus précisément, le navigateur interdit la demande. Même si le serveur renvoie une réponse réussie, le navigateur ne met pas la réponse à la disposition de l’application client.

<a name="options"></a>

### <a name="display-options-requests"></a>Afficher les demandes OPTIONS

Par défaut, les navigateurs Chrome et Edge n’affichent pas les demandes OPTIONS sur l’onglet réseau des outils F12. Pour afficher les demandes OPTIONS dans ces navigateurs :

* `chrome://flags/#out-of-blink-cors` ou `edge://flags/#out-of-blink-cors`
* désactiver le drapeau.
* redémarrer.

Firefox affiche les demandes OPTIONS par défaut.

## <a name="cors-in-iis"></a>CORS en IIS

Lors du déploiement vers IIS, CORS doit s’exécuter avant l’authentification de Windows si le serveur n’est pas configuré pour permettre un accès anonyme. Pour soutenir ce scénario, le [module COS IIS](https://www.iis.net/downloads/microsoft/iis-cors-module) doit être installé et configuré pour l’application.

<a name="testc"></a>

## <a name="test-cors"></a>Tester CORS

[L’exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) a du code pour tester CORS. Consultez [Guide pratique pour télécharger](xref:index#how-to-download-a-sample). L’échantillon est un projet API avec Razor Pages ajouté:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTest2.cs?name=snippet2)]

  > [!WARNING]
  > `WithOrigins("https://localhost:<port>");`ne doit être utilisé que pour tester un exemple d’application similaire au [code d’échantillon de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors).

Ce `ValuesController` qui suit fournit les paramètres de test :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet)]

[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) est fourni par le forfait [Rick.Docs.Samples.RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet et affiche les informations d’itinéraire.

Testez le code d’échantillon précédent en utilisant l’une des approches suivantes :

* Utilisez l’application [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/)d’échantillon déployée à . Il n’est pas nécessaire de télécharger l’échantillon.
* Exécutez l’échantillon avec `dotnet run` `https://localhost:5001`l’utilisation de l’URL par défaut de .
* Exécuter l’échantillon de Visual Studio avec le port réglé à `https://localhost:44398`44398 pour une URL de .

Utilisation d’un navigateur avec les outils F12 :

* Sélectionnez le bouton **Valeurs** et examinez les en-têtes de l’onglet **Réseau.**
* Sélectionnez le bouton **de test PUT.** Voir [afficher les demandes d’instructions options](#options) sur l’affichage de la demande OPTIONS. Le **test PUT** crée deux demandes, une demande de prévol OPTIONS et la demande PUT.
* Sélectionnez **`GetValues2 [DisableCors]`** le bouton pour déclencher une demande CORS ratée. Comme mentionné dans le document, la réponse renvoie 200 succès, mais la demande corS n’est pas faite. Sélectionnez l’onglet **Console** pour voir l’erreur CORS. Selon le navigateur, une erreur similaire à ce qui suit s’affiche :

     L’accès `'https://cors1.azurewebsites.net/api/values/GetValues2'` à `'https://cors3.azurewebsites.net'` aller chercher à partir de l’origine a été bloqué par la politique du CORS : aucun en-tête « Access-Control-Allow-Origin » n’est présent sur la ressource demandée. Si une réponse opaque répond à vos besoins, définissez le mode de la requête sur « no-cors » pour extraire la ressource avec CORS désactivé.
     
Les points de terminaison compatibles CORS peuvent être testés avec un outil, comme [curl](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler), ou [Postman](https://www.getpostman.com/). Lors de l’utilisation d’un outil, l’origine de la demande spécifiée par l’en-tête `Origin` doit différer de l’hôte recevant la demande. Si la demande n’est pas *transversale* en `Origin` fonction de la valeur de l’en-tête :

* Il n’est pas nécessaire pour CORS Middleware de traiter la demande.
* Les en-têtes corS ne sont pas retournés dans la réponse.

La commande `curl` suivante utilise pour émettre une demande OPTIONS avec des informations :

```bash
curl -X OPTIONS https://cors3.azurewebsites.net/api/TodoItems2/5 -i
```

<!--
curl come with Git. Add to path variable
C:\Program Files\Git\mingw64\bin\
-->

<a name="tcer"></a>

### <a name="test-cors-with-endpoint-routing-and-httpoptions"></a>Test CORS avec itinéraire de point de terminaison et [HttpOptions]

Permettre à CORS sur une `RequireCors` base par point de terminaison à l’aide de l’instant ne prend ***pas*** en charge les [demandes automatiques de prévol](#apf). Considérez le code suivant qui utilise [le routage de point final pour activer CORS](#ecors):

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPointBugTest.cs?name=snippet2)]

Ce `TodoItems1Controller` qui suit fournit des paramètres de test :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems1Controller.cs?name=snippet2)]

Testez le code précédent à partir de la page de [test](https://cors1.azurewebsites.net/test?number=1) de [l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)déployé .

Les boutons **Supprimer [EnableCors]** et **GET [EnableCors]** réussissent, `[EnableCors]` parce que les critères d’évaluation ont et répondent aux demandes avant le vol. Les autres critères d’évaluation échouent. Le bouton **GET** échoue, car le [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) envoie :

```javascript
 headers: {
      "Content-Type": "x-custom-header"
 },
```

Ce `TodoItems2Controller` qui suit fournit des points de terminaison similaires, mais comprend un code explicite pour répondre aux demandes OPTIONS :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet2)]

Testez le code précédent à partir de la page de [test](https://cors1.azurewebsites.net/test?number=2) de l’échantillon déployé. Dans la liste **d’abandon du contrôleur,** sélectionnez **Preflight,** puis **Set Controller**. Tous les appels corS aux points `TodoItems2Controller` de terminaison réussissent.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Partage des ressources inter-origine (CORS)](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [Commencer avec le module CORS DE l’IIS](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Par [Rick Anderson](https://twitter.com/RickAndMSFT)

Cet article montre comment activer CORS dans une application ASP.NET Core.

La sécurité du navigateur empêche une page Web de faire des demandes à un domaine différent de celui qui a servi la page Web. Cette restriction est appelée la *même politique d’origine*. La politique de même origine empêche un site malveillant de lire des données sensibles à partir d’un autre site. Parfois, vous pouvez autoriser d’autres sites à faire des demandes d’origine croisée à votre application. Pour plus d’informations, voir [l’article de Mozilla CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS).

[Partage des ressources d’origine croisée](https://www.w3.org/TR/cors/) (CORS) :

* Est une norme W3C qui permet à un serveur de détendre la même stratégie d’origine.
* N’est **pas** une fonction de sécurité, CORS détend la sécurité. Une API n’est pas plus sûre en permettant CORS. Pour plus d’informations, voir [Comment fonctionne CORS](#how-cors).
* Permet à un serveur d’autoriser explicitement certaines demandes d’origine croisée tout en rejetant d’autres.
* Est plus sûr et plus flexible que les techniques antérieures, telles que [JSONP](/dotnet/framework/wcf/samples/jsonp).

[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([comment télécharger](xref:index#how-to-download-a-sample))

## <a name="same-origin"></a>Même origine

Deux URL ont la même origine si elles ont des schémas identiques, hôtes et ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).

Ces deux URL ont la même origine :

* `https://example.com/foo.html`
* `https://example.com/bar.html`

Ces URL ont des origines différentes des deux URL précédentes :

* `https://example.net`&ndash; Domaine différent
* `https://www.example.com/foo.html`&ndash; Sous-comté différent
* `http://example.com/foo.html`&ndash; Différents schémas
* `https://example.com:9000/foo.html`&ndash; Port différent

Internet Explorer ne considère pas le port en comparant les origines.

## <a name="cors-with-named-policy-and-middleware"></a>CORS avec la politique nommée et middleware

CORS Middleware traite les demandes d’origine croisée. Le code suivant permet CORS pour l’ensemble de l’application avec l’origine spécifiée:

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

Le code précédent :

* Définit le nom\_de la politique à "myAllowSpecificOrigins". Le nom de la politique est arbitraire.
* Appelle <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> la méthode d’extension, qui permet CORS.
* Appels <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> avec une [expression lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions). Le lambda <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> prend un objet. [Les options de configuration,](#cors-policy-options)telles que `WithOrigins`, sont décrites plus tard dans cet article.

L’appel <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> de méthode ajoute des services CORS au conteneur de service de l’application :

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

Pour plus d’informations, voir [les options de politique CORS](#cpo) dans ce document .

La <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> méthode peut enchaîner les méthodes, comme le montre le code suivant :

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

Remarque : L’URL **ne** doit`/`pas contenir de barre oblique de fuite (). Si l’URL `/`se termine `false` par , la comparaison revient et aucun en-tête n’est retourné.

Le code suivant applique les stratégies CORS à tous les points de terminaison des applications via CORS Middleware :
```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseHsts();
    }

    app.UseCors();

    app.UseHttpsRedirection();
    app.UseMvc();
}
```
Remarque `UseCors` : doit `UseMvc`être appelée avant .

Voir [Activer CORS dans Razor Pages, contrôleurs et méthodes d’action](#ecors) pour appliquer la stratégie CORS au niveau de la page/contrôleur/action.

Consultez [Test CORS](#test) pour les instructions sur le code de test similaire au code précédent.

## <a name="enable-cors-with-attributes"></a>Activez CORS avec des attributs

L’attribut [ &lbrack;&rbrack; EnableCors](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) offre une alternative à l’application de CORS à l’échelle mondiale. L’attribut `[EnableCors]` permet CORS pour certains points de fin, plutôt que tous les points finaux.

Utilisez `[EnableCors]` pour spécifier la stratégie par défaut et `[EnableCors("{Policy String}")]` pour spécifier une stratégie.

L’attribut `[EnableCors]` peut être appliqué à :

* Razor Page`PageModel`
* Contrôleur
* Méthode d’action du contrôleur

Vous pouvez appliquer différentes stratégies au contrôleur/page-modèle/action avec l’attribut. `[EnableCors]` Lorsque `[EnableCors]` l’attribut est appliqué à un modèle/méthode d’action de contrôleurs/pages, et que le CORS est activé dans middleware, ***les deux*** stratégies sont appliquées. Nous recommandons ***de ne pas*** combiner les politiques. Utilisez `[EnableCors]` l’attribut ou middleware,**pas les deux**. Lors `[EnableCors]`de l’utilisation, ne définissez **pas** une stratégie par défaut.

Le code suivant applique une stratégie différente à chaque méthode :

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

Le code suivant crée une politique de `"AnotherPolicy"`défaut corS et une stratégie nommée :

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a>Désactiver le CORS

[ &lbrack;L’attribut&rbrack; DisableCors](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) désactive CORS pour le contrôleur/page-modèle/action.

<a name="cpo"></a>

## <a name="cors-policy-options"></a>Options de politique CORS

Cette section décrit les différentes options qui peuvent être définies dans une politique du SSC :

* [Définir les origines autorisées](#set-the-allowed-origins)
* [Définir les méthodes HTTP autorisées](#set-the-allowed-http-methods)
* [Définir les en-têtes de demande autorisés](#set-the-allowed-request-headers)
* [Définir les en-têtes de réponse exposés](#set-the-exposed-response-headers)
* [Informations d’identification dans les demandes d’origine croisée](#credentials-in-cross-origin-requests)
* [Définissez l’heure d’expiration avant le vol](#set-the-preflight-expiration-time)

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>est appelé `Startup.ConfigureServices`dans . Pour certaines options, il peut être utile de lire la section [Comment CORS fonctionne](#how-cors) en premier.

## <a name="set-the-allowed-origins"></a>Définir les origines autorisées

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>&ndash; Permet les demandes CORS de`http` toutes `https`origines avec n’importe quel régime ( ou ). `AllowAnyOrigin`n’est pas sûr parce que *n’importe quel site Web* peut faire des demandes d’origine croisée à l’application.

> [!NOTE]
> Spécifier `AllowAnyOrigin` et `AllowCredentials` est une configuration non sécurisée et peut entraîner des faux de demande de site transversal. Pour une application sécurisée, spécifiez une liste exacte des origines si le client doit s’autoriser à accéder aux ressources du serveur.

`AllowAnyOrigin`affecte les demandes de `Access-Control-Allow-Origin` prévol et l’en-tête. Pour plus d’informations, consultez la section [des demandes de prévol.](#preflight-requests)

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>&ndash; Définit <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> la propriété de la stratégie pour être une fonction qui permet aux origines de correspondre à un domaine de wildcard configuré lors de l’évaluation si l’origine est autorisée.

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

### <a name="set-the-allowed-http-methods"></a>Définir les méthodes HTTP autorisées

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:

* Permet n’importe quelle méthode HTTP :
* Affecte les demandes de `Access-Control-Allow-Methods` prévol et l’en-tête. Pour plus d’informations, consultez la section [des demandes de prévol.](#preflight-requests)

### <a name="set-the-allowed-request-headers"></a>Définir les en-têtes de demande autorisés

Pour permettre l’envoi d’en-têtes spécifiques dans une demande <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> CORS, appelé auteur demande *en-têtes*, appelez et spécifier les en-têtes autorisés:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

Pour permettre à tous les <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>auteurs de demander des en-têtes, appelez :

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

Ce paramètre affecte les `Access-Control-Request-Headers` demandes de prévol et l’en-tête. Pour plus d’informations, consultez la section [des demandes de prévol.](#preflight-requests)

CORS Middleware permet toujours d’envoyer quatre en-têtes dans le `Access-Control-Request-Headers` code indépendamment des valeurs configurées dans CorsPolicy.Headers. Cette liste d’en-têtes comprend :

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

Par exemple, considérez une application configurée comme suit :

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

CORS Middleware répond avec succès à une demande de `Content-Language` prévol avec l’en-tête de demande suivant parce qu’il est toujours sur la liste blanche :

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

### <a name="set-the-exposed-response-headers"></a>Définir les en-têtes de réponse exposés

Par défaut, le navigateur n’expose pas tous les en-têtes de réponse à l’application. Pour plus d’informations, voir [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).

Les en-têtes de réponse qui sont disponibles par défaut sont les :

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

La spécification CORS appelle ces en-têtes *simples en-têtes*de réponse . Pour mettre d’autres en-têtes <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>à la disposition de l’application, appelez :

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a>Informations d’identification dans les demandes d’origine croisée

Les informations d’identification nécessitent une manipulation spéciale dans une demande de SSR. Par défaut, le navigateur n’envoie pas d’informations d’identification avec une demande d’origine croisée. Les informations d’identification comprennent les cookies et les systèmes d’authentification HTTP. Pour envoyer des informations d’identification avec une `XMLHttpRequest.withCredentials` `true`demande d’origine croisée, le client doit s’y mettre.

Utilisation `XMLHttpRequest` directe :

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

Utilisation de jQuery:

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

Utilisation de [l’API Fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API):

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

Le serveur doit autoriser les informations d’identification. Pour permettre les informations d’identification d’origine croisée, appelez <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

La réponse HTTP `Access-Control-Allow-Credentials` comprend un en-tête, qui indique au navigateur que le serveur permet des informations d’identification pour une demande d’origine croisée.

Si le navigateur envoie des informations d’identification `Access-Control-Allow-Credentials` mais que la réponse n’inclut pas d’en-tête valide, le navigateur n’expose pas la réponse à l’application, et la demande d’origine croisée échoue.

Autoriser les informations d’identification inter-origine est un risque pour la sécurité. Un site Web d’un autre domaine peut envoyer les informations d’identification d’un utilisateur connecté à l’application au nom de l’utilisateur à l’insu de l’utilisateur. <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

La spécification CORS indique également `"*"` que l’établissement des `Access-Control-Allow-Credentials` origines (toutes origines) est invalide si l’en-tête est présent.

### <a name="preflight-requests"></a>Demandes de prévol

Pour certaines demandes CORS, le navigateur envoie une demande supplémentaire avant de faire la demande réelle. Cette demande est appelée une *demande de prévol*. Le navigateur peut sauter la demande de prévol si les conditions suivantes sont vraies :

* La méthode de demande est GET, HEAD ou POST.
* L’application ne fixe pas les `Accept` `Accept-Language`en-têtes de demande autres que , , `Content-Language`, `Content-Type`, ou `Last-Event-ID`.
* L’en-tête, `Content-Type` s’il est défini, a l’une des valeurs suivantes :
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

La règle sur les en-têtes de demande définie pour la `setRequestHeader` demande `XMLHttpRequest` du client s’applique aux en-têtes que l’application définit en appelant l’objet. La spécification CORS appelle ces en-têtes *auteur demande en-têtes*. La règle ne s’applique pas aux en-têtes `Host`que `Content-Length`le navigateur peut définir, tels que `User-Agent`, , ou .

Voici un exemple de demande de prévol :

```
OPTIONS https://myservice.azurewebsites.net/api/test HTTP/1.1
Accept: */*
Origin: https://myclient.azurewebsites.net
Access-Control-Request-Method: PUT
Access-Control-Request-Headers: accept, x-my-custom-header
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
Content-Length: 0
```

La demande avant vol utilise la méthode HTTP OPTIONS. Il comprend deux en-têtes spéciaux :

* `Access-Control-Request-Method`: La méthode HTTP qui sera utilisée pour la demande réelle.
* `Access-Control-Request-Headers`: Une liste d’en-têtes de demande que l’application fixe sur la demande réelle. Comme indiqué précédemment, cela n’inclut pas les en-têtes que le navigateur définit, tels que `User-Agent`.

<!-- I think this needs to be removed, was put here accidently -->

Lorsque corS est activé avec la stratégie appropriée, ASP.NET Core répond généralement automatiquement aux demandes de vol prévol de CORS. Voir [[HttpOptions] attribut pour les demandes de prévol](#pro).

Une demande de prévol corS peut inclure un `Access-Control-Request-Headers` en-tête, qui indique au serveur les en-têtes qui sont envoyés avec la demande réelle.

Pour permettre des en-têtes spécifiques, appelez <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

Pour permettre à tous les <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>auteurs de demander des en-têtes, appelez :

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

Les navigateurs ne sont pas `Access-Control-Request-Headers`tout à fait cohérents dans la façon dont ils se définissent . Si vous définissez des `"*"` en-têtes <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>à autre chose `Accept` `Content-Type`que `Origin`(ou utiliser ), vous devriez inclure au moins , et , plus tous les en-têtes personnalisés que vous voulez prendre en charge.

Ce qui suit est une réponse exemple à la demande de prévol (en supposant que le serveur permet la demande):

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 0
Access-Control-Allow-Origin: https://myclient.azurewebsites.net
Access-Control-Allow-Headers: x-my-custom-header
Access-Control-Allow-Methods: PUT
Date: Wed, 20 May 2015 06:33:22 GMT
```

La réponse `Access-Control-Allow-Methods` comprend un en-tête qui `Access-Control-Allow-Headers` répertorie les méthodes autorisées et optionnellement un en-tête, qui répertorie les en-têtes autorisés. Si la demande de prévol réussit, le navigateur envoie la demande réelle.

Si la demande de prévol est refusée, l’application renvoie une réponse *200 OK,* mais ne renvoie pas les en-têtes CORS. Par conséquent, le navigateur ne tente pas la demande d’origine croisée.

### <a name="set-the-preflight-expiration-time"></a>Définissez l’heure d’expiration avant le vol

L’en-tête `Access-Control-Max-Age` précise combien de temps la réponse à la demande de prévol peut être mise en cache. Pour définir cet <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>en-tête, appelez :

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a>Fonctionnement du CORS

Cette section décrit ce qui se passe dans une demande [corS](https://developer.mozilla.org/docs/Web/HTTP/CORS) au niveau des messages HTTP.

* CORS **n’est pas** une fonction de sécurité. CORS est une norme W3C qui permet à un serveur d’assouplir la même stratégie d’origine.
  * Par exemple, un acteur malveillant peut utiliser [Prevent Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) contre votre site et exécuter une demande de site transversal à son site compatible CORS pour voler des informations.
* Votre API n’est pas plus sûr en permettant CORS.
  * C’est au client (navigateur) d’appliquer CORS. Le serveur exécute la demande et renvoie la réponse, c’est le client qui renvoie une erreur et bloque la réponse. Par exemple, l’un des outils suivants affichera la réponse du serveur :
    * [Fiddler](https://www.telerik.com/fiddler)
    * [Postman](https://www.getpostman.com/)
    * [.NET HttpClient (en)](/dotnet/csharp/tutorials/console-webapiclient)
    * Un navigateur Web en entrant l’URL dans la barre d’adresse.
* C’est un moyen pour un serveur de permettre aux navigateurs d’exécuter une demande [d’AHR d’origine](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) croisée ou [d’API fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API) qui autrement serait interdite.
  * Les navigateurs (sans CORS) ne peuvent pas faire de demandes d’origine croisée. Avant CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) a été utilisé pour contourner cette restriction. JSONP n’utilise pas XHR, `<script>` il utilise l’étiquette pour recevoir la réponse. Les scripts sont autorisés à être chargés à l’origine croisée.

Les [spécifications CORS](https://www.w3.org/TR/cors/) ont introduit plusieurs nouveaux en-têtes HTTP qui permettent des demandes d’origine croisée. Si un navigateur prend en charge CORS, il définit automatiquement ces en-têtes pour les demandes d’origine croisée. Le code JavaScript personnalisé n’est pas nécessaire pour activer CORS.

Ce qui suit est un exemple d’une demande d’origine croisée. L’en-tête `Origin` fournit le domaine du site qui fait la demande. L’en-tête `Origin` est nécessaire et doit être différent de l’hôte.

```
GET https://myservice.azurewebsites.net/api/test HTTP/1.1
Referer: https://myclient.azurewebsites.net/
Accept: */*
Accept-Language: en-US
Origin: https://myclient.azurewebsites.net
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
```

Si le serveur autorise la `Access-Control-Allow-Origin` demande, il définit l’en-tête dans la réponse. La valeur de cet `Origin` en-tête correspond soit à `"*"`l’en-tête de la demande ou est la valeur wildcard , ce qui signifie que toute origine est autorisée:

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Type: text/plain; charset=utf-8
Access-Control-Allow-Origin: https://myclient.azurewebsites.net
Date: Wed, 20 May 2015 06:27:30 GMT
Content-Length: 12

Test message
```

Si la réponse n’inclut pas l’en-tête, `Access-Control-Allow-Origin` la demande d’origine croisée échoue. Plus précisément, le navigateur interdit la demande. Même si le serveur renvoie une réponse réussie, le navigateur ne met pas la réponse à la disposition de l’application client.

<a name="test"></a>

## <a name="test-cors"></a>Tester CORS

Pour tester CORS :

1. [Créer un projet API](xref:tutorials/first-web-api). Alternativement, vous pouvez [télécharger l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).
1. Activez CORS à l’aide d’une des approches de ce document. Par exemple :

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > `WithOrigins("https://localhost:<port>");`ne doit être utilisé que pour tester un exemple d’application similaire au [code d’échantillon de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).

1. Créez un projet d’application web (Razor Pages ou MVC). L’échantillon utilise Razor Pages. Vous pouvez créer l’application web dans la même solution que le projet API.
1. Ajoutez le code mis en évidence suivant au fichier *Index.cshtml* :

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. Dans le code `url: 'https://<web app>.azurewebsites.net/api/values/1',` précédent, remplacez par l’URL de l’application déployée.
1. Déployer le projet API. Par exemple, [déployez-vous à Azure](xref:host-and-deploy/azure-apps/index).
1. Exécutez les pages Razor ou l’application MVC depuis le bureau et cliquez sur le bouton **Test.** Utilisez les outils F12 pour passer en revue les messages d’erreur.
1. Retirez l’origine `WithOrigins` locale et déployez l’application. Alternativement, exécutez l’application client avec un port différent. Par exemple, dirigez-vous à partir de Visual Studio.
1. Test avec l’application client. Les échecs de CORS renvoient une erreur, mais le message d’erreur n’est pas disponible pour JavaScript. Utilisez l’onglet console dans les outils F12 pour voir l’erreur. Selon le navigateur, vous obtenez une erreur (dans la console d’outils F12) similaire à ce qui suit:

   * Utilisation de Microsoft Edge:

     **SEC7120: [CORS] `https://localhost:44375` L’origine `https://localhost:44375` n’a pas trouvé dans l’en-tête de réponse Access-Control-Allow-Origin pour la ressource d’origine croisée à`https://webapi.azurewebsites.net/api/values/1`**

   * Utilisation de Chrome:

     **L’accès à XMLHttpRequest à `https://webapi.azurewebsites.net/api/values/1` partir de l’origine `https://localhost:44375` a été bloqué par la politique corS: Aucun «Accès-Contrôle-Autoriser-Origin» en-tête n’est présent sur la ressource demandée.**
     
Les paramètres de terminaison compatibles CORS peuvent être testés avec un outil, comme [Fiddler](https://www.telerik.com/fiddler) ou [Postman](https://www.getpostman.com/). Lors de l’utilisation d’un outil, l’origine de la demande spécifiée par l’en-tête `Origin` doit différer de l’hôte recevant la demande. Si la demande n’est pas *transversale* en `Origin` fonction de la valeur de l’en-tête :

* Il n’est pas nécessaire pour CORS Middleware de traiter la demande.
* Les en-têtes corS ne sont pas retournés dans la réponse.

## <a name="cors-in-iis"></a>CORS en IIS

Lors du déploiement vers IIS, CORS doit s’exécuter avant l’authentification de Windows si le serveur n’est pas configuré pour permettre un accès anonyme. Pour soutenir ce scénario, le [module COS IIS](https://www.iis.net/downloads/microsoft/iis-cors-module) doit être installé et configuré pour l’application.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Partage des ressources inter-origine (CORS)](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [Commencer avec le module CORS DE l’IIS](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end
