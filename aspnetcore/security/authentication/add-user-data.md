---
title: Ajouter, télécharger et supprimer les données utilisateur à Identity dans le cadre d’un projet ASP.NET Core
author: rick-anderson
description: Découvrez comment ajouter des données utilisateur personnalisées à Identity dans le cadre d’un projet ASP.NET Core. Supprimer les données par GDPR.
ms.author: riande
ms.date: 03/26/2020
ms.custom: mvc, seodec18
uid: security/authentication/add-user-data
ms.openlocfilehash: 76b83df22381429feab80056c36dbdac1e5f20c7
ms.sourcegitcommit: 1d8f1396ccc66a0c3fcb5e5f36ea29b50db6d92a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80501228"
---
# <a name="add-download-and-delete-custom-user-data-to-identity-in-an-aspnet-core-project"></a><span data-ttu-id="94e8f-104">Ajouter, télécharger et supprimer les données personnalisées de l’utilisateur à Identity dans le cadre d’un projet ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="94e8f-104">Add, download, and delete custom user data to Identity in an ASP.NET Core project</span></span>

<span data-ttu-id="94e8f-105">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="94e8f-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="94e8f-106">Cet article montre comment :</span><span class="sxs-lookup"><span data-stu-id="94e8f-106">This article shows how to:</span></span>

* <span data-ttu-id="94e8f-107">Ajoutez des données utilisateur personnalisées à une application web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="94e8f-107">Add custom user data to an ASP.NET Core web app.</span></span>
* <span data-ttu-id="94e8f-108">Marquez le modèle de <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute> données utilisateur personnalisé avec l’attribut de sorte qu’il est automatiquement disponible pour le téléchargement et la suppression.</span><span class="sxs-lookup"><span data-stu-id="94e8f-108">Mark the custom user data model with the <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute> attribute so it's automatically available for download and deletion.</span></span> <span data-ttu-id="94e8f-109">Rendre les données capables d’être téléchargées et supprimées permet de répondre aux exigences [du GDPR.](xref:security/gdpr)</span><span class="sxs-lookup"><span data-stu-id="94e8f-109">Making the data able to be downloaded and deleted helps meet [GDPR](xref:security/gdpr) requirements.</span></span>

