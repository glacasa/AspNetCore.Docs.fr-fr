---
title: SignalRClient JavaScript ASP.net Core
author: bradygaster
description: Vue d’ensemble de ASP.NET Core SignalR client JavaScript.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/08/2020
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
uid: signalr/javascript-client
ms.openlocfilehash: 6fc586d144547585ef75d653bf54193def5c8b7f
ms.sourcegitcommit: d1a897ebd89daa05170ac448e4831d327f6b21a8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2020
ms.locfileid: "91606678"
---
# <a name="aspnet-core-no-locsignalr-javascript-client"></a><span data-ttu-id="a2ebb-103">SignalRClient JavaScript ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="a2ebb-103">ASP.NET Core SignalR JavaScript client</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a2ebb-104">Par [Rachel Appel](https://twitter.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="a2ebb-104">By [Rachel Appel](https://twitter.com/rachelappel)</span></span>

<span data-ttu-id="a2ebb-105">La SignalR bibliothèque cliente JavaScript ASP.net Core permet aux développeurs d’appeler le code de concentrateur côté serveur.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-105">The ASP.NET Core SignalR JavaScript client library enables developers to call server-side hub code.</span></span>

<span data-ttu-id="a2ebb-106">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a2ebb-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-no-locsignalr-client-package"></a><span data-ttu-id="a2ebb-107">Installer le SignalR package client</span><span class="sxs-lookup"><span data-stu-id="a2ebb-107">Install the SignalR client package</span></span>

