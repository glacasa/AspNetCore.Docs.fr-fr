---
title: Prise en charge des WebSockets dans ASP.NET Core
author: rick-anderson
description: Découvrez comment commencer à utiliser les WebSockets dans ASP.NET Core.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: fundamentals/websockets
ms.openlocfilehash: a8040003374906fd93e12c9fde44c4a5ccc2cf37
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78655784"
---
# <a name="websockets-support-in-aspnet-core"></a><span data-ttu-id="35d5c-103">Prise en charge des WebSockets dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="35d5c-103">WebSockets support in ASP.NET Core</span></span>

<span data-ttu-id="35d5c-104">Par [Tom Dykstra](https://github.com/tdykstra) et [Andrew Stanton-Nurse](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="35d5c-104">By [Tom Dykstra](https://github.com/tdykstra) and [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

<span data-ttu-id="35d5c-105">Cet article explique comment commencer avec les WebSockets dans ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="35d5c-105">This article explains how to get started with WebSockets in ASP.NET Core.</span></span> <span data-ttu-id="35d5c-106">[WebSocket](https://wikipedia.org/wiki/WebSocket) ([RFC 6455](https://tools.ietf.org/html/rfc6455)) est un protocole qui autorise des canaux de communication persistants bidirectionnels sur les connexions TCP.</span><span class="sxs-lookup"><span data-stu-id="35d5c-106">[WebSocket](https://wikipedia.org/wiki/WebSocket) ([RFC 6455](https://tools.ietf.org/html/rfc6455)) is a protocol that enables two-way persistent communication channels over TCP connections.</span></span> <span data-ttu-id="35d5c-107">Son utilisation profite aux applications qui tirent parti d’une communication rapide et en temps réel, par exemple les applications de conversation, de tableau de bord et de jeu.</span><span class="sxs-lookup"><span data-stu-id="35d5c-107">It's used in apps that benefit from fast, real-time communication, such as chat, dashboard, and game apps.</span></span>

<span data-ttu-id="35d5c-108">[Affichez ou téléchargez un exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="35d5c-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="35d5c-109">[Comment courir](#sample-app).</span><span class="sxs-lookup"><span data-stu-id="35d5c-109">[How to run](#sample-app).</span></span>

## <a name="signalr"></a><span data-ttu-id="35d5c-110">SignalR</span><span class="sxs-lookup"><span data-stu-id="35d5c-110">SignalR</span></span>

<span data-ttu-id="35d5c-111">[ASP.NET Core SignalR](xref:signalr/introduction) est une bibliothèque qui simplifie l’ajout de fonctionnalités web en temps réel aux applications.</span><span class="sxs-lookup"><span data-stu-id="35d5c-111">[ASP.NET Core SignalR](xref:signalr/introduction) is a library that simplifies adding real-time web functionality to apps.</span></span> <span data-ttu-id="35d5c-112">Elle utilise des WebSockets dans la mesure du possible.</span><span class="sxs-lookup"><span data-stu-id="35d5c-112">It uses WebSockets whenever possible.</span></span>

<span data-ttu-id="35d5c-113">Pour la plupart des applications, nous recommandons SignalR sur des WebSockets bruts.</span><span class="sxs-lookup"><span data-stu-id="35d5c-113">For most applications, we recommend SignalR over raw WebSockets.</span></span> <span data-ttu-id="35d5c-114">SignalR fournit un transport de secours pour les environnements où WebSockets n’est pas disponible.</span><span class="sxs-lookup"><span data-stu-id="35d5c-114">SignalR provides transport fallback for environments where WebSockets is not available.</span></span> <span data-ttu-id="35d5c-115">Il fournit également un modèle d’application d’appel de procédure distante simple.</span><span class="sxs-lookup"><span data-stu-id="35d5c-115">It also provides a simple remote procedure call app model.</span></span> <span data-ttu-id="35d5c-116">De plus, dans la plupart des scénarios, SignalR ne présente aucun inconvénient majeur concernant le niveau de performance par rapport à l’utilisation de WebSockets bruts.</span><span class="sxs-lookup"><span data-stu-id="35d5c-116">And in most scenarios, SignalR has no significant performance disadvantage compared to using raw WebSockets.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="35d5c-117">Prérequis</span><span class="sxs-lookup"><span data-stu-id="35d5c-117">Prerequisites</span></span>

* <span data-ttu-id="35d5c-118">ASP.NET Core 1.1 ou ultérieur</span><span class="sxs-lookup"><span data-stu-id="35d5c-118">ASP.NET Core 1.1 or later</span></span>
* <span data-ttu-id="35d5c-119">Tout système d’exploitation prenant en charge ASP.NET Core :</span><span class="sxs-lookup"><span data-stu-id="35d5c-119">Any OS that supports ASP.NET Core:</span></span>
  
  * <span data-ttu-id="35d5c-120">Windows 7 / Windows Server 2008 ou ultérieur</span><span class="sxs-lookup"><span data-stu-id="35d5c-120">Windows 7 / Windows Server 2008 or later</span></span>
  * <span data-ttu-id="35d5c-121">Linux</span><span class="sxs-lookup"><span data-stu-id="35d5c-121">Linux</span></span>
  * <span data-ttu-id="35d5c-122">macOS</span><span class="sxs-lookup"><span data-stu-id="35d5c-122">macOS</span></span>
  
* <span data-ttu-id="35d5c-123">Si l’application s’exécute sur Windows avec IIS :</span><span class="sxs-lookup"><span data-stu-id="35d5c-123">If the app runs on Windows with IIS:</span></span>

  * <span data-ttu-id="35d5c-124">Windows 8/Windows Server 2012 ou ultérieur</span><span class="sxs-lookup"><span data-stu-id="35d5c-124">Windows 8 / Windows Server 2012 or later</span></span>
  * <span data-ttu-id="35d5c-125">IIS 8/IIS 8 Express</span><span class="sxs-lookup"><span data-stu-id="35d5c-125">IIS 8 / IIS 8 Express</span></span>
  * <span data-ttu-id="35d5c-126">WebSockets doit être activé (consultez la section [Prise en charge d’IIS/IIS Express](#iisiis-express-support).)</span><span class="sxs-lookup"><span data-stu-id="35d5c-126">WebSockets must be enabled (See the [IIS/IIS Express support](#iisiis-express-support) section.).</span></span>
  
* <span data-ttu-id="35d5c-127">Si l’application s’exécute sur [HTTP.sys](xref:fundamentals/servers/httpsys) :</span><span class="sxs-lookup"><span data-stu-id="35d5c-127">If the app runs on [HTTP.sys](xref:fundamentals/servers/httpsys):</span></span>

  * <span data-ttu-id="35d5c-128">Windows 8/Windows Server 2012 ou ultérieur</span><span class="sxs-lookup"><span data-stu-id="35d5c-128">Windows 8 / Windows Server 2012 or later</span></span>

* <span data-ttu-id="35d5c-129">Pour les navigateurs pris en charge, consultez https://caniuse.com/#feat=websockets.</span><span class="sxs-lookup"><span data-stu-id="35d5c-129">For supported browsers, see https://caniuse.com/#feat=websockets.</span></span>

::: moniker range="< aspnetcore-2.1"

## <a name="nuget-package"></a><span data-ttu-id="35d5c-130">Package NuGet</span><span class="sxs-lookup"><span data-stu-id="35d5c-130">NuGet package</span></span>

<span data-ttu-id="35d5c-131">Installez le package [Microsoft.AspNetCore.WebSockets](https://www.nuget.org/packages/Microsoft.AspNetCore.WebSockets/).</span><span class="sxs-lookup"><span data-stu-id="35d5c-131">Install the [Microsoft.AspNetCore.WebSockets](https://www.nuget.org/packages/Microsoft.AspNetCore.WebSockets/) package.</span></span>

::: moniker-end

## <a name="configure-the-middleware"></a><span data-ttu-id="35d5c-132">Configurer les middlewares</span><span class="sxs-lookup"><span data-stu-id="35d5c-132">Configure the middleware</span></span>


<span data-ttu-id="35d5c-133">Ajoutez le middleware WebSocket dans la méthode `Configure` de la classe `Startup` :</span><span class="sxs-lookup"><span data-stu-id="35d5c-133">Add the WebSockets middleware in the `Configure` method of the `Startup` class:</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSockets)]

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="35d5c-134">Les paramètres suivants peuvent être configurés :</span><span class="sxs-lookup"><span data-stu-id="35d5c-134">The following settings can be configured:</span></span>

* <span data-ttu-id="35d5c-135">`KeepAliveInterval` : fréquence d’envoi de frames de « ping » au client pour garantir que les proxys maintiennent la connexion ouverte.</span><span class="sxs-lookup"><span data-stu-id="35d5c-135">`KeepAliveInterval` - How frequently to send "ping" frames to the client to ensure proxies keep the connection open.</span></span> <span data-ttu-id="35d5c-136">La valeur par défaut est deux minutes.</span><span class="sxs-lookup"><span data-stu-id="35d5c-136">The default is two minutes.</span></span>
* <span data-ttu-id="35d5c-137">`ReceiveBufferSize` : taille de la mémoire tampon utilisée pour recevoir des données.</span><span class="sxs-lookup"><span data-stu-id="35d5c-137">`ReceiveBufferSize` - The size of the buffer used to receive data.</span></span> <span data-ttu-id="35d5c-138">Seuls les utilisateurs avancés peuvent être amenés à changer ce paramètre, pour l’optimisation des performances en fonction de la taille des données.</span><span class="sxs-lookup"><span data-stu-id="35d5c-138">Advanced users may need to change this for performance tuning based on the size of the data.</span></span> <span data-ttu-id="35d5c-139">La valeur par défaut est 4 Ko.</span><span class="sxs-lookup"><span data-stu-id="35d5c-139">The default is 4 KB.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="35d5c-140">Les paramètres suivants peuvent être configurés :</span><span class="sxs-lookup"><span data-stu-id="35d5c-140">The following settings can be configured:</span></span>

* <span data-ttu-id="35d5c-141">`KeepAliveInterval` : fréquence d’envoi de frames de « ping » au client pour garantir que les proxys maintiennent la connexion ouverte.</span><span class="sxs-lookup"><span data-stu-id="35d5c-141">`KeepAliveInterval` - How frequently to send "ping" frames to the client to ensure proxies keep the connection open.</span></span> <span data-ttu-id="35d5c-142">La valeur par défaut est deux minutes.</span><span class="sxs-lookup"><span data-stu-id="35d5c-142">The default is two minutes.</span></span>
* <span data-ttu-id="35d5c-143"><xref:Microsoft.AspNetCore.Builder.WebSocketOptions.ReceiveBufferSize> : taille de la mémoire tampon utilisée pour recevoir des données.</span><span class="sxs-lookup"><span data-stu-id="35d5c-143"><xref:Microsoft.AspNetCore.Builder.WebSocketOptions.ReceiveBufferSize> - The size of the buffer used to receive data.</span></span> <span data-ttu-id="35d5c-144">Seuls les utilisateurs avancés peuvent être amenés à changer ce paramètre, pour l’optimisation des performances en fonction de la taille des données.</span><span class="sxs-lookup"><span data-stu-id="35d5c-144">Advanced users may need to change this for performance tuning based on the size of the data.</span></span> <span data-ttu-id="35d5c-145">La valeur par défaut est 4 Ko.</span><span class="sxs-lookup"><span data-stu-id="35d5c-145">The default is 4 KB.</span></span>
* <span data-ttu-id="35d5c-146">`AllowedOrigins` : liste des valeurs d’en-tête Origin autorisées pour les requêtes WebSocket.</span><span class="sxs-lookup"><span data-stu-id="35d5c-146">`AllowedOrigins` - A list of allowed Origin header values for WebSocket requests.</span></span> <span data-ttu-id="35d5c-147">Par défaut, toutes les origines sont autorisées.</span><span class="sxs-lookup"><span data-stu-id="35d5c-147">By default, all origins are allowed.</span></span> <span data-ttu-id="35d5c-148">Pour plus d’informations, consultez « Restriction d’origine WebSocket » ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="35d5c-148">See "WebSocket origin restriction" below for details.</span></span>

::: moniker-end

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptions)]

## <a name="accept-websocket-requests"></a><span data-ttu-id="35d5c-149">Accepter les requêtes WebSocket</span><span class="sxs-lookup"><span data-stu-id="35d5c-149">Accept WebSocket requests</span></span>

<span data-ttu-id="35d5c-150">Un peu plus tard dans le cycle de vie de la requête (plus loin dans la méthode `Configure` ou dans une méthode action, par exemple), vérifiez s’il s’agit d’une requête WebSocket, puis acceptez-la.</span><span class="sxs-lookup"><span data-stu-id="35d5c-150">Somewhere later in the request life cycle (later in the `Configure` method or in an action method, for example) check if it's a WebSocket request and accept the WebSocket request.</span></span>

<span data-ttu-id="35d5c-151">L’exemple suivant est extrait d’une partie ultérieure de la méthode `Configure` :</span><span class="sxs-lookup"><span data-stu-id="35d5c-151">The following example is from later in the `Configure` method:</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=AcceptWebSocket&highlight=7)]

