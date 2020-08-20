---
title: BlazorPlateformes prises en charge ASP.net Core
author: guardrex
description: En savoir plus sur les plateformes prises en charge pour ASP.NET Core Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
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
uid: blazor/supported-platforms
ms.openlocfilehash: 692ab63bb48dbfa29021d59cdf035e9549d3039c
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88625945"
---
# <a name="aspnet-core-no-locblazor-supported-platforms"></a><span data-ttu-id="29793-103">BlazorPlateformes prises en charge ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="29793-103">ASP.NET Core Blazor supported platforms</span></span>

<span data-ttu-id="29793-104">Par [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="29793-104">By [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="browser-requirements"></a><span data-ttu-id="29793-105">Configuration requise des navigateurs</span><span class="sxs-lookup"><span data-stu-id="29793-105">Browser requirements</span></span>

### Blazor WebAssembly

| <span data-ttu-id="29793-106">Browser</span><span class="sxs-lookup"><span data-stu-id="29793-106">Browser</span></span>                          | <span data-ttu-id="29793-107">Version</span><span class="sxs-lookup"><span data-stu-id="29793-107">Version</span></span>               |
| -------------------------------- | :-------------------: |
| <span data-ttu-id="29793-108">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="29793-108">Microsoft Edge</span></span>                   | <span data-ttu-id="29793-109">Current</span><span class="sxs-lookup"><span data-stu-id="29793-109">Current</span></span>               |
| <span data-ttu-id="29793-110">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="29793-110">Mozilla Firefox</span></span>                  | <span data-ttu-id="29793-111">Current</span><span class="sxs-lookup"><span data-stu-id="29793-111">Current</span></span>               |
| <span data-ttu-id="29793-112">Google Chrome, y compris Android</span><span class="sxs-lookup"><span data-stu-id="29793-112">Google Chrome, including Android</span></span> | <span data-ttu-id="29793-113">Current</span><span class="sxs-lookup"><span data-stu-id="29793-113">Current</span></span>               |
| <span data-ttu-id="29793-114">Safari, y compris iOS</span><span class="sxs-lookup"><span data-stu-id="29793-114">Safari, including iOS</span></span>            | <span data-ttu-id="29793-115">Current</span><span class="sxs-lookup"><span data-stu-id="29793-115">Current</span></span>               |
| <span data-ttu-id="29793-116">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="29793-116">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="29793-117">Non pris en charge&dagger;</span><span class="sxs-lookup"><span data-stu-id="29793-117">Not Supported&dagger;</span></span> |

<span data-ttu-id="29793-118">&dagger;Microsoft Internet Explorer ne prend pas en charge [Webassembly](https://webassembly.org).</span><span class="sxs-lookup"><span data-stu-id="29793-118">&dagger;Microsoft Internet Explorer doesn't support [WebAssembly](https://webassembly.org).</span></span>

### Blazor Server

| <span data-ttu-id="29793-119">Browser</span><span class="sxs-lookup"><span data-stu-id="29793-119">Browser</span></span>                          | <span data-ttu-id="29793-120">Version</span><span class="sxs-lookup"><span data-stu-id="29793-120">Version</span></span>    |
| -------------------------------- | :--------: |
| <span data-ttu-id="29793-121">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="29793-121">Microsoft Edge</span></span>                   | <span data-ttu-id="29793-122">Current</span><span class="sxs-lookup"><span data-stu-id="29793-122">Current</span></span>    |
| <span data-ttu-id="29793-123">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="29793-123">Mozilla Firefox</span></span>                  | <span data-ttu-id="29793-124">Current</span><span class="sxs-lookup"><span data-stu-id="29793-124">Current</span></span>    |
| <span data-ttu-id="29793-125">Google Chrome, y compris Android</span><span class="sxs-lookup"><span data-stu-id="29793-125">Google Chrome, including Android</span></span> | <span data-ttu-id="29793-126">Current</span><span class="sxs-lookup"><span data-stu-id="29793-126">Current</span></span>    |
| <span data-ttu-id="29793-127">Safari, y compris iOS</span><span class="sxs-lookup"><span data-stu-id="29793-127">Safari, including iOS</span></span>            | <span data-ttu-id="29793-128">Current</span><span class="sxs-lookup"><span data-stu-id="29793-128">Current</span></span>    |
| <span data-ttu-id="29793-129">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="29793-129">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="29793-130">11&dagger;</span><span class="sxs-lookup"><span data-stu-id="29793-130">11&dagger;</span></span> |

<span data-ttu-id="29793-131">&dagger;Des polyremplissages supplémentaires sont nécessaires (par exemple, des promesses peuvent être ajoutées via un [`Polyfill.io`](https://polyfill.io/v3/) Bundle).</span><span class="sxs-lookup"><span data-stu-id="29793-131">&dagger;Additional polyfills are required (for example, promises can be added via a [`Polyfill.io`](https://polyfill.io/v3/) bundle).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="29793-132">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="29793-132">Additional resources</span></span>

* <xref:blazor/hosting-models>
