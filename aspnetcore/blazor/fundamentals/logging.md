---
title: BlazorJournalisation ASP.net Core
author: guardrex
description: En savoir plus sur la journalisation dans Blazor les applications, y compris la configuration du niveau de journalisation et comment écrire des messages de journal à partir de Razor composants.
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
uid: blazor/fundamentals/logging
ms.openlocfilehash: b0448d5f6e5e16a726eb2274dcacb4dfa8314b5d
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103693"
---
# <a name="aspnet-core-blazor-logging"></a><span data-ttu-id="b464f-103">BlazorJournalisation ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="b464f-103">ASP.NET Core Blazor logging</span></span>

## <a name="blazor-webassembly"></a>Blazor<span data-ttu-id="b464f-104">Webassembly</span><span class="sxs-lookup"><span data-stu-id="b464f-104"> WebAssembly</span></span>

<span data-ttu-id="b464f-105">Configurez la journalisation dans Blazor les applications Webassembly avec la `WebAssemblyHostBuilder.Logging` propriété dans `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="b464f-105">Configure logging in Blazor WebAssembly apps with the `WebAssemblyHostBuilder.Logging` property in `Program.Main`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;

...

var builder = WebAssemblyHostBuilder.CreateDefault(args);

builder.Logging.SetMinimumLevel(LogLevel.Debug);
builder.Logging.AddProvider(new CustomLoggingProvider());
```

<span data-ttu-id="b464f-106">La `Logging` propriété est de type <xref:Microsoft.Extensions.Logging.ILoggingBuilder> , donc toutes les méthodes d’extension disponibles sur <xref:Microsoft.Extensions.Logging.ILoggingBuilder> sont également disponibles sur `Logging` .</span><span class="sxs-lookup"><span data-stu-id="b464f-106">The `Logging` property is of type <xref:Microsoft.Extensions.Logging.ILoggingBuilder>, so all of the extension methods available on <xref:Microsoft.Extensions.Logging.ILoggingBuilder> are also available on `Logging`.</span></span>

<span data-ttu-id="b464f-107">La configuration de la journalisation peut être chargée à partir des fichiers de paramètres d’application.</span><span class="sxs-lookup"><span data-stu-id="b464f-107">Logging configuration can be loaded from app settings files.</span></span> <span data-ttu-id="b464f-108">Pour plus d’informations, consultez <xref:blazor/fundamentals/logging>.</span><span class="sxs-lookup"><span data-stu-id="b464f-108">For more information, see <xref:blazor/fundamentals/logging>.</span></span>

## <a name="blazor-server"></a>Blazor<span data-ttu-id="b464f-109">Serveurs</span><span class="sxs-lookup"><span data-stu-id="b464f-109"> Server</span></span>

<span data-ttu-id="b464f-110">Pour obtenir des instructions générales sur la journalisation des ASP.NET Core, consultez <xref:fundamentals/logging/index> .</span><span class="sxs-lookup"><span data-stu-id="b464f-110">For general ASP.NET Core logging guidance, see <xref:fundamentals/logging/index>.</span></span>

## <a name="blazor-webassembly-signalr-net-client-logging"></a>Blazor<span data-ttu-id="b464f-111">SignalRJournalisation du client .net Webassembly</span><span class="sxs-lookup"><span data-stu-id="b464f-111"> WebAssembly SignalR .NET client logging</span></span>

<span data-ttu-id="b464f-112">Injectez un <xref:Microsoft.Extensions.Logging.ILoggerProvider> pour ajouter une `WebAssemblyConsoleLogger` aux fournisseurs de journalisation passés à <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> .</span><span class="sxs-lookup"><span data-stu-id="b464f-112">Inject an <xref:Microsoft.Extensions.Logging.ILoggerProvider> to add a `WebAssemblyConsoleLogger` to the logging providers passed to <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder>.</span></span> <span data-ttu-id="b464f-113">Contrairement à un traditionnel <xref:Microsoft.Extensions.Logging.Console.ConsoleLogger> , `WebAssemblyConsoleLogger` est un wrapper autour des API de journalisation spécifiques aux navigateurs (par exemple, `console.log` ).</span><span class="sxs-lookup"><span data-stu-id="b464f-113">Unlike a traditional <xref:Microsoft.Extensions.Logging.Console.ConsoleLogger>, `WebAssemblyConsoleLogger` is a wrapper around browser-specific logging APIs (for example, `console.log`).</span></span> <span data-ttu-id="b464f-114">L’utilisation de `WebAssemblyConsoleLogger` rend la journalisation possible dans mono dans un contexte de navigateur.</span><span class="sxs-lookup"><span data-stu-id="b464f-114">Use of `WebAssemblyConsoleLogger` makes logging possible within Mono inside a browser context.</span></span>

```csharp
@using Microsoft.Extensions.Logging
@inject ILoggerProvider LoggerProvider

...

var connection = new HubConnectionBuilder()
    .WithUrl(NavigationManager.ToAbsoluteUri("/chathub"))
    .ConfigureLogging(logging => logging.AddProvider(LoggerProvider))
    .Build();
```

## <a name="log-in-razor-components"></a><span data-ttu-id="b464f-115">Connecter les Razor composants</span><span class="sxs-lookup"><span data-stu-id="b464f-115">Log in Razor components</span></span>

<span data-ttu-id="b464f-116">Les journaux respectent la configuration de démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="b464f-116">Loggers respect app startup configuration.</span></span>

<span data-ttu-id="b464f-117">La `using` directive pour <xref:Microsoft.Extensions.Logging> est requise pour prendre en charge les saisies semi-automatiques IntelliSense pour les API, telles que <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> et <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A> .</span><span class="sxs-lookup"><span data-stu-id="b464f-117">The `using` directive for <xref:Microsoft.Extensions.Logging> is required to support Intellisense completions for APIs, such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> and <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A>.</span></span>

<span data-ttu-id="b464f-118">L’exemple suivant illustre la journalisation avec un <xref:Microsoft.Extensions.Logging.ILogger> dans des Razor composants :</span><span class="sxs-lookup"><span data-stu-id="b464f-118">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILogger> in Razor components:</span></span>

```razor
@page "/counter"
@using Microsoft.Extensions.Logging;
@inject ILogger<Counter> logger;

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    private void IncrementCount()
    {
        logger.LogWarning("Someone has clicked me!");

        currentCount++;
    }
}
```

<span data-ttu-id="b464f-119">L’exemple suivant illustre la journalisation avec un <xref:Microsoft.Extensions.Logging.ILoggerFactory> dans des Razor composants :</span><span class="sxs-lookup"><span data-stu-id="b464f-119">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILoggerFactory> in Razor components:</span></span>

```razor
@page "/counter"
@using Microsoft.Extensions.Logging;
@inject ILoggerFactory LoggerFactory

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    private void IncrementCount()
    {
        var logger = LoggerFactory.CreateLogger<Counter>();
        logger.LogWarning("Someone has clicked me!");

        currentCount++;
    }
}
```

## <a name="additional-resources"></a><span data-ttu-id="b464f-120">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="b464f-120">Additional resources</span></span>

* <xref:fundamentals/logging/index>
