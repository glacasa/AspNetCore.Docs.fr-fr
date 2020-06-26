---
title: BlazorPlateformes prises en charge ASP.net Core
author: guardrex
description: En savoir plus sur les plateformes prises en charge pour ASP.NET Core Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/supported-platforms
ms.openlocfilehash: adf27fa84acb3929a1639b561c728c2db29723f6
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85401933"
---
# <a name="aspnet-core-blazor-supported-platforms"></a><span data-ttu-id="27f1a-103">BlazorPlateformes prises en charge ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="27f1a-103">ASP.NET Core Blazor supported platforms</span></span>

<span data-ttu-id="27f1a-104">Par [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="27f1a-104">By [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="browser-requirements"></a><span data-ttu-id="27f1a-105">Configuration requise des navigateurs</span><span class="sxs-lookup"><span data-stu-id="27f1a-105">Browser requirements</span></span>

### Blazor WebAssembly

| <span data-ttu-id="27f1a-106">Navigateur</span><span class="sxs-lookup"><span data-stu-id="27f1a-106">Browser</span></span>                          | <span data-ttu-id="27f1a-107">Version</span><span class="sxs-lookup"><span data-stu-id="27f1a-107">Version</span></span>               |
| -------------------------------- | :-------------------: |
| <span data-ttu-id="27f1a-108">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="27f1a-108">Microsoft Edge</span></span>                   | <span data-ttu-id="27f1a-109">Actuel</span><span class="sxs-lookup"><span data-stu-id="27f1a-109">Current</span></span>               |
| <span data-ttu-id="27f1a-110">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="27f1a-110">Mozilla Firefox</span></span>                  | <span data-ttu-id="27f1a-111">Actuel</span><span class="sxs-lookup"><span data-stu-id="27f1a-111">Current</span></span>               |
| <span data-ttu-id="27f1a-112">Google Chrome, y compris Android</span><span class="sxs-lookup"><span data-stu-id="27f1a-112">Google Chrome, including Android</span></span> | <span data-ttu-id="27f1a-113">Actuel</span><span class="sxs-lookup"><span data-stu-id="27f1a-113">Current</span></span>               |
| <span data-ttu-id="27f1a-114">Safari, y compris iOS</span><span class="sxs-lookup"><span data-stu-id="27f1a-114">Safari, including iOS</span></span>            | <span data-ttu-id="27f1a-115">Actuel</span><span class="sxs-lookup"><span data-stu-id="27f1a-115">Current</span></span>               |
| <span data-ttu-id="27f1a-116">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="27f1a-116">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="27f1a-117">Non pris en charge&dagger;</span><span class="sxs-lookup"><span data-stu-id="27f1a-117">Not Supported&dagger;</span></span> |

<span data-ttu-id="27f1a-118">&dagger;Microsoft Internet Explorer ne prend pas en charge [Webassembly](https://webassembly.org).</span><span class="sxs-lookup"><span data-stu-id="27f1a-118">&dagger;Microsoft Internet Explorer doesn't support [WebAssembly](https://webassembly.org).</span></span>

### Blazor Server

| <span data-ttu-id="27f1a-119">Navigateur</span><span class="sxs-lookup"><span data-stu-id="27f1a-119">Browser</span></span>                          | <span data-ttu-id="27f1a-120">Version</span><span class="sxs-lookup"><span data-stu-id="27f1a-120">Version</span></span>    |
| -------------------------------- | :--------: |
| <span data-ttu-id="27f1a-121">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="27f1a-121">Microsoft Edge</span></span>                   | <span data-ttu-id="27f1a-122">Actuel</span><span class="sxs-lookup"><span data-stu-id="27f1a-122">Current</span></span>    |
| <span data-ttu-id="27f1a-123">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="27f1a-123">Mozilla Firefox</span></span>                  | <span data-ttu-id="27f1a-124">Actuel</span><span class="sxs-lookup"><span data-stu-id="27f1a-124">Current</span></span>    |
| <span data-ttu-id="27f1a-125">Google Chrome, y compris Android</span><span class="sxs-lookup"><span data-stu-id="27f1a-125">Google Chrome, including Android</span></span> | <span data-ttu-id="27f1a-126">Actuel</span><span class="sxs-lookup"><span data-stu-id="27f1a-126">Current</span></span>    |
| <span data-ttu-id="27f1a-127">Safari, y compris iOS</span><span class="sxs-lookup"><span data-stu-id="27f1a-127">Safari, including iOS</span></span>            | <span data-ttu-id="27f1a-128">Actuel</span><span class="sxs-lookup"><span data-stu-id="27f1a-128">Current</span></span>    |
| <span data-ttu-id="27f1a-129">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="27f1a-129">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="27f1a-130">11&dagger;</span><span class="sxs-lookup"><span data-stu-id="27f1a-130">11&dagger;</span></span> |

<span data-ttu-id="27f1a-131">&dagger;Des polyremplissages supplémentaires sont nécessaires (par exemple, des promesses peuvent être ajoutées via un [`Polyfill.io`](https://polyfill.io/v3/) Bundle).</span><span class="sxs-lookup"><span data-stu-id="27f1a-131">&dagger;Additional polyfills are required (for example, promises can be added via a [`Polyfill.io`](https://polyfill.io/v3/) bundle).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="27f1a-132">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="27f1a-132">Additional resources</span></span>

* <xref:blazor/hosting-models>
