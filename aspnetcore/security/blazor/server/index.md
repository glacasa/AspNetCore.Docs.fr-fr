---
title: Sécuriser Blazor les applications ASP.net Core Server
author: guardrex
description: Découvrez comment sécuriser Blazor des applications serveur en tant qu’applications ASP.net core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/02/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/server/index
ms.openlocfilehash: 324b3e8ed2d931c81bb41381caa469b0ec2f2fda
ms.sourcegitcommit: c19e388c83c981232e6f128d97440262adfe06e2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2020
ms.locfileid: "82727809"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a><span data-ttu-id="ba069-103">Sécuriser les applications de serveur ASP.NET Core éblouissantes</span><span class="sxs-lookup"><span data-stu-id="ba069-103">Secure ASP.NET Core Blazor Server apps</span></span>

<span data-ttu-id="ba069-104">Par [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="ba069-104">By [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="blazor-server-project-template"></a><span data-ttu-id="ba069-105">Modèle de projet de serveur éblouissant</span><span class="sxs-lookup"><span data-stu-id="ba069-105">Blazor Server project template</span></span>

<span data-ttu-id="ba069-106">Le modèle de projet de serveur éblouissant peut être configuré pour l’authentification lors de la création du projet.</span><span class="sxs-lookup"><span data-stu-id="ba069-106">The Blazor Server project template can be configured for authentication when the project is created.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ba069-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ba069-107">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ba069-108">Suivez les instructions de Visual Studio dans <xref:blazor/get-started> l’article pour créer un projet de serveur éblouissant avec un mécanisme d’authentification.</span><span class="sxs-lookup"><span data-stu-id="ba069-108">Follow the Visual Studio guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="ba069-109">Après avoir choisi le modèle **Application serveur Blazor** dans la boîte de dialogue **Créer une application web ASP.NET Core.**, sélectionnez **Modifier** sous **Authentification**.</span><span class="sxs-lookup"><span data-stu-id="ba069-109">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="ba069-110">Une boîte de dialogue s’ouvre pour offrir le même ensemble de mécanismes d’authentification que ceux disponibles pour les autres projets ASP.NET Core :</span><span class="sxs-lookup"><span data-stu-id="ba069-110">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="ba069-111">**Aucune authentification**</span><span class="sxs-lookup"><span data-stu-id="ba069-111">**No Authentication**</span></span>
* <span data-ttu-id="ba069-112">**Comptes d’utilisateur individuels** &ndash; Les comptes d'utilisateur peuvent être stockés :</span><span class="sxs-lookup"><span data-stu-id="ba069-112">**Individual User Accounts** &ndash; User accounts can be stored:</span></span>
  * <span data-ttu-id="ba069-113">Dans l’application à l’aide du système [d’identité](xref:security/authentication/identity) d’ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ba069-113">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="ba069-114">Avec [Azure ad B2C](xref:security/authentication/azure-ad-b2c).</span><span class="sxs-lookup"><span data-stu-id="ba069-114">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="ba069-115">**Comptes professionnels ou scolaires**</span><span class="sxs-lookup"><span data-stu-id="ba069-115">**Work or School Accounts**</span></span>
* <span data-ttu-id="ba069-116">**Authentification Windows**</span><span class="sxs-lookup"><span data-stu-id="ba069-116">**Windows Authentication**</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ba069-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ba069-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="ba069-118">Suivez les instructions de Visual Studio Code dans <xref:blazor/get-started> l’article pour créer un projet de serveur éblouissant avec un mécanisme d’authentification :</span><span class="sxs-lookup"><span data-stu-id="ba069-118">Follow the Visual Studio Code guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="ba069-119">Les valeurs autorisées d’authentification (`{AUTHENTICATION}`) sont présentées dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="ba069-119">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="ba069-120">Mécanisme d’authentification</span><span class="sxs-lookup"><span data-stu-id="ba069-120">Authentication mechanism</span></span> | <span data-ttu-id="ba069-121">Description</span><span class="sxs-lookup"><span data-stu-id="ba069-121">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="ba069-122">`None` (par défaut)</span><span class="sxs-lookup"><span data-stu-id="ba069-122">`None` (default)</span></span>         | <span data-ttu-id="ba069-123">Aucune authentification</span><span class="sxs-lookup"><span data-stu-id="ba069-123">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="ba069-124">Utilisateurs stockés dans l’application avec ASP.NET Core identité</span><span class="sxs-lookup"><span data-stu-id="ba069-124">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="ba069-125">Utilisateurs stockés dans [Azure ad B2C](xref:security/authentication/azure-ad-b2c)</span><span class="sxs-lookup"><span data-stu-id="ba069-125">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="ba069-126">Authentification d’organisation pour un seul locataire</span><span class="sxs-lookup"><span data-stu-id="ba069-126">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="ba069-127">Authentification d’organisation pour plusieurs locataires</span><span class="sxs-lookup"><span data-stu-id="ba069-127">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="ba069-128">Authentification Windows</span><span class="sxs-lookup"><span data-stu-id="ba069-128">Windows Authentication</span></span> |

<span data-ttu-id="ba069-129">À l' `-o|--output` aide de l’option, la commande utilise la valeur `{APP NAME}` fournie pour l’espace réservé à :</span><span class="sxs-lookup"><span data-stu-id="ba069-129">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="ba069-130">Créez un dossier pour le projet.</span><span class="sxs-lookup"><span data-stu-id="ba069-130">Create a folder for the project.</span></span>
* <span data-ttu-id="ba069-131">Nommez ce projet.</span><span class="sxs-lookup"><span data-stu-id="ba069-131">Name the project.</span></span>

<span data-ttu-id="ba069-132">Pour plus d’informations, consultez la commande [dotnet new](/dotnet/core/tools/dotnet-new) dans le Guide .NET Core.</span><span class="sxs-lookup"><span data-stu-id="ba069-132">For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ba069-133">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="ba069-133">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="ba069-134">Suivez les instructions de Visual Studio pour Mac dans <xref:blazor/get-started> l’article.</span><span class="sxs-lookup"><span data-stu-id="ba069-134">Follow the Visual Studio for Mac guidance in the <xref:blazor/get-started> article.</span></span>

1. <span data-ttu-id="ba069-135">Dans l’étape **configurer votre application de serveur éblouissant** , sélectionnez **authentification individuelle (dans l’application)** dans la liste déroulante **authentification** .</span><span class="sxs-lookup"><span data-stu-id="ba069-135">On the **Configure your new Blazor Server App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="ba069-136">L’application est créée pour les utilisateurs individuels stockés dans l’application avec ASP.NET Core identité.</span><span class="sxs-lookup"><span data-stu-id="ba069-136">The app is created for individual users stored in the app with ASP.NET Core Identity.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="ba069-137">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="ba069-137">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="ba069-138">Suivez les instructions de CLI .NET Core dans <xref:blazor/get-started> l’article pour créer un projet de serveur éblouissant avec un mécanisme d’authentification :</span><span class="sxs-lookup"><span data-stu-id="ba069-138">Follow the .NET Core CLI guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="ba069-139">Les valeurs autorisées d’authentification (`{AUTHENTICATION}`) sont présentées dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="ba069-139">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="ba069-140">Mécanisme d’authentification</span><span class="sxs-lookup"><span data-stu-id="ba069-140">Authentication mechanism</span></span> | <span data-ttu-id="ba069-141">Description</span><span class="sxs-lookup"><span data-stu-id="ba069-141">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="ba069-142">`None` (par défaut)</span><span class="sxs-lookup"><span data-stu-id="ba069-142">`None` (default)</span></span>         | <span data-ttu-id="ba069-143">Aucune authentification</span><span class="sxs-lookup"><span data-stu-id="ba069-143">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="ba069-144">Utilisateurs stockés dans l’application avec ASP.NET Core identité</span><span class="sxs-lookup"><span data-stu-id="ba069-144">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="ba069-145">Utilisateurs stockés dans [Azure ad B2C](xref:security/authentication/azure-ad-b2c)</span><span class="sxs-lookup"><span data-stu-id="ba069-145">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="ba069-146">Authentification d’organisation pour un seul locataire</span><span class="sxs-lookup"><span data-stu-id="ba069-146">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="ba069-147">Authentification d’organisation pour plusieurs locataires</span><span class="sxs-lookup"><span data-stu-id="ba069-147">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="ba069-148">Authentification Windows</span><span class="sxs-lookup"><span data-stu-id="ba069-148">Windows Authentication</span></span> |

<span data-ttu-id="ba069-149">À l' `-o|--output` aide de l’option, la commande utilise la valeur `{APP NAME}` fournie pour l’espace réservé à :</span><span class="sxs-lookup"><span data-stu-id="ba069-149">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="ba069-150">Créez un dossier pour le projet.</span><span class="sxs-lookup"><span data-stu-id="ba069-150">Create a folder for the project.</span></span>
* <span data-ttu-id="ba069-151">Nommez ce projet.</span><span class="sxs-lookup"><span data-stu-id="ba069-151">Name the project.</span></span>

<span data-ttu-id="ba069-152">Pour plus d’informations, consultez la commande [dotnet new](/dotnet/core/tools/dotnet-new) dans le Guide .NET Core.</span><span class="sxs-lookup"><span data-stu-id="ba069-152">For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

---

## <a name="secure-an-existing-app"></a><span data-ttu-id="ba069-153">Sécuriser une application existante</span><span class="sxs-lookup"><span data-stu-id="ba069-153">Secure an existing app</span></span>

Blazor<span data-ttu-id="ba069-154">Les applications serveur sont configurées pour la sécurité de la même façon que les applications ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ba069-154"> Server apps are configured for security in the same manner as ASP.NET Core apps.</span></span> <span data-ttu-id="ba069-155">Pour plus d’informations, consultez les articles <xref:security/index>sous.</span><span class="sxs-lookup"><span data-stu-id="ba069-155">For more information, see the articles under <xref:security/index>.</span></span>
