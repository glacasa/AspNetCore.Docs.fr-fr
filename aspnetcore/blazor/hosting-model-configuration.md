---
title: ASP.NET configuration du Blazor modèle d’hébergement de base
author: guardrex
description: Renseignez-vous sur Blazor la configuration du modèle d’hébergement, y compris la façon d’intégrer les composants Razor dans les pages Razor et les applications MVC.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/16/2020
no-loc:
- Blazor
- SignalR
uid: blazor/hosting-model-configuration
ms.openlocfilehash: 6a3731657d11faed0b005b429058343b2be4c44b
ms.sourcegitcommit: c9d1208e86160615b2d914cce74a839ae41297a8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81791475"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a>ASP.NET configuration du modèle d’hébergement Core Blazor

Par [Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Cet article couvre la configuration du modèle d’hébergement.

## <a name="blazor-webassembly"></a>WebAssembly Blazor

### <a name="environment"></a>Environnement

Lors de l’exécution d’une application localement, l’environnement est par défaut pour le développement. Lorsque l’application est publiée, l’environnement est par défaut à la production.

Une application Hébergée Blazor WebAssembly capte l’environnement du serveur via un middleware `blazor-environment` qui communique l’environnement au navigateur en ajoutant l’en-tête. La valeur de l’en-tête est l’environnement. L’application Blazor hébergée et l’application serveur partagent le même environnement. Pour plus d’informations, y compris <xref:fundamentals/environments>la façon de configurer l’environnement, voir .

Pour une application autonome fonctionnant localement, le `blazor-environment` serveur de développement ajoute l’en-tête pour spécifier l’environnement de développement. Pour spécifier l’environnement `blazor-environment` pour d’autres environnements d’hébergement, ajoutez l’en-tête.

Dans l’exemple suivant pour IIS, ajoutez l’en-tête personnalisé au fichier *web.config* publié. Le fichier *web.config* est situé dans le *dossier bin/Release/TARGET FRAMEWORK/publish:*

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
> Pour utiliser un fichier *web.config* personnalisé pour IIS qui n’est pas écrasé <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>lorsque l’application est publiée dans le dossier *de publication,* voir .

Obtenir l’environnement de l’application `IWebAssemblyHostEnvironment` dans un `Environment` composant en injectant et en lisant la propriété :

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

Pendant le `WebAssemblyHostBuilder` démarrage, `IWebAssemblyHostEnvironment` l’expose à travers la `HostEnvironment` propriété, ce qui permet aux développeurs d’avoir une logique spécifique à l’environnement dans leur code:

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

Les méthodes d’extension de commodité suivantes permettent de vérifier l’environnement actuel pour les noms de développement, de production, de mise en scène et d’environnement personnalisé :

* `IsDevelopment()`
* `IsProduction()`
* `IsStaging()`
* 'IsEnvironment ("''ENVIRONNEMENT NAME')

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

La `IWebAssemblyHostEnvironment.BaseAddress` propriété peut être utilisée `NavigationManager` pendant le démarrage lorsque le service n’est pas disponible.

### <a name="configuration"></a>Configuration

À partir de la version ASP.NET Core 3.2 Preview 3[(la version actuelle est de 3,2 Aperçu 4](xref:blazor/get-started)), Blazor WebAssembly prend en charge la configuration de :

* *wwwroot/appsettings.json (en)*
* *wwwroot/appsettings. 'ENVIRONNEMENT'.json*

Ajoutez un fichier *appsettings.json* dans le dossier *wwwroot* :

```json
{
  "message": "Hello from config!"
}
```

Injecter <xref:Microsoft.Extensions.Configuration.IConfiguration> une instance dans un composant pour accéder aux données de configuration :

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

> [!WARNING]
> La configuration d’une application WebAssembly De Blazor est visible pour les utilisateurs. **Ne stockez pas de secrets d’applications ou d’informations d’identification en configuration.**

Les fichiers de configuration sont mis en cache pour une utilisation hors connexion. Avec [Progressive Web Applications (PWAs),](xref:blazor/progressive-web-app)vous ne pouvez mettre à jour les fichiers de configuration que lors de la création d’un nouveau déploiement. L’édition de fichiers de configuration entre les déploiements n’a aucun effet car :

* Les utilisateurs ont mis en cache les versions des fichiers qu’ils continuent d’utiliser.
* Les fichiers *service-worker.js* de la PWA et les fichiers *service-worker-assets.js* doivent être reconstruits sur compilation, qui signalent à l’application lors de la prochaine visite en ligne de l’utilisateur que l’application a été redéployée.

Pour plus d’informations sur la façon dont <xref:blazor/progressive-web-app#background-updates>les mises à jour de fond sont traitées par les APR, voir .

