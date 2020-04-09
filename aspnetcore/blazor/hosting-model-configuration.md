---
title: ASP.NET configuration du Blazor modèle d’hébergement de base
author: guardrex
description: Renseignez-vous sur Blazor la configuration du modèle d’hébergement, y compris la façon d’intégrer les composants Razor dans les pages Razor et les applications MVC.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/24/2020
no-loc:
- Blazor
- SignalR
uid: blazor/hosting-model-configuration
ms.openlocfilehash: 1f71ac63bbe9dc9d56cfca2ded19a5b863be828f
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80306434"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a>ASP.NET configuration du modèle d’hébergement Core Blazor

Par [Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Cet article couvre la configuration du modèle d’hébergement.

## <a name="blazor-webassembly"></a>WebAssembly Blazor

À la ASP.NET version Core 3.2 Preview 3, Blazor WebAssembly prend en charge la configuration de :

* *wwwroot/appsettings.json (en)*
* *wwwroot/appsettings. 'ENVIRONNEMENT'.json*

Dans une application Blazor Hosted, [l’environnement de temps d’exécution](xref:fundamentals/environments) est le même que la valeur de l’application serveur.

Lors de l’exécution de l’application localement, l’environnement par défaut à Développement. Lorsque l’application est publiée, l’environnement est par défaut à la production. Pour plus d’informations, y compris <xref:fundamentals/environments>la façon de configurer l’environnement, voir .

> [!WARNING]
> La configuration d’une application WebAssembly De Blazor est visible pour les utilisateurs. **Ne stockez pas de secrets d’applications ou d’informations d’identification en configuration.**

Les fichiers de configuration sont mis en cache pour une utilisation hors connexion. Avec [Progressive Web Applications (PWAs),](xref:blazor/progressive-web-app)vous ne pouvez mettre à jour les fichiers de configuration que lors de la création d’un nouveau déploiement. L’édition de fichiers de configuration entre les déploiements n’a aucun effet car :

* Les utilisateurs ont mis en cache les versions des fichiers qu’ils continuent d’utiliser.
* Les fichiers *service-worker.js* de la PWA et les fichiers *service-worker-assets.js* doivent être reconstruits sur compilation, qui signalent à l’application lors de la prochaine visite en ligne de l’utilisateur que l’application a été redéployée.

Pour plus d’informations sur la façon dont <xref:blazor/progressive-web-app#background-updates>les mises à jour de fond sont traitées par les APR, voir .

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