<span data-ttu-id="35d5c-152">Une requête WebSocket peut figurer sur toute URL, mais cet exemple de code accepte uniquement les requêtes pour `/ws`.</span><span class="sxs-lookup"><span data-stu-id="35d5c-152">A WebSocket request could come in on any URL, but this sample code only accepts requests for `/ws`.</span></span>

<span data-ttu-id="35d5c-153">Lorsque vous utilisez un WebSocket, vous **devez** conserver le pipeline de l’intergiciel en cours d’exécution pendant la durée de la connexion.</span><span class="sxs-lookup"><span data-stu-id="35d5c-153">When using a WebSocket, you **must** keep the middleware pipeline running for the duration of the connection.</span></span> <span data-ttu-id="35d5c-154">Si vous tentez d’envoyer ou de recevoir un message WebSocket une fois que le pipeline de l’intergiciel se termine, vous pouvez obtenir une exception comme suit :</span><span class="sxs-lookup"><span data-stu-id="35d5c-154">If you attempt to send or receive a WebSocket message after the middleware pipeline ends, you may get an exception like the following:</span></span>

```
System.Net.WebSockets.WebSocketException (0x80004005): The remote party closed the WebSocket connection without completing the close handshake. ---> System.ObjectDisposedException: Cannot write to the response body, the response has completed.
Object name: 'HttpResponseStream'.
```

