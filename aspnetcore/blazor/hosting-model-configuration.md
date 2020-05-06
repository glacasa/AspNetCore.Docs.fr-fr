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
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="60088-103">Configuration du modèle d’hébergement ASP.NET Core éblouissant</span><span class="sxs-lookup"><span data-stu-id="60088-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="60088-104">Par [Daniel Roth](https://github.com/danroth27) et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="60088-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="60088-105">Cet article traite de l’hébergement de la configuration du modèle.</span><span class="sxs-lookup"><span data-stu-id="60088-105">This article covers hosting model configuration.</span></span>

## <a name="blazor-webassembly"></a><span data-ttu-id="60088-106">WebAssembly Blazor</span><span class="sxs-lookup"><span data-stu-id="60088-106">Blazor WebAssembly</span></span>

### <a name="environment"></a><span data-ttu-id="60088-107">Environnement</span><span class="sxs-lookup"><span data-stu-id="60088-107">Environment</span></span>

<span data-ttu-id="60088-108">Lors de l’exécution locale d’une application, l’environnement est par défaut développement.</span><span class="sxs-lookup"><span data-stu-id="60088-108">When running an app locally, the environment defaults to Development.</span></span> <span data-ttu-id="60088-109">Lorsque l’application est publiée, l’environnement prend par défaut la valeur production.</span><span class="sxs-lookup"><span data-stu-id="60088-109">When the app is published, the environment defaults to Production.</span></span>

<span data-ttu-id="60088-110">Une application webassembly éblouissante hébergée récupère l’environnement à partir du serveur via un intergiciel (middleware) qui communique l’environnement au navigateur en `blazor-environment` ajoutant l’en-tête.</span><span class="sxs-lookup"><span data-stu-id="60088-110">A hosted Blazor WebAssembly app picks up the environment from the server via a middleware that communicates the environment to the browser by adding the `blazor-environment` header.</span></span> <span data-ttu-id="60088-111">La valeur de l’en-tête est l’environnement.</span><span class="sxs-lookup"><span data-stu-id="60088-111">The value of the header is the environment.</span></span> <span data-ttu-id="60088-112">L’application éblouissante et l’application serveur hébergées partagent le même environnement.</span><span class="sxs-lookup"><span data-stu-id="60088-112">The hosted Blazor app and the server app share the same environment.</span></span> <span data-ttu-id="60088-113">Pour plus d’informations, notamment sur la configuration de l’environnement <xref:fundamentals/environments>, consultez.</span><span class="sxs-lookup"><span data-stu-id="60088-113">For more information, including how to configure the environment, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="60088-114">Pour une application autonome exécutée localement, le serveur de développement `blazor-environment` ajoute l’en-tête pour spécifier l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="60088-114">For a standalone app running locally, the development server adds the `blazor-environment` header to specify the Development environment.</span></span> <span data-ttu-id="60088-115">Pour spécifier l’environnement pour d’autres environnements d’hébergement, `blazor-environment` ajoutez l’en-tête.</span><span class="sxs-lookup"><span data-stu-id="60088-115">To specify the environment for other hosting environments, add the `blazor-environment` header.</span></span>

<span data-ttu-id="60088-116">Dans l’exemple suivant pour IIS, ajoutez l’en-tête personnalisé au fichier *Web. config* publié.</span><span class="sxs-lookup"><span data-stu-id="60088-116">In the following example for IIS, add the custom header to the published *web.config* file.</span></span> <span data-ttu-id="60088-117">Le fichier *Web. config* se trouve dans le dossier *bin/Release/{Target Framework}/Publish* :</span><span class="sxs-lookup"><span data-stu-id="60088-117">The *web.config* file is located in the *bin/Release/{TARGET FRAMEWORK}/publish* folder:</span></span>

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
> <span data-ttu-id="60088-118">Pour utiliser un fichier *Web. config* personnalisé pour IIS qui n’est pas remplacé lorsque l’application est publiée dans *publish* le dossier de publication <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>, consultez.</span><span class="sxs-lookup"><span data-stu-id="60088-118">To use a custom *web.config* file for IIS that isn't overwritten when the app is published to the *publish* folder, see <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>.</span></span>

<span data-ttu-id="60088-119">Obtenez l’environnement de l’application dans un composant en injectant `IWebAssemblyHostEnvironment` et en `Environment` lisant la propriété :</span><span class="sxs-lookup"><span data-stu-id="60088-119">Obtain the app's environment in a component by injecting `IWebAssemblyHostEnvironment` and reading the `Environment` property:</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

