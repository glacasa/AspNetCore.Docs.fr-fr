---
title: BlazorConfiguration du modèle d’hébergement ASP.net Core
author: guardrex
description: En savoir plus sur les scénarios supplémentaires pour ASP.NET Core la Blazor configuration du modèle d’hébergement.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/10/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/additional-scenarios
ms.openlocfilehash: b32710e515d111b7dd6556f1db55082cd56a82b5
ms.sourcegitcommit: 84150702757cf7a7b839485382420e8db8e92b9c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/05/2020
ms.locfileid: "87819000"
---
# <a name="aspnet-core-no-locblazor-hosting-model-configuration"></a><span data-ttu-id="f31a2-103">BlazorConfiguration du modèle d’hébergement ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="f31a2-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="f31a2-104">Par [Daniel Roth](https://github.com/danroth27), [MacKinnon argent](https://github.com/MackinnonBuck)et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="f31a2-104">By [Daniel Roth](https://github.com/danroth27), [Mackinnon Buck](https://github.com/MackinnonBuck), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="f31a2-105">Cet article traite de l’hébergement de la configuration du modèle.</span><span class="sxs-lookup"><span data-stu-id="f31a2-105">This article covers hosting model configuration.</span></span>

### <a name="no-locsignalr-cross-origin-negotiation-for-authentication"></a><span data-ttu-id="f31a2-106">SignalRnégociation Cross-Origin pour l’authentification</span><span class="sxs-lookup"><span data-stu-id="f31a2-106">SignalR cross-origin negotiation for authentication</span></span>

<span data-ttu-id="f31a2-107">*Cette section s’applique à Blazor WebAssembly .*</span><span class="sxs-lookup"><span data-stu-id="f31a2-107">*This section applies to Blazor WebAssembly.*</span></span>

<span data-ttu-id="f31a2-108">Pour configurer le SignalR client sous-jacent de manière à envoyer des informations d’identification, telles que des cookies ou des en-têtes d’authentification http :</span><span class="sxs-lookup"><span data-stu-id="f31a2-108">To configure SignalR's underlying client to send credentials, such as cookies or HTTP authentication headers:</span></span>

* <span data-ttu-id="f31a2-109">Utilisez <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> pour définir <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> les demandes Cross-Origin [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) :</span><span class="sxs-lookup"><span data-stu-id="f31a2-109">Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> to set <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> on cross-origin [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) requests:</span></span>

  ```csharp
  public class IncludeRequestCredentialsMessageHandler : DelegatingHandler
  {
      protected override Task<HttpResponseMessage> SendAsync(
          HttpRequestMessage request, CancellationToken cancellationToken)
      {
          request.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
          return base.SendAsync(request, cancellationToken);
      }
  }
  ```

* <span data-ttu-id="f31a2-110">Affectez <xref:System.Net.Http.HttpMessageHandler> à l' <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> option :</span><span class="sxs-lookup"><span data-stu-id="f31a2-110">Assign the <xref:System.Net.Http.HttpMessageHandler> to the <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> option:</span></span>

  ```csharp
  var connection = new HubConnectionBuilder()
      .WithUrl(new Uri("http://signalr.example.com"), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessageHandler { InnerHandler = innerHandler };
      }).Build();
  ```

<span data-ttu-id="f31a2-111">Pour plus d'informations, consultez <xref:signalr/configuration#configure-additional-options>.</span><span class="sxs-lookup"><span data-stu-id="f31a2-111">For more information, see <xref:signalr/configuration#configure-additional-options>.</span></span>

## <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="f31a2-112">Refléter l’état de la connexion dans l’interface utilisateur</span><span class="sxs-lookup"><span data-stu-id="f31a2-112">Reflect the connection state in the UI</span></span>