<span data-ttu-id="35d5c-155">Si vous utilisez un service en arrière-plan pour écrire des données dans un WebSocket, assurez-vous que vous conservez le pipeline de l’intergiciel en cours d’exécution.</span><span class="sxs-lookup"><span data-stu-id="35d5c-155">If you're using a background service to write data to a WebSocket, make sure you keep the middleware pipeline running.</span></span> <span data-ttu-id="35d5c-156">Pour ce faire, utilisez un <xref:System.Threading.Tasks.TaskCompletionSource%601>.</span><span class="sxs-lookup"><span data-stu-id="35d5c-156">Do this by using a <xref:System.Threading.Tasks.TaskCompletionSource%601>.</span></span> <span data-ttu-id="35d5c-157">Passez le `TaskCompletionSource` à l’arrière-plan de votre service et faites appeler <xref:System.Threading.Tasks.TaskCompletionSource%601.TrySetResult%2A> lorsque vous avez terminé avec le WebSocket.</span><span class="sxs-lookup"><span data-stu-id="35d5c-157">Pass the `TaskCompletionSource` to your background service and have it call <xref:System.Threading.Tasks.TaskCompletionSource%601.TrySetResult%2A> when you finish with the WebSocket.</span></span> <span data-ttu-id="35d5c-158">Utilisez ensuite `await` sur la propriété <xref:System.Threading.Tasks.TaskCompletionSource%601.Task> durant l’envoi de la requête, comme indiqué dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="35d5c-158">Then `await` the <xref:System.Threading.Tasks.TaskCompletionSource%601.Task> property during the request, as shown in the following example:</span></span>

