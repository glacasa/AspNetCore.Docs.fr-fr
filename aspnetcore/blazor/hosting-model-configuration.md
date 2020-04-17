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
ms.openlocfilehash: 1b0f5f4071be7134d7de08615ec016ca6567385d
ms.sourcegitcommit: 49c91ad4b69f4f8032394cbf2d5ae1b19a7f863b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81544841"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="a5464-103">ASP.NET configuration du modèle d’hébergement Core Blazor</span><span class="sxs-lookup"><span data-stu-id="a5464-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="a5464-104">Par [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="a5464-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="a5464-105">Cet article couvre la configuration du modèle d’hébergement.</span><span class="sxs-lookup"><span data-stu-id="a5464-105">This article covers hosting model configuration.</span></span>

## <a name="blazor-webassembly"></a><span data-ttu-id="a5464-106">WebAssembly Blazor</span><span class="sxs-lookup"><span data-stu-id="a5464-106">Blazor WebAssembly</span></span>

### <a name="environment"></a><span data-ttu-id="a5464-107">Environnement</span><span class="sxs-lookup"><span data-stu-id="a5464-107">Environment</span></span>

<span data-ttu-id="a5464-108">Lors de l’exécution d’une application localement, l’environnement est par défaut pour le développement.</span><span class="sxs-lookup"><span data-stu-id="a5464-108">When running an app locally, the environment defaults to Development.</span></span> <span data-ttu-id="a5464-109">Lorsque l’application est publiée, l’environnement est par défaut à la production.</span><span class="sxs-lookup"><span data-stu-id="a5464-109">When the app is published, the environment defaults to Production.</span></span>

<span data-ttu-id="a5464-110">Une application Hébergée Blazor WebAssembly capte l’environnement du serveur via un middleware `blazor-environment` qui communique l’environnement au navigateur en ajoutant l’en-tête.</span><span class="sxs-lookup"><span data-stu-id="a5464-110">A hosted Blazor WebAssembly app picks up the environment from the server via a middleware that communicates the environment to the browser by adding the `blazor-environment` header.</span></span> <span data-ttu-id="a5464-111">La valeur de l’en-tête est l’environnement.</span><span class="sxs-lookup"><span data-stu-id="a5464-111">The value of the header is the environment.</span></span> <span data-ttu-id="a5464-112">L’application Blazor hébergée et l’application serveur partagent le même environnement.</span><span class="sxs-lookup"><span data-stu-id="a5464-112">The hosted Blazor app and the server app share the same environment.</span></span> <span data-ttu-id="a5464-113">Pour plus d’informations, y compris <xref:fundamentals/environments>la façon de configurer l’environnement, voir .</span><span class="sxs-lookup"><span data-stu-id="a5464-113">For more information, including how to configure the environment, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="a5464-114">Pour une application autonome fonctionnant localement, le `blazor-environment` serveur de développement ajoute l’en-tête pour spécifier l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="a5464-114">For a standalone app running locally, the development server adds the `blazor-environment` header to specify the Development environment.</span></span> <span data-ttu-id="a5464-115">Pour spécifier l’environnement `blazor-environment` pour d’autres environnements d’hébergement, ajoutez l’en-tête.</span><span class="sxs-lookup"><span data-stu-id="a5464-115">To specify the environment for other hosting environments, add the `blazor-environment` header.</span></span>

<span data-ttu-id="a5464-116">Dans l’exemple suivant pour IIS, ajoutez l’en-tête personnalisé au fichier *web.config* publié.</span><span class="sxs-lookup"><span data-stu-id="a5464-116">In the following example for IIS, add the custom header to the published *web.config* file.</span></span> <span data-ttu-id="a5464-117">Le fichier *web.config* est situé dans le *dossier bin/Release/TARGET FRAMEWORK/publish:*</span><span class="sxs-lookup"><span data-stu-id="a5464-117">The *web.config* file is located in the *bin/Release/{TARGET FRAMEWORK}/publish* folder:</span></span>

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
> <span data-ttu-id="a5464-118">Pour utiliser un fichier *web.config* personnalisé pour IIS qui n’est pas écrasé <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>lorsque l’application est publiée dans le dossier *de publication,* voir .</span><span class="sxs-lookup"><span data-stu-id="a5464-118">To use a custom *web.config* file for IIS that isn't overwritten when the app is published to the *publish* folder, see <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>.</span></span>

<span data-ttu-id="a5464-119">Obtenir l’environnement de l’application `IWebAssemblyHostEnvironment` dans un `Environment` composant en injectant et en lisant la propriété :</span><span class="sxs-lookup"><span data-stu-id="a5464-119">Obtain the app's environment in a component by injecting `IWebAssemblyHostEnvironment` and reading the `Environment` property:</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

<span data-ttu-id="a5464-120">Pendant le `WebAssemblyHostBuilder` démarrage, `IWebAssemblyHostEnvironment` l’expose à travers la `HostEnvironment` propriété, ce qui permet aux développeurs d’avoir une logique spécifique à l’environnement dans leur code:</span><span class="sxs-lookup"><span data-stu-id="a5464-120">During startup, the `WebAssemblyHostBuilder` exposes the `IWebAssemblyHostEnvironment` through the `HostEnvironment` property, which enables developers to have environment-specific logic in their code:</span></span>

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

<span data-ttu-id="a5464-121">Les méthodes d’extension de commodité suivantes permettent de vérifier l’environnement actuel pour les noms de développement, de production, de mise en scène et d’environnement personnalisé :</span><span class="sxs-lookup"><span data-stu-id="a5464-121">The following convenience extension methods permit checking the current environment for Development, Production, Staging, and custom environment names:</span></span>

* `IsDevelopment()`
* `IsProduction()`
* `IsStaging()`
* <span data-ttu-id="a5464-122">'IsEnvironment ("''ENVIRONNEMENT NAME')</span><span class="sxs-lookup"><span data-stu-id="a5464-122">\`IsEnvironment("{ENVIRONMENT NAME}")</span></span>

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

<span data-ttu-id="a5464-123">La `IWebAssemblyHostEnvironment.BaseAddress` propriété peut être utilisée `NavigationManager` pendant le démarrage lorsque le service n’est pas disponible.</span><span class="sxs-lookup"><span data-stu-id="a5464-123">The `IWebAssemblyHostEnvironment.BaseAddress` property can be used during startup when the `NavigationManager` service isn't available.</span></span>

### <a name="configuration"></a><span data-ttu-id="a5464-124">Configuration</span><span class="sxs-lookup"><span data-stu-id="a5464-124">Configuration</span></span>

<span data-ttu-id="a5464-125">À partir de la version ASP.NET Core 3.2 Preview 3[(la version actuelle est de 3,2 Aperçu 4](xref:blazor/get-started)), Blazor WebAssembly prend en charge la configuration de :</span><span class="sxs-lookup"><span data-stu-id="a5464-125">As of the ASP.NET Core 3.2 Preview 3 release ([current release is 3.2 Preview 4](xref:blazor/get-started)), Blazor WebAssembly supports configuration from:</span></span>

* <span data-ttu-id="a5464-126">*wwwroot/appsettings.json (en)*</span><span class="sxs-lookup"><span data-stu-id="a5464-126">*wwwroot/appsettings.json*</span></span>
* <span data-ttu-id="a5464-127">*wwwroot/appsettings. 'ENVIRONNEMENT'.json*</span><span class="sxs-lookup"><span data-stu-id="a5464-127">*wwwroot/appsettings.{ENVIRONMENT}.json*</span></span>

<span data-ttu-id="a5464-128">Ajoutez un fichier *appsettings.json* dans le dossier *wwwroot* :</span><span class="sxs-lookup"><span data-stu-id="a5464-128">Add an *appsettings.json* file in the *wwwroot* folder:</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="a5464-129">Injecter <xref:Microsoft.Extensions.Configuration.IConfiguration> une instance dans un composant pour accéder aux données de configuration :</span><span class="sxs-lookup"><span data-stu-id="a5464-129">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

> [!WARNING]
> <span data-ttu-id="a5464-130">La configuration d’une application WebAssembly De Blazor est visible pour les utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="a5464-130">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="a5464-131">**Ne stockez pas de secrets d’applications ou d’informations d’identification en configuration.**</span><span class="sxs-lookup"><span data-stu-id="a5464-131">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="a5464-132">Les fichiers de configuration sont mis en cache pour une utilisation hors connexion.</span><span class="sxs-lookup"><span data-stu-id="a5464-132">Configuration files are cached for offline use.</span></span> <span data-ttu-id="a5464-133">Avec [Progressive Web Applications (PWAs),](xref:blazor/progressive-web-app)vous ne pouvez mettre à jour les fichiers de configuration que lors de la création d’un nouveau déploiement.</span><span class="sxs-lookup"><span data-stu-id="a5464-133">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="a5464-134">L’édition de fichiers de configuration entre les déploiements n’a aucun effet car :</span><span class="sxs-lookup"><span data-stu-id="a5464-134">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="a5464-135">Les utilisateurs ont mis en cache les versions des fichiers qu’ils continuent d’utiliser.</span><span class="sxs-lookup"><span data-stu-id="a5464-135">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="a5464-136">Les fichiers *service-worker.js* de la PWA et les fichiers *service-worker-assets.js* doivent être reconstruits sur compilation, qui signalent à l’application lors de la prochaine visite en ligne de l’utilisateur que l’application a été redéployée.</span><span class="sxs-lookup"><span data-stu-id="a5464-136">The PWA's *service-worker.js* and *service-worker-assets.js* files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="a5464-137">Pour plus d’informations sur la façon dont <xref:blazor/progressive-web-app#background-updates>les mises à jour de fond sont traitées par les APR, voir .</span><span class="sxs-lookup"><span data-stu-id="a5464-137">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>

## <a name="blazor-server"></a><span data-ttu-id="a5464-138">Serveur Blazor</span><span class="sxs-lookup"><span data-stu-id="a5464-138">Blazor Server</span></span>

### <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="a5464-139">Refléter l’état de connexion dans l’interface utilisateur</span><span class="sxs-lookup"><span data-stu-id="a5464-139">Reflect the connection state in the UI</span></span>

<span data-ttu-id="a5464-140">Lorsque le client détecte que la connexion a été perdue, une interface utilisateur par défaut est affichée à l’utilisateur pendant que le client tente de se reconnecter.</span><span class="sxs-lookup"><span data-stu-id="a5464-140">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="a5464-141">En cas d’échec de la reconnexion, l’utilisateur a la possibilité de réessayer.</span><span class="sxs-lookup"><span data-stu-id="a5464-141">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="a5464-142">Pour personnaliser l’interface utilisateur, définissez `components-reconnect-modal` un `<body>` élément avec un `id` de dans la page *Razor _Host.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="a5464-142">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="a5464-143">Le tableau suivant décrit les classes CSS appliquées à l’élément. `components-reconnect-modal`</span><span class="sxs-lookup"><span data-stu-id="a5464-143">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="a5464-144">Classe CSS</span><span class="sxs-lookup"><span data-stu-id="a5464-144">CSS class</span></span>                       | <span data-ttu-id="a5464-145">Indique&hellip;</span><span class="sxs-lookup"><span data-stu-id="a5464-145">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="a5464-146">Une connexion perdue.</span><span class="sxs-lookup"><span data-stu-id="a5464-146">A lost connection.</span></span> <span data-ttu-id="a5464-147">Le client tente de se reconnecter.</span><span class="sxs-lookup"><span data-stu-id="a5464-147">The client is attempting to reconnect.</span></span> <span data-ttu-id="a5464-148">Montrez le modal.</span><span class="sxs-lookup"><span data-stu-id="a5464-148">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="a5464-149">Une connexion active est rétablie vers le serveur.</span><span class="sxs-lookup"><span data-stu-id="a5464-149">An active connection is re-established to the server.</span></span> <span data-ttu-id="a5464-150">Cachez le modal.</span><span class="sxs-lookup"><span data-stu-id="a5464-150">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="a5464-151">La reconnexion a échoué, probablement en raison d’une défaillance du réseau.</span><span class="sxs-lookup"><span data-stu-id="a5464-151">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="a5464-152">Pour tenter de `window.Blazor.reconnect()`reconnecter, appelez .</span><span class="sxs-lookup"><span data-stu-id="a5464-152">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="a5464-153">La reconnexion rejetée.</span><span class="sxs-lookup"><span data-stu-id="a5464-153">Reconnection rejected.</span></span> <span data-ttu-id="a5464-154">Le serveur a été atteint mais a refusé la connexion, et l’état de l’utilisateur sur le serveur est perdu.</span><span class="sxs-lookup"><span data-stu-id="a5464-154">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="a5464-155">Pour recharger l’application, appelez `location.reload()`.</span><span class="sxs-lookup"><span data-stu-id="a5464-155">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="a5464-156">Cet état de connexion peut en résulter lorsque :</span><span class="sxs-lookup"><span data-stu-id="a5464-156">This connection state may result when:</span></span><ul><li><span data-ttu-id="a5464-157">Un plantage dans le circuit côté serveur se produit.</span><span class="sxs-lookup"><span data-stu-id="a5464-157">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="a5464-158">Le client est déconnecté assez longtemps pour que le serveur laisse tomber l’état de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="a5464-158">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="a5464-159">Les cas des composants avec lesquels l’utilisateur interagit sont éliminés.</span><span class="sxs-lookup"><span data-stu-id="a5464-159">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="a5464-160">Le serveur est redémarré ou le processus de travail de l’application est recyclé.</span><span class="sxs-lookup"><span data-stu-id="a5464-160">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

### <a name="render-mode"></a><span data-ttu-id="a5464-161">Mode d’affichage</span><span class="sxs-lookup"><span data-stu-id="a5464-161">Render mode</span></span>

<span data-ttu-id="a5464-162">Les applications Blazor Server sont configurées par défaut pour pré-réchauffer l’interface utilisateur sur le serveur avant que la connexion client au serveur ne soit établie.</span><span class="sxs-lookup"><span data-stu-id="a5464-162">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="a5464-163">Ceci est mis en place dans la page *_Host.cshtml* Razor:</span><span class="sxs-lookup"><span data-stu-id="a5464-163">This is set up in the *_Host.cshtml* Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="a5464-164">`RenderMode`configure si le composant :</span><span class="sxs-lookup"><span data-stu-id="a5464-164">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="a5464-165">Est préditulé dans la page.</span><span class="sxs-lookup"><span data-stu-id="a5464-165">Is prerendered into the page.</span></span>
* <span data-ttu-id="a5464-166">Est rendu comme HTML statique sur la page ou si elle inclut les informations nécessaires pour bootstrap une application Blazor de l’agent utilisateur.</span><span class="sxs-lookup"><span data-stu-id="a5464-166">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| `RenderMode`        | <span data-ttu-id="a5464-167">Description</span><span class="sxs-lookup"><span data-stu-id="a5464-167">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="a5464-168">Rend le composant en HTML statique et Blazor inclut un marqueur pour une application Server.</span><span class="sxs-lookup"><span data-stu-id="a5464-168">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="a5464-169">Lorsque l’agent utilisateur démarre, ce marqueur Blazor est utilisé pour bootstrap une application.</span><span class="sxs-lookup"><span data-stu-id="a5464-169">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Server`            | <span data-ttu-id="a5464-170">Rend un marqueur Blazor pour une application Server.</span><span class="sxs-lookup"><span data-stu-id="a5464-170">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="a5464-171">La sortie du composant n’est pas incluse.</span><span class="sxs-lookup"><span data-stu-id="a5464-171">Output from the component isn't included.</span></span> <span data-ttu-id="a5464-172">Lorsque l’agent utilisateur démarre, ce marqueur Blazor est utilisé pour bootstrap une application.</span><span class="sxs-lookup"><span data-stu-id="a5464-172">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Static`            | <span data-ttu-id="a5464-173">Rend le composant en HTML statique.</span><span class="sxs-lookup"><span data-stu-id="a5464-173">Renders the component into static HTML.</span></span> |