### <a name="logging"></a>Journalisation

Pour plus d’informations sur Blazor WebAssembly support d’enregistrement, voir <xref:fundamentals/logging/index#create-logs-in-blazor-webassembly>.

## <a name="blazor-server"></a>Serveur Blazor

### <a name="reflect-the-connection-state-in-the-ui"></a>Refléter l’état de connexion dans l’interface utilisateur

Lorsque le client détecte que la connexion a été perdue, une interface utilisateur par défaut est affichée à l’utilisateur pendant que le client tente de se reconnecter. En cas d’échec de la reconnexion, l’utilisateur a la possibilité de réessayer.

Pour personnaliser l’interface utilisateur, définissez `components-reconnect-modal` un `<body>` élément avec un `id` de dans la page *Razor _Host.cshtml* :

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

Le tableau suivant décrit les classes CSS appliquées à l’élément. `components-reconnect-modal`

| Classe CSS                       | Indique&hellip; |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | Une connexion perdue. Le client tente de se reconnecter. Montrez le modal. |
| `components-reconnect-hide`     | Une connexion active est rétablie vers le serveur. Cachez le modal. |
| `components-reconnect-failed`   | La reconnexion a échoué, probablement en raison d’une défaillance du réseau. Pour tenter de `window.Blazor.reconnect()`reconnecter, appelez . |
| `components-reconnect-rejected` | La reconnexion rejetée. Le serveur a été atteint mais a refusé la connexion, et l’état de l’utilisateur sur le serveur est perdu. Pour recharger l’application, appelez `location.reload()`. Cet état de connexion peut en résulter lorsque :<ul><li>Un plantage dans le circuit côté serveur se produit.</li><li>Le client est déconnecté assez longtemps pour que le serveur laisse tomber l’état de l’utilisateur. Les cas des composants avec lesquels l’utilisateur interagit sont éliminés.</li><li>Le serveur est redémarré ou le processus de travail de l’application est recyclé.</li></ul> |

### <a name="render-mode"></a>Mode d’affichage

Les applications Blazor Server sont configurées par défaut pour pré-réchauffer l’interface utilisateur sur le serveur avant que la connexion client au serveur ne soit établie. Ceci est mis en place dans la page *_Host.cshtml* Razor:

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

`RenderMode`configure si le composant :

* Est préditulé dans la page.
* Est rendu comme HTML statique sur la page ou si elle inclut les informations nécessaires pour bootstrap une application Blazor de l’agent utilisateur.

| `RenderMode`        | Description |
| ------------------- | ----------- |
| `ServerPrerendered` | Rend le composant en HTML statique et Blazor inclut un marqueur pour une application Server. Lorsque l’agent utilisateur démarre, ce marqueur Blazor est utilisé pour bootstrap une application. |
| `Server`            | Rend un marqueur Blazor pour une application Server. La sortie du composant n’est pas incluse. Lorsque l’agent utilisateur démarre, ce marqueur Blazor est utilisé pour bootstrap une application. |
| `Static`            | Rend le composant en HTML statique. |

Le rendu des composants du serveur à partir d’une page HTML statique n’est pas pris en charge.

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a>Rendre les composants interactifs majestueux des pages et des vues de Razor

Des composants interactifs majestueux peuvent être ajoutés à une page ou à une vue Razor.

Lorsque la page ou la vue rend :

* Le composant est préditeur avec la page ou la vue.
* L’état de composant initial utilisé pour le prerendering est perdu.
* Un nouvel état de SignalR composant est créé lorsque la connexion est établie.

La page Razor suivante `Counter` rend un composant :

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

### <a name="render-noninteractive-components-from-razor-pages-and-views"></a>Rendre les composants non inactifs des pages et des vues razor

Dans la page Razor `Counter` suivante, le composant est rendu statiquement avec une valeur initiale spécifiée à l’aide d’un formulaire :

```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialValue" />
    <button type="submit">Set initial value</button>
</form>

<component type="typeof(Counter)" render-mode="Static" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

Étant `MyComponent` donné qu’il est rendu statiquement, le composant ne peut pas être interactif.

### <a name="configure-the-opno-locsignalr-client-for-opno-locblazor-server-apps"></a>Configurer SignalR le Blazor client pour les applications Server

Parfois, vous devez SignalR configurer Blazor le client utilisé par les applications Server. Par exemple, vous pouvez configurer SignalR la connexion sur le client pour diagnostiquer un problème de connexion.

Pour configurer le SignalR client dans le fichier *Pages/_Host.cshtml* :

* Ajoutez `autostart="false"` un attribut `<script>` à `blazor.server.js` l’étiquette pour le script.
* Appelez `Blazor.start` et passez dans un objet de SignalR configuration qui spécifie le constructeur.

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
