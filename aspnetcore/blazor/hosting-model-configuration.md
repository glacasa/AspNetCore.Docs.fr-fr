---
title: Configuration Blazor du modèle d’hébergement ASP.net Core
author: guardrex
description: En savoir Blazor plus sur l’hébergement de la configuration de modèle, y compris l’intégration des composants Razor dans les applications Razor pages et MVC.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/25/2020
no-loc:
- Blazor
- SignalR
uid: blazor/hosting-model-configuration
ms.openlocfilehash: c7e8d1f2dcba6432072a5cc11a6c5d78e50c2398
ms.sourcegitcommit: c6f5ea6397af2dd202632cf2be66fc30f3357bcc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/26/2020
ms.locfileid: "82159617"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="f233d-103">Configuration du modèle d’hébergement ASP.NET Core éblouissant</span><span class="sxs-lookup"><span data-stu-id="f233d-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="f233d-104">Par [Daniel Roth](https://github.com/danroth27) et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="f233d-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="f233d-105">Cet article traite de l’hébergement de la configuration du modèle.</span><span class="sxs-lookup"><span data-stu-id="f233d-105">This article covers hosting model configuration.</span></span>

## <a name="blazor-webassembly"></a><span data-ttu-id="f233d-106">WebAssembly Blazor</span><span class="sxs-lookup"><span data-stu-id="f233d-106">Blazor WebAssembly</span></span>

### <a name="environment"></a><span data-ttu-id="f233d-107">Environnement</span><span class="sxs-lookup"><span data-stu-id="f233d-107">Environment</span></span>

<span data-ttu-id="f233d-108">Lors de l’exécution locale d’une application, l’environnement est par défaut développement.</span><span class="sxs-lookup"><span data-stu-id="f233d-108">When running an app locally, the environment defaults to Development.</span></span> <span data-ttu-id="f233d-109">Lorsque l’application est publiée, l’environnement prend par défaut la valeur production.</span><span class="sxs-lookup"><span data-stu-id="f233d-109">When the app is published, the environment defaults to Production.</span></span>

<span data-ttu-id="f233d-110">Une application webassembly éblouissante hébergée récupère l’environnement à partir du serveur via un intergiciel (middleware) qui communique l’environnement au navigateur en `blazor-environment` ajoutant l’en-tête.</span><span class="sxs-lookup"><span data-stu-id="f233d-110">A hosted Blazor WebAssembly app picks up the environment from the server via a middleware that communicates the environment to the browser by adding the `blazor-environment` header.</span></span> <span data-ttu-id="f233d-111">La valeur de l’en-tête est l’environnement.</span><span class="sxs-lookup"><span data-stu-id="f233d-111">The value of the header is the environment.</span></span> <span data-ttu-id="f233d-112">L’application éblouissante et l’application serveur hébergées partagent le même environnement.</span><span class="sxs-lookup"><span data-stu-id="f233d-112">The hosted Blazor app and the server app share the same environment.</span></span> <span data-ttu-id="f233d-113">Pour plus d’informations, notamment sur la configuration de l’environnement <xref:fundamentals/environments>, consultez.</span><span class="sxs-lookup"><span data-stu-id="f233d-113">For more information, including how to configure the environment, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="f233d-114">Pour une application autonome exécutée localement, le serveur de développement `blazor-environment` ajoute l’en-tête pour spécifier l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="f233d-114">For a standalone app running locally, the development server adds the `blazor-environment` header to specify the Development environment.</span></span> <span data-ttu-id="f233d-115">Pour spécifier l’environnement pour d’autres environnements d’hébergement, `blazor-environment` ajoutez l’en-tête.</span><span class="sxs-lookup"><span data-stu-id="f233d-115">To specify the environment for other hosting environments, add the `blazor-environment` header.</span></span>

<span data-ttu-id="f233d-116">Dans l’exemple suivant pour IIS, ajoutez l’en-tête personnalisé au fichier *Web. config* publié.</span><span class="sxs-lookup"><span data-stu-id="f233d-116">In the following example for IIS, add the custom header to the published *web.config* file.</span></span> <span data-ttu-id="f233d-117">Le fichier *Web. config* se trouve dans le dossier *bin/Release/{Target Framework}/Publish* :</span><span class="sxs-lookup"><span data-stu-id="f233d-117">The *web.config* file is located in the *bin/Release/{TARGET FRAMEWORK}/publish* folder:</span></span>

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
> <span data-ttu-id="f233d-118">Pour utiliser un fichier *Web. config* personnalisé pour IIS qui n’est pas remplacé lorsque l’application est publiée dans *publish* le dossier de publication <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>, consultez.</span><span class="sxs-lookup"><span data-stu-id="f233d-118">To use a custom *web.config* file for IIS that isn't overwritten when the app is published to the *publish* folder, see <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>.</span></span>