<span data-ttu-id="f31a2-113">*Cette section s’applique à Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="f31a2-113">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="f31a2-114">Lorsque le client détecte que la connexion a été perdue, une interface utilisateur par défaut est affichée à l’utilisateur pendant que le client tente de se reconnecter.</span><span class="sxs-lookup"><span data-stu-id="f31a2-114">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="f31a2-115">En cas d’échec de la reconnexion, l’utilisateur a la possibilité de réessayer.</span><span class="sxs-lookup"><span data-stu-id="f31a2-115">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="f31a2-116">Pour personnaliser l’interface utilisateur, définissez un élément avec un `id` de `components-reconnect-modal` dans le `<body>` de la `_Host.cshtml` Razor page :</span><span class="sxs-lookup"><span data-stu-id="f31a2-116">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the `_Host.cshtml` Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="f31a2-117">Ajoutez le code suivant à la feuille de style de l’application ( `wwwroot/css/app.css` ou `wwwroot/css/site.css` ) :</span><span class="sxs-lookup"><span data-stu-id="f31a2-117">Add the following to the app's stylesheet (`wwwroot/css/app.css` or `wwwroot/css/site.css`):</span></span>

```css
#components-reconnect-modal {
    display: none;
}

#components-reconnect-modal.components-reconnect-show {
    display: block;
}
```

<span data-ttu-id="f31a2-118">Le tableau suivant décrit les classes CSS appliquées à l' `components-reconnect-modal` élément.</span><span class="sxs-lookup"><span data-stu-id="f31a2-118">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="f31a2-119">Classe CSS</span><span class="sxs-lookup"><span data-stu-id="f31a2-119">CSS class</span></span>                       | <span data-ttu-id="f31a2-120">Détermine&hellip;</span><span class="sxs-lookup"><span data-stu-id="f31a2-120">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="f31a2-121">Connexion perdue.</span><span class="sxs-lookup"><span data-stu-id="f31a2-121">A lost connection.</span></span> <span data-ttu-id="f31a2-122">Le client tente de se reconnecter.</span><span class="sxs-lookup"><span data-stu-id="f31a2-122">The client is attempting to reconnect.</span></span> <span data-ttu-id="f31a2-123">Affichez le modal.</span><span class="sxs-lookup"><span data-stu-id="f31a2-123">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="f31a2-124">Une connexion active est rétablie sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="f31a2-124">An active connection is re-established to the server.</span></span> <span data-ttu-id="f31a2-125">Masquez le modal.</span><span class="sxs-lookup"><span data-stu-id="f31a2-125">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="f31a2-126">Échec de la reconnexion, probablement en raison d’une défaillance du réseau.</span><span class="sxs-lookup"><span data-stu-id="f31a2-126">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="f31a2-127">Pour tenter une reconnexion, appelez `window.Blazor.reconnect()` .</span><span class="sxs-lookup"><span data-stu-id="f31a2-127">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="f31a2-128">Reconnexion refusée.</span><span class="sxs-lookup"><span data-stu-id="f31a2-128">Reconnection rejected.</span></span> <span data-ttu-id="f31a2-129">Le serveur a été atteint mais a refusé la connexion, et l’état de l’utilisateur sur le serveur est perdu.</span><span class="sxs-lookup"><span data-stu-id="f31a2-129">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="f31a2-130">Pour recharger l’application, appelez `location.reload()` .</span><span class="sxs-lookup"><span data-stu-id="f31a2-130">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="f31a2-131">Cet état de connexion peut se produire dans les cas suivants :</span><span class="sxs-lookup"><span data-stu-id="f31a2-131">This connection state may result when:</span></span><ul><li><span data-ttu-id="f31a2-132">Un blocage dans le circuit côté serveur se produit.</span><span class="sxs-lookup"><span data-stu-id="f31a2-132">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="f31a2-133">Le client est déconnecté suffisamment longtemps pour que le serveur supprime l’état de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="f31a2-133">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="f31a2-134">Les instances des composants avec lesquels l’utilisateur interagit sont supprimées.</span><span class="sxs-lookup"><span data-stu-id="f31a2-134">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="f31a2-135">Le serveur est redémarré ou le processus de travail de l’application est recyclé.</span><span class="sxs-lookup"><span data-stu-id="f31a2-135">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

## <a name="render-mode"></a><span data-ttu-id="f31a2-136">Mode d’affichage</span><span class="sxs-lookup"><span data-stu-id="f31a2-136">Render mode</span></span>