<span data-ttu-id="a5464-174">Le rendu des composants du serveur à partir d’une page HTML statique n’est pas pris en charge.</span><span class="sxs-lookup"><span data-stu-id="a5464-174">Rendering server components from a static HTML page isn't supported.</span></span>

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a><span data-ttu-id="a5464-175">Rendre les composants interactifs majestueux des pages et des vues de Razor</span><span class="sxs-lookup"><span data-stu-id="a5464-175">Render stateful interactive components from Razor pages and views</span></span>

<span data-ttu-id="a5464-176">Des composants interactifs majestueux peuvent être ajoutés à une page ou à une vue Razor.</span><span class="sxs-lookup"><span data-stu-id="a5464-176">Stateful interactive components can be added to a Razor page or view.</span></span>

<span data-ttu-id="a5464-177">Lorsque la page ou la vue rend :</span><span class="sxs-lookup"><span data-stu-id="a5464-177">When the page or view renders:</span></span>

* <span data-ttu-id="a5464-178">Le composant est préditeur avec la page ou la vue.</span><span class="sxs-lookup"><span data-stu-id="a5464-178">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="a5464-179">L’état de composant initial utilisé pour le prerendering est perdu.</span><span class="sxs-lookup"><span data-stu-id="a5464-179">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="a5464-180">Un nouvel état de SignalR composant est créé lorsque la connexion est établie.</span><span class="sxs-lookup"><span data-stu-id="a5464-180">New component state is created when the SignalR connection is established.</span></span>

