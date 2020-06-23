---
title: Configuration de ASP.NET Core Blazor
author: guardrex
description: En savoir plus sur la configuration des Blazor applications, notamment les paramètres d’application, l’authentification et la configuration de la journalisation.
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
uid: blazor/fundamentals/configuration
ms.openlocfilehash: 0e36b81d771b07e85158724c02210ee50a3ab118
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2020
ms.locfileid: "85242678"
---
# <a name="aspnet-core-blazor-configuration"></a><span data-ttu-id="c106f-103">Configuration de ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="c106f-103">ASP.NET Core Blazor configuration</span></span>

> [!NOTE]
> <span data-ttu-id="c106f-104">Cette rubrique s’applique à Blazor Webassembly.</span><span class="sxs-lookup"><span data-stu-id="c106f-104">This topic applies to Blazor WebAssembly.</span></span> <span data-ttu-id="c106f-105">Pour obtenir des instructions générales sur ASP.NET Core configuration des applications, consultez <xref:fundamentals/configuration/index> .</span><span class="sxs-lookup"><span data-stu-id="c106f-105">For general guidance on ASP.NET Core app configuration, see <xref:fundamentals/configuration/index>.</span></span>

Blazor<span data-ttu-id="c106f-106">Webassembly charge la configuration à partir de :</span><span class="sxs-lookup"><span data-stu-id="c106f-106"> WebAssembly loads configuration from:</span></span>

* <span data-ttu-id="c106f-107">Fichiers de paramètres d’application par défaut :</span><span class="sxs-lookup"><span data-stu-id="c106f-107">App settings files by default:</span></span>
  * `wwwroot/appsettings.json`
  * `wwwroot/appsettings.{ENVIRONMENT}.json`
