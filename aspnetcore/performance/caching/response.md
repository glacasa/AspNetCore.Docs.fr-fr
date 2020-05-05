---
title: Mise en cache des réponses dans ASP.NET Core
author: rick-anderson
description: Découvrez comment utiliser la mise en cache des réponses pour réduire les besoins en bande passante et accroître les performances des applications ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 11/04/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: performance/caching/response
ms.openlocfilehash: 3e4bb9980c94f36319cf9b17e65a35ba0f77824e
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776075"
---
# <a name="response-caching-in-aspnet-core"></a>Mise en cache des réponses dans ASP.NET Core

Par [John Luo](https://github.com/JunTaoLuo), [Rick Anderson](https://twitter.com/RickAndMSFT)et [Steve Smith](https://ardalis.com/)

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/response/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

La mise en cache des réponses réduit le nombre de demandes qu’un client ou un proxy effectue sur un serveur Web. La mise en cache des réponses réduit également la quantité de travail effectuée par le serveur Web pour générer une réponse. La mise en cache des réponses est contrôlée par des en-têtes qui spécifient comment vous souhaitez que le client, le proxy et l’intergiciel (middleware) effectuent le cache des réponses.

L' [attribut ResponseCache](#responsecache-attribute) participe à la définition des en-têtes de mise en cache des réponses. Les clients et les proxys intermédiaires doivent honorer les en-têtes pour la mise en cache des réponses sous la [spécification de Caching HTTP 1,1](https://tools.ietf.org/html/rfc7234).

Pour la mise en cache côté serveur qui suit la spécification de mise en cache HTTP 1,1, utilisez l’intergiciel (middleware) de [mise en](xref:performance/caching/middleware)cache des réponses. L’intergiciel (middleware) peut <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> utiliser les propriétés pour influencer le comportement de mise en cache côté serveur.

## <a name="http-based-response-caching"></a>Mise en cache des réponses HTTP

La [spécification de mise en cache HTTP 1,1](https://tools.ietf.org/html/rfc7234) décrit le comportement des caches Internet. L’en-tête HTTP principal utilisé pour la mise en cache est [Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2), qui est utilisé pour spécifier les *directives*de cache. Les directives contrôlent le comportement de mise en cache en fonction des demandes en provenance des clients et des serveurs, tandis que les réponses sont rétablies des serveurs aux clients. Les demandes et les réponses passent par les serveurs proxy, et les serveurs proxy doivent également se conformer à la spécification de mise en cache HTTP 1,1.

Les `Cache-Control` directives communes sont présentées dans le tableau suivant.

| Directive                                                       | Action |
| --------------------------------------------------------------- | ------ |
| [publique](https://tools.ietf.org/html/rfc7234#section-5.2.2.5)   | Un cache peut stocker la réponse. |
| [priv](https://tools.ietf.org/html/rfc7234#section-5.2.2.6)  | La réponse ne doit pas être stockée par un cache partagé. Un cache privé peut stocker et réutiliser la réponse. |
| [âge maximal](https://tools.ietf.org/html/rfc7234#section-5.2.1.1)  | Le client n’accepte pas de réponse dont l’âge est supérieur au nombre de secondes spécifié. Exemples : `max-age=60` (60 secondes), `max-age=2592000` (1 mois) |
| [non-cache](https://tools.ietf.org/html/rfc7234#section-5.2.1.4) | **Sur les demandes**: un cache ne doit pas utiliser une réponse stockée pour satisfaire la demande. Le serveur d’origine régénère la réponse pour le client, et l’intergiciel met à jour la réponse stockée dans son cache.<br><br>**Sur les réponses**: la réponse ne doit pas être utilisée pour une demande ultérieure sans validation sur le serveur d’origine. |
| [non-Store](https://tools.ietf.org/html/rfc7234#section-5.2.1.5) | **Sur les demandes**: un cache ne doit pas stocker la demande.<br><br>**Sur les réponses**: un cache ne doit pas stocker une partie de la réponse. |

Les autres en-têtes de cache qui jouent un rôle dans la mise en cache sont répertoriés dans le tableau suivant.

| En-tête                                                     | Fonction |
| ---------------------------------------------------------- | -------- |
| [Age](https://tools.ietf.org/html/rfc7234#section-5.1)     | Estimation de la durée en secondes depuis que la réponse a été générée ou validée sur le serveur d’origine. |
| [Expires](https://tools.ietf.org/html/rfc7234#section-5.3) | Heure après laquelle la réponse est considérée comme périmée. |
| [Bali](https://tools.ietf.org/html/rfc7234#section-5.4)  | Existe pour la compatibilité descendante avec les caches HTTP/1.0 pour `no-cache` la définition du comportement. Si l' `Cache-Control` en-tête est présent `Pragma` , l’en-tête est ignoré. |
| [Varier](https://tools.ietf.org/html/rfc7231#section-7.1.4)  | Spécifie qu’une réponse mise en cache ne doit pas être envoyée, `Vary` sauf si tous les champs d’en-tête correspondent à la fois à la demande d’origine de la réponse mise en cache et à la nouvelle requête. |

## <a name="http-based-caching-respects-request-cache-control-directives"></a>La mise en cache basée sur HTTP respecte les directives de contrôle de cache de demande

La [spécification de mise en cache HTTP 1,1 pour l’en-tête Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2) requiert un `Cache-Control` cache pour honorer un en-tête valide envoyé par le client. Un client peut faire des demandes avec `no-cache` une valeur d’en-tête et forcer le serveur à générer une nouvelle réponse pour chaque demande.

L’Honoring des `Cache-Control` en-têtes de demande client est judicieux si vous considérez l’objectif de la mise en cache http. Dans le cadre de la spécification officielle, la mise en cache est destinée à réduire la latence et la surcharge réseau de la satisfaction des demandes sur un réseau de clients, de proxys et de serveurs. Il ne s’agit pas nécessairement d’un moyen de contrôler la charge sur un serveur d’origine.

Il n’existe aucun contrôle de développeur sur ce comportement de mise en cache lors de l’utilisation de l’intergiciel (middleware) de [mise en cache des réponses](xref:performance/caching/middleware) , car celui-ci adhère à la spécification officielle de mise en cache. [Les améliorations prévues pour l’intergiciel (middleware](https://github.com/dotnet/AspNetCore/issues/2612) ) permettent de configurer l’intergiciel afin d’ignorer l’en- `Cache-Control` tête d’une demande lorsque vous décidez de traiter une réponse mise en cache. Les améliorations planifiées permettent de mieux contrôler la charge du serveur.

## <a name="other-caching-technology-in-aspnet-core"></a>Autres technologies de mise en cache dans ASP.NET Core

### <a name="in-memory-caching"></a>Mise en cache en mémoire

La mise en cache en mémoire utilise la mémoire du serveur pour stocker les données mises en cache. Ce type de mise en cache convient pour un serveur unique ou plusieurs serveurs à l’aide de *sessions rémanentes*. Les sessions rémanentes signifient que les demandes d’un client sont toujours routées vers le même serveur pour traitement.

Pour plus d’informations, consultez <xref:performance/caching/memory>.

### <a name="distributed-cache"></a>Cache distribué

Utilisez un cache distribué pour stocker les données en mémoire lorsque l’application est hébergée dans une batterie de serveurs ou un Cloud. Le cache est partagé entre les serveurs qui traitent les demandes. Un client peut soumettre une demande gérée par n’importe quel serveur du groupe si les données mises en cache pour le client sont disponibles. ASP.NET Core fonctionne avec les caches distribués SQL Server, [ReDim](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis)et [NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/) .

Pour plus d’informations, consultez <xref:performance/caching/distributed>.

### <a name="cache-tag-helper"></a>Tag Helper de cache

Mettez en cache le contenu à partir d’une vue MVC ou d’une page Razor avec le tag Helper cache. Le tag Helper cache utilise la mise en cache en mémoire pour stocker les données.

Pour plus d’informations, consultez <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>.

### <a name="distributed-cache-tag-helper"></a>Tag Helper Cache distribué

Mettez en cache le contenu à partir d’une vue MVC ou d’une page Razor dans des scénarios de batterie de serveurs Web ou de Cloud distribué avec le tag Helper de cache distribué. Le tag Helper de cache distribué utilise SQL Server, les [redims](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis)ou [NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/) pour stocker des données.

Pour plus d’informations, consultez <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>.

## <a name="responsecache-attribute"></a>Attribut ResponseCache

<xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> Spécifie les paramètres nécessaires à la définition des en-têtes appropriés dans la mise en cache des réponses.

> [!WARNING]
> Désactivez la mise en cache pour le contenu qui contient des informations pour les clients authentifiés. La mise en cache ne doit être activée que pour le contenu qui ne change pas en fonction de l’identité d’un utilisateur ou si un utilisateur est connecté.

<xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys>fait varier la réponse stockée en fonction des valeurs de la liste donnée des clés de requête. Quand une seule valeur est `*` fournie, l’intergiciel (middleware) varie les réponses par tous les paramètres de chaîne de requête de la demande.

L' [intergiciel de mise en cache des réponses](xref:performance/caching/middleware) doit être <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> activé pour définir la propriété. Dans le cas contraire, une exception Runtime est levée. Il n’existe pas d’en-tête <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> http correspondant pour la propriété. La propriété est une fonctionnalité HTTP gérée par l’intergiciel (middleware) de mise en cache des réponses. Pour que l’intergiciel serve une réponse mise en cache, la chaîne de requête et la valeur de chaîne de requête doivent correspondre à une demande précédente. Par exemple, considérez la séquence de requêtes et les résultats présentés dans le tableau suivant.

| Requête                          | Résultats                    |
| -------------------------------- | ------------------------- |
| `http://example.com?key1=value1` | Retourné à partir du serveur. |
| `http://example.com?key1=value1` | Renvoyé par l’intergiciel (middleware). |
| `http://example.com?key1=value2` | Retourné à partir du serveur. |

La première demande est retournée par le serveur et mise en cache dans l’intergiciel (middleware). La deuxième demande est retournée par l’intergiciel (middleware), car la chaîne de requête correspond à la requête précédente. La troisième demande n’est pas dans le cache de l’intergiciel (middleware), car la valeur de la chaîne de requête ne correspond pas à une demande précédente.

Le <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> est utilisé pour configurer et créer (via <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>) un `Microsoft.AspNetCore.Mvc.Internal.ResponseCacheFilter`. Le `ResponseCacheFilter` effectue le travail de mise à jour des en-têtes et des fonctionnalités http appropriées de la réponse. Le filtre :

* Supprime tous les en-têtes `Vary`existants `Cache-Control`pour, `Pragma`et.
* Écrit les en-têtes appropriés en fonction des propriétés définies dans le <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute>.
* Met à jour la fonctionnalité HTTP de mise en <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> cache des réponses si est défini.

### <a name="vary"></a>Varier

Cet en-tête est écrit uniquement <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> lorsque la propriété est définie. Propriété définie sur la `Vary` valeur de la propriété. L’exemple suivant utilise la <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> propriété :

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache1.cshtml.cs?name=snippet)]

À l’aide de l’exemple d’application, affichez les en-têtes de réponse avec les outils réseau du navigateur. Les en-têtes de réponse suivants sont envoyés avec la réponse de la page Cache1 :

```
Cache-Control: public,max-age=30
Vary: User-Agent
```

### <a name="nostore-and-locationnone"></a>NoStore et location. None

<xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore>remplace la plupart des autres propriétés. Quand cette propriété a la valeur `true`, l' `Cache-Control` en-tête a `no-store`la valeur. Si <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> a la valeur `None`:

* `Cache-Control` est défini sur `no-store,no-cache`.
* `Pragma` est défini sur `no-cache`.

Si <xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> est `false` et <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> est `None`, `Cache-Control`et `Pragma` ont la valeur `no-cache`.

<xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore>est généralement défini sur `true` pour les pages d’erreur. La page cache2 de l’exemple d’application génère des en-têtes de réponse qui indiquent au client de ne pas stocker la réponse.

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache2.cshtml.cs?name=snippet)]

L’exemple d’application retourne la page cache2 avec les en-têtes suivants :

```
Cache-Control: no-store,no-cache
Pragma: no-cache
```

### <a name="location-and-duration"></a>Emplacement et durée

Pour activer la mise <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> en cache, doit être défini sur une <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> valeur positive et `Any` doit être (valeur par `Client`défaut) ou. L’infrastructure affecte à `Cache-Control` l’en-tête la valeur d’emplacement `max-age` suivie du de la réponse.

<xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location>les options de `Any` et `Client` se traduisent en `Cache-Control` valeurs `public` d' `private`en-tête de et, respectivement. Comme indiqué dans la section [NoStore et location. None](#nostore-and-locationnone) , le <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> paramètre `None` pour définit `Cache-Control` les `Pragma` deux et en `no-cache`-têtes sur.

`Location.Any`(`Cache-Control` défini sur `public`) indique que le *client ou un proxy intermédiaire* peut mettre en cache la valeur, y compris l' [intergiciel de mise en](xref:performance/caching/middleware)cache des réponses.

`Location.Client`(`Cache-Control` défini sur `private`) indique que *seul le client* peut mettre en cache la valeur. Aucun cache intermédiaire ne doit mettre en cache la valeur, y compris l' [intergiciel de mise en](xref:performance/caching/middleware)cache des réponses.

Les en-têtes de contrôle de cache fournissent simplement des conseils aux clients et aux proxies intermédiaires quand et comment mettre en cache des réponses. Il n’y a aucune garantie que les clients et les proxys honoreront la [spécification de mise en cache HTTP 1,1](https://tools.ietf.org/html/rfc7234). L’intergiciel (middleware) de [mise en cache des réponses](xref:performance/caching/middleware) suit toujours les règles de mise en cache établies par la spécification.

L’exemple suivant montre le modèle de page cache3 de l’exemple d’application et les en-têtes <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> produits par la définition <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> et la conservation de la valeur par défaut :

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache3.cshtml.cs?name=snippet)]

L’exemple d’application retourne la page cache3 avec l’en-tête suivant :

```
Cache-Control: public,max-age=10
```

### <a name="cache-profiles"></a>Profils de cache

Au lieu de dupliquer les paramètres du cache de réponse sur de nombreux attributs d’action de contrôleur, les profils de cache peuventRazor être configurés en tant qu’options lors de la configuration de MVC/pages dans `Startup.ConfigureServices`. Les valeurs trouvées dans un profil de cache référencé sont utilisées comme valeurs par défaut par <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> et sont remplacées par les propriétés spécifiées sur l’attribut.

Configurez un profil de cache. L’exemple suivant montre un profil de cache de 30 secondes dans l’exemple `Startup.ConfigureServices`d’application :

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Startup.cs?name=snippet1)]

Le modèle de page cache4 de l’exemple d' `Default30` application fait référence au profil de cache :

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache4.cshtml.cs?name=snippet)]

Le <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> peut être appliqué aux éléments suivants :

* RazorLes attributs de gestionnaires de pages &ndash; (classes) ne peuvent pas être appliqués aux méthodes de gestionnaire.
* Contrôleurs MVC (classes).
* Les attributs de niveau méthode &ndash; des actions MVC (méthodes) remplacent les paramètres spécifiés dans les attributs de niveau classe.

En-tête résultant appliqué à la réponse de la page `Default30` cache4 par le profil de cache :

```
Cache-Control: public,max-age=30
```

## <a name="additional-resources"></a>Ressources supplémentaires

* [Stockage des réponses dans les caches](https://tools.ietf.org/html/rfc7234#section-3)
* [Cache-Control](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9)
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