<span data-ttu-id="a2ebb-108">La SignalR bibliothèque cliente JavaScript est fournie en tant que package [NPM](https://www.npmjs.com/) .</span><span class="sxs-lookup"><span data-stu-id="a2ebb-108">The SignalR JavaScript client library is delivered as an [npm](https://www.npmjs.com/) package.</span></span> <span data-ttu-id="a2ebb-109">Les sections suivantes décrivent différentes façons d’installer la bibliothèque cliente.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-109">The following sections outline different ways to install the client library.</span></span>

### <a name="install-with-npm"></a><span data-ttu-id="a2ebb-110">Installer avec NPM</span><span class="sxs-lookup"><span data-stu-id="a2ebb-110">Install with npm</span></span>

<span data-ttu-id="a2ebb-111">Pour Visual Studio, exécutez les commandes suivantes à partir de la **console du gestionnaire de package** dans le dossier racine.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-111">For Visual Studio, run the following commands from **Package Manager Console** while in the root folder.</span></span> <span data-ttu-id="a2ebb-112">Pour Visual Studio Code, exécutez les commandes suivantes à partir du **Terminal intégré**.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-112">For Visual Studio Code, run the following commands from the **Integrated Terminal**.</span></span>

```bash
npm init -y
npm install @microsoft/signalr
```

<span data-ttu-id="a2ebb-113">NPM installe le contenu du package dans le dossier \*node_modules \\ @microsoft\signalr\dist\browser \* .</span><span class="sxs-lookup"><span data-stu-id="a2ebb-113">npm installs the package contents in the *node_modules\\@microsoft\signalr\dist\browser* folder.</span></span> <span data-ttu-id="a2ebb-114">Créez un nouveau dossier nommé *signalr* sous le dossier de la \* \\ bibliothèque wwwroot\* .</span><span class="sxs-lookup"><span data-stu-id="a2ebb-114">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="a2ebb-115">Copiez le fichier *signalr.js* dans le dossier *wwwroot\lib\signalr*</span><span class="sxs-lookup"><span data-stu-id="a2ebb-115">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

<span data-ttu-id="a2ebb-116">Référencez le SignalR client JavaScript dans l' `<script>` élément.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-116">Reference the SignalR JavaScript client in the `<script>` element.</span></span> <span data-ttu-id="a2ebb-117">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="a2ebb-117">For example:</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a><span data-ttu-id="a2ebb-118">Utiliser un réseau de distribution de contenu (CDN)</span><span class="sxs-lookup"><span data-stu-id="a2ebb-118">Use a Content Delivery Network (CDN)</span></span>

<span data-ttu-id="a2ebb-119">Pour utiliser la bibliothèque cliente sans le composant requis NPM, référencez une copie hébergée par CDN de la bibliothèque cliente.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-119">To use the client library without the npm prerequisite, reference a CDN-hosted copy of the client library.</span></span> <span data-ttu-id="a2ebb-120">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="a2ebb-120">For example:</span></span>

[!code-html[](javascript-client/samples/3.x/SignalRChat/Pages/Index.cshtml?name=snippet_CDN)]

<span data-ttu-id="a2ebb-121">La bibliothèque cliente est disponible sur le CDN suivant :</span><span class="sxs-lookup"><span data-stu-id="a2ebb-121">The client library is available on the following CDNs:</span></span>

* [<span data-ttu-id="a2ebb-122">cdnjs</span><span class="sxs-lookup"><span data-stu-id="a2ebb-122">cdnjs</span></span>](https://cdnjs.com/libraries/microsoft-signalr)
* [<span data-ttu-id="a2ebb-123">jsDelivr</span><span class="sxs-lookup"><span data-stu-id="a2ebb-123">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@microsoft/signalr)
* [<span data-ttu-id="a2ebb-124">unpkg</span><span class="sxs-lookup"><span data-stu-id="a2ebb-124">unpkg</span></span>](https://unpkg.com/@microsoft/signalr@next/dist/browser/signalr.min.js)

### <a name="install-with-libman"></a><span data-ttu-id="a2ebb-125">Installer avec LibMan</span><span class="sxs-lookup"><span data-stu-id="a2ebb-125">Install with LibMan</span></span>

<span data-ttu-id="a2ebb-126">[LibMan](xref:client-side/libman/index) peut être utilisé pour installer des fichiers de bibliothèque cliente spécifiques à partir de la bibliothèque cliente hébergée par CDN.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-126">[LibMan](xref:client-side/libman/index) can be used to install specific client library files from the CDN-hosted client library.</span></span> <span data-ttu-id="a2ebb-127">Par exemple, ajoutez uniquement le fichier JavaScript minimisés au projet.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-127">For example, only add the minified JavaScript file to the project.</span></span> <span data-ttu-id="a2ebb-128">Pour plus d’informations sur cette approche, consultez [Ajouter la SignalR bibliothèque cliente](xref:tutorials/signalr#add-the-signalr-client-library).</span><span class="sxs-lookup"><span data-stu-id="a2ebb-128">For details on that approach, see [Add the SignalR client library](xref:tutorials/signalr#add-the-signalr-client-library).</span></span>

## <a name="connect-to-a-hub"></a><span data-ttu-id="a2ebb-129">Se connecter à un concentrateur</span><span class="sxs-lookup"><span data-stu-id="a2ebb-129">Connect to a hub</span></span>

<span data-ttu-id="a2ebb-130">Le code suivant crée et démarre une connexion.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-130">The following code creates and starts a connection.</span></span> <span data-ttu-id="a2ebb-131">Le nom du concentrateur ne respecte pas la casse :</span><span class="sxs-lookup"><span data-stu-id="a2ebb-131">The hub's name is case insensitive:</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?range=3-6,29-43)]

### <a name="cross-origin-connections"></a><span data-ttu-id="a2ebb-132">Connexions Cross-Origin</span><span class="sxs-lookup"><span data-stu-id="a2ebb-132">Cross-origin connections</span></span>

<span data-ttu-id="a2ebb-133">En règle générale, les navigateurs chargent les connexions à partir du même domaine que la page demandée.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-133">Typically, browsers load connections from the same domain as the requested page.</span></span> <span data-ttu-id="a2ebb-134">Toutefois, il peut arriver qu’une connexion à un autre domaine soit nécessaire.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-134">However, there are occasions when a connection to another domain is required.</span></span>

<span data-ttu-id="a2ebb-135">Pour empêcher un site malveillant de lire des données sensibles à partir d’un autre site, les [connexions Cross-Origin](xref:security/cors) sont désactivées par défaut.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-135">To prevent a malicious site from reading sensitive data from another site, [cross-origin connections](xref:security/cors) are disabled by default.</span></span> <span data-ttu-id="a2ebb-136">Pour autoriser une demande Cross-Origin, activez-la dans la `Startup` classe :</span><span class="sxs-lookup"><span data-stu-id="a2ebb-136">To allow a cross-origin request, enable it in the `Startup` class:</span></span>

[!code-csharp[](javascript-client/samples/3.x/SignalRChat/Startup.cs?highlight=16-23,40)]

## <a name="call-hub-methods-from-the-client"></a><span data-ttu-id="a2ebb-137">Appeler les méthodes de concentrateur à partir du client</span><span class="sxs-lookup"><span data-stu-id="a2ebb-137">Call hub methods from the client</span></span>

<span data-ttu-id="a2ebb-138">Les clients JavaScript appellent des méthodes publiques sur des hubs à l’aide de la méthode [Invoke](/javascript/api/%40microsoft/signalr/hubconnection#invoke-string--any---) de [HubConnection](/javascript/api/%40microsoft/signalr/hubconnection).</span><span class="sxs-lookup"><span data-stu-id="a2ebb-138">JavaScript clients call public methods on hubs via the [invoke](/javascript/api/%40microsoft/signalr/hubconnection#invoke-string--any---) method of the [HubConnection](/javascript/api/%40microsoft/signalr/hubconnection).</span></span> <span data-ttu-id="a2ebb-139">La `invoke` méthode accepte les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="a2ebb-139">The `invoke` method accepts:</span></span>

* <span data-ttu-id="a2ebb-140">Nom de la méthode de concentrateur.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-140">The name of the hub method.</span></span>
* <span data-ttu-id="a2ebb-141">Tous les arguments définis dans la méthode de concentrateur.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-141">Any arguments defined in the hub method.</span></span>

<span data-ttu-id="a2ebb-142">Dans l’exemple suivant, le nom de la méthode sur le Hub est `SendMessage` .</span><span class="sxs-lookup"><span data-stu-id="a2ebb-142">In the following example, the method name on the hub is `SendMessage`.</span></span> <span data-ttu-id="a2ebb-143">Le deuxième et le troisième argument passés pour `invoke` mapper aux arguments et de la méthode de concentrateur `user` `message` :</span><span class="sxs-lookup"><span data-stu-id="a2ebb-143">The second and third arguments passed to `invoke` map to the hub method's `user` and `message` arguments:</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?name=snippet_Invoke&highlight=2)]

> [!NOTE]
> <span data-ttu-id="a2ebb-144">L’appel de méthodes de Hub à partir d’un client est pris en charge uniquement lors de l’utilisation du SignalR service Azure en mode *par défaut* .</span><span class="sxs-lookup"><span data-stu-id="a2ebb-144">Calling hub methods from a client is only supported when using the Azure SignalR Service in *Default* mode.</span></span> <span data-ttu-id="a2ebb-145">Pour plus d’informations, consultez [Forum aux questions (référentiel GitHub Azure-signalr)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span><span class="sxs-lookup"><span data-stu-id="a2ebb-145">For more information, see [Frequently Asked Questions (azure-signalr GitHub repository)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span></span>

<span data-ttu-id="a2ebb-146">La `invoke` méthode retourne une [promesse](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)JavaScript.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-146">The `invoke` method returns a JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span></span> <span data-ttu-id="a2ebb-147">Le `Promise` est résolu avec la valeur de retour (le cas échéant) lorsque la méthode sur le serveur retourne.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-147">The `Promise` is resolved with the return value (if any) when the method on the server returns.</span></span> <span data-ttu-id="a2ebb-148">Si la méthode sur le serveur génère une erreur, `Promise` est rejetée avec le message d’erreur.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-148">If the method on the server throws an error, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="a2ebb-149">Utilisez `async` `await` les méthodes et `Promise` et `then` `catch` pour gérer ces cas.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-149">Use `async` and `await` or the `Promise`'s `then` and `catch` methods to handle these cases.</span></span>

<span data-ttu-id="a2ebb-150">Les clients JavaScript peuvent également appeler des méthodes publiques sur des hubs à l’aide de la méthode [Send](/javascript/api/%40microsoft/signalr/hubconnection#send-string--any---) de `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="a2ebb-150">JavaScript clients can also call public methods on hubs via the the [send](/javascript/api/%40microsoft/signalr/hubconnection#send-string--any---) method of the `HubConnection`.</span></span> <span data-ttu-id="a2ebb-151">Contrairement à la `invoke` méthode, la `send` méthode n’attend pas de réponse du serveur.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-151">Unlike the `invoke` method, the `send` method doesn't wait for a response from the server.</span></span> <span data-ttu-id="a2ebb-152">La `send` méthode retourne un JavaScript `Promise` .</span><span class="sxs-lookup"><span data-stu-id="a2ebb-152">The `send` method returns a JavaScript `Promise`.</span></span> <span data-ttu-id="a2ebb-153">Le `Promise` est résolu lorsque le message a été envoyé au serveur.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-153">The `Promise` is resolved when the message has been sent to the server.</span></span> <span data-ttu-id="a2ebb-154">En cas d’erreur lors de l’envoi du message, `Promise` est rejeté avec le message d’erreur.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-154">If there is an error sending the message, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="a2ebb-155">Utilisez `async` `await` les méthodes et `Promise` et `then` `catch` pour gérer ces cas.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-155">Use `async` and `await` or the `Promise`'s `then` and `catch` methods to handle these cases.</span></span>

> [!NOTE]
> <span data-ttu-id="a2ebb-156">L’utilisation de `send` n’attend pas que le serveur ait reçu le message.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-156">Using `send` doesn't wait until the server has received the message.</span></span> <span data-ttu-id="a2ebb-157">Par conséquent, il n’est pas possible de retourner des données ou des erreurs à partir du serveur.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-157">Consequently, it's not possible to return data or errors from the server.</span></span>

## <a name="call-client-methods-from-the-hub"></a><span data-ttu-id="a2ebb-158">Appeler les méthodes du client à partir du Hub</span><span class="sxs-lookup"><span data-stu-id="a2ebb-158">Call client methods from the hub</span></span>

<span data-ttu-id="a2ebb-159">Pour recevoir des messages à partir du concentrateur, définissez une méthode à l’aide de la méthode [on](/javascript/api/%40microsoft/signalr/hubconnection#on-string---args--any-------void-) de `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="a2ebb-159">To receive messages from the hub, define a method using the [on](/javascript/api/%40microsoft/signalr/hubconnection#on-string---args--any-------void-) method of the `HubConnection`.</span></span>

* <span data-ttu-id="a2ebb-160">Nom de la méthode du client JavaScript.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-160">The name of the JavaScript client method.</span></span>
* <span data-ttu-id="a2ebb-161">Arguments que le Hub transmet à la méthode.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-161">Arguments the hub passes to the method.</span></span>

<span data-ttu-id="a2ebb-162">Dans l’exemple suivant, le nom de la méthode est `ReceiveMessage` .</span><span class="sxs-lookup"><span data-stu-id="a2ebb-162">In the following example, the method name is `ReceiveMessage`.</span></span> <span data-ttu-id="a2ebb-163">Les noms d’arguments sont `user` et `message` :</span><span class="sxs-lookup"><span data-stu-id="a2ebb-163">The argument names are `user` and `message`:</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?name=snippet_ReceiveMessage)]

<span data-ttu-id="a2ebb-164">Le code précédent dans `connection.on` s’exécute quand le code côté serveur l’appelle à l’aide de la <xref:Microsoft.AspNetCore.SignalR.ClientProxyExtensions.SendAsync%2A> méthode :</span><span class="sxs-lookup"><span data-stu-id="a2ebb-164">The preceding code in `connection.on` runs when server-side code calls it using the <xref:Microsoft.AspNetCore.SignalR.ClientProxyExtensions.SendAsync%2A> method:</span></span>

[!code-csharp[Call client-side](javascript-client/samples/3.x/SignalRChat/Hubs/ChatHub.cs?name=snippet_SendMessage)]

<span data-ttu-id="a2ebb-165">SignalR détermine la méthode cliente à appeler en faisant correspondre le nom de la méthode et les arguments définis dans `SendAsync` et `connection.on` .</span><span class="sxs-lookup"><span data-stu-id="a2ebb-165">SignalR determines which client method to call by matching the method name and arguments defined in `SendAsync` and `connection.on`.</span></span>

> [!NOTE]
> <span data-ttu-id="a2ebb-166">Il est recommandé d’appeler la méthode [Start](/javascript/api/%40aspnet/signalr/hubconnection#start) sur le `HubConnection` after `on` .</span><span class="sxs-lookup"><span data-stu-id="a2ebb-166">As a best practice, call the [start](/javascript/api/%40aspnet/signalr/hubconnection#start) method on the `HubConnection` after `on`.</span></span> <span data-ttu-id="a2ebb-167">Cela permet de s’assurer que vos gestionnaires sont inscrits avant la réception de tous les messages.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-167">Doing so ensures your handlers are registered before any messages are received.</span></span>

## <a name="error-handling-and-logging"></a><span data-ttu-id="a2ebb-168">Gestion et journalisation des erreurs</span><span class="sxs-lookup"><span data-stu-id="a2ebb-168">Error handling and logging</span></span>

<span data-ttu-id="a2ebb-169">Utilisez `try` et `catch` avec `async` et `await` ou la `Promise` `catch` méthode de pour gérer les erreurs côté client.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-169">Use `try` and `catch` with `async` and `await` or the `Promise`'s `catch` method to handle client-side errors.</span></span> <span data-ttu-id="a2ebb-170">Utilisez `console.error` pour générer des erreurs dans la console du navigateur :</span><span class="sxs-lookup"><span data-stu-id="a2ebb-170">Use `console.error` to output errors to the browser's console:</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?name=snippet_Invoke&highlight=1,3-5)]

<span data-ttu-id="a2ebb-171">Configurez le suivi des journaux côté client en passant un enregistreur d’événements et un type d’événement à consigner lorsque la connexion est établie.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-171">Set up client-side log tracing by passing a logger and type of event to log when the connection is made.</span></span> <span data-ttu-id="a2ebb-172">Les messages sont enregistrés avec le niveau de journalisation spécifié et supérieur.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-172">Messages are logged with the specified log level and higher.</span></span> <span data-ttu-id="a2ebb-173">Les niveaux de journalisation disponibles sont les suivants :</span><span class="sxs-lookup"><span data-stu-id="a2ebb-173">Available log levels are as follows:</span></span>

* <span data-ttu-id="a2ebb-174">`signalR.LogLevel.Error`: Messages d’erreur.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-174">`signalR.LogLevel.Error`: Error messages.</span></span> <span data-ttu-id="a2ebb-175">Journalise `Error` uniquement les messages.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-175">Logs `Error` messages only.</span></span>
* <span data-ttu-id="a2ebb-176">`signalR.LogLevel.Warning`: Messages d’avertissement concernant les erreurs potentielles.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-176">`signalR.LogLevel.Warning`: Warning messages about potential errors.</span></span> <span data-ttu-id="a2ebb-177">Journaux `Warning` et `Error` messages.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-177">Logs `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="a2ebb-178">`signalR.LogLevel.Information`: Messages d’État sans erreurs.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-178">`signalR.LogLevel.Information`: Status messages without errors.</span></span> <span data-ttu-id="a2ebb-179">Journalise `Information` `Warning` `Error` les messages, et.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-179">Logs `Information`, `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="a2ebb-180">`signalR.LogLevel.Trace`: Messages de trace.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-180">`signalR.LogLevel.Trace`: Trace messages.</span></span> <span data-ttu-id="a2ebb-181">Journalise tout, y compris les données transférées entre le Hub et le client.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-181">Logs everything, including data transported between hub and client.</span></span>

<span data-ttu-id="a2ebb-182">Utilisez la méthode [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) sur [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) pour configurer le niveau de journalisation.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-182">Use the [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) to configure the log level.</span></span> <span data-ttu-id="a2ebb-183">Les messages sont enregistrés dans la console du navigateur :</span><span class="sxs-lookup"><span data-stu-id="a2ebb-183">Messages are logged to the browser console:</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?name=snippet_Connection&highlight=3)]

## <a name="reconnect-clients"></a><span data-ttu-id="a2ebb-184">Reconnecter les clients</span><span class="sxs-lookup"><span data-stu-id="a2ebb-184">Reconnect clients</span></span>

### <a name="automatically-reconnect"></a><span data-ttu-id="a2ebb-185">Se reconnecter automatiquement</span><span class="sxs-lookup"><span data-stu-id="a2ebb-185">Automatically reconnect</span></span>

<span data-ttu-id="a2ebb-186">Le client JavaScript pour SignalR peut être configuré pour se reconnecter automatiquement à l’aide de la `withAutomaticReconnect` méthode sur [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="a2ebb-186">The JavaScript client for SignalR can be configured to automatically reconnect using the `withAutomaticReconnect` method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span></span> <span data-ttu-id="a2ebb-187">Par défaut, il ne se reconnectera pas automatiquement.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-187">It won't automatically reconnect by default.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="a2ebb-188">Sans aucun paramètre, `withAutomaticReconnect()` configure le client pour attendre 0, 2, 10 et 30 secondes, respectivement, avant d’essayer chaque tentative de reconnexion, en s’arrêtant après quatre tentatives ayant échoué.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-188">Without any parameters, `withAutomaticReconnect()` configures the client to wait 0, 2, 10, and 30 seconds respectively before trying each reconnect attempt, stopping after four failed attempts.</span></span>

<span data-ttu-id="a2ebb-189">Avant de commencer les tentatives de reconnexion, le `HubConnection` passe à l' `HubConnectionState.Reconnecting` État et déclenche ses `onreconnecting` rappels au lieu de passer à l' `Disconnected` État et de déclencher ses `onclose` rappels comme un `HubConnection` sans reconnexion automatique configurée.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-189">Before starting any reconnect attempts, the `HubConnection` will transition to the `HubConnectionState.Reconnecting` state and fire its `onreconnecting` callbacks instead of transitioning to the `Disconnected` state and triggering its `onclose` callbacks like a `HubConnection` without automatic reconnect configured.</span></span> <span data-ttu-id="a2ebb-190">Cela permet d’avertir les utilisateurs que la connexion a été perdue et de désactiver les éléments d’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-190">This provides an opportunity to warn users that the connection has been lost and to disable UI elements.</span></span>

```javascript
connection.onreconnecting(error => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="a2ebb-191">Si le client se reconnecte correctement dans les quatre premières tentatives, le `HubConnection` passe à l' `Connected` État et déclenche ses `onreconnected` rappels.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-191">If the client successfully reconnects within its first four attempts, the `HubConnection` will transition back to the `Connected` state and fire its `onreconnected` callbacks.</span></span> <span data-ttu-id="a2ebb-192">Cela permet d’informer les utilisateurs que la connexion a été rétablie.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-192">This provides an opportunity to inform users the connection has been reestablished.</span></span>

<span data-ttu-id="a2ebb-193">Étant donné que la connexion semble entièrement nouvelle sur le serveur, un nouveau `connectionId` sera fourni au `onreconnected` rappel.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-193">Since the connection looks entirely new to the server, a new `connectionId` will be provided to the `onreconnected` callback.</span></span>

> [!WARNING]
> <span data-ttu-id="a2ebb-194">Le `onreconnected` paramètre du rappel n’est pas `connectionId` défini si le `HubConnection` a été configuré pour [ignorer la négociation](xref:signalr/configuration#configure-client-options).</span><span class="sxs-lookup"><span data-stu-id="a2ebb-194">The `onreconnected` callback's `connectionId` parameter will be undefined if the `HubConnection` was configured to [skip negotiation](xref:signalr/configuration#configure-client-options).</span></span>

```javascript
connection.onreconnected(connectionId => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="a2ebb-195">`withAutomaticReconnect()` ne configure pas le `HubConnection` pour réessayer les échecs de démarrage initial. les échecs de démarrage doivent donc être gérés manuellement :</span><span class="sxs-lookup"><span data-stu-id="a2ebb-195">`withAutomaticReconnect()` won't configure the `HubConnection` to retry initial start failures, so start failures need to be handled manually:</span></span>

```javascript
async function start() {
    try {
        await connection.start();
        console.assert(connection.state === signalR.HubConnectionState.Connected);
        console.log("SignalR Connected.");
    } catch (err) {
        console.assert(connection.state === signalR.HubConnectionState.Disconnected);
        console.log(err);
        setTimeout(() => start(), 5000);
    }
};
```

<span data-ttu-id="a2ebb-196">Si le client ne se reconnecte pas correctement dans les quatre premières tentatives, le `HubConnection` passe à l' `Disconnected` État et déclenche ses rappels [OnClose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) .</span><span class="sxs-lookup"><span data-stu-id="a2ebb-196">If the client doesn't successfully reconnect within its first four attempts, the `HubConnection` will transition to the `Disconnected` state and fire its [onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) callbacks.</span></span> <span data-ttu-id="a2ebb-197">Cela permet d’informer les utilisateurs que la connexion a été perdue définitivement et de recommander l’actualisation de la page :</span><span class="sxs-lookup"><span data-stu-id="a2ebb-197">This provides an opportunity to inform users the connection has been permanently lost and recommend refreshing the page:</span></span>

```javascript
connection.onclose(error => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="a2ebb-198">Pour configurer un nombre personnalisé de tentatives de reconnexion avant de vous déconnecter ou de modifier le délai de reconnexion, `withAutomaticReconnect` accepte un tableau de nombres représentant le délai en millisecondes à attendre avant de démarrer chaque tentative de reconnexion.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-198">In order to configure a custom number of reconnect attempts before disconnecting or change the reconnect timing, `withAutomaticReconnect` accepts an array of numbers representing the delay in milliseconds to wait before starting each reconnect attempt.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

<span data-ttu-id="a2ebb-199">L’exemple précédent configure le `HubConnection` pour qu’il commence à tenter de se reconnecter immédiatement après la perte de la connexion.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-199">The preceding example configures the `HubConnection` to start attempting reconnects immediately after the connection is lost.</span></span> <span data-ttu-id="a2ebb-200">Cela est également vrai pour la configuration par défaut.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-200">This is also true for the default configuration.</span></span>

<span data-ttu-id="a2ebb-201">Si la première tentative de reconnexion échoue, la deuxième tentative de reconnexion démarre également immédiatement au lieu d’attendre 2 secondes comme dans la configuration par défaut.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-201">If the first reconnect attempt fails, the second reconnect attempt will also start immediately instead of waiting 2 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="a2ebb-202">Si la deuxième tentative de reconnexion échoue, la troisième tentative de reconnexion démarrera dans 10 secondes, ce qui revient à la configuration par défaut.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-202">If the second reconnect attempt fails, the third reconnect attempt will start in 10 seconds which is again like the default configuration.</span></span>

<span data-ttu-id="a2ebb-203">Le comportement personnalisé divergent ensuite du comportement par défaut en s’arrêtant après le troisième échec de tentative de reconnexion au lieu d’essayer une nouvelle tentative de reconnexion dans 30 secondes, comme dans la configuration par défaut.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-203">The custom behavior then diverges again from the default behavior by stopping after the third reconnect attempt failure instead of trying one more reconnect attempt in another 30 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="a2ebb-204">Si vous souhaitez encore plus de contrôle sur le minutage et le nombre de tentatives de reconnexion automatique, `withAutomaticReconnect` accepte un objet qui implémente l' `IRetryPolicy` interface, qui a une méthode unique nommée `nextRetryDelayInMilliseconds` .</span><span class="sxs-lookup"><span data-stu-id="a2ebb-204">If you want even more control over the timing and number of automatic reconnect attempts, `withAutomaticReconnect` accepts an object implementing the `IRetryPolicy` interface, which has a single method named `nextRetryDelayInMilliseconds`.</span></span>

<span data-ttu-id="a2ebb-205">`nextRetryDelayInMilliseconds` accepte un seul argument avec le type `RetryContext` .</span><span class="sxs-lookup"><span data-stu-id="a2ebb-205">`nextRetryDelayInMilliseconds` takes a single argument with the type `RetryContext`.</span></span> <span data-ttu-id="a2ebb-206">`RetryContext`A trois propriétés : `previousRetryCount` , `elapsedMilliseconds` et `retryReason` qui sont respectivement un `number` , un `number` et un `Error` .</span><span class="sxs-lookup"><span data-stu-id="a2ebb-206">The `RetryContext` has three properties: `previousRetryCount`, `elapsedMilliseconds` and `retryReason` which are a `number`, a `number` and an `Error` respectively.</span></span> <span data-ttu-id="a2ebb-207">Avant la première tentative de reconnexion, `previousRetryCount` et est égal à zéro et est `elapsedMilliseconds` `retryReason` l’erreur qui a provoqué la perte de la connexion.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-207">Before the first reconnect attempt, both `previousRetryCount` and `elapsedMilliseconds` will be zero, and the `retryReason` will be the Error that caused the connection to be lost.</span></span> <span data-ttu-id="a2ebb-208">Après chaque nouvelle tentative d’échec, est `previousRetryCount` incrémenté d’une unité, est `elapsedMilliseconds` mis à jour pour refléter le temps passé à se reconnecter jusqu’à présent en millisecondes, et est l' `retryReason` erreur qui a provoqué l’échec de la dernière tentative de reconnexion.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-208">After each failed retry attempt, `previousRetryCount` will be incremented by one, `elapsedMilliseconds` will be updated to reflect the amount of time spent reconnecting so far in milliseconds, and the `retryReason` will be the Error that caused the last reconnect attempt to fail.</span></span>

<span data-ttu-id="a2ebb-209">`nextRetryDelayInMilliseconds` doit retourner un nombre représentant le nombre de millisecondes à attendre avant la tentative de reconnexion suivante ou `null` si `HubConnection` doit arrêter la reconnexion.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-209">`nextRetryDelayInMilliseconds` must return either a number representing the number of milliseconds to wait before the next reconnect attempt or `null` if the `HubConnection` should stop reconnecting.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect({
        nextRetryDelayInMilliseconds: retryContext => {
            if (retryContext.elapsedMilliseconds < 60000) {
                // If we've been reconnecting for less than 60 seconds so far,
                // wait between 0 and 10 seconds before the next reconnect attempt.
                return Math.random() * 10000;
            } else {
                // If we've been reconnecting for more than 60 seconds so far, stop reconnecting.
                return null;
            }
        }
    })
    .build();
```

<span data-ttu-id="a2ebb-210">Vous pouvez également écrire du code qui reconnectera votre client manuellement, comme illustré dans [reconnexion manuelle](#manually-reconnect).</span><span class="sxs-lookup"><span data-stu-id="a2ebb-210">Alternatively, you can write code that will reconnect your client manually as demonstrated in [Manually reconnect](#manually-reconnect).</span></span>

### <a name="manually-reconnect"></a><span data-ttu-id="a2ebb-211">Reconnexion manuelle</span><span class="sxs-lookup"><span data-stu-id="a2ebb-211">Manually reconnect</span></span>

<span data-ttu-id="a2ebb-212">Le code suivant illustre une approche de reconnexion manuelle classique :</span><span class="sxs-lookup"><span data-stu-id="a2ebb-212">The following code demonstrates a typical manual reconnection approach:</span></span>

1. <span data-ttu-id="a2ebb-213">Une fonction (dans ce cas, la `start` fonction) est créée pour démarrer la connexion.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-213">A function (in this case, the `start` function) is created to start the connection.</span></span>
1. <span data-ttu-id="a2ebb-214">Appelez la `start` fonction dans le gestionnaire d’événements de la connexion `onclose` .</span><span class="sxs-lookup"><span data-stu-id="a2ebb-214">Call the `start` function in the connection's `onclose` event handler.</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?range=30-40)]

<span data-ttu-id="a2ebb-215">Une implémentation réelle utilise une interruption exponentielle ou une nouvelle tentative un nombre spécifié de fois avant d’abandonner.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-215">A real-world implementation would use an exponential back-off or retry a specified number of times before giving up.</span></span>

## <a name="troubleshoot-websocket-handshake-errors"></a><span data-ttu-id="a2ebb-216">Résoudre les erreurs de protocole de transfert WebSocket</span><span class="sxs-lookup"><span data-stu-id="a2ebb-216">Troubleshoot WebSocket handshake errors</span></span>

<span data-ttu-id="a2ebb-217">Cette section fournit de l’aide sur l’exception *« erreur durant le protocole de transfert WebSocket »* qui se produit lors de la tentative d’établissement d’une connexion à ASP.net Core SignalR Hub.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-217">This section provides help on the *"Error during WebSocket handshake"* exception that occurs when trying to establish connection to ASP.NET Core SignalR hub.</span></span>

### <a name="response-code-400-or-503"></a><span data-ttu-id="a2ebb-218">Code de réponse 400 ou 503</span><span class="sxs-lookup"><span data-stu-id="a2ebb-218">Response code 400 or 503</span></span>

<span data-ttu-id="a2ebb-219">Pour l’erreur suivante :</span><span class="sxs-lookup"><span data-stu-id="a2ebb-219">For the following error:</span></span>

```log
WebSocket connection to 'wss://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 400

Error: Failed to start the connection: Error: There was an error with the transport.
```

<span data-ttu-id="a2ebb-220">L’erreur est généralement due au fait que le client utilise uniquement le transport WebSockets, mais que le protocole WebSocket n’est pas activé sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-220">The error is usually caused by client only use WebSockets transport but WebSockets protocol is not enabled on server.</span></span>

### <a name="response-code-307"></a><span data-ttu-id="a2ebb-221">Code de réponse 307</span><span class="sxs-lookup"><span data-stu-id="a2ebb-221">Response code 307</span></span>

```log
WebSocket connection to 'ws://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 307
```

<span data-ttu-id="a2ebb-222">Cela se produit souvent lorsque le SignalR serveur Hub :</span><span class="sxs-lookup"><span data-stu-id="a2ebb-222">Frequently this occurs when the SignalR hub server:</span></span>

* <span data-ttu-id="a2ebb-223">Écoute et répond à la fois sur HTTP et HTTPs.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-223">Listens to and responds over both HTTP and HTTPS.</span></span>
* <span data-ttu-id="a2ebb-224">Est configuré pour appliquer le protocole HTTPs en appelant `UseHttpsRedirection` `Startup` , ou applique le protocole HTTPS via une règle de réécriture d’URL.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-224">Is configured to enforce HTTPS by calling `UseHttpsRedirection` in `Startup`, or enforces HTTPS via URL rewrite rule.</span></span>

<span data-ttu-id="a2ebb-225">Cette erreur peut être causée par la spécification de l’URL HTTP côté client à l’aide de `.withUrl("http://xxx/HubName")` .</span><span class="sxs-lookup"><span data-stu-id="a2ebb-225">This error can be caused by specifying the HTTP URL on client side using `.withUrl("http://xxx/HubName")`.</span></span> <span data-ttu-id="a2ebb-226">Le correctif pour ce cas consiste à modifier le code pour utiliser une URL HTTPs.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-226">The fix for this case is modifying the code to use an HTTPS URL.</span></span>

### <a name="response-code-404"></a><span data-ttu-id="a2ebb-227">Code de réponse 404</span><span class="sxs-lookup"><span data-stu-id="a2ebb-227">Response code 404</span></span>

```log
WebSocket connection to 'wss://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 404
```

<span data-ttu-id="a2ebb-228">Si l’application fonctionne sur localhost, mais retourne cette erreur après la publication sur le serveur IIS :</span><span class="sxs-lookup"><span data-stu-id="a2ebb-228">If the app works on localhost, but returns this error after publishing to IIS server:</span></span>

* <span data-ttu-id="a2ebb-229">Vérifiez que l’application ASP.NET Core SignalR est hébergée en tant que sous-application IIS.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-229">Verify the ASP.NET Core SignalR app is hosted as an IIS sub-application.</span></span>
* <span data-ttu-id="a2ebb-230">Ne définissez pas l’URL avec le pathbase de la sous-application sur le SignalR côté client JavaScript `.withUrl("/SubAppName/HubName")` .</span><span class="sxs-lookup"><span data-stu-id="a2ebb-230">Don't set URL with the sub-app's pathbase on SignalR JavaScript client side `.withUrl("/SubAppName/HubName")`.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a2ebb-231">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="a2ebb-231">Additional resources</span></span>

* [<span data-ttu-id="a2ebb-232">Informations de référence sur l’API JavaScript</span><span class="sxs-lookup"><span data-stu-id="a2ebb-232">JavaScript API reference</span></span>](/javascript/api/?view=signalr-js-latest&preserve-view=true )
* [<span data-ttu-id="a2ebb-233">Didacticiel JavaScript</span><span class="sxs-lookup"><span data-stu-id="a2ebb-233">JavaScript tutorial</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="a2ebb-234">Didacticiel WebPack et machine à écrire</span><span class="sxs-lookup"><span data-stu-id="a2ebb-234">WebPack and TypeScript tutorial</span></span>](xref:tutorials/signalr-typescript-webpack)
* [<span data-ttu-id="a2ebb-235">Hubs</span><span class="sxs-lookup"><span data-stu-id="a2ebb-235">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="a2ebb-236">Client .NET</span><span class="sxs-lookup"><span data-stu-id="a2ebb-236">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="a2ebb-237">Publication dans Azure</span><span class="sxs-lookup"><span data-stu-id="a2ebb-237">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="a2ebb-238">Requêtes Cross-Origin (CORS)</span><span class="sxs-lookup"><span data-stu-id="a2ebb-238">Cross-Origin Requests (CORS)</span></span>](xref:security/cors)
* [<span data-ttu-id="a2ebb-239">SignalRDocumentation sans serveur de service Azure</span><span class="sxs-lookup"><span data-stu-id="a2ebb-239">Azure SignalR Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a2ebb-240">Par [Rachel Appel](https://twitter.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="a2ebb-240">By [Rachel Appel](https://twitter.com/rachelappel)</span></span>

<span data-ttu-id="a2ebb-241">La SignalR bibliothèque cliente JavaScript ASP.net Core permet aux développeurs d’appeler le code de concentrateur côté serveur.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-241">The ASP.NET Core SignalR JavaScript client library enables developers to call server-side hub code.</span></span>

<span data-ttu-id="a2ebb-242">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a2ebb-242">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-no-locsignalr-client-package"></a><span data-ttu-id="a2ebb-243">Installer le SignalR package client</span><span class="sxs-lookup"><span data-stu-id="a2ebb-243">Install the SignalR client package</span></span>

<span data-ttu-id="a2ebb-244">La SignalR bibliothèque cliente JavaScript est fournie en tant que package [NPM](https://www.npmjs.com/) .</span><span class="sxs-lookup"><span data-stu-id="a2ebb-244">The SignalR JavaScript client library is delivered as an [npm](https://www.npmjs.com/) package.</span></span> <span data-ttu-id="a2ebb-245">Les sections suivantes décrivent différentes façons d’installer la bibliothèque cliente.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-245">The following sections outline different ways to install the client library.</span></span>

### <a name="install-with-npm"></a><span data-ttu-id="a2ebb-246">Installer avec NPM</span><span class="sxs-lookup"><span data-stu-id="a2ebb-246">Install with npm</span></span>

<span data-ttu-id="a2ebb-247">Si vous utilisez Visual Studio, exécutez les commandes suivantes à partir de la **console du gestionnaire de package** dans le dossier racine.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-247">If using Visual Studio, run the following commands from **Package Manager Console** while in the root folder.</span></span> <span data-ttu-id="a2ebb-248">Pour Visual Studio Code, exécutez les commandes suivantes à partir du **Terminal intégré**.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-248">For Visual Studio Code, run the following commands from the **Integrated Terminal**.</span></span>

```bash
npm init -y
npm install @aspnet/signalr
```

<span data-ttu-id="a2ebb-249">NPM installe le contenu du package dans le dossier \*node_modules \\ @aspnet\signalr\dist\browser \* .</span><span class="sxs-lookup"><span data-stu-id="a2ebb-249">npm installs the package contents in the *node_modules\\@aspnet\signalr\dist\browser* folder.</span></span> <span data-ttu-id="a2ebb-250">Créez un nouveau dossier nommé *signalr* sous le dossier de la \* \\ bibliothèque wwwroot\* .</span><span class="sxs-lookup"><span data-stu-id="a2ebb-250">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="a2ebb-251">Copiez le fichier *signalr.js* dans le dossier *wwwroot\lib\signalr*</span><span class="sxs-lookup"><span data-stu-id="a2ebb-251">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

<span data-ttu-id="a2ebb-252">Référencez le SignalR client JavaScript dans l' `<script>` élément.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-252">Reference the SignalR JavaScript client in the `<script>` element.</span></span> <span data-ttu-id="a2ebb-253">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="a2ebb-253">For example:</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a><span data-ttu-id="a2ebb-254">Utiliser un réseau de distribution de contenu (CDN)</span><span class="sxs-lookup"><span data-stu-id="a2ebb-254">Use a Content Delivery Network (CDN)</span></span>

<span data-ttu-id="a2ebb-255">Pour utiliser la bibliothèque cliente sans le composant requis NPM, référencez une copie hébergée par CDN de la bibliothèque cliente.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-255">To use the client library without the npm prerequisite, reference a CDN-hosted copy of the client library.</span></span> <span data-ttu-id="a2ebb-256">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="a2ebb-256">For example:</span></span>

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/microsoft-signalr/3.1.3/signalr.min.js"></script>
```

<span data-ttu-id="a2ebb-257">La bibliothèque cliente est disponible sur le CDN suivant :</span><span class="sxs-lookup"><span data-stu-id="a2ebb-257">The client library is available on the following CDNs:</span></span>

* [<span data-ttu-id="a2ebb-258">cdnjs</span><span class="sxs-lookup"><span data-stu-id="a2ebb-258">cdnjs</span></span>](https://cdnjs.com/libraries/aspnet-signalr)
* [<span data-ttu-id="a2ebb-259">jsDelivr</span><span class="sxs-lookup"><span data-stu-id="a2ebb-259">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@aspnet/signalr)
* [<span data-ttu-id="a2ebb-260">unpkg</span><span class="sxs-lookup"><span data-stu-id="a2ebb-260">unpkg</span></span>](https://unpkg.com/@aspnet/signalr@next/dist/browser/signalr.min.js)

### <a name="install-with-libman"></a><span data-ttu-id="a2ebb-261">Installer avec LibMan</span><span class="sxs-lookup"><span data-stu-id="a2ebb-261">Install with LibMan</span></span>

<span data-ttu-id="a2ebb-262">[LibMan](xref:client-side/libman/index) peut être utilisé pour installer des fichiers de bibliothèque cliente spécifiques à partir de la bibliothèque cliente hébergée par CDN.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-262">[LibMan](xref:client-side/libman/index) can be used to install specific client library files from the CDN-hosted client library.</span></span> <span data-ttu-id="a2ebb-263">Par exemple, ajoutez uniquement le fichier JavaScript minimisés au projet.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-263">For example, only add the minified JavaScript file to the project.</span></span> <span data-ttu-id="a2ebb-264">Pour plus d’informations sur cette approche, consultez [Ajouter la SignalR bibliothèque cliente](xref:tutorials/signalr#add-the-signalr-client-library).</span><span class="sxs-lookup"><span data-stu-id="a2ebb-264">For details on that approach, see [Add the SignalR client library](xref:tutorials/signalr#add-the-signalr-client-library).</span></span>

## <a name="connect-to-a-hub"></a><span data-ttu-id="a2ebb-265">Se connecter à un concentrateur</span><span class="sxs-lookup"><span data-stu-id="a2ebb-265">Connect to a hub</span></span>

<span data-ttu-id="a2ebb-266">Le code suivant crée et démarre une connexion.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-266">The following code creates and starts a connection.</span></span> <span data-ttu-id="a2ebb-267">Le nom du concentrateur ne respecte pas la casse.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-267">The hub's name is case insensitive.</span></span>

[!code-javascript[Call hub methods](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=9-13,28-51)]

### <a name="cross-origin-connections"></a><span data-ttu-id="a2ebb-268">Connexions Cross-Origin</span><span class="sxs-lookup"><span data-stu-id="a2ebb-268">Cross-origin connections</span></span>

<span data-ttu-id="a2ebb-269">En règle générale, les navigateurs chargent les connexions à partir du même domaine que la page demandée.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-269">Typically, browsers load connections from the same domain as the requested page.</span></span> <span data-ttu-id="a2ebb-270">Toutefois, il peut arriver qu’une connexion à un autre domaine soit nécessaire.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-270">However, there are occasions when a connection to another domain is required.</span></span>

<span data-ttu-id="a2ebb-271">Pour empêcher un site malveillant de lire des données sensibles à partir d’un autre site, les [connexions Cross-Origin](xref:security/cors) sont désactivées par défaut.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-271">To prevent a malicious site from reading sensitive data from another site, [cross-origin connections](xref:security/cors) are disabled by default.</span></span> <span data-ttu-id="a2ebb-272">Pour autoriser une demande Cross-Origin, activez-la dans la `Startup` classe.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-272">To allow a cross-origin request, enable it in the `Startup` class.</span></span>

[!code-csharp[Cross-origin connections](javascript-client/samples/2.x/SignalRChat/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="a2ebb-273">Appeler les méthodes de concentrateur à partir du client</span><span class="sxs-lookup"><span data-stu-id="a2ebb-273">Call hub methods from client</span></span>

<span data-ttu-id="a2ebb-274">Les clients JavaScript appellent des méthodes publiques sur des hubs à l’aide de la méthode [Invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) de [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span><span class="sxs-lookup"><span data-stu-id="a2ebb-274">JavaScript clients call public methods on hubs via the [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) method of the [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span></span> <span data-ttu-id="a2ebb-275">La `invoke` méthode accepte deux arguments :</span><span class="sxs-lookup"><span data-stu-id="a2ebb-275">The `invoke` method accepts two arguments:</span></span>

* <span data-ttu-id="a2ebb-276">Nom de la méthode de concentrateur.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-276">The name of the hub method.</span></span> <span data-ttu-id="a2ebb-277">Dans l’exemple suivant, le nom de la méthode sur le Hub est `SendMessage` .</span><span class="sxs-lookup"><span data-stu-id="a2ebb-277">In the following example, the method name on the hub is `SendMessage`.</span></span>
* <span data-ttu-id="a2ebb-278">Tous les arguments définis dans la méthode de concentrateur.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-278">Any arguments defined in the hub method.</span></span> <span data-ttu-id="a2ebb-279">Dans l’exemple suivant, le nom de l’argument est `message` .</span><span class="sxs-lookup"><span data-stu-id="a2ebb-279">In the following example, the argument name is `message`.</span></span> <span data-ttu-id="a2ebb-280">L’exemple de code utilise la syntaxe de fonction Arrow qui est prise en charge dans les versions actuelles de tous les principaux navigateurs, à l’exception d’Internet Explorer.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-280">The example code uses arrow function syntax that is supported in current versions of all major browsers except Internet Explorer.</span></span>

  [!code-javascript[Call hub methods](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> <span data-ttu-id="a2ebb-281">L’appel de méthodes de Hub à partir d’un client est pris en charge uniquement lors de l’utilisation du SignalR service Azure en mode *par défaut* .</span><span class="sxs-lookup"><span data-stu-id="a2ebb-281">Calling hub methods from a client is only supported when using the Azure SignalR Service in *Default* mode.</span></span> <span data-ttu-id="a2ebb-282">Pour plus d’informations, consultez [Forum aux questions (référentiel GitHub Azure-signalr)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span><span class="sxs-lookup"><span data-stu-id="a2ebb-282">For more information, see [Frequently Asked Questions (azure-signalr GitHub repository)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span></span>

<span data-ttu-id="a2ebb-283">La `invoke` méthode retourne une [promesse](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)JavaScript.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-283">The `invoke` method returns a JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span></span> <span data-ttu-id="a2ebb-284">Le `Promise` est résolu avec la valeur de retour (le cas échéant) lorsque la méthode sur le serveur retourne.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-284">The `Promise` is resolved with the return value (if any) when the method on the server returns.</span></span> <span data-ttu-id="a2ebb-285">Si la méthode sur le serveur génère une erreur, `Promise` est rejetée avec le message d’erreur.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-285">If the method on the server throws an error, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="a2ebb-286">Utilisez les `then` `catch` méthodes et sur le `Promise` lui-même pour gérer ces cas ( `await` syntaxe).</span><span class="sxs-lookup"><span data-stu-id="a2ebb-286">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

<span data-ttu-id="a2ebb-287">La `send` méthode retourne un JavaScript `Promise` .</span><span class="sxs-lookup"><span data-stu-id="a2ebb-287">The `send` method returns a JavaScript `Promise`.</span></span> <span data-ttu-id="a2ebb-288">Le `Promise` est résolu lorsque le message a été envoyé au serveur.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-288">The `Promise` is resolved when the message has been sent to the server.</span></span> <span data-ttu-id="a2ebb-289">En cas d’erreur lors de l’envoi du message, `Promise` est rejeté avec le message d’erreur.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-289">If there is an error sending the message, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="a2ebb-290">Utilisez les `then` `catch` méthodes et sur le `Promise` lui-même pour gérer ces cas ( `await` syntaxe).</span><span class="sxs-lookup"><span data-stu-id="a2ebb-290">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

> [!NOTE]
> <span data-ttu-id="a2ebb-291">L’utilisation de `send` n’attend pas que le serveur ait reçu le message.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-291">Using `send` doesn't wait until the server has received the message.</span></span> <span data-ttu-id="a2ebb-292">Par conséquent, il n’est pas possible de retourner des données ou des erreurs à partir du serveur.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-292">Consequently, it's not possible to return data or errors from the server.</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="a2ebb-293">Appeler des méthodes clientes à partir du Hub</span><span class="sxs-lookup"><span data-stu-id="a2ebb-293">Call client methods from hub</span></span>

<span data-ttu-id="a2ebb-294">Pour recevoir des messages à partir du concentrateur, définissez une méthode à l’aide de la méthode [on](/javascript/api/%40aspnet/signalr/hubconnection#on) de `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="a2ebb-294">To receive messages from the hub, define a method using the [on](/javascript/api/%40aspnet/signalr/hubconnection#on) method of the `HubConnection`.</span></span>

* <span data-ttu-id="a2ebb-295">Nom de la méthode du client JavaScript.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-295">The name of the JavaScript client method.</span></span> <span data-ttu-id="a2ebb-296">Dans l’exemple suivant, le nom de la méthode est `ReceiveMessage` .</span><span class="sxs-lookup"><span data-stu-id="a2ebb-296">In the following example, the method name is `ReceiveMessage`.</span></span>
* <span data-ttu-id="a2ebb-297">Arguments que le Hub transmet à la méthode.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-297">Arguments the hub passes to the method.</span></span> <span data-ttu-id="a2ebb-298">Dans l’exemple suivant, la valeur de l’argument est `message` .</span><span class="sxs-lookup"><span data-stu-id="a2ebb-298">In the following example, the argument value is `message`.</span></span>

[!code-javascript[Receive calls from hub](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=14-19)]

<span data-ttu-id="a2ebb-299">Le code précédent dans `connection.on` s’exécute quand le code côté serveur l’appelle à l’aide de la <xref:Microsoft.AspNetCore.SignalR.ClientProxyExtensions.SendAsync%2A> méthode.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-299">The preceding code in `connection.on` runs when server-side code calls it using the <xref:Microsoft.AspNetCore.SignalR.ClientProxyExtensions.SendAsync%2A> method.</span></span>

[!code-csharp[Call client-side](javascript-client/samples/2.x/SignalRChat/hubs/chathub.cs?range=8-11)]

<span data-ttu-id="a2ebb-300">SignalR détermine la méthode cliente à appeler en faisant correspondre le nom de la méthode et les arguments définis dans `SendAsync` et `connection.on` .</span><span class="sxs-lookup"><span data-stu-id="a2ebb-300">SignalR determines which client method to call by matching the method name and arguments defined in `SendAsync` and `connection.on`.</span></span>

> [!NOTE]
> <span data-ttu-id="a2ebb-301">Il est recommandé d’appeler la méthode [Start](/javascript/api/%40aspnet/signalr/hubconnection#start) sur le `HubConnection` after `on` .</span><span class="sxs-lookup"><span data-stu-id="a2ebb-301">As a best practice, call the [start](/javascript/api/%40aspnet/signalr/hubconnection#start) method on the `HubConnection` after `on`.</span></span> <span data-ttu-id="a2ebb-302">Cela permet de s’assurer que vos gestionnaires sont inscrits avant la réception de tous les messages.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-302">Doing so ensures your handlers are registered before any messages are received.</span></span>

## <a name="error-handling-and-logging"></a><span data-ttu-id="a2ebb-303">Gestion et journalisation des erreurs</span><span class="sxs-lookup"><span data-stu-id="a2ebb-303">Error handling and logging</span></span>

<span data-ttu-id="a2ebb-304">Chaînez une `catch` méthode à la fin de la `start` méthode pour gérer les erreurs côté client.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-304">Chain a `catch` method to the end of the `start` method to handle client-side errors.</span></span> <span data-ttu-id="a2ebb-305">Utilisez `console.error` pour générer des erreurs dans la console du navigateur.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-305">Use `console.error` to output errors to the browser's console.</span></span>

[!code-javascript[Error handling](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=50)]

<span data-ttu-id="a2ebb-306">Configurez le suivi des journaux côté client en passant un enregistreur d’événements et un type d’événement à consigner lorsque la connexion est établie.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-306">Set up client-side log tracing by passing a logger and type of event to log when the connection is made.</span></span> <span data-ttu-id="a2ebb-307">Les messages sont enregistrés avec le niveau de journalisation spécifié et supérieur.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-307">Messages are logged with the specified log level and higher.</span></span> <span data-ttu-id="a2ebb-308">Les niveaux de journalisation disponibles sont les suivants :</span><span class="sxs-lookup"><span data-stu-id="a2ebb-308">Available log levels are as follows:</span></span>

* <span data-ttu-id="a2ebb-309">`signalR.LogLevel.Error`: Messages d’erreur.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-309">`signalR.LogLevel.Error`: Error messages.</span></span> <span data-ttu-id="a2ebb-310">Journalise `Error` uniquement les messages.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-310">Logs `Error` messages only.</span></span>
* <span data-ttu-id="a2ebb-311">`signalR.LogLevel.Warning`: Messages d’avertissement concernant les erreurs potentielles.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-311">`signalR.LogLevel.Warning`: Warning messages about potential errors.</span></span> <span data-ttu-id="a2ebb-312">Journaux `Warning` et `Error` messages.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-312">Logs `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="a2ebb-313">`signalR.LogLevel.Information`: Messages d’État sans erreurs.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-313">`signalR.LogLevel.Information`: Status messages without errors.</span></span> <span data-ttu-id="a2ebb-314">Journalise `Information` `Warning` `Error` les messages, et.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-314">Logs `Information`, `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="a2ebb-315">`signalR.LogLevel.Trace`: Messages de trace.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-315">`signalR.LogLevel.Trace`: Trace messages.</span></span> <span data-ttu-id="a2ebb-316">Journalise tout, y compris les données transférées entre le Hub et le client.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-316">Logs everything, including data transported between hub and client.</span></span>

<span data-ttu-id="a2ebb-317">Utilisez la méthode [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) sur [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) pour configurer le niveau de journalisation.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-317">Use the [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) to configure the log level.</span></span> <span data-ttu-id="a2ebb-318">Les messages sont enregistrés dans la console du navigateur.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-318">Messages are logged to the browser console.</span></span>

[!code-javascript[Logging levels](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a><span data-ttu-id="a2ebb-319">Reconnecter les clients</span><span class="sxs-lookup"><span data-stu-id="a2ebb-319">Reconnect clients</span></span>

### <a name="manually-reconnect"></a><span data-ttu-id="a2ebb-320">Reconnexion manuelle</span><span class="sxs-lookup"><span data-stu-id="a2ebb-320">Manually reconnect</span></span>

> [!WARNING]
> <span data-ttu-id="a2ebb-321">Avant le 3,0, le client JavaScript pour SignalR ne se reconnecte pas automatiquement.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-321">Prior to 3.0, the JavaScript client for SignalR doesn't automatically reconnect.</span></span> <span data-ttu-id="a2ebb-322">Vous devez écrire du code qui reconnectera votre client manuellement.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-322">You must write code that will reconnect your client manually.</span></span>

<span data-ttu-id="a2ebb-323">Le code suivant illustre une approche de reconnexion manuelle classique :</span><span class="sxs-lookup"><span data-stu-id="a2ebb-323">The following code demonstrates a typical manual reconnection approach:</span></span>

1. <span data-ttu-id="a2ebb-324">Une fonction (dans ce cas, la `start` fonction) est créée pour démarrer la connexion.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-324">A function (in this case, the `start` function) is created to start the connection.</span></span>
1. <span data-ttu-id="a2ebb-325">Appelez la `start` fonction dans le gestionnaire d’événements de la connexion `onclose` .</span><span class="sxs-lookup"><span data-stu-id="a2ebb-325">Call the `start` function in the connection's `onclose` event handler.</span></span>

[!code-javascript[Reconnect the JavaScript client](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=28-40)]

<span data-ttu-id="a2ebb-326">Une implémentation réelle utilise une interruption exponentielle ou une nouvelle tentative un nombre spécifié de fois avant d’abandonner.</span><span class="sxs-lookup"><span data-stu-id="a2ebb-326">A real-world implementation would use an exponential back-off or retry a specified number of times before giving up.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a2ebb-327">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="a2ebb-327">Additional resources</span></span>

* [<span data-ttu-id="a2ebb-328">Informations de référence sur l’API JavaScript</span><span class="sxs-lookup"><span data-stu-id="a2ebb-328">JavaScript API reference</span></span>](/javascript/api/?view=signalr-js-latest)
* [<span data-ttu-id="a2ebb-329">Didacticiel JavaScript</span><span class="sxs-lookup"><span data-stu-id="a2ebb-329">JavaScript tutorial</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="a2ebb-330">Didacticiel WebPack et machine à écrire</span><span class="sxs-lookup"><span data-stu-id="a2ebb-330">WebPack and TypeScript tutorial</span></span>](xref:tutorials/signalr-typescript-webpack)
* [<span data-ttu-id="a2ebb-331">Hubs</span><span class="sxs-lookup"><span data-stu-id="a2ebb-331">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="a2ebb-332">Client .NET</span><span class="sxs-lookup"><span data-stu-id="a2ebb-332">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="a2ebb-333">Publication dans Azure</span><span class="sxs-lookup"><span data-stu-id="a2ebb-333">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="a2ebb-334">Requêtes Cross-Origin (CORS)</span><span class="sxs-lookup"><span data-stu-id="a2ebb-334">Cross-Origin Requests (CORS)</span></span>](xref:security/cors)
* [<span data-ttu-id="a2ebb-335">SignalRDocumentation sans serveur de service Azure</span><span class="sxs-lookup"><span data-stu-id="a2ebb-335">Azure SignalR Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)

::: moniker-end
