---
title: Sécuriser les Blazor Server applications ASP.net Core
author: guardrex
description: Découvrez comment sécuriser des applications Blazor Server en tant qu’applications ASP.net core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/02/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/server/index
ms.openlocfilehash: ab3baad30f78c5d5e2f969b3292d4886fcd0406d
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85402310"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a><span data-ttu-id="08b57-103">Sécuriser les Blazor Server applications ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="08b57-103">Secure ASP.NET Core Blazor Server apps</span></span>

<span data-ttu-id="08b57-104">Par [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="08b57-104">By [Luke Latham](https://github.com/guardrex)</span></span>

Blazor Server<span data-ttu-id="08b57-105">les applications sont configurées pour la sécurité de la même façon que les applications ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="08b57-105"> apps are configured for security in the same manner as ASP.NET Core apps.</span></span> <span data-ttu-id="08b57-106">Pour plus d’informations, consultez les articles sous <xref:security/index> .</span><span class="sxs-lookup"><span data-stu-id="08b57-106">For more information, see the articles under <xref:security/index>.</span></span> <span data-ttu-id="08b57-107">Les rubriques de cette vue d’ensemble s’appliquent spécifiquement à Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="08b57-107">Topics under this overview apply specifically to Blazor Server.</span></span> 

## <a name="blazor-server-project-template"></a>Blazor Server<span data-ttu-id="08b57-108">modèle de projet</span><span class="sxs-lookup"><span data-stu-id="08b57-108"> project template</span></span>

<span data-ttu-id="08b57-109">Le Blazor Server modèle de projet peut être configuré pour l’authentification lors de la création du projet.</span><span class="sxs-lookup"><span data-stu-id="08b57-109">The Blazor Server project template can be configured for authentication when the project is created.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="08b57-110">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="08b57-110">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="08b57-111">Suivez les instructions de Visual Studio dans l' <xref:blazor/get-started> article pour créer un nouveau Blazor Server projet avec un mécanisme d’authentification.</span><span class="sxs-lookup"><span data-stu-id="08b57-111">Follow the Visual Studio guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="08b57-112">Après avoir choisi le modèle d' \*\* Blazor Server application\*\* dans la boîte de dialogue **créer une application Web ASP.net Core** , sélectionnez **modifier** sous **authentification**.</span><span class="sxs-lookup"><span data-stu-id="08b57-112">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="08b57-113">Une boîte de dialogue s’ouvre pour offrir le même ensemble de mécanismes d’authentification que ceux disponibles pour les autres projets ASP.NET Core :</span><span class="sxs-lookup"><span data-stu-id="08b57-113">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="08b57-114">**Aucune authentification**</span><span class="sxs-lookup"><span data-stu-id="08b57-114">**No Authentication**</span></span>
* <span data-ttu-id="08b57-115">**Comptes d’utilisateur individuels : les**comptes d’utilisateur peuvent être stockés :</span><span class="sxs-lookup"><span data-stu-id="08b57-115">**Individual User Accounts**: User accounts can be stored:</span></span>
  * <span data-ttu-id="08b57-116">Au sein de l’application à l’aide du système de ASP.NET Core [Identity](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="08b57-116">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="08b57-117">Avec [Azure ad B2C](xref:security/authentication/azure-ad-b2c).</span><span class="sxs-lookup"><span data-stu-id="08b57-117">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="08b57-118">**Comptes professionnels ou scolaires**</span><span class="sxs-lookup"><span data-stu-id="08b57-118">**Work or School Accounts**</span></span>
* <span data-ttu-id="08b57-119">**Authentification Windows**</span><span class="sxs-lookup"><span data-stu-id="08b57-119">**Windows Authentication**</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="08b57-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="08b57-120">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="08b57-121">Suivez les instructions de Visual Studio Code dans l' <xref:blazor/get-started> article pour créer un Blazor Server projet avec un mécanisme d’authentification :</span><span class="sxs-lookup"><span data-stu-id="08b57-121">Follow the Visual Studio Code guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="08b57-122">Les valeurs autorisées d’authentification (`{AUTHENTICATION}`) sont présentées dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="08b57-122">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="08b57-123">Mécanisme d’authentification</span><span class="sxs-lookup"><span data-stu-id="08b57-123">Authentication mechanism</span></span> | <span data-ttu-id="08b57-124">Description</span><span class="sxs-lookup"><span data-stu-id="08b57-124">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="08b57-125">`None` (valeur par défaut)</span><span class="sxs-lookup"><span data-stu-id="08b57-125">`None` (default)</span></span>         | <span data-ttu-id="08b57-126">Aucune authentification</span><span class="sxs-lookup"><span data-stu-id="08b57-126">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="08b57-127">Utilisateurs stockés dans l’application avec ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="08b57-127">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="08b57-128">Utilisateurs stockés dans [Azure ad B2C](xref:security/authentication/azure-ad-b2c)</span><span class="sxs-lookup"><span data-stu-id="08b57-128">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="08b57-129">Authentification d’organisation pour un seul locataire</span><span class="sxs-lookup"><span data-stu-id="08b57-129">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="08b57-130">Authentification d’organisation pour plusieurs locataires</span><span class="sxs-lookup"><span data-stu-id="08b57-130">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="08b57-131">Authentification Windows</span><span class="sxs-lookup"><span data-stu-id="08b57-131">Windows Authentication</span></span> |

<span data-ttu-id="08b57-132">À l’aide de l' `-o|--output` option, la commande utilise la valeur fournie pour l' `{APP NAME}` espace réservé à :</span><span class="sxs-lookup"><span data-stu-id="08b57-132">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="08b57-133">Créez un dossier pour le projet.</span><span class="sxs-lookup"><span data-stu-id="08b57-133">Create a folder for the project.</span></span>
* <span data-ttu-id="08b57-134">Nommez ce projet.</span><span class="sxs-lookup"><span data-stu-id="08b57-134">Name the project.</span></span>

<span data-ttu-id="08b57-135">Pour plus d’informations, consultez la [`dotnet new`](/dotnet/core/tools/dotnet-new) commande dans le guide .net core.</span><span class="sxs-lookup"><span data-stu-id="08b57-135">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="08b57-136">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="08b57-136">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="08b57-137">Suivez les instructions de Visual Studio pour Mac dans l' <xref:blazor/get-started> article.</span><span class="sxs-lookup"><span data-stu-id="08b57-137">Follow the Visual Studio for Mac guidance in the <xref:blazor/get-started> article.</span></span>

1. <span data-ttu-id="08b57-138">Dans l’étape **configurer votre nouvelle Blazor Server application** , sélectionnez **authentification individuelle (dans l’application)** dans la liste déroulante **authentification** .</span><span class="sxs-lookup"><span data-stu-id="08b57-138">On the **Configure your new Blazor Server App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="08b57-139">L’application est créée pour les utilisateurs individuels stockés dans l’application avec ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="08b57-139">The app is created for individual users stored in the app with ASP.NET Core Identity.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="08b57-140">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="08b57-140">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="08b57-141">Suivez les instructions de CLI .NET Core dans l' <xref:blazor/get-started> article pour créer un Blazor Server projet avec un mécanisme d’authentification :</span><span class="sxs-lookup"><span data-stu-id="08b57-141">Follow the .NET Core CLI guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="08b57-142">Les valeurs autorisées d’authentification (`{AUTHENTICATION}`) sont présentées dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="08b57-142">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="08b57-143">Mécanisme d’authentification</span><span class="sxs-lookup"><span data-stu-id="08b57-143">Authentication mechanism</span></span> | <span data-ttu-id="08b57-144">Description</span><span class="sxs-lookup"><span data-stu-id="08b57-144">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="08b57-145">`None` (valeur par défaut)</span><span class="sxs-lookup"><span data-stu-id="08b57-145">`None` (default)</span></span>         | <span data-ttu-id="08b57-146">Aucune authentification</span><span class="sxs-lookup"><span data-stu-id="08b57-146">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="08b57-147">Utilisateurs stockés dans l’application avec ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="08b57-147">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="08b57-148">Utilisateurs stockés dans [Azure ad B2C](xref:security/authentication/azure-ad-b2c)</span><span class="sxs-lookup"><span data-stu-id="08b57-148">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="08b57-149">Authentification d’organisation pour un seul locataire</span><span class="sxs-lookup"><span data-stu-id="08b57-149">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="08b57-150">Authentification d’organisation pour plusieurs locataires</span><span class="sxs-lookup"><span data-stu-id="08b57-150">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="08b57-151">Authentification Windows</span><span class="sxs-lookup"><span data-stu-id="08b57-151">Windows Authentication</span></span> |

<span data-ttu-id="08b57-152">À l’aide de l' `-o|--output` option, la commande utilise la valeur fournie pour l' `{APP NAME}` espace réservé à :</span><span class="sxs-lookup"><span data-stu-id="08b57-152">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="08b57-153">Créez un dossier pour le projet.</span><span class="sxs-lookup"><span data-stu-id="08b57-153">Create a folder for the project.</span></span>
* <span data-ttu-id="08b57-154">Nommez ce projet.</span><span class="sxs-lookup"><span data-stu-id="08b57-154">Name the project.</span></span>

<span data-ttu-id="08b57-155">Pour plus d’informations, consultez la [`dotnet new`](/dotnet/core/tools/dotnet-new) commande dans le guide .net core.</span><span class="sxs-lookup"><span data-stu-id="08b57-155">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

---

## <a name="scaffold-identity"></a><span data-ttu-id="08b57-156">DestinIdentity</span><span class="sxs-lookup"><span data-stu-id="08b57-156">Scaffold Identity</span></span>

<span data-ttu-id="08b57-157">Génération Identity de modèles automatique dans un Blazor Server projet :</span><span class="sxs-lookup"><span data-stu-id="08b57-157">Scaffold Identity into a Blazor Server project:</span></span>

* <span data-ttu-id="08b57-158">[Sans autorisation existante](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span><span class="sxs-lookup"><span data-stu-id="08b57-158">[Without existing authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span></span>
* <span data-ttu-id="08b57-159">[Avec autorisation](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span><span class="sxs-lookup"><span data-stu-id="08b57-159">[With authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span></span>