<span data-ttu-id="f233d-119">Obtenez l’environnement de l’application dans un composant en injectant `IWebAssemblyHostEnvironment` et en `Environment` lisant la propriété :</span><span class="sxs-lookup"><span data-stu-id="f233d-119">Obtain the app's environment in a component by injecting `IWebAssemblyHostEnvironment` and reading the `Environment` property:</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

<span data-ttu-id="f233d-120">Au démarrage, `WebAssemblyHostBuilder` expose le `IWebAssemblyHostEnvironment` via la `HostEnvironment` propriété, qui permet aux développeurs d’avoir une logique spécifique à l’environnement dans leur code :</span><span class="sxs-lookup"><span data-stu-id="f233d-120">During startup, the `WebAssemblyHostBuilder` exposes the `IWebAssemblyHostEnvironment` through the `HostEnvironment` property, which enables developers to have environment-specific logic in their code:</span></span>

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

<span data-ttu-id="f233d-121">Les méthodes d’extension suivantes permettent de vérifier l’environnement actuel pour les noms d’environnement de développement, de production, intermédiaires et personnalisés :</span><span class="sxs-lookup"><span data-stu-id="f233d-121">The following convenience extension methods permit checking the current environment for Development, Production, Staging, and custom environment names:</span></span>

* `IsDevelopment()`
* `IsProduction()`
* `IsStaging()`
* <span data-ttu-id="f233d-122">'IsEnvironment ("{nom d’environnement}")</span><span class="sxs-lookup"><span data-stu-id="f233d-122">\`IsEnvironment("{ENVIRONMENT NAME}")</span></span>

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

<span data-ttu-id="f233d-123">La `IWebAssemblyHostEnvironment.BaseAddress` propriété peut être utilisée au démarrage lorsque le `NavigationManager` service n’est pas disponible.</span><span class="sxs-lookup"><span data-stu-id="f233d-123">The `IWebAssemblyHostEnvironment.BaseAddress` property can be used during startup when the `NavigationManager` service isn't available.</span></span>

### <a name="configuration"></a><span data-ttu-id="f233d-124">Configuration</span><span class="sxs-lookup"><span data-stu-id="f233d-124">Configuration</span></span>

<span data-ttu-id="f233d-125">L’assembly éblouissant prend en charge la configuration à partir de :</span><span class="sxs-lookup"><span data-stu-id="f233d-125">Blazor WebAssembly supports configuration from:</span></span>