<span data-ttu-id="60088-120">Au démarrage, `WebAssemblyHostBuilder` expose le `IWebAssemblyHostEnvironment` via la `HostEnvironment` propriété, qui permet aux développeurs d’avoir une logique spécifique à l’environnement dans leur code :</span><span class="sxs-lookup"><span data-stu-id="60088-120">During startup, the `WebAssemblyHostBuilder` exposes the `IWebAssemblyHostEnvironment` through the `HostEnvironment` property, which enables developers to have environment-specific logic in their code:</span></span>

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

<span data-ttu-id="60088-121">Les méthodes d’extension suivantes permettent de vérifier l’environnement actuel pour les noms d’environnement de développement, de production, intermédiaires et personnalisés :</span><span class="sxs-lookup"><span data-stu-id="60088-121">The following convenience extension methods permit checking the current environment for Development, Production, Staging, and custom environment names:</span></span>

* `IsDevelopment()`
* `IsProduction()`
* `IsStaging()`
* <span data-ttu-id="60088-122">'IsEnvironment ("{nom d’environnement}")</span><span class="sxs-lookup"><span data-stu-id="60088-122">\`IsEnvironment("{ENVIRONMENT NAME}")</span></span>

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

<span data-ttu-id="60088-123">La `IWebAssemblyHostEnvironment.BaseAddress` propriété peut être utilisée au démarrage lorsque le `NavigationManager` service n’est pas disponible.</span><span class="sxs-lookup"><span data-stu-id="60088-123">The `IWebAssemblyHostEnvironment.BaseAddress` property can be used during startup when the `NavigationManager` service isn't available.</span></span>

### <a name="configuration"></a><span data-ttu-id="60088-124">Configuration</span><span class="sxs-lookup"><span data-stu-id="60088-124">Configuration</span></span>

<span data-ttu-id="60088-125">Webassembly éblouissant charge la configuration à partir de :</span><span class="sxs-lookup"><span data-stu-id="60088-125">Blazor WebAssembly loads configuration from:</span></span>

* <span data-ttu-id="60088-126">Fichiers de paramètres d’application par défaut :</span><span class="sxs-lookup"><span data-stu-id="60088-126">App settings files by default:</span></span>
  * <span data-ttu-id="60088-127">*wwwroot/appSettings. JSON*</span><span class="sxs-lookup"><span data-stu-id="60088-127">*wwwroot/appsettings.json*</span></span>
  * <span data-ttu-id="60088-128">*wwwroot/appSettings. {ENVIRONMENT}. JSON*</span><span class="sxs-lookup"><span data-stu-id="60088-128">*wwwroot/appsettings.{ENVIRONMENT}.json*</span></span>
