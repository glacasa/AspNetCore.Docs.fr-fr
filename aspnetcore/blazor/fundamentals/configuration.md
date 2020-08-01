---
title: Configuration de ASP.NET Core Blazor
author: guardrex
description: En savoir plus sur la configuration des Blazor applications, notamment les paramètres d’application, l’authentification et la configuration de la journalisation.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/29/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/configuration
ms.openlocfilehash: 9ae0dcc16b9debd47a61010953243b0abe499c4f
ms.sourcegitcommit: ca6a1f100c1a3f59999189aa962523442dd4ead1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2020
ms.locfileid: "87443969"
---
# <a name="aspnet-core-no-locblazor-configuration"></a><span data-ttu-id="6dd3f-103">Configuration de ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="6dd3f-103">ASP.NET Core Blazor configuration</span></span>

> [!NOTE]
> <span data-ttu-id="6dd3f-104">Cette rubrique s’applique à Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="6dd3f-104">This topic applies to Blazor WebAssembly.</span></span> <span data-ttu-id="6dd3f-105">Pour obtenir des instructions générales sur ASP.NET Core configuration des applications, consultez <xref:fundamentals/configuration/index> .</span><span class="sxs-lookup"><span data-stu-id="6dd3f-105">For general guidance on ASP.NET Core app configuration, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="6dd3f-106">Blazor WebAssemblycharge la configuration à partir des fichiers de paramètres d’application par défaut :</span><span class="sxs-lookup"><span data-stu-id="6dd3f-106">Blazor WebAssembly loads configuration from app settings files by default:</span></span>

* `wwwroot/appsettings.json`
* `wwwroot/appsettings.{ENVIRONMENT}.json`

<span data-ttu-id="6dd3f-107">D’autres fournisseurs de configuration inscrits par l’application peuvent également fournir une configuration.</span><span class="sxs-lookup"><span data-stu-id="6dd3f-107">Other configuration providers registered by the app can also provide configuration.</span></span>

<span data-ttu-id="6dd3f-108">Les fonctionnalités des fournisseurs ou du fournisseur ne sont pas toutes appropriées pour les Blazor WebAssembly applications :</span><span class="sxs-lookup"><span data-stu-id="6dd3f-108">Not all providers or provider features are appropriate for Blazor WebAssembly apps:</span></span>

* <span data-ttu-id="6dd3f-109">[Fournisseur de configuration Azure Key Vault](xref:security/key-vault-configuration): le fournisseur n’est pas pris en charge pour l’identité managée et l’ID d’application (ID client) avec des scénarios de clé secrète client.</span><span class="sxs-lookup"><span data-stu-id="6dd3f-109">[Azure Key Vault configuration provider](xref:security/key-vault-configuration): The provider isn't supported for managed identity and application ID (client ID) with client secret scenarios.</span></span> <span data-ttu-id="6dd3f-110">L’ID d’application avec une clé secrète client n’est pas recommandé pour une application ASP.NET Core, en particulier pour les Blazor WebAssembly applications, car la clé secrète client ne peut pas être sécurisée côté client pour accéder au service.</span><span class="sxs-lookup"><span data-stu-id="6dd3f-110">Application ID with a client secret isn't recommended for any ASP.NET Core app, especially Blazor WebAssembly apps because the client secret can't be secured client-side to access to the service.</span></span>
* <span data-ttu-id="6dd3f-111">[Fournisseur de configuration Azure App](/azure/azure-app-configuration/quickstart-aspnet-core-app): le fournisseur n’est pas approprié pour les Blazor WebAssembly applications, car les Blazor WebAssembly applications ne s’exécutent pas sur un serveur dans Azure.</span><span class="sxs-lookup"><span data-stu-id="6dd3f-111">[Azure App configuration provider](/azure/azure-app-configuration/quickstart-aspnet-core-app): The provider isn't appropriate for Blazor WebAssembly apps because Blazor WebAssembly apps don't run on a server in Azure.</span></span>

