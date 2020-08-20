---
title: SignalRPlateformes prises en charge ASP.net Core
author: bradygaster
description: En savoir plus sur les plateformes prises en charge pour ASP.NET Core SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/16/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/supported-platforms
ms.openlocfilehash: 91fd2553803d855b338b1d1b46d55e1d1e4cc21e
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88635149"
---
# <a name="aspnet-core-no-locsignalr-supported-platforms"></a><span data-ttu-id="b6b63-103">SignalRPlateformes prises en charge ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="b6b63-103">ASP.NET Core SignalR supported platforms</span></span>

## <a name="server-system-requirements"></a><span data-ttu-id="b6b63-104">Configuration requise pour le serveur</span><span class="sxs-lookup"><span data-stu-id="b6b63-104">Server system requirements</span></span>

<span data-ttu-id="b6b63-105">SignalR pour ASP.NET Core prend en charge toutes les plateformes de serveur prises en charge par ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b6b63-105">SignalR for ASP.NET Core supports any server platform that ASP.NET Core supports.</span></span>

## <a name="javascript-client"></a><span data-ttu-id="b6b63-106">Client JavaScript</span><span class="sxs-lookup"><span data-stu-id="b6b63-106">JavaScript client</span></span>

<span data-ttu-id="b6b63-107">Le [client JavaScript](xref:signalr/javascript-client) s’exécute sur NodeJS 8 et versions ultérieures, ainsi que sur les navigateurs suivants :</span><span class="sxs-lookup"><span data-stu-id="b6b63-107">The [JavaScript client](xref:signalr/javascript-client) runs on NodeJS 8 and later versions and the following browsers:</span></span>

| <span data-ttu-id="b6b63-108">Browser</span><span class="sxs-lookup"><span data-stu-id="b6b63-108">Browser</span></span>                         | <span data-ttu-id="b6b63-109">Version</span><span class="sxs-lookup"><span data-stu-id="b6b63-109">Version</span></span>         |
| ------------------------------- | --------------- |
| <span data-ttu-id="b6b63-110">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="b6b63-110">Microsoft Edge</span></span>                  | <span data-ttu-id="b6b63-111">Actif&dagger;</span><span class="sxs-lookup"><span data-stu-id="b6b63-111">Current&dagger;</span></span> |
| <span data-ttu-id="b6b63-112">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="b6b63-112">Mozilla Firefox</span></span>                 | <span data-ttu-id="b6b63-113">Actif&dagger;</span><span class="sxs-lookup"><span data-stu-id="b6b63-113">Current&dagger;</span></span> |
| <span data-ttu-id="b6b63-114">Google Chrome ; comprend Android</span><span class="sxs-lookup"><span data-stu-id="b6b63-114">Google Chrome; includes Android</span></span> | <span data-ttu-id="b6b63-115">Actif&dagger;</span><span class="sxs-lookup"><span data-stu-id="b6b63-115">Current&dagger;</span></span> |
| <span data-ttu-id="b6b63-116">Safari comprend iOS</span><span class="sxs-lookup"><span data-stu-id="b6b63-116">Safari; includes iOS</span></span>            | <span data-ttu-id="b6b63-117">Actif&dagger;</span><span class="sxs-lookup"><span data-stu-id="b6b63-117">Current&dagger;</span></span> |
| <span data-ttu-id="b6b63-118">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="b6b63-118">Microsoft Internet Explorer</span></span>     | <span data-ttu-id="b6b63-119">11</span><span class="sxs-lookup"><span data-stu-id="b6b63-119">11</span></span>              |

<span data-ttu-id="b6b63-120">&dagger;*Current* fait référence à la dernière version du navigateur.</span><span class="sxs-lookup"><span data-stu-id="b6b63-120">&dagger;*Current* refers to the latest version of the browser.</span></span>

## <a name="net-client"></a><span data-ttu-id="b6b63-121">Client .NET</span><span class="sxs-lookup"><span data-stu-id="b6b63-121">.NET client</span></span>

<span data-ttu-id="b6b63-122">Le [client .net](xref:signalr/dotnet-client) s’exécute sur n’importe quelle plateforme prise en charge par ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="b6b63-122">The [.NET client](xref:signalr/dotnet-client) runs on any platform supported by ASP.NET Core.</span></span> <span data-ttu-id="b6b63-123">Par exemple, [les développeurs Xamarin peuvent SignalR utiliser](https://github.com/aspnet/Announcements/issues/305) pour créer des applications Android à l’aide de Xamarin. Android 8.4.0.1 et versions ultérieures et des applications iOS à l’aide de Xamarin. iOS 11.14.0.4 et versions ultérieures.</span><span class="sxs-lookup"><span data-stu-id="b6b63-123">For example, [Xamarin developers can use SignalR](https://github.com/aspnet/Announcements/issues/305) for building Android apps using Xamarin.Android 8.4.0.1 and later and iOS apps using Xamarin.iOS 11.14.0.4 and later.</span></span>

<span data-ttu-id="b6b63-124">Si le serveur exécute IIS, le transport WebSockets requiert IIS 8,0 ou une version ultérieure sur Windows Server 2012 ou version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="b6b63-124">If the server runs IIS, the WebSockets transport requires IIS 8.0 or later on Windows Server 2012 or later.</span></span> <span data-ttu-id="b6b63-125">D’autres transports sont pris en charge sur toutes les plateformes.</span><span class="sxs-lookup"><span data-stu-id="b6b63-125">Other transports are supported on all platforms.</span></span>

## <a name="java-client"></a><span data-ttu-id="b6b63-126">Client Java</span><span class="sxs-lookup"><span data-stu-id="b6b63-126">Java client</span></span>

<span data-ttu-id="b6b63-127">Le [client Java](xref:signalr/java-client) prend en charge Java 8 et versions ultérieures.</span><span class="sxs-lookup"><span data-stu-id="b6b63-127">The [Java client](xref:signalr/java-client) supports Java 8 and later versions.</span></span>

## <a name="unsupported-clients"></a><span data-ttu-id="b6b63-128">Clients non pris en charge</span><span class="sxs-lookup"><span data-stu-id="b6b63-128">Unsupported clients</span></span>

<span data-ttu-id="b6b63-129">Les clients suivants sont disponibles, mais sont expérimentaux ou non officiels.</span><span class="sxs-lookup"><span data-stu-id="b6b63-129">The following clients are available but are experimental or unofficial.</span></span> <span data-ttu-id="b6b63-130">Ils ne sont actuellement pas pris en charge et peuvent ne jamais être.</span><span class="sxs-lookup"><span data-stu-id="b6b63-130">They aren't currently supported and may never be.</span></span>

* <span data-ttu-id="b6b63-131">[Client C++](https://github.com/aspnet/SignalR-Client-Cpp)</span><span class="sxs-lookup"><span data-stu-id="b6b63-131">[C++ client](https://github.com/aspnet/SignalR-Client-Cpp)</span></span>

* <span data-ttu-id="b6b63-132">[Client SWIFT](https://github.com/moozzyk/SignalR-Client-Swift)</span><span class="sxs-lookup"><span data-stu-id="b6b63-132">[Swift client](https://github.com/moozzyk/SignalR-Client-Swift)</span></span>