<span data-ttu-id="a5464-181">La page Razor suivante `Counter` rend un composant :</span><span class="sxs-lookup"><span data-stu-id="a5464-181">The following Razor page renders a `Counter` component:</span></span>

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

### <a name="render-noninteractive-components-from-razor-pages-and-views"></a><span data-ttu-id="a5464-182">Rendre les composants non inactifs des pages et des vues razor</span><span class="sxs-lookup"><span data-stu-id="a5464-182">Render noninteractive components from Razor pages and views</span></span>

<span data-ttu-id="a5464-183">Dans la page Razor `Counter` suivante, le composant est rendu statiquement avec une valeur initiale spécifiée à l’aide d’un formulaire :</span><span class="sxs-lookup"><span data-stu-id="a5464-183">In the following Razor page, the `Counter` component is statically rendered with an initial value that's specified using a form:</span></span>

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

<span data-ttu-id="a5464-184">Étant `MyComponent` donné qu’il est rendu statiquement, le composant ne peut pas être interactif.</span><span class="sxs-lookup"><span data-stu-id="a5464-184">Since `MyComponent` is statically rendered, the component can't be interactive.</span></span>

### <a name="configure-the-opno-locsignalr-client-for-opno-locblazor-server-apps"></a><span data-ttu-id="a5464-185">Configurer SignalR le Blazor client pour les applications Server</span><span class="sxs-lookup"><span data-stu-id="a5464-185">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="a5464-186">Parfois, vous devez SignalR configurer Blazor le client utilisé par les applications Server.</span><span class="sxs-lookup"><span data-stu-id="a5464-186">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="a5464-187">Par exemple, vous pouvez configurer SignalR la connexion sur le client pour diagnostiquer un problème de connexion.</span><span class="sxs-lookup"><span data-stu-id="a5464-187">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="a5464-188">Pour configurer le SignalR client dans le fichier *Pages/_Host.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="a5464-188">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="a5464-189">Ajoutez `autostart="false"` un attribut `<script>` à `blazor.server.js` l’étiquette pour le script.</span><span class="sxs-lookup"><span data-stu-id="a5464-189">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="a5464-190">Appelez `Blazor.start` et passez dans un objet de SignalR configuration qui spécifie le constructeur.</span><span class="sxs-lookup"><span data-stu-id="a5464-190">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

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