<span data-ttu-id="f31a2-137">*Cette section s’applique à Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="f31a2-137">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="f31a2-138">Blazor Serverles applications sont configurées par défaut pour prérestituer l’interface utilisateur sur le serveur avant que la connexion cliente au serveur soit établie.</span><span class="sxs-lookup"><span data-stu-id="f31a2-138">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="f31a2-139">Il est configuré dans la `_Host.cshtml` Razor page :</span><span class="sxs-lookup"><span data-stu-id="f31a2-139">This is set up in the `_Host.cshtml` Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="f31a2-140"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>Configure si le composant :</span><span class="sxs-lookup"><span data-stu-id="f31a2-140"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="f31a2-141">Est prérendu dans la page.</span><span class="sxs-lookup"><span data-stu-id="f31a2-141">Is prerendered into the page.</span></span>
* <span data-ttu-id="f31a2-142">Est rendu en HTML statique sur la page ou s’il contient les informations nécessaires pour démarrer une Blazor application à partir de l’agent utilisateur.</span><span class="sxs-lookup"><span data-stu-id="f31a2-142">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <span data-ttu-id="f31a2-143">Mode d’affichage</span><span class="sxs-lookup"><span data-stu-id="f31a2-143">Render mode</span></span> | <span data-ttu-id="f31a2-144">Description</span><span class="sxs-lookup"><span data-stu-id="f31a2-144">Description</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="f31a2-145">Restitue le composant en HTML statique et comprend un marqueur pour une Blazor Server application.</span><span class="sxs-lookup"><span data-stu-id="f31a2-145">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="f31a2-146">Au démarrage de l’agent utilisateur, ce marqueur est utilisé pour démarrer une Blazor application.</span><span class="sxs-lookup"><span data-stu-id="f31a2-146">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="f31a2-147">Restitue un marqueur pour une Blazor Server application.</span><span class="sxs-lookup"><span data-stu-id="f31a2-147">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="f31a2-148">La sortie du composant n’est pas incluse.</span><span class="sxs-lookup"><span data-stu-id="f31a2-148">Output from the component isn't included.</span></span> <span data-ttu-id="f31a2-149">Au démarrage de l’agent utilisateur, ce marqueur est utilisé pour démarrer une Blazor application.</span><span class="sxs-lookup"><span data-stu-id="f31a2-149">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="f31a2-150">Génère le rendu du composant en HTML statique.</span><span class="sxs-lookup"><span data-stu-id="f31a2-150">Renders the component into static HTML.</span></span> |

<span data-ttu-id="f31a2-151">Le rendu des composants serveur à partir d’une page HTML statique n’est pas pris en charge.</span><span class="sxs-lookup"><span data-stu-id="f31a2-151">Rendering server components from a static HTML page isn't supported.</span></span>

## <a name="configure-the-no-locsignalr-client-for-no-locblazor-server-apps"></a><span data-ttu-id="f31a2-152">Configurer le SignalR client pour les Blazor Server applications</span><span class="sxs-lookup"><span data-stu-id="f31a2-152">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="f31a2-153">*Cette section s’applique à Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="f31a2-153">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="f31a2-154">Configurez le SignalR client utilisé par Blazor Server les applications dans le `Pages/_Host.cshtml` fichier.</span><span class="sxs-lookup"><span data-stu-id="f31a2-154">Configure the SignalR client used by Blazor Server apps in the `Pages/_Host.cshtml` file.</span></span> <span data-ttu-id="f31a2-155">Placez un script qui appelle `Blazor.start` après le `_framework/blazor.server.js` script et à l’intérieur de la `</body>` balise.</span><span class="sxs-lookup"><span data-stu-id="f31a2-155">Place a script that calls `Blazor.start` after the `_framework/blazor.server.js` script and inside the `</body>` tag.</span></span>

### <a name="logging"></a><span data-ttu-id="f31a2-156">Journalisation</span><span class="sxs-lookup"><span data-stu-id="f31a2-156">Logging</span></span>

<span data-ttu-id="f31a2-157">Pour configurer la SignalR journalisation du client :</span><span class="sxs-lookup"><span data-stu-id="f31a2-157">To configure SignalR client logging:</span></span>

* <span data-ttu-id="f31a2-158">Ajoutez un `autostart="false"` attribut à la `<script>` balise pour le `blazor.server.js` script.</span><span class="sxs-lookup"><span data-stu-id="f31a2-158">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="f31a2-159">Transmettez un objet de configuration ( `configureSignalR` ) qui appelle `configureLogging` avec le niveau de journalisation sur le générateur client.</span><span class="sxs-lookup"><span data-stu-id="f31a2-159">Pass in a configuration object (`configureSignalR`) that calls `configureLogging` with the log level on the client builder.</span></span>

