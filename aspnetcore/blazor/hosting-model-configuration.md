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
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="4ab8c-103">ASP.NET configuration du modèle d’hébergement Core Blazor</span><span class="sxs-lookup"><span data-stu-id="4ab8c-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="4ab8c-104">Par [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="4ab8c-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="4ab8c-105">Cet article couvre la configuration du modèle d’hébergement.</span><span class="sxs-lookup"><span data-stu-id="4ab8c-105">This article covers hosting model configuration.</span></span>

## <a name="blazor-webassembly"></a><span data-ttu-id="4ab8c-106">WebAssembly Blazor</span><span class="sxs-lookup"><span data-stu-id="4ab8c-106">Blazor WebAssembly</span></span>

<span data-ttu-id="4ab8c-107">À la ASP.NET version Core 3.2 Preview 3, Blazor WebAssembly prend en charge la configuration de :</span><span class="sxs-lookup"><span data-stu-id="4ab8c-107">As of the ASP.NET Core 3.2 Preview 3 release, Blazor WebAssembly supports configuration from:</span></span>

* <span data-ttu-id="4ab8c-108">*wwwroot/appsettings.json (en)*</span><span class="sxs-lookup"><span data-stu-id="4ab8c-108">*wwwroot/appsettings.json*</span></span>
* <span data-ttu-id="4ab8c-109">*wwwroot/appsettings. 'ENVIRONNEMENT'.json*</span><span class="sxs-lookup"><span data-stu-id="4ab8c-109">*wwwroot/appsettings.{ENVIRONMENT}.json*</span></span>

<span data-ttu-id="4ab8c-110">Dans une application Blazor Hosted, [l’environnement de temps d’exécution](xref:fundamentals/environments) est le même que la valeur de l’application serveur.</span><span class="sxs-lookup"><span data-stu-id="4ab8c-110">In a Blazor Hosted app, the [runtime environment](xref:fundamentals/environments) is the same as the server app's value.</span></span>

<span data-ttu-id="4ab8c-111">Lors de l’exécution de l’application localement, l’environnement par défaut à Développement.</span><span class="sxs-lookup"><span data-stu-id="4ab8c-111">When running the app locally, the environment defaults to Development.</span></span> <span data-ttu-id="4ab8c-112">Lorsque l’application est publiée, l’environnement est par défaut à la production.</span><span class="sxs-lookup"><span data-stu-id="4ab8c-112">When the app is published, the environment defaults to Production.</span></span> <span data-ttu-id="4ab8c-113">Pour plus d’informations, y compris <xref:fundamentals/environments>la façon de configurer l’environnement, voir .</span><span class="sxs-lookup"><span data-stu-id="4ab8c-113">For more information, including how to configure the environment, see <xref:fundamentals/environments>.</span></span>

> [!WARNING]
> <span data-ttu-id="4ab8c-114">La configuration d’une application WebAssembly De Blazor est visible pour les utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="4ab8c-114">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="4ab8c-115">**Ne stockez pas de secrets d’applications ou d’informations d’identification en configuration.**</span><span class="sxs-lookup"><span data-stu-id="4ab8c-115">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="4ab8c-116">Les fichiers de configuration sont mis en cache pour une utilisation hors connexion.</span><span class="sxs-lookup"><span data-stu-id="4ab8c-116">Configuration files are cached for offline use.</span></span> <span data-ttu-id="4ab8c-117">Avec [Progressive Web Applications (PWAs),](xref:blazor/progressive-web-app)vous ne pouvez mettre à jour les fichiers de configuration que lors de la création d’un nouveau déploiement.</span><span class="sxs-lookup"><span data-stu-id="4ab8c-117">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="4ab8c-118">L’édition de fichiers de configuration entre les déploiements n’a aucun effet car :</span><span class="sxs-lookup"><span data-stu-id="4ab8c-118">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="4ab8c-119">Les utilisateurs ont mis en cache les versions des fichiers qu’ils continuent d’utiliser.</span><span class="sxs-lookup"><span data-stu-id="4ab8c-119">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="4ab8c-120">Les fichiers *service-worker.js* de la PWA et les fichiers *service-worker-assets.js* doivent être reconstruits sur compilation, qui signalent à l’application lors de la prochaine visite en ligne de l’utilisateur que l’application a été redéployée.</span><span class="sxs-lookup"><span data-stu-id="4ab8c-120">The PWA's *service-worker.js* and *service-worker-assets.js* files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="4ab8c-121">Pour plus d’informations sur la façon dont <xref:blazor/progressive-web-app#background-updates>les mises à jour de fond sont traitées par les APR, voir .</span><span class="sxs-lookup"><span data-stu-id="4ab8c-121">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>

