---
title: ASP.NET client SignalR JavaScript de base
author: bradygaster
description: Aperçu du client SignalR JavaScript de ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- SignalR
uid: signalr/javascript-client
ms.openlocfilehash: a99c1dd2aba6ef6ff925783762a98e2c81ed7225
ms.sourcegitcommit: 9a46e78c79d167e5fa0cddf89c1ef584e5fe1779
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80994581"
---
# <a name="aspnet-core-opno-locsignalr-javascript-client"></a><span data-ttu-id="efea7-103">ASP.NET client SignalR JavaScript de base</span><span class="sxs-lookup"><span data-stu-id="efea7-103">ASP.NET Core SignalR JavaScript client</span></span>

<span data-ttu-id="efea7-104">Par [Rachel Appel](https://twitter.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="efea7-104">By [Rachel Appel](https://twitter.com/rachelappel)</span></span>

<span data-ttu-id="efea7-105">La ASP.NET la SignalR bibliothèque client Core JavaScript permet aux développeurs d’appeler le code du hub côté serveur.</span><span class="sxs-lookup"><span data-stu-id="efea7-105">The ASP.NET Core SignalR JavaScript client library enables developers to call server-side hub code.</span></span>

<span data-ttu-id="efea7-106">[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([comment télécharger](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="efea7-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-opno-locsignalr-client-package"></a><span data-ttu-id="efea7-107">Installer SignalR le forfait client</span><span class="sxs-lookup"><span data-stu-id="efea7-107">Install the SignalR client package</span></span>

<span data-ttu-id="efea7-108">La SignalR bibliothèque client JavaScript est livrée sous forme de forfait [npm.](https://www.npmjs.com/)</span><span class="sxs-lookup"><span data-stu-id="efea7-108">The SignalR JavaScript client library is delivered as an [npm](https://www.npmjs.com/) package.</span></span> <span data-ttu-id="efea7-109">Les sections suivantes décrivent différentes façons d’installer la bibliothèque cliente.</span><span class="sxs-lookup"><span data-stu-id="efea7-109">The following sections outline different ways to install the client library.</span></span>

### <a name="install-with-npm"></a><span data-ttu-id="efea7-110">Installer avec npm</span><span class="sxs-lookup"><span data-stu-id="efea7-110">Install with npm</span></span>

<span data-ttu-id="efea7-111">Si vous utilisez Visual Studio, exécutez les commandes suivantes à partir de **Package Manager Console** dans le dossier racine.</span><span class="sxs-lookup"><span data-stu-id="efea7-111">If using Visual Studio, run the following commands from **Package Manager Console** while in the root folder.</span></span> <span data-ttu-id="efea7-112">Pour Visual Studio Code, exécutez les commandes suivantes à partir du **terminal intégré**.</span><span class="sxs-lookup"><span data-stu-id="efea7-112">For Visual Studio Code, run the following commands from the **Integrated Terminal**.</span></span>

::: moniker range=">= aspnetcore-3.0"

```bash
npm init -y
npm install @microsoft/signalr
```

<span data-ttu-id="efea7-113">npm installe le contenu de l’emballage dans le dossier \*node_modules.\\ \*</span><span class="sxs-lookup"><span data-stu-id="efea7-113">npm installs the package contents in the *node_modules\\@microsoft\signalr\dist\browser* folder.</span></span> <span data-ttu-id="efea7-114">Créez un nouveau dossier nommé *signaleur* sous le dossier *lib\\wwwroot.*</span><span class="sxs-lookup"><span data-stu-id="efea7-114">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="efea7-115">Copiez le fichier *signalur.js* sur le dossier *wwwroot-lib-signaler.*</span><span class="sxs-lookup"><span data-stu-id="efea7-115">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```bash
npm init -y
npm install @aspnet/signalr
```

<span data-ttu-id="efea7-116">npm installe le contenu de l’emballage dans le dossier \*node_modules.\\ \*</span><span class="sxs-lookup"><span data-stu-id="efea7-116">npm installs the package contents in the *node_modules\\@aspnet\signalr\dist\browser* folder.</span></span> <span data-ttu-id="efea7-117">Créez un nouveau dossier nommé *signaleur* sous le dossier *lib\\wwwroot.*</span><span class="sxs-lookup"><span data-stu-id="efea7-117">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="efea7-118">Copiez le fichier *signalur.js* sur le dossier *wwwroot-lib-signaler.*</span><span class="sxs-lookup"><span data-stu-id="efea7-118">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

::: moniker-end

<span data-ttu-id="efea7-119">Référence SignalR au client JavaScript dans l’élément. `<script>`</span><span class="sxs-lookup"><span data-stu-id="efea7-119">Reference the SignalR JavaScript client in the `<script>` element.</span></span> <span data-ttu-id="efea7-120">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="efea7-120">For example:</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a><span data-ttu-id="efea7-121">Utiliser un réseau de diffusion de contenu (CDN)</span><span class="sxs-lookup"><span data-stu-id="efea7-121">Use a Content Delivery Network (CDN)</span></span>

<span data-ttu-id="efea7-122">Pour utiliser la bibliothèque cliente sans la condition préalable de npm, faites référence à une copie hébergée par CDN de la bibliothèque cliente.</span><span class="sxs-lookup"><span data-stu-id="efea7-122">To use the client library without the npm prerequisite, reference a CDN-hosted copy of the client library.</span></span> <span data-ttu-id="efea7-123">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="efea7-123">For example:</span></span>

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/microsoft-signalr/3.1.3/signalr.min.js"></script>
```

<span data-ttu-id="efea7-124">La bibliothèque cliente est disponible sur les CDN suivants :</span><span class="sxs-lookup"><span data-stu-id="efea7-124">The client library is available on the following CDNs:</span></span>

::: moniker range=">= aspnetcore-3.0"

* [<span data-ttu-id="efea7-125">cdnjs (cdnjs)</span><span class="sxs-lookup"><span data-stu-id="efea7-125">cdnjs</span></span>](https://cdnjs.com/libraries/microsoft-signalr)
* [<span data-ttu-id="efea7-126">jsDelivr</span><span class="sxs-lookup"><span data-stu-id="efea7-126">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@microsoft/signalr)
* [<span data-ttu-id="efea7-127">unpkg</span><span class="sxs-lookup"><span data-stu-id="efea7-127">unpkg</span></span>](https://unpkg.com/@microsoft/signalr@next/dist/browser/signalr.min.js)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* [<span data-ttu-id="efea7-128">cdnjs (cdnjs)</span><span class="sxs-lookup"><span data-stu-id="efea7-128">cdnjs</span></span>](https://cdnjs.com/libraries/aspnet-signalr)
* [<span data-ttu-id="efea7-129">jsDelivr</span><span class="sxs-lookup"><span data-stu-id="efea7-129">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@aspnet/signalr)
* [<span data-ttu-id="efea7-130">unpkg</span><span class="sxs-lookup"><span data-stu-id="efea7-130">unpkg</span></span>](https://unpkg.com/@aspnet/signalr@next/dist/browser/signalr.min.js)

::: moniker-end

### <a name="install-with-libman"></a><span data-ttu-id="efea7-131">Installer avec LibMan</span><span class="sxs-lookup"><span data-stu-id="efea7-131">Install with LibMan</span></span>

<span data-ttu-id="efea7-132">[LibMan](xref:client-side/libman/index) peut être utilisé pour installer des fichiers spécifiques de bibliothèque client de la bibliothèque cliente hébergée par CDN.</span><span class="sxs-lookup"><span data-stu-id="efea7-132">[LibMan](xref:client-side/libman/index) can be used to install specific client library files from the CDN-hosted client library.</span></span> <span data-ttu-id="efea7-133">Par exemple, n’ajoutez que le fichier JavaScript minifié au projet.</span><span class="sxs-lookup"><span data-stu-id="efea7-133">For example, only add the minified JavaScript file to the project.</span></span> <span data-ttu-id="efea7-134">Pour plus de détails sur cette approche, voir [Ajouter la SignalR bibliothèque client](xref:tutorials/signalr#add-the-signalr-client-library).</span><span class="sxs-lookup"><span data-stu-id="efea7-134">For details on that approach, see [Add the SignalR client library](xref:tutorials/signalr#add-the-signalr-client-library).</span></span>

## <a name="connect-to-a-hub"></a><span data-ttu-id="efea7-135">Connectez-vous à un hub</span><span class="sxs-lookup"><span data-stu-id="efea7-135">Connect to a hub</span></span>

<span data-ttu-id="efea7-136">Le code suivant crée et démarre une connexion.</span><span class="sxs-lookup"><span data-stu-id="efea7-136">The following code creates and starts a connection.</span></span> <span data-ttu-id="efea7-137">Le nom du hub est insensible au cas.</span><span class="sxs-lookup"><span data-stu-id="efea7-137">The hub's name is case insensitive.</span></span>

[!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=9-13,43-45)]

### <a name="cross-origin-connections"></a><span data-ttu-id="efea7-138">Connexions d’origine croisée</span><span class="sxs-lookup"><span data-stu-id="efea7-138">Cross-origin connections</span></span>

<span data-ttu-id="efea7-139">En règle générale, les navigateurs chargent les connexions à partir du même domaine que la page demandée.</span><span class="sxs-lookup"><span data-stu-id="efea7-139">Typically, browsers load connections from the same domain as the requested page.</span></span> <span data-ttu-id="efea7-140">Cependant, il y a des occasions où une connexion à un autre domaine est nécessaire.</span><span class="sxs-lookup"><span data-stu-id="efea7-140">However, there are occasions when a connection to another domain is required.</span></span>

<span data-ttu-id="efea7-141">Pour empêcher un site malveillant de lire des données sensibles à partir d’un autre site, [les connexions inter-origines](xref:security/cors) sont désactivées par défaut.</span><span class="sxs-lookup"><span data-stu-id="efea7-141">To prevent a malicious site from reading sensitive data from another site, [cross-origin connections](xref:security/cors) are disabled by default.</span></span> <span data-ttu-id="efea7-142">Pour permettre une demande d’origine `Startup` croisée, activez-la dans la classe.</span><span class="sxs-lookup"><span data-stu-id="efea7-142">To allow a cross-origin request, enable it in the `Startup` class.</span></span>

[!code-csharp[Cross-origin connections](javascript-client/sample/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="efea7-143">Méthodes de moyeu d’appel du client</span><span class="sxs-lookup"><span data-stu-id="efea7-143">Call hub methods from client</span></span>

<span data-ttu-id="efea7-144">Les clients JavaScript appellent des méthodes publiques sur les hubs via la méthode [d’invoquer](/javascript/api/%40aspnet/signalr/hubconnection#invoke) de la [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span><span class="sxs-lookup"><span data-stu-id="efea7-144">JavaScript clients call public methods on hubs via the [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) method of the [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span></span> <span data-ttu-id="efea7-145">La `invoke` méthode accepte deux arguments :</span><span class="sxs-lookup"><span data-stu-id="efea7-145">The `invoke` method accepts two arguments:</span></span>

* <span data-ttu-id="efea7-146">Le nom de la méthode hub.</span><span class="sxs-lookup"><span data-stu-id="efea7-146">The name of the hub method.</span></span> <span data-ttu-id="efea7-147">Dans l’exemple suivant, le nom `SendMessage`de la méthode sur le moyeu est .</span><span class="sxs-lookup"><span data-stu-id="efea7-147">In the following example, the method name on the hub is `SendMessage`.</span></span>
* <span data-ttu-id="efea7-148">Tous les arguments définis dans la méthode du hub.</span><span class="sxs-lookup"><span data-stu-id="efea7-148">Any arguments defined in the hub method.</span></span> <span data-ttu-id="efea7-149">Dans l’exemple suivant, `message`le nom de l’argument est .</span><span class="sxs-lookup"><span data-stu-id="efea7-149">In the following example, the argument name is `message`.</span></span> <span data-ttu-id="efea7-150">L’exemple code utilise la syntaxe fonction fléchée qui est pris en charge dans les versions actuelles de tous les principaux navigateurs, sauf Internet Explorer.</span><span class="sxs-lookup"><span data-stu-id="efea7-150">The example code uses arrow function syntax that is supported in current versions of all major browsers except Internet Explorer.</span></span>

  [!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> <span data-ttu-id="efea7-151">Si vous utilisez Azure SignalR Service en mode Sans *serveur,* vous ne pouvez pas appeler les méthodes de moyeu d’un client.</span><span class="sxs-lookup"><span data-stu-id="efea7-151">If you're using Azure SignalR Service in *Serverless mode*, you cannot call hub methods from a client.</span></span> <span data-ttu-id="efea7-152">Pour plus d’informations, consultez la [ SignalR documentation](/azure/azure-signalr/signalr-concept-serverless-development-config)du Service .</span><span class="sxs-lookup"><span data-stu-id="efea7-152">For more information, see the [SignalR Service documentation](/azure/azure-signalr/signalr-concept-serverless-development-config).</span></span>

<span data-ttu-id="efea7-153">La `invoke` méthode renvoie une [promesse](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)JavaScript .</span><span class="sxs-lookup"><span data-stu-id="efea7-153">The `invoke` method returns a JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span></span> <span data-ttu-id="efea7-154">Le `Promise` est résolu avec la valeur de retour (le cas échéant) lorsque la méthode sur le serveur revient.</span><span class="sxs-lookup"><span data-stu-id="efea7-154">The `Promise` is resolved with the return value (if any) when the method on the server returns.</span></span> <span data-ttu-id="efea7-155">Si la méthode sur le serveur `Promise` lance une erreur, la est rejetée avec le message d’erreur.</span><span class="sxs-lookup"><span data-stu-id="efea7-155">If the method on the server throws an error, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="efea7-156">Utilisez `then` les `catch` méthodes `Promise` et les méthodes en `await` soi pour traiter ces cas (ou syntaxe).</span><span class="sxs-lookup"><span data-stu-id="efea7-156">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

<span data-ttu-id="efea7-157">La `send` méthode renvoie `Promise`un JavaScript .</span><span class="sxs-lookup"><span data-stu-id="efea7-157">The `send` method returns a JavaScript `Promise`.</span></span> <span data-ttu-id="efea7-158">Le `Promise` est résolu lorsque le message a été envoyé au serveur.</span><span class="sxs-lookup"><span data-stu-id="efea7-158">The `Promise` is resolved when the message has been sent to the server.</span></span> <span data-ttu-id="efea7-159">S’il y a une `Promise` erreur envoyant le message, le est rejeté avec le message d’erreur.</span><span class="sxs-lookup"><span data-stu-id="efea7-159">If there is an error sending the message, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="efea7-160">Utilisez `then` les `catch` méthodes `Promise` et les méthodes en `await` soi pour traiter ces cas (ou syntaxe).</span><span class="sxs-lookup"><span data-stu-id="efea7-160">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

> [!NOTE]
> <span data-ttu-id="efea7-161">L’utilisation `send` n’attend pas que le serveur a reçu le message.</span><span class="sxs-lookup"><span data-stu-id="efea7-161">Using `send` doesn't wait until the server has received the message.</span></span> <span data-ttu-id="efea7-162">Par conséquent, il n’est pas possible de retourner des données ou des erreurs à partir du serveur.</span><span class="sxs-lookup"><span data-stu-id="efea7-162">Consequently, it's not possible to return data or errors from the server.</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="efea7-163">Appelez les méthodes des clients à partir d’un hub</span><span class="sxs-lookup"><span data-stu-id="efea7-163">Call client methods from hub</span></span>

<span data-ttu-id="efea7-164">Pour recevoir des messages du hub, [on](/javascript/api/%40aspnet/signalr/hubconnection#on) définissez `HubConnection`une méthode en utilisant la méthode sur le .</span><span class="sxs-lookup"><span data-stu-id="efea7-164">To receive messages from the hub, define a method using the [on](/javascript/api/%40aspnet/signalr/hubconnection#on) method of the `HubConnection`.</span></span>

* <span data-ttu-id="efea7-165">Le nom de la méthode client JavaScript.</span><span class="sxs-lookup"><span data-stu-id="efea7-165">The name of the JavaScript client method.</span></span> <span data-ttu-id="efea7-166">Dans l’exemple suivant, `ReceiveMessage`le nom de la méthode est .</span><span class="sxs-lookup"><span data-stu-id="efea7-166">In the following example, the method name is `ReceiveMessage`.</span></span>
* <span data-ttu-id="efea7-167">Arguments le moyeu passe à la méthode.</span><span class="sxs-lookup"><span data-stu-id="efea7-167">Arguments the hub passes to the method.</span></span> <span data-ttu-id="efea7-168">Dans l’exemple suivant, `message`la valeur de l’argument est .</span><span class="sxs-lookup"><span data-stu-id="efea7-168">In the following example, the argument value is `message`.</span></span>

[!code-javascript[Receive calls from hub](javascript-client/sample/wwwroot/js/chat.js?range=14-19)]

<span data-ttu-id="efea7-169">Le code `connection.on` précédent s’exécute lorsque le code côté serveur l’appelle en utilisant la méthode [SendAsync.](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync)</span><span class="sxs-lookup"><span data-stu-id="efea7-169">The preceding code in `connection.on` runs when server-side code calls it using the [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) method.</span></span>

[!code-csharp[Call client-side](javascript-client/sample/hubs/chathub.cs?range=8-11)]

SignalR<span data-ttu-id="efea7-170">détermine la méthode du client à appeler en `SendAsync` faisant `connection.on`correspondre le nom de la méthode et les arguments définis dans et .</span><span class="sxs-lookup"><span data-stu-id="efea7-170"> determines which client method to call by matching the method name and arguments defined in `SendAsync` and `connection.on`.</span></span>

> [!NOTE]
> <span data-ttu-id="efea7-171">Comme une meilleure pratique, appelez `HubConnection` la `on`méthode [de départ](/javascript/api/%40aspnet/signalr/hubconnection#start) sur l’après .</span><span class="sxs-lookup"><span data-stu-id="efea7-171">As a best practice, call the [start](/javascript/api/%40aspnet/signalr/hubconnection#start) method on the `HubConnection` after `on`.</span></span> <span data-ttu-id="efea7-172">Cela garantit que vos gestionnaires sont enregistrés avant que tous les messages ne soient reçus.</span><span class="sxs-lookup"><span data-stu-id="efea7-172">Doing so ensures your handlers are registered before any messages are received.</span></span>

## <a name="error-handling-and-logging"></a><span data-ttu-id="efea7-173">Gestion et journalisation des erreurs</span><span class="sxs-lookup"><span data-stu-id="efea7-173">Error handling and logging</span></span>

<span data-ttu-id="efea7-174">Enchaîner une `catch` méthode `start` jusqu’à la fin de la méthode pour gérer les erreurs côté client.</span><span class="sxs-lookup"><span data-stu-id="efea7-174">Chain a `catch` method to the end of the `start` method to handle client-side errors.</span></span> <span data-ttu-id="efea7-175">Utilisez `console.error` pour les erreurs de sortie sur la console du navigateur.</span><span class="sxs-lookup"><span data-stu-id="efea7-175">Use `console.error` to output errors to the browser's console.</span></span>

[!code-javascript[Error handling](javascript-client/sample/wwwroot/js/chat.js?range=49-51)]

<span data-ttu-id="efea7-176">Configurez le traçage des journaux côté client en passant un enregistreur et un type d’événement à enregistrer lorsque la connexion est effectuée.</span><span class="sxs-lookup"><span data-stu-id="efea7-176">Setup client-side log tracing by passing a logger and type of event to log when the connection is made.</span></span> <span data-ttu-id="efea7-177">Les messages sont enregistrés avec le niveau de journal spécifié et plus élevé.</span><span class="sxs-lookup"><span data-stu-id="efea7-177">Messages are logged with the specified log level and higher.</span></span> <span data-ttu-id="efea7-178">Les niveaux de journal disponibles sont les suivants :</span><span class="sxs-lookup"><span data-stu-id="efea7-178">Available log levels are as follows:</span></span>

* <span data-ttu-id="efea7-179">`signalR.LogLevel.Error`&ndash; Messages d’erreur.</span><span class="sxs-lookup"><span data-stu-id="efea7-179">`signalR.LogLevel.Error` &ndash; Error messages.</span></span> <span data-ttu-id="efea7-180">Enregistre `Error` uniquement les messages.</span><span class="sxs-lookup"><span data-stu-id="efea7-180">Logs `Error` messages only.</span></span>
* <span data-ttu-id="efea7-181">`signalR.LogLevel.Warning`&ndash; Messages d’avertissement sur les erreurs potentielles.</span><span class="sxs-lookup"><span data-stu-id="efea7-181">`signalR.LogLevel.Warning` &ndash; Warning messages about potential errors.</span></span> <span data-ttu-id="efea7-182">`Warning`Journaux, et `Error` messages.</span><span class="sxs-lookup"><span data-stu-id="efea7-182">Logs `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="efea7-183">`signalR.LogLevel.Information`&ndash; Messages de statut sans erreurs.</span><span class="sxs-lookup"><span data-stu-id="efea7-183">`signalR.LogLevel.Information` &ndash; Status messages without errors.</span></span> <span data-ttu-id="efea7-184">Journaux `Information`, `Warning`, `Error` et des messages.</span><span class="sxs-lookup"><span data-stu-id="efea7-184">Logs `Information`, `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="efea7-185">`signalR.LogLevel.Trace`&ndash; Tracez des messages.</span><span class="sxs-lookup"><span data-stu-id="efea7-185">`signalR.LogLevel.Trace` &ndash; Trace messages.</span></span> <span data-ttu-id="efea7-186">Enregistre tout, y compris les données transportées entre le hub et le client.</span><span class="sxs-lookup"><span data-stu-id="efea7-186">Logs everything, including data transported between hub and client.</span></span>

<span data-ttu-id="efea7-187">Utilisez la méthode [configurer Encombrement](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) sur [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) pour configurer le niveau du journal.</span><span class="sxs-lookup"><span data-stu-id="efea7-187">Use the [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) to configure the log level.</span></span> <span data-ttu-id="efea7-188">Les messages sont connectés à la console du navigateur.</span><span class="sxs-lookup"><span data-stu-id="efea7-188">Messages are logged to the browser console.</span></span>

[!code-javascript[Logging levels](javascript-client/sample/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a><span data-ttu-id="efea7-189">Reconnecter les clients</span><span class="sxs-lookup"><span data-stu-id="efea7-189">Reconnect clients</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a><span data-ttu-id="efea7-190">Reconnectez-vous automatiquement</span><span class="sxs-lookup"><span data-stu-id="efea7-190">Automatically reconnect</span></span>

<span data-ttu-id="efea7-191">Le client JavaScript peut être configuré pour SignalR se reconnecter automatiquement en utilisant la `withAutomaticReconnect` méthode sur [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="efea7-191">The JavaScript client for SignalR can be configured to automatically reconnect using the `withAutomaticReconnect` method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span></span> <span data-ttu-id="efea7-192">Il ne se reconnecte pas automatiquement par défaut.</span><span class="sxs-lookup"><span data-stu-id="efea7-192">It won't automatically reconnect by default.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="efea7-193">Sans aucun paramètre, `withAutomaticReconnect()` configure le client pour attendre 0, 2, 10 et 30 secondes respectivement avant d’essayer chaque tentative de reconnexion, s’arrêtant après quatre tentatives infructueuses.</span><span class="sxs-lookup"><span data-stu-id="efea7-193">Without any parameters, `withAutomaticReconnect()` configures the client to wait 0, 2, 10, and 30 seconds respectively before trying each reconnect attempt, stopping after four failed attempts.</span></span>

<span data-ttu-id="efea7-194">Avant de commencer toute `HubConnection` tentative de `HubConnectionState.Reconnecting` reconnexion, la transition à l’état et le feu de ses `onreconnecting` rappels au lieu de la transition à l’état et le `Disconnected` déclenchement de ses `onclose` rappels comme un `HubConnection` sans reconnecter automatique configuré.</span><span class="sxs-lookup"><span data-stu-id="efea7-194">Before starting any reconnect attempts, the `HubConnection` will transition to the `HubConnectionState.Reconnecting` state and fire its `onreconnecting` callbacks instead of transitioning to the `Disconnected` state and triggering its `onclose` callbacks like a `HubConnection` without automatic reconnect configured.</span></span> <span data-ttu-id="efea7-195">Cela donne l’occasion d’avertir les utilisateurs que la connexion a été perdue et de désactiver les éléments d’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="efea7-195">This provides an opportunity to warn users that the connection has been lost and to disable UI elements.</span></span>

```javascript
connection.onreconnecting((error) => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="efea7-196">Si le client se reconnecte avec `HubConnection` succès au cours `Connected` de ses `onreconnected` quatre premières tentatives, le sera la transition vers l’état et le feu de ses rappels.</span><span class="sxs-lookup"><span data-stu-id="efea7-196">If the client successfully reconnects within its first four attempts, the `HubConnection` will transition back to the `Connected` state and fire its `onreconnected` callbacks.</span></span> <span data-ttu-id="efea7-197">Cela donne l’occasion d’informer les utilisateurs que la connexion a été rétablie.</span><span class="sxs-lookup"><span data-stu-id="efea7-197">This provides an opportunity to inform users the connection has been reestablished.</span></span>

<span data-ttu-id="efea7-198">Étant donné que la connexion semble `connectionId` entièrement nouvelle sur `onreconnected` le serveur, un nouveau sera fourni au rappel.</span><span class="sxs-lookup"><span data-stu-id="efea7-198">Since the connection looks entirely new to the server, a new `connectionId` will be provided to the `onreconnected` callback.</span></span>

> [!WARNING]
> <span data-ttu-id="efea7-199">Le `onreconnected` paramètre `connectionId` du rappel ne sera pas `HubConnection` défini si le a été configuré pour [sauter la négociation](xref:signalr/configuration#configure-client-options).</span><span class="sxs-lookup"><span data-stu-id="efea7-199">The `onreconnected` callback's `connectionId` parameter will be undefined if the `HubConnection` was configured to [skip negotiation](xref:signalr/configuration#configure-client-options).</span></span>

```javascript
connection.onreconnected((connectionId) => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="efea7-200">`withAutomaticReconnect()`ne configurera `HubConnection` pas les défaillances de démarrage initiales, de sorte que les échecs de démarrage doivent être manipulés manuellement :</span><span class="sxs-lookup"><span data-stu-id="efea7-200">`withAutomaticReconnect()` won't configure the `HubConnection` to retry initial start failures, so start failures need to be handled manually:</span></span>

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

<span data-ttu-id="efea7-201">Si le client ne réussit pas à `HubConnection` se reconnecter `Disconnected` au cours de ses quatre premières tentatives, le sera la transition vers l’état et le feu de ses [rappels onclose.](/javascript/api/%40aspnet/signalr/hubconnection#onclose)</span><span class="sxs-lookup"><span data-stu-id="efea7-201">If the client doesn't successfully reconnect within its first four attempts, the `HubConnection` will transition to the `Disconnected` state and fire its [onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) callbacks.</span></span> <span data-ttu-id="efea7-202">Cela donne l’occasion d’informer les utilisateurs de la connexion a été définitivement perdu et recommande de rafraîchir la page:</span><span class="sxs-lookup"><span data-stu-id="efea7-202">This provides an opportunity to inform users the connection has been permanently lost and recommend refreshing the page:</span></span>

```javascript
connection.onclose((error) => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="efea7-203">Afin de configurer un nombre personnalisé de tentatives de `withAutomaticReconnect` reconnexion avant de déconnecter ou de modifier le calendrier de reconnexion, accepte une série de numéros représentant le retard en millisecondes à attendre avant de commencer chaque tentative de reconnexion.</span><span class="sxs-lookup"><span data-stu-id="efea7-203">In order to configure a custom number of reconnect attempts before disconnecting or change the reconnect timing, `withAutomaticReconnect` accepts an array of numbers representing the delay in milliseconds to wait before starting each reconnect attempt.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

<span data-ttu-id="efea7-204">L’exemple précédent `HubConnection` configure le pour commencer à essayer se reconnecte immédiatement après la perte de la connexion.</span><span class="sxs-lookup"><span data-stu-id="efea7-204">The preceding example configures the `HubConnection` to start attempting reconnects immediately after the connection is lost.</span></span> <span data-ttu-id="efea7-205">Cela est également vrai pour la configuration par défaut.</span><span class="sxs-lookup"><span data-stu-id="efea7-205">This is also true for the default configuration.</span></span>

<span data-ttu-id="efea7-206">Si la première tentative de reconnexion échoue, la deuxième tentative de reconnexion commencera également immédiatement au lieu d’attendre 2 secondes comme elle le ferait dans la configuration par défaut.</span><span class="sxs-lookup"><span data-stu-id="efea7-206">If the first reconnect attempt fails, the second reconnect attempt will also start immediately instead of waiting 2 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="efea7-207">Si la deuxième tentative de reconnexion échoue, la troisième tentative de reconnexion commencera en 10 secondes, ce qui est à nouveau comme la configuration par défaut.</span><span class="sxs-lookup"><span data-stu-id="efea7-207">If the second reconnect attempt fails, the third reconnect attempt will start in 10 seconds which is again like the default configuration.</span></span>

<span data-ttu-id="efea7-208">Le comportement personnalisé diverge alors à nouveau du comportement par défaut en s’arrêtant après l’échec troisième tentative de reconnexion au lieu d’essayer une tentative de reconnexion de plus dans un autre 30 secondes comme il le ferait dans la configuration par défaut.</span><span class="sxs-lookup"><span data-stu-id="efea7-208">The custom behavior then diverges again from the default behavior by stopping after the third reconnect attempt failure instead of trying one more reconnect attempt in another 30 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="efea7-209">Si vous voulez encore plus de contrôle sur `withAutomaticReconnect` le calendrier et le `IRetryPolicy` nombre de tentatives `nextRetryDelayInMilliseconds`de reconnexion automatique, accepte un objet implémentant l’interface, qui a une méthode unique nommée .</span><span class="sxs-lookup"><span data-stu-id="efea7-209">If you want even more control over the timing and number of automatic reconnect attempts, `withAutomaticReconnect` accepts an object implementing the `IRetryPolicy` interface, which has a single method named `nextRetryDelayInMilliseconds`.</span></span>

<span data-ttu-id="efea7-210">`nextRetryDelayInMilliseconds`prend un seul argument `RetryContext`avec le type .</span><span class="sxs-lookup"><span data-stu-id="efea7-210">`nextRetryDelayInMilliseconds` takes a single argument with the type `RetryContext`.</span></span> <span data-ttu-id="efea7-211">Le `RetryContext` a trois `previousRetryCount` `elapsedMilliseconds` propriétés: , `retryReason` et qui sont un `number`, a `number` et un `Error` respectivement.</span><span class="sxs-lookup"><span data-stu-id="efea7-211">The `RetryContext` has three properties: `previousRetryCount`, `elapsedMilliseconds` and `retryReason` which are a `number`, a `number` and an `Error` respectively.</span></span> <span data-ttu-id="efea7-212">Avant la première tentative `previousRetryCount` `elapsedMilliseconds` de reconnexion, les deux et seront nuls, et le `retryReason` sera l’erreur qui a causé la connexion à perdre.</span><span class="sxs-lookup"><span data-stu-id="efea7-212">Before the first reconnect attempt, both `previousRetryCount` and `elapsedMilliseconds` will be zero, and the `retryReason` will be the Error that caused the connection to be lost.</span></span> <span data-ttu-id="efea7-213">Après chaque tentative de `previousRetryCount` réessaie ratée, `elapsedMilliseconds` sera incrémenté par un, sera mis à jour `retryReason` pour refléter la quantité de temps passé à se reconnecter jusqu’à présent en millisecondes, et le sera l’erreur qui a causé la dernière tentative de reconnexion d’échouer.</span><span class="sxs-lookup"><span data-stu-id="efea7-213">After each failed retry attempt, `previousRetryCount` will be incremented by one, `elapsedMilliseconds` will be updated to reflect the amount of time spent reconnecting so far in milliseconds, and the `retryReason` will be the Error that caused the last reconnect attempt to fail.</span></span>

<span data-ttu-id="efea7-214">`nextRetryDelayInMilliseconds`doit retourner soit un numéro représentant le nombre de millisecondes à attendre avant la prochaine tentative de reconnexion ou `null` si le `HubConnection` devrait cesser de se reconnecter.</span><span class="sxs-lookup"><span data-stu-id="efea7-214">`nextRetryDelayInMilliseconds` must return either a number representing the number of milliseconds to wait before the next reconnect attempt or `null` if the `HubConnection` should stop reconnecting.</span></span>

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

<span data-ttu-id="efea7-215">Alternativement, vous pouvez écrire du code qui reconnectera votre client manuellement comme démontré dans [Manuellement reconnecter](#manually-reconnect).</span><span class="sxs-lookup"><span data-stu-id="efea7-215">Alternatively, you can write code that will reconnect your client manually as demonstrated in [Manually reconnect](#manually-reconnect).</span></span>

::: moniker-end

### <a name="manually-reconnect"></a><span data-ttu-id="efea7-216">Reconnecter manuellement</span><span class="sxs-lookup"><span data-stu-id="efea7-216">Manually reconnect</span></span>

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="efea7-217">Avant 3.0, le client SignalR JavaScript ne se reconnecte pas automatiquement.</span><span class="sxs-lookup"><span data-stu-id="efea7-217">Prior to 3.0, the JavaScript client for SignalR doesn't automatically reconnect.</span></span> <span data-ttu-id="efea7-218">Vous devez écrire du code qui reconnectera votre client manuellement.</span><span class="sxs-lookup"><span data-stu-id="efea7-218">You must write code that will reconnect your client manually.</span></span>

::: moniker-end

<span data-ttu-id="efea7-219">Le code suivant démontre une approche de reconnexion manuelle typique :</span><span class="sxs-lookup"><span data-stu-id="efea7-219">The following code demonstrates a typical manual reconnection approach:</span></span>

1. <span data-ttu-id="efea7-220">Une fonction (dans ce `start` cas, la fonction) est créée pour démarrer la connexion.</span><span class="sxs-lookup"><span data-stu-id="efea7-220">A function (in this case, the `start` function) is created to start the connection.</span></span>
1. <span data-ttu-id="efea7-221">Appelez `start` la fonction dans `onclose` le gestionnaire d’événement de la connexion.</span><span class="sxs-lookup"><span data-stu-id="efea7-221">Call the `start` function in the connection's `onclose` event handler.</span></span>

[!code-javascript[Reconnect the JavaScript client](javascript-client/sample/wwwroot/js/chat.js?range=28-40)]

<span data-ttu-id="efea7-222">Une implémentation du monde réel utiliserait un back-off exponentiel ou retenterait un certain nombre de fois avant d’abandonner.</span><span class="sxs-lookup"><span data-stu-id="efea7-222">A real-world implementation would use an exponential back-off or retry a specified number of times before giving up.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="efea7-223">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="efea7-223">Additional resources</span></span>

* [<span data-ttu-id="efea7-224">Référence de l’API JavaScript</span><span class="sxs-lookup"><span data-stu-id="efea7-224">JavaScript API reference</span></span>](/javascript/api/?view=signalr-js-latest)
* [<span data-ttu-id="efea7-225">Tutoriel JavaScript</span><span class="sxs-lookup"><span data-stu-id="efea7-225">JavaScript tutorial</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="efea7-226">Tutoriel WebPack et TypeScript</span><span class="sxs-lookup"><span data-stu-id="efea7-226">WebPack and TypeScript tutorial</span></span>](xref:tutorials/signalr-typescript-webpack)
* [<span data-ttu-id="efea7-227">Moyeux</span><span class="sxs-lookup"><span data-stu-id="efea7-227">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="efea7-228">.NET client</span><span class="sxs-lookup"><span data-stu-id="efea7-228">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="efea7-229">Publication dans Azure</span><span class="sxs-lookup"><span data-stu-id="efea7-229">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="efea7-230">Demandes d’origine croisée (CORS)</span><span class="sxs-lookup"><span data-stu-id="efea7-230">Cross-Origin Requests (CORS)</span></span>](xref:security/cors)
* <span data-ttu-id="efea7-231">[Documentation SignalR sans serveur Azure Service](/azure/azure-signalr/signalr-concept-serverless-development-config)</span><span class="sxs-lookup"><span data-stu-id="efea7-231">[Azure SignalR Service serverless documentation](/azure/azure-signalr/signalr-concept-serverless-development-config)</span></span>
