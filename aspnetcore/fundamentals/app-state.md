---
title: Session à ASP.NET Core
author: rick-anderson
description: Découvrez les approches pour préserver la session entre les demandes.
ms.author: riande
ms.custom: mvc
ms.date: 03/06/2020
no-loc:
- SignalR
uid: fundamentals/app-state
ms.openlocfilehash: 85d2a418c3aaae40bbcdc040095c2c98d4b7242c
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80640043"
---
# <a name="session-and-state-management-in-aspnet-core"></a>Session et gestion de l’État dans ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Par [Rick Anderson](https://twitter.com/RickAndMSFT), Kirk [Larkin](https://twitter.com/serpent5), et Diana [LaRose](https://github.com/DianaLaRose)

HTTP est un protocole sans état. Par défaut, les demandes HTTP sont des messages indépendants qui ne conservent pas les valeurs des utilisateurs. Cet article décrit plusieurs approches pour préserver les données des utilisateurs entre les demandes.

[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/app-state/samples) ([comment télécharger](xref:index#how-to-download-a-sample))

## <a name="state-management"></a>Gestion de l'état

L’état peut être stocké à l’aide de plusieurs approches. Chacune d’elles est abordée plus loin dans cette rubrique.

| Approche du stockage | Mécanisme de stockage |
| ---------------- | ----------------- |
| [Cookies](#cookies) | HTTP cookies. Peut inclure des données stockées à l’aide du code d’application côté serveur. |
| [État de session](#session-state) | Cookies HTTP et code d’application côté serveur |
| [TempData TempData](#tempdata) | Cookies HTTP ou état de session |
| [Chaînes de requête](#query-strings) | Chaînes de requête HTTP |
| [Champs masqués](#hidden-fields) | Champs de formulaires HTTP |
| [HttpContext.Items](#httpcontextitems) | Code d’application côté serveur |
| [Cache](#cache) | Code d’application côté serveur |

## <a name="cookies"></a>Cookies

Les cookies stockent des données entre les requêtes. Les cookies sont envoyés avec chaque requête. Il est donc essentiel de limiter leur taille au minimum. Dans l’idéal, un cookie doit stocker uniquement un identificateur et les données stockées par l’application. La plupart des navigateurs limitent la taille des cookies à 4096 octets. Seul un nombre limité de cookies est disponible pour chaque domaine.

Pour éviter les risques de falsification, les cookies doivent être validés par l’application. Les cookies peuvent être supprimés par les utilisateurs et ils expirent sur les clients. Toutefois, ils constituent généralement la forme la plus durable de persistance des données sur le client.

Les cookies servent souvent à personnaliser le contenu pour un utilisateur connu. L’utilisateur est uniquement identifié, et pas authentifié dans la plupart des cas. Le cookie peut stocker le nom, le nom de compte ou l’identifiant utilisateur unique de l’utilisateur tel qu’un GUID. Le cookie peut être utilisé pour accéder aux paramètres personnalisés de l’utilisateur, tels que leur couleur de fond de site Web préféré.

Consultez le Règlement général sur la [protection des données (RPG) de l’Union européenne](https://ec.europa.eu/info/law/law-topic/data-protection) lors de la délivrance de cookies et des préoccupations relatives à la protection de la vie privée. Pour plus d’informations, consultez [Prise en charge du règlement général sur la protection des données (RGPD) de l’Union Européenne dans ASP.NET Core](xref:security/gdpr).

## <a name="session-state"></a>État de la session

L’état de session est un scénario ASP.NET Core pour le stockage des données utilisateur pendant que l’utilisateur parcourt une application web. L’état de session utilise un magasin tenu à jour par l’application afin de conserver les données entre les requêtes d’un client. Les données de session sont étayées par un cache et considérées comme des données éphémères. Le site doit continuer à fonctionner sans les données de la session. Les données d’application critiques doivent être stockées dans la base de données utilisateur et mises en cache dans la session uniquement à des fins d’optimisation des performances.

La session n’est pas prise en charge dans les applications [SignalR](xref:signalr/index) car un [hub SignalR](xref:signalr/hubs) peut s’exécuter indépendamment d’un contexte HTTP. Par exemple, cela peut se produire quand une longue requête d’interrogation est ouverte par un hub au-delà de la durée de vie du contexte de la requête HTTP.

ASP.NET Core maintient l’état de session en fournissant un cookie au client qui contient une pièce d’identité de session. L’ID de la session de cookies :

* Est envoyé à l’application à chaque demande.
* Est utilisé par l’application pour récupérer les données de session.

L’état de session présente les comportements suivants :

* Le cookie de session est spécifique au navigateur. Les sessions ne sont pas partagées entre les navigateurs.
* Les cookies de session sont supprimés uniquement quand la session se termine.
* Si un cookie est reçu pour une session qui a expiré, une session qui utilise le même cookie de session est créée.
* Les sessions vides ne sont pas conservées. La session doit avoir au moins une valeur définie pour persister la session entre les demandes. Quand une session n’est pas conservée, un nouvel ID de session est généré pour chaque nouvelle requête.
* L’application conserve une session pendant une période limitée après la dernière requête. L’application définit le délai d’expiration d’une session ou utilise la valeur par défaut de 20 minutes. L’état de session est idéal pour stocker les données utilisateur :
  * C’est spécifique à une session particulière.
  * Lorsque les données ne nécessitent pas de stockage permanent entre les sessions.
* Les données de session sont supprimées quand l’implémentation [ISession.Clear](/dotnet/api/microsoft.aspnetcore.http.isession.clear) est appelée ou quand la session expire.
* Il n’existe aucun mécanisme par défaut permettant de signaler au code d’application qu’un navigateur client a été fermé ou que le cookie de session a été supprimé ou a expiré sur le client.
* Les cookies d’état de session ne sont pas marqués essentiels par défaut. L’état de session n’est pas fonctionnel à moins que le suivi soit autorisé par le visiteur du site. Pour plus d’informations, consultez <xref:security/gdpr#tempdata-provider-and-session-state-cookies-arent-essential>.

> [!WARNING]
> Ne stockez pas de données sensibles dans l’état de session. L’utilisateur risque de ne pas fermer le navigateur et de ne pas effacer le cookie de session. Certains navigateurs conservent des cookies de session valides entre les fenêtres du navigateur. Une session peut ne pas être limitée à un seul utilisateur. Le prochain utilisateur peut continuer à parcourir l’application avec le même cookie de session.

Le fournisseur de cache en mémoire stocke les données de session dans la mémoire du serveur où réside l’application. Dans un scénario de batterie de serveurs :

* Utilisez des *sessions persistantes* pour lier chaque session à une instance d’application spécifique sur un serveur donné. [Azure App Service](https://azure.microsoft.com/services/app-service/) utilise [Application Request Routing (ARR)](/iis/extensions/planning-for-arr/using-the-application-request-routing-module) pour appliquer les sessions persistantes par défaut. Toutefois, les sessions rémanentes peuvent impacter l’extensibilité et compliquer les mises à jour des applications web. Une meilleure approche consiste à utiliser le cache distribué Redis ou SQL Server, qui ne nécessite pas de sessions persistantes. Pour plus d’informations, consultez <xref:performance/caching/distributed>.
* Le cookie de session est chiffré par le biais d’[IDataProtector](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotector). La protection des données doit être configurée correctement pour lire les cookies de session sur chaque ordinateur. Pour plus d’informations, consultez <xref:security/data-protection/introduction> et [Fournisseurs de stockage de clés](xref:security/data-protection/implementation/key-storage-providers).

### <a name="configure-session-state"></a>Configurer l’état de session

Le package [Microsoft.AspNetCore.Session](https://www.nuget.org/packages/Microsoft.AspNetCore.Session/) :

* Est inclus implicitement par le cadre.
* Fournit middleware pour la gestion de l’état de session.

Pour activer le middleware Session, `Startup` doit contenir les éléments suivants :

* Un des caches mémoire [IDistributedCache](/dotnet/api/microsoft.extensions.caching.distributed.idistributedcache). L’implémentation `IDistributedCache` est utilisée comme magasin de stockage pour la session. Pour plus d’informations, consultez <xref:performance/caching/distributed>.
* Un appel à [AddSession](/dotnet/api/microsoft.extensions.dependencyinjection.sessionservicecollectionextensions.addsession) dans `ConfigureServices`.
* Un appel à [UseSession](/dotnet/api/microsoft.aspnetcore.builder.sessionmiddlewareextensions.usesession#Microsoft_AspNetCore_Builder_SessionMiddlewareExtensions_UseSession_Microsoft_AspNetCore_Builder_IApplicationBuilder_) dans `Configure`.

Le code suivant montre comment configurer le fournisseur de session en mémoire avec une implémentation en mémoire par défaut de `IDistributedCache` :

[!code-csharp[](app-state/samples/3.x/SessionSample/Startup4.cs?name=snippet1&highlight=12-19,39)]

Le code précédent définit un court délai pour simplifier les tests.

L’ordre des middlewares est important.  Appelez `UseSession` `UseRouting` après `UseEndpoints`et avant . Voir [Middleware Ordering](xref:fundamentals/middleware/index#order).

[HttpContext.Session](xref:Microsoft.AspNetCore.Http.HttpContext.Session) est disponible une fois l’état de session configuré.

`HttpContext.Session` n’est pas accessible avant que `UseSession` ait été appelée.

Il est impossible de créer une nouvelle session avec un nouveau cookie de session une fois que l’application a commencé à écrire dans le flux de réponse. L’exception est enregistrée dans le journal de serveur web et n’est pas affichée pas dans le navigateur.

### <a name="load-session-state-asynchronously"></a>Charger l’état de session de façon asynchrone

Le fournisseur de session par défaut dans ASP.NET Core charge les enregistrements de session de façon asynchrone à partir du magasin de stockage sous-jacent [IDistributedCache](/dotnet/api/microsoft.extensions.caching.distributed.idistributedcache) uniquement si la méthode [ISession.LoadAsync](/dotnet/api/microsoft.aspnetcore.http.isession.loadasync) est appelée explicitement avant les méthodes [TryGetValue](/dotnet/api/microsoft.aspnetcore.http.isession.trygetvalue), [Set](/dotnet/api/microsoft.aspnetcore.http.isession.set) ou [Remove](/dotnet/api/microsoft.aspnetcore.http.isession.remove). Si `LoadAsync` n’est pas appelée en premier, l’enregistrement de session sous-jacent est chargé de façon synchrone, ce qui peut entraîner une baisse des performances à l’échelle.

Pour forcer les applications à appliquer ce modèle, incluez les implémentations [DistributedSessionStore](/dotnet/api/microsoft.aspnetcore.session.distributedsessionstore) et [DistributedSession](/dotnet/api/microsoft.aspnetcore.session.distributedsession) dans un wrapper, avec des versions qui lèvent une exception si la méthode `LoadAsync` n’est pas appelée avant `TryGetValue`, `Set` ou `Remove`. Inscrivez ensuite les versions incluses dans le wrapper auprès du conteneur de services.

### <a name="session-options"></a>Options de session

Pour remplacer les valeurs par défaut de la session, utilisez [SessionOptions](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions).

| Option | Description |
| ------ | ----------- |
| [Cookie](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.cookie) | Détermine les paramètres utilisés pour créer le cookie. [Name](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.name) prend la valeur [SessionDefaults.CookieName](/dotnet/api/microsoft.aspnetcore.session.sessiondefaults.cookiename) (`.AspNetCore.Session`) par défaut. [Path](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.path) prend la valeur [SessionDefaults.CookiePath](/dotnet/api/microsoft.aspnetcore.session.sessiondefaults.cookiepath) (`/`) par défaut. [SameSite](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.samesite) prend la valeur [SameSiteMode.Lax](/dotnet/api/microsoft.aspnetcore.http.samesitemode) (`1`) par défaut. [HttpOnly](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.httponly) prend la valeur `true` par défaut. [IsEssential](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.isessential) prend la valeur `false` par défaut. |
| [IdleTimeout](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.idletimeout) | `IdleTimeout` indique la durée pendant laquelle la session peut être inactive avant que son contenu soit abandonné. Chaque accès à la session réinitialise le délai d’expiration. Ce paramètre s’applique uniquement au contenu de la session, et non au cookie. La valeur par défaut est 20 minutes. |
| [IOTimeout](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.iotimeout) | Durée maximale autorisée pour charger une session à partir du magasin ou pour la valider dans le magasin. Ce paramètre peut s’appliquer uniquement aux opérations asynchrones. Vous pouvez désactiver ce délai d’expiration à l’aide de [InfiniteTimeSpan](/dotnet/api/system.threading.timeout.infinitetimespan). La valeur par défaut est de 1 minute. |

Session utilise un cookie pour suivre et identifier les requêtes reçues d’un navigateur. Par défaut, ce cookie se nomme `.AspNetCore.Session` et utilise le chemin `/`. Étant donné que le cookie par défaut ne spécifie pas de domaine, il n’est pas mis à disposition du script côté client dans la page (car [HttpOnly](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.httponly) a la valeur `true` par défaut).

Pour substituer les valeurs de session de cookie par défaut, utilisez <xref:Microsoft.AspNetCore.Builder.SessionOptions> :

[!code-csharp[](app-state/samples/3.x/SessionSample/Startup2.cs?name=snippet1&highlight=5-10)]

L’application utilise la propriété [IdleTimeout](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.idletimeout) pour déterminer la durée pendant laquelle une session peut rester inactive avant que son contenu dans le cache du serveur soit abandonné. Cette propriété est indépendante du délai d’expiration du cookie. Chaque requête qui passe par le [middleware Session](/dotnet/api/microsoft.aspnetcore.session.sessionmiddleware) réinitialise le délai d’expiration.

L’état de session est *sans verrouillage*. Si deux requêtes tentent simultanément de modifier le contenu d’une session, la dernière requête remplace la première. `Session` est implémentée comme une *session cohérente*, ce qui signifie que tout le contenu est stocké au même emplacement. Quand deux requêtes tentent de modifier différentes valeurs de session, la dernière requête peut remplacer les modifications de session effectuées par la première.

### <a name="set-and-get-session-values"></a>Définir et obtenir des valeurs Session

L’état de session est accessible à partir d’une classe Razor Pages [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel) ou d’une classe MVC [Controller](/dotnet/api/microsoft.aspnetcore.mvc.controller) avec [HttpContext.Session](/dotnet/api/microsoft.aspnetcore.http.httpcontext.session). Cette propriété est une implémentation [ISession](/dotnet/api/microsoft.aspnetcore.http.isession).

L’implémentation de `ISession` fournit plusieurs méthodes d’extension pour définir et récupérer des valeurs de chaîne et d’entier. Les méthodes d’extension sont dans le [microsoft.AspNetCore.Http](/dotnet/api/microsoft.aspnetcore.http) namespace.

Méthodes d’extension `ISession` :

* [Get(ISession, String)](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.get)
* [GetInt32(ISession, String)](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.getint32)
* [GetString(ISession, String)](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.getstring)
* [SetInt32(ISession, String, Int32)](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.setint32)
* [SetString(ISession, String, String)](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.setstring)

L’exemple suivant récupère la valeur de session pour la clé `IndexModel.SessionKeyName` (`_Name` dans l’exemple d’application) dans une page Razor Pages :

```csharp
@page
@using Microsoft.AspNetCore.Http
@model IndexModel

...

Name: @HttpContext.Session.GetString(IndexModel.SessionKeyName)
```

L’exemple suivant montre comment définir et obtenir un entier et une chaîne :

[!code-csharp[](app-state/samples/3.x/SessionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=18-19,22-23)]

Toutes les données de session doivent être sérialisées pour activer un scénario de cache distribué, même si vous utilisez le cache en mémoire. Les sérialisateurs de cordes et d’attaches sont fournis par les méthodes d’extension de [l’ISession](/dotnet/api/microsoft.aspnetcore.http.isession). Les types complexes doivent être sérialisés par l’utilisateur à l’aide d’un autre mécanisme, tel que JSON.

Utilisez le code d’échantillon suivant pour sérialiser des objets :

[!code-csharp[](app-state/samples/3.x/SessionSample/Extensions/SessionExtensions.cs?name=snippet1)]

L’exemple suivant montre comment définir et obtenir `SessionExtensions` un objet sérialisable avec la classe:

[!code-csharp[](app-state/samples/3.x/SessionSample/Pages/Index.cshtml.cs?name=snippet2)]

## <a name="tempdata"></a>TempData

ASP.NET Core expose les Pages Razor [TempData](xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.TempData) ou Contrôleur <xref:Microsoft.AspNetCore.Mvc.Controller.TempData>. Cette propriété stocke des données jusqu’à ce qu’elle soit lue dans une autre demande. Les méthodes [Keep(String)](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Keep*) et [Peek(string)](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Peek*) peuvent être utilisées pour examiner les données sans suppression à la fin de la demande. [Conservez](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Keep*) les marques tous les éléments du dictionnaire pour la conservation. `TempData` est :

* Utile pour la redirection lorsque des données sont nécessaires pour plus d’une seule demande.
* Implémenté par `TempData` les fournisseurs utilisant des cookies ou l’état de session.

## <a name="tempdata-samples"></a>Échantillons de TempData

Considérez la page suivante qui crée un client :

[!code-csharp[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet&highlight=15-16,30)]

La page `TempData["Message"]`suivante affiche :

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/IndexPeek.cshtml?range=1-14)]

Dans la balisage précédent, à `TempData["Message"]` la fin de `Peek` la demande, **n’est pas** supprimé parce qu’il est utilisé. Rafraîchir la page affiche `TempData["Message"]`le contenu de .

La balisage suivant est similaire au `Keep` code précédent, mais utilise pour préserver les données à la fin de la demande :

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/IndexKeep.cshtml?range=1-14)]

Naviguer entre les pages *IndexPeek* et *IndexKeep* ne sera pas supprimé `TempData["Message"]`.

Le code `TempData["Message"]`suivant affiche , mais à `TempData["Message"]` la fin de la demande, est supprimé:

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/Index.cshtml?range=1-14)]

### <a name="tempdata-providers"></a>Fournisseurs TempData

Le fournisseur TempData basé sur les cookies est utilisé par défaut pour stocker TempData dans des cookies.

Les données de cookie sont chiffrées avec [IDataProtector](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotector), encodées avec [Base64UrlTextEncoder](/dotnet/api/microsoft.aspnetcore.webutilities.base64urltextencoder), puis mémorisées en bloc. La taille maximale des biscuits est inférieure à [4096 octets](http://www.faqs.org/rfcs/rfc2965.html) en raison du cryptage et du gros morceau. Les données de cookie ne sont pas compressées, car la compression de données chiffrées peut entraîner des problèmes de sécurité, notamment des attaques [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit)) et [BREACH](https://wikipedia.org/wiki/BREACH_(security_exploit)). Pour plus d’informations sur le fournisseur TempData basé sur les cookies, consultez [CookieTempDataProvider](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.cookietempdataprovider).

### <a name="choose-a-tempdata-provider"></a>Choisir un fournisseur TempData

Pour choisir un fournisseur TempData, vous devez tenir compte de plusieurs points. Par exemple :

* L’application utilise-t-elle déjà l’état de session ? Si c’est le cas, l’utilisation du fournisseur TempData d’état de session n’a aucun coût supplémentaire pour l’application au-delà de la taille des données.
* L’application n’utilise-t-elle TempData qu’avec parcimonie pour des quantités relativement faibles de données, jusqu’à 500 octets ? Si c’est le cas, le fournisseur TempData de cookies ajoute un petit coût à chaque requête traitée par TempData. Si ce n’est pas le cas, le fournisseur TempData d’état de session peut être utile pour éviter l’aller-retour d’une grande quantité de données dans chaque requête jusqu’à ce que le TempData soit consommé.
* L’application s’exécute-t-elle dans une batterie de serveurs sur plusieurs serveurs ? Si c’est le cas, aucune configuration supplémentaire n’est nécessaire pour utiliser le fournisseur TempData de cookies en dehors de la Protection des données (consultez <xref:security/data-protection/introduction> et [Fournisseurs de stockage de clés](xref:security/data-protection/implementation/key-storage-providers)).

La plupart des clients Web tels que les navigateurs Web imposent des limites sur la taille maximale de chaque cookie et le nombre total de cookies. Lorsque vous utilisez le fournisseur de cookies TempData, vérifiez que l’application ne dépassera pas [ces limites](http://www.faqs.org/rfcs/rfc2965.html). Prenez en compte la taille totale des données. Pensez aux augmentations de taille de cookie dues au chiffrement et à la segmentation.

### <a name="configure-the-tempdata-provider"></a>Configuration du fournisseur TempData

Le fournisseur TempData basé sur les cookies est activé par défaut.

Pour activer le fournisseur TempData basé sur la session, utilisez la méthode [d’extension AddSessionStateTempDataProvider.](/dotnet/api/microsoft.extensions.dependencyinjection.mvcviewfeaturesmvcbuilderextensions.addsessionstatetempdataprovider) Un seul `AddSessionStateTempDataProvider` appel est requis :

[!code-csharp[](app-state/samples/3.x/SessionSample/Startup3.cs?name=snippet1&highlight=4,6,30)]

## <a name="query-strings"></a>Chaînes de requête

Vous pouvez passer une quantité limitée de données d’une requête à une autre en l’ajoutant à la chaîne de la nouvelle requête. Cela est utile pour capturer l’état de manière persistante et permettre ainsi le partage de liens avec état incorporé par e-mail ou sur les réseaux sociaux. Les chaînes de requête URL étant publiques, vous ne devez jamais utiliser de chaînes de requête pour des données sensibles.

En plus du partage involontaire, y compris les données dans les chaînes de requêtes peut exposer l’application à [Cross-Site Request Forgery (CSRF)](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) attaques. Tout état de session préservé doit se protéger contre les attaques du CSRF. Pour plus d’informations, consultez [Empêcher les attaques par falsification de requête intersites (XSRF/CSRF)](xref:security/anti-request-forgery).

## <a name="hidden-fields"></a>Champs masqués

Les données peuvent être enregistrées dans des champs de formulaire masqués et être republiées lors de la requête suivante. Cela est courant dans les formulaires de plusieurs pages. Étant donné que le client peut potentiellement falsifier les données, l’application doit toujours revalider les données stockées dans les champs masqués.

## <a name="httpcontextitems"></a>HttpContext.Items

La collection [HttpContext.Items](/dotnet/api/microsoft.aspnetcore.http.httpcontext.items) est utilisée pour stocker les données lors du traitement d’une seule requête. Le contenu de la collection est supprimé une fois la requête traitée. La collection `Items` est souvent utilisée pour permettre à des composants ou des middlewares de communiquer quand ils opèrent à différents moments de l’exécution d’une requête et qu’ils ne peuvent pas passer les paramètres de façon directe.

Dans l’exemple suivant, `isVerified` [middleware](xref:fundamentals/middleware/index) ajoute à la `Items` collection :

[!code-csharp[](app-state/samples/3.x/SessionSample/Startup.cs?name=snippet1)]

Pour les middlewares qui ne sont `string` utilisés que dans une seule application, les touches fixes sont acceptables. Middleware partagé entre les applications devrait utiliser des clés d’objet uniques pour éviter les collisions clés. L’exemple suivant montre comment utiliser une clé d’objet unique définie dans une classe de middleware :

[!code-csharp[](app-state/samples/3.x/SessionSample/Middleware/HttpContextItemsMiddleware.cs?name=snippet1&highlight=4,13)]

Tout autre code peut accéder à la valeur stockée dans `HttpContext.Items` à l’aide de la clé exposée par la classe du middleware :

[!code-csharp[](app-state/samples/3.x/SessionSample/Pages/Index.cshtml.cs?name=snippet3)]

Cette approche a également l’avantage d’éliminer l’utilisation des chaînes de clés dans le code.

## <a name="cache"></a>Cache

La mise en cache est un moyen efficace de stocker et récupérer des données. L’application peut contrôler la durée de vie des éléments mis en cache. Pour plus d’informations, consultez <xref:performance/caching/response>.

Les données mises en cache ne sont pas associées à une requête, un utilisateur ou une session spécifique. **Ne cachez pas les données spécifiques à l’utilisateur qui peuvent être récupérées par d’autres demandes d’utilisateurs.**

Pour mettre en cache <xref:performance/caching/memory>les données larges d’application, voir .

## <a name="common-errors"></a>Erreurs courantes

* « Impossible de résoudre le service pour le type 'Microsoft.Extensions.Caching.Distributed.IDistributedCache' lors de la tentative d’activation de 'Microsoft.AspNetCore.Session.DistributedSessionStore'. »

  Ceci est généralement causé par le `IDistributedCache` défaut de configurer au moins une implémentation. Pour plus d’informations, consultez <xref:performance/caching/distributed> et <xref:performance/caching/memory>.

Si la session middleware ne parvient pas à persister une session:

* Le middleware enregistre l’exception et la demande se poursuit normalement.
* Cela entraîne un comportement imprévisible.

Le middleware session peut ne pas persister une session si le magasin de soutien n’est pas disponible. Par exemple, imaginez qu’un utilisateur stocke un panier d’achat dans la session. Il ajoute un élément au panier, mais la validation échoue. L’application n’a pas connaissance de l’échec et signale donc à l’utilisateur que l’élément a été ajouté au panier, ce qui est faux.

L’approche recommandée pour vérifier `await feature.Session.CommitAsync` les erreurs est d’appeler lorsque l’application est terminée en écrivant à la session. <xref:Microsoft.AspNetCore.Http.ISession.CommitAsync*> lève une exception si le magasin de stockage n’est pas disponible. Si `CommitAsync` échoue, l’application peut traiter l’exception. <xref:Microsoft.AspNetCore.Http.ISession.LoadAsync*>dans les mêmes conditions lorsque le magasin de données n’est pas disponible.
  
## <a name="signalr-and-session-state"></a>SignalR et état de session

Les applications SignalR ne doivent pas utiliser l’état de session pour stocker des informations. Les applications SignalR peuvent `Context.Items` stocker par état de connexion dans le hub. <!-- https://github.com/aspnet/SignalR/issues/2139 -->

## <a name="additional-resources"></a>Ressources supplémentaires

<xref:host-and-deploy/web-farm>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

Par [Rick Anderson](https://twitter.com/RickAndMSFT), [Steve Smith](https://ardalis.com/), [Diana LaRose](https://github.com/DianaLaRose) et [Luke Latham](https://github.com/guardrex)

HTTP est un protocole sans état. Sans effectuer des étapes supplémentaires, les requêtes HTTP sont des messages indépendants qui ne conservent pas les valeurs utilisateur ou l’état de l’application. Cet article décrit plusieurs approches pour conserver l’état de l’application et les données utilisateur entre les requêtes.

[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/app-state/samples) ([comment télécharger](xref:index#how-to-download-a-sample))

## <a name="state-management"></a>Gestion de l'état

L’état peut être stocké à l’aide de plusieurs approches. Chacune d’elles est abordée plus loin dans cette rubrique.

| Approche du stockage | Mécanisme de stockage |
| ---------------- | ----------------- |
| [Cookies](#cookies) | Cookies HTTP (peuvent inclure des données stockées à l’aide de code d’application côté serveur) |
| [État de session](#session-state) | Cookies HTTP et code d’application côté serveur |
| [TempData TempData](#tempdata) | Cookies HTTP ou état de session |
| [Chaînes de requête](#query-strings) | Chaînes de requête HTTP |
| [Champs masqués](#hidden-fields) | Champs de formulaires HTTP |
| [HttpContext.Items](#httpcontextitems) | Code d’application côté serveur |
| [Cache](#cache) | Code d’application côté serveur |
| [Injection de dépendance](#dependency-injection) | Code d’application côté serveur |

## <a name="cookies"></a>Cookies

Les cookies stockent des données entre les requêtes. Les cookies sont envoyés avec chaque requête. Il est donc essentiel de limiter leur taille au minimum. Dans l’idéal, un cookie doit stocker uniquement un identificateur et les données stockées par l’application. La plupart des navigateurs limitent la taille des cookies à 4096 octets. Seul un nombre limité de cookies est disponible pour chaque domaine.

Pour éviter les risques de falsification, les cookies doivent être validés par l’application. Les cookies peuvent être supprimés par les utilisateurs et ils expirent sur les clients. Toutefois, ils constituent généralement la forme la plus durable de persistance des données sur le client.

Les cookies servent souvent à personnaliser le contenu pour un utilisateur connu. L’utilisateur est uniquement identifié, et pas authentifié dans la plupart des cas. Le cookie peut stocker le nom de l’utilisateur, le nom du compte ou l’ID utilisateur unique (par exemple un GUID). Vous pouvez ensuite utiliser le cookie pour accéder aux paramètres personnalisés de l’utilisateur, telles que sa couleur d’arrière-plan de site web par défaut.

Tenez compte du [Règlement général sur la protection des données (RGPD) de l’Union Européenne](https://ec.europa.eu/info/law/law-topic/data-protection) lors de l’émission de cookies et de la gestion de la confidentialité. Pour plus d’informations, consultez [Prise en charge du règlement général sur la protection des données (RGPD) de l’Union Européenne dans ASP.NET Core](xref:security/gdpr).

## <a name="session-state"></a>État de la session

L’état de session est un scénario ASP.NET Core pour le stockage des données utilisateur pendant que l’utilisateur parcourt une application web. L’état de session utilise un magasin tenu à jour par l’application afin de conserver les données entre les requêtes d’un client. Les données de session sont secondées par un cache et considérées comme des données éphémères (le site doit continuer à fonctionner sans elles). Les données d’application critiques doivent être stockées dans la base de données utilisateur et mises en cache dans la session uniquement à des fins d’optimisation des performances.

> [!NOTE]
> La session n’est pas prise en charge dans les applications [SignalR](xref:signalr/index) car un [hub SignalR](xref:signalr/hubs) peut s’exécuter indépendamment d’un contexte HTTP. Par exemple, cela peut se produire quand une longue requête d’interrogation est ouverte par un hub au-delà de la durée de vie du contexte de la requête HTTP.

ASP.NET Core tient à jour l’état de session en fournissant au client un cookie qui contient un ID de session, qui est envoyé à l’application lors de chaque requête. Le serveur utilise l’ID de session pour récupérer les données de session.

L’état de session présente les comportements suivants :

* Le cookie de session étant propre au navigateur, les sessions ne sont pas partagées par les navigateurs.
* Les cookies de session sont supprimés uniquement quand la session se termine.
* Si un cookie est reçu pour une session qui a expiré, une session qui utilise le même cookie de session est créée.
* Les sessions vides ne sont pas conservées. La session doit avoir au moins une valeur définie pour pouvoir être conservée entre les requêtes. Quand une session n’est pas conservée, un nouvel ID de session est généré pour chaque nouvelle requête.
* L’application conserve une session pendant une période limitée après la dernière requête. L’application définit le délai d’expiration d’une session ou utilise la valeur par défaut de 20 minutes. L’état de session est idéal pour le stockage des données utilisateur qui sont propres à une session particulière, mais où les données ne nécessitent pas un stockage permanent entre les sessions.
* Les données de session sont supprimées quand l’implémentation [ISession.Clear](/dotnet/api/microsoft.aspnetcore.http.isession.clear) est appelée ou quand la session expire.
* Il n’existe aucun mécanisme par défaut permettant de signaler au code d’application qu’un navigateur client a été fermé ou que le cookie de session a été supprimé ou a expiré sur le client.
* Les modèles de pages ASP.NET Core MVC et Razor incluent la prise en charge du Règlement général sur la protection des données (RGPD). Les cookies d’état de session ne sont pas marqués comme étant essentiels par défaut, donc l’état de session n’est pas fonctionnel, sauf si le suivi est autorisé par le visiteur du site. Pour plus d’informations, consultez <xref:security/gdpr#tempdata-provider-and-session-state-cookies-arent-essential>.

> [!WARNING]
> Ne stockez pas de données sensibles dans l’état de session. L’utilisateur risque de ne pas fermer le navigateur et de ne pas effacer le cookie de session. Certains navigateurs conservent des cookies de session valides entre les fenêtres du navigateur. Une session n’est pas toujours limitée à un seul utilisateur ; l’utilisateur suivant risque donc de parcourir l’application avec le même cookie de session.

Le fournisseur de cache en mémoire stocke les données de session dans la mémoire du serveur où réside l’application. Dans un scénario de batterie de serveurs :

* Utilisez des *sessions persistantes* pour lier chaque session à une instance d’application spécifique sur un serveur donné. [Azure App Service](https://azure.microsoft.com/services/app-service/) utilise [Application Request Routing (ARR)](/iis/extensions/planning-for-arr/using-the-application-request-routing-module) pour appliquer les sessions persistantes par défaut. Toutefois, les sessions rémanentes peuvent impacter l’extensibilité et compliquer les mises à jour des applications web. Une meilleure approche consiste à utiliser le cache distribué Redis ou SQL Server, qui ne nécessite pas de sessions persistantes. Pour plus d’informations, consultez <xref:performance/caching/distributed>.
* Le cookie de session est chiffré par le biais d’[IDataProtector](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotector). La protection des données doit être configurée correctement pour lire les cookies de session sur chaque ordinateur. Pour plus d’informations, consultez <xref:security/data-protection/introduction> et [Fournisseurs de stockage de clés](xref:security/data-protection/implementation/key-storage-providers).

### <a name="configure-session-state"></a>Configurer l’état de session

Le package [Microsoft.AspNetCore.Session](https://www.nuget.org/packages/Microsoft.AspNetCore.Session/), qui est inclus dans le [métapackage Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), fournit un middleware (intergiciel) pour gérer l’état de session. Pour activer le middleware Session, `Startup` doit contenir les éléments suivants :

* Un des caches mémoire [IDistributedCache](/dotnet/api/microsoft.extensions.caching.distributed.idistributedcache). L’implémentation `IDistributedCache` est utilisée comme magasin de stockage pour la session. Pour plus d’informations, consultez <xref:performance/caching/distributed>.
* Un appel à [AddSession](/dotnet/api/microsoft.extensions.dependencyinjection.sessionservicecollectionextensions.addsession) dans `ConfigureServices`.
* Un appel à [UseSession](/dotnet/api/microsoft.aspnetcore.builder.sessionmiddlewareextensions.usesession#Microsoft_AspNetCore_Builder_SessionMiddlewareExtensions_UseSession_Microsoft_AspNetCore_Builder_IApplicationBuilder_) dans `Configure`.

Le code suivant montre comment configurer le fournisseur de session en mémoire avec une implémentation en mémoire par défaut de `IDistributedCache` :

[!code-csharp[](app-state/samples/2.x/SessionSample/Startup.cs?name=snippet1&highlight=5-14,34)]

L’ordre des middlewares est important. Dans l’exemple précédent, une exception `InvalidOperationException` se produit quand `UseSession` est appelée après `UseMvc`. Pour plus d’informations, consultez [Ordre des intergiciels (middleware)](xref:fundamentals/middleware/index#order).

[HttpContext.Session](/dotnet/api/microsoft.aspnetcore.http.httpcontext.session) est disponible une fois l’état de session configuré.

`HttpContext.Session` n’est pas accessible avant que `UseSession` ait été appelée.

Il est impossible de créer une nouvelle session avec un nouveau cookie de session une fois que l’application a commencé à écrire dans le flux de réponse. L’exception est enregistrée dans le journal de serveur web et n’est pas affichée pas dans le navigateur.

### <a name="load-session-state-asynchronously"></a>Charger l’état de session de façon asynchrone

Le fournisseur de session par défaut dans ASP.NET Core charge les enregistrements de session de façon asynchrone à partir du magasin de stockage sous-jacent [IDistributedCache](/dotnet/api/microsoft.extensions.caching.distributed.idistributedcache) uniquement si la méthode [ISession.LoadAsync](/dotnet/api/microsoft.aspnetcore.http.isession.loadasync) est appelée explicitement avant les méthodes [TryGetValue](/dotnet/api/microsoft.aspnetcore.http.isession.trygetvalue), [Set](/dotnet/api/microsoft.aspnetcore.http.isession.set) ou [Remove](/dotnet/api/microsoft.aspnetcore.http.isession.remove). Si `LoadAsync` n’est pas appelée en premier, l’enregistrement de session sous-jacent est chargé de façon synchrone, ce qui peut entraîner une baisse des performances à l’échelle.

Pour forcer les applications à appliquer ce modèle, incluez les implémentations [DistributedSessionStore](/dotnet/api/microsoft.aspnetcore.session.distributedsessionstore) et [DistributedSession](/dotnet/api/microsoft.aspnetcore.session.distributedsession) dans un wrapper, avec des versions qui lèvent une exception si la méthode `LoadAsync` n’est pas appelée avant `TryGetValue`, `Set` ou `Remove`. Inscrivez ensuite les versions incluses dans le wrapper auprès du conteneur de services.

### <a name="session-options"></a>Options de session

Pour remplacer les valeurs par défaut de la session, utilisez [SessionOptions](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions).

| Option | Description |
| ------ | ----------- |
| [Cookie](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.cookie) | Détermine les paramètres utilisés pour créer le cookie. [Name](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.name) prend la valeur [SessionDefaults.CookieName](/dotnet/api/microsoft.aspnetcore.session.sessiondefaults.cookiename) (`.AspNetCore.Session`) par défaut. [Path](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.path) prend la valeur [SessionDefaults.CookiePath](/dotnet/api/microsoft.aspnetcore.session.sessiondefaults.cookiepath) (`/`) par défaut. [SameSite](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.samesite) prend la valeur [SameSiteMode.Lax](/dotnet/api/microsoft.aspnetcore.http.samesitemode) (`1`) par défaut. [HttpOnly](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.httponly) prend la valeur `true` par défaut. [IsEssential](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.isessential) prend la valeur `false` par défaut. |
| [IdleTimeout](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.idletimeout) | `IdleTimeout` indique la durée pendant laquelle la session peut être inactive avant que son contenu soit abandonné. Chaque accès à la session réinitialise le délai d’expiration. Ce paramètre s’applique uniquement au contenu de la session, et non au cookie. La valeur par défaut est 20 minutes. |
| [IOTimeout](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.iotimeout) | Durée maximale autorisée pour charger une session à partir du magasin ou pour la valider dans le magasin. Ce paramètre peut s’appliquer uniquement aux opérations asynchrones. Vous pouvez désactiver ce délai d’expiration à l’aide de [InfiniteTimeSpan](/dotnet/api/system.threading.timeout.infinitetimespan). La valeur par défaut est de 1 minute. |

Session utilise un cookie pour suivre et identifier les requêtes reçues d’un navigateur. Par défaut, ce cookie se nomme `.AspNetCore.Session` et utilise le chemin `/`. Étant donné que le cookie par défaut ne spécifie pas de domaine, il n’est pas mis à disposition du script côté client dans la page (car [HttpOnly](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.httponly) a la valeur `true` par défaut).

Pour substituer les valeurs de session de cookie par défaut, utilisez `SessionOptions` :

[!code-csharp[](app-state/samples_snapshot/2.x/SessionSample/Startup.cs?name=snippet1&highlight=14-19)]

L’application utilise la propriété [IdleTimeout](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.idletimeout) pour déterminer la durée pendant laquelle une session peut rester inactive avant que son contenu dans le cache du serveur soit abandonné. Cette propriété est indépendante du délai d’expiration du cookie. Chaque requête qui passe par le [middleware Session](/dotnet/api/microsoft.aspnetcore.session.sessionmiddleware) réinitialise le délai d’expiration.

L’état de session est *sans verrouillage*. Si deux requêtes tentent simultanément de modifier le contenu d’une session, la dernière requête remplace la première. `Session` est implémentée comme une *session cohérente*, ce qui signifie que tout le contenu est stocké au même emplacement. Quand deux requêtes tentent de modifier différentes valeurs de session, la dernière requête peut remplacer les modifications de session effectuées par la première.

### <a name="set-and-get-session-values"></a>Définir et obtenir des valeurs Session

L’état de session est accessible à partir d’une classe Razor Pages [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel) ou d’une classe MVC [Controller](/dotnet/api/microsoft.aspnetcore.mvc.controller) avec [HttpContext.Session](/dotnet/api/microsoft.aspnetcore.http.httpcontext.session). Cette propriété est une implémentation [ISession](/dotnet/api/microsoft.aspnetcore.http.isession).

L’implémentation de `ISession` fournit plusieurs méthodes d’extension pour définir et récupérer des valeurs de chaîne et d’entier. Les méthodes d’extension se trouvent dans l’espace de noms [Microsoft.AspNetCore.Http](/dotnet/api/microsoft.aspnetcore.http) (ajoutez une instruction `using Microsoft.AspNetCore.Http;` pour accéder aux méthodes d’extension) quand le package [Microsoft.AspNetCore.Http.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.Http.Extensions/) est référencé par le projet. Les deux packages sont inclus dans le [métapackage Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).

Méthodes d’extension `ISession` :

* [Get(ISession, String)](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.get)
* [GetInt32(ISession, String)](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.getint32)
* [GetString(ISession, String)](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.getstring)
* [SetInt32(ISession, String, Int32)](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.setint32)
* [SetString(ISession, String, String)](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.setstring)

L’exemple suivant récupère la valeur de session pour la clé `IndexModel.SessionKeyName` (`_Name` dans l’exemple d’application) dans une page Razor Pages :

```csharp
@page
@using Microsoft.AspNetCore.Http
@model IndexModel

...

Name: @HttpContext.Session.GetString(IndexModel.SessionKeyName)
```

L’exemple suivant montre comment définir et obtenir un entier et une chaîne :

[!code-csharp[](app-state/samples/2.x/SessionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=18-19,22-23)]

Toutes les données de session doivent être sérialisées pour activer un scénario de cache distribué, même si vous utilisez le cache en mémoire. Les sérialisateurs de cordes et d’attaches sont fournis par les méthodes d’extension [d’ISession](/dotnet/api/microsoft.aspnetcore.http.isession)). Les types complexes doivent être sérialisés par l’utilisateur à l’aide d’un autre mécanisme, tel que JSON.

Ajoutez les méthodes d’extension suivantes pour définir et obtenir des objets sérialisables :

[!code-csharp[](app-state/samples/2.x/SessionSample/Extensions/SessionExtensions.cs?name=snippet1)]

L’exemple suivant montre comment définir et obtenir un objet sérialisable avec les méthodes d’extension :

[!code-csharp[](app-state/samples/2.x/SessionSample/Pages/Index.cshtml.cs?name=snippet2)]

## <a name="tempdata"></a>TempData

ASP.NET Core expose les Pages Razor [TempData](xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.TempData) ou Contrôleur <xref:Microsoft.AspNetCore.Mvc.Controller.TempData>. Cette propriété stocke des données jusqu’à ce qu’elle soit lue dans une autre demande. Les méthodes [Keep(String)](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Keep*) et [Peek(string)](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Peek*) peuvent être utilisées pour examiner les données sans suppression à la fin de la demande. [Keep()](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Keep*) marque tous les éléments du dictionnaire pour la conservation. `TempData`est particulièrement utile pour la redirection lorsque des données sont nécessaires pour plus d’une seule demande. `TempData`est implémenté par `TempData` les fournisseurs en utilisant des cookies ou l’état de session.

## <a name="tempdata-samples"></a>Échantillons de TempData

Considérez la page suivante qui crée un client :

[!code-csharp[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet&highlight=15-16,30)]

La page `TempData["Message"]`suivante affiche :

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/IndexPeek.cshtml?range=1-14)]

Dans la balisage précédent, à `TempData["Message"]` la fin de `Peek` la demande, **n’est pas** supprimé parce qu’il est utilisé. Rafraîchir les `TempData["Message"]`affichages de la page .

La balisage suivant est similaire au `Keep` code précédent, mais utilise pour préserver les données à la fin de la demande :

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/IndexKeep.cshtml?range=1-14)]

Naviguer entre les pages *IndexPeek* et *IndexKeep* ne sera pas supprimé `TempData["Message"]`.

Le code `TempData["Message"]`suivant affiche , mais à `TempData["Message"]` la fin de la demande, est supprimé:

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/Index.cshtml?range=1-14)]

### <a name="tempdata-providers"></a>Fournisseurs TempData

Le fournisseur TempData basé sur les cookies est utilisé par défaut pour stocker TempData dans des cookies.

Les données de cookie sont chiffrées avec [IDataProtector](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotector), encodées avec [Base64UrlTextEncoder](/dotnet/api/microsoft.aspnetcore.webutilities.base64urltextencoder), puis mémorisées en bloc. Étant donné que le cookie est mémorisé en bloc, la limite de taille d’un cookie définie dans ASP.NET Core 1.x ne s’applique pas. Les données de cookie ne sont pas compressées, car la compression de données chiffrées peut entraîner des problèmes de sécurité, notamment des attaques [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit)) et [BREACH](https://wikipedia.org/wiki/BREACH_(security_exploit)). Pour plus d’informations sur le fournisseur TempData basé sur les cookies, consultez [CookieTempDataProvider](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.cookietempdataprovider).

### <a name="choose-a-tempdata-provider"></a>Choisir un fournisseur TempData

Pour choisir un fournisseur TempData, vous devez tenir compte de plusieurs points. Par exemple :

1. L’application utilise-t-elle déjà l’état de session ? Si c’est le cas, l’utilisation du fournisseur TempData d’état de session n’entraîne pas de surcoût pour l’application (hormis la taille des données).
2. L’application utilise-t-elle TempData seulement de façon ponctuelle, pour des quantités de données relativement petites (jusqu’à 500 octets) ? Si c’est le cas, le fournisseur TempData de cookies ajoute un petit coût à chaque requête traitée par TempData. Si ce n’est pas le cas, le fournisseur TempData d’état de session peut être utile pour éviter l’aller-retour d’une grande quantité de données dans chaque requête jusqu’à ce que le TempData soit consommé.
3. L’application s’exécute-t-elle dans une batterie de serveurs sur plusieurs serveurs ? Si c’est le cas, aucune configuration supplémentaire n’est nécessaire pour utiliser le fournisseur TempData de cookies en dehors de la Protection des données (consultez <xref:security/data-protection/introduction> et [Fournisseurs de stockage de clés](xref:security/data-protection/implementation/key-storage-providers)).

> [!NOTE]
> La plupart des clients web (par exemple, les navigateurs web) appliquent des limites pour la taille maximale de chaque cookie et/ou le nombre total de cookies. Quand vous utilisez le fournisseur TempData de cookies, vérifiez que l’application ne dépasse pas ces limites. Prenez en compte la taille totale des données. Pensez aux augmentations de taille de cookie dues au chiffrement et à la segmentation.

### <a name="configure-the-tempdata-provider"></a>Configuration du fournisseur TempData

Le fournisseur TempData basé sur les cookies est activé par défaut.

Pour activer le fournisseur TempData basé sur la session, utilisez la méthode d’extension [AddSessionStateTempDataProvider](/dotnet/api/microsoft.extensions.dependencyinjection.mvcviewfeaturesmvcbuilderextensions.addsessionstatetempdataprovider) :

[!code-csharp[](app-state/samples_snapshot_2/2.x/SessionSample/Startup.cs?name=snippet1&highlight=11,13,32)]

L’ordre des middlewares est important. Dans l’exemple précédent, une exception `InvalidOperationException` se produit quand `UseSession` est appelée après `UseMvc`. Pour plus d’informations, consultez [Ordre des intergiciels (middleware)](xref:fundamentals/middleware/index#order).

> [!IMPORTANT]
> Si vous ciblez le .NET Framework et que vous utilisez le fournisseur TempData basé sur la session, ajoutez le package [Microsoft.AspNetCore.Session](https://www.nuget.org/packages/Microsoft.AspNetCore.Session/) au projet.

## <a name="query-strings"></a>Chaînes de requête

Vous pouvez passer une quantité limitée de données d’une requête à une autre en l’ajoutant à la chaîne de la nouvelle requête. Cela est utile pour capturer l’état de manière persistante et permettre ainsi le partage de liens avec état incorporé par e-mail ou sur les réseaux sociaux. Les chaînes de requête URL étant publiques, vous ne devez jamais utiliser de chaînes de requête pour des données sensibles.

En plus du partage accidentel, l’ajout de données dans des chaînes de requête peut favoriser les attaques par [falsification de requête intersites (CSRF, Cross Site Request Forgery)](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) et conduire des utilisateurs authentifiés à visiter des sites malveillants. Les attaquants peuvent ensuite dérober des données utilisateur à partir de l’application ou effectuer des actions malveillantes en utilisant un compte d’utilisateur. Chaque état de session ou d’application conservé doit garantir une protection contre les attaques CSRF. Pour plus d’informations, consultez [Empêcher les attaques par falsification de requête intersites (XSRF/CSRF)](xref:security/anti-request-forgery).

## <a name="hidden-fields"></a>Champs masqués

Les données peuvent être enregistrées dans des champs de formulaire masqués et être republiées lors de la requête suivante. Cela est courant dans les formulaires de plusieurs pages. Étant donné que le client peut potentiellement falsifier les données, l’application doit toujours revalider les données stockées dans les champs masqués.

## <a name="httpcontextitems"></a>HttpContext.Items

La collection [HttpContext.Items](/dotnet/api/microsoft.aspnetcore.http.httpcontext.items) est utilisée pour stocker les données lors du traitement d’une seule requête. Le contenu de la collection est supprimé une fois la requête traitée. La collection `Items` est souvent utilisée pour permettre à des composants ou des middlewares de communiquer quand ils opèrent à différents moments de l’exécution d’une requête et qu’ils ne peuvent pas passer les paramètres de façon directe.

Dans l’exemple suivant, le [middleware](xref:fundamentals/middleware/index) ajoute `isVerified` à la collection `Items`.

```csharp
app.Use(async (context, next) =>
{
    // perform some verification
    context.Items["isVerified"] = true;
    await next.Invoke();
});
```

Plus tard dans le pipeline, un autre middleware peut accéder à la valeur de `isVerified` :

```csharp
app.Run(async (context) =>
{
    await context.Response.WriteAsync($"Verified: {context.Items["isVerified"]}");
});
```

Si un middleware est destiné uniquement à l’usage d’une seule application, les clés `string` sont acceptables. Le middleware partagé entre des instances de l’application doit utiliser des clés d’objets uniques afin d’éviter les conflits de clé. L’exemple suivant montre comment utiliser une clé d’objet unique définie dans une classe de middleware :

[!code-csharp[](app-state/samples/2.x/SessionSample/Middleware/HttpContextItemsMiddleware.cs?name=snippet1&highlight=4,13)]

Tout autre code peut accéder à la valeur stockée dans `HttpContext.Items` à l’aide de la clé exposée par la classe du middleware :

[!code-csharp[](app-state/samples/2.x/SessionSample/Pages/Index.cshtml.cs?name=snippet3)]

Cette approche a également l’avantage d’éliminer l’utilisation des chaînes de clés dans le code.

## <a name="cache"></a>Cache

La mise en cache est un moyen efficace de stocker et récupérer des données. L’application peut contrôler la durée de vie des éléments mis en cache.

Les données mises en cache ne sont pas associées à une requête, un utilisateur ou une session spécifique. **Veillez à ne pas mettre en cache des données propres à l’utilisateur susceptibles d’être récupérées par les requêtes d’autres utilisateurs.**

Pour plus d’informations, consultez <xref:performance/caching/response>.

## <a name="dependency-injection"></a>Injection de dépendances

Utilisez [l’injection de dépendances](xref:fundamentals/dependency-injection) pour rendre les données accessibles à tous les utilisateurs :

1. Définissez un service contenant les données. Par exemple, une classe nommée `MyAppData` est définie :

    ```csharp
    public class MyAppData
    {
        // Declare properties and methods
    }
    ```

2. Ajoutez la classe de service à `Startup.ConfigureServices` :

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<MyAppData>();
    }
    ```

3. Consommez la classe de service de données :

    ```csharp
    public class IndexModel : PageModel
    {
        public IndexModel(MyAppData myService)
        {
            // Do something with the service
            //    Examples: Read data, store in a field or property
        }
    }
    ```

## <a name="common-errors"></a>Erreurs courantes

* « Impossible de résoudre le service pour le type 'Microsoft.Extensions.Caching.Distributed.IDistributedCache' lors de la tentative d’activation de 'Microsoft.AspNetCore.Session.DistributedSessionStore'. »

  Cette erreur est généralement due à un échec de configuration d’au moins une implémentation `IDistributedCache`. Pour plus d’informations, consultez <xref:performance/caching/distributed> et <xref:performance/caching/memory>.

* Dans le cas où le middleware Session ne parvient pas à rendre une session persistante (par exemple si le magasin de stockage n’est pas disponible), il enregistre l’exception dans le journal et la requête se poursuit normalement. Cela entraîne un comportement imprévisible.

  Par exemple, imaginez qu’un utilisateur stocke un panier d’achat dans la session. Il ajoute un élément au panier, mais la validation échoue. L’application n’a pas connaissance de l’échec et signale donc à l’utilisateur que l’élément a été ajouté au panier, ce qui est faux.

  L’approche recommandée pour rechercher les erreurs de ce type consiste à appeler `await feature.Session.CommitAsync();` à partir du code d’application quand l’application a terminé d’écrire dans la session. `CommitAsync` lève une exception si le magasin de stockage n’est pas disponible. Si `CommitAsync` échoue, l’application peut traiter l’exception. `LoadAsync` lève une exception dans les mêmes conditions, quand le magasin de données n’est pas disponible.
  
## <a name="opno-locsignalr-and-session-state"></a>SignalRet l’état de session

SignalRles applications ne doivent pas utiliser l’état de session pour stocker des informations. SignalRles applications peuvent stocker `Context.Items` par état de connexion dans le hub. <!-- https://github.com/aspnet/SignalR/issues/2139 -->

## <a name="additional-resources"></a>Ressources supplémentaires

<xref:host-and-deploy/web-farm>
::: moniker-end
