---
title: Exemples d’authentification pour ASP.NET Core
author: rick-anderson
description: Fournit des liens vers les exemples d’authentification dans le référentiel ASP.NET Core.
ms.author: riande
ms.date: 01/31/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/samples
ms.openlocfilehash: 7cd0fe60d7917abda7d8ac0e071deca13a4136ce
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776550"
---
# <a name="authentication-samples-for-aspnet-core"></a><span data-ttu-id="512a8-103">Exemples d’authentification pour ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="512a8-103">Authentication samples for ASP.NET Core</span></span>

<span data-ttu-id="512a8-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="512a8-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="512a8-105">Le [référentiel ASP.net Core](https://github.com/dotnet/AspNetCore) contient les exemples d’authentification suivants dans le dossier *AspNetCore/SRC/Security/Samples* :</span><span class="sxs-lookup"><span data-stu-id="512a8-105">The [ASP.NET Core repository](https://github.com/dotnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="512a8-106">Transformation de revendications</span><span class="sxs-lookup"><span data-stu-id="512a8-106">Claims transformation</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/ClaimsTransformation)
* [<span data-ttu-id="512a8-107">Authentification par cookie</span><span class="sxs-lookup"><span data-stu-id="512a8-107">Cookie authentication</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Cookies)
* [<span data-ttu-id="512a8-108">Fournisseur de stratégie personnalisée-IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="512a8-108">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="512a8-109">Options et schémas d’authentification dynamique</span><span class="sxs-lookup"><span data-stu-id="512a8-109">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/DynamicSchemes)
* <span data-ttu-id="512a8-110">[Revendications externes](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Identity.ExternalClaims)</span><span class="sxs-lookup"><span data-stu-id="512a8-110">[External claims](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Identity.ExternalClaims)</span></span>
* [<span data-ttu-id="512a8-111">Sélection entre un cookie et un autre schéma d’authentification en fonction de la demande</span><span class="sxs-lookup"><span data-stu-id="512a8-111">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="512a8-112">Restreint l’accès aux fichiers statiques</span><span class="sxs-lookup"><span data-stu-id="512a8-112">Restricts access to static files</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="512a8-113">Exécuter des exemples</span><span class="sxs-lookup"><span data-stu-id="512a8-113">Run the samples</span></span>

* <span data-ttu-id="512a8-114">Sélectionnez une [branche](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="512a8-114">Select a [branch](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="512a8-115">Par exemple : `release/3.1`</span><span class="sxs-lookup"><span data-stu-id="512a8-115">For example, `release/3.1`</span></span>
* <span data-ttu-id="512a8-116">Clonez ou téléchargez le [référentiel ASP.net Core](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="512a8-116">Clone or download the [ASP.NET Core repository](https://github.com/dotnet/AspNetCore).</span></span>
* <span data-ttu-id="512a8-117">Vérifiez que vous avez installé la version de [Kit SDK .net Core](https://dotnet.microsoft.com/download/dotnet-core) qui correspond au clone du référentiel ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="512a8-117">Verify you have installed the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="512a8-118">Accédez à un exemple dans *AspNetCore/SRC/Security/Samples* et exécutez l’exemple `dotnet run`avec.</span><span class="sxs-lookup"><span data-stu-id="512a8-118">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="512a8-119">Le [référentiel ASP.net Core](https://github.com/dotnet/AspNetCore) contient les exemples d’authentification suivants dans le dossier *AspNetCore/SRC/Security/Samples* :</span><span class="sxs-lookup"><span data-stu-id="512a8-119">The [ASP.NET Core repository](https://github.com/dotnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="512a8-120">Transformation de revendications</span><span class="sxs-lookup"><span data-stu-id="512a8-120">Claims transformation</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/ClaimsTransformation)
* [<span data-ttu-id="512a8-121">Authentification par cookie</span><span class="sxs-lookup"><span data-stu-id="512a8-121">Cookie authentication</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Cookies)
* [<span data-ttu-id="512a8-122">Fournisseur de stratégie personnalisée-IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="512a8-122">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="512a8-123">Options et schémas d’authentification dynamique</span><span class="sxs-lookup"><span data-stu-id="512a8-123">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/DynamicSchemes)
* <span data-ttu-id="512a8-124">[Revendications externes](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Identity.ExternalClaims)</span><span class="sxs-lookup"><span data-stu-id="512a8-124">[External claims](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Identity.ExternalClaims)</span></span>
* [<span data-ttu-id="512a8-125">Sélection entre un cookie et un autre schéma d’authentification en fonction de la demande</span><span class="sxs-lookup"><span data-stu-id="512a8-125">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="512a8-126">Restreint l’accès aux fichiers statiques</span><span class="sxs-lookup"><span data-stu-id="512a8-126">Restricts access to static files</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="512a8-127">Exécuter des exemples</span><span class="sxs-lookup"><span data-stu-id="512a8-127">Run the samples</span></span>

* <span data-ttu-id="512a8-128">Sélectionnez une [branche](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="512a8-128">Select a [branch](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="512a8-129">Par exemple : `release/2.2`</span><span class="sxs-lookup"><span data-stu-id="512a8-129">For example, `release/2.2`</span></span>
* <span data-ttu-id="512a8-130">Clonez ou téléchargez le [référentiel ASP.net Core](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="512a8-130">Clone or download the [ASP.NET Core repository](https://github.com/dotnet/AspNetCore).</span></span>
* <span data-ttu-id="512a8-131">Vérifiez que vous avez installé la version de [Kit SDK .net Core](https://dotnet.microsoft.com/download/dotnet-core) qui correspond au clone du référentiel ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="512a8-131">Verify you have installed the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="512a8-132">Accédez à un exemple dans *AspNetCore/SRC/Security/Samples* et exécutez l’exemple `dotnet run`avec.</span><span class="sxs-lookup"><span data-stu-id="512a8-132">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end