```csharp
app.Use(async (context, next) => {
    var socket = await context.WebSockets.AcceptWebSocketAsync();
    var socketFinishedTcs = new TaskCompletionSource<object>();

    BackgroundSocketProcessor.AddSocket(socket, socketFinishedTcs); 

    await socketFinishedTcs.Task;
});
```
<span data-ttu-id="35d5c-159">L’exception relative à la fermeture de WebSocket peut également se produire si vous effectuez un retour trop tôt à partir d’une méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="35d5c-159">The WebSocket closed exception can also happen if you return too soon from an action method.</span></span> <span data-ttu-id="35d5c-160">Si vous acceptez un socket dans une méthode d’action, attendez que le code qui l’utilise soit entièrement exécuté, avant d’effectuer un retour à partir de la méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="35d5c-160">If you accept a socket in an action method, wait for the code that uses the socket to complete before returning from the action method.</span></span>

<span data-ttu-id="35d5c-161">N’utilisez jamais `Task.Wait()`, `Task.Result` ou des appels de blocage similaires pour attendre la fin de l’exécution du socket, car cela peut entraîner de graves problèmes de thread.</span><span class="sxs-lookup"><span data-stu-id="35d5c-161">Never use `Task.Wait()`, `Task.Result`, or similar blocking calls to wait for the socket to complete, as that can cause serious threading issues.</span></span> <span data-ttu-id="35d5c-162">Utilisez toujours `await`.</span><span class="sxs-lookup"><span data-stu-id="35d5c-162">Always use `await`.</span></span>

