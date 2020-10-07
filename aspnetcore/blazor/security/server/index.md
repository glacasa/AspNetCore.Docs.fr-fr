---
title: Sécuriser les Blazor Server applications ASP.net Core
author: guardrex
description: Découvrez comment sécuriser des applications Blazor Server en tant qu’applications ASP.net core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/06/2020
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
uid: blazor/security/server/index
ms.openlocfilehash: d6d0f6f859dbaef98c6c8a9c53fe9858705cdc0a
ms.sourcegitcommit: 139c998d37e9f3e3d0e3d72e10dbce8b75957d89
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/07/2020
ms.locfileid: "91805503"
---
# <a name="secure-aspnet-core-no-locblazor-server-apps"></a><span data-ttu-id="b2ae1-103">Sécuriser les Blazor Server applications ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="b2ae1-103">Secure ASP.NET Core Blazor Server apps</span></span>

<span data-ttu-id="b2ae1-104">Par [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="b2ae1-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="b2ae1-105">Blazor Server les applications sont configurées pour la sécurité de la même façon que les applications ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b2ae1-105">Blazor Server apps are configured for security in the same manner as ASP.NET Core apps.</span></span> <span data-ttu-id="b2ae1-106">Pour plus d’informations, consultez les articles sous <xref:security/index> .</span><span class="sxs-lookup"><span data-stu-id="b2ae1-106">For more information, see the articles under <xref:security/index>.</span></span> <span data-ttu-id="b2ae1-107">Les rubriques de cette vue d’ensemble s’appliquent spécifiquement à Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="b2ae1-107">Topics under this overview apply specifically to Blazor Server.</span></span>

## <a name="no-locblazor-server-project-template"></a><span data-ttu-id="b2ae1-108">Blazor Server modèle de projet</span><span class="sxs-lookup"><span data-stu-id="b2ae1-108">Blazor Server project template</span></span>

<span data-ttu-id="b2ae1-109">Le Blazor Server modèle de projet peut être configuré pour l’authentification lors de la création du projet.</span><span class="sxs-lookup"><span data-stu-id="b2ae1-109">The Blazor Server project template can be configured for authentication when the project is created.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b2ae1-110">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b2ae1-110">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b2ae1-111">Suivez les instructions de Visual Studio dans <xref:blazor/tooling> pour créer un nouveau Blazor Server projet avec un mécanisme d’authentification.</span><span class="sxs-lookup"><span data-stu-id="b2ae1-111">Follow the Visual Studio guidance in <xref:blazor/tooling> to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="b2ae1-112">Après avoir choisi le modèle d' \*\* Blazor Server application\*\* dans la boîte de dialogue **créer une application Web ASP.net Core** , sélectionnez **modifier** sous **authentification**.</span><span class="sxs-lookup"><span data-stu-id="b2ae1-112">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="b2ae1-113">Une boîte de dialogue s’ouvre pour offrir le même ensemble de mécanismes d’authentification que ceux disponibles pour les autres projets ASP.NET Core :</span><span class="sxs-lookup"><span data-stu-id="b2ae1-113">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="b2ae1-114">**Aucune authentification**</span><span class="sxs-lookup"><span data-stu-id="b2ae1-114">**No Authentication**</span></span>
* <span data-ttu-id="b2ae1-115">**Comptes d’utilisateur individuels : les**comptes d’utilisateur peuvent être stockés :</span><span class="sxs-lookup"><span data-stu-id="b2ae1-115">**Individual User Accounts**: User accounts can be stored:</span></span>
  * <span data-ttu-id="b2ae1-116">Au sein de l’application à l’aide du système de ASP.NET Core [Identity](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="b2ae1-116">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="b2ae1-117">Avec [Azure ad B2C](xref:security/authentication/azure-ad-b2c).</span><span class="sxs-lookup"><span data-stu-id="b2ae1-117">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="b2ae1-118">**Comptes professionnels ou scolaires**</span><span class="sxs-lookup"><span data-stu-id="b2ae1-118">**Work or School Accounts**</span></span>
* <span data-ttu-id="b2ae1-119">**Authentification Windows**</span><span class="sxs-lookup"><span data-stu-id="b2ae1-119">**Windows Authentication**</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b2ae1-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b2ae1-120">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b2ae1-121">Suivez les instructions de Visual Studio Code dans <xref:blazor/tooling> pour créer un nouveau Blazor Server projet avec un mécanisme d’authentification :</span><span class="sxs-lookup"><span data-stu-id="b2ae1-121">Follow the Visual Studio Code guidance in <xref:blazor/tooling> to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="b2ae1-122">Les valeurs autorisées d’authentification (`{AUTHENTICATION}`) sont présentées dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="b2ae1-122">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="b2ae1-123">Mécanisme d’authentification</span><span class="sxs-lookup"><span data-stu-id="b2ae1-123">Authentication mechanism</span></span> | <span data-ttu-id="b2ae1-124">Description</span><span class="sxs-lookup"><span data-stu-id="b2ae1-124">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="b2ae1-125">`None` (valeur par défaut)</span><span class="sxs-lookup"><span data-stu-id="b2ae1-125">`None` (default)</span></span>         | <span data-ttu-id="b2ae1-126">Aucune authentification</span><span class="sxs-lookup"><span data-stu-id="b2ae1-126">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="b2ae1-127">Utilisateurs stockés dans l’application avec ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="b2ae1-127">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="b2ae1-128">Utilisateurs stockés dans [Azure ad B2C](xref:security/authentication/azure-ad-b2c)</span><span class="sxs-lookup"><span data-stu-id="b2ae1-128">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="b2ae1-129">Authentification d’organisation pour un seul locataire</span><span class="sxs-lookup"><span data-stu-id="b2ae1-129">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="b2ae1-130">Authentification d’organisation pour plusieurs locataires</span><span class="sxs-lookup"><span data-stu-id="b2ae1-130">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="b2ae1-131">Authentification Windows</span><span class="sxs-lookup"><span data-stu-id="b2ae1-131">Windows Authentication</span></span> |

<span data-ttu-id="b2ae1-132">À l’aide de l' `-o|--output` option, la commande utilise la valeur fournie pour l' `{APP NAME}` espace réservé à :</span><span class="sxs-lookup"><span data-stu-id="b2ae1-132">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="b2ae1-133">Créez un dossier pour le projet.</span><span class="sxs-lookup"><span data-stu-id="b2ae1-133">Create a folder for the project.</span></span>
* <span data-ttu-id="b2ae1-134">Nommez ce projet.</span><span class="sxs-lookup"><span data-stu-id="b2ae1-134">Name the project.</span></span>

<span data-ttu-id="b2ae1-135">Pour plus d’informations, consultez la [`dotnet new`](/dotnet/core/tools/dotnet-new) commande dans le guide .net core.</span><span class="sxs-lookup"><span data-stu-id="b2ae1-135">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b2ae1-136">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="b2ae1-136">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="b2ae1-137">Suivez les instructions de Visual Studio pour Mac dans <xref:blazor/tooling> .</span><span class="sxs-lookup"><span data-stu-id="b2ae1-137">Follow the Visual Studio for Mac guidance in <xref:blazor/tooling>.</span></span>

1. <span data-ttu-id="b2ae1-138">Dans l’étape **configurer votre nouvelle Blazor Server application** , sélectionnez **authentification individuelle (dans l’application)** dans la liste déroulante **authentification** .</span><span class="sxs-lookup"><span data-stu-id="b2ae1-138">On the **Configure your new Blazor Server App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="b2ae1-139">L’application est créée pour les utilisateurs individuels stockés dans l’application avec ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="b2ae1-139">The app is created for individual users stored in the app with ASP.NET Core Identity.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="b2ae1-140">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="b2ae1-140">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="b2ae1-141">Créez un Blazor Server projet avec un mécanisme d’authentification à l’aide de la commande suivante dans une interface de commande :</span><span class="sxs-lookup"><span data-stu-id="b2ae1-141">Create a new Blazor Server project with an authentication mechanism using the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="b2ae1-142">Les valeurs autorisées d’authentification (`{AUTHENTICATION}`) sont présentées dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="b2ae1-142">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="b2ae1-143">Mécanisme d’authentification</span><span class="sxs-lookup"><span data-stu-id="b2ae1-143">Authentication mechanism</span></span> | <span data-ttu-id="b2ae1-144">Description</span><span class="sxs-lookup"><span data-stu-id="b2ae1-144">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="b2ae1-145">`None` (valeur par défaut)</span><span class="sxs-lookup"><span data-stu-id="b2ae1-145">`None` (default)</span></span>         | <span data-ttu-id="b2ae1-146">Aucune authentification</span><span class="sxs-lookup"><span data-stu-id="b2ae1-146">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="b2ae1-147">Utilisateurs stockés dans l’application avec ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="b2ae1-147">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="b2ae1-148">Utilisateurs stockés dans [Azure ad B2C](xref:security/authentication/azure-ad-b2c)</span><span class="sxs-lookup"><span data-stu-id="b2ae1-148">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="b2ae1-149">Authentification d’organisation pour un seul locataire</span><span class="sxs-lookup"><span data-stu-id="b2ae1-149">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="b2ae1-150">Authentification d’organisation pour plusieurs locataires</span><span class="sxs-lookup"><span data-stu-id="b2ae1-150">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="b2ae1-151">Authentification Windows</span><span class="sxs-lookup"><span data-stu-id="b2ae1-151">Windows Authentication</span></span> |

<span data-ttu-id="b2ae1-152">À l’aide de l' `-o|--output` option, la commande utilise la valeur fournie pour l' `{APP NAME}` espace réservé à :</span><span class="sxs-lookup"><span data-stu-id="b2ae1-152">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="b2ae1-153">Créez un dossier pour le projet.</span><span class="sxs-lookup"><span data-stu-id="b2ae1-153">Create a folder for the project.</span></span>
* <span data-ttu-id="b2ae1-154">Nommez ce projet.</span><span class="sxs-lookup"><span data-stu-id="b2ae1-154">Name the project.</span></span>

<span data-ttu-id="b2ae1-155">Pour plus d'informations :</span><span class="sxs-lookup"><span data-stu-id="b2ae1-155">For more information:</span></span>

* <span data-ttu-id="b2ae1-156">Consultez la [`dotnet new`](/dotnet/core/tools/dotnet-new) commande dans le guide .net core.</span><span class="sxs-lookup"><span data-stu-id="b2ae1-156">See the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>
* <span data-ttu-id="b2ae1-157">Exécutez la commande help pour le Blazor Server modèle ( `blazorserver` ) dans une interface de commande :</span><span class="sxs-lookup"><span data-stu-id="b2ae1-157">Execute the help command for the Blazor Server template (`blazorserver`) in a command shell:</span></span>

  ```dotnetcli
  dotnet new blazorserver --help
  ```

---

## <a name="scaffold-no-locidentity"></a><span data-ttu-id="b2ae1-158">Destin Identity</span><span class="sxs-lookup"><span data-stu-id="b2ae1-158">Scaffold Identity</span></span>

<span data-ttu-id="b2ae1-159">Génération Identity de modèles automatique dans un Blazor Server projet :</span><span class="sxs-lookup"><span data-stu-id="b2ae1-159">Scaffold Identity into a Blazor Server project:</span></span>

* <span data-ttu-id="b2ae1-160">[Sans autorisation existante](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span><span class="sxs-lookup"><span data-stu-id="b2ae1-160">[Without existing authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span></span>
* <span data-ttu-id="b2ae1-161">[Avec autorisation](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span><span class="sxs-lookup"><span data-stu-id="b2ae1-161">[With authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b2ae1-162">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="b2ae1-162">Additional resources</span></span>

* [<span data-ttu-id="b2ae1-163">Démarrage rapide : Ajouter la connexion avec Microsoft à une application web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b2ae1-163">Quickstart: Add sign-in with Microsoft to an ASP.NET Core web app</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp)
* [<span data-ttu-id="b2ae1-164">Démarrage rapide : Protéger une API web ASP.NET Core avec la plateforme d’identités Microsoft</span><span class="sxs-lookup"><span data-stu-id="b2ae1-164">Quickstart: Protect an ASP.NET Core web API with Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-web-api)