* <span data-ttu-id="f233d-126">Le [fournisseur de configuration de fichiers](xref:fundamentals/configuration/index#file-configuration-provider) pour les fichiers de paramètres d’application par défaut :</span><span class="sxs-lookup"><span data-stu-id="f233d-126">The [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider) for app settings files by default:</span></span>
  * <span data-ttu-id="f233d-127">*wwwroot/appSettings. JSON*</span><span class="sxs-lookup"><span data-stu-id="f233d-127">*wwwroot/appsettings.json*</span></span>
  * <span data-ttu-id="f233d-128">*wwwroot/appSettings. {ENVIRONMENT}. JSON*</span><span class="sxs-lookup"><span data-stu-id="f233d-128">*wwwroot/appsettings.{ENVIRONMENT}.json*</span></span>
* <span data-ttu-id="f233d-129">Autres [fournisseurs de configuration](xref:fundamentals/configuration/index) inscrits par l’application.</span><span class="sxs-lookup"><span data-stu-id="f233d-129">Other [configuration providers](xref:fundamentals/configuration/index) registered by the app.</span></span>

> [!WARNING]
> <span data-ttu-id="f233d-130">La configuration dans une application de webassembly éblouissante est visible pour les utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="f233d-130">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="f233d-131">**Ne stockez pas les secrets ou les informations d’identification de l’application dans la configuration.**</span><span class="sxs-lookup"><span data-stu-id="f233d-131">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="f233d-132">Pour plus d’informations sur les fournisseurs de <xref:fundamentals/configuration/index>configuration, consultez.</span><span class="sxs-lookup"><span data-stu-id="f233d-132">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

#### <a name="app-settings-configuration"></a><span data-ttu-id="f233d-133">Configuration des paramètres de l’application</span><span class="sxs-lookup"><span data-stu-id="f233d-133">App settings configuration</span></span>

<span data-ttu-id="f233d-134">*wwwroot/appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="f233d-134">*wwwroot/appsettings.json*:</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="f233d-135">Injecter <xref:Microsoft.Extensions.Configuration.IConfiguration> une instance dans un composant pour accéder aux données de configuration :</span><span class="sxs-lookup"><span data-stu-id="f233d-135">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

#### <a name="provider-configuration"></a><span data-ttu-id="f233d-136">Configuration du fournisseur</span><span class="sxs-lookup"><span data-stu-id="f233d-136">Provider configuration</span></span>

<span data-ttu-id="f233d-137">L’exemple suivant utilise un <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> et le [fournisseur de configuration de fichier](xref:fundamentals/configuration/index#file-configuration-provider) pour fournir une configuration supplémentaire :</span><span class="sxs-lookup"><span data-stu-id="f233d-137">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> and the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider) to supply additional configuration:</span></span>

<span data-ttu-id="f233d-138">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="f233d-138">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Configuration;

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

builder.Configuration
    .Add(memoryConfig)
    .AddJsonFile("cars.json", optional: false, reloadOnChange: true);
```

<span data-ttu-id="f233d-139">Injecter <xref:Microsoft.Extensions.Configuration.IConfiguration> une instance dans un composant pour accéder aux données de configuration :</span><span class="sxs-lookup"><span data-stu-id="f233d-139">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<h2>Wheels</h2>

<ul>
    <li>Count: @Configuration["wheels:count"]</p>
    <li>Brand: @Configuration["wheels:brand"]</p>
    <li>Type: @Configuration["wheels:brand:type"]</p>
    <li>Year: @Configuration["wheels:year"]</p>
</ul>

@code {
    var wheelsSection = Configuration.GetSection("wheels");
    
    ...
}
```

#### <a name="authentication-configuration"></a><span data-ttu-id="f233d-140">Configuration de l’authentification</span><span class="sxs-lookup"><span data-stu-id="f233d-140">Authentication configuration</span></span>

<span data-ttu-id="f233d-141">*wwwroot/appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="f233d-141">*wwwroot/appsettings.json*:</span></span>

```json
{
  "AzureAD": {
    "Authority": "https://login.microsoftonline.com/",
    "ClientId": "aeaebf0f-d416-4d92-a08f-e1d5b51fc494"
  }
}
```

<span data-ttu-id="f233d-142">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="f233d-142">`Program.Main`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("AzureAD", options);
```

#### <a name="logging-configuration"></a><span data-ttu-id="f233d-143">Configuration de la journalisation</span><span class="sxs-lookup"><span data-stu-id="f233d-143">Logging configuration</span></span>

<span data-ttu-id="f233d-144">*wwwroot/appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="f233d-144">*wwwroot/appsettings.json*:</span></span>

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

<span data-ttu-id="f233d-145">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="f233d-145">`Program.Main`:</span></span>

```csharp
builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

#### <a name="host-builder-configuration"></a><span data-ttu-id="f233d-146">Configuration du générateur d’ordinateurs hôtes</span><span class="sxs-lookup"><span data-stu-id="f233d-146">Host builder configuration</span></span>

<span data-ttu-id="f233d-147">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="f233d-147">`Program.Main`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

#### <a name="cached-configuration"></a><span data-ttu-id="f233d-148">Configuration mise en cache</span><span class="sxs-lookup"><span data-stu-id="f233d-148">Cached configuration</span></span>

<span data-ttu-id="f233d-149">Les fichiers de configuration sont mis en cache pour une utilisation hors connexion.</span><span class="sxs-lookup"><span data-stu-id="f233d-149">Configuration files are cached for offline use.</span></span> <span data-ttu-id="f233d-150">Avec les [applications Web progressives (PWA)](xref:blazor/progressive-web-app), vous pouvez uniquement mettre à jour les fichiers de configuration lors de la création d’un déploiement.</span><span class="sxs-lookup"><span data-stu-id="f233d-150">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="f233d-151">La modification des fichiers de configuration entre les déploiements n’a aucun effet, car :</span><span class="sxs-lookup"><span data-stu-id="f233d-151">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="f233d-152">Les utilisateurs ont des versions mises en cache des fichiers qu’ils continuent d’utiliser.</span><span class="sxs-lookup"><span data-stu-id="f233d-152">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="f233d-153">Les fichiers *Service-Worker. js* et *Service-Worker-Assets. js* de PWA doivent être reconstruits lors de la compilation, qui signalent à l’application à la prochaine visite en ligne de l’utilisateur que l’application a été redéployée.</span><span class="sxs-lookup"><span data-stu-id="f233d-153">The PWA's *service-worker.js* and *service-worker-assets.js* files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="f233d-154">Pour plus d’informations sur la façon dont les mises à jour en arrière- <xref:blazor/progressive-web-app#background-updates>plan sont gérées par PWA, consultez.</span><span class="sxs-lookup"><span data-stu-id="f233d-154">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>

### <a name="logging"></a><span data-ttu-id="f233d-155">Journalisation</span><span class="sxs-lookup"><span data-stu-id="f233d-155">Logging</span></span>

<span data-ttu-id="f233d-156">Pour plus d’informations sur la prise en charge de la <xref:fundamentals/logging/index#create-logs-in-blazor>journalisation de webassembly éblouissant, consultez.</span><span class="sxs-lookup"><span data-stu-id="f233d-156">For information on Blazor WebAssembly logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>

## <a name="blazor-server"></a><span data-ttu-id="f233d-157">Serveur Blazor</span><span class="sxs-lookup"><span data-stu-id="f233d-157">Blazor Server</span></span>

### <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="f233d-158">Refléter l’état de la connexion dans l’interface utilisateur</span><span class="sxs-lookup"><span data-stu-id="f233d-158">Reflect the connection state in the UI</span></span>

<span data-ttu-id="f233d-159">Lorsque le client détecte que la connexion a été perdue, une interface utilisateur par défaut est affichée à l’utilisateur pendant que le client tente de se reconnecter.</span><span class="sxs-lookup"><span data-stu-id="f233d-159">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="f233d-160">En cas d’échec de la reconnexion, l’utilisateur a la possibilité de réessayer.</span><span class="sxs-lookup"><span data-stu-id="f233d-160">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="f233d-161">Pour personnaliser l’interface utilisateur, définissez un élément avec `id` un `components-reconnect-modal` de dans `<body>` le de la page Razor *_Host. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="f233d-161">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="f233d-162">Le tableau suivant décrit les classes CSS appliquées à l' `components-reconnect-modal` élément.</span><span class="sxs-lookup"><span data-stu-id="f233d-162">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="f233d-163">Classe CSS</span><span class="sxs-lookup"><span data-stu-id="f233d-163">CSS class</span></span>                       | <span data-ttu-id="f233d-164">Détermine&hellip;</span><span class="sxs-lookup"><span data-stu-id="f233d-164">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="f233d-165">Connexion perdue.</span><span class="sxs-lookup"><span data-stu-id="f233d-165">A lost connection.</span></span> <span data-ttu-id="f233d-166">Le client tente de se reconnecter.</span><span class="sxs-lookup"><span data-stu-id="f233d-166">The client is attempting to reconnect.</span></span> <span data-ttu-id="f233d-167">Affichez le modal.</span><span class="sxs-lookup"><span data-stu-id="f233d-167">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="f233d-168">Une connexion active est rétablie sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="f233d-168">An active connection is re-established to the server.</span></span> <span data-ttu-id="f233d-169">Masquez le modal.</span><span class="sxs-lookup"><span data-stu-id="f233d-169">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="f233d-170">Échec de la reconnexion, probablement en raison d’une défaillance du réseau.</span><span class="sxs-lookup"><span data-stu-id="f233d-170">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="f233d-171">Pour tenter une reconnexion, `window.Blazor.reconnect()`appelez.</span><span class="sxs-lookup"><span data-stu-id="f233d-171">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="f233d-172">Reconnexion refusée.</span><span class="sxs-lookup"><span data-stu-id="f233d-172">Reconnection rejected.</span></span> <span data-ttu-id="f233d-173">Le serveur a été atteint mais a refusé la connexion, et l’état de l’utilisateur sur le serveur est perdu.</span><span class="sxs-lookup"><span data-stu-id="f233d-173">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="f233d-174">Pour recharger l’application, appelez `location.reload()`.</span><span class="sxs-lookup"><span data-stu-id="f233d-174">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="f233d-175">Cet état de connexion peut se produire dans les cas suivants :</span><span class="sxs-lookup"><span data-stu-id="f233d-175">This connection state may result when:</span></span><ul><li><span data-ttu-id="f233d-176">Un blocage dans le circuit côté serveur se produit.</span><span class="sxs-lookup"><span data-stu-id="f233d-176">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="f233d-177">Le client est déconnecté suffisamment longtemps pour que le serveur supprime l’état de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="f233d-177">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="f233d-178">Les instances des composants avec lesquels l’utilisateur interagit sont supprimées.</span><span class="sxs-lookup"><span data-stu-id="f233d-178">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="f233d-179">Le serveur est redémarré ou le processus de travail de l’application est recyclé.</span><span class="sxs-lookup"><span data-stu-id="f233d-179">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

### <a name="render-mode"></a><span data-ttu-id="f233d-180">Mode d’affichage</span><span class="sxs-lookup"><span data-stu-id="f233d-180">Render mode</span></span>

<span data-ttu-id="f233d-181">Les applications serveur éblouissantes sont configurées par défaut pour prérestituer l’interface utilisateur sur le serveur avant que la connexion cliente au serveur soit établie.</span><span class="sxs-lookup"><span data-stu-id="f233d-181">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="f233d-182">Elle est configurée dans la page Razor *_Host. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="f233d-182">This is set up in the *_Host.cshtml* Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="f233d-183">`RenderMode`Configure si le composant :</span><span class="sxs-lookup"><span data-stu-id="f233d-183">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="f233d-184">Est prérendu dans la page.</span><span class="sxs-lookup"><span data-stu-id="f233d-184">Is prerendered into the page.</span></span>
* <span data-ttu-id="f233d-185">Est rendu en HTML statique sur la page ou s’il contient les informations nécessaires pour démarrer une application éblouissant à partir de l’agent utilisateur.</span><span class="sxs-lookup"><span data-stu-id="f233d-185">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| `RenderMode`        | <span data-ttu-id="f233d-186">Description</span><span class="sxs-lookup"><span data-stu-id="f233d-186">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="f233d-187">Génère le rendu du composant en HTML statique et comprend un marqueur Blazor pour une application serveur.</span><span class="sxs-lookup"><span data-stu-id="f233d-187">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="f233d-188">Au démarrage de l’agent utilisateur, ce marqueur est utilisé pour démarrer Blazor une application.</span><span class="sxs-lookup"><span data-stu-id="f233d-188">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Server`            | <span data-ttu-id="f233d-189">Restitue un marqueur pour Blazor une application serveur.</span><span class="sxs-lookup"><span data-stu-id="f233d-189">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="f233d-190">La sortie du composant n’est pas incluse.</span><span class="sxs-lookup"><span data-stu-id="f233d-190">Output from the component isn't included.</span></span> <span data-ttu-id="f233d-191">Au démarrage de l’agent utilisateur, ce marqueur est utilisé pour démarrer Blazor une application.</span><span class="sxs-lookup"><span data-stu-id="f233d-191">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Static`            | <span data-ttu-id="f233d-192">Génère le rendu du composant en HTML statique.</span><span class="sxs-lookup"><span data-stu-id="f233d-192">Renders the component into static HTML.</span></span> |

<span data-ttu-id="f233d-193">Le rendu des composants serveur à partir d’une page HTML statique n’est pas pris en charge.</span><span class="sxs-lookup"><span data-stu-id="f233d-193">Rendering server components from a static HTML page isn't supported.</span></span>

### <a name="configure-the-opno-locsignalr-client-for-opno-locblazor-server-apps"></a><span data-ttu-id="f233d-194">Configurer le SignalR client pour Blazor les applications serveur</span><span class="sxs-lookup"><span data-stu-id="f233d-194">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="f233d-195">Parfois, vous devez configurer le client SignalR utilisé par Blazor les applications serveur.</span><span class="sxs-lookup"><span data-stu-id="f233d-195">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="f233d-196">Par exemple, vous pouvez configurer la journalisation sur le SignalR client pour diagnostiquer un problème de connexion.</span><span class="sxs-lookup"><span data-stu-id="f233d-196">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="f233d-197">Pour configurer le SignalR client dans le fichier *pages/_Host. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="f233d-197">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="f233d-198">Ajoutez un `autostart="false"` attribut à la `<script>` balise pour `blazor.server.js` le script.</span><span class="sxs-lookup"><span data-stu-id="f233d-198">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="f233d-199">Appelez `Blazor.start` et transmettez un objet de configuration qui spécifie le SignalR générateur.</span><span class="sxs-lookup"><span data-stu-id="f233d-199">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

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

### <a name="logging"></a><span data-ttu-id="f233d-200">Journalisation</span><span class="sxs-lookup"><span data-stu-id="f233d-200">Logging</span></span>

<span data-ttu-id="f233d-201">Pour plus d' Blazor informations sur la prise en <xref:fundamentals/logging/index#create-logs-in-blazor>charge de la journalisation du serveur, consultez.</span><span class="sxs-lookup"><span data-stu-id="f233d-201">For information on Blazor Server logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>
