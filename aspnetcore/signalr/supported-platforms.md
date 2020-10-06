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
ms.openlocfilehash: a342dd787eceadd22ac26b57a3615a6b0b21f461
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91754500"
---
# <a name="aspnet-core-no-locsignalr-supported-platforms"></a><span data-ttu-id="c585d-103">SignalRPlateformes prises en charge ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="c585d-103">ASP.NET Core SignalR supported platforms</span></span>

## <a name="server-system-requirements"></a><span data-ttu-id="c585d-104">Configuration requise pour le serveur</span><span class="sxs-lookup"><span data-stu-id="c585d-104">Server system requirements</span></span>

<span data-ttu-id="c585d-105">SignalR pour ASP.NET Core prend en charge toutes les plateformes de serveur prises en charge par ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c585d-105">SignalR for ASP.NET Core supports any server platform that ASP.NET Core supports.</span></span>

## <a name="javascript-client"></a><span data-ttu-id="c585d-106">Client JavaScript</span><span class="sxs-lookup"><span data-stu-id="c585d-106">JavaScript client</span></span>

<span data-ttu-id="c585d-107">Le [client JavaScript](xref:signalr/javascript-client) s’exécute sur NodeJS 8 et versions ultérieures, ainsi que sur les navigateurs suivants :</span><span class="sxs-lookup"><span data-stu-id="c585d-107">The [JavaScript client](xref:signalr/javascript-client) runs on NodeJS 8 and later versions and the following browsers:</span></span>

| <span data-ttu-id="c585d-108">Navigateur</span><span class="sxs-lookup"><span data-stu-id="c585d-108">Browser</span></span>                          | <span data-ttu-id="c585d-109">Version</span><span class="sxs-lookup"><span data-stu-id="c585d-109">Version</span></span>         |
| -------------------------------- | --------------- |
| <span data-ttu-id="c585d-110">Apple Safari, y compris iOS</span><span class="sxs-lookup"><span data-stu-id="c585d-110">Apple Safari, including iOS</span></span>      | <span data-ttu-id="c585d-111">Actif&dagger;</span><span class="sxs-lookup"><span data-stu-id="c585d-111">Current&dagger;</span></span> |
| <span data-ttu-id="c585d-112">Google Chrome, y compris Android</span><span class="sxs-lookup"><span data-stu-id="c585d-112">Google Chrome, including Android</span></span> | <span data-ttu-id="c585d-113">Actif&dagger;</span><span class="sxs-lookup"><span data-stu-id="c585d-113">Current&dagger;</span></span> |
| <span data-ttu-id="c585d-114">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="c585d-114">Microsoft Edge</span></span>                   | <span data-ttu-id="c585d-115">Actif&dagger;</span><span class="sxs-lookup"><span data-stu-id="c585d-115">Current&dagger;</span></span> |
| <span data-ttu-id="c585d-116">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="c585d-116">Mozilla Firefox</span></span>                  | <span data-ttu-id="c585d-117">Actif&dagger;</span><span class="sxs-lookup"><span data-stu-id="c585d-117">Current&dagger;</span></span> |

<span data-ttu-id="c585d-118">&dagger;*Current* fait référence à la dernière version du navigateur.</span><span class="sxs-lookup"><span data-stu-id="c585d-118">&dagger;*Current* refers to the latest version of the browser.</span></span>

## <a name="net-client"></a><span data-ttu-id="c585d-119">Client .NET</span><span class="sxs-lookup"><span data-stu-id="c585d-119">.NET client</span></span>

<span data-ttu-id="c585d-120">Le [client .net](xref:signalr/dotnet-client) s’exécute sur n’importe quelle plateforme prise en charge par ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="c585d-120">The [.NET client](xref:signalr/dotnet-client) runs on any platform supported by ASP.NET Core.</span></span> <span data-ttu-id="c585d-121">Par exemple, [les développeurs Xamarin peuvent SignalR utiliser](https://github.com/aspnet/Announcements/issues/305) pour créer des applications Android à l’aide de Xamarin. Android 8.4.0.1 et versions ultérieures et des applications iOS à l’aide de Xamarin. iOS 11.14.0.4 et versions ultérieures.</span><span class="sxs-lookup"><span data-stu-id="c585d-121">For example, [Xamarin developers can use SignalR](https://github.com/aspnet/Announcements/issues/305) for building Android apps using Xamarin.Android 8.4.0.1 and later and iOS apps using Xamarin.iOS 11.14.0.4 and later.</span></span>

<span data-ttu-id="c585d-122">Si le serveur exécute IIS, le transport WebSockets requiert IIS 8,0 ou une version ultérieure sur Windows Server 2012 ou version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="c585d-122">If the server runs IIS, the WebSockets transport requires IIS 8.0 or later on Windows Server 2012 or later.</span></span> <span data-ttu-id="c585d-123">D’autres transports sont pris en charge sur toutes les plateformes.</span><span class="sxs-lookup"><span data-stu-id="c585d-123">Other transports are supported on all platforms.</span></span>

## <a name="java-client"></a><span data-ttu-id="c585d-124">Client Java</span><span class="sxs-lookup"><span data-stu-id="c585d-124">Java client</span></span>

<span data-ttu-id="c585d-125">Le [client Java](xref:signalr/java-client) prend en charge Java 8 et versions ultérieures.</span><span class="sxs-lookup"><span data-stu-id="c585d-125">The [Java client](xref:signalr/java-client) supports Java 8 and later versions.</span></span>

## <a name="unsupported-clients"></a><span data-ttu-id="c585d-126">Clients non pris en charge</span><span class="sxs-lookup"><span data-stu-id="c585d-126">Unsupported clients</span></span>

<span data-ttu-id="c585d-127">Les clients suivants sont disponibles, mais sont expérimentaux ou non officiels.</span><span class="sxs-lookup"><span data-stu-id="c585d-127">The following clients are available but are experimental or unofficial.</span></span> <span data-ttu-id="c585d-128">Ils ne sont actuellement pas pris en charge et peuvent ne jamais être.</span><span class="sxs-lookup"><span data-stu-id="c585d-128">They aren't currently supported and may never be.</span></span>

* <span data-ttu-id="c585d-129">[Client C++](https://github.com/aspnet/SignalR-Client-Cpp)</span><span class="sxs-lookup"><span data-stu-id="c585d-129">[C++ client](https://github.com/aspnet/SignalR-Client-Cpp)</span></span>

* <span data-ttu-id="c585d-130">[Client SWIFT](https://github.com/moozzyk/SignalR-Client-Swift)</span><span class="sxs-lookup"><span data-stu-id="c585d-130">[Swift client](https://github.com/moozzyk/SignalR-Client-Swift)</span></span>