## <a name="blazor-server"></a><span data-ttu-id="4ab8c-122">Serveur Blazor</span><span class="sxs-lookup"><span data-stu-id="4ab8c-122">Blazor Server</span></span>

### <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="4ab8c-123">Refléter l’état de connexion dans l’interface utilisateur</span><span class="sxs-lookup"><span data-stu-id="4ab8c-123">Reflect the connection state in the UI</span></span>

<span data-ttu-id="4ab8c-124">Lorsque le client détecte que la connexion a été perdue, une interface utilisateur par défaut est affichée à l’utilisateur pendant que le client tente de se reconnecter.</span><span class="sxs-lookup"><span data-stu-id="4ab8c-124">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="4ab8c-125">En cas d’échec de la reconnexion, l’utilisateur a la possibilité de réessayer.</span><span class="sxs-lookup"><span data-stu-id="4ab8c-125">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="4ab8c-126">Pour personnaliser l’interface utilisateur, définissez `components-reconnect-modal` un `<body>` élément avec un `id` de dans la page *Razor _Host.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="4ab8c-126">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="4ab8c-127">Le tableau suivant décrit les classes CSS appliquées à l’élément. `components-reconnect-modal`</span><span class="sxs-lookup"><span data-stu-id="4ab8c-127">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="4ab8c-128">Classe CSS</span><span class="sxs-lookup"><span data-stu-id="4ab8c-128">CSS class</span></span>                       | <span data-ttu-id="4ab8c-129">Indique&hellip;</span><span class="sxs-lookup"><span data-stu-id="4ab8c-129">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="4ab8c-130">Une connexion perdue.</span><span class="sxs-lookup"><span data-stu-id="4ab8c-130">A lost connection.</span></span> <span data-ttu-id="4ab8c-131">Le client tente de se reconnecter.</span><span class="sxs-lookup"><span data-stu-id="4ab8c-131">The client is attempting to reconnect.</span></span> <span data-ttu-id="4ab8c-132">Montrez le modal.</span><span class="sxs-lookup"><span data-stu-id="4ab8c-132">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="4ab8c-133">Une connexion active est rétablie vers le serveur.</span><span class="sxs-lookup"><span data-stu-id="4ab8c-133">An active connection is re-established to the server.</span></span> <span data-ttu-id="4ab8c-134">Cachez le modal.</span><span class="sxs-lookup"><span data-stu-id="4ab8c-134">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="4ab8c-135">La reconnexion a échoué, probablement en raison d’une défaillance du réseau.</span><span class="sxs-lookup"><span data-stu-id="4ab8c-135">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="4ab8c-136">Pour tenter de `window.Blazor.reconnect()`reconnecter, appelez .</span><span class="sxs-lookup"><span data-stu-id="4ab8c-136">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="4ab8c-137">La reconnexion rejetée.</span><span class="sxs-lookup"><span data-stu-id="4ab8c-137">Reconnection rejected.</span></span> <span data-ttu-id="4ab8c-138">Le serveur a été atteint mais a refusé la connexion, et l’état de l’utilisateur sur le serveur est perdu.</span><span class="sxs-lookup"><span data-stu-id="4ab8c-138">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="4ab8c-139">Pour recharger l’application, appelez `location.reload()`.</span><span class="sxs-lookup"><span data-stu-id="4ab8c-139">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="4ab8c-140">Cet état de connexion peut en résulter lorsque :</span><span class="sxs-lookup"><span data-stu-id="4ab8c-140">This connection state may result when:</span></span><ul><li><span data-ttu-id="4ab8c-141">Un plantage dans le circuit côté serveur se produit.</span><span class="sxs-lookup"><span data-stu-id="4ab8c-141">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="4ab8c-142">Le client est déconnecté assez longtemps pour que le serveur laisse tomber l’état de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="4ab8c-142">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="4ab8c-143">Les cas des composants avec lesquels l’utilisateur interagit sont éliminés.</span><span class="sxs-lookup"><span data-stu-id="4ab8c-143">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="4ab8c-144">Le serveur est redémarré ou le processus de travail de l’application est recyclé.</span><span class="sxs-lookup"><span data-stu-id="4ab8c-144">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

