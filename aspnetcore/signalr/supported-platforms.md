---
title: Plateformes prises en charge ASP.NET Core SignalR
author: bradygaster
description: En savoir plus sur les plateformes SignalRprises en charge pour ASP.net core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/16/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/supported-platforms
ms.openlocfilehash: 70a05dabb95aaf561aa78d5c8b24b430c51bd973
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82772603"
---
# <a name="aspnet-core-signalr-supported-platforms"></a><span data-ttu-id="5b2c2-103">Plateformes prises en charge par ASP.NET Core Signalr</span><span class="sxs-lookup"><span data-stu-id="5b2c2-103">ASP.NET Core SignalR supported platforms</span></span>

## <a name="server-system-requirements"></a><span data-ttu-id="5b2c2-104">Configuration requise pour le serveur</span><span class="sxs-lookup"><span data-stu-id="5b2c2-104">Server system requirements</span></span>

<span data-ttu-id="5b2c2-105">Signalr pour ASP.NET Core prend en charge toute plateforme de serveur prise en charge par ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5b2c2-105">SignalR for ASP.NET Core supports any server platform that ASP.NET Core supports.</span></span>

## <a name="javascript-client"></a><span data-ttu-id="5b2c2-106">Client JavaScript</span><span class="sxs-lookup"><span data-stu-id="5b2c2-106">JavaScript client</span></span>

<span data-ttu-id="5b2c2-107">Le [client JavaScript](xref:signalr/javascript-client) s’exécute sur NodeJS 8 et versions ultérieures, ainsi que sur les navigateurs suivants :</span><span class="sxs-lookup"><span data-stu-id="5b2c2-107">The [JavaScript client](xref:signalr/javascript-client) runs on NodeJS 8 and later versions and the following browsers:</span></span>

| <span data-ttu-id="5b2c2-108">Browser</span><span class="sxs-lookup"><span data-stu-id="5b2c2-108">Browser</span></span>                         | <span data-ttu-id="5b2c2-109">Version</span><span class="sxs-lookup"><span data-stu-id="5b2c2-109">Version</span></span>         |
| ------------------------------- | --------------- |
| <span data-ttu-id="5b2c2-110">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="5b2c2-110">Microsoft Edge</span></span>                  | <span data-ttu-id="5b2c2-111">Actif&dagger;</span><span class="sxs-lookup"><span data-stu-id="5b2c2-111">Current&dagger;</span></span> |
| <span data-ttu-id="5b2c2-112">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="5b2c2-112">Mozilla Firefox</span></span>                 | <span data-ttu-id="5b2c2-113">Actif&dagger;</span><span class="sxs-lookup"><span data-stu-id="5b2c2-113">Current&dagger;</span></span> |
| <span data-ttu-id="5b2c2-114">Google Chrome ; comprend Android</span><span class="sxs-lookup"><span data-stu-id="5b2c2-114">Google Chrome; includes Android</span></span> | <span data-ttu-id="5b2c2-115">Actif&dagger;</span><span class="sxs-lookup"><span data-stu-id="5b2c2-115">Current&dagger;</span></span> |
| <span data-ttu-id="5b2c2-116">Safari comprend iOS</span><span class="sxs-lookup"><span data-stu-id="5b2c2-116">Safari; includes iOS</span></span>            | <span data-ttu-id="5b2c2-117">Actif&dagger;</span><span class="sxs-lookup"><span data-stu-id="5b2c2-117">Current&dagger;</span></span> |
| <span data-ttu-id="5b2c2-118">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="5b2c2-118">Microsoft Internet Explorer</span></span>     | <span data-ttu-id="5b2c2-119">11</span><span class="sxs-lookup"><span data-stu-id="5b2c2-119">11</span></span>              |

<span data-ttu-id="5b2c2-120">&dagger;*Current* fait référence à la dernière version du navigateur.</span><span class="sxs-lookup"><span data-stu-id="5b2c2-120">&dagger;*Current* refers to the latest version of the browser.</span></span>

## <a name="net-client"></a><span data-ttu-id="5b2c2-121">Client .NET</span><span class="sxs-lookup"><span data-stu-id="5b2c2-121">.NET client</span></span>

<span data-ttu-id="5b2c2-122">Le [client .net](xref:signalr/dotnet-client) s’exécute sur n’importe quelle plateforme prise en charge par ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="5b2c2-122">The [.NET client](xref:signalr/dotnet-client) runs on any platform supported by ASP.NET Core.</span></span> <span data-ttu-id="5b2c2-123">Par exemple, [les développeurs Xamarin peuvent SignalR utiliser](https://github.com/aspnet/Announcements/issues/305) pour créer des applications Android à l’aide de Xamarin. Android 8.4.0.1 et versions ultérieures et des applications iOS à l’aide de Xamarin. iOS 11.14.0.4 et versions ultérieures.</span><span class="sxs-lookup"><span data-stu-id="5b2c2-123">For example, [Xamarin developers can use SignalR](https://github.com/aspnet/Announcements/issues/305) for building Android apps using Xamarin.Android 8.4.0.1 and later and iOS apps using Xamarin.iOS 11.14.0.4 and later.</span></span>

<span data-ttu-id="5b2c2-124">Si le serveur exécute IIS, le transport WebSockets requiert IIS 8,0 ou une version ultérieure sur Windows Server 2012 ou version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="5b2c2-124">If the server runs IIS, the WebSockets transport requires IIS 8.0 or later on Windows Server 2012 or later.</span></span> <span data-ttu-id="5b2c2-125">D’autres transports sont pris en charge sur toutes les plateformes.</span><span class="sxs-lookup"><span data-stu-id="5b2c2-125">Other transports are supported on all platforms.</span></span>

## <a name="java-client"></a><span data-ttu-id="5b2c2-126">Client Java</span><span class="sxs-lookup"><span data-stu-id="5b2c2-126">Java client</span></span>

<span data-ttu-id="5b2c2-127">Le [client Java](xref:signalr/java-client) prend en charge Java 8 et versions ultérieures.</span><span class="sxs-lookup"><span data-stu-id="5b2c2-127">The [Java client](xref:signalr/java-client) supports Java 8 and later versions.</span></span>

## <a name="unsupported-clients"></a><span data-ttu-id="5b2c2-128">Clients non pris en charge</span><span class="sxs-lookup"><span data-stu-id="5b2c2-128">Unsupported clients</span></span>

<span data-ttu-id="5b2c2-129">Les clients suivants sont disponibles, mais sont expérimentaux ou non officiels.</span><span class="sxs-lookup"><span data-stu-id="5b2c2-129">The following clients are available but are experimental or unofficial.</span></span> <span data-ttu-id="5b2c2-130">Ils ne sont actuellement pas pris en charge et peuvent ne jamais être.</span><span class="sxs-lookup"><span data-stu-id="5b2c2-130">They aren't currently supported and may never be.</span></span>

* <span data-ttu-id="5b2c2-131">[Client C++](https://github.com/aspnet/SignalR-Client-Cpp)</span><span class="sxs-lookup"><span data-stu-id="5b2c2-131">[C++ client](https://github.com/aspnet/SignalR-Client-Cpp)</span></span>

* <span data-ttu-id="5b2c2-132">[Client SWIFT](https://github.com/moozzyk/SignalR-Client-Swift)</span><span class="sxs-lookup"><span data-stu-id="5b2c2-132">[Swift client](https://github.com/moozzyk/SignalR-Client-Swift)</span></span>