```cshtml
    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start({
        configureSignalR: function (builder) {
          builder.configureLogging("information");
        }
      });
    </script>
</body>
```

<span data-ttu-id="f31a2-160">Dans l’exemple précédent, `information` est équivalent à un niveau de journal de <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="f31a2-160">In the preceding example, `information` is equivalent to a log level of <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>.</span></span>

### <a name="modify-the-reconnection-handler"></a><span data-ttu-id="f31a2-161">Modifier le gestionnaire de reconnexion</span><span class="sxs-lookup"><span data-stu-id="f31a2-161">Modify the reconnection handler</span></span>

<span data-ttu-id="f31a2-162">Les événements de connexion du circuit du gestionnaire de reconnexion peuvent être modifiés pour les comportements personnalisés, par exemple :</span><span class="sxs-lookup"><span data-stu-id="f31a2-162">The reconnection handler's circuit connection events can be modified for custom behaviors, such as:</span></span>

* <span data-ttu-id="f31a2-163">Pour avertir l’utilisateur si la connexion est abandonnée.</span><span class="sxs-lookup"><span data-stu-id="f31a2-163">To notify the user if the connection is dropped.</span></span>
* <span data-ttu-id="f31a2-164">Pour effectuer la journalisation (à partir du client) lorsqu’un circuit est connecté.</span><span class="sxs-lookup"><span data-stu-id="f31a2-164">To perform logging (from the client) when a circuit is connected.</span></span>

<span data-ttu-id="f31a2-165">Pour modifier les événements de connexion :</span><span class="sxs-lookup"><span data-stu-id="f31a2-165">To modify the connection events:</span></span>

* <span data-ttu-id="f31a2-166">Ajoutez un `autostart="false"` attribut à la `<script>` balise pour le `blazor.server.js` script.</span><span class="sxs-lookup"><span data-stu-id="f31a2-166">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="f31a2-167">Enregistrer les rappels pour les modifications de connexion pour les connexions abandonnées ( `onConnectionDown` ) et les connexions établies/rétablies ( `onConnectionUp` ).</span><span class="sxs-lookup"><span data-stu-id="f31a2-167">Register callbacks for connection changes for dropped connections (`onConnectionDown`) and established/re-established connections (`onConnectionUp`).</span></span> <span data-ttu-id="f31a2-168">**Les deux** `onConnectionDown` et `onConnectionUp` doivent être spécifiés.</span><span class="sxs-lookup"><span data-stu-id="f31a2-168">**Both** `onConnectionDown` and `onConnectionUp` must be specified.</span></span>

```cshtml
    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start({
        reconnectionHandler: {
          onConnectionDown: (options, error) => console.error(error);
          onConnectionUp: () => console.log("Up, up, and away!");
        }
      });
    </script>
</body>
```

### <a name="adjust-the-reconnection-retry-count-and-interval"></a><span data-ttu-id="f31a2-169">Ajuster le nombre et l’intervalle de tentatives de reconnexion</span><span class="sxs-lookup"><span data-stu-id="f31a2-169">Adjust the reconnection retry count and interval</span></span>

<span data-ttu-id="f31a2-170">Pour régler le nombre et l’intervalle de nouvelles tentatives de connexion :</span><span class="sxs-lookup"><span data-stu-id="f31a2-170">To adjust the reconnection retry count and interval:</span></span>

* <span data-ttu-id="f31a2-171">Ajoutez un `autostart="false"` attribut à la `<script>` balise pour le `blazor.server.js` script.</span><span class="sxs-lookup"><span data-stu-id="f31a2-171">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="f31a2-172">Définissez le nombre de tentatives ( `maxRetries` ) et la période en millisecondes autorisées pour chaque tentative de nouvelle tentative ( `retryIntervalMilliseconds` ).</span><span class="sxs-lookup"><span data-stu-id="f31a2-172">Set the number of retries (`maxRetries`) and period in milliseconds permitted for each retry attempt (`retryIntervalMilliseconds`).</span></span>