### <a name="render-mode"></a><span data-ttu-id="4ab8c-145">Mode d’affichage</span><span class="sxs-lookup"><span data-stu-id="4ab8c-145">Render mode</span></span>

<span data-ttu-id="4ab8c-146">Les applications Blazor Server sont configurées par défaut pour pré-réchauffer l’interface utilisateur sur le serveur avant que la connexion client au serveur ne soit établie.</span><span class="sxs-lookup"><span data-stu-id="4ab8c-146">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="4ab8c-147">Ceci est mis en place dans la page *_Host.cshtml* Razor:</span><span class="sxs-lookup"><span data-stu-id="4ab8c-147">This is set up in the *_Host.cshtml* Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="4ab8c-148">`RenderMode`configure si le composant :</span><span class="sxs-lookup"><span data-stu-id="4ab8c-148">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="4ab8c-149">Est préditulé dans la page.</span><span class="sxs-lookup"><span data-stu-id="4ab8c-149">Is prerendered into the page.</span></span>
* <span data-ttu-id="4ab8c-150">Est rendu comme HTML statique sur la page ou si elle inclut les informations nécessaires pour bootstrap une application Blazor de l’agent utilisateur.</span><span class="sxs-lookup"><span data-stu-id="4ab8c-150">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| `RenderMode`        | <span data-ttu-id="4ab8c-151">Description</span><span class="sxs-lookup"><span data-stu-id="4ab8c-151">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="4ab8c-152">Rend le composant en HTML statique et Blazor inclut un marqueur pour une application Server.</span><span class="sxs-lookup"><span data-stu-id="4ab8c-152">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="4ab8c-153">Lorsque l’agent utilisateur démarre, ce marqueur Blazor est utilisé pour bootstrap une application.</span><span class="sxs-lookup"><span data-stu-id="4ab8c-153">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Server`            | <span data-ttu-id="4ab8c-154">Rend un marqueur Blazor pour une application Server.</span><span class="sxs-lookup"><span data-stu-id="4ab8c-154">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="4ab8c-155">La sortie du composant n’est pas incluse.</span><span class="sxs-lookup"><span data-stu-id="4ab8c-155">Output from the component isn't included.</span></span> <span data-ttu-id="4ab8c-156">Lorsque l’agent utilisateur démarre, ce marqueur Blazor est utilisé pour bootstrap une application.</span><span class="sxs-lookup"><span data-stu-id="4ab8c-156">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Static`            | <span data-ttu-id="4ab8c-157">Rend le composant en HTML statique.</span><span class="sxs-lookup"><span data-stu-id="4ab8c-157">Renders the component into static HTML.</span></span> |

