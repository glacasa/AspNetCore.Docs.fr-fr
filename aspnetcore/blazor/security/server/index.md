---
title: Sécuriser les Blazor applications ASP.net Core Server
author: guardrex
description: Découvrez comment sécuriser des applications Blazor serveur en tant qu’applications ASP.net core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/02/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/server/index
ms.openlocfilehash: 2bdd2f256f456cbf474181021fafc6830bfd68f4
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2020
ms.locfileid: "85242925"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a><span data-ttu-id="1624c-103">Sécuriser les Blazor applications ASP.net Core Server</span><span class="sxs-lookup"><span data-stu-id="1624c-103">Secure ASP.NET Core Blazor Server apps</span></span>

<span data-ttu-id="1624c-104">Par [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="1624c-104">By [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="blazor-server-project-template"></a>Blazor<span data-ttu-id="1624c-105">Modèle de projet serveur</span><span class="sxs-lookup"><span data-stu-id="1624c-105"> Server project template</span></span>

<span data-ttu-id="1624c-106">Le Blazor modèle de projet serveur peut être configuré pour l’authentification lors de la création du projet.</span><span class="sxs-lookup"><span data-stu-id="1624c-106">The Blazor Server project template can be configured for authentication when the project is created.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1624c-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1624c-107">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1624c-108">Suivez les instructions de Visual Studio dans l' <xref:blazor/get-started> article pour créer un nouveau Blazor projet serveur avec un mécanisme d’authentification.</span><span class="sxs-lookup"><span data-stu-id="1624c-108">Follow the Visual Studio guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="1624c-109">Après avoir choisi le modèle d’application \*\* Blazor serveur\*\* dans la boîte de dialogue **créer une application Web ASP.net Core** , sélectionnez **modifier** sous **authentification**.</span><span class="sxs-lookup"><span data-stu-id="1624c-109">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="1624c-110">Une boîte de dialogue s’ouvre pour offrir le même ensemble de mécanismes d’authentification que ceux disponibles pour les autres projets ASP.NET Core :</span><span class="sxs-lookup"><span data-stu-id="1624c-110">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="1624c-111">**Aucune authentification**</span><span class="sxs-lookup"><span data-stu-id="1624c-111">**No Authentication**</span></span>
* <span data-ttu-id="1624c-112">**Comptes d’utilisateur individuels : les**comptes d’utilisateur peuvent être stockés :</span><span class="sxs-lookup"><span data-stu-id="1624c-112">**Individual User Accounts**: User accounts can be stored:</span></span>
  * <span data-ttu-id="1624c-113">Au sein de l’application à l’aide du système de ASP.NET Core [Identity](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="1624c-113">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="1624c-114">Avec [Azure ad B2C](xref:security/authentication/azure-ad-b2c).</span><span class="sxs-lookup"><span data-stu-id="1624c-114">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="1624c-115">**Comptes professionnels ou scolaires**</span><span class="sxs-lookup"><span data-stu-id="1624c-115">**Work or School Accounts**</span></span>
* <span data-ttu-id="1624c-116">**Authentification Windows**</span><span class="sxs-lookup"><span data-stu-id="1624c-116">**Windows Authentication**</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="1624c-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1624c-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="1624c-118">Suivez les instructions de Visual Studio Code dans l' <xref:blazor/get-started> article pour créer un nouveau Blazor projet serveur avec un mécanisme d’authentification :</span><span class="sxs-lookup"><span data-stu-id="1624c-118">Follow the Visual Studio Code guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="1624c-119">Les valeurs autorisées d’authentification (`{AUTHENTICATION}`) sont présentées dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="1624c-119">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="1624c-120">Mécanisme d’authentification</span><span class="sxs-lookup"><span data-stu-id="1624c-120">Authentication mechanism</span></span> | <span data-ttu-id="1624c-121">Description</span><span class="sxs-lookup"><span data-stu-id="1624c-121">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="1624c-122">`None` (valeur par défaut)</span><span class="sxs-lookup"><span data-stu-id="1624c-122">`None` (default)</span></span>         | <span data-ttu-id="1624c-123">Aucune authentification</span><span class="sxs-lookup"><span data-stu-id="1624c-123">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="1624c-124">Utilisateurs stockés dans l’application avec ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="1624c-124">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="1624c-125">Utilisateurs stockés dans [Azure ad B2C](xref:security/authentication/azure-ad-b2c)</span><span class="sxs-lookup"><span data-stu-id="1624c-125">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="1624c-126">Authentification d’organisation pour un seul locataire</span><span class="sxs-lookup"><span data-stu-id="1624c-126">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="1624c-127">Authentification d’organisation pour plusieurs locataires</span><span class="sxs-lookup"><span data-stu-id="1624c-127">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="1624c-128">Authentification Windows</span><span class="sxs-lookup"><span data-stu-id="1624c-128">Windows Authentication</span></span> |

<span data-ttu-id="1624c-129">À l’aide de l' `-o|--output` option, la commande utilise la valeur fournie pour l' `{APP NAME}` espace réservé à :</span><span class="sxs-lookup"><span data-stu-id="1624c-129">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="1624c-130">Créez un dossier pour le projet.</span><span class="sxs-lookup"><span data-stu-id="1624c-130">Create a folder for the project.</span></span>
* <span data-ttu-id="1624c-131">Nommez ce projet.</span><span class="sxs-lookup"><span data-stu-id="1624c-131">Name the project.</span></span>

<span data-ttu-id="1624c-132">Pour plus d’informations, consultez la [`dotnet new`](/dotnet/core/tools/dotnet-new) commande dans le guide .net core.</span><span class="sxs-lookup"><span data-stu-id="1624c-132">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1624c-133">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="1624c-133">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="1624c-134">Suivez les instructions de Visual Studio pour Mac dans l' <xref:blazor/get-started> article.</span><span class="sxs-lookup"><span data-stu-id="1624c-134">Follow the Visual Studio for Mac guidance in the <xref:blazor/get-started> article.</span></span>

1. <span data-ttu-id="1624c-135">Dans l’étape **configurer votre Blazor application de serveur** , sélectionnez **authentification individuelle (dans l’application)** dans la liste déroulante **authentification** .</span><span class="sxs-lookup"><span data-stu-id="1624c-135">On the **Configure your new Blazor Server App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="1624c-136">L’application est créée pour les utilisateurs individuels stockés dans l’application avec ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="1624c-136">The app is created for individual users stored in the app with ASP.NET Core Identity.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="1624c-137">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="1624c-137">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="1624c-138">Suivez les instructions de CLI .NET Core dans l' <xref:blazor/get-started> article pour créer un nouveau Blazor projet serveur avec un mécanisme d’authentification :</span><span class="sxs-lookup"><span data-stu-id="1624c-138">Follow the .NET Core CLI guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="1624c-139">Les valeurs autorisées d’authentification (`{AUTHENTICATION}`) sont présentées dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="1624c-139">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="1624c-140">Mécanisme d’authentification</span><span class="sxs-lookup"><span data-stu-id="1624c-140">Authentication mechanism</span></span> | <span data-ttu-id="1624c-141">Description</span><span class="sxs-lookup"><span data-stu-id="1624c-141">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="1624c-142">`None` (valeur par défaut)</span><span class="sxs-lookup"><span data-stu-id="1624c-142">`None` (default)</span></span>         | <span data-ttu-id="1624c-143">Aucune authentification</span><span class="sxs-lookup"><span data-stu-id="1624c-143">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="1624c-144">Utilisateurs stockés dans l’application avec ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="1624c-144">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="1624c-145">Utilisateurs stockés dans [Azure ad B2C](xref:security/authentication/azure-ad-b2c)</span><span class="sxs-lookup"><span data-stu-id="1624c-145">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="1624c-146">Authentification d’organisation pour un seul locataire</span><span class="sxs-lookup"><span data-stu-id="1624c-146">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="1624c-147">Authentification d’organisation pour plusieurs locataires</span><span class="sxs-lookup"><span data-stu-id="1624c-147">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="1624c-148">Authentification Windows</span><span class="sxs-lookup"><span data-stu-id="1624c-148">Windows Authentication</span></span> |

<span data-ttu-id="1624c-149">À l’aide de l' `-o|--output` option, la commande utilise la valeur fournie pour l' `{APP NAME}` espace réservé à :</span><span class="sxs-lookup"><span data-stu-id="1624c-149">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="1624c-150">Créez un dossier pour le projet.</span><span class="sxs-lookup"><span data-stu-id="1624c-150">Create a folder for the project.</span></span>
* <span data-ttu-id="1624c-151">Nommez ce projet.</span><span class="sxs-lookup"><span data-stu-id="1624c-151">Name the project.</span></span>

<span data-ttu-id="1624c-152">Pour plus d’informations, consultez la [`dotnet new`](/dotnet/core/tools/dotnet-new) commande dans le guide .net core.</span><span class="sxs-lookup"><span data-stu-id="1624c-152">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

---

## <a name="secure-an-existing-app"></a><span data-ttu-id="1624c-153">Sécuriser une application existante</span><span class="sxs-lookup"><span data-stu-id="1624c-153">Secure an existing app</span></span>

Blazor<span data-ttu-id="1624c-154">Les applications serveur sont configurées pour la sécurité de la même façon que les applications ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1624c-154"> Server apps are configured for security in the same manner as ASP.NET Core apps.</span></span> <span data-ttu-id="1624c-155">Pour plus d’informations, consultez les articles sous <xref:security/index> .</span><span class="sxs-lookup"><span data-stu-id="1624c-155">For more information, see the articles under <xref:security/index>.</span></span>

## <a name="scaffold-identity"></a><span data-ttu-id="1624c-156">DestinIdentity</span><span class="sxs-lookup"><span data-stu-id="1624c-156">Scaffold Identity</span></span>

<span data-ttu-id="1624c-157">Génération Identity de modèles automatique dans un Blazor projet serveur :</span><span class="sxs-lookup"><span data-stu-id="1624c-157">Scaffold Identity into a Blazor Server project:</span></span>

* <span data-ttu-id="1624c-158">[Sans autorisation existante](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span><span class="sxs-lookup"><span data-stu-id="1624c-158">[Without existing authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span></span>
* <span data-ttu-id="1624c-159">[Avec autorisation](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span><span class="sxs-lookup"><span data-stu-id="1624c-159">[With authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span></span>