> [!WARNING]
> <span data-ttu-id="6dd3f-112">La configuration dans une Blazor WebAssembly application est visible pour les utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="6dd3f-112">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="6dd3f-113">**Ne stockez pas les secrets ou les informations d’identification de l’application dans la configuration.**</span><span class="sxs-lookup"><span data-stu-id="6dd3f-113">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="6dd3f-114">Pour plus d’informations sur les fournisseurs de configuration, consultez <xref:fundamentals/configuration/index> .</span><span class="sxs-lookup"><span data-stu-id="6dd3f-114">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="app-settings-configuration"></a><span data-ttu-id="6dd3f-115">Configuration des paramètres de l’application</span><span class="sxs-lookup"><span data-stu-id="6dd3f-115">App settings configuration</span></span>

<span data-ttu-id="6dd3f-116">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="6dd3f-116">`wwwroot/appsettings.json`:</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="6dd3f-117">Injecter une <xref:Microsoft.Extensions.Configuration.IConfiguration> instance dans un composant pour accéder aux données de configuration :</span><span class="sxs-lookup"><span data-stu-id="6dd3f-117">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

## <a name="custom-configuration-provider-with-ef-core"></a><span data-ttu-id="6dd3f-118">Fournisseur de configuration personnalisé avec EF Core</span><span class="sxs-lookup"><span data-stu-id="6dd3f-118">Custom configuration provider with EF Core</span></span>

<span data-ttu-id="6dd3f-119">Le fournisseur de configuration personnalisé avec EF Core illustré dans <xref:fundamentals/configuration/index#custom-configuration-provider> fonctionne avec les Blazor WebAssembly applications.</span><span class="sxs-lookup"><span data-stu-id="6dd3f-119">The custom configuration provider with EF Core demonstrated in <xref:fundamentals/configuration/index#custom-configuration-provider> works with Blazor WebAssembly apps.</span></span>

<span data-ttu-id="6dd3f-120">Ajoutez le fournisseur de configuration de l’exemple avec le code suivant dans `Program.Main` ( `Program.cs` ) :</span><span class="sxs-lookup"><span data-stu-id="6dd3f-120">Add the example's configuration provider with the following code in `Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Configuration.AddEFConfiguration(
    options => options.UseInMemoryDatabase("InMemoryDb"));
```

<span data-ttu-id="6dd3f-121">Injecter une <xref:Microsoft.Extensions.Configuration.IConfiguration> instance dans un composant pour accéder aux données de configuration :</span><span class="sxs-lookup"><span data-stu-id="6dd3f-121">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<ul>
    <li>@Configuration["quote1"]</li>
    <li>@Configuration["quote2"]</li>
    <li>@Configuration["quote3"]</li>
</ul>
```

## <a name="memory-configuration-source"></a><span data-ttu-id="6dd3f-122">Source de la configuration de la mémoire</span><span class="sxs-lookup"><span data-stu-id="6dd3f-122">Memory Configuration Source</span></span>

<span data-ttu-id="6dd3f-123">L’exemple suivant utilise un <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> pour fournir une configuration supplémentaire :</span><span class="sxs-lookup"><span data-stu-id="6dd3f-123">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> to supply additional configuration:</span></span>

<span data-ttu-id="6dd3f-124">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="6dd3f-124">`Program.Main`:</span></span>

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

<span data-ttu-id="6dd3f-125">Injecter une <xref:Microsoft.Extensions.Configuration.IConfiguration> instance dans un composant pour accéder aux données de configuration :</span><span class="sxs-lookup"><span data-stu-id="6dd3f-125">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

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
    protected override void OnInitialized()
    {
        var wheelsSection = Configuration.GetSection("wheels");
        
        ...
    }
}
```

<span data-ttu-id="6dd3f-126">Pour lire d’autres fichiers de configuration du `wwwroot` dossier dans la configuration, utilisez un <xref:System.Net.Http.HttpClient> pour obtenir le contenu du fichier.</span><span class="sxs-lookup"><span data-stu-id="6dd3f-126">To read other configuration files from the `wwwroot` folder into configuration, use an <xref:System.Net.Http.HttpClient> to obtain the file's content.</span></span> <span data-ttu-id="6dd3f-127">Lors de l’utilisation de cette approche, l' <xref:System.Net.Http.HttpClient> inscription de service existante peut utiliser le client local créé pour lire le fichier, comme le montre l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="6dd3f-127">When using this approach, the existing <xref:System.Net.Http.HttpClient> service registration can use the local client created to read the file, as the following example shows:</span></span>

<span data-ttu-id="6dd3f-128">`wwwroot/cars.json`:</span><span class="sxs-lookup"><span data-stu-id="6dd3f-128">`wwwroot/cars.json`:</span></span>

```json
{
    "size": "tiny"
}
```

<span data-ttu-id="6dd3f-129">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="6dd3f-129">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Configuration;

...

var client = new HttpClient()
{
    BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
};

builder.Services.AddScoped(sp => client);

using var response = await client.GetAsync("cars.json");
using var stream = await response.Content.ReadAsStreamAsync();

builder.Configuration.AddJsonStream(stream);
```

