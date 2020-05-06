---
title: Configuration Blazor du modèle d’hébergement ASP.net Core
author: guardrex
description: En savoir Blazor plus sur l’hébergement de la configuration de Razor modèle, Razor y compris l’intégration de composants dans les pages et les applications MVC.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/hosting-model-configuration
ms.openlocfilehash: 17ed43a12643f067da73658bec72400acbe1be43
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82772072"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a>Configuration du modèle d’hébergement ASP.NET Core éblouissant

Par [Daniel Roth](https://github.com/danroth27) et [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Cet article traite de l’hébergement de la configuration du modèle.

## <a name="blazor-webassembly"></a>WebAssembly Blazor

### <a name="environment"></a>Environnement

Lors de l’exécution locale d’une application, l’environnement est par défaut développement. Lorsque l’application est publiée, l’environnement prend par défaut la valeur production.

Une application webassembly éblouissante hébergée récupère l’environnement à partir du serveur via un intergiciel (middleware) qui communique l’environnement au navigateur en `blazor-environment` ajoutant l’en-tête. La valeur de l’en-tête est l’environnement. L’application éblouissante et l’application serveur hébergées partagent le même environnement. Pour plus d’informations, notamment sur la configuration de l’environnement <xref:fundamentals/environments>, consultez.

Pour une application autonome exécutée localement, le serveur de développement `blazor-environment` ajoute l’en-tête pour spécifier l’environnement de développement. Pour spécifier l’environnement pour d’autres environnements d’hébergement, `blazor-environment` ajoutez l’en-tête.

Dans l’exemple suivant pour IIS, ajoutez l’en-tête personnalisé au fichier *Web. config* publié. Le fichier *Web. config* se trouve dans le dossier *bin/Release/{Target Framework}/Publish* :

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
> Pour utiliser un fichier *Web. config* personnalisé pour IIS qui n’est pas remplacé lorsque l’application est publiée dans *publish* le dossier de publication <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>, consultez.

Obtenez l’environnement de l’application dans un composant en injectant `IWebAssemblyHostEnvironment` et en `Environment` lisant la propriété :

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

Au démarrage, `WebAssemblyHostBuilder` expose le `IWebAssemblyHostEnvironment` via la `HostEnvironment` propriété, qui permet aux développeurs d’avoir une logique spécifique à l’environnement dans leur code :

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
* 'IsEnvironment ("{nom d’environnement}")

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

La `IWebAssemblyHostEnvironment.BaseAddress` propriété peut être utilisée au démarrage lorsque le `NavigationManager` service n’est pas disponible.

### <a name="configuration"></a>Configuration

Webassembly éblouissant charge la configuration à partir de :

* Fichiers de paramètres d’application par défaut :
  * *wwwroot/appSettings. JSON*
  * *wwwroot/appSettings. {ENVIRONMENT}. JSON*
* Autres [fournisseurs de configuration](xref:fundamentals/configuration/index) inscrits par l’application. Tous les fournisseurs ne sont pas appropriés pour les applications de webassembly éblouissantes. La clarification des fournisseurs pris en charge pour le webassembly éblouissant est suivie par [clarifier les fournisseurs de configuration pour éblouissant WASM (dotnet/AspNetCore. Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).

> [!WARNING]
> La configuration dans une application de webassembly éblouissante est visible pour les utilisateurs. **Ne stockez pas les secrets ou les informations d’identification de l’application dans la configuration.**

Pour plus d’informations sur les fournisseurs de <xref:fundamentals/configuration/index>configuration, consultez.

#### <a name="app-settings-configuration"></a>Configuration des paramètres de l’application

*wwwroot/appSettings. JSON*:

```json
{
  "message": "Hello from config!"
}
```

Injecter <xref:Microsoft.Extensions.Configuration.IConfiguration> une instance dans un composant pour accéder aux données de configuration :

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

Injecter <xref:Microsoft.Extensions.Configuration.IConfiguration> une instance dans un composant pour accéder aux données de configuration :

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

Pour lire d’autres fichiers de configuration du dossier *wwwroot* dans la configuration, `HttpClient` utilisez un pour obtenir le contenu du fichier. Lors de l’utilisation de cette approche `HttpClient` , l’inscription de service existante peut utiliser le client local créé pour lire le fichier, comme le montre l’exemple suivant :

*wwwroot/cars. JSON*:

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

*wwwroot/appSettings. JSON*:

```json
{
  "AzureAD": {
    "Authority": "https://login.microsoftonline.com/",
    "ClientId": "aeaebf0f-d416-4d92-a08f-e1d5b51fc494"
  }
}
```

`Program.Main`:

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("AzureAD", options);
```

#### <a name="logging-configuration"></a>Configuration de la journalisation

*wwwroot/appSettings. JSON*:

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
* Les fichiers *Service-Worker. js* et *Service-Worker-Assets. js* de PWA doivent être reconstruits lors de la compilation, qui signalent à l’application à la prochaine visite en ligne de l’utilisateur que l’application a été redéployée.

Pour plus d’informations sur la façon dont les mises à jour en arrière- <xref:blazor/progressive-web-app#background-updates>plan sont gérées par PWA, consultez.

### <a name="logging"></a>Journalisation

Pour plus d’informations sur la prise en charge de la <xref:fundamentals/logging/index#create-logs-in-blazor>journalisation de webassembly éblouissant, consultez.

## <a name="blazor-server"></a>Serveur Blazor

### <a name="reflect-the-connection-state-in-the-ui"></a>Refléter l’état de la connexion dans l’interface utilisateur

Lorsque le client détecte que la connexion a été perdue, une interface utilisateur par défaut est affichée à l’utilisateur pendant que le client tente de se reconnecter. En cas d’échec de la reconnexion, l’utilisateur a la possibilité de réessayer.

Pour personnaliser l’interface utilisateur, définissez un élément avec `id` un `components-reconnect-modal` de dans `<body>` le de la page Razor *_Host. cshtml* :

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
| `components-reconnect-failed`   | Échec de la reconnexion, probablement en raison d’une défaillance du réseau. Pour tenter une reconnexion, `window.Blazor.reconnect()`appelez. |
| `components-reconnect-rejected` | Reconnexion refusée. Le serveur a été atteint mais a refusé la connexion, et l’état de l’utilisateur sur le serveur est perdu. Pour recharger l’application, appelez `location.reload()`. Cet état de connexion peut se produire dans les cas suivants :<ul><li>Un blocage dans le circuit côté serveur se produit.</li><li>Le client est déconnecté suffisamment longtemps pour que le serveur supprime l’état de l’utilisateur. Les instances des composants avec lesquels l’utilisateur interagit sont supprimées.</li><li>Le serveur est redémarré ou le processus de travail de l’application est recyclé.</li></ul> |

### <a name="render-mode"></a>Mode d’affichage

Les applications serveur éblouissantes sont configurées par défaut pour prérestituer l’interface utilisateur sur le serveur avant que la connexion cliente au serveur soit établie. Elle est configurée dans la page Razor *_Host. cshtml* :

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

`RenderMode`Configure si le composant :

* Est prérendu dans la page.
* Est rendu en HTML statique sur la page ou s’il contient les informations nécessaires pour démarrer une application éblouissant à partir de l’agent utilisateur.

| `RenderMode`        | Description |
| ------------------- | ----------- |
| `ServerPrerendered` | Génère le rendu du composant en HTML statique et comprend un marqueur Blazor pour une application serveur. Au démarrage de l’agent utilisateur, ce marqueur est utilisé pour démarrer Blazor une application. |
| `Server`            | Restitue un marqueur pour Blazor une application serveur. La sortie du composant n’est pas incluse. Au démarrage de l’agent utilisateur, ce marqueur est utilisé pour démarrer Blazor une application. |
| `Static`            | Génère le rendu du composant en HTML statique. |

Le rendu des composants serveur à partir d’une page HTML statique n’est pas pris en charge.

### <a name="configure-the-signalr-client-for-blazor-server-apps"></a>Configurer le SignalR client pour Blazor les applications serveur

Parfois, vous devez configurer le client SignalR utilisé par Blazor les applications serveur. Par exemple, vous pouvez configurer la journalisation sur le SignalR client pour diagnostiquer un problème de connexion.

Pour configurer le SignalR client dans le fichier *pages/_Host. cshtml* :

* Ajoutez un `autostart="false"` attribut à la `<script>` balise pour `blazor.server.js` le script.
* Appelez `Blazor.start` et transmettez un objet de configuration qui spécifie le SignalR générateur.

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

Pour plus d' Blazor informations sur la prise en <xref:fundamentals/logging/index#create-logs-in-blazor>charge de la journalisation du serveur, consultez.
