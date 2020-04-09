---
title: Fonctionnalités de requête dans ASP.NET Core
author: ardalis
description: Découvrez les détails d’implémentation d’un serveur web relatifs aux requêtes et réponses HTTP qui sont définies dans les interfaces pour ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
uid: fundamentals/request-features
ms.openlocfilehash: d0f3ae521d1f314dd04cb581d9a921da4719273d
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "79416225"
---
# <a name="request-features-in-aspnet-core"></a><span data-ttu-id="be323-103">Fonctionnalités de requête dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="be323-103">Request Features in ASP.NET Core</span></span>

<span data-ttu-id="be323-104">Par [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="be323-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="be323-105">Les détails d’implémentation d’un serveur web relatifs aux requêtes HTTP et aux réponses sont définis dans les interfaces.</span><span class="sxs-lookup"><span data-stu-id="be323-105">Web server implementation details related to HTTP requests and responses are defined in interfaces.</span></span> <span data-ttu-id="be323-106">Ces interfaces sont utilisées par les implémentations de serveur et les intergiciels (middleware) pour créer et modifier le pipeline d’hébergement de l’application.</span><span class="sxs-lookup"><span data-stu-id="be323-106">These interfaces are used by server implementations and middleware to create and modify the application's hosting pipeline.</span></span>

## <a name="feature-interfaces"></a><span data-ttu-id="be323-107">Interfaces de fonctionnalités</span><span class="sxs-lookup"><span data-stu-id="be323-107">Feature interfaces</span></span>

<span data-ttu-id="be323-108">ASP.NET Core définit un certain nombre d’interfaces de fonctionnalités HTTP dans `Microsoft.AspNetCore.Http.Features`. Ces interfaces sont utilisées par les serveurs pour identifier les fonctionnalités prises en charge.</span><span class="sxs-lookup"><span data-stu-id="be323-108">ASP.NET Core defines a number of HTTP feature interfaces in `Microsoft.AspNetCore.Http.Features` which are used by servers to identify the features they support.</span></span> <span data-ttu-id="be323-109">Les interfaces de fonctionnalités suivantes gèrent les requêtes et retournent des réponses :</span><span class="sxs-lookup"><span data-stu-id="be323-109">The following feature interfaces handle requests and return responses:</span></span>

<span data-ttu-id="be323-110">`IHttpRequestFeature` Définit la structure d’une requête HTTP, notamment le protocole, le chemin, la chaîne de requête, les en-têtes et le corps.</span><span class="sxs-lookup"><span data-stu-id="be323-110">`IHttpRequestFeature` Defines the structure of an HTTP request, including the protocol, path, query string, headers, and body.</span></span>

<span data-ttu-id="be323-111">`IHttpResponseFeature` Définit la structure d’une réponse HTTP, notamment le code d’état, les en-têtes et le corps de la réponse.</span><span class="sxs-lookup"><span data-stu-id="be323-111">`IHttpResponseFeature` Defines the structure of an HTTP response, including the status code, headers, and body of the response.</span></span>

<span data-ttu-id="be323-112">`IHttpAuthenticationFeature` Définit la prise en charge de l’identification des utilisateurs basée sur un `ClaimsPrincipal` et de la spécification d’un gestionnaire d’authentification.</span><span class="sxs-lookup"><span data-stu-id="be323-112">`IHttpAuthenticationFeature` Defines support for identifying users based on a `ClaimsPrincipal` and specifying an authentication handler.</span></span>

<span data-ttu-id="be323-113">`IHttpUpgradeFeature` Définit la prise en charge des [mises à niveau HTTP](https://tools.ietf.org/html/rfc2616.html#section-14.42), qui permettent au client de spécifier des protocoles supplémentaires qu’il veut utiliser si le serveur souhaite changer de protocole.</span><span class="sxs-lookup"><span data-stu-id="be323-113">`IHttpUpgradeFeature` Defines support for [HTTP Upgrades](https://tools.ietf.org/html/rfc2616.html#section-14.42), which allow the client to specify which additional protocols it would like to use if the server wishes to switch protocols.</span></span>

<span data-ttu-id="be323-114">`IHttpBufferingFeature` Définit les méthodes permettant de désactiver la mise en mémoire tampon des requêtes et/ou des réponses.</span><span class="sxs-lookup"><span data-stu-id="be323-114">`IHttpBufferingFeature` Defines methods for disabling buffering of requests and/or responses.</span></span>

<span data-ttu-id="be323-115">`IHttpConnectionFeature` Définit les propriétés des adresses et ports locaux et distants.</span><span class="sxs-lookup"><span data-stu-id="be323-115">`IHttpConnectionFeature` Defines properties for local and remote addresses and ports.</span></span>

<span data-ttu-id="be323-116">`IHttpRequestLifetimeFeature` Définit la prise en charge de l’abandon de connexions, ou de la détection de l’arrêt prématuré d’une requête, par exemple en raison de la déconnexion d’un client.</span><span class="sxs-lookup"><span data-stu-id="be323-116">`IHttpRequestLifetimeFeature` Defines support for aborting connections, or detecting if a request has been terminated prematurely, such as by a client disconnect.</span></span>

<span data-ttu-id="be323-117">`IHttpSendFileFeature` Définit une méthode d’envoi de fichiers de façon asynchrone.</span><span class="sxs-lookup"><span data-stu-id="be323-117">`IHttpSendFileFeature` Defines a method for sending files asynchronously.</span></span>

<span data-ttu-id="be323-118">`IHttpWebSocketFeature` Définit une API pour la prise en charge de sockets web.</span><span class="sxs-lookup"><span data-stu-id="be323-118">`IHttpWebSocketFeature` Defines an API for supporting web sockets.</span></span>

<span data-ttu-id="be323-119">`IHttpRequestIdentifierFeature` Ajoute une propriété qui peut être implémentée pour identifier de façon unique des requêtes.</span><span class="sxs-lookup"><span data-stu-id="be323-119">`IHttpRequestIdentifierFeature` Adds a property that can be implemented to uniquely identify requests.</span></span>

<span data-ttu-id="be323-120">`ISessionFeature` Définit les abstractions `ISessionFactory` et `ISession` pour prendre en charge les sessions utilisateur.</span><span class="sxs-lookup"><span data-stu-id="be323-120">`ISessionFeature` Defines `ISessionFactory` and `ISession` abstractions for supporting user sessions.</span></span>

<span data-ttu-id="be323-121">`ITlsConnectionFeature` Définit une API pour la récupération de certificats clients.</span><span class="sxs-lookup"><span data-stu-id="be323-121">`ITlsConnectionFeature` Defines an API for retrieving client certificates.</span></span>

<span data-ttu-id="be323-122">`ITlsTokenBindingFeature` Définit des méthodes permettant d’utiliser des paramètres de liaison de jeton TLS.</span><span class="sxs-lookup"><span data-stu-id="be323-122">`ITlsTokenBindingFeature` Defines methods for working with TLS token binding parameters.</span></span>

> [!NOTE]
> <span data-ttu-id="be323-123">`ISessionFeature` n’est pas une fonctionnalité de serveur, mais est implémentée par `SessionMiddleware` (consultez [Gestion de l’état de l’application](app-state.md)).</span><span class="sxs-lookup"><span data-stu-id="be323-123">`ISessionFeature` isn't a server feature, but is implemented by the `SessionMiddleware` (see [Managing Application State](app-state.md)).</span></span>

## <a name="feature-collections"></a><span data-ttu-id="be323-124">Collections de fonctionnalités</span><span class="sxs-lookup"><span data-stu-id="be323-124">Feature collections</span></span>

<span data-ttu-id="be323-125">La propriété `Features` de `HttpContext` fournit une interface permettant d’obtenir et de définir les fonctionnalités HTTP disponibles pour la requête actuelle.</span><span class="sxs-lookup"><span data-stu-id="be323-125">The `Features` property of `HttpContext` provides an interface for getting and setting the available HTTP features for the current request.</span></span> <span data-ttu-id="be323-126">Étant donné que la collection de fonctionnalités est mutable même dans le contexte d’une requête, il est possible d’utiliser un intergiciel (middleware) pour modifier la collection et ajouter la prise en charge de fonctionnalités supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="be323-126">Since the feature collection is mutable even within the context of a request, middleware can be used to modify the collection and add support for additional features.</span></span>

## <a name="middleware-and-request-features"></a><span data-ttu-id="be323-127">Intergiciel (middleware) et fonctionnalités de requête</span><span class="sxs-lookup"><span data-stu-id="be323-127">Middleware and request features</span></span>

<span data-ttu-id="be323-128">Alors que les serveurs sont chargés de créer la collection de fonctionnalités, l’intergiciel peut aussi bien ajouter des fonctionnalités à cette collection et consommer des fonctionnalités de celle-ci.</span><span class="sxs-lookup"><span data-stu-id="be323-128">While servers are responsible for creating the feature collection, middleware can both add to this collection and consume features from the collection.</span></span> <span data-ttu-id="be323-129">Par exemple, `StaticFileMiddleware` accède à la fonctionnalité `IHttpSendFileFeature`.</span><span class="sxs-lookup"><span data-stu-id="be323-129">For example, the `StaticFileMiddleware` accesses the `IHttpSendFileFeature` feature.</span></span> <span data-ttu-id="be323-130">Si la fonctionnalité existe, elle est utilisée pour envoyer le fichier statique demandé à partir de son chemin physique.</span><span class="sxs-lookup"><span data-stu-id="be323-130">If the feature exists, it's used to send the requested static file from its physical path.</span></span> <span data-ttu-id="be323-131">Sinon, une autre méthode plus lente est utilisée pour envoyer le fichier.</span><span class="sxs-lookup"><span data-stu-id="be323-131">Otherwise, a slower alternative method is used to send the file.</span></span> <span data-ttu-id="be323-132">Quand l’interface `IHttpSendFileFeature` est disponible, elle permet au système d’exploitation d’ouvrir le fichier et d’effectuer une copie directe en mode noyau sur la carte réseau.</span><span class="sxs-lookup"><span data-stu-id="be323-132">When available, the `IHttpSendFileFeature` allows the operating system to open the file and perform a direct kernel mode copy to the network card.</span></span>

<span data-ttu-id="be323-133">De plus, l’intergiciel peut effectuer des ajouts à la collection de fonctionnalités établie par le serveur.</span><span class="sxs-lookup"><span data-stu-id="be323-133">Additionally, middleware can add to the feature collection established by the server.</span></span> <span data-ttu-id="be323-134">Il est même possible de remplacer des fonctionnalités existantes par un intergiciel, ce qui permet à ce dernier d’augmenter les fonctionnalités du serveur.</span><span class="sxs-lookup"><span data-stu-id="be323-134">Existing features can even be replaced by middleware, allowing the middleware to augment the functionality of the server.</span></span> <span data-ttu-id="be323-135">Les fonctionnalités ajoutées à la collection sont disponibles immédiatement pour les autres intergiciels ou l’application sous-jacente elle-même plus loin dans le pipeline de requête.</span><span class="sxs-lookup"><span data-stu-id="be323-135">Features added to the collection are available immediately to other middleware or the underlying application itself later in the request pipeline.</span></span>

<span data-ttu-id="be323-136">La combinaison d’implémentations de serveurs personnalisées et d’améliorations spécifiques de l’intergiciel permet de construire l’ensemble précis de fonctionnalités qu’exige une application.</span><span class="sxs-lookup"><span data-stu-id="be323-136">By combining custom server implementations and specific middleware enhancements, the precise set of features an application requires can be constructed.</span></span> <span data-ttu-id="be323-137">Cela permet d’ajouter les fonctionnalités manquantes sans nécessiter un changement de serveur, et de s’assurer que seule la quantité minimale de fonctionnalités sont exposées, ce qui limite la zone de surface d’attaque et améliore les performances.</span><span class="sxs-lookup"><span data-stu-id="be323-137">This allows missing features to be added without requiring a change in server, and ensures only the minimal amount of features are exposed, thus limiting attack surface area and improving performance.</span></span>

## <a name="summary"></a><span data-ttu-id="be323-138">Résumé</span><span class="sxs-lookup"><span data-stu-id="be323-138">Summary</span></span>

<span data-ttu-id="be323-139">Les interfaces de fonctionnalités définissent des fonctionnalités HTTP spécifiques qu’une requête donnée peut prendre en charge.</span><span class="sxs-lookup"><span data-stu-id="be323-139">Feature interfaces define specific HTTP features that a given request may support.</span></span> <span data-ttu-id="be323-140">Les serveurs définissent des collections de fonctionnalités et l’ensemble initial de fonctionnalités prises en charge par ce serveur, mais il est possible d’utiliser un intergiciel pour améliorer ces fonctionnalités.</span><span class="sxs-lookup"><span data-stu-id="be323-140">Servers define collections of features, and the initial set of features supported by that server, but middleware can be used to enhance these features.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="be323-141">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="be323-141">Additional resources</span></span>

* [<span data-ttu-id="be323-142">Serveurs</span><span class="sxs-lookup"><span data-stu-id="be323-142">Servers</span></span>](xref:fundamentals/servers/index)
* [<span data-ttu-id="be323-143">Middleware</span><span class="sxs-lookup"><span data-stu-id="be323-143">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="be323-144">OWIN (Open Web Interface pour .NET)</span><span class="sxs-lookup"><span data-stu-id="be323-144">Open Web Interface for .NET (OWIN)</span></span>](xref:fundamentals/owin)