```cshtml
    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start({
        reconnectionOptions: {
          maxRetries: 3,
          retryIntervalMilliseconds: 2000
        }
      });
    </script>
</body>
```

### <a name="hide-or-replace-the-reconnection-display"></a><span data-ttu-id="f31a2-173">Masquer ou remplacer l’affichage de reconnexion</span><span class="sxs-lookup"><span data-stu-id="f31a2-173">Hide or replace the reconnection display</span></span>

<span data-ttu-id="f31a2-174">Pour masquer l’affichage de reconnexion :</span><span class="sxs-lookup"><span data-stu-id="f31a2-174">To hide the reconnection display:</span></span>

* <span data-ttu-id="f31a2-175">Ajoutez un `autostart="false"` attribut à la `<script>` balise pour le `blazor.server.js` script.</span><span class="sxs-lookup"><span data-stu-id="f31a2-175">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="f31a2-176">Définissez le gestionnaire de reconnexion `_reconnectionDisplay` sur un objet vide ( `{}` ou `new Object()` ).</span><span class="sxs-lookup"><span data-stu-id="f31a2-176">Set the reconnection handler's `_reconnectionDisplay` to an empty object (`{}` or `new Object()`).</span></span>

```cshtml
    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      window.addEventListener('beforeunload', function () {
        Blazor.defaultReconnectionHandler._reconnectionDisplay = {};
      });
    </script>
</body>
```

<span data-ttu-id="f31a2-177">Pour remplacer l’affichage de reconnexion, `_reconnectionDisplay` dans l’exemple précédent, définissez l’élément pour l’affichage :</span><span class="sxs-lookup"><span data-stu-id="f31a2-177">To replace the reconnection display, set `_reconnectionDisplay` in the preceding example to the element for display:</span></span>

```javascript
Blazor.defaultReconnectionHandler._reconnectionDisplay = 
  document.getElementById("{ELEMENT ID}");
```

<span data-ttu-id="f31a2-178">L’espace réservé `{ELEMENT ID}` est l’ID de l’élément HTML à afficher.</span><span class="sxs-lookup"><span data-stu-id="f31a2-178">The placeholder `{ELEMENT ID}` is the ID of the HTML element to display.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="influence-html-head-tag-elements"></a><span data-ttu-id="f31a2-179">Influencer les `<head>` éléments de balise HTML</span><span class="sxs-lookup"><span data-stu-id="f31a2-179">Influence HTML `<head>` tag elements</span></span>

<span data-ttu-id="f31a2-180">*Cette section s’applique à Blazor WebAssembly et Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="f31a2-180">*This section applies to Blazor WebAssembly and Blazor Server.*</span></span>

<span data-ttu-id="f31a2-181">Lors du rendu, `Title` les `Link` composants, et `Meta` ajoutent ou mettent à jour des données dans les `<head>` éléments de balise HTML :</span><span class="sxs-lookup"><span data-stu-id="f31a2-181">When rendered, the `Title`, `Link`, and `Meta` components add or update data in the HTML `<head>` tag elements:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Web.Extensions.Head

