---
title: Exemples d’authentification pour ASP.NET Core
author: rick-anderson
description: Fournit des liens vers les exemples d’authentification dans le référentiel ASP.NET Core.
ms.author: riande
ms.date: 01/31/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/samples
ms.openlocfilehash: 95915c28b132a1a48fb772b2663794754006627f
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85405352"
---
# <a name="authentication-samples-for-aspnet-core"></a><span data-ttu-id="a9199-103">Exemples d’authentification pour ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a9199-103">Authentication samples for ASP.NET Core</span></span>

<span data-ttu-id="a9199-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="a9199-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a9199-105">Le [référentiel ASP.net Core](https://github.com/dotnet/AspNetCore) contient les exemples d’authentification suivants dans le dossier *AspNetCore/SRC/Security/Samples* :</span><span class="sxs-lookup"><span data-stu-id="a9199-105">The [ASP.NET Core repository](https://github.com/dotnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="a9199-106">Transformation de revendications</span><span class="sxs-lookup"><span data-stu-id="a9199-106">Claims transformation</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/ClaimsTransformation)
* [<span data-ttu-id="a9199-107">Authentification par cookie</span><span class="sxs-lookup"><span data-stu-id="a9199-107">Cookie authentication</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Cookies)
* [<span data-ttu-id="a9199-108">Fournisseur de stratégie personnalisée-IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="a9199-108">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="a9199-109">Options et schémas d’authentification dynamique</span><span class="sxs-lookup"><span data-stu-id="a9199-109">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/DynamicSchemes)
* <span data-ttu-id="a9199-110">[Revendications externes](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Identity.ExternalClaims)</span><span class="sxs-lookup"><span data-stu-id="a9199-110">[External claims](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Identity.ExternalClaims)</span></span>
* [<span data-ttu-id="a9199-111">Sélection entre un cookie et un autre schéma d’authentification en fonction de la demande</span><span class="sxs-lookup"><span data-stu-id="a9199-111">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="a9199-112">Restreint l’accès aux fichiers statiques</span><span class="sxs-lookup"><span data-stu-id="a9199-112">Restricts access to static files</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="a9199-113">Exécuter des exemples</span><span class="sxs-lookup"><span data-stu-id="a9199-113">Run the samples</span></span>

* <span data-ttu-id="a9199-114">Sélectionnez une [branche](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="a9199-114">Select a [branch](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="a9199-115">Par exemple : `release/3.1`</span><span class="sxs-lookup"><span data-stu-id="a9199-115">For example, `release/3.1`</span></span>
* <span data-ttu-id="a9199-116">Clonez ou téléchargez le [référentiel ASP.net Core](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="a9199-116">Clone or download the [ASP.NET Core repository](https://github.com/dotnet/AspNetCore).</span></span>
* <span data-ttu-id="a9199-117">Vérifiez que vous avez installé la version de [Kit SDK .net Core](https://dotnet.microsoft.com/download/dotnet-core) qui correspond au clone du référentiel ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="a9199-117">Verify you have installed the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="a9199-118">Accédez à un exemple dans *AspNetCore/SRC/Security/Samples* et exécutez l’exemple avec `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="a9199-118">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a9199-119">Le [référentiel ASP.net Core](https://github.com/dotnet/AspNetCore) contient les exemples d’authentification suivants dans le dossier *AspNetCore/SRC/Security/Samples* :</span><span class="sxs-lookup"><span data-stu-id="a9199-119">The [ASP.NET Core repository](https://github.com/dotnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="a9199-120">Transformation de revendications</span><span class="sxs-lookup"><span data-stu-id="a9199-120">Claims transformation</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/ClaimsTransformation)
* [<span data-ttu-id="a9199-121">Authentification par cookie</span><span class="sxs-lookup"><span data-stu-id="a9199-121">Cookie authentication</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Cookies)
* [<span data-ttu-id="a9199-122">Fournisseur de stratégie personnalisée-IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="a9199-122">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="a9199-123">Options et schémas d’authentification dynamique</span><span class="sxs-lookup"><span data-stu-id="a9199-123">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/DynamicSchemes)
* <span data-ttu-id="a9199-124">[Revendications externes](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Identity.ExternalClaims)</span><span class="sxs-lookup"><span data-stu-id="a9199-124">[External claims](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Identity.ExternalClaims)</span></span>
* [<span data-ttu-id="a9199-125">Sélection entre un cookie et un autre schéma d’authentification en fonction de la demande</span><span class="sxs-lookup"><span data-stu-id="a9199-125">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="a9199-126">Restreint l’accès aux fichiers statiques</span><span class="sxs-lookup"><span data-stu-id="a9199-126">Restricts access to static files</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="a9199-127">Exécuter des exemples</span><span class="sxs-lookup"><span data-stu-id="a9199-127">Run the samples</span></span>

* <span data-ttu-id="a9199-128">Sélectionnez une [branche](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="a9199-128">Select a [branch](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="a9199-129">Par exemple : `release/2.2`</span><span class="sxs-lookup"><span data-stu-id="a9199-129">For example, `release/2.2`</span></span>
* <span data-ttu-id="a9199-130">Clonez ou téléchargez le [référentiel ASP.net Core](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="a9199-130">Clone or download the [ASP.NET Core repository](https://github.com/dotnet/AspNetCore).</span></span>
* <span data-ttu-id="a9199-131">Vérifiez que vous avez installé la version de [Kit SDK .net Core](https://dotnet.microsoft.com/download/dotnet-core) qui correspond au clone du référentiel ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="a9199-131">Verify you have installed the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="a9199-132">Accédez à un exemple dans *AspNetCore/SRC/Security/Samples* et exécutez l’exemple avec `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="a9199-132">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end
