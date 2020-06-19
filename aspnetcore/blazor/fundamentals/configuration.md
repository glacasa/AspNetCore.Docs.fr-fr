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
# <a name="aspnet-core-blazor-configuration"></a>Configuration de ASP.NET Core Blazor

> [!NOTE]
> Cette rubrique s’applique à Blazor Webassembly. Pour obtenir des instructions générales sur ASP.NET Core configuration des applications, consultez <xref:fundamentals/configuration/index> .

BlazorWebassembly charge la configuration à partir de :

* Fichiers de paramètres d’application par défaut :
  * *wwwroot/appsettings.jssur*
  * *wwwroot/appSettings. {ENVIRONMENT}. JSON*
* Autres [fournisseurs de configuration](xref:fundamentals/configuration/index) inscrits par l’application. Tous les fournisseurs ne sont pas appropriés pour les Blazor applications Webassembly. La clarification des fournisseurs pris en charge pour Blazor Webassembly est suivie par [clarifier les fournisseurs de configuration pour Blazor WASM (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).

> [!WARNING]
> La configuration dans une Blazor application Webassembly est visible pour les utilisateurs. **Ne stockez pas les secrets ou les informations d’identification de l’application dans la configuration.**

Pour plus d’informations sur les fournisseurs de configuration, consultez <xref:fundamentals/configuration/index> .

## <a name="app-settings-configuration"></a>Configuration des paramètres de l’application

*wwwroot/appsettings.jssur*:

```json
{
  "message": "Hello from config!"
}
```

Injecter une <xref:Microsoft.Extensions.Configuration.IConfiguration> instance dans un composant pour accéder aux données de configuration :

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

## <a name="provider-configuration"></a>Configuration du fournisseur

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

Injecter une <xref:Microsoft.Extensions.Configuration.IConfiguration> instance dans un composant pour accéder aux données de configuration :

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

Pour lire d’autres fichiers de configuration du dossier *wwwroot* dans la configuration, utilisez un <xref:System.Net.Http.HttpClient> pour obtenir le contenu du fichier. Lors de l’utilisation de cette approche, l' <xref:System.Net.Http.HttpClient> inscription de service existante peut utiliser le client local créé pour lire le fichier, comme le montre l’exemple suivant :

*wwwroot/cars.jssur*:

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

## <a name="authentication-configuration"></a>Configuration de l’authentification

*wwwroot/appsettings.jssur*:

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

`Program.Main`:

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions));
```

## <a name="logging-configuration"></a>Configuration de la journalisation

Ajoutez une référence de package pour [Microsoft.Extensions.Logging.Configfiguration](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration/):

```xml
<PackageReference Include="Microsoft.Extensions.Logging.Configuration" Version="{VERSION}" />
```

*wwwroot/appsettings.jssur*:

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
using Microsoft.Extensions.Logging;

...

builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

## <a name="host-builder-configuration"></a>Configuration du générateur d’ordinateurs hôtes

`Program.Main`:

```csharp
var hostname = builder.Configuration["HostName"];
```

## <a name="cached-configuration"></a>Configuration mise en cache

Les fichiers de configuration sont mis en cache pour une utilisation hors connexion. Avec les [applications Web progressives (PWA)](xref:blazor/progressive-web-app), vous pouvez uniquement mettre à jour les fichiers de configuration lors de la création d’un déploiement. La modification des fichiers de configuration entre les déploiements n’a aucun effet, car :

* Les utilisateurs ont des versions mises en cache des fichiers qu’ils continuent d’utiliser.
* Les fichiers *service-worker.js* et *service-worker-assets.js* de PWA doivent être reconstruits lors de la compilation, qui signalent à l’application à la prochaine visite en ligne de l’utilisateur que l’application a été redéployée.

Pour plus d’informations sur la façon dont les mises à jour en arrière-plan sont gérées par PWA, consultez <xref:blazor/progressive-web-app#background-updates> .
