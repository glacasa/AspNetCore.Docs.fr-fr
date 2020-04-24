---
title: Utiliser ASP.NET Core SignalR avec Blazor webassembly
author: guardrex
description: Créer une application de conversation qui utilise SignalR ASP.net Core Blazor avec webassembly.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- SignalR
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: 78c5fbb8b91b934bcb34525672e9e26b6a95290e
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82111147"
---
# <a name="use-aspnet-core-signalr-with-blazor-webassembly"></a><span data-ttu-id="47a39-103">Utiliser ASP.NET Core Signalr avec le webassembly éblouissant</span><span class="sxs-lookup"><span data-stu-id="47a39-103">Use ASP.NET Core SignalR with Blazor WebAssembly</span></span>

<span data-ttu-id="47a39-104">Par [Daniel Roth](https://github.com/danroth27) et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="47a39-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="47a39-105">Ce didacticiel enseigne les bases de la création d’une application en temps réel à l’aide de Signalr avec le webassembly éblouissant.</span><span class="sxs-lookup"><span data-stu-id="47a39-105">This tutorial teaches the basics of building a real-time app using SignalR with Blazor WebAssembly.</span></span> <span data-ttu-id="47a39-106">Vous allez apprendre à effectuer les actions suivantes :</span><span class="sxs-lookup"><span data-stu-id="47a39-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="47a39-107">Créer un projet d’application hébergée webassembly éblouissant</span><span class="sxs-lookup"><span data-stu-id="47a39-107">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="47a39-108">Ajouter la bibliothèque de client SignalR</span><span class="sxs-lookup"><span data-stu-id="47a39-108">Add the SignalR client library</span></span>
> * <span data-ttu-id="47a39-109">Ajouter un concentrateur Signalr</span><span class="sxs-lookup"><span data-stu-id="47a39-109">Add a SignalR hub</span></span>
> * <span data-ttu-id="47a39-110">Ajouter des services Signalr et un point de terminaison pour le concentrateur Signalr</span><span class="sxs-lookup"><span data-stu-id="47a39-110">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="47a39-111">Ajouter du code de composant Razor pour la conversation</span><span class="sxs-lookup"><span data-stu-id="47a39-111">Add Razor component code for chat</span></span>

<span data-ttu-id="47a39-112">À la fin de ce didacticiel, vous disposerez d’une application de conversation de travail.</span><span class="sxs-lookup"><span data-stu-id="47a39-112">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="47a39-113">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="47a39-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="47a39-114">Prérequis</span><span class="sxs-lookup"><span data-stu-id="47a39-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="47a39-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="47a39-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="47a39-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="47a39-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="47a39-117">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="47a39-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="47a39-118">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="47a39-118">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

## <a name="create-a-hosted-blazor-webassembly-app-project"></a><span data-ttu-id="47a39-119">Créer un projet d’application webassembly éblouissant hébergé</span><span class="sxs-lookup"><span data-stu-id="47a39-119">Create a hosted Blazor WebAssembly app project</span></span>

<span data-ttu-id="47a39-120">Si vous n’utilisez pas Visual Studio version 16,6 Preview 2 ou une version ultérieure, installez le modèle de [Webassembly éblouissant](xref:blazor/hosting-models#blazor-webassembly) .</span><span class="sxs-lookup"><span data-stu-id="47a39-120">When not using Visual Studio version 16.6 Preview 2 or later, install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) template.</span></span> <span data-ttu-id="47a39-121">Le package [Microsoft. AspNetCore. Components. Webassembly. Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Templates/) a une préversion alors que le composant webassembly éblouissant est en version préliminaire.</span><span class="sxs-lookup"><span data-stu-id="47a39-121">The [Microsoft.AspNetCore.Components.WebAssembly.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Templates/) package has a preview version while Blazor WebAssembly is in preview.</span></span> <span data-ttu-id="47a39-122">Dans une interface de commande, exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="47a39-122">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview5.20216.8
```

<span data-ttu-id="47a39-123">Suivez les instructions de votre choix d’outils :</span><span class="sxs-lookup"><span data-stu-id="47a39-123">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="47a39-124">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="47a39-124">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="47a39-125">Créez un projet.</span><span class="sxs-lookup"><span data-stu-id="47a39-125">Create a new project.</span></span>

1. <span data-ttu-id="47a39-126">Sélectionnez l' **application éblouissant** , puis sélectionnez **suivant**.</span><span class="sxs-lookup"><span data-stu-id="47a39-126">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="47a39-127">Tapez « BlazorSignalRApp » dans le champ **nom du projet** .</span><span class="sxs-lookup"><span data-stu-id="47a39-127">Type "BlazorSignalRApp" in the **Project name** field.</span></span> <span data-ttu-id="47a39-128">Confirmez que l’entrée d' **emplacement** est correcte ou indiquez un emplacement pour le projet.</span><span class="sxs-lookup"><span data-stu-id="47a39-128">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="47a39-129">Sélectionnez **Créer**.</span><span class="sxs-lookup"><span data-stu-id="47a39-129">Select **Create**.</span></span>

1. <span data-ttu-id="47a39-130">Choisissez le modèle **application éblouissant Webassembly** .</span><span class="sxs-lookup"><span data-stu-id="47a39-130">Choose the **Blazor WebAssembly App** template.</span></span>

1. <span data-ttu-id="47a39-131">Sous **avancé**, activez la case à cocher **ASP.net Core hébergé** .</span><span class="sxs-lookup"><span data-stu-id="47a39-131">Under **Advanced**, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="47a39-132">Sélectionnez **Créer**.</span><span class="sxs-lookup"><span data-stu-id="47a39-132">Select **Create**.</span></span>

> [!NOTE]
> <span data-ttu-id="47a39-133">Si vous avez mis à niveau ou installé une nouvelle version de Visual Studio et que le modèle éblouissant webassembly n’apparaît pas dans l’interface utilisateur VS, réinstallez le modèle à l’aide de la `dotnet new` commande indiquée précédemment.</span><span class="sxs-lookup"><span data-stu-id="47a39-133">If you upgraded or installed a new version of Visual Studio and the Blazor WebAssembly template doesn't appear in the VS UI, reinstall the template using the `dotnet new` command shown previously.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="47a39-134">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="47a39-134">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="47a39-135">Dans une interface de commande, exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="47a39-135">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. <span data-ttu-id="47a39-136">Dans Visual Studio Code, ouvrez le dossier du projet de l’application.</span><span class="sxs-lookup"><span data-stu-id="47a39-136">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="47a39-137">Quand la boîte de dialogue semble ajouter des éléments multimédias pour générer et déboguer l’application, sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="47a39-137">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="47a39-138">Visual Studio Code ajoute automatiquement le dossier *. vscode* avec les fichiers *Launch. JSON* et *Tasks. JSON* générés.</span><span class="sxs-lookup"><span data-stu-id="47a39-138">Visual Studio Code automatically adds the *.vscode* folder with generated *launch.json* and *tasks.json* files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="47a39-139">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="47a39-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="47a39-140">Dans une interface de commande, exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="47a39-140">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. <span data-ttu-id="47a39-141">Dans Visual Studio pour Mac, ouvrez le projet en accédant au dossier du projet et en ouvrant le fichier solution du projet (*. sln*).</span><span class="sxs-lookup"><span data-stu-id="47a39-141">In Visual Studio for Mac, open the project by navigating to the project folder and opening the project's solution file (*.sln*).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="47a39-142">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="47a39-142">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="47a39-143">Dans une interface de commande, exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="47a39-143">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="47a39-144">Ajouter la bibliothèque de client SignalR</span><span class="sxs-lookup"><span data-stu-id="47a39-144">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="47a39-145">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="47a39-145">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="47a39-146">Dans **Explorateur de solutions**, cliquez avec le bouton droit sur le projet **BlazorSignalRApp. client** , puis sélectionnez **gérer les packages NuGet**.</span><span class="sxs-lookup"><span data-stu-id="47a39-146">In **Solution Explorer**, right-click the **BlazorSignalRApp.Client** project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="47a39-147">Dans la boîte de dialogue **gérer les packages NuGet** , vérifiez que la **source du package** est définie sur *NuGet.org*.</span><span class="sxs-lookup"><span data-stu-id="47a39-147">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to *nuget.org*.</span></span>

1. <span data-ttu-id="47a39-148">Avec l’option **Parcourir** sélectionnée, tapez « Microsoft. AspNetCore. signalr. client » dans la zone de recherche.</span><span class="sxs-lookup"><span data-stu-id="47a39-148">With **Browse** selected, type "Microsoft.AspNetCore.SignalR.Client" in the search box.</span></span>

1. <span data-ttu-id="47a39-149">Dans les résultats de la recherche, `Microsoft.AspNetCore.SignalR.Client` sélectionnez le package et sélectionnez **installer**.</span><span class="sxs-lookup"><span data-stu-id="47a39-149">In the search results, select the `Microsoft.AspNetCore.SignalR.Client` package and select **Install**.</span></span>

1. <span data-ttu-id="47a39-150">Si la boîte de dialogue **aperçu des modifications** s’affiche, sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="47a39-150">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="47a39-151">Si la boîte de dialogue acceptation de la **licence** s’affiche, sélectionnez **J’accepte** si vous acceptez les termes du contrat de licence.</span><span class="sxs-lookup"><span data-stu-id="47a39-151">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="47a39-152">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="47a39-152">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="47a39-153">Dans le **Terminal intégré** (**Afficher** > **Terminal** à partir de la barre d’outils), exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="47a39-153">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="47a39-154">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="47a39-154">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="47a39-155">Dans la barre latérale **solution** , cliquez avec le bouton droit sur le projet **BlazorSignalRApp. client** , puis sélectionnez **gérer les packages NuGet**.</span><span class="sxs-lookup"><span data-stu-id="47a39-155">In the **Solution** sidebar, right-click the **BlazorSignalRApp.Client** project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="47a39-156">Dans la boîte de dialogue **gérer les packages NuGet** , vérifiez que la liste déroulante source est définie sur *NuGet.org*.</span><span class="sxs-lookup"><span data-stu-id="47a39-156">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to *nuget.org*.</span></span>

1. <span data-ttu-id="47a39-157">Avec l’option **Parcourir** sélectionnée, tapez « Microsoft. AspNetCore. signalr. client » dans la zone de recherche.</span><span class="sxs-lookup"><span data-stu-id="47a39-157">With **Browse** selected, type "Microsoft.AspNetCore.SignalR.Client" in the search box.</span></span>

1. <span data-ttu-id="47a39-158">Dans les résultats de la recherche, activez la case à cocher en regard du `Microsoft.AspNetCore.SignalR.Client` package, puis sélectionnez Ajouter un **package**.</span><span class="sxs-lookup"><span data-stu-id="47a39-158">In the search results, select the check box next to the `Microsoft.AspNetCore.SignalR.Client` package and select **Add Package**.</span></span>

1. <span data-ttu-id="47a39-159">Si la boîte de dialogue acceptation de la **licence** s’affiche, sélectionnez **accepter** si vous acceptez les termes du contrat de licence.</span><span class="sxs-lookup"><span data-stu-id="47a39-159">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="47a39-160">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="47a39-160">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="47a39-161">Dans une interface de commande, exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="47a39-161">In a command shell, execute the following commands:</span></span>

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="add-a-signalr-hub"></a><span data-ttu-id="47a39-162">Ajouter un concentrateur Signalr</span><span class="sxs-lookup"><span data-stu-id="47a39-162">Add a SignalR hub</span></span>

<span data-ttu-id="47a39-163">Dans le projet **BlazorSignalRApp. Server** , créez un dossier *hubs* (plural) et ajoutez la classe `ChatHub` suivante (*hubs/ChatHub. cs*) :</span><span class="sxs-lookup"><span data-stu-id="47a39-163">In the **BlazorSignalRApp.Server** project, create a *Hubs* (plural) folder and add the following `ChatHub` class (*Hubs/ChatHub.cs*):</span></span>

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a><span data-ttu-id="47a39-164">Ajouter des services et un point de terminaison pour le concentrateur Signalr</span><span class="sxs-lookup"><span data-stu-id="47a39-164">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="47a39-165">Dans le projet **BlazorSignalRApp. Server** , ouvrez le fichier *Startup.cs* .</span><span class="sxs-lookup"><span data-stu-id="47a39-165">In the **BlazorSignalRApp.Server** project, open the *Startup.cs* file.</span></span>

1. <span data-ttu-id="47a39-166">Ajoutez l’espace de noms `ChatHub` de la classe au début du fichier :</span><span class="sxs-lookup"><span data-stu-id="47a39-166">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

1. <span data-ttu-id="47a39-167">Ajoutez des services d’intergiciel (middleware) de compression `Startup.ConfigureServices`de signalr et de réponse à :</span><span class="sxs-lookup"><span data-stu-id="47a39-167">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

1. <span data-ttu-id="47a39-168">`Startup.Configure` Entre les points de terminaison pour les contrôleurs et le secours côté client, ajoutez un point de terminaison pour le Hub :</span><span class="sxs-lookup"><span data-stu-id="47a39-168">In `Startup.Configure` between the endpoints for controllers and the client-side fallback, add an endpoint for the hub:</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_UseEndpoints&highlight=4)]

## <a name="add-razor-component-code-for-chat"></a><span data-ttu-id="47a39-169">Ajouter du code de composant Razor pour la conversation</span><span class="sxs-lookup"><span data-stu-id="47a39-169">Add Razor component code for chat</span></span>

1. <span data-ttu-id="47a39-170">Dans le projet **BlazorSignalRApp. client** , ouvrez le fichier *pages/index. Razor* .</span><span class="sxs-lookup"><span data-stu-id="47a39-170">In the **BlazorSignalRApp.Client** project, open the *Pages/Index.razor* file.</span></span>

1. <span data-ttu-id="47a39-171">Remplacez le balisage par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="47a39-171">Replace the markup with the following code:</span></span>

[!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

## <a name="run-the-app"></a><span data-ttu-id="47a39-172">Exécuter l’application</span><span class="sxs-lookup"><span data-stu-id="47a39-172">Run the app</span></span>

1. <span data-ttu-id="47a39-173">Suivez les instructions pour vos outils :</span><span class="sxs-lookup"><span data-stu-id="47a39-173">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="47a39-174">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="47a39-174">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="47a39-175">Dans **Explorateur de solutions**, sélectionnez le projet **BlazorSignalRApp. Server** .</span><span class="sxs-lookup"><span data-stu-id="47a39-175">In **Solution Explorer**, select the **BlazorSignalRApp.Server** project.</span></span> <span data-ttu-id="47a39-176">Appuyez sur <kbd>F5</kbd> pour exécuter l’application avec débogage ou sur <kbd>CTRL</kbd>+<kbd>F5</kbd> pour exécuter l’application sans débogage.</span><span class="sxs-lookup"><span data-stu-id="47a39-176">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="47a39-177">Copiez l’URL à partir de la barre d’adresse, ouvrez un autre onglet ou instance du navigateur, puis collez l’URL dans la barre d’adresse.</span><span class="sxs-lookup"><span data-stu-id="47a39-177">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="47a39-178">Choisissez l’un des navigateurs, entrez un nom et un message, puis sélectionnez le bouton **Envoyer**.</span><span class="sxs-lookup"><span data-stu-id="47a39-178">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="47a39-179">Le nom et le message s’affichent instantanément sur les deux pages :</span><span class="sxs-lookup"><span data-stu-id="47a39-179">The name and message are displayed on both pages instantly:</span></span>

   ![L’exemple d’application signalr du webassembly éblouissant est ouvert dans deux fenêtres de navigateur qui affichent les messages échangés.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="47a39-181">Devis &copy;: *Star Trek VI : le pays indécouvert* 1991 [primordial](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="47a39-181">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="47a39-182">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="47a39-182">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="47a39-183">Lorsque VS Code propose de créer un profil de lancement pour l’application serveur (*. vscode/Launch. JSON*), `program` l’entrée ressemble à ce qui suit pour pointer vers l’assembly`{APPLICATION NAME}.Server.dll`de l’application () :</span><span class="sxs-lookup"><span data-stu-id="47a39-183">When VS Code offers to create a launch profile for the Server app (*.vscode/launch.json*), the `program` entry appears similar to the following to point to the app's assembly (`{APPLICATION NAME}.Server.dll`):</span></span>

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/{APPLICATION NAME}.Server.dll"
   ```

1. <span data-ttu-id="47a39-184">Appuyez sur <kbd>F5</kbd> pour exécuter l’application avec débogage ou sur <kbd>CTRL</kbd>+<kbd>F5</kbd> pour exécuter l’application sans débogage.</span><span class="sxs-lookup"><span data-stu-id="47a39-184">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="47a39-185">Copiez l’URL à partir de la barre d’adresse, ouvrez un autre onglet ou instance du navigateur, puis collez l’URL dans la barre d’adresse.</span><span class="sxs-lookup"><span data-stu-id="47a39-185">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="47a39-186">Choisissez l’un des navigateurs, entrez un nom et un message, puis sélectionnez le bouton **Envoyer**.</span><span class="sxs-lookup"><span data-stu-id="47a39-186">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="47a39-187">Le nom et le message s’affichent instantanément sur les deux pages :</span><span class="sxs-lookup"><span data-stu-id="47a39-187">The name and message are displayed on both pages instantly:</span></span>

   ![L’exemple d’application signalr du webassembly éblouissant est ouvert dans deux fenêtres de navigateur qui affichent les messages échangés.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="47a39-189">Devis &copy;: *Star Trek VI : le pays indécouvert* 1991 [primordial](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="47a39-189">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="47a39-190">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="47a39-190">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="47a39-191">Dans la barre latérale **solution** , sélectionnez le projet **BlazorSignalRApp. Server** .</span><span class="sxs-lookup"><span data-stu-id="47a39-191">In the **Solution** sidebar, select the **BlazorSignalRApp.Server** project.</span></span> <span data-ttu-id="47a39-192">Appuyez sur <kbd>⌘</kbd>+<kbd>↩</kbd>\* \* pour exécuter l’application avec débogage ou <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> pour exécuter l’application sans débogage.</span><span class="sxs-lookup"><span data-stu-id="47a39-192">Press <kbd>⌘</kbd>+<kbd>↩</kbd>\*\* to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="47a39-193">Copiez l’URL à partir de la barre d’adresse, ouvrez un autre onglet ou instance du navigateur, puis collez l’URL dans la barre d’adresse.</span><span class="sxs-lookup"><span data-stu-id="47a39-193">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="47a39-194">Choisissez l’un des navigateurs, entrez un nom et un message, puis sélectionnez le bouton **Envoyer**.</span><span class="sxs-lookup"><span data-stu-id="47a39-194">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="47a39-195">Le nom et le message s’affichent instantanément sur les deux pages :</span><span class="sxs-lookup"><span data-stu-id="47a39-195">The name and message are displayed on both pages instantly:</span></span>

   ![L’exemple d’application signalr du webassembly éblouissant est ouvert dans deux fenêtres de navigateur qui affichent les messages échangés.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="47a39-197">Devis &copy;: *Star Trek VI : le pays indécouvert* 1991 [primordial](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="47a39-197">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="47a39-198">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="47a39-198">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="47a39-199">Dans une interface de commande, exécutez les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="47a39-199">In a command shell, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="47a39-200">Copiez l’URL à partir de la barre d’adresse, ouvrez un autre onglet ou instance du navigateur, puis collez l’URL dans la barre d’adresse.</span><span class="sxs-lookup"><span data-stu-id="47a39-200">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="47a39-201">Choisissez l’un des navigateurs, entrez un nom et un message, puis sélectionnez le bouton **Envoyer**.</span><span class="sxs-lookup"><span data-stu-id="47a39-201">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="47a39-202">Le nom et le message s’affichent instantanément sur les deux pages :</span><span class="sxs-lookup"><span data-stu-id="47a39-202">The name and message are displayed on both pages instantly:</span></span>

   ![L’exemple d’application signalr du webassembly éblouissant est ouvert dans deux fenêtres de navigateur qui affichent les messages échangés.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="47a39-204">Devis &copy;: *Star Trek VI : le pays indécouvert* 1991 [primordial](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="47a39-204">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="47a39-205">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="47a39-205">Next steps</span></span>

<span data-ttu-id="47a39-206">Dans ce didacticiel, vous avez appris à :</span><span class="sxs-lookup"><span data-stu-id="47a39-206">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="47a39-207">Créer un Blazor projet d’application hébergée webassembly</span><span class="sxs-lookup"><span data-stu-id="47a39-207">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="47a39-208">Ajouter la SignalR bibliothèque cliente</span><span class="sxs-lookup"><span data-stu-id="47a39-208">Add the SignalR client library</span></span>
> * <span data-ttu-id="47a39-209">Ajouter un SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="47a39-209">Add a SignalR hub</span></span>
> * <span data-ttu-id="47a39-210">Ajouter SignalR des services et un point de SignalR terminaison pour le Hub</span><span class="sxs-lookup"><span data-stu-id="47a39-210">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="47a39-211">Ajouter du code de composant Razor pour la conversation</span><span class="sxs-lookup"><span data-stu-id="47a39-211">Add Razor component code for chat</span></span>

<span data-ttu-id="47a39-212">Pour en savoir plus sur Blazor la création d’applications Blazor , consultez la documentation :</span><span class="sxs-lookup"><span data-stu-id="47a39-212">To learn more about building Blazor apps, see the Blazor documentation:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/index>

## <a name="additional-resources"></a><span data-ttu-id="47a39-213">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="47a39-213">Additional resources</span></span>

* <xref:signalr/introduction>