## <a name="send-and-receive-messages"></a><span data-ttu-id="35d5c-163">Envoyer et recevoir des messages</span><span class="sxs-lookup"><span data-stu-id="35d5c-163">Send and receive messages</span></span>

<span data-ttu-id="35d5c-164">La méthode `AcceptWebSocketAsync` met à niveau la connexion TCP vers une connexion WebSocket et fournit un objet [WebSocket](/dotnet/core/api/system.net.websockets.websocket).</span><span class="sxs-lookup"><span data-stu-id="35d5c-164">The `AcceptWebSocketAsync` method upgrades the TCP connection to a WebSocket connection and provides a [WebSocket](/dotnet/core/api/system.net.websockets.websocket) object.</span></span> <span data-ttu-id="35d5c-165">Utilisez l’objet `WebSocket` pour envoyer et recevoir des messages.</span><span class="sxs-lookup"><span data-stu-id="35d5c-165">Use the `WebSocket` object to send and receive messages.</span></span>

<span data-ttu-id="35d5c-166">Le code montré précédemment qui accepte la requête WebSocket passe l’objet `WebSocket` à une méthode `Echo`.</span><span class="sxs-lookup"><span data-stu-id="35d5c-166">The code shown earlier that accepts the WebSocket request passes the `WebSocket` object to an `Echo` method.</span></span> <span data-ttu-id="35d5c-167">Le code reçoit un message et renvoie immédiatement le même message.</span><span class="sxs-lookup"><span data-stu-id="35d5c-167">The code receives a message and immediately sends back the same message.</span></span> <span data-ttu-id="35d5c-168">Les messages sont envoyés et reçus dans une boucle jusqu’à ce que le client ferme la connexion :</span><span class="sxs-lookup"><span data-stu-id="35d5c-168">Messages are sent and received in a loop until the client closes the connection:</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=Echo)]

<span data-ttu-id="35d5c-169">Quand vous acceptez le WebSocket avant de commencer cette boucle, le pipeline de middlewares se termine.</span><span class="sxs-lookup"><span data-stu-id="35d5c-169">When accepting the WebSocket connection before beginning the loop, the middleware pipeline ends.</span></span> <span data-ttu-id="35d5c-170">À la fermeture du socket, le pipeline se déroule.</span><span class="sxs-lookup"><span data-stu-id="35d5c-170">Upon closing the socket, the pipeline unwinds.</span></span> <span data-ttu-id="35d5c-171">Autrement dit, la requête cesse d’avancer dans le pipeline quand le WebSocket est accepté.</span><span class="sxs-lookup"><span data-stu-id="35d5c-171">That is, the request stops moving forward in the pipeline when the WebSocket is accepted.</span></span> <span data-ttu-id="35d5c-172">Quand la boucle est terminée et que le socket est fermé, la requête recule dans le pipeline.</span><span class="sxs-lookup"><span data-stu-id="35d5c-172">When the loop is finished and the socket is closed, the request proceeds back up the pipeline.</span></span>

::: moniker range=">= aspnetcore-2.2"

## <a name="handle-client-disconnects"></a><span data-ttu-id="35d5c-173">Gérer la déconnexion du client</span><span class="sxs-lookup"><span data-stu-id="35d5c-173">Handle client disconnects</span></span>

<span data-ttu-id="35d5c-174">Le serveur n’est pas automatiquement informé lorsque le client se déconnecte en raison d’une perte de connectivité.</span><span class="sxs-lookup"><span data-stu-id="35d5c-174">The server is not automatically informed when the client disconnects due to loss of connectivity.</span></span> <span data-ttu-id="35d5c-175">Le serveur reçoit un message de déconnexion uniquement si le client l’envoie, ce qui n’est pas possible si la connexion Internet est perdue.</span><span class="sxs-lookup"><span data-stu-id="35d5c-175">The server receives a disconnect message only if the client sends it, which can't be done if the internet connection is lost.</span></span> <span data-ttu-id="35d5c-176">Si vous souhaitez prendre des mesures quand cela se produit, définissez un délai d’attente lorsque rien n’est reçu du client dans un certain laps de temps.</span><span class="sxs-lookup"><span data-stu-id="35d5c-176">If you want to take some action when that happens, set a timeout after nothing is received from the client within a certain time window.</span></span>

