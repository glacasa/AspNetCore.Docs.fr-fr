---
title: BlazorConfiguration du modèle d’hébergement ASP.net Core
author: guardrex
description: En savoir plus sur les scénarios supplémentaires pour ASP.NET Core la Blazor configuration du modèle d’hébergement.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/07/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/additional-scenarios
ms.openlocfilehash: e62cb2ab865fbf57166d5ec3d1344183c00c2095
ms.sourcegitcommit: fa89d6553378529ae86b388689ac2c6f38281bb9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86059837"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="11122-103">BlazorConfiguration du modèle d’hébergement ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="11122-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="11122-104">Par [Daniel Roth](https://github.com/danroth27) et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="11122-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="11122-105">Cet article traite de l’hébergement de la configuration du modèle.</span><span class="sxs-lookup"><span data-stu-id="11122-105">This article covers hosting model configuration.</span></span>

### <a name="signalr-cross-origin-negotiation-for-authentication"></a>SignalR<span data-ttu-id="11122-106">négociation Cross-Origin pour l’authentification</span><span class="sxs-lookup"><span data-stu-id="11122-106"> cross-origin negotiation for authentication</span></span>

<span data-ttu-id="11122-107">*Cette section s’applique à Blazor WebAssembly .*</span><span class="sxs-lookup"><span data-stu-id="11122-107">*This section applies to Blazor WebAssembly.*</span></span>

<span data-ttu-id="11122-108">Pour configurer le SignalR client sous-jacent de manière à envoyer des informations d’identification, telles que des cookies ou des en-têtes d’authentification http :</span><span class="sxs-lookup"><span data-stu-id="11122-108">To configure SignalR's underlying client to send credentials, such as cookies or HTTP authentication headers:</span></span>

* <span data-ttu-id="11122-109">Utilisez <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> pour définir <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> les demandes Cross-Origin [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) :</span><span class="sxs-lookup"><span data-stu-id="11122-109">Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> to set <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> on cross-origin [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) requests:</span></span>

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

* <span data-ttu-id="11122-110">Affectez <xref:System.Net.Http.HttpMessageHandler> à l' <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> option :</span><span class="sxs-lookup"><span data-stu-id="11122-110">Assign the <xref:System.Net.Http.HttpMessageHandler> to the <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> option:</span></span>

  ```csharp
  var connection = new HubConnectionBuilder()
      .WithUrl(new Uri("http://signalr.example.com"), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessageHandler { InnerHandler = innerHandler };
      }).Build();
  ```

<span data-ttu-id="11122-111">Pour plus d’informations, consultez <xref:signalr/configuration#configure-additional-options>.</span><span class="sxs-lookup"><span data-stu-id="11122-111">For more information, see <xref:signalr/configuration#configure-additional-options>.</span></span>

## <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="11122-112">Refléter l’état de la connexion dans l’interface utilisateur</span><span class="sxs-lookup"><span data-stu-id="11122-112">Reflect the connection state in the UI</span></span>

<span data-ttu-id="11122-113">*Cette section s’applique à Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="11122-113">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="11122-114">Lorsque le client détecte que la connexion a été perdue, une interface utilisateur par défaut est affichée à l’utilisateur pendant que le client tente de se reconnecter.</span><span class="sxs-lookup"><span data-stu-id="11122-114">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="11122-115">En cas d’échec de la reconnexion, l’utilisateur a la possibilité de réessayer.</span><span class="sxs-lookup"><span data-stu-id="11122-115">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="11122-116">Pour personnaliser l’interface utilisateur, définissez un élément avec un `id` de `components-reconnect-modal` dans le `<body>` de la `_Host.cshtml` Razor page :</span><span class="sxs-lookup"><span data-stu-id="11122-116">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the `_Host.cshtml` Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="11122-117">Ajoutez le code suivant à la feuille de style de l’application ( `wwwroot/css/app.css` ou `wwwroot/css/site.css` ) :</span><span class="sxs-lookup"><span data-stu-id="11122-117">Add the following to the app's stylesheet (`wwwroot/css/app.css` or `wwwroot/css/site.css`):</span></span>

```css
#components-reconnect-modal {
    display: none;
}

#components-reconnect-modal.components-reconnect-show {
    display: block;
}
```

<span data-ttu-id="11122-118">Le tableau suivant décrit les classes CSS appliquées à l' `components-reconnect-modal` élément.</span><span class="sxs-lookup"><span data-stu-id="11122-118">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="11122-119">Classe CSS</span><span class="sxs-lookup"><span data-stu-id="11122-119">CSS class</span></span>                       | <span data-ttu-id="11122-120">Détermine&hellip;</span><span class="sxs-lookup"><span data-stu-id="11122-120">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="11122-121">Connexion perdue.</span><span class="sxs-lookup"><span data-stu-id="11122-121">A lost connection.</span></span> <span data-ttu-id="11122-122">Le client tente de se reconnecter.</span><span class="sxs-lookup"><span data-stu-id="11122-122">The client is attempting to reconnect.</span></span> <span data-ttu-id="11122-123">Affichez le modal.</span><span class="sxs-lookup"><span data-stu-id="11122-123">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="11122-124">Une connexion active est rétablie sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="11122-124">An active connection is re-established to the server.</span></span> <span data-ttu-id="11122-125">Masquez le modal.</span><span class="sxs-lookup"><span data-stu-id="11122-125">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="11122-126">Échec de la reconnexion, probablement en raison d’une défaillance du réseau.</span><span class="sxs-lookup"><span data-stu-id="11122-126">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="11122-127">Pour tenter une reconnexion, appelez `window.Blazor.reconnect()` .</span><span class="sxs-lookup"><span data-stu-id="11122-127">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="11122-128">Reconnexion refusée.</span><span class="sxs-lookup"><span data-stu-id="11122-128">Reconnection rejected.</span></span> <span data-ttu-id="11122-129">Le serveur a été atteint mais a refusé la connexion, et l’état de l’utilisateur sur le serveur est perdu.</span><span class="sxs-lookup"><span data-stu-id="11122-129">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="11122-130">Pour recharger l’application, appelez `location.reload()` .</span><span class="sxs-lookup"><span data-stu-id="11122-130">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="11122-131">Cet état de connexion peut se produire dans les cas suivants :</span><span class="sxs-lookup"><span data-stu-id="11122-131">This connection state may result when:</span></span><ul><li><span data-ttu-id="11122-132">Un blocage dans le circuit côté serveur se produit.</span><span class="sxs-lookup"><span data-stu-id="11122-132">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="11122-133">Le client est déconnecté suffisamment longtemps pour que le serveur supprime l’état de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="11122-133">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="11122-134">Les instances des composants avec lesquels l’utilisateur interagit sont supprimées.</span><span class="sxs-lookup"><span data-stu-id="11122-134">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="11122-135">Le serveur est redémarré ou le processus de travail de l’application est recyclé.</span><span class="sxs-lookup"><span data-stu-id="11122-135">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

## <a name="render-mode"></a><span data-ttu-id="11122-136">Mode d’affichage</span><span class="sxs-lookup"><span data-stu-id="11122-136">Render mode</span></span>

<span data-ttu-id="11122-137">*Cette section s’applique à Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="11122-137">*This section applies to Blazor Server.*</span></span>

Blazor Server<span data-ttu-id="11122-138">les applications sont configurées par défaut pour prérestituer l’interface utilisateur sur le serveur avant que la connexion cliente au serveur soit établie.</span><span class="sxs-lookup"><span data-stu-id="11122-138"> apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="11122-139">Il est configuré dans la `_Host.cshtml` Razor page :</span><span class="sxs-lookup"><span data-stu-id="11122-139">This is set up in the `_Host.cshtml` Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="11122-140"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>Configure si le composant :</span><span class="sxs-lookup"><span data-stu-id="11122-140"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="11122-141">Est prérendu dans la page.</span><span class="sxs-lookup"><span data-stu-id="11122-141">Is prerendered into the page.</span></span>
* <span data-ttu-id="11122-142">Est rendu en HTML statique sur la page ou s’il contient les informations nécessaires pour démarrer une Blazor application à partir de l’agent utilisateur.</span><span class="sxs-lookup"><span data-stu-id="11122-142">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <span data-ttu-id="11122-143">Mode d’affichage</span><span class="sxs-lookup"><span data-stu-id="11122-143">Render mode</span></span> | <span data-ttu-id="11122-144">Description</span><span class="sxs-lookup"><span data-stu-id="11122-144">Description</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="11122-145">Restitue le composant en HTML statique et comprend un marqueur pour une Blazor Server application.</span><span class="sxs-lookup"><span data-stu-id="11122-145">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="11122-146">Au démarrage de l’agent utilisateur, ce marqueur est utilisé pour démarrer une Blazor application.</span><span class="sxs-lookup"><span data-stu-id="11122-146">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="11122-147">Restitue un marqueur pour une Blazor Server application.</span><span class="sxs-lookup"><span data-stu-id="11122-147">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="11122-148">La sortie du composant n’est pas incluse.</span><span class="sxs-lookup"><span data-stu-id="11122-148">Output from the component isn't included.</span></span> <span data-ttu-id="11122-149">Au démarrage de l’agent utilisateur, ce marqueur est utilisé pour démarrer une Blazor application.</span><span class="sxs-lookup"><span data-stu-id="11122-149">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="11122-150">Génère le rendu du composant en HTML statique.</span><span class="sxs-lookup"><span data-stu-id="11122-150">Renders the component into static HTML.</span></span> |

<span data-ttu-id="11122-151">Le rendu des composants serveur à partir d’une page HTML statique n’est pas pris en charge.</span><span class="sxs-lookup"><span data-stu-id="11122-151">Rendering server components from a static HTML page isn't supported.</span></span>

## <a name="configure-the-signalr-client-for-blazor-server-apps"></a><span data-ttu-id="11122-152">Configurer le SignalR client pour les Blazor Server applications</span><span class="sxs-lookup"><span data-stu-id="11122-152">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="11122-153">*Cette section s’applique à Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="11122-153">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="11122-154">Parfois, vous devez configurer le SignalR client utilisé par les Blazor Server applications.</span><span class="sxs-lookup"><span data-stu-id="11122-154">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="11122-155">Par exemple, vous pouvez configurer la journalisation sur le SignalR client pour diagnostiquer un problème de connexion.</span><span class="sxs-lookup"><span data-stu-id="11122-155">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="11122-156">Pour configurer le SignalR client dans le `Pages/_Host.cshtml` fichier :</span><span class="sxs-lookup"><span data-stu-id="11122-156">To configure the SignalR client in the `Pages/_Host.cshtml` file:</span></span>

* <span data-ttu-id="11122-157">Ajoutez un `autostart="false"` attribut à la `<script>` balise pour le `blazor.server.js` script.</span><span class="sxs-lookup"><span data-stu-id="11122-157">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="11122-158">Appelez `Blazor.start` et transmettez un objet de configuration qui spécifie le SignalR Générateur.</span><span class="sxs-lookup"><span data-stu-id="11122-158">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

```html
<script src="_framework/blazor.server.js" autostart="false"></script>
<script>
  Blazor.start({
    configureSignalR: function (builder) {
      builder.configureLogging("information"); // LogLevel.Information
    }
  });
</script>
```

## <a name="additional-resources"></a><span data-ttu-id="11122-159">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="11122-159">Additional resources</span></span>

* <xref:fundamentals/logging/index>
