---
title: Articles basés sur des projets de ASP.NET Core créés avec des comptes d’utilisateur individuels
author: rick-anderson
description: Découvrez des articles basés sur des projets ASP.NET Core créés avec des comptes d’utilisateur individuels.
ms.author: riande
ms.date: 12/11/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/individual
ms.openlocfilehash: 26f53b6452e307bbd0816c1a3604f38b04c6af15
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82768648"
---
# <a name="articles-based-on-aspnet-core-projects-created-with-individual-user-accounts"></a><span data-ttu-id="8fe00-103">Articles basés sur des projets de ASP.NET Core créés avec des comptes d’utilisateur individuels</span><span class="sxs-lookup"><span data-stu-id="8fe00-103">Articles based on ASP.NET Core projects created with individual user accounts</span></span>

<span data-ttu-id="8fe00-104">ASP.NET Core identité est incluse dans les modèles de projet dans Visual Studio avec l’option « comptes d’utilisateur individuels ».</span><span class="sxs-lookup"><span data-stu-id="8fe00-104">ASP.NET Core Identity is included in project templates in Visual Studio with the "Individual User Accounts" option.</span></span>

<span data-ttu-id="8fe00-105">Les modèles d’authentification sont disponibles dans CLI .NET Core `-au Individual`avec :</span><span class="sxs-lookup"><span data-stu-id="8fe00-105">The authentication templates are available in .NET Core CLI with `-au Individual`:</span></span>

::: moniker range=">= aspnetcore-2.1"

```dotnetcli
dotnet new mvc -au Individual
dotnet new webapp -au Individual
```

::: moniker-end

::: moniker range="= aspnetcore-2.0"

```dotnetcli
dotnet new mvc -au Individual
dotnet new razor -au Individual
```

::: moniker-end

<span data-ttu-id="8fe00-106">Consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore/issues/5833) pour l’authentification de l’API Web.</span><span class="sxs-lookup"><span data-stu-id="8fe00-106">See [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/5833) for web API authentication.</span></span>

<a name="no"></a>

## <a name="no-authentication"></a><span data-ttu-id="8fe00-107">Aucune authentification</span><span class="sxs-lookup"><span data-stu-id="8fe00-107">No Authentication</span></span>

<span data-ttu-id="8fe00-108">L’authentification est spécifiée dans le CLI .NET Core avec `-au` l’option.</span><span class="sxs-lookup"><span data-stu-id="8fe00-108">Authentication is specified in the .NET Core CLI with the `-au` option.</span></span> <span data-ttu-id="8fe00-109">Dans Visual Studio, la boîte de dialogue **modifier l’authentification** est disponible pour les nouvelles applications Web.</span><span class="sxs-lookup"><span data-stu-id="8fe00-109">In Visual Studio, the **Change Authentication** dialog is available for new web applications.</span></span> <span data-ttu-id="8fe00-110">La valeur par défaut pour les nouvelles applications Web dans Visual Studio n’est **pas une authentification**.</span><span class="sxs-lookup"><span data-stu-id="8fe00-110">The default for new web apps in Visual Studio is **No Authentication**.</span></span>

<span data-ttu-id="8fe00-111">Projets créés sans authentification :</span><span class="sxs-lookup"><span data-stu-id="8fe00-111">Projects created with no authentication:</span></span>

* <span data-ttu-id="8fe00-112">Ne contiennent pas les pages Web et l’interface utilisateur pour la connexion et la déconnexion.</span><span class="sxs-lookup"><span data-stu-id="8fe00-112">Don't contain web pages and UI to sign in and sign out.</span></span>
* <span data-ttu-id="8fe00-113">Ne contiennent pas de code d’authentification.</span><span class="sxs-lookup"><span data-stu-id="8fe00-113">Don't contain authentication code.</span></span>

<a name="win"></a>

## <a name="windows-authentication"></a><span data-ttu-id="8fe00-114">Authentification Windows</span><span class="sxs-lookup"><span data-stu-id="8fe00-114">Windows Authentication</span></span>

<span data-ttu-id="8fe00-115">L’authentification Windows est spécifiée pour les nouvelles applications Web dans le CLI .NET Core `-au Windows` avec l’option.</span><span class="sxs-lookup"><span data-stu-id="8fe00-115">Windows Authentication is specified for new web apps in the .NET Core CLI with the `-au Windows` option.</span></span> <span data-ttu-id="8fe00-116">Dans Visual Studio, la boîte de dialogue **modifier l’authentification** fournit les options **d’authentification Windows** .</span><span class="sxs-lookup"><span data-stu-id="8fe00-116">In Visual Studio, the **Change Authentication** dialog provides the **Windows Authentication** options.</span></span>

