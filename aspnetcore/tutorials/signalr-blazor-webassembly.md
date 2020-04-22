---
title: Utiliser ASP.NET Core SignalR Blazor avec WebAssembly
author: guardrex
description: Créez une application de chat qui SignalR Blazor utilise ASP.NET Core avec WebAssembly.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/21/2020
no-loc:
- Blazor
- SignalR
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: 03db8b48bdacec1d6877a4ea09f97c242761c42d
ms.sourcegitcommit: f976dce28ad887bbd31720c318fd4a97cf96cc6d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81738009"
---
# <a name="use-aspnet-core-signalr-with-blazor-webassembly"></a><span data-ttu-id="9f8c0-103">Utilisez ASP.NET SignalR de base avec Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="9f8c0-103">Use ASP.NET Core SignalR with Blazor WebAssembly</span></span>

<span data-ttu-id="9f8c0-104">Par [Daniel Roth](https://github.com/danroth27) et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="9f8c0-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="9f8c0-105">Ce tutoriel enseigne les bases de la construction d’une application en temps réel en utilisant SignalR avec Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="9f8c0-105">This tutorial teaches the basics of building a real-time app using SignalR with Blazor WebAssembly.</span></span> <span data-ttu-id="9f8c0-106">Vous allez apprendre à effectuer les actions suivantes :</span><span class="sxs-lookup"><span data-stu-id="9f8c0-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="9f8c0-107">Créer un projet d’application Blazor WebAssembly Hébergé</span><span class="sxs-lookup"><span data-stu-id="9f8c0-107">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="9f8c0-108">Ajouter la bibliothèque de client SignalR</span><span class="sxs-lookup"><span data-stu-id="9f8c0-108">Add the SignalR client library</span></span>
> * <span data-ttu-id="9f8c0-109">Ajouter un hub SignalR</span><span class="sxs-lookup"><span data-stu-id="9f8c0-109">Add a SignalR hub</span></span>
> * <span data-ttu-id="9f8c0-110">Ajouter des services SignalR et un point de terminaison pour le hub SignalR</span><span class="sxs-lookup"><span data-stu-id="9f8c0-110">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="9f8c0-111">Ajouter le code de composant Razor pour le chat</span><span class="sxs-lookup"><span data-stu-id="9f8c0-111">Add Razor component code for chat</span></span>

<span data-ttu-id="9f8c0-112">À la fin de ce tutoriel, vous aurez une application de chat de travail.</span><span class="sxs-lookup"><span data-stu-id="9f8c0-112">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="9f8c0-113">[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([comment télécharger](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9f8c0-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="9f8c0-114">Prérequis</span><span class="sxs-lookup"><span data-stu-id="9f8c0-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9f8c0-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9f8c0-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="9f8c0-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9f8c0-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9f8c0-117">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="9f8c0-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="9f8c0-118">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="9f8c0-118">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

## <a name="create-a-hosted-blazor-webassembly-app-project"></a><span data-ttu-id="9f8c0-119">Créer un projet d’application Blazor WebAssembly hébergé</span><span class="sxs-lookup"><span data-stu-id="9f8c0-119">Create a hosted Blazor WebAssembly app project</span></span>

<span data-ttu-id="9f8c0-120">Lorsque vous n’utilisez pas la version Visual Studio 16.6 Aperçu 2 ou plus tard, installez le modèle [WebAssembly Blazor.](xref:blazor/hosting-models#blazor-webassembly)</span><span class="sxs-lookup"><span data-stu-id="9f8c0-120">When not using Visual Studio version 16.6 Preview 2 or later, install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) template.</span></span> <span data-ttu-id="9f8c0-121">Le paquet [Microsoft.AspNetCore.Components.WebAssembly.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Templates/) a une version de prévisualisation tandis que Blazor WebAssembly est en avant-première.</span><span class="sxs-lookup"><span data-stu-id="9f8c0-121">The [Microsoft.AspNetCore.Components.WebAssembly.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Templates/) package has a preview version while Blazor WebAssembly is in preview.</span></span> <span data-ttu-id="9f8c0-122">Dans une coque de commande, exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="9f8c0-122">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview4.20210.8
```

<span data-ttu-id="9f8c0-123">Suivez les conseils pour votre choix d’outillage :</span><span class="sxs-lookup"><span data-stu-id="9f8c0-123">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9f8c0-124">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9f8c0-124">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="9f8c0-125">Créez un projet.</span><span class="sxs-lookup"><span data-stu-id="9f8c0-125">Create a new project.</span></span>

1. <span data-ttu-id="9f8c0-126">Sélectionnez **Blazor App** et sélectionnez **Next**.</span><span class="sxs-lookup"><span data-stu-id="9f8c0-126">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="9f8c0-127">Tapez "BlazorSignalRApp" dans le champ du **nom du projet.**</span><span class="sxs-lookup"><span data-stu-id="9f8c0-127">Type "BlazorSignalRApp" in the **Project name** field.</span></span> <span data-ttu-id="9f8c0-128">Confirmez que **l’entrée de localisation** est correcte ou fournissez un emplacement pour le projet.</span><span class="sxs-lookup"><span data-stu-id="9f8c0-128">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="9f8c0-129">Sélectionnez **Create** (Créer).</span><span class="sxs-lookup"><span data-stu-id="9f8c0-129">Select **Create**.</span></span>

1. <span data-ttu-id="9f8c0-130">Choisissez le modèle **Blazor WebAssembly App.**</span><span class="sxs-lookup"><span data-stu-id="9f8c0-130">Choose the **Blazor WebAssembly App** template.</span></span>

1. <span data-ttu-id="9f8c0-131">Sous **Advanced**, sélectionnez la case à cocher **hébergée ASP.NET Core.**</span><span class="sxs-lookup"><span data-stu-id="9f8c0-131">Under **Advanced**, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="9f8c0-132">Sélectionnez **Create** (Créer).</span><span class="sxs-lookup"><span data-stu-id="9f8c0-132">Select **Create**.</span></span>

> [!NOTE]
> <span data-ttu-id="9f8c0-133">Si vous avez mis à niveau ou installé une nouvelle version de Visual Studio et que le modèle WebAssembly de Blazor n’apparaît pas dans l’interface utilisateur VS, réinstaller le modèle à l’aide de la `dotnet new` commande indiquée précédemment.</span><span class="sxs-lookup"><span data-stu-id="9f8c0-133">If you upgraded or installed a new version of Visual Studio and the Blazor WebAssembly template doesn't appear in the VS UI, reinstall the template using the `dotnet new` command shown previously.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9f8c0-134">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9f8c0-134">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="9f8c0-135">Dans une coque de commande, exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="9f8c0-135">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. <span data-ttu-id="9f8c0-136">Dans Visual Studio Code, ouvrez le dossier de projet de l’application.</span><span class="sxs-lookup"><span data-stu-id="9f8c0-136">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="9f8c0-137">Lorsque le dialogue semble ajouter des actifs pour construire et déboger l’application, sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="9f8c0-137">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="9f8c0-138">Visual Studio Code ajoute automatiquement le dossier *.vscode* avec des fichiers *launch.json* et *tasks.json* générés.</span><span class="sxs-lookup"><span data-stu-id="9f8c0-138">Visual Studio Code automatically adds the *.vscode* folder with generated *launch.json* and *tasks.json* files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9f8c0-139">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="9f8c0-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="9f8c0-140">Dans une coque de commande, exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="9f8c0-140">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. <span data-ttu-id="9f8c0-141">Dans Visual Studio for Mac, ouvrez le projet en naviguant vers le dossier du projet et en ouvrant le fichier de solution du projet (*.sln*).</span><span class="sxs-lookup"><span data-stu-id="9f8c0-141">In Visual Studio for Mac, open the project by navigating to the project folder and opening the project's solution file (*.sln*).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="9f8c0-142">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="9f8c0-142">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="9f8c0-143">Dans une coque de commande, exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="9f8c0-143">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="9f8c0-144">Ajouter la bibliothèque de client SignalR</span><span class="sxs-lookup"><span data-stu-id="9f8c0-144">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9f8c0-145">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9f8c0-145">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="9f8c0-146">Dans **Solution Explorer**, cliquez à droite sur le projet **BlazorSignalRApp.Client** et **sélectionnez Manage NuGet Packages**.</span><span class="sxs-lookup"><span data-stu-id="9f8c0-146">In **Solution Explorer**, right-click the **BlazorSignalRApp.Client** project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="9f8c0-147">Dans le dialogue **Manage NuGet Packages,** confirmer que la **source du paquet** est configurée à *nuget.org*.</span><span class="sxs-lookup"><span data-stu-id="9f8c0-147">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to *nuget.org*.</span></span>

1. <span data-ttu-id="9f8c0-148">Avec **Browse** sélectionné, tapez "Microsoft.AspNetCore.SignalR.Client" dans la zone de recherche.</span><span class="sxs-lookup"><span data-stu-id="9f8c0-148">With **Browse** selected, type "Microsoft.AspNetCore.SignalR.Client" in the search box.</span></span>

1. <span data-ttu-id="9f8c0-149">Dans les résultats de `Microsoft.AspNetCore.SignalR.Client` recherche, sélectionnez le paquet et sélectionnez **Installer**.</span><span class="sxs-lookup"><span data-stu-id="9f8c0-149">In the search results, select the `Microsoft.AspNetCore.SignalR.Client` package and select **Install**.</span></span>

1. <span data-ttu-id="9f8c0-150">Si le dialogue **de modifications d’aperçu** apparaît, sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="9f8c0-150">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="9f8c0-151">Si le dialogue **d’acceptation de licence** apparaît, sélectionnez **J’accepte** si vous êtes d’accord avec les conditions de licence.</span><span class="sxs-lookup"><span data-stu-id="9f8c0-151">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9f8c0-152">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9f8c0-152">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="9f8c0-153">Dans le **terminal intégré** **(voir** > **le terminal** depuis la barre d’outils), exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="9f8c0-153">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9f8c0-154">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="9f8c0-154">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="9f8c0-155">Dans la barre latérale **Solution,** cliquez à droite sur le projet **BlazorSignalRApp.Client** et **sélectionnez Manage NuGet Packages**.</span><span class="sxs-lookup"><span data-stu-id="9f8c0-155">In the **Solution** sidebar, right-click the **BlazorSignalRApp.Client** project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="9f8c0-156">Dans le dialogue **Manage NuGet Packages,** confirmer que la source de décrochage est configuré pour *nuget.org*.</span><span class="sxs-lookup"><span data-stu-id="9f8c0-156">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to *nuget.org*.</span></span>

1. <span data-ttu-id="9f8c0-157">Avec **Browse** sélectionné, tapez "Microsoft.AspNetCore.SignalR.Client" dans la zone de recherche.</span><span class="sxs-lookup"><span data-stu-id="9f8c0-157">With **Browse** selected, type "Microsoft.AspNetCore.SignalR.Client" in the search box.</span></span>

1. <span data-ttu-id="9f8c0-158">Dans les résultats de recherche, sélectionnez la case à cocher à côté du `Microsoft.AspNetCore.SignalR.Client` paquet et sélectionnez Add **Package**.</span><span class="sxs-lookup"><span data-stu-id="9f8c0-158">In the search results, select the check box next to the `Microsoft.AspNetCore.SignalR.Client` package and select **Add Package**.</span></span>

1. <span data-ttu-id="9f8c0-159">Si le dialogue **d’acceptation de licence** apparaît, **sélectionnez Accept** si vous êtes d’accord avec les conditions de licence.</span><span class="sxs-lookup"><span data-stu-id="9f8c0-159">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="9f8c0-160">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="9f8c0-160">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="9f8c0-161">Dans une coque de commande, exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="9f8c0-161">In a command shell, execute the following commands:</span></span>

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="add-a-signalr-hub"></a><span data-ttu-id="9f8c0-162">Ajouter un hub SignalR</span><span class="sxs-lookup"><span data-stu-id="9f8c0-162">Add a SignalR hub</span></span>

<span data-ttu-id="9f8c0-163">Dans le cadre du projet **BlazorSignalRApp.Server,** créez un dossier `ChatHub` *Hubs* (pluriel) et ajoutez la classe suivante *(Hubs/ChatHub.cs*) :</span><span class="sxs-lookup"><span data-stu-id="9f8c0-163">In the **BlazorSignalRApp.Server** project, create a *Hubs* (plural) folder and add the following `ChatHub` class (*Hubs/ChatHub.cs*):</span></span>

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a><span data-ttu-id="9f8c0-164">Ajouter des services et un point de terminaison pour le hub SignalR</span><span class="sxs-lookup"><span data-stu-id="9f8c0-164">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="9f8c0-165">Dans le cadre du projet **BlazorSignalRApp.Server,** ouvrez le *fichier Startup.cs.*</span><span class="sxs-lookup"><span data-stu-id="9f8c0-165">In the **BlazorSignalRApp.Server** project, open the *Startup.cs* file.</span></span>

1. <span data-ttu-id="9f8c0-166">Ajouter l’espace `ChatHub` nom pour la classe en haut du fichier :</span><span class="sxs-lookup"><span data-stu-id="9f8c0-166">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

1. <span data-ttu-id="9f8c0-167">Ajoutez les services SignalR et `Startup.ConfigureServices`Response Compression Middleware à :</span><span class="sxs-lookup"><span data-stu-id="9f8c0-167">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

1. <span data-ttu-id="9f8c0-168">Entre `Startup.Configure` les points de terminaison pour les contrôleurs et le repli côté client, ajoutez un point de terminaison pour le hub :</span><span class="sxs-lookup"><span data-stu-id="9f8c0-168">In `Startup.Configure` between the endpoints for controllers and the client-side fallback, add an endpoint for the hub:</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_UseEndpoints&highlight=4)]

## <a name="add-razor-component-code-for-chat"></a><span data-ttu-id="9f8c0-169">Ajouter le code de composant Razor pour le chat</span><span class="sxs-lookup"><span data-stu-id="9f8c0-169">Add Razor component code for chat</span></span>

1. <span data-ttu-id="9f8c0-170">Dans le cadre du projet **BlazorSignalRApp.Client,** ouvrez le fichier *Pages/Index.razor.*</span><span class="sxs-lookup"><span data-stu-id="9f8c0-170">In the **BlazorSignalRApp.Client** project, open the *Pages/Index.razor* file.</span></span>

1. <span data-ttu-id="9f8c0-171">Remplacez la balisage par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="9f8c0-171">Replace the markup with the following code:</span></span>

[!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

## <a name="run-the-app"></a><span data-ttu-id="9f8c0-172">Exécuter l’application</span><span class="sxs-lookup"><span data-stu-id="9f8c0-172">Run the app</span></span>

1. <span data-ttu-id="9f8c0-173">Suivez les conseils pour votre outillage :</span><span class="sxs-lookup"><span data-stu-id="9f8c0-173">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9f8c0-174">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9f8c0-174">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="9f8c0-175">Dans **Solution Explorer**, sélectionnez le projet **BlazorSignalRApp.Server.**</span><span class="sxs-lookup"><span data-stu-id="9f8c0-175">In **Solution Explorer**, select the **BlazorSignalRApp.Server** project.</span></span> <span data-ttu-id="9f8c0-176">Appuyez sur <kbd>F5</kbd> pour exécuter l’application avec débogage ou <kbd>Ctrl</kbd>+<kbd>F5</kbd> pour exécuter l’application sans débogage.</span><span class="sxs-lookup"><span data-stu-id="9f8c0-176">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="9f8c0-177">Copiez l’URL à partir de la barre d’adresse, ouvrez un autre onglet ou instance du navigateur, puis collez l’URL dans la barre d’adresse.</span><span class="sxs-lookup"><span data-stu-id="9f8c0-177">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="9f8c0-178">Choisissez l’un des navigateurs, entrez un nom et un message, puis sélectionnez le bouton **Envoyer**.</span><span class="sxs-lookup"><span data-stu-id="9f8c0-178">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="9f8c0-179">Le nom et le message sont affichés instantanément sur les deux pages :</span><span class="sxs-lookup"><span data-stu-id="9f8c0-179">The name and message are displayed on both pages instantly:</span></span>

   ![SignalR Blazor WebAssembly exemple app ouvert dans deux fenêtres de navigateur montrant des messages échangés.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="9f8c0-181">Citations: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="9f8c0-181">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9f8c0-182">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9f8c0-182">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="9f8c0-183">Lorsque VS Code propose de créer un profil de lancement pour l’application Server (*.vscode/launch.json*), l’entrée `program` apparaît similaire à ce qui suit pour indiquer l’assemblage de l’application (`{APPLICATION NAME}.Server.dll`</span><span class="sxs-lookup"><span data-stu-id="9f8c0-183">When VS Code offers to create a launch profile for the Server app (*.vscode/launch.json*), the `program` entry appears similar to the following to point to the app's assembly (`{APPLICATION NAME}.Server.dll`):</span></span>

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/{APPLICATION NAME}.Server.dll"
   ```

1. <span data-ttu-id="9f8c0-184">Appuyez sur <kbd>F5</kbd> pour exécuter l’application avec débogage ou <kbd>Ctrl</kbd>+<kbd>F5</kbd> pour exécuter l’application sans débogage.</span><span class="sxs-lookup"><span data-stu-id="9f8c0-184">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="9f8c0-185">Copiez l’URL à partir de la barre d’adresse, ouvrez un autre onglet ou instance du navigateur, puis collez l’URL dans la barre d’adresse.</span><span class="sxs-lookup"><span data-stu-id="9f8c0-185">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="9f8c0-186">Choisissez l’un des navigateurs, entrez un nom et un message, puis sélectionnez le bouton **Envoyer**.</span><span class="sxs-lookup"><span data-stu-id="9f8c0-186">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="9f8c0-187">Le nom et le message sont affichés instantanément sur les deux pages :</span><span class="sxs-lookup"><span data-stu-id="9f8c0-187">The name and message are displayed on both pages instantly:</span></span>

   ![SignalR Blazor WebAssembly exemple app ouvert dans deux fenêtres de navigateur montrant des messages échangés.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="9f8c0-189">Citations: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="9f8c0-189">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9f8c0-190">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="9f8c0-190">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="9f8c0-191">Dans la barre latérale **Solution,** sélectionnez le projet **BlazorSignalRApp.Server.**</span><span class="sxs-lookup"><span data-stu-id="9f8c0-191">In the **Solution** sidebar, select the **BlazorSignalRApp.Server** project.</span></span> <span data-ttu-id="9f8c0-192">+<kbd>⌘</kbd>+<kbd>↩</kbd> <kbd>⌥</kbd><kbd>↩</kbd> <kbd>⌘</kbd>+Appuyez sur ↩ pour exécuter l’application avec débogage ou ↩ pour exécuter l’application sans débogage.</span><span class="sxs-lookup"><span data-stu-id="9f8c0-192">Press <kbd>⌘</kbd>+<kbd>↩</kbd>\*\* to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="9f8c0-193">Copiez l’URL à partir de la barre d’adresse, ouvrez un autre onglet ou instance du navigateur, puis collez l’URL dans la barre d’adresse.</span><span class="sxs-lookup"><span data-stu-id="9f8c0-193">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="9f8c0-194">Choisissez l’un des navigateurs, entrez un nom et un message, puis sélectionnez le bouton **Envoyer**.</span><span class="sxs-lookup"><span data-stu-id="9f8c0-194">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="9f8c0-195">Le nom et le message sont affichés instantanément sur les deux pages :</span><span class="sxs-lookup"><span data-stu-id="9f8c0-195">The name and message are displayed on both pages instantly:</span></span>

   ![SignalR Blazor WebAssembly exemple app ouvert dans deux fenêtres de navigateur montrant des messages échangés.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="9f8c0-197">Citations: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="9f8c0-197">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="9f8c0-198">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="9f8c0-198">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="9f8c0-199">Dans une coque de commande, exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="9f8c0-199">In a command shell, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="9f8c0-200">Copiez l’URL à partir de la barre d’adresse, ouvrez un autre onglet ou instance du navigateur, puis collez l’URL dans la barre d’adresse.</span><span class="sxs-lookup"><span data-stu-id="9f8c0-200">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="9f8c0-201">Choisissez l’un des navigateurs, entrez un nom et un message, puis sélectionnez le bouton **Envoyer**.</span><span class="sxs-lookup"><span data-stu-id="9f8c0-201">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="9f8c0-202">Le nom et le message sont affichés instantanément sur les deux pages :</span><span class="sxs-lookup"><span data-stu-id="9f8c0-202">The name and message are displayed on both pages instantly:</span></span>

   ![SignalR Blazor WebAssembly exemple app ouvert dans deux fenêtres de navigateur montrant des messages échangés.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="9f8c0-204">Citations: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="9f8c0-204">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="9f8c0-205">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="9f8c0-205">Next steps</span></span>

<span data-ttu-id="9f8c0-206">Dans ce didacticiel, vous avez appris à :</span><span class="sxs-lookup"><span data-stu-id="9f8c0-206">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="9f8c0-207">Créer Blazor un projet d’application WebAssembly Hosted</span><span class="sxs-lookup"><span data-stu-id="9f8c0-207">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="9f8c0-208">Ajouter SignalR la bibliothèque client</span><span class="sxs-lookup"><span data-stu-id="9f8c0-208">Add the SignalR client library</span></span>
> * <span data-ttu-id="9f8c0-209">Ajouter SignalR un hub</span><span class="sxs-lookup"><span data-stu-id="9f8c0-209">Add a SignalR hub</span></span>
> * <span data-ttu-id="9f8c0-210">Ajouter SignalR des services et SignalR un point de terminaison pour le hub</span><span class="sxs-lookup"><span data-stu-id="9f8c0-210">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="9f8c0-211">Ajouter le code de composant Razor pour le chat</span><span class="sxs-lookup"><span data-stu-id="9f8c0-211">Add Razor component code for chat</span></span>

<span data-ttu-id="9f8c0-212">Pour en savoir Blazor plus sur Blazor la création d’applications, consultez la documentation :</span><span class="sxs-lookup"><span data-stu-id="9f8c0-212">To learn more about building Blazor apps, see the Blazor documentation:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/index>

## <a name="additional-resources"></a><span data-ttu-id="9f8c0-213">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="9f8c0-213">Additional resources</span></span>

* <xref:signalr/introduction>
