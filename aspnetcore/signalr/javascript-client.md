---
title: SignalRClient JavaScript ASP.net Core
author: bradygaster
description: Vue d’ensemble de ASP.NET Core SignalR client JavaScript.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/javascript-client
ms.openlocfilehash: 4b3a4b2323b7f221d9cd1aab1c56d1d9828eb916
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84106453"
---
# <a name="aspnet-core-signalr-javascript-client"></a><span data-ttu-id="4028e-103">SignalRClient JavaScript ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="4028e-103">ASP.NET Core SignalR JavaScript client</span></span>

<span data-ttu-id="4028e-104">Par [Rachel Appel](https://twitter.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="4028e-104">By [Rachel Appel](https://twitter.com/rachelappel)</span></span>

<span data-ttu-id="4028e-105">La SignalR bibliothèque cliente JavaScript ASP.net Core permet aux développeurs d’appeler le code de concentrateur côté serveur.</span><span class="sxs-lookup"><span data-stu-id="4028e-105">The ASP.NET Core SignalR JavaScript client library enables developers to call server-side hub code.</span></span>

<span data-ttu-id="4028e-106">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="4028e-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-signalr-client-package"></a><span data-ttu-id="4028e-107">Installer le SignalR package client</span><span class="sxs-lookup"><span data-stu-id="4028e-107">Install the SignalR client package</span></span>

<span data-ttu-id="4028e-108">La SignalR bibliothèque cliente JavaScript est fournie en tant que package [NPM](https://www.npmjs.com/) .</span><span class="sxs-lookup"><span data-stu-id="4028e-108">The SignalR JavaScript client library is delivered as an [npm](https://www.npmjs.com/) package.</span></span> <span data-ttu-id="4028e-109">Les sections suivantes décrivent différentes façons d’installer la bibliothèque cliente.</span><span class="sxs-lookup"><span data-stu-id="4028e-109">The following sections outline different ways to install the client library.</span></span>

### <a name="install-with-npm"></a><span data-ttu-id="4028e-110">Installer avec NPM</span><span class="sxs-lookup"><span data-stu-id="4028e-110">Install with npm</span></span>

<span data-ttu-id="4028e-111">Si vous utilisez Visual Studio, exécutez les commandes suivantes à partir de la **console du gestionnaire de package** dans le dossier racine.</span><span class="sxs-lookup"><span data-stu-id="4028e-111">If using Visual Studio, run the following commands from **Package Manager Console** while in the root folder.</span></span> <span data-ttu-id="4028e-112">Pour Visual Studio Code, exécutez les commandes suivantes à partir du **Terminal intégré**.</span><span class="sxs-lookup"><span data-stu-id="4028e-112">For Visual Studio Code, run the following commands from the **Integrated Terminal**.</span></span>

::: moniker range=">= aspnetcore-3.0"

```bash
npm init -y
npm install @microsoft/signalr
```

<span data-ttu-id="4028e-113">NPM installe le contenu du package dans le dossier \*node_modules \\ @microsoft\signalr\dist\browser \* .</span><span class="sxs-lookup"><span data-stu-id="4028e-113">npm installs the package contents in the *node_modules\\@microsoft\signalr\dist\browser* folder.</span></span> <span data-ttu-id="4028e-114">Créez un nouveau dossier nommé *signalr* sous le dossier de la \* \\ bibliothèque wwwroot\* .</span><span class="sxs-lookup"><span data-stu-id="4028e-114">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="4028e-115">Copiez le fichier *signalr. js* dans le dossier *wwwroot\lib\signalr* .</span><span class="sxs-lookup"><span data-stu-id="4028e-115">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```bash
npm init -y
npm install @aspnet/signalr
```

<span data-ttu-id="4028e-116">NPM installe le contenu du package dans le dossier \*node_modules \\ @aspnet\signalr\dist\browser \* .</span><span class="sxs-lookup"><span data-stu-id="4028e-116">npm installs the package contents in the *node_modules\\@aspnet\signalr\dist\browser* folder.</span></span> <span data-ttu-id="4028e-117">Créez un nouveau dossier nommé *signalr* sous le dossier de la \* \\ bibliothèque wwwroot\* .</span><span class="sxs-lookup"><span data-stu-id="4028e-117">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="4028e-118">Copiez le fichier *signalr. js* dans le dossier *wwwroot\lib\signalr* .</span><span class="sxs-lookup"><span data-stu-id="4028e-118">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

::: moniker-end

<span data-ttu-id="4028e-119">Référencez le SignalR client JavaScript dans l' `<script>` élément.</span><span class="sxs-lookup"><span data-stu-id="4028e-119">Reference the SignalR JavaScript client in the `<script>` element.</span></span> <span data-ttu-id="4028e-120">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="4028e-120">For example:</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a><span data-ttu-id="4028e-121">Utiliser un réseau de distribution de contenu (CDN)</span><span class="sxs-lookup"><span data-stu-id="4028e-121">Use a Content Delivery Network (CDN)</span></span>

<span data-ttu-id="4028e-122">Pour utiliser la bibliothèque cliente sans le composant requis NPM, référencez une copie hébergée par CDN de la bibliothèque cliente.</span><span class="sxs-lookup"><span data-stu-id="4028e-122">To use the client library without the npm prerequisite, reference a CDN-hosted copy of the client library.</span></span> <span data-ttu-id="4028e-123">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="4028e-123">For example:</span></span>

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/microsoft-signalr/3.1.3/signalr.min.js"></script>
```

<span data-ttu-id="4028e-124">La bibliothèque cliente est disponible sur le CDN suivant :</span><span class="sxs-lookup"><span data-stu-id="4028e-124">The client library is available on the following CDNs:</span></span>

::: moniker range=">= aspnetcore-3.0"

* [<span data-ttu-id="4028e-125">cdnjs</span><span class="sxs-lookup"><span data-stu-id="4028e-125">cdnjs</span></span>](https://cdnjs.com/libraries/microsoft-signalr)
* [<span data-ttu-id="4028e-126">jsDelivr</span><span class="sxs-lookup"><span data-stu-id="4028e-126">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@microsoft/signalr)
* [<span data-ttu-id="4028e-127">unpkg</span><span class="sxs-lookup"><span data-stu-id="4028e-127">unpkg</span></span>](https://unpkg.com/@microsoft/signalr@next/dist/browser/signalr.min.js)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* [<span data-ttu-id="4028e-128">cdnjs</span><span class="sxs-lookup"><span data-stu-id="4028e-128">cdnjs</span></span>](https://cdnjs.com/libraries/aspnet-signalr)
* [<span data-ttu-id="4028e-129">jsDelivr</span><span class="sxs-lookup"><span data-stu-id="4028e-129">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@aspnet/signalr)
* [<span data-ttu-id="4028e-130">unpkg</span><span class="sxs-lookup"><span data-stu-id="4028e-130">unpkg</span></span>](https://unpkg.com/@aspnet/signalr@next/dist/browser/signalr.min.js)

::: moniker-end

### <a name="install-with-libman"></a><span data-ttu-id="4028e-131">Installer avec LibMan</span><span class="sxs-lookup"><span data-stu-id="4028e-131">Install with LibMan</span></span>

<span data-ttu-id="4028e-132">[LibMan](xref:client-side/libman/index) peut être utilisé pour installer des fichiers de bibliothèque cliente spécifiques à partir de la bibliothèque cliente hébergée par CDN.</span><span class="sxs-lookup"><span data-stu-id="4028e-132">[LibMan](xref:client-side/libman/index) can be used to install specific client library files from the CDN-hosted client library.</span></span> <span data-ttu-id="4028e-133">Par exemple, ajoutez uniquement le fichier JavaScript minimisés au projet.</span><span class="sxs-lookup"><span data-stu-id="4028e-133">For example, only add the minified JavaScript file to the project.</span></span> <span data-ttu-id="4028e-134">Pour plus d’informations sur cette approche, consultez [Ajouter la SignalR bibliothèque cliente](xref:tutorials/signalr#add-the-signalr-client-library).</span><span class="sxs-lookup"><span data-stu-id="4028e-134">For details on that approach, see [Add the SignalR client library](xref:tutorials/signalr#add-the-signalr-client-library).</span></span>

## <a name="connect-to-a-hub"></a><span data-ttu-id="4028e-135">Se connecter à un concentrateur</span><span class="sxs-lookup"><span data-stu-id="4028e-135">Connect to a hub</span></span>

<span data-ttu-id="4028e-136">Le code suivant crée et démarre une connexion.</span><span class="sxs-lookup"><span data-stu-id="4028e-136">The following code creates and starts a connection.</span></span> <span data-ttu-id="4028e-137">Le nom du concentrateur ne respecte pas la casse.</span><span class="sxs-lookup"><span data-stu-id="4028e-137">The hub's name is case insensitive.</span></span>

[!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=9-13,28-51)]

### <a name="cross-origin-connections"></a><span data-ttu-id="4028e-138">Connexions Cross-Origin</span><span class="sxs-lookup"><span data-stu-id="4028e-138">Cross-origin connections</span></span>

<span data-ttu-id="4028e-139">En règle générale, les navigateurs chargent les connexions à partir du même domaine que la page demandée.</span><span class="sxs-lookup"><span data-stu-id="4028e-139">Typically, browsers load connections from the same domain as the requested page.</span></span> <span data-ttu-id="4028e-140">Toutefois, il peut arriver qu’une connexion à un autre domaine soit nécessaire.</span><span class="sxs-lookup"><span data-stu-id="4028e-140">However, there are occasions when a connection to another domain is required.</span></span>

<span data-ttu-id="4028e-141">Pour empêcher un site malveillant de lire des données sensibles à partir d’un autre site, les [connexions Cross-Origin](xref:security/cors) sont désactivées par défaut.</span><span class="sxs-lookup"><span data-stu-id="4028e-141">To prevent a malicious site from reading sensitive data from another site, [cross-origin connections](xref:security/cors) are disabled by default.</span></span> <span data-ttu-id="4028e-142">Pour autoriser une demande Cross-Origin, activez-la dans la `Startup` classe.</span><span class="sxs-lookup"><span data-stu-id="4028e-142">To allow a cross-origin request, enable it in the `Startup` class.</span></span>

[!code-csharp[Cross-origin connections](javascript-client/sample/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="4028e-143">Appeler les méthodes de concentrateur à partir du client</span><span class="sxs-lookup"><span data-stu-id="4028e-143">Call hub methods from client</span></span>

<span data-ttu-id="4028e-144">Les clients JavaScript appellent des méthodes publiques sur des hubs à l’aide de la méthode [Invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) de [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span><span class="sxs-lookup"><span data-stu-id="4028e-144">JavaScript clients call public methods on hubs via the [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) method of the [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span></span> <span data-ttu-id="4028e-145">La `invoke` méthode accepte deux arguments :</span><span class="sxs-lookup"><span data-stu-id="4028e-145">The `invoke` method accepts two arguments:</span></span>

* <span data-ttu-id="4028e-146">Nom de la méthode de concentrateur.</span><span class="sxs-lookup"><span data-stu-id="4028e-146">The name of the hub method.</span></span> <span data-ttu-id="4028e-147">Dans l’exemple suivant, le nom de la méthode sur le Hub est `SendMessage` .</span><span class="sxs-lookup"><span data-stu-id="4028e-147">In the following example, the method name on the hub is `SendMessage`.</span></span>
* <span data-ttu-id="4028e-148">Tous les arguments définis dans la méthode de concentrateur.</span><span class="sxs-lookup"><span data-stu-id="4028e-148">Any arguments defined in the hub method.</span></span> <span data-ttu-id="4028e-149">Dans l’exemple suivant, le nom de l’argument est `message` .</span><span class="sxs-lookup"><span data-stu-id="4028e-149">In the following example, the argument name is `message`.</span></span> <span data-ttu-id="4028e-150">L’exemple de code utilise la syntaxe de fonction Arrow qui est prise en charge dans les versions actuelles de tous les principaux navigateurs, à l’exception d’Internet Explorer.</span><span class="sxs-lookup"><span data-stu-id="4028e-150">The example code uses arrow function syntax that is supported in current versions of all major browsers except Internet Explorer.</span></span>

  [!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> <span data-ttu-id="4028e-151">Si vous utilisez SignalR le service Azure en *mode sans serveur*, vous ne pouvez pas appeler les méthodes de concentrateur à partir d’un client.</span><span class="sxs-lookup"><span data-stu-id="4028e-151">If you're using Azure SignalR Service in *Serverless mode*, you cannot call hub methods from a client.</span></span> <span data-ttu-id="4028e-152">Pour plus d’informations, consultez la [ SignalR documentation du service](/azure/azure-signalr/signalr-concept-serverless-development-config).</span><span class="sxs-lookup"><span data-stu-id="4028e-152">For more information, see the [SignalR Service documentation](/azure/azure-signalr/signalr-concept-serverless-development-config).</span></span>

<span data-ttu-id="4028e-153">La `invoke` méthode retourne une [promesse](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)JavaScript.</span><span class="sxs-lookup"><span data-stu-id="4028e-153">The `invoke` method returns a JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span></span> <span data-ttu-id="4028e-154">Le `Promise` est résolu avec la valeur de retour (le cas échéant) lorsque la méthode sur le serveur retourne.</span><span class="sxs-lookup"><span data-stu-id="4028e-154">The `Promise` is resolved with the return value (if any) when the method on the server returns.</span></span> <span data-ttu-id="4028e-155">Si la méthode sur le serveur génère une erreur, `Promise` est rejetée avec le message d’erreur.</span><span class="sxs-lookup"><span data-stu-id="4028e-155">If the method on the server throws an error, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="4028e-156">Utilisez les `then` `catch` méthodes et sur le `Promise` lui-même pour gérer ces cas ( `await` syntaxe).</span><span class="sxs-lookup"><span data-stu-id="4028e-156">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

<span data-ttu-id="4028e-157">La `send` méthode retourne un JavaScript `Promise` .</span><span class="sxs-lookup"><span data-stu-id="4028e-157">The `send` method returns a JavaScript `Promise`.</span></span> <span data-ttu-id="4028e-158">Le `Promise` est résolu lorsque le message a été envoyé au serveur.</span><span class="sxs-lookup"><span data-stu-id="4028e-158">The `Promise` is resolved when the message has been sent to the server.</span></span> <span data-ttu-id="4028e-159">En cas d’erreur lors de l’envoi du message, `Promise` est rejeté avec le message d’erreur.</span><span class="sxs-lookup"><span data-stu-id="4028e-159">If there is an error sending the message, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="4028e-160">Utilisez les `then` `catch` méthodes et sur le `Promise` lui-même pour gérer ces cas ( `await` syntaxe).</span><span class="sxs-lookup"><span data-stu-id="4028e-160">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

> [!NOTE]
> <span data-ttu-id="4028e-161">L’utilisation de `send` n’attend pas que le serveur ait reçu le message.</span><span class="sxs-lookup"><span data-stu-id="4028e-161">Using `send` doesn't wait until the server has received the message.</span></span> <span data-ttu-id="4028e-162">Par conséquent, il n’est pas possible de retourner des données ou des erreurs à partir du serveur.</span><span class="sxs-lookup"><span data-stu-id="4028e-162">Consequently, it's not possible to return data or errors from the server.</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="4028e-163">Appeler des méthodes clientes à partir du Hub</span><span class="sxs-lookup"><span data-stu-id="4028e-163">Call client methods from hub</span></span>

<span data-ttu-id="4028e-164">Pour recevoir des messages à partir du concentrateur, définissez une méthode à l’aide de la méthode [on](/javascript/api/%40aspnet/signalr/hubconnection#on) de `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="4028e-164">To receive messages from the hub, define a method using the [on](/javascript/api/%40aspnet/signalr/hubconnection#on) method of the `HubConnection`.</span></span>

* <span data-ttu-id="4028e-165">Nom de la méthode du client JavaScript.</span><span class="sxs-lookup"><span data-stu-id="4028e-165">The name of the JavaScript client method.</span></span> <span data-ttu-id="4028e-166">Dans l’exemple suivant, le nom de la méthode est `ReceiveMessage` .</span><span class="sxs-lookup"><span data-stu-id="4028e-166">In the following example, the method name is `ReceiveMessage`.</span></span>
* <span data-ttu-id="4028e-167">Arguments que le Hub transmet à la méthode.</span><span class="sxs-lookup"><span data-stu-id="4028e-167">Arguments the hub passes to the method.</span></span> <span data-ttu-id="4028e-168">Dans l’exemple suivant, la valeur de l’argument est `message` .</span><span class="sxs-lookup"><span data-stu-id="4028e-168">In the following example, the argument value is `message`.</span></span>

[!code-javascript[Receive calls from hub](javascript-client/sample/wwwroot/js/chat.js?range=14-19)]

<span data-ttu-id="4028e-169">Le code précédent dans `connection.on` s’exécute lorsque le code côté serveur l’appelle à l’aide de la méthode [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) .</span><span class="sxs-lookup"><span data-stu-id="4028e-169">The preceding code in `connection.on` runs when server-side code calls it using the [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) method.</span></span>

[!code-csharp[Call client-side](javascript-client/sample/hubs/chathub.cs?range=8-11)]

SignalR<span data-ttu-id="4028e-170">détermine la méthode cliente à appeler en faisant correspondre le nom de la méthode et les arguments définis dans `SendAsync` et `connection.on` .</span><span class="sxs-lookup"><span data-stu-id="4028e-170"> determines which client method to call by matching the method name and arguments defined in `SendAsync` and `connection.on`.</span></span>

> [!NOTE]
> <span data-ttu-id="4028e-171">Il est recommandé d’appeler la méthode [Start](/javascript/api/%40aspnet/signalr/hubconnection#start) sur le `HubConnection` after `on` .</span><span class="sxs-lookup"><span data-stu-id="4028e-171">As a best practice, call the [start](/javascript/api/%40aspnet/signalr/hubconnection#start) method on the `HubConnection` after `on`.</span></span> <span data-ttu-id="4028e-172">Cela permet de s’assurer que vos gestionnaires sont inscrits avant la réception de tous les messages.</span><span class="sxs-lookup"><span data-stu-id="4028e-172">Doing so ensures your handlers are registered before any messages are received.</span></span>

## <a name="error-handling-and-logging"></a><span data-ttu-id="4028e-173">Gestion et journalisation des erreurs</span><span class="sxs-lookup"><span data-stu-id="4028e-173">Error handling and logging</span></span>

<span data-ttu-id="4028e-174">Chaînez une `catch` méthode à la fin de la `start` méthode pour gérer les erreurs côté client.</span><span class="sxs-lookup"><span data-stu-id="4028e-174">Chain a `catch` method to the end of the `start` method to handle client-side errors.</span></span> <span data-ttu-id="4028e-175">Utilisez `console.error` pour générer des erreurs dans la console du navigateur.</span><span class="sxs-lookup"><span data-stu-id="4028e-175">Use `console.error` to output errors to the browser's console.</span></span>

[!code-javascript[Error handling](javascript-client/sample/wwwroot/js/chat.js?range=50)]

<span data-ttu-id="4028e-176">Configurez le suivi des journaux côté client en passant un enregistreur d’événements et un type d’événement à consigner lorsque la connexion est établie.</span><span class="sxs-lookup"><span data-stu-id="4028e-176">Set up client-side log tracing by passing a logger and type of event to log when the connection is made.</span></span> <span data-ttu-id="4028e-177">Les messages sont enregistrés avec le niveau de journalisation spécifié et supérieur.</span><span class="sxs-lookup"><span data-stu-id="4028e-177">Messages are logged with the specified log level and higher.</span></span> <span data-ttu-id="4028e-178">Les niveaux de journalisation disponibles sont les suivants :</span><span class="sxs-lookup"><span data-stu-id="4028e-178">Available log levels are as follows:</span></span>

* <span data-ttu-id="4028e-179">`signalR.LogLevel.Error`: Messages d’erreur.</span><span class="sxs-lookup"><span data-stu-id="4028e-179">`signalR.LogLevel.Error`: Error messages.</span></span> <span data-ttu-id="4028e-180">Journalise `Error` uniquement les messages.</span><span class="sxs-lookup"><span data-stu-id="4028e-180">Logs `Error` messages only.</span></span>
* <span data-ttu-id="4028e-181">`signalR.LogLevel.Warning`: Messages d’avertissement concernant les erreurs potentielles.</span><span class="sxs-lookup"><span data-stu-id="4028e-181">`signalR.LogLevel.Warning`: Warning messages about potential errors.</span></span> <span data-ttu-id="4028e-182">Journaux `Warning` et `Error` messages.</span><span class="sxs-lookup"><span data-stu-id="4028e-182">Logs `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="4028e-183">`signalR.LogLevel.Information`: Messages d’État sans erreurs.</span><span class="sxs-lookup"><span data-stu-id="4028e-183">`signalR.LogLevel.Information`: Status messages without errors.</span></span> <span data-ttu-id="4028e-184">Journalise `Information` `Warning` `Error` les messages, et.</span><span class="sxs-lookup"><span data-stu-id="4028e-184">Logs `Information`, `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="4028e-185">`signalR.LogLevel.Trace`: Messages de trace.</span><span class="sxs-lookup"><span data-stu-id="4028e-185">`signalR.LogLevel.Trace`: Trace messages.</span></span> <span data-ttu-id="4028e-186">Journalise tout, y compris les données transférées entre le Hub et le client.</span><span class="sxs-lookup"><span data-stu-id="4028e-186">Logs everything, including data transported between hub and client.</span></span>

<span data-ttu-id="4028e-187">Utilisez la méthode [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) sur [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) pour configurer le niveau de journalisation.</span><span class="sxs-lookup"><span data-stu-id="4028e-187">Use the [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) to configure the log level.</span></span> <span data-ttu-id="4028e-188">Les messages sont enregistrés dans la console du navigateur.</span><span class="sxs-lookup"><span data-stu-id="4028e-188">Messages are logged to the browser console.</span></span>

[!code-javascript[Logging levels](javascript-client/sample/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a><span data-ttu-id="4028e-189">Reconnecter les clients</span><span class="sxs-lookup"><span data-stu-id="4028e-189">Reconnect clients</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a><span data-ttu-id="4028e-190">Se reconnecter automatiquement</span><span class="sxs-lookup"><span data-stu-id="4028e-190">Automatically reconnect</span></span>

<span data-ttu-id="4028e-191">Le client JavaScript pour SignalR peut être configuré pour se reconnecter automatiquement à l’aide de la `withAutomaticReconnect` méthode sur [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="4028e-191">The JavaScript client for SignalR can be configured to automatically reconnect using the `withAutomaticReconnect` method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span></span> <span data-ttu-id="4028e-192">Par défaut, il ne se reconnectera pas automatiquement.</span><span class="sxs-lookup"><span data-stu-id="4028e-192">It won't automatically reconnect by default.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="4028e-193">Sans aucun paramètre, `withAutomaticReconnect()` configure le client pour attendre 0, 2, 10 et 30 secondes, respectivement, avant d’essayer chaque tentative de reconnexion, en s’arrêtant après quatre tentatives ayant échoué.</span><span class="sxs-lookup"><span data-stu-id="4028e-193">Without any parameters, `withAutomaticReconnect()` configures the client to wait 0, 2, 10, and 30 seconds respectively before trying each reconnect attempt, stopping after four failed attempts.</span></span>

<span data-ttu-id="4028e-194">Avant de commencer les tentatives de reconnexion, le `HubConnection` passe à l' `HubConnectionState.Reconnecting` État et déclenche ses `onreconnecting` rappels au lieu de passer à l' `Disconnected` État et de déclencher ses `onclose` rappels comme un `HubConnection` sans reconnexion automatique configurée.</span><span class="sxs-lookup"><span data-stu-id="4028e-194">Before starting any reconnect attempts, the `HubConnection` will transition to the `HubConnectionState.Reconnecting` state and fire its `onreconnecting` callbacks instead of transitioning to the `Disconnected` state and triggering its `onclose` callbacks like a `HubConnection` without automatic reconnect configured.</span></span> <span data-ttu-id="4028e-195">Cela permet d’avertir les utilisateurs que la connexion a été perdue et de désactiver les éléments d’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="4028e-195">This provides an opportunity to warn users that the connection has been lost and to disable UI elements.</span></span>

```javascript
connection.onreconnecting(error => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="4028e-196">Si le client se reconnecte correctement dans les quatre premières tentatives, le `HubConnection` passe à l' `Connected` État et déclenche ses `onreconnected` rappels.</span><span class="sxs-lookup"><span data-stu-id="4028e-196">If the client successfully reconnects within its first four attempts, the `HubConnection` will transition back to the `Connected` state and fire its `onreconnected` callbacks.</span></span> <span data-ttu-id="4028e-197">Cela permet d’informer les utilisateurs que la connexion a été rétablie.</span><span class="sxs-lookup"><span data-stu-id="4028e-197">This provides an opportunity to inform users the connection has been reestablished.</span></span>

<span data-ttu-id="4028e-198">Étant donné que la connexion semble entièrement nouvelle sur le serveur, un nouveau `connectionId` sera fourni au `onreconnected` rappel.</span><span class="sxs-lookup"><span data-stu-id="4028e-198">Since the connection looks entirely new to the server, a new `connectionId` will be provided to the `onreconnected` callback.</span></span>

> [!WARNING]
> <span data-ttu-id="4028e-199">Le `onreconnected` paramètre du rappel n’est pas `connectionId` défini si le `HubConnection` a été configuré pour [ignorer la négociation](xref:signalr/configuration#configure-client-options).</span><span class="sxs-lookup"><span data-stu-id="4028e-199">The `onreconnected` callback's `connectionId` parameter will be undefined if the `HubConnection` was configured to [skip negotiation](xref:signalr/configuration#configure-client-options).</span></span>

```javascript
connection.onreconnected(connectionId => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="4028e-200">`withAutomaticReconnect()`ne configure pas le `HubConnection` pour réessayer les échecs de démarrage initial. les échecs de démarrage doivent donc être gérés manuellement :</span><span class="sxs-lookup"><span data-stu-id="4028e-200">`withAutomaticReconnect()` won't configure the `HubConnection` to retry initial start failures, so start failures need to be handled manually:</span></span>

```javascript
async function start() {
    try {
        await connection.start();
        console.assert(connection.state === signalR.HubConnectionState.Connected);
        console.log("connected");
    } catch (err) {
        console.assert(connection.state === signalR.HubConnectionState.Disconnected);
        console.log(err);
        setTimeout(() => start(), 5000);
    }
};
```

<span data-ttu-id="4028e-201">Si le client ne se reconnecte pas correctement dans les quatre premières tentatives, le `HubConnection` passe à l' `Disconnected` État et déclenche ses rappels [OnClose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) .</span><span class="sxs-lookup"><span data-stu-id="4028e-201">If the client doesn't successfully reconnect within its first four attempts, the `HubConnection` will transition to the `Disconnected` state and fire its [onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) callbacks.</span></span> <span data-ttu-id="4028e-202">Cela permet d’informer les utilisateurs que la connexion a été perdue définitivement et de recommander l’actualisation de la page :</span><span class="sxs-lookup"><span data-stu-id="4028e-202">This provides an opportunity to inform users the connection has been permanently lost and recommend refreshing the page:</span></span>

```javascript
connection.onclose(error => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="4028e-203">Pour configurer un nombre personnalisé de tentatives de reconnexion avant de vous déconnecter ou de modifier le délai de reconnexion, `withAutomaticReconnect` accepte un tableau de nombres représentant le délai en millisecondes à attendre avant de démarrer chaque tentative de reconnexion.</span><span class="sxs-lookup"><span data-stu-id="4028e-203">In order to configure a custom number of reconnect attempts before disconnecting or change the reconnect timing, `withAutomaticReconnect` accepts an array of numbers representing the delay in milliseconds to wait before starting each reconnect attempt.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

<span data-ttu-id="4028e-204">L’exemple précédent configure le `HubConnection` pour qu’il commence à tenter de se reconnecter immédiatement après la perte de la connexion.</span><span class="sxs-lookup"><span data-stu-id="4028e-204">The preceding example configures the `HubConnection` to start attempting reconnects immediately after the connection is lost.</span></span> <span data-ttu-id="4028e-205">Cela est également vrai pour la configuration par défaut.</span><span class="sxs-lookup"><span data-stu-id="4028e-205">This is also true for the default configuration.</span></span>

<span data-ttu-id="4028e-206">Si la première tentative de reconnexion échoue, la deuxième tentative de reconnexion démarre également immédiatement au lieu d’attendre 2 secondes comme dans la configuration par défaut.</span><span class="sxs-lookup"><span data-stu-id="4028e-206">If the first reconnect attempt fails, the second reconnect attempt will also start immediately instead of waiting 2 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="4028e-207">Si la deuxième tentative de reconnexion échoue, la troisième tentative de reconnexion démarrera dans 10 secondes, ce qui revient à la configuration par défaut.</span><span class="sxs-lookup"><span data-stu-id="4028e-207">If the second reconnect attempt fails, the third reconnect attempt will start in 10 seconds which is again like the default configuration.</span></span>

<span data-ttu-id="4028e-208">Le comportement personnalisé divergent ensuite du comportement par défaut en s’arrêtant après le troisième échec de tentative de reconnexion au lieu d’essayer une nouvelle tentative de reconnexion dans 30 secondes, comme dans la configuration par défaut.</span><span class="sxs-lookup"><span data-stu-id="4028e-208">The custom behavior then diverges again from the default behavior by stopping after the third reconnect attempt failure instead of trying one more reconnect attempt in another 30 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="4028e-209">Si vous souhaitez encore plus de contrôle sur le minutage et le nombre de tentatives de reconnexion automatique, `withAutomaticReconnect` accepte un objet qui implémente l' `IRetryPolicy` interface, qui a une méthode unique nommée `nextRetryDelayInMilliseconds` .</span><span class="sxs-lookup"><span data-stu-id="4028e-209">If you want even more control over the timing and number of automatic reconnect attempts, `withAutomaticReconnect` accepts an object implementing the `IRetryPolicy` interface, which has a single method named `nextRetryDelayInMilliseconds`.</span></span>

<span data-ttu-id="4028e-210">`nextRetryDelayInMilliseconds`accepte un seul argument avec le type `RetryContext` .</span><span class="sxs-lookup"><span data-stu-id="4028e-210">`nextRetryDelayInMilliseconds` takes a single argument with the type `RetryContext`.</span></span> <span data-ttu-id="4028e-211">`RetryContext`A trois propriétés : `previousRetryCount` , `elapsedMilliseconds` et `retryReason` qui sont respectivement un `number` , un `number` et un `Error` .</span><span class="sxs-lookup"><span data-stu-id="4028e-211">The `RetryContext` has three properties: `previousRetryCount`, `elapsedMilliseconds` and `retryReason` which are a `number`, a `number` and an `Error` respectively.</span></span> <span data-ttu-id="4028e-212">Avant la première tentative de reconnexion, `previousRetryCount` et est égal à zéro et est `elapsedMilliseconds` `retryReason` l’erreur qui a provoqué la perte de la connexion.</span><span class="sxs-lookup"><span data-stu-id="4028e-212">Before the first reconnect attempt, both `previousRetryCount` and `elapsedMilliseconds` will be zero, and the `retryReason` will be the Error that caused the connection to be lost.</span></span> <span data-ttu-id="4028e-213">Après chaque nouvelle tentative d’échec, est `previousRetryCount` incrémenté d’une unité, est `elapsedMilliseconds` mis à jour pour refléter le temps passé à se reconnecter jusqu’à présent en millisecondes, et est l' `retryReason` erreur qui a provoqué l’échec de la dernière tentative de reconnexion.</span><span class="sxs-lookup"><span data-stu-id="4028e-213">After each failed retry attempt, `previousRetryCount` will be incremented by one, `elapsedMilliseconds` will be updated to reflect the amount of time spent reconnecting so far in milliseconds, and the `retryReason` will be the Error that caused the last reconnect attempt to fail.</span></span>

<span data-ttu-id="4028e-214">`nextRetryDelayInMilliseconds`doit retourner un nombre représentant le nombre de millisecondes à attendre avant la tentative de reconnexion suivante ou `null` si `HubConnection` doit arrêter la reconnexion.</span><span class="sxs-lookup"><span data-stu-id="4028e-214">`nextRetryDelayInMilliseconds` must return either a number representing the number of milliseconds to wait before the next reconnect attempt or `null` if the `HubConnection` should stop reconnecting.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
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

<span data-ttu-id="4028e-215">Vous pouvez également écrire du code qui reconnectera votre client manuellement, comme illustré dans [reconnexion manuelle](#manually-reconnect).</span><span class="sxs-lookup"><span data-stu-id="4028e-215">Alternatively, you can write code that will reconnect your client manually as demonstrated in [Manually reconnect](#manually-reconnect).</span></span>

::: moniker-end

### <a name="manually-reconnect"></a><span data-ttu-id="4028e-216">Reconnexion manuelle</span><span class="sxs-lookup"><span data-stu-id="4028e-216">Manually reconnect</span></span>

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="4028e-217">Avant le 3,0, le client JavaScript pour SignalR ne se reconnecte pas automatiquement.</span><span class="sxs-lookup"><span data-stu-id="4028e-217">Prior to 3.0, the JavaScript client for SignalR doesn't automatically reconnect.</span></span> <span data-ttu-id="4028e-218">Vous devez écrire du code qui reconnectera votre client manuellement.</span><span class="sxs-lookup"><span data-stu-id="4028e-218">You must write code that will reconnect your client manually.</span></span>

::: moniker-end

<span data-ttu-id="4028e-219">Le code suivant illustre une approche de reconnexion manuelle classique :</span><span class="sxs-lookup"><span data-stu-id="4028e-219">The following code demonstrates a typical manual reconnection approach:</span></span>

1. <span data-ttu-id="4028e-220">Une fonction (dans ce cas, la `start` fonction) est créée pour démarrer la connexion.</span><span class="sxs-lookup"><span data-stu-id="4028e-220">A function (in this case, the `start` function) is created to start the connection.</span></span>
1. <span data-ttu-id="4028e-221">Appelez la `start` fonction dans le gestionnaire d’événements de la connexion `onclose` .</span><span class="sxs-lookup"><span data-stu-id="4028e-221">Call the `start` function in the connection's `onclose` event handler.</span></span>

[!code-javascript[Reconnect the JavaScript client](javascript-client/sample/wwwroot/js/chat.js?range=28-40)]

<span data-ttu-id="4028e-222">Une implémentation réelle utilise une interruption exponentielle ou une nouvelle tentative un nombre spécifié de fois avant d’abandonner.</span><span class="sxs-lookup"><span data-stu-id="4028e-222">A real-world implementation would use an exponential back-off or retry a specified number of times before giving up.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4028e-223">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="4028e-223">Additional resources</span></span>

* [<span data-ttu-id="4028e-224">Informations de référence sur l’API JavaScript</span><span class="sxs-lookup"><span data-stu-id="4028e-224">JavaScript API reference</span></span>](/javascript/api/?view=signalr-js-latest)
* [<span data-ttu-id="4028e-225">Didacticiel JavaScript</span><span class="sxs-lookup"><span data-stu-id="4028e-225">JavaScript tutorial</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="4028e-226">Didacticiel WebPack et machine à écrire</span><span class="sxs-lookup"><span data-stu-id="4028e-226">WebPack and TypeScript tutorial</span></span>](xref:tutorials/signalr-typescript-webpack)
* [<span data-ttu-id="4028e-227">Hubs</span><span class="sxs-lookup"><span data-stu-id="4028e-227">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="4028e-228">Client .NET</span><span class="sxs-lookup"><span data-stu-id="4028e-228">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="4028e-229">Publication dans Azure</span><span class="sxs-lookup"><span data-stu-id="4028e-229">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="4028e-230">Requêtes Cross-Origin (CORS)</span><span class="sxs-lookup"><span data-stu-id="4028e-230">Cross-Origin Requests (CORS)</span></span>](xref:security/cors)
* <span data-ttu-id="4028e-231">[SignalRDocumentation sans serveur de service Azure](/azure/azure-signalr/signalr-concept-serverless-development-config)</span><span class="sxs-lookup"><span data-stu-id="4028e-231">[Azure SignalR Service serverless documentation](/azure/azure-signalr/signalr-concept-serverless-development-config)</span></span>
