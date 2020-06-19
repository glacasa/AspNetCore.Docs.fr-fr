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
ms.openlocfilehash: b43eae03c71cabbaafa2bc0d704765e89f743279
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103701"
---
# <a name="aspnet-core-blazor-configuration"></a><span data-ttu-id="f2382-103">Configuration de ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="f2382-103">ASP.NET Core Blazor configuration</span></span>

> [!NOTE]
> <span data-ttu-id="f2382-104">Cette rubrique s’applique à Blazor Webassembly.</span><span class="sxs-lookup"><span data-stu-id="f2382-104">This topic applies to Blazor WebAssembly.</span></span> <span data-ttu-id="f2382-105">Pour obtenir des instructions générales sur ASP.NET Core configuration des applications, consultez <xref:fundamentals/configuration/index> .</span><span class="sxs-lookup"><span data-stu-id="f2382-105">For general guidance on ASP.NET Core app configuration, see <xref:fundamentals/configuration/index>.</span></span>

Blazor<span data-ttu-id="f2382-106">Webassembly charge la configuration à partir de :</span><span class="sxs-lookup"><span data-stu-id="f2382-106"> WebAssembly loads configuration from:</span></span>

* <span data-ttu-id="f2382-107">Fichiers de paramètres d’application par défaut :</span><span class="sxs-lookup"><span data-stu-id="f2382-107">App settings files by default:</span></span>
  * <span data-ttu-id="f2382-108">*wwwroot/appsettings.jssur*</span><span class="sxs-lookup"><span data-stu-id="f2382-108">*wwwroot/appsettings.json*</span></span>
  * <span data-ttu-id="f2382-109">*wwwroot/appSettings. {ENVIRONMENT}. JSON*</span><span class="sxs-lookup"><span data-stu-id="f2382-109">*wwwroot/appsettings.{ENVIRONMENT}.json*</span></span>
