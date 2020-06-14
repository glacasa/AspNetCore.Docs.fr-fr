---
title: BlazorConfiguration du modèle d’hébergement ASP.net Core
author: guardrex
description: En savoir plus sur Blazor l’hébergement de la configuration de modèle, y compris l’intégration de Razor composants dans Razor les pages et les applications MVC.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/hosting-model-configuration
ms.openlocfilehash: 74501c70df5ad33d5a2478b2ec359713e29292d8
ms.sourcegitcommit: a423e8fcde4b6181a3073ed646a603ba20bfa5f9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2020
ms.locfileid: "84755779"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a>BlazorConfiguration du modèle d’hébergement ASP.net Core

Par [Daniel Roth](https://github.com/danroth27) et [Luke Latham](https://github.com/guardrex)

Cet article traite de l’hébergement de la configuration du modèle.

## <a name="blazor-webassembly"></a>BlazorWebassembly

### <a name="environment"></a>Environnement

Lors de l’exécution locale d’une application, l’environnement est par défaut développement. Lorsque l’application est publiée, l’environnement prend par défaut la valeur production.

Une Blazor application Webassembly hébergée récupère l’environnement à partir du serveur via un intergiciel qui communique l’environnement au navigateur en ajoutant l' `blazor-environment` en-tête. La valeur de l’en-tête est l’environnement. L’application hébergée Blazor et l’application serveur partagent le même environnement. Pour plus d’informations, notamment sur la configuration de l’environnement, consultez <xref:fundamentals/environments> .

Pour une application autonome exécutée localement, le serveur de développement ajoute l' `blazor-environment` en-tête pour spécifier l’environnement de développement. Pour spécifier l’environnement pour d’autres environnements d’hébergement, ajoutez l' `blazor-environment` en-tête.

Dans l’exemple suivant pour IIS, ajoutez l’en-tête personnalisé au fichier *web.config* publié. Le fichier *web.config* se trouve dans le dossier *bin/Release/{Target Framework}/Publish* :

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <system.webServer>

    ...

    <httpProtocol>
      <customHeaders>
        <add name="blazor-environment" value="Staging" />
      </customHeaders>
    </httpProtocol>
  </system.webServer>
</configuration>
```

> [!NOTE]
> Pour utiliser un fichier de *web.config* personnalisé pour IIS qui n’est pas remplacé lorsque l’application est publiée dans le dossier de *publication* , consultez <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig> .

Obtenez l’environnement de l’application dans un composant en injectant <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> et en lisant la <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> propriété :

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

Au démarrage, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> expose le <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> via la <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> propriété, qui permet aux développeurs d’avoir une logique spécifique à l’environnement dans leur code :

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

Les méthodes d’extension suivantes permettent de vérifier l’environnement actuel pour les noms d’environnement de développement, de production, intermédiaires et personnalisés :

* `IsDevelopment()`
* `IsProduction()`
* `IsStaging()`
* `IsEnvironment("{ENVIRONMENT NAME}")`

```csharp
if (builder.HostEnvironment.IsStaging())
{
    ...
};

if (builder.HostEnvironment.IsEnvironment("Custom"))
{
    ...
};
```

La <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> propriété peut être utilisée au démarrage lorsque le <xref:Microsoft.AspNetCore.Components.NavigationManager> service n’est pas disponible.

### <a name="configuration"></a>Configuration

BlazorWebassembly charge la configuration à partir de :

* Fichiers de paramètres d’application par défaut :
  * *wwwroot/appsettings.jssur*
  * *wwwroot/appSettings. {ENVIRONMENT}. JSON*
* Autres [fournisseurs de configuration](xref:fundamentals/configuration/index) inscrits par l’application. Tous les fournisseurs ne sont pas appropriés pour les Blazor applications Webassembly. La clarification des fournisseurs pris en charge pour Blazor Webassembly est suivie par [clarifier les fournisseurs de configuration pour Blazor WASM (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).

> [!WARNING]
> La configuration dans une Blazor application Webassembly est visible pour les utilisateurs. **Ne stockez pas les secrets ou les informations d’identification de l’application dans la configuration.**

Pour plus d’informations sur les fournisseurs de configuration, consultez <xref:fundamentals/configuration/index> .

#### <a name="app-settings-configuration"></a>Configuration des paramètres de l’application

*wwwroot/appsettings.jssur*:

```json
{
  "message": "Hello from config!"
}
```

Injecter une <xref:Microsoft.Extensions.Configuration.IConfiguration> instance dans un composant pour accéder aux données de configuration :

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

#### <a name="provider-configuration"></a>Configuration du fournisseur

L’exemple suivant utilise un <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> pour fournir une configuration supplémentaire :

`Program.Main`:

```csharp
using Microsoft.Extensions.Configuration.Memory;

...

var vehicleData = new Dictionary<string, string>()
{
    { "color", "blue" },
    { "type", "car" },
    { "wheels:count", "3" },
    { "wheels:brand", "Blazin" },
    { "wheels:brand:type", "rally" },
    { "wheels:year", "2008" },
};

var memoryConfig = new MemoryConfigurationSource { InitialData = vehicleData };

...

builder.Configuration.Add(memoryConfig);
```

Injecter une <xref:Microsoft.Extensions.Configuration.IConfiguration> instance dans un composant pour accéder aux données de configuration :

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<h2>Wheels</h2>

<ul>
    <li>Count: @Configuration["wheels:count"]</li>
    <li>Brand: @Configuration["wheels:brand"]</li>
    <li>Type: @Configuration["wheels:brand:type"]</li>
    <li>Year: @Configuration["wheels:year"]</li>
</ul>

@code {
    var wheelsSection = Configuration.GetSection("wheels");
    
    ...
}
```

Pour lire d’autres fichiers de configuration du dossier *wwwroot* dans la configuration, utilisez un <xref:System.Net.Http.HttpClient> pour obtenir le contenu du fichier. Lors de l’utilisation de cette approche, l' <xref:System.Net.Http.HttpClient> inscription de service existante peut utiliser le client local créé pour lire le fichier, comme le montre l’exemple suivant :

*wwwroot/cars.jssur*:

```json
{
    "size": "tiny"
}
```

`Program.Main`:

```csharp
using Microsoft.Extensions.Configuration;

...

var client = new HttpClient()
{
    BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
};

builder.Services.AddTransient(sp => client);

using var response = await client.GetAsync("cars.json");
using var stream = await response.Content.ReadAsStreamAsync();

builder.Configuration.AddJsonStream(stream);
```

#### <a name="authentication-configuration"></a>Configuration de l’authentification

*wwwroot/appsettings.jssur*:

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

`Program.Main`:

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions));
```

#### <a name="logging-configuration"></a>Configuration de la journalisation

Ajoutez une référence de package pour [Microsoft.Extensions.Logging.Configfiguration](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration/):

```xml
<PackageReference Include="Microsoft.Extensions.Logging.Configuration" Version="{VERSION}" />
```

*wwwroot/appsettings.jssur*:

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  }
}
```

