---
title: Échantillons d’authentification pour ASP.NET Core
author: rick-anderson
description: Fournit des liens vers les échantillons d’authentification dans le référentiel ASP.NET Core.
ms.author: riande
ms.date: 01/31/2019
uid: security/authentication/samples
ms.openlocfilehash: b33eaa5c1bda9e23b2815cd1663e02ae06fec856
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81308213"
---
# <a name="authentication-samples-for-aspnet-core"></a><span data-ttu-id="d5919-103">Échantillons d’authentification pour ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d5919-103">Authentication samples for ASP.NET Core</span></span>

<span data-ttu-id="d5919-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d5919-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d5919-105">Le [dépôt ASP.NET Core](https://github.com/dotnet/AspNetCore) contient les échantillons d’authentification suivants dans le dossier *AspNetCore/src/Security/samples* :</span><span class="sxs-lookup"><span data-stu-id="d5919-105">The [ASP.NET Core repository](https://github.com/dotnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="d5919-106">Transformation de revendications</span><span class="sxs-lookup"><span data-stu-id="d5919-106">Claims transformation</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/ClaimsTransformation)
* [<span data-ttu-id="d5919-107">Authentification de cookie</span><span class="sxs-lookup"><span data-stu-id="d5919-107">Cookie authentication</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Cookies)
* [<span data-ttu-id="d5919-108">Fournisseur de stratégie personnalisé - IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="d5919-108">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="d5919-109">Systèmes et options d’authentification dynamiques</span><span class="sxs-lookup"><span data-stu-id="d5919-109">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/DynamicSchemes)
* [<span data-ttu-id="d5919-110">Réclamations externes</span><span class="sxs-lookup"><span data-stu-id="d5919-110">External claims</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Identity.ExternalClaims)
* [<span data-ttu-id="d5919-111">Sélection entre cookie et un autre schéma d’authentification basé sur la demande</span><span class="sxs-lookup"><span data-stu-id="d5919-111">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="d5919-112">Restreint l’accès aux fichiers statiques</span><span class="sxs-lookup"><span data-stu-id="d5919-112">Restricts access to static files</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="d5919-113">Exécuter des exemples</span><span class="sxs-lookup"><span data-stu-id="d5919-113">Run the samples</span></span>

* <span data-ttu-id="d5919-114">Sélectionnez une [branche](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="d5919-114">Select a [branch](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="d5919-115">Par exemple : `release/3.1`</span><span class="sxs-lookup"><span data-stu-id="d5919-115">For example, `release/3.1`</span></span>
* <span data-ttu-id="d5919-116">Clonez ou téléchargez le [référentiel ASP.NET Core](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="d5919-116">Clone or download the [ASP.NET Core repository](https://github.com/dotnet/AspNetCore).</span></span>
* <span data-ttu-id="d5919-117">Vérifiez que vous avez installé la version [SDK core .NET](https://dotnet.microsoft.com/download/dotnet-core) correspondant au clone du référentiel ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d5919-117">Verify you have installed the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="d5919-118">Naviguez vers un échantillon dans *AspNetCore/src/Security/samples* et exécutez l’échantillon avec `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="d5919-118">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d5919-119">Le [dépôt ASP.NET Core](https://github.com/dotnet/AspNetCore) contient les échantillons d’authentification suivants dans le dossier *AspNetCore/src/Security/samples* :</span><span class="sxs-lookup"><span data-stu-id="d5919-119">The [ASP.NET Core repository](https://github.com/dotnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="d5919-120">Transformation de revendications</span><span class="sxs-lookup"><span data-stu-id="d5919-120">Claims transformation</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/ClaimsTransformation)
* [<span data-ttu-id="d5919-121">Authentification de cookie</span><span class="sxs-lookup"><span data-stu-id="d5919-121">Cookie authentication</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Cookies)
* [<span data-ttu-id="d5919-122">Fournisseur de stratégie personnalisé - IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="d5919-122">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="d5919-123">Systèmes et options d’authentification dynamiques</span><span class="sxs-lookup"><span data-stu-id="d5919-123">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/DynamicSchemes)
* [<span data-ttu-id="d5919-124">Réclamations externes</span><span class="sxs-lookup"><span data-stu-id="d5919-124">External claims</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Identity.ExternalClaims)
* [<span data-ttu-id="d5919-125">Sélection entre cookie et un autre schéma d’authentification basé sur la demande</span><span class="sxs-lookup"><span data-stu-id="d5919-125">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="d5919-126">Restreint l’accès aux fichiers statiques</span><span class="sxs-lookup"><span data-stu-id="d5919-126">Restricts access to static files</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="d5919-127">Exécuter des exemples</span><span class="sxs-lookup"><span data-stu-id="d5919-127">Run the samples</span></span>

* <span data-ttu-id="d5919-128">Sélectionnez une [branche](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="d5919-128">Select a [branch](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="d5919-129">Par exemple : `release/2.2`</span><span class="sxs-lookup"><span data-stu-id="d5919-129">For example, `release/2.2`</span></span>
* <span data-ttu-id="d5919-130">Clonez ou téléchargez le [référentiel ASP.NET Core](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="d5919-130">Clone or download the [ASP.NET Core repository](https://github.com/dotnet/AspNetCore).</span></span>
* <span data-ttu-id="d5919-131">Vérifiez que vous avez installé la version [SDK core .NET](https://dotnet.microsoft.com/download/dotnet-core) correspondant au clone du référentiel ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d5919-131">Verify you have installed the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="d5919-132">Naviguez vers un échantillon dans *AspNetCore/src/Security/samples* et exécutez l’échantillon avec `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="d5919-132">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end