* <span data-ttu-id="c106f-108">Autres [fournisseurs de configuration](xref:fundamentals/configuration/index) inscrits par l’application.</span><span class="sxs-lookup"><span data-stu-id="c106f-108">Other [configuration providers](xref:fundamentals/configuration/index) registered by the app.</span></span> <span data-ttu-id="c106f-109">Tous les fournisseurs ne sont pas appropriés pour les Blazor applications Webassembly.</span><span class="sxs-lookup"><span data-stu-id="c106f-109">Not all providers are appropriate for Blazor WebAssembly apps.</span></span> <span data-ttu-id="c106f-110">La clarification des fournisseurs pris en charge pour Blazor Webassembly est suivie par [clarifier les fournisseurs de configuration pour Blazor WASM (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span><span class="sxs-lookup"><span data-stu-id="c106f-110">Clarification on which providers are supported for Blazor WebAssembly is tracked by [Clarify configuration providers for Blazor WASM (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span></span>

> [!WARNING]
> <span data-ttu-id="c106f-111">La configuration dans une Blazor application Webassembly est visible pour les utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="c106f-111">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="c106f-112">**Ne stockez pas les secrets ou les informations d’identification de l’application dans la configuration.**</span><span class="sxs-lookup"><span data-stu-id="c106f-112">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="c106f-113">Pour plus d’informations sur les fournisseurs de configuration, consultez <xref:fundamentals/configuration/index> .</span><span class="sxs-lookup"><span data-stu-id="c106f-113">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="app-settings-configuration"></a><span data-ttu-id="c106f-114">Configuration des paramètres de l’application</span><span class="sxs-lookup"><span data-stu-id="c106f-114">App settings configuration</span></span>

<span data-ttu-id="c106f-115">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="c106f-115">`wwwroot/appsettings.json`:</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="c106f-116">Injecter une <xref:Microsoft.Extensions.Configuration.IConfiguration> instance dans un composant pour accéder aux données de configuration :</span><span class="sxs-lookup"><span data-stu-id="c106f-116">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

## <a name="provider-configuration"></a><span data-ttu-id="c106f-117">Configuration du fournisseur</span><span class="sxs-lookup"><span data-stu-id="c106f-117">Provider configuration</span></span>

<span data-ttu-id="c106f-118">L’exemple suivant utilise un <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> pour fournir une configuration supplémentaire :</span><span class="sxs-lookup"><span data-stu-id="c106f-118">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> to supply additional configuration:</span></span>

<span data-ttu-id="c106f-119">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="c106f-119">`Program.Main`:</span></span>

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

<span data-ttu-id="c106f-120">Injecter une <xref:Microsoft.Extensions.Configuration.IConfiguration> instance dans un composant pour accéder aux données de configuration :</span><span class="sxs-lookup"><span data-stu-id="c106f-120">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

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

<span data-ttu-id="c106f-121">Pour lire d’autres fichiers de configuration du `wwwroot` dossier dans la configuration, utilisez un <xref:System.Net.Http.HttpClient> pour obtenir le contenu du fichier.</span><span class="sxs-lookup"><span data-stu-id="c106f-121">To read other configuration files from the `wwwroot` folder into configuration, use an <xref:System.Net.Http.HttpClient> to obtain the file's content.</span></span> <span data-ttu-id="c106f-122">Lors de l’utilisation de cette approche, l' <xref:System.Net.Http.HttpClient> inscription de service existante peut utiliser le client local créé pour lire le fichier, comme le montre l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="c106f-122">When using this approach, the existing <xref:System.Net.Http.HttpClient> service registration can use the local client created to read the file, as the following example shows:</span></span>

<span data-ttu-id="c106f-123">`wwwroot/cars.json`:</span><span class="sxs-lookup"><span data-stu-id="c106f-123">`wwwroot/cars.json`:</span></span>

```json
{
    "size": "tiny"
}
```

<span data-ttu-id="c106f-124">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="c106f-124">`Program.Main`:</span></span>

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

## <a name="authentication-configuration"></a><span data-ttu-id="c106f-125">Configuration de l’authentification</span><span class="sxs-lookup"><span data-stu-id="c106f-125">Authentication configuration</span></span>

<span data-ttu-id="c106f-126">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="c106f-126">`wwwroot/appsettings.json`:</span></span>

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="c106f-127">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="c106f-127">`Program.Main`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions));
```

## <a name="logging-configuration"></a><span data-ttu-id="c106f-128">Configuration de la journalisation</span><span class="sxs-lookup"><span data-stu-id="c106f-128">Logging configuration</span></span>

<span data-ttu-id="c106f-129">Ajoutez une référence de package pour [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration/) :</span><span class="sxs-lookup"><span data-stu-id="c106f-129">Add a package reference for [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration/):</span></span>

```xml
<PackageReference Include="Microsoft.Extensions.Logging.Configuration" Version="{VERSION}" />
```

<span data-ttu-id="c106f-130">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="c106f-130">`wwwroot/appsettings.json`:</span></span>

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

<span data-ttu-id="c106f-131">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="c106f-131">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Logging;

...

builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

## <a name="host-builder-configuration"></a><span data-ttu-id="c106f-132">Configuration du générateur d’ordinateurs hôtes</span><span class="sxs-lookup"><span data-stu-id="c106f-132">Host builder configuration</span></span>

<span data-ttu-id="c106f-133">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="c106f-133">`Program.Main`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

## <a name="cached-configuration"></a><span data-ttu-id="c106f-134">Configuration mise en cache</span><span class="sxs-lookup"><span data-stu-id="c106f-134">Cached configuration</span></span>

<span data-ttu-id="c106f-135">Les fichiers de configuration sont mis en cache pour une utilisation hors connexion.</span><span class="sxs-lookup"><span data-stu-id="c106f-135">Configuration files are cached for offline use.</span></span> <span data-ttu-id="c106f-136">Avec les [applications Web progressives (PWA)](xref:blazor/progressive-web-app), vous pouvez uniquement mettre à jour les fichiers de configuration lors de la création d’un déploiement.</span><span class="sxs-lookup"><span data-stu-id="c106f-136">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="c106f-137">La modification des fichiers de configuration entre les déploiements n’a aucun effet, car :</span><span class="sxs-lookup"><span data-stu-id="c106f-137">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="c106f-138">Les utilisateurs ont des versions mises en cache des fichiers qu’ils continuent d’utiliser.</span><span class="sxs-lookup"><span data-stu-id="c106f-138">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="c106f-139">Les `service-worker.js` fichiers et de PWA `service-worker-assets.js` doivent être reconstruits lors de la compilation, qui signalent à l’application à la prochaine visite en ligne de l’utilisateur que l’application a été redéployée.</span><span class="sxs-lookup"><span data-stu-id="c106f-139">The PWA's `service-worker.js` and `service-worker-assets.js` files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="c106f-140">Pour plus d’informations sur la façon dont les mises à jour en arrière-plan sont gérées par PWA, consultez <xref:blazor/progressive-web-app#background-updates> .</span><span class="sxs-lookup"><span data-stu-id="c106f-140">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>