<Title Value="{TITLE}" />
<Link href="{URL}" rel="stylesheet" />
<Meta content="{DESCRIPTION}" name="description" />
```

<span data-ttu-id="f31a2-182">Dans l’exemple précédent, les espaces réservés pour `{TITLE}` , `{URL}` et `{DESCRIPTION}` sont des valeurs de chaîne, des Razor variables ou des Razor expressions.</span><span class="sxs-lookup"><span data-stu-id="f31a2-182">In the preceding example, placeholders for `{TITLE}`, `{URL}`, and `{DESCRIPTION}` are string values, Razor variables, or Razor expressions.</span></span>

<span data-ttu-id="f31a2-183">Les caractéristiques suivantes s’appliquent :</span><span class="sxs-lookup"><span data-stu-id="f31a2-183">The following characteristics apply:</span></span>

* <span data-ttu-id="f31a2-184">Le prérendu côté serveur est pris en charge.</span><span class="sxs-lookup"><span data-stu-id="f31a2-184">Server-side prerendering is supported.</span></span>
* <span data-ttu-id="f31a2-185">Le `Value` paramètre est le seul paramètre valide pour le `Title` composant.</span><span class="sxs-lookup"><span data-stu-id="f31a2-185">The `Value` parameter is the only valid parameter for the `Title` component.</span></span>
* <span data-ttu-id="f31a2-186">Les attributs HTML fournis aux `Meta` `Link` composants et sont capturés dans des [attributs supplémentaires](xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters) et passés à la balise HTML rendue.</span><span class="sxs-lookup"><span data-stu-id="f31a2-186">HTML attributes provided to the `Meta` and `Link` components are captured in [additional attributes](xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters) and passed through to the rendered HTML tag.</span></span>
* <span data-ttu-id="f31a2-187">Pour plusieurs `Title` composants, le titre de la page reflète le `Value` du dernier `Title` composant restitué.</span><span class="sxs-lookup"><span data-stu-id="f31a2-187">For multiple `Title` components, the title of the page reflects the `Value` of the last `Title` component rendered.</span></span>
* <span data-ttu-id="f31a2-188">Si plusieurs `Meta` `Link` composants ou sont inclus avec des attributs identiques, une seule balise HTML est restituée pour chaque `Meta` `Link` composant ou.</span><span class="sxs-lookup"><span data-stu-id="f31a2-188">If multiple `Meta` or `Link` components are included with identical attributes, there's exactly one HTML tag rendered per `Meta` or `Link` component.</span></span> <span data-ttu-id="f31a2-189">Deux `Meta` `Link` composants ou ne peuvent pas faire référence à la même balise HTML rendue.</span><span class="sxs-lookup"><span data-stu-id="f31a2-189">Two `Meta` or `Link` components can't refer to the same rendered HTML tag.</span></span>
* <span data-ttu-id="f31a2-190">Les modifications apportées aux paramètres des `Meta` composants ou existants `Link` sont reflétées dans leurs balises HTML rendues.</span><span class="sxs-lookup"><span data-stu-id="f31a2-190">Changes to the parameters of existing `Meta` or `Link` components are reflected in their rendered HTML tags.</span></span>
* <span data-ttu-id="f31a2-191">Lorsque les `Link` `Meta` composants ou ne sont plus restitués et donc supprimés par l’infrastructure, leurs balises HTML rendues sont supprimées.</span><span class="sxs-lookup"><span data-stu-id="f31a2-191">When the `Link` or `Meta` components are no longer rendered and thus disposed by the framework, their rendered HTML tags are removed.</span></span>

<span data-ttu-id="f31a2-192">Quand l’un des composants de l’infrastructure est utilisé dans un composant enfant, la balise HTML rendue influence tout autre composant enfant du composant parent, à condition que le composant enfant contenant le composant d’infrastructure soit rendu.</span><span class="sxs-lookup"><span data-stu-id="f31a2-192">When one of the framework components is used in a child component, the rendered HTML tag influences any other child component of the parent component as long as the child component containing the framework component is rendered.</span></span> <span data-ttu-id="f31a2-193">La distinction entre l’utilisation de l’un de ces composants d’infrastructure dans un composant enfant et la mise en place d’une balise HTML dans `wwwroot/index.html` ou `Pages/_Host.cshtml` est que la balise HTML rendue d’un composant d’infrastructure :</span><span class="sxs-lookup"><span data-stu-id="f31a2-193">The distinction between using the one of these framework components in a child component and placing a an HTML tag in `wwwroot/index.html` or `Pages/_Host.cshtml` is that a framework component's rendered HTML tag:</span></span>

* <span data-ttu-id="f31a2-194">Peut être modifié par l’état de l’application.</span><span class="sxs-lookup"><span data-stu-id="f31a2-194">Can be modified by application state.</span></span> <span data-ttu-id="f31a2-195">Une balise HTML codée en dur ne peut pas être modifiée par l’état de l’application.</span><span class="sxs-lookup"><span data-stu-id="f31a2-195">A hard-coded HTML tag can't be modified by application state.</span></span>
* <span data-ttu-id="f31a2-196">Est supprimé du code HTML `<head>` lorsque le composant parent n’est plus rendu.</span><span class="sxs-lookup"><span data-stu-id="f31a2-196">Is removed from the HTML `<head>` when the parent component is no longer rendered.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="f31a2-197">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="f31a2-197">Additional resources</span></span>

* <xref:fundamentals/logging/index>
