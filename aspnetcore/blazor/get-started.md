---
title: Prise en main de ASP.NET CoreBlazor
author: guardrex
description: Commencez Blazor par créer une Blazor application avec les outils de votre choix.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/02/2020
no-loc:
- Blazor
- SignalR
uid: blazor/get-started
ms.openlocfilehash: 6cf1de6e68d04505ce4ba5d18f2a7d0bbe5be333
ms.sourcegitcommit: c19e388c83c981232e6f128d97440262adfe06e2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2020
ms.locfileid: "82727655"
---
# <a name="get-started-with-aspnet-core-blazor"></a><span data-ttu-id="43185-103">Prise en main d’ASP.NET Core éblouissante</span><span class="sxs-lookup"><span data-stu-id="43185-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="43185-104">Par [Daniel Roth](https://github.com/danroth27) et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="43185-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="43185-105">Pour vous familiariser avec éblouissant, suivez les instructions de votre choix d’outils :</span><span class="sxs-lookup"><span data-stu-id="43185-105">To get started with Blazor, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="43185-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="43185-106">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="43185-107">Pour créer des applications de serveur éblouissantes, installez la dernière version de [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) avec la charge de travail **développement Web et ASP.net** .</span><span class="sxs-lookup"><span data-stu-id="43185-107">To create Blazor Server apps, install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="43185-108">Pour créer un serveur éblouissant et des applications webassembly éblouissantes, installez la dernière version préliminaire de [Visual Studio 2019](https://visualstudio.microsoft.com/vs/preview/) avec la charge de travail **développement Web et ASP.net** .</span><span class="sxs-lookup"><span data-stu-id="43185-108">To create Blazor Server and Blazor WebAssembly apps, install the latest preview of [Visual Studio 2019](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="43185-109">Pour plus d’informations sur les deux modèles d’hébergement éblouissants, le *Webassembly éblouissant* et le <xref:blazor/hosting-models> *serveur éblouissant*, consultez.</span><span class="sxs-lookup"><span data-stu-id="43185-109">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="43185-110">Installez le modèle d’aperçu de webassembly éblouissant en exécutant la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="43185-110">Install the Blazor WebAssembly Preview template by running the following command:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-rc1.20223.4
   ```

1. <span data-ttu-id="43185-111">Créez un projet.</span><span class="sxs-lookup"><span data-stu-id="43185-111">Create a new project.</span></span>

1. <span data-ttu-id="43185-112">Sélectionnez l' **application éblouissant**.</span><span class="sxs-lookup"><span data-stu-id="43185-112">Select **Blazor App**.</span></span> <span data-ttu-id="43185-113">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="43185-113">Select **Next**.</span></span>

1. <span data-ttu-id="43185-114">Indiquez un nom de projet dans le champ **Nom du projet**, ou acceptez le nom de projet par défaut.</span><span class="sxs-lookup"><span data-stu-id="43185-114">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="43185-115">Confirmez que l’entrée d' **emplacement** est correcte ou indiquez un emplacement pour le projet.</span><span class="sxs-lookup"><span data-stu-id="43185-115">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="43185-116">Sélectionnez **Create** (Créer).</span><span class="sxs-lookup"><span data-stu-id="43185-116">Select **Create**.</span></span>

1. <span data-ttu-id="43185-117">Pour une expérience webassembly éblouissant (Visual Studio 16,6 Preview 2 ou version ultérieure), choisissez le modèle **application éblouissant Webassembly** .</span><span class="sxs-lookup"><span data-stu-id="43185-117">For a Blazor WebAssembly experience (Visual Studio 16.6 Preview 2 or later), choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="43185-118">Pour une expérience de serveur éblouissant (Visual Studio 16,4 ou version ultérieure), choisissez le modèle **application de serveur éblouissant** .</span><span class="sxs-lookup"><span data-stu-id="43185-118">For a Blazor Server experience (Visual Studio 16.4 or later), choose the **Blazor Server App** template.</span></span> <span data-ttu-id="43185-119">Sélectionnez **Create** (Créer).</span><span class="sxs-lookup"><span data-stu-id="43185-119">Select **Create**.</span></span>

1. <span data-ttu-id="43185-120">Appuyez sur <kbd>CTRL</kbd>+<kbd>F5</kbd> pour exécuter l’application.</span><span class="sxs-lookup"><span data-stu-id="43185-120">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="43185-121">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="43185-121">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="43185-122">Installez le [Kit de développement logiciel (SDK) .net Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="43185-122">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="43185-123">Si vous le souhaitez, vous pouvez installer le modèle d’aperçu de [Webassembly éblouissant](xref:blazor/hosting-models#blazor-webassembly) en exécutant la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="43185-123">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) preview template by running the following command:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-rc1.20223.4
   ```
   
   <span data-ttu-id="43185-124">Pour plus d’informations sur les deux modèles d’hébergement éblouissants, le *Webassembly éblouissant* et le <xref:blazor/hosting-models> *serveur éblouissant*, consultez.</span><span class="sxs-lookup"><span data-stu-id="43185-124">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

   > [!NOTE]
   > <span data-ttu-id="43185-125">La [version kit SDK .net Core 3.1.201 ou ultérieure](https://dotnet.microsoft.com/download/dotnet-core/3.1) est **requise** pour utiliser le modèle webassembly éblouissant 3,2 Preview.</span><span class="sxs-lookup"><span data-stu-id="43185-125">The [.NET Core SDK version 3.1.201 or later](https://dotnet.microsoft.com/download/dotnet-core/3.1) is **required** to use the 3.2 Preview Blazor WebAssembly template.</span></span> <span data-ttu-id="43185-126">Confirmez la version de kit SDK .NET Core installée `dotnet --version` en exécutant dans un interpréteur de commandes.</span><span class="sxs-lookup"><span data-stu-id="43185-126">Confirm the installed .NET Core SDK version by running `dotnet --version` in a command shell.</span></span>

1. <span data-ttu-id="43185-127">Installez [Visual Studio Code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="43185-127">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

1. <span data-ttu-id="43185-128">Installez la dernière [extension C# for Visual Studio code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) et l’extension de [débogueur JavaScript (nocturne)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) avec `debug.javascript.usePreview` la valeur `true`.</span><span class="sxs-lookup"><span data-stu-id="43185-128">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

1. <span data-ttu-id="43185-129">Pour une expérience de serveur éblouissant, exécutez la commande suivante dans une interface de commande :</span><span class="sxs-lookup"><span data-stu-id="43185-129">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="43185-130">Pour une expérience de webassembly éblouissant, exécutez la commande suivante dans une interface de commande :</span><span class="sxs-lookup"><span data-stu-id="43185-130">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

1. <span data-ttu-id="43185-131">Ouvrez le dossier *WebApplication1* dans Visual Studio code.</span><span class="sxs-lookup"><span data-stu-id="43185-131">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="43185-132">L’IDE demande que vous ajoutiez des ressources pour générer et déboguer le projet.</span><span class="sxs-lookup"><span data-stu-id="43185-132">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="43185-133">Sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="43185-133">Select **Yes**.</span></span>

1. <span data-ttu-id="43185-134">Avec le serveur éblouissant, exécutez l’application à l’aide du débogueur Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="43185-134">With Blazor Server, run the app using the Visual Studio Code debugger.</span></span>

   <span data-ttu-id="43185-135">Avec éblouissant webassembly, démarrez l’application à l’aide de la configuration de lancement de **.net Core (éblouissant)** , puis démarrez le navigateur à l’aide de l' **Assembly Web de débogage .net Core éblouissant dans** la configuration de lancement chrome (nécessite chrome).</span><span class="sxs-lookup"><span data-stu-id="43185-135">With Blazor WebAssembly, start the app using the **.NET Core Launch (Blazor Standalone)** launch configuration and then start the browser using the **.NET Core Debug Blazor Web Assembly in Chrome** launch configuration (requires Chrome).</span></span> <span data-ttu-id="43185-136">Pour plus d’informations, consultez <xref:blazor/debug#visual-studio-code>.</span><span class="sxs-lookup"><span data-stu-id="43185-136">For more information, see <xref:blazor/debug#visual-studio-code>.</span></span>

1. <span data-ttu-id="43185-137">Dans un navigateur, accédez à `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="43185-137">In a browser, navigate to `https://localhost:5001`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="43185-138">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="43185-138">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="43185-139">Le serveur éblouissant est pris en charge dans Visual Studio pour Mac.</span><span class="sxs-lookup"><span data-stu-id="43185-139">Blazor Server is supported in Visual Studio for Mac.</span></span> <span data-ttu-id="43185-140">Le webassembly éblouissant n’est pas pris en charge pour le moment.</span><span class="sxs-lookup"><span data-stu-id="43185-140">Blazor WebAssembly isn't supported at this time.</span></span> <span data-ttu-id="43185-141">Pour créer des applications webassembly éblouissantes sur macOS, suivez les instructions de l’onglet **CLI .net Core** . Pour plus d’informations sur les deux modèles d’hébergement éblouissants, le *Webassembly éblouissant* et le <xref:blazor/hosting-models> *serveur éblouissant*, consultez.</span><span class="sxs-lookup"><span data-stu-id="43185-141">To create Blazor WebAssembly apps on macOS, follow the guidance on the **.NET Core CLI** tab. For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="43185-142">Installez [Visual Studio pour Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="43185-142">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="43185-143">Sélectionnez **fichier** > **nouvelle solution** ou créer un **nouveau** projet à partir de la **fenêtre démarrer**.</span><span class="sxs-lookup"><span data-stu-id="43185-143">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="43185-144">Dans la barre latérale, sélectionnez**application** **.net Core** > .</span><span class="sxs-lookup"><span data-stu-id="43185-144">In the sidebar, select **.NET Core** > **App**.</span></span>

1. <span data-ttu-id="43185-145">Sélectionnez le modèle **application de serveur éblouissant** .</span><span class="sxs-lookup"><span data-stu-id="43185-145">Select the **Blazor Server App** template.</span></span> <span data-ttu-id="43185-146">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="43185-146">Select **Next**.</span></span>

1. <span data-ttu-id="43185-147">Vérifiez les configurations suivantes :</span><span class="sxs-lookup"><span data-stu-id="43185-147">Confirm the following configurations:</span></span>

   * <span data-ttu-id="43185-148">**Framework cible** défini sur **.net Core 3,1**.</span><span class="sxs-lookup"><span data-stu-id="43185-148">**Target Framework** set to **.NET Core 3.1**.</span></span>
   * <span data-ttu-id="43185-149">**L’authentification** est définie sur **aucune authentification**.</span><span class="sxs-lookup"><span data-stu-id="43185-149">**Authentication** set to **No Authentication**.</span></span>
   
   <span data-ttu-id="43185-150">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="43185-150">Select **Next**.</span></span>

1. <span data-ttu-id="43185-151">Dans le champ **nom du projet** , nommez `WebApplication1`l’application.</span><span class="sxs-lookup"><span data-stu-id="43185-151">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="43185-152">Sélectionnez **Create** (Créer).</span><span class="sxs-lookup"><span data-stu-id="43185-152">Select **Create**.</span></span>

1. <span data-ttu-id="43185-153">Sélectionnez **exécuter** > **Démarrer sans débogage** pour exécuter l’application *sans le débogueur*.</span><span class="sxs-lookup"><span data-stu-id="43185-153">Select **Run** > **Start Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="43185-154">Le débogage n’est pas pris en charge pour l’instant.</span><span class="sxs-lookup"><span data-stu-id="43185-154">Debugging isn't supported at this time.</span></span>

<!-- HOLD FOR 8.6 GA

1. Select **File** > **New Solution** or create a **New** project from the **Start Window**.

1. In the sidebar, select **Web and Console** > **App**.

1. For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template. For a Blazor Server experience, choose the **Blazor Server App** template. Select **Next**.

   For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.

1. Confirm the following configurations:

   * **Target Framework** set to **.NET Core 3.1**.
   * **Authentication** set to **No Authentication**.
   
   Select **Next**.

1. In the **Project Name** field, name the app `WebApplication1`. Select **Create**.

1. Select **Run** > **Start Without Debugging** to run the app *without the debugger*. Run the app with **Run** > **Start Debugging** or the Run (&#9654;) button to run the app *with the debugger*.

-->

<span data-ttu-id="43185-155">Si une invite s’affiche pour faire confiance au certificat de développement, approuvez le certificat et continuez.</span><span class="sxs-lookup"><span data-stu-id="43185-155">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="43185-156">Les mots de passe utilisateur et trousseau sont requis pour approuver le certificat.</span><span class="sxs-lookup"><span data-stu-id="43185-156">The user and keychain passwords are required to trust the certificate.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="43185-157">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="43185-157">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="43185-158">Installez le [Kit de développement logiciel (SDK) .net Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="43185-158">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="43185-159">Si vous le souhaitez, vous pouvez installer le modèle d’aperçu de webassembly éblouissant en exécutant la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="43185-159">Optionally install the Blazor WebAssembly preview template by running the following command:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-rc1.20223.4
   ```
   
   <span data-ttu-id="43185-160">Pour plus d’informations sur les deux modèles d’hébergement éblouissants, le *Webassembly éblouissant* et le <xref:blazor/hosting-models> *serveur éblouissant*, consultez.</span><span class="sxs-lookup"><span data-stu-id="43185-160">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

   > [!NOTE]
   > <span data-ttu-id="43185-161">La [version kit SDK .net Core 3.1.201 ou ultérieure](https://dotnet.microsoft.com/download/dotnet-core/3.1) est **requise** pour utiliser le modèle webassembly éblouissant 3,2 Preview.</span><span class="sxs-lookup"><span data-stu-id="43185-161">The [.NET Core SDK version 3.1.201 or later](https://dotnet.microsoft.com/download/dotnet-core/3.1) is **required** to use the 3.2 Preview Blazor WebAssembly template.</span></span> <span data-ttu-id="43185-162">Confirmez la version de kit SDK .NET Core installée `dotnet --version` en exécutant dans un interpréteur de commandes.</span><span class="sxs-lookup"><span data-stu-id="43185-162">Confirm the installed .NET Core SDK version by running `dotnet --version` in a command shell.</span></span>

1. <span data-ttu-id="43185-163">Pour une expérience de serveur éblouissant, exécutez les commandes suivantes dans une interface de commande :</span><span class="sxs-lookup"><span data-stu-id="43185-163">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="43185-164">Pour une expérience de webassembly éblouissant, exécutez les commandes suivantes dans une interface de commande :</span><span class="sxs-lookup"><span data-stu-id="43185-164">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

1. <span data-ttu-id="43185-165">Dans un navigateur, accédez à `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="43185-165">In a browser, navigate to `https://localhost:5001`.</span></span>

---

<span data-ttu-id="43185-166">Plusieurs pages sont disponibles à partir des onglets de la barre latérale :</span><span class="sxs-lookup"><span data-stu-id="43185-166">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="43185-167">Accueil</span><span class="sxs-lookup"><span data-stu-id="43185-167">Home</span></span>
* <span data-ttu-id="43185-168">Compteur</span><span class="sxs-lookup"><span data-stu-id="43185-168">Counter</span></span>
* <span data-ttu-id="43185-169">Extraire les données</span><span class="sxs-lookup"><span data-stu-id="43185-169">Fetch data</span></span>

<span data-ttu-id="43185-170">Sur la page Counter, sélectionnez le bouton **Click me** pour incrémenter le compteur sans actualisation de la page.</span><span class="sxs-lookup"><span data-stu-id="43185-170">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="43185-171">L’incrémentation d’un compteur dans une page Web nécessite normalement l’écriture Blazor de JavaScript, mais avec vous pouvez utiliser C#.</span><span class="sxs-lookup"><span data-stu-id="43185-171">Incrementing a counter in a webpage normally requires writing JavaScript, but with Blazor you can use C#.</span></span>

<span data-ttu-id="43185-172">*Pages/Counter.razor* :</span><span class="sxs-lookup"><span data-stu-id="43185-172">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="43185-173">Une demande de `/counter` dans le navigateur, comme spécifié par la `@page` directive en haut, fait en sorte `Counter` que le composant restitue son contenu.</span><span class="sxs-lookup"><span data-stu-id="43185-173">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="43185-174">Les composants sont rendus dans une représentation en mémoire de l’arborescence de rendu qui peut ensuite être utilisée pour mettre à jour l’interface utilisateur de manière flexible et efficace.</span><span class="sxs-lookup"><span data-stu-id="43185-174">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="43185-175">Chaque fois que le bouton **Click Me** est sélectionné :</span><span class="sxs-lookup"><span data-stu-id="43185-175">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="43185-176">L' `onclick` événement est déclenché.</span><span class="sxs-lookup"><span data-stu-id="43185-176">The `onclick` event is fired.</span></span>
* <span data-ttu-id="43185-177">La méthode `IncrementCount` est appelée.</span><span class="sxs-lookup"><span data-stu-id="43185-177">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="43185-178">`currentCount` Est incrémenté.</span><span class="sxs-lookup"><span data-stu-id="43185-178">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="43185-179">Le composant est de nouveau restitué.</span><span class="sxs-lookup"><span data-stu-id="43185-179">The component is rendered again.</span></span>

<span data-ttu-id="43185-180">Le runtime compare le nouveau contenu au contenu précédent et applique uniquement le contenu modifié à l’Document Object Model (DOM).</span><span class="sxs-lookup"><span data-stu-id="43185-180">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="43185-181">Ajoutez un composant à un autre composant à l’aide de la syntaxe HTML.</span><span class="sxs-lookup"><span data-stu-id="43185-181">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="43185-182">Par exemple, ajoutez le `Counter` composant à la page d’accueil de l’application `<Counter />` en ajoutant un `Index` élément au composant.</span><span class="sxs-lookup"><span data-stu-id="43185-182">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="43185-183">*Pages/Index.razor* :</span><span class="sxs-lookup"><span data-stu-id="43185-183">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="43185-184">Exécutez l'application.</span><span class="sxs-lookup"><span data-stu-id="43185-184">Run the app.</span></span> <span data-ttu-id="43185-185">La page d’accueil possède son propre compteur fourni `Counter` par le composant.</span><span class="sxs-lookup"><span data-stu-id="43185-185">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="43185-186">Les paramètres de composant sont spécifiés à l’aide d’attributs ou de [contenu enfant](xref:blazor/components#child-content), ce qui vous permet de définir des propriétés sur le composant enfant.</span><span class="sxs-lookup"><span data-stu-id="43185-186">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="43185-187">Pour ajouter un paramètre au `Counter` composant, mettez à jour le bloc `@code` du composant :</span><span class="sxs-lookup"><span data-stu-id="43185-187">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="43185-188">Ajoutez une propriété publique pour `IncrementAmount` avec un `[Parameter]` attribut.</span><span class="sxs-lookup"><span data-stu-id="43185-188">Add a public property for `IncrementAmount` with a `[Parameter]` attribute.</span></span>
* <span data-ttu-id="43185-189">Modifiez la méthode `IncrementCount` pour utiliser `IncrementAmount` lorsque vous augmentez la valeur de `currentCount`.</span><span class="sxs-lookup"><span data-stu-id="43185-189">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="43185-190">*Pages/Counter.razor* :</span><span class="sxs-lookup"><span data-stu-id="43185-190">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="43185-191">Spécifiez `IncrementAmount` dans l' `Index` élément du `<Counter>` composant à l’aide d’un attribut.</span><span class="sxs-lookup"><span data-stu-id="43185-191">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="43185-192">*Pages/Index.razor* :</span><span class="sxs-lookup"><span data-stu-id="43185-192">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="43185-193">Exécutez l'application.</span><span class="sxs-lookup"><span data-stu-id="43185-193">Run the app.</span></span> <span data-ttu-id="43185-194">Le `Index` composant a son propre compteur qui est incrémenté de dix chaque fois que le bouton **Click Me** est sélectionné.</span><span class="sxs-lookup"><span data-stu-id="43185-194">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="43185-195">Le `Counter` composant (*Counter. Razor*) de `/counter` continue à être incrémenté d’une unité.</span><span class="sxs-lookup"><span data-stu-id="43185-195">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="43185-196">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="43185-196">Next steps</span></span>

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="43185-197">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="43185-197">Additional resources</span></span>

* <xref:blazor/templates>
* <xref:signalr/introduction>