<span data-ttu-id="35d5c-177">Si le client n’envoie toujours pas de messages et si vous ne souhaitez pas appliquer le délai d’attente uniquement parce que la connexion devient inactive, demandez au client d’utiliser un minuteur pour envoyer un message ping toutes les X secondes.</span><span class="sxs-lookup"><span data-stu-id="35d5c-177">If the client isn't always sending messages and you don't want to timeout just because the connection goes idle, have the client use a timer to send a ping message every X seconds.</span></span> <span data-ttu-id="35d5c-178">Sur le serveur, si un message n’arrive pas dans un délai de 2\*X secondes après le précédent, mettez fin à la connexion et signalez que le client s’est déconnecté.</span><span class="sxs-lookup"><span data-stu-id="35d5c-178">On the server, if a message hasn't arrived within 2\*X seconds after the previous one, terminate the connection and report that the client disconnected.</span></span> <span data-ttu-id="35d5c-179">Attendez deux fois la durée de l’intervalle de temps attendu pour autoriser les délais réseau qui peuvent retarder le message ping.</span><span class="sxs-lookup"><span data-stu-id="35d5c-179">Wait for twice the expected time interval to leave extra time for network delays that might hold up the ping message.</span></span>

## <a name="websocket-origin-restriction"></a><span data-ttu-id="35d5c-180">Restriction d’origine WebSocket</span><span class="sxs-lookup"><span data-stu-id="35d5c-180">WebSocket origin restriction</span></span>

<span data-ttu-id="35d5c-181">Les protections fournies par CORS ne s’appliquent pas aux WebSockets.</span><span class="sxs-lookup"><span data-stu-id="35d5c-181">The protections provided by CORS don't apply to WebSockets.</span></span> <span data-ttu-id="35d5c-182">Les navigateurs **:**</span><span class="sxs-lookup"><span data-stu-id="35d5c-182">Browsers do **not**:</span></span>

* <span data-ttu-id="35d5c-183">n’effectuent pas de requêtes préalables CORS ;</span><span class="sxs-lookup"><span data-stu-id="35d5c-183">Perform CORS pre-flight requests.</span></span>
* <span data-ttu-id="35d5c-184">respectent les restrictions spécifiées dans les en-têtes `Access-Control` quand ils effectuent des requêtes WebSocket.</span><span class="sxs-lookup"><span data-stu-id="35d5c-184">Respect the restrictions specified in `Access-Control` headers when making WebSocket requests.</span></span>

<span data-ttu-id="35d5c-185">Toutefois, les navigateurs envoient l’en-tête `Origin` au moment de l’émission des requêtes WebSocket.</span><span class="sxs-lookup"><span data-stu-id="35d5c-185">However, browsers do send the `Origin` header when issuing WebSocket requests.</span></span> <span data-ttu-id="35d5c-186">Les applications doivent être configurées de manière à valider ces en-têtes, le but étant de vérifier que seuls les WebSockets provenant des origines attendues sont autorisés.</span><span class="sxs-lookup"><span data-stu-id="35d5c-186">Applications should be configured to validate these headers to ensure that only WebSockets coming from the expected origins are allowed.</span></span>

<span data-ttu-id="35d5c-187">Si vous hébergez votre serveur sur «https://server.com» et votre client sur «https://client.com», ajoutez «https://client.com» à la liste `AllowedOrigins` pour autoriser les WebSockets.</span><span class="sxs-lookup"><span data-stu-id="35d5c-187">If you're hosting your server on "https://server.com" and hosting your client on "https://client.com", add "https://client.com" to the `AllowedOrigins` list for WebSockets to verify.</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptionsAO&highlight=6-7)]

> [!NOTE]
> <span data-ttu-id="35d5c-188">L’en-tête `Origin` est contrôlé par le client et, comme l’en-tête `Referer`, peut être falsifié.</span><span class="sxs-lookup"><span data-stu-id="35d5c-188">The `Origin` header is controlled by the client and, like the `Referer` header, can be faked.</span></span> <span data-ttu-id="35d5c-189">**N’utilisez pas** ces en-têtes comme mécanisme d’authentification.</span><span class="sxs-lookup"><span data-stu-id="35d5c-189">Do **not** use these headers as an authentication mechanism.</span></span>

::: moniker-end

## <a name="iisiis-express-support"></a><span data-ttu-id="35d5c-190">Prise en charge d’IIS/IIS Express</span><span class="sxs-lookup"><span data-stu-id="35d5c-190">IIS/IIS Express support</span></span>

