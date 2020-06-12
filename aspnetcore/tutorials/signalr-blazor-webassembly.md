---
title: Utiliser ASP.NET Core SignalR avec Blazor webassembly
author: guardrex
description: Créer une application de conversation qui utilise ASP.NET Core SignalR avec Blazor webassembly.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: 720f534426cc0e2b32778e49050c7f7d75ecd60d
ms.sourcegitcommit: 6371114344a5f4fbc5d4a119b0be1ad3762e0216
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2020
ms.locfileid: "84679590"
---
# <a name="use-aspnet-core-signalr-with-blazor-webassembly"></a><span data-ttu-id="2d6f6-103">Utiliser ASP.NET Core SignalR avec Blazor webassembly</span><span class="sxs-lookup"><span data-stu-id="2d6f6-103">Use ASP.NET Core SignalR with Blazor WebAssembly</span></span>

<span data-ttu-id="2d6f6-104">Par [Daniel Roth](https://github.com/danroth27) et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="2d6f6-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="2d6f6-105">Ce didacticiel enseigne les bases de la création d’une application en temps réel à l’aide SignalR de avec Blazor webassembly.</span><span class="sxs-lookup"><span data-stu-id="2d6f6-105">This tutorial teaches the basics of building a real-time app using SignalR with Blazor WebAssembly.</span></span> <span data-ttu-id="2d6f6-106">Vous allez apprendre à effectuer les actions suivantes :</span><span class="sxs-lookup"><span data-stu-id="2d6f6-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="2d6f6-107">Créer un Blazor projet d’application hébergée Webassembly</span><span class="sxs-lookup"><span data-stu-id="2d6f6-107">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="2d6f6-108">Ajouter la SignalR bibliothèque cliente</span><span class="sxs-lookup"><span data-stu-id="2d6f6-108">Add the SignalR client library</span></span>
> * <span data-ttu-id="2d6f6-109">Ajouter un SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="2d6f6-109">Add a SignalR hub</span></span>
> * <span data-ttu-id="2d6f6-110">Ajouter des SignalR services et un point de terminaison pour le SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="2d6f6-110">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="2d6f6-111">Ajouter Razor du code de composant pour la conversation</span><span class="sxs-lookup"><span data-stu-id="2d6f6-111">Add Razor component code for chat</span></span>

<span data-ttu-id="2d6f6-112">À la fin de ce didacticiel, vous disposerez d’une application de conversation de travail.</span><span class="sxs-lookup"><span data-stu-id="2d6f6-112">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="2d6f6-113">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2d6f6-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="2d6f6-114">Prérequis</span><span class="sxs-lookup"><span data-stu-id="2d6f6-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2d6f6-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2d6f6-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2d6f6-116">[Visual Studio 2019 16,6 ou version ultérieure](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) avec la charge de travail de **développement ASP.net et Web**</span><span class="sxs-lookup"><span data-stu-id="2d6f6-116">[Visual Studio 2019 16.6 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="2d6f6-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2d6f6-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2d6f6-118">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="2d6f6-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="2d6f6-119">Visual Studio pour Mac version 8,6 ou ultérieure</span><span class="sxs-lookup"><span data-stu-id="2d6f6-119">Visual Studio for Mac version 8.6 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="2d6f6-120">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="2d6f6-120">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

## <a name="create-a-hosted-blazor-webassembly-app-project"></a><span data-ttu-id="2d6f6-121">Créer un Blazor projet d’application Webassembly hébergé</span><span class="sxs-lookup"><span data-stu-id="2d6f6-121">Create a hosted Blazor WebAssembly app project</span></span>

<span data-ttu-id="2d6f6-122">Suivez les instructions de votre choix d’outils :</span><span class="sxs-lookup"><span data-stu-id="2d6f6-122">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2d6f6-123">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2d6f6-123">Visual Studio</span></span>](#tab/visual-studio)

> [!NOTE]
> <span data-ttu-id="2d6f6-124">Visual Studio 16,6 ou version ultérieure et kit SDK .NET Core 3.1.300 ou version ultérieure sont requis.</span><span class="sxs-lookup"><span data-stu-id="2d6f6-124">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

1. <span data-ttu-id="2d6f6-125">Créez un projet.</span><span class="sxs-lookup"><span data-stu-id="2d6f6-125">Create a new project.</span></span>

1. <span data-ttu-id="2d6f6-126">Sélectionnez \*\* Blazor application\*\* , puis sélectionnez **suivant**.</span><span class="sxs-lookup"><span data-stu-id="2d6f6-126">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="2d6f6-127">Tapez « BlazorSignalRApp » dans le champ **nom du projet** .</span><span class="sxs-lookup"><span data-stu-id="2d6f6-127">Type "BlazorSignalRApp" in the **Project name** field.</span></span> <span data-ttu-id="2d6f6-128">Confirmez que l’entrée d' **emplacement** est correcte ou indiquez un emplacement pour le projet.</span><span class="sxs-lookup"><span data-stu-id="2d6f6-128">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="2d6f6-129">Sélectionnez **Create** (Créer).</span><span class="sxs-lookup"><span data-stu-id="2d6f6-129">Select **Create**.</span></span>

1. <span data-ttu-id="2d6f6-130">Choisissez le modèle d' \*\* Blazor application webassembly\*\* .</span><span class="sxs-lookup"><span data-stu-id="2d6f6-130">Choose the **Blazor WebAssembly App** template.</span></span>

1. <span data-ttu-id="2d6f6-131">Sous **avancé**, activez la case à cocher **ASP.net Core hébergé** .</span><span class="sxs-lookup"><span data-stu-id="2d6f6-131">Under **Advanced**, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="2d6f6-132">Sélectionnez **Create** (Créer).</span><span class="sxs-lookup"><span data-stu-id="2d6f6-132">Select **Create**.</span></span>

> [!NOTE]
> <span data-ttu-id="2d6f6-133">Si vous avez mis à niveau ou installé une nouvelle version de Visual Studio et que le Blazor modèle Webassembly n’apparaît pas dans l’interface utilisateur vs, réinstallez le modèle à l’aide de la `dotnet new` commande indiquée précédemment.</span><span class="sxs-lookup"><span data-stu-id="2d6f6-133">If you upgraded or installed a new version of Visual Studio and the Blazor WebAssembly template doesn't appear in the VS UI, reinstall the template using the `dotnet new` command shown previously.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2d6f6-134">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2d6f6-134">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="2d6f6-135">Dans une interface de commande, exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="2d6f6-135">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. <span data-ttu-id="2d6f6-136">Dans Visual Studio Code, ouvrez le dossier du projet de l’application.</span><span class="sxs-lookup"><span data-stu-id="2d6f6-136">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="2d6f6-137">Quand la boîte de dialogue semble ajouter des éléments multimédias pour générer et déboguer l’application, sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="2d6f6-137">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="2d6f6-138">Visual Studio Code ajoute automatiquement le dossier *. vscode* avec les *launch.js* générés sur et *tasks.jssur* les fichiers.</span><span class="sxs-lookup"><span data-stu-id="2d6f6-138">Visual Studio Code automatically adds the *.vscode* folder with generated *launch.json* and *tasks.json* files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2d6f6-139">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="2d6f6-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="2d6f6-140">Installez la dernière version de [Visual Studio pour Mac](https://visualstudio.microsoft.com/vs/mac/) et procédez comme suit :</span><span class="sxs-lookup"><span data-stu-id="2d6f6-140">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="2d6f6-141">Sélectionnez **fichier**  >  **nouvelle solution** ou créer un **nouveau** projet à partir de la **fenêtre démarrer**.</span><span class="sxs-lookup"><span data-stu-id="2d6f6-141">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="2d6f6-142">Dans la barre latérale, sélectionnez application **Web et console**  >  **App**.</span><span class="sxs-lookup"><span data-stu-id="2d6f6-142">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="2d6f6-143">Choisissez le modèle d' \*\* Blazor application webassembly\*\* .</span><span class="sxs-lookup"><span data-stu-id="2d6f6-143">Choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="2d6f6-144">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="2d6f6-144">Select **Next**.</span></span>

   <span data-ttu-id="2d6f6-145">Vérifiez les configurations suivantes :</span><span class="sxs-lookup"><span data-stu-id="2d6f6-145">Confirm the following configurations:</span></span>

   * <span data-ttu-id="2d6f6-146">**Framework cible** défini sur **.net Core 3,1**.</span><span class="sxs-lookup"><span data-stu-id="2d6f6-146">**Target Framework** set to **.NET Core 3.1**.</span></span>
   * <span data-ttu-id="2d6f6-147">**L’authentification** est définie sur **aucune authentification**.</span><span class="sxs-lookup"><span data-stu-id="2d6f6-147">**Authentication** set to **No Authentication**.</span></span>

   <span data-ttu-id="2d6f6-148">Activez la case à cocher **ASP.net Core hébergé** .</span><span class="sxs-lookup"><span data-stu-id="2d6f6-148">Select the **ASP.NET Core Hosted** check box.</span></span>

   <span data-ttu-id="2d6f6-149">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="2d6f6-149">Select **Next**.</span></span>

1. <span data-ttu-id="2d6f6-150">Dans le champ **nom du projet** , nommez l’application `BlazorSignalRApp` .</span><span class="sxs-lookup"><span data-stu-id="2d6f6-150">In the **Project Name** field, name the app `BlazorSignalRApp`.</span></span> <span data-ttu-id="2d6f6-151">Sélectionnez **Create** (Créer).</span><span class="sxs-lookup"><span data-stu-id="2d6f6-151">Select **Create**.</span></span>

   <span data-ttu-id="2d6f6-152">Si une invite s’affiche pour faire confiance au certificat de développement, approuvez le certificat et continuez.</span><span class="sxs-lookup"><span data-stu-id="2d6f6-152">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="2d6f6-153">Les mots de passe utilisateur et trousseau sont requis pour approuver le certificat.</span><span class="sxs-lookup"><span data-stu-id="2d6f6-153">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="2d6f6-154">Ouvrez le projet en accédant au dossier du projet et en ouvrant le fichier solution du projet (*. sln*).</span><span class="sxs-lookup"><span data-stu-id="2d6f6-154">Open the project by navigating to the project folder and opening the project's solution file (*.sln*).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="2d6f6-155">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="2d6f6-155">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="2d6f6-156">Dans une interface de commande, exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="2d6f6-156">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="2d6f6-157">Ajouter la SignalR bibliothèque cliente</span><span class="sxs-lookup"><span data-stu-id="2d6f6-157">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2d6f6-158">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2d6f6-158">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="2d6f6-159">Dans **Explorateur de solutions**, cliquez avec le bouton droit sur le projet **BlazorSignalRApp. client** , puis sélectionnez **gérer les packages NuGet**.</span><span class="sxs-lookup"><span data-stu-id="2d6f6-159">In **Solution Explorer**, right-click the **BlazorSignalRApp.Client** project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="2d6f6-160">Dans la boîte de dialogue **gérer les packages NuGet** , vérifiez que la **source du package** est définie sur *NuGet.org*.</span><span class="sxs-lookup"><span data-stu-id="2d6f6-160">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to *nuget.org*.</span></span>

1. <span data-ttu-id="2d6f6-161">Avec l’option **Parcourir** sélectionnée, tapez «Microsoft. AspNetCore. SignalR . Client» dans la zone de recherche.</span><span class="sxs-lookup"><span data-stu-id="2d6f6-161">With **Browse** selected, type "Microsoft.AspNetCore.SignalR.Client" in the search box.</span></span>

1. <span data-ttu-id="2d6f6-162">Dans les résultats de la recherche, sélectionnez [Microsoft. AspNetCore. SignalR ](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/)Et sélectionnez **installer**.</span><span class="sxs-lookup"><span data-stu-id="2d6f6-162">In the search results, select the [Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) package and select **Install**.</span></span>

1. <span data-ttu-id="2d6f6-163">Si la boîte de dialogue **aperçu des modifications** s’affiche, sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="2d6f6-163">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="2d6f6-164">Si la boîte de dialogue acceptation de la **licence** s’affiche, sélectionnez **J’accepte** si vous acceptez les termes du contrat de licence.</span><span class="sxs-lookup"><span data-stu-id="2d6f6-164">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2d6f6-165">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2d6f6-165">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="2d6f6-166">Dans le **Terminal intégré** (**Afficher**  >  **Terminal** à partir de la barre d’outils), exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="2d6f6-166">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2d6f6-167">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="2d6f6-167">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="2d6f6-168">Dans la barre latérale **solution** , cliquez avec le bouton droit sur le projet **BlazorSignalRApp. client** , puis sélectionnez **gérer les packages NuGet**.</span><span class="sxs-lookup"><span data-stu-id="2d6f6-168">In the **Solution** sidebar, right-click the **BlazorSignalRApp.Client** project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="2d6f6-169">Dans la boîte de dialogue **gérer les packages NuGet** , vérifiez que la liste déroulante source est définie sur *NuGet.org*.</span><span class="sxs-lookup"><span data-stu-id="2d6f6-169">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to *nuget.org*.</span></span>

1. <span data-ttu-id="2d6f6-170">Avec l’option **Parcourir** sélectionnée, tapez «Microsoft. AspNetCore. SignalR . Client» dans la zone de recherche.</span><span class="sxs-lookup"><span data-stu-id="2d6f6-170">With **Browse** selected, type "Microsoft.AspNetCore.SignalR.Client" in the search box.</span></span>

1. <span data-ttu-id="2d6f6-171">Dans les résultats de la recherche, activez la case à cocher en regard de [Microsoft. AspNetCore. SignalR Package client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) et sélectionnez **Ajouter un package**.</span><span class="sxs-lookup"><span data-stu-id="2d6f6-171">In the search results, select the check box next to the [Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) package and select **Add Package**.</span></span>

1. <span data-ttu-id="2d6f6-172">Si la boîte de dialogue acceptation de la **licence** s’affiche, sélectionnez **accepter** si vous acceptez les termes du contrat de licence.</span><span class="sxs-lookup"><span data-stu-id="2d6f6-172">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="2d6f6-173">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="2d6f6-173">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="2d6f6-174">Dans une interface de commande, exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="2d6f6-174">In a command shell, execute the following commands:</span></span>

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="add-a-signalr-hub"></a><span data-ttu-id="2d6f6-175">Ajouter un SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="2d6f6-175">Add a SignalR hub</span></span>

<span data-ttu-id="2d6f6-176">Dans le projet **BlazorSignalRApp. Server** , créez un dossier *hubs* (plural) et ajoutez la `ChatHub` classe suivante (*hubs/ChatHub. cs*) :</span><span class="sxs-lookup"><span data-stu-id="2d6f6-176">In the **BlazorSignalRApp.Server** project, create a *Hubs* (plural) folder and add the following `ChatHub` class (*Hubs/ChatHub.cs*):</span></span>

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a><span data-ttu-id="2d6f6-177">Ajouter des services et un point de terminaison pour le SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="2d6f6-177">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="2d6f6-178">Dans le projet **BlazorSignalRApp. Server** , ouvrez le fichier *Startup.cs* .</span><span class="sxs-lookup"><span data-stu-id="2d6f6-178">In the **BlazorSignalRApp.Server** project, open the *Startup.cs* file.</span></span>

1. <span data-ttu-id="2d6f6-179">Ajoutez l’espace de noms de la `ChatHub` classe au début du fichier :</span><span class="sxs-lookup"><span data-stu-id="2d6f6-179">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

1. <span data-ttu-id="2d6f6-180">Ajoutez SignalR et répondez les services d’intergiciel (middleware) de compression à `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="2d6f6-180">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

1. <span data-ttu-id="2d6f6-181">Dans `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="2d6f6-181">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="2d6f6-182">Utilisez l’intergiciel de compression de réponse en haut de la configuration du pipeline de traitement.</span><span class="sxs-lookup"><span data-stu-id="2d6f6-182">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="2d6f6-183">Entre les points de terminaison pour les contrôleurs et le secours côté client, ajoutez un point de terminaison pour le Hub.</span><span class="sxs-lookup"><span data-stu-id="2d6f6-183">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

## <a name="add-razor-component-code-for-chat"></a><span data-ttu-id="2d6f6-184">Ajouter Razor du code de composant pour la conversation</span><span class="sxs-lookup"><span data-stu-id="2d6f6-184">Add Razor component code for chat</span></span>

1. <span data-ttu-id="2d6f6-185">Dans le projet **BlazorSignalRApp. client** , ouvrez le fichier *pages/index. Razor* .</span><span class="sxs-lookup"><span data-stu-id="2d6f6-185">In the **BlazorSignalRApp.Client** project, open the *Pages/Index.razor* file.</span></span>

1. <span data-ttu-id="2d6f6-186">Remplacez le balisage par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="2d6f6-186">Replace the markup with the following code:</span></span>

[!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

## <a name="run-the-app"></a><span data-ttu-id="2d6f6-187">Exécuter l’application</span><span class="sxs-lookup"><span data-stu-id="2d6f6-187">Run the app</span></span>

1. <span data-ttu-id="2d6f6-188">Suivez les instructions pour vos outils :</span><span class="sxs-lookup"><span data-stu-id="2d6f6-188">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2d6f6-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2d6f6-189">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="2d6f6-190">Dans **Explorateur de solutions**, sélectionnez le projet **BlazorSignalRApp. Server** .</span><span class="sxs-lookup"><span data-stu-id="2d6f6-190">In **Solution Explorer**, select the **BlazorSignalRApp.Server** project.</span></span> <span data-ttu-id="2d6f6-191">Appuyez sur <kbd>F5</kbd> pour exécuter l’application avec débogage ou sur <kbd>CTRL</kbd> + <kbd>F5</kbd> pour exécuter l’application sans débogage.</span><span class="sxs-lookup"><span data-stu-id="2d6f6-191">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="2d6f6-192">Copiez l’URL à partir de la barre d’adresse, ouvrez un autre onglet ou instance du navigateur, puis collez l’URL dans la barre d’adresse.</span><span class="sxs-lookup"><span data-stu-id="2d6f6-192">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="2d6f6-193">Choisissez l’un des navigateurs, entrez un nom et un message, puis sélectionnez le bouton **Envoyer**.</span><span class="sxs-lookup"><span data-stu-id="2d6f6-193">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="2d6f6-194">Le nom et le message s’affichent instantanément sur les deux pages :</span><span class="sxs-lookup"><span data-stu-id="2d6f6-194">The name and message are displayed on both pages instantly:</span></span>

   <span data-ttu-id="2d6f6-195">![SignalRBlazorL’exemple d’application Webassembly est ouvert dans deux fenêtres de navigateur qui affichent les messages échangés.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span><span class="sxs-lookup"><span data-stu-id="2d6f6-195">![SignalR Blazor WebAssembly sample app open in two browser windows showing exchanged messages.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span></span>

   <span data-ttu-id="2d6f6-196">Devis : *Star Trek VI : le pays indécouvert* &copy; 1991 [primordial](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="2d6f6-196">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2d6f6-197">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2d6f6-197">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="2d6f6-198">Lorsque VS Code propose de créer un profil de lancement pour l’application serveur (*. vscode/launch.jssur*), l' `program` entrée ressemble à ce qui suit pour pointer vers l’assembly de l’application ( `{APPLICATION NAME}.Server.dll` ) :</span><span class="sxs-lookup"><span data-stu-id="2d6f6-198">When VS Code offers to create a launch profile for the Server app (*.vscode/launch.json*), the `program` entry appears similar to the following to point to the app's assembly (`{APPLICATION NAME}.Server.dll`):</span></span>

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/{APPLICATION NAME}.Server.dll"
   ```

1. <span data-ttu-id="2d6f6-199">Appuyez sur <kbd>F5</kbd> pour exécuter l’application avec débogage ou sur <kbd>CTRL</kbd> + <kbd>F5</kbd> pour exécuter l’application sans débogage.</span><span class="sxs-lookup"><span data-stu-id="2d6f6-199">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="2d6f6-200">Copiez l’URL à partir de la barre d’adresse, ouvrez un autre onglet ou instance du navigateur, puis collez l’URL dans la barre d’adresse.</span><span class="sxs-lookup"><span data-stu-id="2d6f6-200">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="2d6f6-201">Choisissez l’un des navigateurs, entrez un nom et un message, puis sélectionnez le bouton **Envoyer**.</span><span class="sxs-lookup"><span data-stu-id="2d6f6-201">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="2d6f6-202">Le nom et le message s’affichent instantanément sur les deux pages :</span><span class="sxs-lookup"><span data-stu-id="2d6f6-202">The name and message are displayed on both pages instantly:</span></span>

   <span data-ttu-id="2d6f6-203">![SignalRBlazorL’exemple d’application Webassembly est ouvert dans deux fenêtres de navigateur qui affichent les messages échangés.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span><span class="sxs-lookup"><span data-stu-id="2d6f6-203">![SignalR Blazor WebAssembly sample app open in two browser windows showing exchanged messages.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span></span>

   <span data-ttu-id="2d6f6-204">Devis : *Star Trek VI : le pays indécouvert* &copy; 1991 [primordial](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="2d6f6-204">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2d6f6-205">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="2d6f6-205">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="2d6f6-206">Dans la barre latérale **solution** , sélectionnez le projet **BlazorSignalRApp. Server** .</span><span class="sxs-lookup"><span data-stu-id="2d6f6-206">In the **Solution** sidebar, select the **BlazorSignalRApp.Server** project.</span></span> <span data-ttu-id="2d6f6-207">Appuyez sur <kbd>⌘</kbd> + <kbd>↩</kbd> pour exécuter l’application avec débogage ou <kbd>⌥</kbd> + <kbd>⌘</kbd> + <kbd>↩</kbd> pour exécuter l’application sans débogage.</span><span class="sxs-lookup"><span data-stu-id="2d6f6-207">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="2d6f6-208">Copiez l’URL à partir de la barre d’adresse, ouvrez un autre onglet ou instance du navigateur, puis collez l’URL dans la barre d’adresse.</span><span class="sxs-lookup"><span data-stu-id="2d6f6-208">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="2d6f6-209">Choisissez l’un des navigateurs, entrez un nom et un message, puis sélectionnez le bouton **Envoyer**.</span><span class="sxs-lookup"><span data-stu-id="2d6f6-209">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="2d6f6-210">Le nom et le message s’affichent instantanément sur les deux pages :</span><span class="sxs-lookup"><span data-stu-id="2d6f6-210">The name and message are displayed on both pages instantly:</span></span>

   <span data-ttu-id="2d6f6-211">![SignalRBlazorL’exemple d’application Webassembly est ouvert dans deux fenêtres de navigateur qui affichent les messages échangés.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span><span class="sxs-lookup"><span data-stu-id="2d6f6-211">![SignalR Blazor WebAssembly sample app open in two browser windows showing exchanged messages.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span></span>

   <span data-ttu-id="2d6f6-212">Devis : *Star Trek VI : le pays indécouvert* &copy; 1991 [primordial](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="2d6f6-212">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="2d6f6-213">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="2d6f6-213">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="2d6f6-214">Dans une interface de commande, exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="2d6f6-214">In a command shell, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="2d6f6-215">Copiez l’URL à partir de la barre d’adresse, ouvrez un autre onglet ou instance du navigateur, puis collez l’URL dans la barre d’adresse.</span><span class="sxs-lookup"><span data-stu-id="2d6f6-215">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="2d6f6-216">Choisissez l’un des navigateurs, entrez un nom et un message, puis sélectionnez le bouton **Envoyer**.</span><span class="sxs-lookup"><span data-stu-id="2d6f6-216">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="2d6f6-217">Le nom et le message s’affichent instantanément sur les deux pages :</span><span class="sxs-lookup"><span data-stu-id="2d6f6-217">The name and message are displayed on both pages instantly:</span></span>

   <span data-ttu-id="2d6f6-218">![SignalRBlazorL’exemple d’application Webassembly est ouvert dans deux fenêtres de navigateur qui affichent les messages échangés.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span><span class="sxs-lookup"><span data-stu-id="2d6f6-218">![SignalR Blazor WebAssembly sample app open in two browser windows showing exchanged messages.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span></span>

   <span data-ttu-id="2d6f6-219">Devis : *Star Trek VI : le pays indécouvert* &copy; 1991 [primordial](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="2d6f6-219">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="2d6f6-220">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="2d6f6-220">Next steps</span></span>

<span data-ttu-id="2d6f6-221">Dans ce didacticiel, vous avez appris à :</span><span class="sxs-lookup"><span data-stu-id="2d6f6-221">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="2d6f6-222">Créer un Blazor projet d’application hébergée Webassembly</span><span class="sxs-lookup"><span data-stu-id="2d6f6-222">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="2d6f6-223">Ajouter la SignalR bibliothèque cliente</span><span class="sxs-lookup"><span data-stu-id="2d6f6-223">Add the SignalR client library</span></span>
> * <span data-ttu-id="2d6f6-224">Ajouter un SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="2d6f6-224">Add a SignalR hub</span></span>
> * <span data-ttu-id="2d6f6-225">Ajouter des SignalR services et un point de terminaison pour le SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="2d6f6-225">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="2d6f6-226">Ajouter Razor du code de composant pour la conversation</span><span class="sxs-lookup"><span data-stu-id="2d6f6-226">Add Razor component code for chat</span></span>

<span data-ttu-id="2d6f6-227">Pour en savoir plus sur Blazor la création d’applications, consultez la Blazor documentation :</span><span class="sxs-lookup"><span data-stu-id="2d6f6-227">To learn more about building Blazor apps, see the Blazor documentation:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/index>

## <a name="additional-resources"></a><span data-ttu-id="2d6f6-228">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="2d6f6-228">Additional resources</span></span>

* <xref:signalr/introduction>
* <span data-ttu-id="2d6f6-229">[SignalRnégociation Cross-Origin pour l’authentification](xref:blazor/hosting-model-configuration#signalr-cross-origin-negotiation-for-authentication)</span><span class="sxs-lookup"><span data-stu-id="2d6f6-229">[SignalR cross-origin negotiation for authentication](xref:blazor/hosting-model-configuration#signalr-cross-origin-negotiation-for-authentication)</span></span>
