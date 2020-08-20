---
title: Exemples d’authentification pour ASP.NET Core
author: rick-anderson
description: Fournit des liens vers les exemples d’authentification dans le référentiel ASP.NET Core.
ms.author: riande
ms.date: 01/31/2019
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
uid: security/authentication/samples
ms.openlocfilehash: 290c956b2035e47e5b34dba15fbec665461dd94a
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88630742"
---
# <a name="authentication-samples-for-aspnet-core"></a><span data-ttu-id="104d0-103">Exemples d’authentification pour ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="104d0-103">Authentication samples for ASP.NET Core</span></span>

<span data-ttu-id="104d0-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="104d0-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="104d0-105">Le [référentiel ASP.net Core](https://github.com/dotnet/AspNetCore) contient les exemples d’authentification suivants dans le dossier *AspNetCore/SRC/Security/Samples* :</span><span class="sxs-lookup"><span data-stu-id="104d0-105">The [ASP.NET Core repository](https://github.com/dotnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="104d0-106">Transformation de revendications</span><span class="sxs-lookup"><span data-stu-id="104d0-106">Claims transformation</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/ClaimsTransformation)
* <span data-ttu-id="104d0-107">[Cookie identification](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Cookies)</span><span class="sxs-lookup"><span data-stu-id="104d0-107">[Cookie authentication](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Cookies)</span></span>
* [<span data-ttu-id="104d0-108">Fournisseur de stratégie personnalisée-IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="104d0-108">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="104d0-109">Options et schémas d’authentification dynamique</span><span class="sxs-lookup"><span data-stu-id="104d0-109">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/DynamicSchemes)
* <span data-ttu-id="104d0-110">[Revendications externes](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Identity.ExternalClaims)</span><span class="sxs-lookup"><span data-stu-id="104d0-110">[External claims](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Identity.ExternalClaims)</span></span>
* [<span data-ttu-id="104d0-111">Sélection de cookie l’un ou l’autre schéma d’authentification en fonction de la demande</span><span class="sxs-lookup"><span data-stu-id="104d0-111">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="104d0-112">Restreint l’accès aux fichiers statiques</span><span class="sxs-lookup"><span data-stu-id="104d0-112">Restricts access to static files</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="104d0-113">Exécuter des exemples</span><span class="sxs-lookup"><span data-stu-id="104d0-113">Run the samples</span></span>

* <span data-ttu-id="104d0-114">Sélectionnez une [branche](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="104d0-114">Select a [branch](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="104d0-115">Par exemple : `release/3.1`</span><span class="sxs-lookup"><span data-stu-id="104d0-115">For example, `release/3.1`</span></span>
* <span data-ttu-id="104d0-116">Clonez ou téléchargez le [référentiel ASP.net Core](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="104d0-116">Clone or download the [ASP.NET Core repository](https://github.com/dotnet/AspNetCore).</span></span>
* <span data-ttu-id="104d0-117">Vérifiez que vous avez installé la version de [Kit SDK .net Core](https://dotnet.microsoft.com/download/dotnet-core) qui correspond au clone du référentiel ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="104d0-117">Verify you have installed the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="104d0-118">Accédez à un exemple dans *AspNetCore/SRC/Security/Samples* et exécutez l’exemple avec `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="104d0-118">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="104d0-119">Le [référentiel ASP.net Core](https://github.com/dotnet/AspNetCore) contient les exemples d’authentification suivants dans le dossier *AspNetCore/SRC/Security/Samples* :</span><span class="sxs-lookup"><span data-stu-id="104d0-119">The [ASP.NET Core repository](https://github.com/dotnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="104d0-120">Transformation de revendications</span><span class="sxs-lookup"><span data-stu-id="104d0-120">Claims transformation</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/ClaimsTransformation)
* <span data-ttu-id="104d0-121">[Cookie identification](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/Cookies)</span><span class="sxs-lookup"><span data-stu-id="104d0-121">[Cookie authentication](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/Cookies)</span></span>
* [<span data-ttu-id="104d0-122">Fournisseur de stratégie personnalisée-IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="104d0-122">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/AspNetCore/tree/2.1.3/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="104d0-123">Options et schémas d’authentification dynamique</span><span class="sxs-lookup"><span data-stu-id="104d0-123">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/DynamicSchemes)
* <span data-ttu-id="104d0-124">[Revendications externes](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/Identity.ExternalClaims)</span><span class="sxs-lookup"><span data-stu-id="104d0-124">[External claims](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/Identity.ExternalClaims)</span></span>
* [<span data-ttu-id="104d0-125">Sélection de cookie l’un ou l’autre schéma d’authentification en fonction de la demande</span><span class="sxs-lookup"><span data-stu-id="104d0-125">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="104d0-126">Restreint l’accès aux fichiers statiques</span><span class="sxs-lookup"><span data-stu-id="104d0-126">Restricts access to static files</span></span>](https://github.com/dotnet/AspNetCore/tree/2.1.3/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="104d0-127">Exécuter des exemples</span><span class="sxs-lookup"><span data-stu-id="104d0-127">Run the samples</span></span>

* <span data-ttu-id="104d0-128">Sélectionnez une [branche](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="104d0-128">Select a [branch](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="104d0-129">Par exemple : `release/2.1`</span><span class="sxs-lookup"><span data-stu-id="104d0-129">For example, `release/2.1`</span></span>
* <span data-ttu-id="104d0-130">Clonez ou téléchargez le [référentiel ASP.net Core](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="104d0-130">Clone or download the [ASP.NET Core repository](https://github.com/dotnet/AspNetCore).</span></span>
* <span data-ttu-id="104d0-131">Vérifiez que vous avez installé la version de [Kit SDK .net Core](https://dotnet.microsoft.com/download/dotnet-core) qui correspond au clone du référentiel ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="104d0-131">Verify you have installed the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="104d0-132">Accédez à un exemple dans *AspNetCore/SRC/Security/Samples* et exécutez l’exemple avec `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="104d0-132">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end