<span data-ttu-id="35d5c-191">Windows Server 2012 ou ultérieur et Windows 8 ou ultérieur avec IIS/IIS Express 8 ou ultérieure prennent en charge le protocole WebSocket.</span><span class="sxs-lookup"><span data-stu-id="35d5c-191">Windows Server 2012 or later and Windows 8 or later with IIS/IIS Express 8 or later has support for the WebSocket protocol.</span></span>

> [!NOTE]
> <span data-ttu-id="35d5c-192">WebSockets sont toujours activés lorsque vous utilisez IIS Express.</span><span class="sxs-lookup"><span data-stu-id="35d5c-192">WebSockets are always enabled when using IIS Express.</span></span>

### <a name="enabling-websockets-on-iis"></a><span data-ttu-id="35d5c-193">Activation de WebSockets sur IIS</span><span class="sxs-lookup"><span data-stu-id="35d5c-193">Enabling WebSockets on IIS</span></span>

<span data-ttu-id="35d5c-194">Pour activer la prise en charge du protocole WebSocket sur Windows Server 2012 ou ultérieur :</span><span class="sxs-lookup"><span data-stu-id="35d5c-194">To enable support for the WebSocket protocol on Windows Server 2012 or later:</span></span>

> [!NOTE]
> <span data-ttu-id="35d5c-195">Ces étapes ne sont pas nécessaires si vous utilisez IIS Express</span><span class="sxs-lookup"><span data-stu-id="35d5c-195">These steps are not required when using IIS Express</span></span>

1. <span data-ttu-id="35d5c-196">Utilisez l’Assistant **Ajouter des rôles et des fonctionnalités** par le biais du menu **Gérer** ou du lien dans **Gestionnaire de serveur**.</span><span class="sxs-lookup"><span data-stu-id="35d5c-196">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span>
1. <span data-ttu-id="35d5c-197">Sélectionnez **Installation basée sur un rôle ou une fonctionnalité**.</span><span class="sxs-lookup"><span data-stu-id="35d5c-197">Select **Role-based or Feature-based Installation**.</span></span> <span data-ttu-id="35d5c-198">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="35d5c-198">Select **Next**.</span></span>
1. <span data-ttu-id="35d5c-199">Sélectionnez le serveur approprié (le serveur local est sélectionné par défaut).</span><span class="sxs-lookup"><span data-stu-id="35d5c-199">Select the appropriate server (the local server is selected by default).</span></span> <span data-ttu-id="35d5c-200">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="35d5c-200">Select **Next**.</span></span>
1. <span data-ttu-id="35d5c-201">Développez **Serveur web (IIS)** dans l’arborescence **Rôles**, développez **Serveur Web**, puis développez **Développement d’applications**.</span><span class="sxs-lookup"><span data-stu-id="35d5c-201">Expand **Web Server (IIS)** in the **Roles** tree, expand **Web Server**, and then expand **Application Development**.</span></span>
1. <span data-ttu-id="35d5c-202">Sélectionnez **Protocole WebSocket**.</span><span class="sxs-lookup"><span data-stu-id="35d5c-202">Select **WebSocket Protocol**.</span></span> <span data-ttu-id="35d5c-203">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="35d5c-203">Select **Next**.</span></span>
1. <span data-ttu-id="35d5c-204">Si vous n’avez pas besoin d’autres fonctionnalités, sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="35d5c-204">If additional features aren't needed, select **Next**.</span></span>
1. <span data-ttu-id="35d5c-205">Sélectionnez **Installer**.</span><span class="sxs-lookup"><span data-stu-id="35d5c-205">Select **Install**.</span></span>
1. <span data-ttu-id="35d5c-206">Une fois l’installation terminée, sélectionnez **Fermer** pour quitter l’Assistant.</span><span class="sxs-lookup"><span data-stu-id="35d5c-206">When the installation completes, select **Close** to exit the wizard.</span></span>

<span data-ttu-id="35d5c-207">Pour activer la prise en charge du protocole WebSocket sur Windows 8 ou ultérieur :</span><span class="sxs-lookup"><span data-stu-id="35d5c-207">To enable support for the WebSocket protocol on Windows 8 or later:</span></span>

> [!NOTE]
> <span data-ttu-id="35d5c-208">Ces étapes ne sont pas nécessaires si vous utilisez IIS Express</span><span class="sxs-lookup"><span data-stu-id="35d5c-208">These steps are not required when using IIS Express</span></span>