`Program.Main`:

```csharp
using Microsoft.Extensions.Logging;

...

builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

#### <a name="host-builder-configuration"></a>Configuration du générateur d’ordinateurs hôtes

`Program.Main`:

```csharp
var hostname = builder.Configuration["HostName"];
```

#### <a name="cached-configuration"></a>Configuration mise en cache

Les fichiers de configuration sont mis en cache pour une utilisation hors connexion. Avec les [applications Web progressives (PWA)](xref:blazor/progressive-web-app), vous pouvez uniquement mettre à jour les fichiers de configuration lors de la création d’un déploiement. La modification des fichiers de configuration entre les déploiements n’a aucun effet, car :

* Les utilisateurs ont des versions mises en cache des fichiers qu’ils continuent d’utiliser.
* Les fichiers *service-worker.js* et *service-worker-assets.js* de PWA doivent être reconstruits lors de la compilation, qui signalent à l’application à la prochaine visite en ligne de l’utilisateur que l’application a été redéployée.

Pour plus d’informations sur la façon dont les mises à jour en arrière-plan sont gérées par PWA, consultez <xref:blazor/progressive-web-app#background-updates> .

### <a name="logging"></a>Journalisation

Pour plus d’informations sur la Blazor prise en charge de la journalisation Webassembly, consultez <xref:fundamentals/logging/index#create-logs-in-blazor> .

### <a name="signalr-cross-origin-negotiation-for-authentication"></a>SignalRnégociation Cross-Origin pour l’authentification

Pour configurer le SignalR client sous-jacent de manière à envoyer des informations d’identification, telles que des cookies ou des en-têtes d’authentification http :

* Utilisez <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> pour définir <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> les demandes d' [extraction](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) Cross-Origin :

  ```csharp
  public class IncludeRequestCredentialsMessagHandler : DelegatingHandler
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
              new IncludeRequestCredentialsMessagHandler { InnerHandler = innerHandler };
      }).Build();
  ```

Pour plus d’informations, consultez <xref:signalr/configuration#configure-additional-options>.

## <a name="blazor-server"></a>BlazorServeurs

### <a name="reflect-the-connection-state-in-the-ui"></a>Refléter l’état de la connexion dans l’interface utilisateur

Lorsque le client détecte que la connexion a été perdue, une interface utilisateur par défaut est affichée à l’utilisateur pendant que le client tente de se reconnecter. En cas d’échec de la reconnexion, l’utilisateur a la possibilité de réessayer.

Pour personnaliser l’interface utilisateur, définissez un élément avec un `id` de `components-reconnect-modal` dans le `<body>` de la page *_Host. cshtml* Razor :

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

Le tableau suivant décrit les classes CSS appliquées à l' `components-reconnect-modal` élément.

| Classe CSS                       | Détermine&hellip; |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | Connexion perdue. Le client tente de se reconnecter. Affichez le modal. |
| `components-reconnect-hide`     | Une connexion active est rétablie sur le serveur. Masquez le modal. |
| `components-reconnect-failed`   | Échec de la reconnexion, probablement en raison d’une défaillance du réseau. Pour tenter une reconnexion, appelez `window.Blazor.reconnect()` . |
| `components-reconnect-rejected` | Reconnexion refusée. Le serveur a été atteint mais a refusé la connexion, et l’état de l’utilisateur sur le serveur est perdu. Pour recharger l’application, appelez `location.reload()` . Cet état de connexion peut se produire dans les cas suivants :<ul><li>Un blocage dans le circuit côté serveur se produit.</li><li>Le client est déconnecté suffisamment longtemps pour que le serveur supprime l’état de l’utilisateur. Les instances des composants avec lesquels l’utilisateur interagit sont supprimées.</li><li>Le serveur est redémarré ou le processus de travail de l’application est recyclé.</li></ul> |

### <a name="render-mode"></a>Mode d’affichage

BlazorLes applications serveur sont configurées par défaut pour prérestituer l’interface utilisateur sur le serveur avant que la connexion cliente au serveur soit établie. Elle est configurée dans la page *_Host. cshtml* Razor :

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

| <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> | Description |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Génère le rendu du composant en HTML statique et comprend un marqueur pour une Blazor application serveur. Au démarrage de l’agent utilisateur, ce marqueur est utilisé pour démarrer une Blazor application. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Restitue un marqueur pour une Blazor application serveur. La sortie du composant n’est pas incluse. Au démarrage de l’agent utilisateur, ce marqueur est utilisé pour démarrer une Blazor application. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Génère le rendu du composant en HTML statique. |

Le rendu des composants serveur à partir d’une page HTML statique n’est pas pris en charge.

### <a name="configure-the-signalr-client-for-blazor-server-apps"></a>Configurer le SignalR client pour les Blazor applications serveur

Parfois, vous devez configurer le SignalR client utilisé par les Blazor applications serveur. Par exemple, vous pouvez configurer la journalisation sur le SignalR client pour diagnostiquer un problème de connexion.

Pour configurer le SignalR client dans le fichier *Pages/_Host. cshtml* :

* Ajoutez un `autostart="false"` attribut à la `<script>` balise pour le `blazor.server.js` script.
* Appelez `Blazor.start` et transmettez un objet de configuration qui spécifie le SignalR Générateur.

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

### <a name="logging"></a>Journalisation

Pour plus d’informations sur la Blazor prise en charge de la journalisation du serveur, consultez <xref:fundamentals/logging/index#create-logs-in-blazor> .
