---
title: BlazorPlateformes prises en charge ASP.net Core
author: guardrex
description: En savoir plus sur les plateformes prises en charge pour ASP.NET Core Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/supported-platforms
ms.openlocfilehash: 67896bcdd5d99ff2c9cf22e3902262f9513eb4f6
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88013526"
---
# <a name="aspnet-core-no-locblazor-supported-platforms"></a><span data-ttu-id="7c813-103">BlazorPlateformes prises en charge ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="7c813-103">ASP.NET Core Blazor supported platforms</span></span>

<span data-ttu-id="7c813-104">Par [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="7c813-104">By [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="browser-requirements"></a><span data-ttu-id="7c813-105">Configuration requise des navigateurs</span><span class="sxs-lookup"><span data-stu-id="7c813-105">Browser requirements</span></span>

### Blazor WebAssembly

| <span data-ttu-id="7c813-106">Browser</span><span class="sxs-lookup"><span data-stu-id="7c813-106">Browser</span></span>                          | <span data-ttu-id="7c813-107">Version</span><span class="sxs-lookup"><span data-stu-id="7c813-107">Version</span></span>               |
| -------------------------------- | :-------------------: |
| <span data-ttu-id="7c813-108">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="7c813-108">Microsoft Edge</span></span>                   | <span data-ttu-id="7c813-109">Current</span><span class="sxs-lookup"><span data-stu-id="7c813-109">Current</span></span>               |
| <span data-ttu-id="7c813-110">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="7c813-110">Mozilla Firefox</span></span>                  | <span data-ttu-id="7c813-111">Current</span><span class="sxs-lookup"><span data-stu-id="7c813-111">Current</span></span>               |
| <span data-ttu-id="7c813-112">Google Chrome, y compris Android</span><span class="sxs-lookup"><span data-stu-id="7c813-112">Google Chrome, including Android</span></span> | <span data-ttu-id="7c813-113">Current</span><span class="sxs-lookup"><span data-stu-id="7c813-113">Current</span></span>               |
| <span data-ttu-id="7c813-114">Safari, y compris iOS</span><span class="sxs-lookup"><span data-stu-id="7c813-114">Safari, including iOS</span></span>            | <span data-ttu-id="7c813-115">Current</span><span class="sxs-lookup"><span data-stu-id="7c813-115">Current</span></span>               |
| <span data-ttu-id="7c813-116">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="7c813-116">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="7c813-117">Non pris en charge&dagger;</span><span class="sxs-lookup"><span data-stu-id="7c813-117">Not Supported&dagger;</span></span> |

<span data-ttu-id="7c813-118">&dagger;Microsoft Internet Explorer ne prend pas en charge [Webassembly](https://webassembly.org).</span><span class="sxs-lookup"><span data-stu-id="7c813-118">&dagger;Microsoft Internet Explorer doesn't support [WebAssembly](https://webassembly.org).</span></span>

### Blazor Server

| <span data-ttu-id="7c813-119">Browser</span><span class="sxs-lookup"><span data-stu-id="7c813-119">Browser</span></span>                          | <span data-ttu-id="7c813-120">Version</span><span class="sxs-lookup"><span data-stu-id="7c813-120">Version</span></span>    |
| -------------------------------- | :--------: |
| <span data-ttu-id="7c813-121">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="7c813-121">Microsoft Edge</span></span>                   | <span data-ttu-id="7c813-122">Current</span><span class="sxs-lookup"><span data-stu-id="7c813-122">Current</span></span>    |
| <span data-ttu-id="7c813-123">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="7c813-123">Mozilla Firefox</span></span>                  | <span data-ttu-id="7c813-124">Current</span><span class="sxs-lookup"><span data-stu-id="7c813-124">Current</span></span>    |
| <span data-ttu-id="7c813-125">Google Chrome, y compris Android</span><span class="sxs-lookup"><span data-stu-id="7c813-125">Google Chrome, including Android</span></span> | <span data-ttu-id="7c813-126">Current</span><span class="sxs-lookup"><span data-stu-id="7c813-126">Current</span></span>    |
| <span data-ttu-id="7c813-127">Safari, y compris iOS</span><span class="sxs-lookup"><span data-stu-id="7c813-127">Safari, including iOS</span></span>            | <span data-ttu-id="7c813-128">Current</span><span class="sxs-lookup"><span data-stu-id="7c813-128">Current</span></span>    |
| <span data-ttu-id="7c813-129">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="7c813-129">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="7c813-130">11&dagger;</span><span class="sxs-lookup"><span data-stu-id="7c813-130">11&dagger;</span></span> |

<span data-ttu-id="7c813-131">&dagger;Des polyremplissages supplémentaires sont nécessaires (par exemple, des promesses peuvent être ajoutées via un [`Polyfill.io`](https://polyfill.io/v3/) Bundle).</span><span class="sxs-lookup"><span data-stu-id="7c813-131">&dagger;Additional polyfills are required (for example, promises can be added via a [`Polyfill.io`](https://polyfill.io/v3/) bundle).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7c813-132">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="7c813-132">Additional resources</span></span>

* <xref:blazor/hosting-models>
