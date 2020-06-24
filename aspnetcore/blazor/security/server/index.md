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
ms.openlocfilehash: 2811e08fd2f6c66112ffa0bb40f474158f4c7a59
ms.sourcegitcommit: 5e462c3328c70f95969d02adce9c71592049f54c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/24/2020
ms.locfileid: "85292683"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a><span data-ttu-id="99f26-103">Sécuriser les Blazor applications ASP.net Core Server</span><span class="sxs-lookup"><span data-stu-id="99f26-103">Secure ASP.NET Core Blazor Server apps</span></span>

<span data-ttu-id="99f26-104">Par [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="99f26-104">By [Luke Latham](https://github.com/guardrex)</span></span>

Blazor<span data-ttu-id="99f26-105">Les applications serveur sont configurées pour la sécurité de la même façon que les applications ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="99f26-105"> Server apps are configured for security in the same manner as ASP.NET Core apps.</span></span> <span data-ttu-id="99f26-106">Pour plus d’informations, consultez les articles sous <xref:security/index> .</span><span class="sxs-lookup"><span data-stu-id="99f26-106">For more information, see the articles under <xref:security/index>.</span></span> <span data-ttu-id="99f26-107">Les rubriques de cette vue d’ensemble s’appliquent spécifiquement au Blazor serveur.</span><span class="sxs-lookup"><span data-stu-id="99f26-107">Topics under this overview apply specifically to Blazor Server.</span></span> 

## <a name="blazor-server-project-template"></a>Blazor<span data-ttu-id="99f26-108">Modèle de projet serveur</span><span class="sxs-lookup"><span data-stu-id="99f26-108"> Server project template</span></span>

<span data-ttu-id="99f26-109">Le Blazor modèle de projet serveur peut être configuré pour l’authentification lors de la création du projet.</span><span class="sxs-lookup"><span data-stu-id="99f26-109">The Blazor Server project template can be configured for authentication when the project is created.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="99f26-110">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="99f26-110">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="99f26-111">Suivez les instructions de Visual Studio dans l' <xref:blazor/get-started> article pour créer un nouveau Blazor projet serveur avec un mécanisme d’authentification.</span><span class="sxs-lookup"><span data-stu-id="99f26-111">Follow the Visual Studio guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="99f26-112">Après avoir choisi le modèle d’application \*\* Blazor serveur\*\* dans la boîte de dialogue **créer une application Web ASP.net Core** , sélectionnez **modifier** sous **authentification**.</span><span class="sxs-lookup"><span data-stu-id="99f26-112">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="99f26-113">Une boîte de dialogue s’ouvre pour offrir le même ensemble de mécanismes d’authentification que ceux disponibles pour les autres projets ASP.NET Core :</span><span class="sxs-lookup"><span data-stu-id="99f26-113">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="99f26-114">**Aucune authentification**</span><span class="sxs-lookup"><span data-stu-id="99f26-114">**No Authentication**</span></span>
* <span data-ttu-id="99f26-115">**Comptes d’utilisateur individuels : les**comptes d’utilisateur peuvent être stockés :</span><span class="sxs-lookup"><span data-stu-id="99f26-115">**Individual User Accounts**: User accounts can be stored:</span></span>
  * <span data-ttu-id="99f26-116">Au sein de l’application à l’aide du système de ASP.NET Core [Identity](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="99f26-116">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="99f26-117">Avec [Azure ad B2C](xref:security/authentication/azure-ad-b2c).</span><span class="sxs-lookup"><span data-stu-id="99f26-117">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="99f26-118">**Comptes professionnels ou scolaires**</span><span class="sxs-lookup"><span data-stu-id="99f26-118">**Work or School Accounts**</span></span>
* <span data-ttu-id="99f26-119">**Authentification Windows**</span><span class="sxs-lookup"><span data-stu-id="99f26-119">**Windows Authentication**</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="99f26-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="99f26-120">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="99f26-121">Suivez les instructions de Visual Studio Code dans l' <xref:blazor/get-started> article pour créer un nouveau Blazor projet serveur avec un mécanisme d’authentification :</span><span class="sxs-lookup"><span data-stu-id="99f26-121">Follow the Visual Studio Code guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="99f26-122">Les valeurs autorisées d’authentification (`{AUTHENTICATION}`) sont présentées dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="99f26-122">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="99f26-123">Mécanisme d’authentification</span><span class="sxs-lookup"><span data-stu-id="99f26-123">Authentication mechanism</span></span> | <span data-ttu-id="99f26-124">Description</span><span class="sxs-lookup"><span data-stu-id="99f26-124">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="99f26-125">`None` (valeur par défaut)</span><span class="sxs-lookup"><span data-stu-id="99f26-125">`None` (default)</span></span>         | <span data-ttu-id="99f26-126">Aucune authentification</span><span class="sxs-lookup"><span data-stu-id="99f26-126">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="99f26-127">Utilisateurs stockés dans l’application avec ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="99f26-127">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="99f26-128">Utilisateurs stockés dans [Azure ad B2C](xref:security/authentication/azure-ad-b2c)</span><span class="sxs-lookup"><span data-stu-id="99f26-128">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="99f26-129">Authentification d’organisation pour un seul locataire</span><span class="sxs-lookup"><span data-stu-id="99f26-129">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="99f26-130">Authentification d’organisation pour plusieurs locataires</span><span class="sxs-lookup"><span data-stu-id="99f26-130">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="99f26-131">Authentification Windows</span><span class="sxs-lookup"><span data-stu-id="99f26-131">Windows Authentication</span></span> |

<span data-ttu-id="99f26-132">À l’aide de l' `-o|--output` option, la commande utilise la valeur fournie pour l' `{APP NAME}` espace réservé à :</span><span class="sxs-lookup"><span data-stu-id="99f26-132">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="99f26-133">Créez un dossier pour le projet.</span><span class="sxs-lookup"><span data-stu-id="99f26-133">Create a folder for the project.</span></span>
* <span data-ttu-id="99f26-134">Nommez ce projet.</span><span class="sxs-lookup"><span data-stu-id="99f26-134">Name the project.</span></span>

<span data-ttu-id="99f26-135">Pour plus d’informations, consultez la [`dotnet new`](/dotnet/core/tools/dotnet-new) commande dans le guide .net core.</span><span class="sxs-lookup"><span data-stu-id="99f26-135">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="99f26-136">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="99f26-136">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="99f26-137">Suivez les instructions de Visual Studio pour Mac dans l' <xref:blazor/get-started> article.</span><span class="sxs-lookup"><span data-stu-id="99f26-137">Follow the Visual Studio for Mac guidance in the <xref:blazor/get-started> article.</span></span>

1. <span data-ttu-id="99f26-138">Dans l’étape **configurer votre Blazor application de serveur** , sélectionnez **authentification individuelle (dans l’application)** dans la liste déroulante **authentification** .</span><span class="sxs-lookup"><span data-stu-id="99f26-138">On the **Configure your new Blazor Server App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="99f26-139">L’application est créée pour les utilisateurs individuels stockés dans l’application avec ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="99f26-139">The app is created for individual users stored in the app with ASP.NET Core Identity.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="99f26-140">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="99f26-140">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="99f26-141">Suivez les instructions de CLI .NET Core dans l' <xref:blazor/get-started> article pour créer un nouveau Blazor projet serveur avec un mécanisme d’authentification :</span><span class="sxs-lookup"><span data-stu-id="99f26-141">Follow the .NET Core CLI guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="99f26-142">Les valeurs autorisées d’authentification (`{AUTHENTICATION}`) sont présentées dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="99f26-142">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="99f26-143">Mécanisme d’authentification</span><span class="sxs-lookup"><span data-stu-id="99f26-143">Authentication mechanism</span></span> | <span data-ttu-id="99f26-144">Description</span><span class="sxs-lookup"><span data-stu-id="99f26-144">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="99f26-145">`None` (valeur par défaut)</span><span class="sxs-lookup"><span data-stu-id="99f26-145">`None` (default)</span></span>         | <span data-ttu-id="99f26-146">Aucune authentification</span><span class="sxs-lookup"><span data-stu-id="99f26-146">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="99f26-147">Utilisateurs stockés dans l’application avec ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="99f26-147">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="99f26-148">Utilisateurs stockés dans [Azure ad B2C](xref:security/authentication/azure-ad-b2c)</span><span class="sxs-lookup"><span data-stu-id="99f26-148">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="99f26-149">Authentification d’organisation pour un seul locataire</span><span class="sxs-lookup"><span data-stu-id="99f26-149">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="99f26-150">Authentification d’organisation pour plusieurs locataires</span><span class="sxs-lookup"><span data-stu-id="99f26-150">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="99f26-151">Authentification Windows</span><span class="sxs-lookup"><span data-stu-id="99f26-151">Windows Authentication</span></span> |

<span data-ttu-id="99f26-152">À l’aide de l' `-o|--output` option, la commande utilise la valeur fournie pour l' `{APP NAME}` espace réservé à :</span><span class="sxs-lookup"><span data-stu-id="99f26-152">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="99f26-153">Créez un dossier pour le projet.</span><span class="sxs-lookup"><span data-stu-id="99f26-153">Create a folder for the project.</span></span>
* <span data-ttu-id="99f26-154">Nommez ce projet.</span><span class="sxs-lookup"><span data-stu-id="99f26-154">Name the project.</span></span>

<span data-ttu-id="99f26-155">Pour plus d’informations, consultez la [`dotnet new`](/dotnet/core/tools/dotnet-new) commande dans le guide .net core.</span><span class="sxs-lookup"><span data-stu-id="99f26-155">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

---

## <a name="scaffold-identity"></a><span data-ttu-id="99f26-156">DestinIdentity</span><span class="sxs-lookup"><span data-stu-id="99f26-156">Scaffold Identity</span></span>

<span data-ttu-id="99f26-157">Génération Identity de modèles automatique dans un Blazor projet serveur :</span><span class="sxs-lookup"><span data-stu-id="99f26-157">Scaffold Identity into a Blazor Server project:</span></span>

* <span data-ttu-id="99f26-158">[Sans autorisation existante](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span><span class="sxs-lookup"><span data-stu-id="99f26-158">[Without existing authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span></span>
* <span data-ttu-id="99f26-159">[Avec autorisation](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span><span class="sxs-lookup"><span data-stu-id="99f26-159">[With authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span></span>