1. <span data-ttu-id="35d5c-209">Naviguez vers **contrôler les**programmes de > **panneaux** > **et les fonctionnalités** > **Activez ou descendez** les fonctionnalités Windows (côté gauche de l’écran).</span><span class="sxs-lookup"><span data-stu-id="35d5c-209">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="35d5c-210">Ouvrez les nœuds suivants : **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span><span class="sxs-lookup"><span data-stu-id="35d5c-210">Open the following nodes: **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span>
1. <span data-ttu-id="35d5c-211">Sélectionnez la fonctionnalité **Protocole WebSocket**.</span><span class="sxs-lookup"><span data-stu-id="35d5c-211">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="35d5c-212">Sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="35d5c-212">Select **OK**.</span></span>

### <a name="disable-websocket-when-using-socketio-on-nodejs"></a><span data-ttu-id="35d5c-213">Désactiver WebSocket lors de l’utilisation de socket.io sur Node.js</span><span class="sxs-lookup"><span data-stu-id="35d5c-213">Disable WebSocket when using socket.io on Node.js</span></span>

<span data-ttu-id="35d5c-214">Si vous utilisez le support WebSocket dans [socket.io](https://socket.io/) sur [Node.js](https://nodejs.org/), désactiver le `webSocket` module WebSocket PAR défaut IIS en utilisant l’élément dans *web.config* ou *applicationHost.config*. Si cette étape n’est pas effectuée, le module WebSocket IIS tente de gérer la communication WebSocket plutôt que Node.js et l’application.</span><span class="sxs-lookup"><span data-stu-id="35d5c-214">If using the WebSocket support in [socket.io](https://socket.io/) on [Node.js](https://nodejs.org/), disable the default IIS WebSocket module using the `webSocket` element in *web.config* or *applicationHost.config*. If this step isn't performed, the IIS WebSocket module attempts to handle the WebSocket communication rather than Node.js and the app.</span></span>

```xml
<system.webServer>
  <webSocket enabled="false" />
</system.webServer>
```

## <a name="sample-app"></a><span data-ttu-id="35d5c-215">Exemple d'application</span><span class="sxs-lookup"><span data-stu-id="35d5c-215">Sample app</span></span>

<span data-ttu-id="35d5c-216">[L’exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) qui accompagne cet article est une application d’écho.</span><span class="sxs-lookup"><span data-stu-id="35d5c-216">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) that accompanies this article is an echo app.</span></span> <span data-ttu-id="35d5c-217">Elle comporte une page web qui établit des connexions WebSocket, et le serveur renvoie au client les messages qu’il reçoit.</span><span class="sxs-lookup"><span data-stu-id="35d5c-217">It has a web page that makes WebSocket connections, and the server resends any messages it receives back to the client.</span></span> <span data-ttu-id="35d5c-218">Exécutez l’application à partir d’une invite de commandes (elle n’est pas configurée pour s’exécuter à partir de Visual Studio avec IIS Express) et accédez à http://localhost:5000.</span><span class="sxs-lookup"><span data-stu-id="35d5c-218">Run the app from a command prompt (it's not set up to run from Visual Studio with IIS Express) and navigate to http://localhost:5000.</span></span> <span data-ttu-id="35d5c-219">La page web affiche l’état de la connexion en haut à gauche :</span><span class="sxs-lookup"><span data-stu-id="35d5c-219">The web page shows the connection status in the upper left:</span></span>

![État initial de la page web](websockets/_static/start.png)

<span data-ttu-id="35d5c-221">Sélectionnez **Connect** (Connexion) pour envoyer une requête WebSocket à l’URL affichée.</span><span class="sxs-lookup"><span data-stu-id="35d5c-221">Select **Connect** to send a WebSocket request to the URL shown.</span></span> <span data-ttu-id="35d5c-222">Entrez un message de test, puis sélectionnez **Send** (Envoyer).</span><span class="sxs-lookup"><span data-stu-id="35d5c-222">Enter a test message and select **Send**.</span></span> <span data-ttu-id="35d5c-223">Quand vous avez terminé, sélectionnez **Close Socket** (Fermer le socket).</span><span class="sxs-lookup"><span data-stu-id="35d5c-223">When done, select **Close Socket**.</span></span> <span data-ttu-id="35d5c-224">La section **Communication Log** (Journal des communications) signale chaque action d’ouverture, d’envoi et de fermeture en temps réel.</span><span class="sxs-lookup"><span data-stu-id="35d5c-224">The **Communication Log** section reports each open, send, and close action as it happens.</span></span>

![État initial de la page web](websockets/_static/end.png)