<span data-ttu-id="4ab8c-158">Le rendu des composants du serveur à partir d’une page HTML statique n’est pas pris en charge.</span><span class="sxs-lookup"><span data-stu-id="4ab8c-158">Rendering server components from a static HTML page isn't supported.</span></span>

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a><span data-ttu-id="4ab8c-159">Rendre les composants interactifs majestueux des pages et des vues de Razor</span><span class="sxs-lookup"><span data-stu-id="4ab8c-159">Render stateful interactive components from Razor pages and views</span></span>

<span data-ttu-id="4ab8c-160">Des composants interactifs majestueux peuvent être ajoutés à une page ou à une vue Razor.</span><span class="sxs-lookup"><span data-stu-id="4ab8c-160">Stateful interactive components can be added to a Razor page or view.</span></span>

<span data-ttu-id="4ab8c-161">Lorsque la page ou la vue rend :</span><span class="sxs-lookup"><span data-stu-id="4ab8c-161">When the page or view renders:</span></span>

* <span data-ttu-id="4ab8c-162">Le composant est préditeur avec la page ou la vue.</span><span class="sxs-lookup"><span data-stu-id="4ab8c-162">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="4ab8c-163">L’état de composant initial utilisé pour le prerendering est perdu.</span><span class="sxs-lookup"><span data-stu-id="4ab8c-163">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="4ab8c-164">Un nouvel état de SignalR composant est créé lorsque la connexion est établie.</span><span class="sxs-lookup"><span data-stu-id="4ab8c-164">New component state is created when the SignalR connection is established.</span></span>

<span data-ttu-id="4ab8c-165">La page Razor suivante `Counter` rend un composant :</span><span class="sxs-lookup"><span data-stu-id="4ab8c-165">The following Razor page renders a `Counter` component:</span></span>

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

### <a name="render-noninteractive-components-from-razor-pages-and-views"></a><span data-ttu-id="4ab8c-166">Rendre les composants non inactifs des pages et des vues razor</span><span class="sxs-lookup"><span data-stu-id="4ab8c-166">Render noninteractive components from Razor pages and views</span></span>

<span data-ttu-id="4ab8c-167">Dans la page Razor `Counter` suivante, le composant est rendu statiquement avec une valeur initiale spécifiée à l’aide d’un formulaire :</span><span class="sxs-lookup"><span data-stu-id="4ab8c-167">In the following Razor page, the `Counter` component is statically rendered with an initial value that's specified using a form:</span></span>

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

<span data-ttu-id="4ab8c-168">Étant `MyComponent` donné qu’il est rendu statiquement, le composant ne peut pas être interactif.</span><span class="sxs-lookup"><span data-stu-id="4ab8c-168">Since `MyComponent` is statically rendered, the component can't be interactive.</span></span>

### <a name="configure-the-opno-locsignalr-client-for-opno-locblazor-server-apps"></a><span data-ttu-id="4ab8c-169">Configurer SignalR le Blazor client pour les applications Server</span><span class="sxs-lookup"><span data-stu-id="4ab8c-169">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="4ab8c-170">Parfois, vous devez SignalR configurer Blazor le client utilisé par les applications Server.</span><span class="sxs-lookup"><span data-stu-id="4ab8c-170">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="4ab8c-171">Par exemple, vous pouvez configurer SignalR la connexion sur le client pour diagnostiquer un problème de connexion.</span><span class="sxs-lookup"><span data-stu-id="4ab8c-171">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="4ab8c-172">Pour configurer le SignalR client dans le fichier *Pages/_Host.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="4ab8c-172">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="4ab8c-173">Ajoutez `autostart="false"` un attribut `<script>` à `blazor.server.js` l’étiquette pour le script.</span><span class="sxs-lookup"><span data-stu-id="4ab8c-173">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="4ab8c-174">Appelez `Blazor.start` et passez dans un objet de SignalR configuration qui spécifie le constructeur.</span><span class="sxs-lookup"><span data-stu-id="4ab8c-174">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

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