<span data-ttu-id="94e8f-110">L’échantillon de projet est créé à partir d’une application Web Razor Pages, mais les instructions sont similaires pour une application web ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="94e8f-110">The project sample is created from a Razor Pages web app, but the instructions are similar for a ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="94e8f-111">[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data) ([comment télécharger](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="94e8f-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="94e8f-112">Prérequis</span><span class="sxs-lookup"><span data-stu-id="94e8f-112">Prerequisites</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [](~/includes/3.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [](~/includes/2.2-SDK.md)]

::: moniker-end

## <a name="create-a-razor-web-app"></a><span data-ttu-id="94e8f-113">Créer une application web Razor</span><span class="sxs-lookup"><span data-stu-id="94e8f-113">Create a Razor web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="94e8f-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="94e8f-114">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="94e8f-115">Dans Visual Studio, dans le menu **Fichier**, sélectionnez **Nouveau** > **Projet**.</span><span class="sxs-lookup"><span data-stu-id="94e8f-115">From the Visual Studio **File** menu, select **New** > **Project**.</span></span> <span data-ttu-id="94e8f-116">Nommez le projet **WebApp1** si vous le souhaitez correspondre à l’espace nom du code [d’échantillon de téléchargement.](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data)</span><span class="sxs-lookup"><span data-stu-id="94e8f-116">Name the project **WebApp1** if you want to it match the namespace of the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) code.</span></span>
* <span data-ttu-id="94e8f-117">Sélectionnez **ASP.NET’application** > Web de **base OK**</span><span class="sxs-lookup"><span data-stu-id="94e8f-117">Select **ASP.NET Core Web Application** > **OK**</span></span>
* <span data-ttu-id="94e8f-118">Sélectionnez **ASP.NET Core 3.0** dans le dropdown</span><span class="sxs-lookup"><span data-stu-id="94e8f-118">Select **ASP.NET Core 3.0** in the dropdown</span></span>
* <span data-ttu-id="94e8f-119">Sélectionnez **Web Application** > **OK**</span><span class="sxs-lookup"><span data-stu-id="94e8f-119">Select **Web Application** > **OK**</span></span>
* <span data-ttu-id="94e8f-120">Générez et exécutez le projet.</span><span class="sxs-lookup"><span data-stu-id="94e8f-120">Build and run the project.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="94e8f-121">Dans Visual Studio, dans le menu **Fichier**, sélectionnez **Nouveau** > **Projet**.</span><span class="sxs-lookup"><span data-stu-id="94e8f-121">From the Visual Studio **File** menu, select **New** > **Project**.</span></span> <span data-ttu-id="94e8f-122">Nommez le projet **WebApp1** si vous le souhaitez correspondre à l’espace nom du code [d’échantillon de téléchargement.](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data)</span><span class="sxs-lookup"><span data-stu-id="94e8f-122">Name the project **WebApp1** if you want to it match the namespace of the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) code.</span></span>
* <span data-ttu-id="94e8f-123">Sélectionnez **ASP.NET’application** > Web de **base OK**</span><span class="sxs-lookup"><span data-stu-id="94e8f-123">Select **ASP.NET Core Web Application** > **OK**</span></span>
* <span data-ttu-id="94e8f-124">Sélectionnez **ASP.NET Core 2.2** dans le dropdown</span><span class="sxs-lookup"><span data-stu-id="94e8f-124">Select **ASP.NET Core 2.2** in the dropdown</span></span>
* <span data-ttu-id="94e8f-125">Sélectionnez **Web Application** > **OK**</span><span class="sxs-lookup"><span data-stu-id="94e8f-125">Select **Web Application** > **OK**</span></span>
* <span data-ttu-id="94e8f-126">Générez et exécutez le projet.</span><span class="sxs-lookup"><span data-stu-id="94e8f-126">Build and run the project.</span></span>

::: moniker-end


# <a name="net-core-cli"></a>[<span data-ttu-id="94e8f-127">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="94e8f-127">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp -o WebApp1
```

---

## <a name="run-the-identity-scaffolder"></a><span data-ttu-id="94e8f-128">Exécuter l’échafaudage d’identité</span><span class="sxs-lookup"><span data-stu-id="94e8f-128">Run the Identity scaffolder</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="94e8f-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="94e8f-129">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="94e8f-130">De **Solution Explorer**, cliquez à droite sur le projet > **Ajouter** > **un nouvel article échafaudé**.</span><span class="sxs-lookup"><span data-stu-id="94e8f-130">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="94e8f-131">De la vitre gauche du dialogue **Ajouter échafaudage,** sélectionnez **Identity** > **Add**.</span><span class="sxs-lookup"><span data-stu-id="94e8f-131">From the left pane of the **Add Scaffold** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="94e8f-132">Dans le dialogue **Add Identity,** les options suivantes :</span><span class="sxs-lookup"><span data-stu-id="94e8f-132">In the **Add Identity** dialog, the following options:</span></span>
  * <span data-ttu-id="94e8f-133">Sélectionnez le fichier de mise en page existant */Pages/Shared/_Layout.cshtml*</span><span class="sxs-lookup"><span data-stu-id="94e8f-133">Select the existing layout  file  *~/Pages/Shared/_Layout.cshtml*</span></span>
  * <span data-ttu-id="94e8f-134">Sélectionnez les fichiers suivants pour remplacer :</span><span class="sxs-lookup"><span data-stu-id="94e8f-134">Select the following files to override:</span></span>
    * <span data-ttu-id="94e8f-135">**Compte/enregistrement**</span><span class="sxs-lookup"><span data-stu-id="94e8f-135">**Account/Register**</span></span>
    * <span data-ttu-id="94e8f-136">**Compte/Gestion/Index**</span><span class="sxs-lookup"><span data-stu-id="94e8f-136">**Account/Manage/Index**</span></span>
  * <span data-ttu-id="94e8f-137">Sélectionnez **+** le bouton pour créer une nouvelle **classe de contexte de données**.</span><span class="sxs-lookup"><span data-stu-id="94e8f-137">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="94e8f-138">Acceptez le type **(WebApp1.Models.WebApp1Context** si le projet est nommé **WebApp1**).</span><span class="sxs-lookup"><span data-stu-id="94e8f-138">Accept the type (**WebApp1.Models.WebApp1Context** if the project is named **WebApp1**).</span></span>
  * <span data-ttu-id="94e8f-139">Sélectionnez **+** le bouton pour créer une nouvelle **classe d’utilisateurs**.</span><span class="sxs-lookup"><span data-stu-id="94e8f-139">Select the **+** button to create a new **User class**.</span></span> <span data-ttu-id="94e8f-140">Acceptez le type (**WebApp1User** si le projet est nommé **WebApp1**) > **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="94e8f-140">Accept the type (**WebApp1User** if the project is named **WebApp1**) > **Add**.</span></span>
* <span data-ttu-id="94e8f-141">Sélectionnez **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="94e8f-141">Select **Add**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="94e8f-142">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="94e8f-142">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="94e8f-143">Si vous n’avez pas déjà installé l’échafaudage ASP.NET Core, installez-le dès maintenant :</span><span class="sxs-lookup"><span data-stu-id="94e8f-143">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="94e8f-144">Ajoutez une référence de paquet à [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) au fichier projet (.csproj).</span><span class="sxs-lookup"><span data-stu-id="94e8f-144">Add a package reference to [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) to the project (.csproj) file.</span></span> <span data-ttu-id="94e8f-145">Exécuter la commande suivante dans l’annuaire du projet:</span><span class="sxs-lookup"><span data-stu-id="94e8f-145">Run the following command in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

<span data-ttu-id="94e8f-146">Exécutez la commande suivante pour énumérer les options d’échafaudage d’identité :</span><span class="sxs-lookup"><span data-stu-id="94e8f-146">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

<span data-ttu-id="94e8f-147">Dans le dossier du projet, exécutez l’échafaudage Identity :</span><span class="sxs-lookup"><span data-stu-id="94e8f-147">In the project folder, run the Identity scaffolder:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -u WebApp1User -fi Account.Register;Account.Manage.Index
```

---

<span data-ttu-id="94e8f-148">Suivez l’instruction dans [Migrations, UseAuthentication et layout](xref:security/authentication/scaffold-identity#efm) pour effectuer les étapes suivantes :</span><span class="sxs-lookup"><span data-stu-id="94e8f-148">Follow the instruction in [Migrations, UseAuthentication, and layout](xref:security/authentication/scaffold-identity#efm) to perform the following steps:</span></span>

* <span data-ttu-id="94e8f-149">Créez une migration et mettez à jour la base de données.</span><span class="sxs-lookup"><span data-stu-id="94e8f-149">Create a migration and update the database.</span></span>
* <span data-ttu-id="94e8f-150">Ajout de `UseAuthentication` à `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="94e8f-150">Add `UseAuthentication` to `Startup.Configure`.</span></span>
* <span data-ttu-id="94e8f-151">Ajouter `<partial name="_LoginPartial" />` au fichier de mise en page.</span><span class="sxs-lookup"><span data-stu-id="94e8f-151">Add `<partial name="_LoginPartial" />` to the layout file.</span></span>
* <span data-ttu-id="94e8f-152">Testez l’application :</span><span class="sxs-lookup"><span data-stu-id="94e8f-152">Test the app:</span></span>
  * <span data-ttu-id="94e8f-153">Inscrire un utilisateur</span><span class="sxs-lookup"><span data-stu-id="94e8f-153">Register a user</span></span>
  * <span data-ttu-id="94e8f-154">Sélectionnez le nouveau nom d’utilisateur (à côté du lien **Logout).**</span><span class="sxs-lookup"><span data-stu-id="94e8f-154">Select the new user name (next to the **Logout** link).</span></span> <span data-ttu-id="94e8f-155">Vous devrez peut-être élargir la fenêtre ou sélectionner l’icône de barre de navigation pour afficher le nom d’utilisateur et d’autres liens.</span><span class="sxs-lookup"><span data-stu-id="94e8f-155">You might need to expand the window or select the navigation bar icon to show the user name and other links.</span></span>
  * <span data-ttu-id="94e8f-156">Sélectionnez l’onglet **Données personnelles.**</span><span class="sxs-lookup"><span data-stu-id="94e8f-156">Select the **Personal Data** tab.</span></span>
  * <span data-ttu-id="94e8f-157">Sélectionnez le bouton **Télécharger** et examinez le fichier *PersonalData.json.*</span><span class="sxs-lookup"><span data-stu-id="94e8f-157">Select the **Download** button and examined the *PersonalData.json* file.</span></span>
  * <span data-ttu-id="94e8f-158">Testez le bouton **Supprimer,** qui supprime l’enregistrement sur l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="94e8f-158">Test the **Delete** button, which deletes the logged on user.</span></span>

## <a name="add-custom-user-data-to-the-identity-db"></a><span data-ttu-id="94e8f-159">Ajouter des données personnalisées aux données d’identité</span><span class="sxs-lookup"><span data-stu-id="94e8f-159">Add custom user data to the Identity DB</span></span>

<span data-ttu-id="94e8f-160">Mettre `IdentityUser` à jour la classe dérivée avec des propriétés personnalisées.</span><span class="sxs-lookup"><span data-stu-id="94e8f-160">Update the `IdentityUser` derived class with custom properties.</span></span> <span data-ttu-id="94e8f-161">Si vous avez nommé le projet WebApp1, le fichier est nommé *Areas/Identity/Data/WebApp1User.cs*.</span><span class="sxs-lookup"><span data-stu-id="94e8f-161">If you named the project WebApp1, the file is named *Areas/Identity/Data/WebApp1User.cs*.</span></span> <span data-ttu-id="94e8f-162">Mettre à jour le fichier avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="94e8f-162">Update the file with the following code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

<span data-ttu-id="94e8f-163">Les propriétés avec l’attribut [PersonalData](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute) sont les :</span><span class="sxs-lookup"><span data-stu-id="94e8f-163">Properties with the [PersonalData](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute) attribute are:</span></span>

* <span data-ttu-id="94e8f-164">Supprimé lorsque les *appels Zones/Identité/Pages/Account/Manage/DeletePersonalData.cshtml* Razor Page `UserManager.Delete`.</span><span class="sxs-lookup"><span data-stu-id="94e8f-164">Deleted when the *Areas/Identity/Pages/Account/Manage/DeletePersonalData.cshtml* Razor Page calls `UserManager.Delete`.</span></span>
* <span data-ttu-id="94e8f-165">Inclus dans les données téléchargées par les *zones/identité/pages/Compte/Manage/DownloadPersonalData.cshtml* Razor Page.</span><span class="sxs-lookup"><span data-stu-id="94e8f-165">Included in the downloaded data by the *Areas/Identity/Pages/Account/Manage/DownloadPersonalData.cshtml* Razor Page.</span></span>

### <a name="update-the-accountmanageindexcshtml-page"></a><span data-ttu-id="94e8f-166">Mettre à jour la page Compte/Gérer/Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="94e8f-166">Update the Account/Manage/Index.cshtml page</span></span>

<span data-ttu-id="94e8f-167">Mettre `InputModel` à jour le code in *Areas/Identity/Pages/Account/Manage/Index.cshtml.cs* avec le code mis en évidence suivant :</span><span class="sxs-lookup"><span data-stu-id="94e8f-167">Update the `InputModel` in *Areas/Identity/Pages/Account/Manage/Index.cshtml.cs* with the following highlighted code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=24-32,48-49,96-104,106)]

<span data-ttu-id="94e8f-168">Mettre à jour les *domaines/identité/pages/Compte/Gestion/Index.cshtml* avec la balisage mis en évidence ci-après :</span><span class="sxs-lookup"><span data-stu-id="94e8f-168">Update the *Areas/Identity/Pages/Account/Manage/Index.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=18-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=28-36,63-64,98-106,119)]

<span data-ttu-id="94e8f-169">Mettre à jour les *domaines/identité/pages/Compte/Gestion/Index.cshtml* avec la balisage mis en évidence ci-après :</span><span class="sxs-lookup"><span data-stu-id="94e8f-169">Update the *Areas/Identity/Pages/Account/Manage/Index.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=35-42)]

::: moniker-end

### <a name="update-the-accountregistercshtml-page"></a><span data-ttu-id="94e8f-170">Mettre à jour la page Compte/Register.cshtml</span><span class="sxs-lookup"><span data-stu-id="94e8f-170">Update the Account/Register.cshtml page</span></span>

<span data-ttu-id="94e8f-171">Mettre `InputModel` à jour le code in *Areas/Identity/Pages/Account/Register.cshtml.cs* avec le code mis en évidence suivant :</span><span class="sxs-lookup"><span data-stu-id="94e8f-171">Update the `InputModel` in *Areas/Identity/Pages/Account/Register.cshtml.cs* with the following highlighted code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=30-38,70-71)]

<span data-ttu-id="94e8f-172">Mettre à jour les *zones/identité/pages/Compte/Register.cshtml* avec la balisage mis en évidence ci-après :</span><span class="sxs-lookup"><span data-stu-id="94e8f-172">Update the *Areas/Identity/Pages/Account/Register.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=28-36,67,66)]

<span data-ttu-id="94e8f-173">Mettre à jour les *zones/identité/pages/Compte/Register.cshtml* avec la balisage mis en évidence ci-après :</span><span class="sxs-lookup"><span data-stu-id="94e8f-173">Update the *Areas/Identity/Pages/Account/Register.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end


<span data-ttu-id="94e8f-174">Créez le projet.</span><span class="sxs-lookup"><span data-stu-id="94e8f-174">Build the project.</span></span>

### <a name="add-a-migration-for-the-custom-user-data"></a><span data-ttu-id="94e8f-175">Ajouter une migration pour les données utilisateur personnalisées</span><span class="sxs-lookup"><span data-stu-id="94e8f-175">Add a migration for the custom user data</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="94e8f-176">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="94e8f-176">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="94e8f-177">Dans la **console**Visual Studio Package Manager :</span><span class="sxs-lookup"><span data-stu-id="94e8f-177">In the Visual Studio **Package Manager Console**:</span></span>

```powershell
Add-Migration CustomUserData
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="94e8f-178">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="94e8f-178">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CustomUserData
dotnet ef database update
```

---

## <a name="test-create-view-download-delete-custom-user-data"></a><span data-ttu-id="94e8f-179">Testez, visualiser, télécharger, supprimer les données personnalisées de l’utilisateur</span><span class="sxs-lookup"><span data-stu-id="94e8f-179">Test create, view, download, delete custom user data</span></span>

<span data-ttu-id="94e8f-180">Testez l’application :</span><span class="sxs-lookup"><span data-stu-id="94e8f-180">Test the app:</span></span>

* <span data-ttu-id="94e8f-181">Enregistrez un nouvel utilisateur.</span><span class="sxs-lookup"><span data-stu-id="94e8f-181">Register a new user.</span></span>
* <span data-ttu-id="94e8f-182">Afficher les données personnalisées de l’utilisateur sur la `/Identity/Account/Manage` page.</span><span class="sxs-lookup"><span data-stu-id="94e8f-182">View the custom user data on the `/Identity/Account/Manage` page.</span></span>
* <span data-ttu-id="94e8f-183">Téléchargez et visualisons `/Identity/Account/Manage/PersonalData` les données personnelles des utilisateurs à partir de la page.</span><span class="sxs-lookup"><span data-stu-id="94e8f-183">Download and view the users personal data from the `/Identity/Account/Manage/PersonalData` page.</span></span>

## <a name="add-claims-to-identity-using-iuserclaimsprincipalfactoryapplicationuser"></a><span data-ttu-id="94e8f-184">Ajouter des revendications à Identity à l’aide d’IUserClaimsPrincipalFactory<ApplicationUser></span><span class="sxs-lookup"><span data-stu-id="94e8f-184">Add claims to Identity using IUserClaimsPrincipalFactory<ApplicationUser></span></span>

<span data-ttu-id="94e8f-185">Des réclamations supplémentaires peuvent être ajoutées à `IUserClaimsPrincipalFactory<T>` ASP.NET’identité de base en utilisant l’interface.</span><span class="sxs-lookup"><span data-stu-id="94e8f-185">Additional claims can be added to ASP.NET Core Identity by using the `IUserClaimsPrincipalFactory<T>` interface.</span></span> <span data-ttu-id="94e8f-186">Cette classe peut être ajoutée `Startup.ConfigureServices` à l’application dans la méthode.</span><span class="sxs-lookup"><span data-stu-id="94e8f-186">This class can be added to the app in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="94e8f-187">Ajoutez la mise en œuvre personnalisée de la classe comme suit :</span><span class="sxs-lookup"><span data-stu-id="94e8f-187">Add the custom implementation of the class as follows:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    services.AddScoped<IUserClaimsPrincipalFactory<ApplicationUser>, 
        AdditionalUserClaimsPrincipalFactory>();
```

<span data-ttu-id="94e8f-188">Le code de `ApplicationUser` démonstration utilise la classe.</span><span class="sxs-lookup"><span data-stu-id="94e8f-188">The demo code uses the `ApplicationUser` class.</span></span> <span data-ttu-id="94e8f-189">Cette classe `IsAdmin` ajoute une propriété qui est utilisée pour ajouter la réclamation supplémentaire.</span><span class="sxs-lookup"><span data-stu-id="94e8f-189">This class adds an `IsAdmin` property which is used to add the additional claim.</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public bool IsAdmin { get; set; }
}
```

<span data-ttu-id="94e8f-190">`AdditionalUserClaimsPrincipalFactory` implémente l'interface `UserClaimsPrincipalFactory`.</span><span class="sxs-lookup"><span data-stu-id="94e8f-190">The `AdditionalUserClaimsPrincipalFactory` implements the `UserClaimsPrincipalFactory` interface.</span></span> <span data-ttu-id="94e8f-191">Une nouvelle revendication de `ClaimsPrincipal`rôle est ajoutée à la .</span><span class="sxs-lookup"><span data-stu-id="94e8f-191">A new role claim is added to the `ClaimsPrincipal`.</span></span>

```csharp
public class AdditionalUserClaimsPrincipalFactory 
        : UserClaimsPrincipalFactory<ApplicationUser, IdentityRole>
{
    public AdditionalUserClaimsPrincipalFactory( 
        UserManager<ApplicationUser> userManager,
        RoleManager<IdentityRole> roleManager, 
        IOptions<IdentityOptions> optionsAccessor) 
        : base(userManager, roleManager, optionsAccessor)
    {}

    public async override Task<ClaimsPrincipal> CreateAsync(ApplicationUser user)
    {
        var principal = await base.CreateAsync(user);
        var identity = (ClaimsIdentity)principal.Identity;

        var claims = new List<Claim>();
        if (user.IsAdmin)
        {
            claims.Add(new Claim(JwtClaimTypes.Role, "admin"));
        }
        else
        {
            claims.Add(new Claim(JwtClaimTypes.Role, "user"));
        }

        identity.AddClaims(claims);
        return principal;
    }
}
```

<span data-ttu-id="94e8f-192">La réclamation supplémentaire peut ensuite être utilisée dans l’application.</span><span class="sxs-lookup"><span data-stu-id="94e8f-192">The additional claim can then be used in the app.</span></span> <span data-ttu-id="94e8f-193">Dans une page `IAuthorizationService` Razor, l’instance peut être utilisée pour accéder à la valeur de réclamation.</span><span class="sxs-lookup"><span data-stu-id="94e8f-193">In a Razor Page, the `IAuthorizationService` instance can be used to access the claim value.</span></span>

```cshtml
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService

@if ((await AuthorizationService.AuthorizeAsync(User, "IsAdmin")).Succeeded)
{
    <ul class="mr-auto navbar-nav">
        <li class="nav-item">
            <a class="nav-link" asp-controller="Admin" asp-action="Index">ADMIN</a>
        </li>
    </ul>
}
```