<span data-ttu-id="8fe00-117">Si l’authentification Windows est sélectionnée, l’application est configurée pour utiliser le [module IIS d’authentification Windows](xref:host-and-deploy/iis/modules).</span><span class="sxs-lookup"><span data-stu-id="8fe00-117">If Windows Authentication is selected, the app is configured to use the [Windows Authentication IIS module](xref:host-and-deploy/iis/modules).</span></span> <span data-ttu-id="8fe00-118">L’authentification Windows est destinée aux sites Web intranet.</span><span class="sxs-lookup"><span data-stu-id="8fe00-118">Windows Authentication is intended for Intranet web sites.</span></span>

## <a name="dotnet-new-webapp-authentication-options"></a><span data-ttu-id="8fe00-119">nouvelles options d’authentification webapp de dotnet</span><span class="sxs-lookup"><span data-stu-id="8fe00-119">dotnet new webapp authentication options</span></span>

<span data-ttu-id="8fe00-120">Le tableau suivant présente les options d’authentification disponibles pour les nouvelles applications Web :</span><span class="sxs-lookup"><span data-stu-id="8fe00-120">The following table shows the authentication options available for new web apps:</span></span>

| <span data-ttu-id="8fe00-121">Option</span><span class="sxs-lookup"><span data-stu-id="8fe00-121">Option</span></span> | <span data-ttu-id="8fe00-122">Type d'authentification</span><span class="sxs-lookup"><span data-stu-id="8fe00-122">Type of authentication</span></span> | <span data-ttu-id="8fe00-123">Lien vers plus d’informations</span><span class="sxs-lookup"><span data-stu-id="8fe00-123">Link for more information</span></span> |
 | ----------------- | ------------ | ---------- |