## <a name="authentication-configuration"></a><span data-ttu-id="6dd3f-130">Configuration de l’authentification</span><span class="sxs-lookup"><span data-stu-id="6dd3f-130">Authentication configuration</span></span>

<span data-ttu-id="6dd3f-131">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="6dd3f-131">`wwwroot/appsettings.json`:</span></span>

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="6dd3f-132">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="6dd3f-132">`Program.Main`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions));
```

## <a name="logging-configuration"></a><span data-ttu-id="6dd3f-133">Configuration de la journalisation</span><span class="sxs-lookup"><span data-stu-id="6dd3f-133">Logging configuration</span></span>

<span data-ttu-id="6dd3f-134">Ajoutez une référence de package pour [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration/) :</span><span class="sxs-lookup"><span data-stu-id="6dd3f-134">Add a package reference for [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration/):</span></span>

```xml
<PackageReference Include="Microsoft.Extensions.Logging.Configuration" Version="{VERSION}" />
```

<span data-ttu-id="6dd3f-135">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="6dd3f-135">`wwwroot/appsettings.json`:</span></span>

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

<span data-ttu-id="6dd3f-136">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="6dd3f-136">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Logging;

...

builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

## <a name="host-builder-configuration"></a><span data-ttu-id="6dd3f-137">Configuration du générateur d’ordinateurs hôtes</span><span class="sxs-lookup"><span data-stu-id="6dd3f-137">Host builder configuration</span></span>

<span data-ttu-id="6dd3f-138">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="6dd3f-138">`Program.Main`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

## <a name="cached-configuration"></a><span data-ttu-id="6dd3f-139">Configuration mise en cache</span><span class="sxs-lookup"><span data-stu-id="6dd3f-139">Cached configuration</span></span>

<span data-ttu-id="6dd3f-140">Les fichiers de configuration sont mis en cache pour une utilisation hors connexion.</span><span class="sxs-lookup"><span data-stu-id="6dd3f-140">Configuration files are cached for offline use.</span></span> <span data-ttu-id="6dd3f-141">Avec les [applications Web progressives (PWA)](xref:blazor/progressive-web-app), vous pouvez uniquement mettre à jour les fichiers de configuration lors de la création d’un déploiement.</span><span class="sxs-lookup"><span data-stu-id="6dd3f-141">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="6dd3f-142">La modification des fichiers de configuration entre les déploiements n’a aucun effet, car :</span><span class="sxs-lookup"><span data-stu-id="6dd3f-142">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="6dd3f-143">Les utilisateurs ont des versions mises en cache des fichiers qu’ils continuent d’utiliser.</span><span class="sxs-lookup"><span data-stu-id="6dd3f-143">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="6dd3f-144">Les `service-worker.js` fichiers et de PWA `service-worker-assets.js` doivent être reconstruits lors de la compilation, qui signalent à l’application à la prochaine visite en ligne de l’utilisateur que l’application a été redéployée.</span><span class="sxs-lookup"><span data-stu-id="6dd3f-144">The PWA's `service-worker.js` and `service-worker-assets.js` files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="6dd3f-145">Pour plus d’informations sur la façon dont les mises à jour en arrière-plan sont gérées par PWA, consultez <xref:blazor/progressive-web-app#background-updates> .</span><span class="sxs-lookup"><span data-stu-id="6dd3f-145">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>