* <span data-ttu-id="60088-129">Autres [fournisseurs de configuration](xref:fundamentals/configuration/index) inscrits par l’application.</span><span class="sxs-lookup"><span data-stu-id="60088-129">Other [configuration providers](xref:fundamentals/configuration/index) registered by the app.</span></span> <span data-ttu-id="60088-130">Tous les fournisseurs ne sont pas appropriés pour les applications de webassembly éblouissantes.</span><span class="sxs-lookup"><span data-stu-id="60088-130">Not all providers are appropriate for Blazor WebAssembly apps.</span></span> <span data-ttu-id="60088-131">La clarification des fournisseurs pris en charge pour le webassembly éblouissant est suivie par [clarifier les fournisseurs de configuration pour éblouissant WASM (dotnet/AspNetCore. Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span><span class="sxs-lookup"><span data-stu-id="60088-131">Clarification on which providers are supported for Blazor WebAssembly is tracked by [Clarify configuration providers for Blazor WASM (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span></span>

> [!WARNING]
> <span data-ttu-id="60088-132">La configuration dans une application de webassembly éblouissante est visible pour les utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="60088-132">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="60088-133">**Ne stockez pas les secrets ou les informations d’identification de l’application dans la configuration.**</span><span class="sxs-lookup"><span data-stu-id="60088-133">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="60088-134">Pour plus d’informations sur les fournisseurs de <xref:fundamentals/configuration/index>configuration, consultez.</span><span class="sxs-lookup"><span data-stu-id="60088-134">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

#### <a name="app-settings-configuration"></a><span data-ttu-id="60088-135">Configuration des paramètres de l’application</span><span class="sxs-lookup"><span data-stu-id="60088-135">App settings configuration</span></span>

<span data-ttu-id="60088-136">*wwwroot/appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="60088-136">*wwwroot/appsettings.json*:</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="60088-137">Injecter <xref:Microsoft.Extensions.Configuration.IConfiguration> une instance dans un composant pour accéder aux données de configuration :</span><span class="sxs-lookup"><span data-stu-id="60088-137">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

#### <a name="provider-configuration"></a><span data-ttu-id="60088-138">Configuration du fournisseur</span><span class="sxs-lookup"><span data-stu-id="60088-138">Provider configuration</span></span>

<span data-ttu-id="60088-139">L’exemple suivant utilise un <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> pour fournir une configuration supplémentaire :</span><span class="sxs-lookup"><span data-stu-id="60088-139">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> to supply additional configuration:</span></span>

<span data-ttu-id="60088-140">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="60088-140">`Program.Main`:</span></span>

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

<span data-ttu-id="60088-141">Injecter <xref:Microsoft.Extensions.Configuration.IConfiguration> une instance dans un composant pour accéder aux données de configuration :</span><span class="sxs-lookup"><span data-stu-id="60088-141">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

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

<span data-ttu-id="60088-142">Pour lire d’autres fichiers de configuration du dossier *wwwroot* dans la configuration, `HttpClient` utilisez un pour obtenir le contenu du fichier.</span><span class="sxs-lookup"><span data-stu-id="60088-142">To read other configuration files from the *wwwroot* folder into configuration, use an `HttpClient` to obtain the file's content.</span></span> <span data-ttu-id="60088-143">Lors de l’utilisation de cette approche `HttpClient` , l’inscription de service existante peut utiliser le client local créé pour lire le fichier, comme le montre l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="60088-143">When using this approach, the existing `HttpClient` service registration can use the local client created to read the file, as the following example shows:</span></span>

<span data-ttu-id="60088-144">*wwwroot/cars. JSON*:</span><span class="sxs-lookup"><span data-stu-id="60088-144">*wwwroot/cars.json*:</span></span>

```json
{
    "size": "tiny"
}
```

<span data-ttu-id="60088-145">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="60088-145">`Program.Main`:</span></span>

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

#### <a name="authentication-configuration"></a><span data-ttu-id="60088-146">Configuration de l’authentification</span><span class="sxs-lookup"><span data-stu-id="60088-146">Authentication configuration</span></span>

<span data-ttu-id="60088-147">*wwwroot/appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="60088-147">*wwwroot/appsettings.json*:</span></span>

```json
{
  "AzureAD": {
    "Authority": "https://login.microsoftonline.com/",
    "ClientId": "aeaebf0f-d416-4d92-a08f-e1d5b51fc494"
  }
}
```

<span data-ttu-id="60088-148">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="60088-148">`Program.Main`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("AzureAD", options);
```

#### <a name="logging-configuration"></a><span data-ttu-id="60088-149">Configuration de la journalisation</span><span class="sxs-lookup"><span data-stu-id="60088-149">Logging configuration</span></span>

<span data-ttu-id="60088-150">*wwwroot/appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="60088-150">*wwwroot/appsettings.json*:</span></span>

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

<span data-ttu-id="60088-151">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="60088-151">`Program.Main`:</span></span>

```csharp
builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

#### <a name="host-builder-configuration"></a><span data-ttu-id="60088-152">Configuration du générateur d’ordinateurs hôtes</span><span class="sxs-lookup"><span data-stu-id="60088-152">Host builder configuration</span></span>

<span data-ttu-id="60088-153">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="60088-153">`Program.Main`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

#### <a name="cached-configuration"></a><span data-ttu-id="60088-154">Configuration mise en cache</span><span class="sxs-lookup"><span data-stu-id="60088-154">Cached configuration</span></span>

<span data-ttu-id="60088-155">Les fichiers de configuration sont mis en cache pour une utilisation hors connexion.</span><span class="sxs-lookup"><span data-stu-id="60088-155">Configuration files are cached for offline use.</span></span> <span data-ttu-id="60088-156">Avec les [applications Web progressives (PWA)](xref:blazor/progressive-web-app), vous pouvez uniquement mettre à jour les fichiers de configuration lors de la création d’un déploiement.</span><span class="sxs-lookup"><span data-stu-id="60088-156">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="60088-157">La modification des fichiers de configuration entre les déploiements n’a aucun effet, car :</span><span class="sxs-lookup"><span data-stu-id="60088-157">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="60088-158">Les utilisateurs ont des versions mises en cache des fichiers qu’ils continuent d’utiliser.</span><span class="sxs-lookup"><span data-stu-id="60088-158">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="60088-159">Les fichiers *Service-Worker. js* et *Service-Worker-Assets. js* de PWA doivent être reconstruits lors de la compilation, qui signalent à l’application à la prochaine visite en ligne de l’utilisateur que l’application a été redéployée.</span><span class="sxs-lookup"><span data-stu-id="60088-159">The PWA's *service-worker.js* and *service-worker-assets.js* files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="60088-160">Pour plus d’informations sur la façon dont les mises à jour en arrière- <xref:blazor/progressive-web-app#background-updates>plan sont gérées par PWA, consultez.</span><span class="sxs-lookup"><span data-stu-id="60088-160">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>

### <a name="logging"></a><span data-ttu-id="60088-161">Journalisation</span><span class="sxs-lookup"><span data-stu-id="60088-161">Logging</span></span>

<span data-ttu-id="60088-162">Pour plus d’informations sur la prise en charge de la <xref:fundamentals/logging/index#create-logs-in-blazor>journalisation de webassembly éblouissant, consultez.</span><span class="sxs-lookup"><span data-stu-id="60088-162">For information on Blazor WebAssembly logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>

## <a name="blazor-server"></a><span data-ttu-id="60088-163">Serveur Blazor</span><span class="sxs-lookup"><span data-stu-id="60088-163">Blazor Server</span></span>

### <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="60088-164">Refléter l’état de la connexion dans l’interface utilisateur</span><span class="sxs-lookup"><span data-stu-id="60088-164">Reflect the connection state in the UI</span></span>

<span data-ttu-id="60088-165">Lorsque le client détecte que la connexion a été perdue, une interface utilisateur par défaut est affichée à l’utilisateur pendant que le client tente de se reconnecter.</span><span class="sxs-lookup"><span data-stu-id="60088-165">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="60088-166">En cas d’échec de la reconnexion, l’utilisateur a la possibilité de réessayer.</span><span class="sxs-lookup"><span data-stu-id="60088-166">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="60088-167">Pour personnaliser l’interface utilisateur, définissez un élément avec `id` un `components-reconnect-modal` de dans `<body>` le de la page Razor *_Host. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="60088-167">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="60088-168">Le tableau suivant décrit les classes CSS appliquées à l' `components-reconnect-modal` élément.</span><span class="sxs-lookup"><span data-stu-id="60088-168">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="60088-169">Classe CSS</span><span class="sxs-lookup"><span data-stu-id="60088-169">CSS class</span></span>                       | <span data-ttu-id="60088-170">Détermine&hellip;</span><span class="sxs-lookup"><span data-stu-id="60088-170">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="60088-171">Connexion perdue.</span><span class="sxs-lookup"><span data-stu-id="60088-171">A lost connection.</span></span> <span data-ttu-id="60088-172">Le client tente de se reconnecter.</span><span class="sxs-lookup"><span data-stu-id="60088-172">The client is attempting to reconnect.</span></span> <span data-ttu-id="60088-173">Affichez le modal.</span><span class="sxs-lookup"><span data-stu-id="60088-173">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="60088-174">Une connexion active est rétablie sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="60088-174">An active connection is re-established to the server.</span></span> <span data-ttu-id="60088-175">Masquez le modal.</span><span class="sxs-lookup"><span data-stu-id="60088-175">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="60088-176">Échec de la reconnexion, probablement en raison d’une défaillance du réseau.</span><span class="sxs-lookup"><span data-stu-id="60088-176">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="60088-177">Pour tenter une reconnexion, `window.Blazor.reconnect()`appelez.</span><span class="sxs-lookup"><span data-stu-id="60088-177">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="60088-178">Reconnexion refusée.</span><span class="sxs-lookup"><span data-stu-id="60088-178">Reconnection rejected.</span></span> <span data-ttu-id="60088-179">Le serveur a été atteint mais a refusé la connexion, et l’état de l’utilisateur sur le serveur est perdu.</span><span class="sxs-lookup"><span data-stu-id="60088-179">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="60088-180">Pour recharger l’application, appelez `location.reload()`.</span><span class="sxs-lookup"><span data-stu-id="60088-180">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="60088-181">Cet état de connexion peut se produire dans les cas suivants :</span><span class="sxs-lookup"><span data-stu-id="60088-181">This connection state may result when:</span></span><ul><li><span data-ttu-id="60088-182">Un blocage dans le circuit côté serveur se produit.</span><span class="sxs-lookup"><span data-stu-id="60088-182">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="60088-183">Le client est déconnecté suffisamment longtemps pour que le serveur supprime l’état de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="60088-183">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="60088-184">Les instances des composants avec lesquels l’utilisateur interagit sont supprimées.</span><span class="sxs-lookup"><span data-stu-id="60088-184">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="60088-185">Le serveur est redémarré ou le processus de travail de l’application est recyclé.</span><span class="sxs-lookup"><span data-stu-id="60088-185">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

### <a name="render-mode"></a><span data-ttu-id="60088-186">Mode d’affichage</span><span class="sxs-lookup"><span data-stu-id="60088-186">Render mode</span></span>

<span data-ttu-id="60088-187">Les applications serveur éblouissantes sont configurées par défaut pour prérestituer l’interface utilisateur sur le serveur avant que la connexion cliente au serveur soit établie.</span><span class="sxs-lookup"><span data-stu-id="60088-187">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="60088-188">Elle est configurée dans la page Razor *_Host. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="60088-188">This is set up in the *_Host.cshtml* Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="60088-189">`RenderMode`Configure si le composant :</span><span class="sxs-lookup"><span data-stu-id="60088-189">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="60088-190">Est prérendu dans la page.</span><span class="sxs-lookup"><span data-stu-id="60088-190">Is prerendered into the page.</span></span>
* <span data-ttu-id="60088-191">Est rendu en HTML statique sur la page ou s’il contient les informations nécessaires pour démarrer une application éblouissant à partir de l’agent utilisateur.</span><span class="sxs-lookup"><span data-stu-id="60088-191">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| `RenderMode`        | <span data-ttu-id="60088-192">Description</span><span class="sxs-lookup"><span data-stu-id="60088-192">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="60088-193">Génère le rendu du composant en HTML statique et comprend un marqueur Blazor pour une application serveur.</span><span class="sxs-lookup"><span data-stu-id="60088-193">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="60088-194">Au démarrage de l’agent utilisateur, ce marqueur est utilisé pour démarrer Blazor une application.</span><span class="sxs-lookup"><span data-stu-id="60088-194">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Server`            | <span data-ttu-id="60088-195">Restitue un marqueur pour Blazor une application serveur.</span><span class="sxs-lookup"><span data-stu-id="60088-195">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="60088-196">La sortie du composant n’est pas incluse.</span><span class="sxs-lookup"><span data-stu-id="60088-196">Output from the component isn't included.</span></span> <span data-ttu-id="60088-197">Au démarrage de l’agent utilisateur, ce marqueur est utilisé pour démarrer Blazor une application.</span><span class="sxs-lookup"><span data-stu-id="60088-197">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Static`            | <span data-ttu-id="60088-198">Génère le rendu du composant en HTML statique.</span><span class="sxs-lookup"><span data-stu-id="60088-198">Renders the component into static HTML.</span></span> |

<span data-ttu-id="60088-199">Le rendu des composants serveur à partir d’une page HTML statique n’est pas pris en charge.</span><span class="sxs-lookup"><span data-stu-id="60088-199">Rendering server components from a static HTML page isn't supported.</span></span>

### <a name="configure-the-signalr-client-for-blazor-server-apps"></a><span data-ttu-id="60088-200">Configurer le SignalR client pour Blazor les applications serveur</span><span class="sxs-lookup"><span data-stu-id="60088-200">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="60088-201">Parfois, vous devez configurer le client SignalR utilisé par Blazor les applications serveur.</span><span class="sxs-lookup"><span data-stu-id="60088-201">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="60088-202">Par exemple, vous pouvez configurer la journalisation sur le SignalR client pour diagnostiquer un problème de connexion.</span><span class="sxs-lookup"><span data-stu-id="60088-202">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="60088-203">Pour configurer le SignalR client dans le fichier *pages/_Host. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="60088-203">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="60088-204">Ajoutez un `autostart="false"` attribut à la `<script>` balise pour `blazor.server.js` le script.</span><span class="sxs-lookup"><span data-stu-id="60088-204">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="60088-205">Appelez `Blazor.start` et transmettez un objet de configuration qui spécifie le SignalR générateur.</span><span class="sxs-lookup"><span data-stu-id="60088-205">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

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

### <a name="logging"></a><span data-ttu-id="60088-206">Journalisation</span><span class="sxs-lookup"><span data-stu-id="60088-206">Logging</span></span>

<span data-ttu-id="60088-207">Pour plus d' Blazor informations sur la prise en <xref:fundamentals/logging/index#create-logs-in-blazor>charge de la journalisation du serveur, consultez.</span><span class="sxs-lookup"><span data-stu-id="60088-207">For information on Blazor Server logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>