| <span data-ttu-id="8fe00-124">None</span><span class="sxs-lookup"><span data-stu-id="8fe00-124">None</span></span>            |  <span data-ttu-id="8fe00-125">Aucune authentification</span><span class="sxs-lookup"><span data-stu-id="8fe00-125">No authentication</span></span> | | 
| <span data-ttu-id="8fe00-126">Individuel</span><span class="sxs-lookup"><span data-stu-id="8fe00-126">Individual</span></span>      |  <span data-ttu-id="8fe00-127">Authentification individuelle</span><span class="sxs-lookup"><span data-stu-id="8fe00-127">Individual authentication</span></span> | <xref:security/authentication/identity>
| <span data-ttu-id="8fe00-128">IndividualB2C</span><span class="sxs-lookup"><span data-stu-id="8fe00-128">IndividualB2C</span></span>   |  <span data-ttu-id="8fe00-129">Authentification individuelle hébergée dans le Cloud avec Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="8fe00-129">Cloud-hosted individual authentication with Azure AD B2C</span></span> | [<span data-ttu-id="8fe00-130">Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="8fe00-130">Azure AD B2C</span></span>](/azure/active-directory-b2c/) |
| <span data-ttu-id="8fe00-131">SingleOrg</span><span class="sxs-lookup"><span data-stu-id="8fe00-131">SingleOrg</span></span>       |  <span data-ttu-id="8fe00-132">Authentification d’organisation pour un seul locataire</span><span class="sxs-lookup"><span data-stu-id="8fe00-132">Organizational authentication for a single tenant</span></span> | [<span data-ttu-id="8fe00-133">Azure AD</span><span class="sxs-lookup"><span data-stu-id="8fe00-133">Azure AD</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| <span data-ttu-id="8fe00-134">MultiOrg</span><span class="sxs-lookup"><span data-stu-id="8fe00-134">MultiOrg</span></span>        |  <span data-ttu-id="8fe00-135">Authentification d’organisation pour plusieurs locataires</span><span class="sxs-lookup"><span data-stu-id="8fe00-135">Organizational authentication for multiple tenants</span></span> | [<span data-ttu-id="8fe00-136">Azure AD</span><span class="sxs-lookup"><span data-stu-id="8fe00-136">Azure AD</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| <span data-ttu-id="8fe00-137"> Windows</span><span class="sxs-lookup"><span data-stu-id="8fe00-137">Windows</span></span>         |  <span data-ttu-id="8fe00-138">Authentification Windows</span><span class="sxs-lookup"><span data-stu-id="8fe00-138">Windows authentication</span></span> | [<span data-ttu-id="8fe00-139">Authentification Windows</span><span class="sxs-lookup"><span data-stu-id="8fe00-139">Windows Authentication</span></span>](xref:security/authentication/windowsauth)

## <a name="visual-studio-new-webapp-authentication-options"></a><span data-ttu-id="8fe00-140">Nouvelles options d’authentification webapp de Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8fe00-140">Visual Studio new webapp authentication options</span></span>

<span data-ttu-id="8fe00-141">Le tableau suivant présente les options d’authentification disponibles lors de la création d’une nouvelle application Web avec Visual Studio :</span><span class="sxs-lookup"><span data-stu-id="8fe00-141">The following table shows the authentication options available when creating a new web app with Visual Studio:</span></span>

| <span data-ttu-id="8fe00-142">Option</span><span class="sxs-lookup"><span data-stu-id="8fe00-142">Option</span></span> | <span data-ttu-id="8fe00-143">Type d'authentification</span><span class="sxs-lookup"><span data-stu-id="8fe00-143">Type of authentication</span></span> | <span data-ttu-id="8fe00-144">Lien vers plus d’informations</span><span class="sxs-lookup"><span data-stu-id="8fe00-144">Link for more information</span></span> |
 | ----------------- | ------------ | ---------- |
| <span data-ttu-id="8fe00-145">None</span><span class="sxs-lookup"><span data-stu-id="8fe00-145">None</span></span>            |  <span data-ttu-id="8fe00-146">Aucune authentification</span><span class="sxs-lookup"><span data-stu-id="8fe00-146">No authentication</span></span> | | 
| <span data-ttu-id="8fe00-147">Comptes d’utilisateur individuels/stocker les comptes d’utilisateur dans l’application</span><span class="sxs-lookup"><span data-stu-id="8fe00-147">Individual User Accounts / Store user accounts in-app</span></span> |  <span data-ttu-id="8fe00-148">Authentification individuelle</span><span class="sxs-lookup"><span data-stu-id="8fe00-148">Individual authentication</span></span> | <xref:security/authentication/identity> |
| <span data-ttu-id="8fe00-149">Comptes d’utilisateur individuels/se connecter à un magasin d’utilisateurs existant dans le Cloud</span><span class="sxs-lookup"><span data-stu-id="8fe00-149">Individual User Accounts / Connect to an existing user store in the cloud</span></span> |  <span data-ttu-id="8fe00-150">Authentification individuelle hébergée dans le Cloud avec Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="8fe00-150">Cloud-hosted individual authentication with Azure AD B2C</span></span> | [<span data-ttu-id="8fe00-151">Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="8fe00-151">Azure AD B2C</span></span>](/azure/active-directory-b2c/) |
| <span data-ttu-id="8fe00-152">Cloud professionnel ou scolaire/organisation unique</span><span class="sxs-lookup"><span data-stu-id="8fe00-152">Work or School Cloud / Single Org</span></span>  |  <span data-ttu-id="8fe00-153">Authentification d’organisation pour un seul locataire</span><span class="sxs-lookup"><span data-stu-id="8fe00-153">Organizational authentication for a single tenant</span></span> | [<span data-ttu-id="8fe00-154">Azure AD</span><span class="sxs-lookup"><span data-stu-id="8fe00-154">Azure AD</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| <span data-ttu-id="8fe00-155">Cloud professionnel ou scolaire/organisation multiple</span><span class="sxs-lookup"><span data-stu-id="8fe00-155">Work or School Cloud / Multiple Org</span></span> |  <span data-ttu-id="8fe00-156">Authentification d’organisation pour plusieurs locataires</span><span class="sxs-lookup"><span data-stu-id="8fe00-156">Organizational authentication for multiple tenants</span></span> | [<span data-ttu-id="8fe00-157">Azure AD</span><span class="sxs-lookup"><span data-stu-id="8fe00-157">Azure AD</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| <span data-ttu-id="8fe00-158"> Windows</span><span class="sxs-lookup"><span data-stu-id="8fe00-158">Windows</span></span>         |  <span data-ttu-id="8fe00-159">Authentification Windows</span><span class="sxs-lookup"><span data-stu-id="8fe00-159">Windows authentication</span></span> | [<span data-ttu-id="8fe00-160">Authentification Windows</span><span class="sxs-lookup"><span data-stu-id="8fe00-160">Windows Authentication</span></span>](xref:security/authentication/windowsauth)

## <a name="additional-resources"></a><span data-ttu-id="8fe00-161">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="8fe00-161">Additional resources</span></span>

<span data-ttu-id="8fe00-162">Les articles suivants montrent comment utiliser le code généré dans ASP.NET Core modèles qui utilisent des comptes d’utilisateur individuels :</span><span class="sxs-lookup"><span data-stu-id="8fe00-162">The following articles show how to use the code generated in ASP.NET Core templates that use individual user accounts:</span></span>

* [<span data-ttu-id="8fe00-163">Account confirmation and password recovery in ASP.NET Core (Confirmation de compte et récupération de mot de passe dans ASP.NET Core)</span><span class="sxs-lookup"><span data-stu-id="8fe00-163">Account confirmation and password recovery in ASP.NET Core</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="8fe00-164">Créer une application ASP.NET Core avec les données utilisateur protégées par l’autorisation</span><span class="sxs-lookup"><span data-stu-id="8fe00-164">Create an ASP.NET Core app with user data protected by authorization</span></span>](xref:security/authorization/secure-data)
