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
# <a name="aspnet-core-no-locblazor-hosting-model-configuration"></a>BlazorConfiguration du modèle d’hébergement ASP.net Core

Par [Daniel Roth](https://github.com/danroth27), [MacKinnon argent](https://github.com/MackinnonBuck)et [Luke Latham](https://github.com/guardrex)

Cet article traite de l’hébergement de la configuration du modèle.

### <a name="no-locsignalr-cross-origin-negotiation-for-authentication"></a>SignalRnégociation Cross-Origin pour l’authentification

*Cette section s’applique à Blazor WebAssembly .*

Pour configurer le SignalR client sous-jacent de manière à envoyer des informations d’identification, telles que des cookies ou des en-têtes d’authentification http :

* Utilisez <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> pour définir <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> les demandes Cross-Origin [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) :

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

* Affectez <xref:System.Net.Http.HttpMessageHandler> à l' <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> option :

  ```csharp
  var connection = new HubConnectionBuilder()
      .WithUrl(new Uri("http://signalr.example.com"), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessageHandler { InnerHandler = innerHandler };
      }).Build();
  ```

Pour plus d'informations, consultez <xref:signalr/configuration#configure-additional-options>.

## <a name="reflect-the-connection-state-in-the-ui"></a>Refléter l’état de la connexion dans l’interface utilisateur

*Cette section s’applique à Blazor Server .*

Lorsque le client détecte que la connexion a été perdue, une interface utilisateur par défaut est affichée à l’utilisateur pendant que le client tente de se reconnecter. En cas d’échec de la reconnexion, l’utilisateur a la possibilité de réessayer.

Pour personnaliser l’interface utilisateur, définissez un élément avec un `id` de `components-reconnect-modal` dans le `<body>` de la `_Host.cshtml` Razor page :

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

Ajoutez le code suivant à la feuille de style de l’application ( `wwwroot/css/app.css` ou `wwwroot/css/site.css` ) :

```css
#components-reconnect-modal {
    display: none;
}

#components-reconnect-modal.components-reconnect-show {
    display: block;
}
```

Le tableau suivant décrit les classes CSS appliquées à l' `components-reconnect-modal` élément.

| Classe CSS                       | Détermine&hellip; |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | Connexion perdue. Le client tente de se reconnecter. Affichez le modal. |
| `components-reconnect-hide`     | Une connexion active est rétablie sur le serveur. Masquez le modal. |
| `components-reconnect-failed`   | Échec de la reconnexion, probablement en raison d’une défaillance du réseau. Pour tenter une reconnexion, appelez `window.Blazor.reconnect()` . |
| `components-reconnect-rejected` | Reconnexion refusée. Le serveur a été atteint mais a refusé la connexion, et l’état de l’utilisateur sur le serveur est perdu. Pour recharger l’application, appelez `location.reload()` . Cet état de connexion peut se produire dans les cas suivants :<ul><li>Un blocage dans le circuit côté serveur se produit.</li><li>Le client est déconnecté suffisamment longtemps pour que le serveur supprime l’état de l’utilisateur. Les instances des composants avec lesquels l’utilisateur interagit sont supprimées.</li><li>Le serveur est redémarré ou le processus de travail de l’application est recyclé.</li></ul> |

## <a name="render-mode"></a>Mode d’affichage

*Cette section s’applique à Blazor Server .*

Blazor Serverles applications sont configurées par défaut pour prérestituer l’interface utilisateur sur le serveur avant que la connexion cliente au serveur soit établie. Il est configuré dans la `_Host.cshtml` Razor page :

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>Configure si le composant :

* Est prérendu dans la page.
* Est rendu en HTML statique sur la page ou s’il contient les informations nécessaires pour démarrer une Blazor application à partir de l’agent utilisateur.

| Mode d’affichage | Description |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Restitue le composant en HTML statique et comprend un marqueur pour une Blazor Server application. Au démarrage de l’agent utilisateur, ce marqueur est utilisé pour démarrer une Blazor application. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Restitue un marqueur pour une Blazor Server application. La sortie du composant n’est pas incluse. Au démarrage de l’agent utilisateur, ce marqueur est utilisé pour démarrer une Blazor application. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Génère le rendu du composant en HTML statique. |

Le rendu des composants serveur à partir d’une page HTML statique n’est pas pris en charge.

## <a name="configure-the-no-locsignalr-client-for-no-locblazor-server-apps"></a>Configurer le SignalR client pour les Blazor Server applications

*Cette section s’applique à Blazor Server .*

Configurez le SignalR client utilisé par Blazor Server les applications dans le `Pages/_Host.cshtml` fichier. Placez un script qui appelle `Blazor.start` après le `_framework/blazor.server.js` script et à l’intérieur de la `</body>` balise.

### <a name="logging"></a>Journalisation

Pour configurer la SignalR journalisation du client :

* Ajoutez un `autostart="false"` attribut à la `<script>` balise pour le `blazor.server.js` script.
* Transmettez un objet de configuration ( `configureSignalR` ) qui appelle `configureLogging` avec le niveau de journalisation sur le générateur client.

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

Dans l’exemple précédent, `information` est équivalent à un niveau de journal de <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType> .

### <a name="modify-the-reconnection-handler"></a>Modifier le gestionnaire de reconnexion

Les événements de connexion du circuit du gestionnaire de reconnexion peuvent être modifiés pour les comportements personnalisés, par exemple :

* Pour avertir l’utilisateur si la connexion est abandonnée.
* Pour effectuer la journalisation (à partir du client) lorsqu’un circuit est connecté.

Pour modifier les événements de connexion :

* Ajoutez un `autostart="false"` attribut à la `<script>` balise pour le `blazor.server.js` script.
* Enregistrer les rappels pour les modifications de connexion pour les connexions abandonnées ( `onConnectionDown` ) et les connexions établies/rétablies ( `onConnectionUp` ). **Les deux** `onConnectionDown` et `onConnectionUp` doivent être spécifiés.

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

### <a name="adjust-the-reconnection-retry-count-and-interval"></a>Ajuster le nombre et l’intervalle de tentatives de reconnexion

Pour régler le nombre et l’intervalle de nouvelles tentatives de connexion :

* Ajoutez un `autostart="false"` attribut à la `<script>` balise pour le `blazor.server.js` script.
* Définissez le nombre de tentatives ( `maxRetries` ) et la période en millisecondes autorisées pour chaque tentative de nouvelle tentative ( `retryIntervalMilliseconds` ).

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

### <a name="hide-or-replace-the-reconnection-display"></a>Masquer ou remplacer l’affichage de reconnexion

Pour masquer l’affichage de reconnexion :

* Ajoutez un `autostart="false"` attribut à la `<script>` balise pour le `blazor.server.js` script.
* Définissez le gestionnaire de reconnexion `_reconnectionDisplay` sur un objet vide ( `{}` ou `new Object()` ).

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

Pour remplacer l’affichage de reconnexion, `_reconnectionDisplay` dans l’exemple précédent, définissez l’élément pour l’affichage :

```javascript
Blazor.defaultReconnectionHandler._reconnectionDisplay = 
  document.getElementById("{ELEMENT ID}");
```

L’espace réservé `{ELEMENT ID}` est l’ID de l’élément HTML à afficher.

::: moniker range=">= aspnetcore-5.0"

## <a name="influence-html-head-tag-elements"></a>Influencer les `<head>` éléments de balise HTML

*Cette section s’applique à Blazor WebAssembly et Blazor Server .*

Lors du rendu, `Title` les `Link` composants, et `Meta` ajoutent ou mettent à jour des données dans les `<head>` éléments de balise HTML :

```razor
@using Microsoft.AspNetCore.Components.Web.Extensions.Head

<Title Value="{TITLE}" />
<Link href="{URL}" rel="stylesheet" />
<Meta content="{DESCRIPTION}" name="description" />
```

Dans l’exemple précédent, les espaces réservés pour `{TITLE}` , `{URL}` et `{DESCRIPTION}` sont des valeurs de chaîne, des Razor variables ou des Razor expressions.

Les caractéristiques suivantes s’appliquent :

* Le prérendu côté serveur est pris en charge.
* Le `Value` paramètre est le seul paramètre valide pour le `Title` composant.
* Les attributs HTML fournis aux `Meta` `Link` composants et sont capturés dans des [attributs supplémentaires](xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters) et passés à la balise HTML rendue.
* Pour plusieurs `Title` composants, le titre de la page reflète le `Value` du dernier `Title` composant restitué.
* Si plusieurs `Meta` `Link` composants ou sont inclus avec des attributs identiques, une seule balise HTML est restituée pour chaque `Meta` `Link` composant ou. Deux `Meta` `Link` composants ou ne peuvent pas faire référence à la même balise HTML rendue.
* Les modifications apportées aux paramètres des `Meta` composants ou existants `Link` sont reflétées dans leurs balises HTML rendues.
* Lorsque les `Link` `Meta` composants ou ne sont plus restitués et donc supprimés par l’infrastructure, leurs balises HTML rendues sont supprimées.

Quand l’un des composants de l’infrastructure est utilisé dans un composant enfant, la balise HTML rendue influence tout autre composant enfant du composant parent, à condition que le composant enfant contenant le composant d’infrastructure soit rendu. La distinction entre l’utilisation de l’un de ces composants d’infrastructure dans un composant enfant et la mise en place d’une balise HTML dans `wwwroot/index.html` ou `Pages/_Host.cshtml` est que la balise HTML rendue d’un composant d’infrastructure :

* Peut être modifié par l’état de l’application. Une balise HTML codée en dur ne peut pas être modifiée par l’état de l’application.
* Est supprimé du code HTML `<head>` lorsque le composant parent n’est plus rendu.

::: moniker-end

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:fundamentals/logging/index>