* <span data-ttu-id="f2382-110">Autres [fournisseurs de configuration](xref:fundamentals/configuration/index) inscrits par l’application.</span><span class="sxs-lookup"><span data-stu-id="f2382-110">Other [configuration providers](xref:fundamentals/configuration/index) registered by the app.</span></span> <span data-ttu-id="f2382-111">Tous les fournisseurs ne sont pas appropriés pour les Blazor applications Webassembly.</span><span class="sxs-lookup"><span data-stu-id="f2382-111">Not all providers are appropriate for Blazor WebAssembly apps.</span></span> <span data-ttu-id="f2382-112">La clarification des fournisseurs pris en charge pour Blazor Webassembly est suivie par [clarifier les fournisseurs de configuration pour Blazor WASM (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span><span class="sxs-lookup"><span data-stu-id="f2382-112">Clarification on which providers are supported for Blazor WebAssembly is tracked by [Clarify configuration providers for Blazor WASM (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span></span>

> [!WARNING]
> <span data-ttu-id="f2382-113">La configuration dans une Blazor application Webassembly est visible pour les utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="f2382-113">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="f2382-114">**Ne stockez pas les secrets ou les informations d’identification de l’application dans la configuration.**</span><span class="sxs-lookup"><span data-stu-id="f2382-114">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="f2382-115">Pour plus d’informations sur les fournisseurs de configuration, consultez <xref:fundamentals/configuration/index> .</span><span class="sxs-lookup"><span data-stu-id="f2382-115">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="app-settings-configuration"></a><span data-ttu-id="f2382-116">Configuration des paramètres de l’application</span><span class="sxs-lookup"><span data-stu-id="f2382-116">App settings configuration</span></span>

<span data-ttu-id="f2382-117">*wwwroot/appsettings.jssur*:</span><span class="sxs-lookup"><span data-stu-id="f2382-117">*wwwroot/appsettings.json*:</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="f2382-118">Injecter une <xref:Microsoft.Extensions.Configuration.IConfiguration> instance dans un composant pour accéder aux données de configuration :</span><span class="sxs-lookup"><span data-stu-id="f2382-118">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

## <a name="provider-configuration"></a><span data-ttu-id="f2382-119">Configuration du fournisseur</span><span class="sxs-lookup"><span data-stu-id="f2382-119">Provider configuration</span></span>

<span data-ttu-id="f2382-120">L’exemple suivant utilise un <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> pour fournir une configuration supplémentaire :</span><span class="sxs-lookup"><span data-stu-id="f2382-120">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> to supply additional configuration:</span></span>

<span data-ttu-id="f2382-121">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="f2382-121">`Program.Main`:</span></span>

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

<span data-ttu-id="f2382-122">Injecter une <xref:Microsoft.Extensions.Configuration.IConfiguration> instance dans un composant pour accéder aux données de configuration :</span><span class="sxs-lookup"><span data-stu-id="f2382-122">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

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

<span data-ttu-id="f2382-123">Pour lire d’autres fichiers de configuration du dossier *wwwroot* dans la configuration, utilisez un <xref:System.Net.Http.HttpClient> pour obtenir le contenu du fichier.</span><span class="sxs-lookup"><span data-stu-id="f2382-123">To read other configuration files from the *wwwroot* folder into configuration, use an <xref:System.Net.Http.HttpClient> to obtain the file's content.</span></span> <span data-ttu-id="f2382-124">Lors de l’utilisation de cette approche, l' <xref:System.Net.Http.HttpClient> inscription de service existante peut utiliser le client local créé pour lire le fichier, comme le montre l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="f2382-124">When using this approach, the existing <xref:System.Net.Http.HttpClient> service registration can use the local client created to read the file, as the following example shows:</span></span>

<span data-ttu-id="f2382-125">*wwwroot/cars.jssur*:</span><span class="sxs-lookup"><span data-stu-id="f2382-125">*wwwroot/cars.json*:</span></span>

```json
{
    "size": "tiny"
}
```

<span data-ttu-id="f2382-126">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="f2382-126">`Program.Main`:</span></span>

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

## <a name="authentication-configuration"></a><span data-ttu-id="f2382-127">Configuration de l’authentification</span><span class="sxs-lookup"><span data-stu-id="f2382-127">Authentication configuration</span></span>

<span data-ttu-id="f2382-128">*wwwroot/appsettings.jssur*:</span><span class="sxs-lookup"><span data-stu-id="f2382-128">*wwwroot/appsettings.json*:</span></span>

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="f2382-129">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="f2382-129">`Program.Main`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions));
```

## <a name="logging-configuration"></a><span data-ttu-id="f2382-130">Configuration de la journalisation</span><span class="sxs-lookup"><span data-stu-id="f2382-130">Logging configuration</span></span>

<span data-ttu-id="f2382-131">Ajoutez une référence de package pour [Microsoft.Extensions.Logging.Configfiguration](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration/):</span><span class="sxs-lookup"><span data-stu-id="f2382-131">Add a package reference for [Microsoft.Extensions.Logging.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration/):</span></span>

```xml
<PackageReference Include="Microsoft.Extensions.Logging.Configuration" Version="{VERSION}" />
```

<span data-ttu-id="f2382-132">*wwwroot/appsettings.jssur*:</span><span class="sxs-lookup"><span data-stu-id="f2382-132">*wwwroot/appsettings.json*:</span></span>

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

<span data-ttu-id="f2382-133">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="f2382-133">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Logging;

...

builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

## <a name="host-builder-configuration"></a><span data-ttu-id="f2382-134">Configuration du générateur d’ordinateurs hôtes</span><span class="sxs-lookup"><span data-stu-id="f2382-134">Host builder configuration</span></span>

<span data-ttu-id="f2382-135">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="f2382-135">`Program.Main`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

## <a name="cached-configuration"></a><span data-ttu-id="f2382-136">Configuration mise en cache</span><span class="sxs-lookup"><span data-stu-id="f2382-136">Cached configuration</span></span>

<span data-ttu-id="f2382-137">Les fichiers de configuration sont mis en cache pour une utilisation hors connexion.</span><span class="sxs-lookup"><span data-stu-id="f2382-137">Configuration files are cached for offline use.</span></span> <span data-ttu-id="f2382-138">Avec les [applications Web progressives (PWA)](xref:blazor/progressive-web-app), vous pouvez uniquement mettre à jour les fichiers de configuration lors de la création d’un déploiement.</span><span class="sxs-lookup"><span data-stu-id="f2382-138">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="f2382-139">La modification des fichiers de configuration entre les déploiements n’a aucun effet, car :</span><span class="sxs-lookup"><span data-stu-id="f2382-139">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="f2382-140">Les utilisateurs ont des versions mises en cache des fichiers qu’ils continuent d’utiliser.</span><span class="sxs-lookup"><span data-stu-id="f2382-140">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="f2382-141">Les fichiers *service-worker.js* et *service-worker-assets.js* de PWA doivent être reconstruits lors de la compilation, qui signalent à l’application à la prochaine visite en ligne de l’utilisateur que l’application a été redéployée.</span><span class="sxs-lookup"><span data-stu-id="f2382-141">The PWA's *service-worker.js* and *service-worker-assets.js* files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="f2382-142">Pour plus d’informations sur la façon dont les mises à jour en arrière-plan sont gérées par PWA, consultez <xref:blazor/progressive-web-app#background-updates> .</span><span class="sxs-lookup"><span data-stu-id="f2382-142">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>
