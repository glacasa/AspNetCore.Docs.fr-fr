---
title: Prise en main de ASP.NET CoreBlazor
author: guardrex
description: Commencez Blazor par créer une Blazor application avec les outils de votre choix.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/30/2020
no-loc:
- Blazor
- SignalR
uid: blazor/get-started
ms.openlocfilehash: 8ef55b92c45aa07113fd4601a3c7464b42125623
ms.sourcegitcommit: 6318d2bdd63116e178c34492a904be85ec9ac108
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2020
ms.locfileid: "82604764"
---
# <a name="get-started-with-aspnet-core-blazor"></a><span data-ttu-id="e804a-103">Prise en main d’ASP.NET Core éblouissante</span><span class="sxs-lookup"><span data-stu-id="e804a-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="e804a-104">Par [Daniel Roth](https://github.com/danroth27) et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="e804a-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="e804a-105">Pour vous familiariser avec éblouissant, suivez les instructions de votre choix d’outils :</span><span class="sxs-lookup"><span data-stu-id="e804a-105">To get started with Blazor, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e804a-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e804a-106">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="e804a-107">Pour créer des applications de serveur éblouissantes, installez la dernière version de [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) avec la charge de travail **développement Web et ASP.net** .</span><span class="sxs-lookup"><span data-stu-id="e804a-107">To create Blazor Server apps, install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="e804a-108">Pour créer un serveur éblouissant et des applications webassembly éblouissantes, installez la dernière version préliminaire de [Visual Studio 2019](https://visualstudio.microsoft.com/vs/preview/) avec la charge de travail **développement Web et ASP.net** .</span><span class="sxs-lookup"><span data-stu-id="e804a-108">To create Blazor Server and Blazor WebAssembly apps, install the latest preview of [Visual Studio 2019](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="e804a-109">Pour plus d’informations sur les deux modèles d’hébergement éblouissants, le *Webassembly éblouissant* et le <xref:blazor/hosting-models> *serveur éblouissant*, consultez.</span><span class="sxs-lookup"><span data-stu-id="e804a-109">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="e804a-110">Installez le modèle d’aperçu de [Webassembly éblouissant](xref:blazor/hosting-models#blazor-webassembly) en exécutant la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="e804a-110">Install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) preview template by running the following command:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-rc1.20223.4
   ```

1. <span data-ttu-id="e804a-111">Créez un projet.</span><span class="sxs-lookup"><span data-stu-id="e804a-111">Create a new project.</span></span>

1. <span data-ttu-id="e804a-112">Sélectionnez l' **application éblouissant**.</span><span class="sxs-lookup"><span data-stu-id="e804a-112">Select **Blazor App**.</span></span> <span data-ttu-id="e804a-113">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="e804a-113">Select **Next**.</span></span>

1. <span data-ttu-id="e804a-114">Indiquez un nom de projet dans le champ **Nom du projet**, ou acceptez le nom de projet par défaut.</span><span class="sxs-lookup"><span data-stu-id="e804a-114">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="e804a-115">Confirmez que l’entrée d' **emplacement** est correcte ou indiquez un emplacement pour le projet.</span><span class="sxs-lookup"><span data-stu-id="e804a-115">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="e804a-116">Sélectionnez **Create** (Créer).</span><span class="sxs-lookup"><span data-stu-id="e804a-116">Select **Create**.</span></span>

1. <span data-ttu-id="e804a-117">Pour une expérience webassembly éblouissant (Visual Studio 16,6 Preview 2 ou version ultérieure), choisissez le modèle **application éblouissant Webassembly** .</span><span class="sxs-lookup"><span data-stu-id="e804a-117">For a Blazor WebAssembly experience (Visual Studio 16.6 Preview 2 or later), choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="e804a-118">Pour une expérience de serveur éblouissant (Visual Studio 16,4 ou version ultérieure), choisissez le modèle **application de serveur éblouissant** .</span><span class="sxs-lookup"><span data-stu-id="e804a-118">For a Blazor Server experience (Visual Studio 16.4 or later), choose the **Blazor Server App** template.</span></span> <span data-ttu-id="e804a-119">Sélectionnez **Create** (Créer).</span><span class="sxs-lookup"><span data-stu-id="e804a-119">Select **Create**.</span></span>

1. <span data-ttu-id="e804a-120">Appuyez sur <kbd>CTRL</kbd>+<kbd>F5</kbd> pour exécuter l’application.</span><span class="sxs-lookup"><span data-stu-id="e804a-120">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="e804a-121">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e804a-121">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="e804a-122">Installez le [Kit de développement logiciel (SDK) .net Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="e804a-122">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="e804a-123">Si vous le souhaitez, vous pouvez installer le modèle d’aperçu de [Webassembly éblouissant](xref:blazor/hosting-models#blazor-webassembly) en exécutant la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="e804a-123">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) preview template by running the following command:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-rc1.20223.4
   ```

   > [!NOTE]
   > <span data-ttu-id="e804a-124">La [version kit SDK .net Core 3.1.201 ou ultérieure](https://dotnet.microsoft.com/download/dotnet-core/3.1) est **requise** pour utiliser le modèle webassembly 3,2 Preview 4.</span><span class="sxs-lookup"><span data-stu-id="e804a-124">The [.NET Core SDK version 3.1.201 or later](https://dotnet.microsoft.com/download/dotnet-core/3.1) is **required** to use the 3.2 Preview 4 Blazor WebAssembly template.</span></span> <span data-ttu-id="e804a-125">Confirmez la version de kit SDK .NET Core installée `dotnet --version` en exécutant dans un interpréteur de commandes.</span><span class="sxs-lookup"><span data-stu-id="e804a-125">Confirm the installed .NET Core SDK version by running `dotnet --version` in a command shell.</span></span>

1. <span data-ttu-id="e804a-126">Installez [Visual Studio Code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="e804a-126">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

1. <span data-ttu-id="e804a-127">Installez la dernière [extension C# for Visual Studio code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) et l’extension de [débogueur JavaScript (nocturne)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) avec `debug.javascript.usePreview` la valeur `true`.</span><span class="sxs-lookup"><span data-stu-id="e804a-127">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

1. <span data-ttu-id="e804a-128">Pour une expérience de serveur éblouissant, exécutez la commande suivante dans une interface de commande :</span><span class="sxs-lookup"><span data-stu-id="e804a-128">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="e804a-129">Pour une expérience de webassembly éblouissant, exécutez la commande suivante dans une interface de commande :</span><span class="sxs-lookup"><span data-stu-id="e804a-129">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   <span data-ttu-id="e804a-130">Pour plus d’informations sur les deux modèles d’hébergement éblouissants, le *serveur éblouissant* et le *webassembly éblouissant*, consultez <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="e804a-130">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="e804a-131">Ouvrez le dossier *WebApplication1* dans Visual Studio code.</span><span class="sxs-lookup"><span data-stu-id="e804a-131">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="e804a-132">L’IDE demande que vous ajoutiez des ressources pour générer et déboguer le projet.</span><span class="sxs-lookup"><span data-stu-id="e804a-132">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="e804a-133">Sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="e804a-133">Select **Yes**.</span></span>

1. <span data-ttu-id="e804a-134">Avec le serveur éblouissant, exécutez l’application à l’aide du débogueur Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="e804a-134">With Blazor Server, run the app using the Visual Studio Code debugger.</span></span>

   <span data-ttu-id="e804a-135">Avec éblouissant webassembly, démarrez l’application à l’aide de la configuration de lancement de **.net Core (éblouissant)** , puis démarrez le navigateur à l’aide de l' **Assembly Web de débogage .net Core éblouissant dans** la configuration de lancement chrome (nécessite chrome).</span><span class="sxs-lookup"><span data-stu-id="e804a-135">With Blazor WebAssembly, start the app using the **.NET Core Launch (Blazor Standalone)** launch configuration and then start the browser using the **.NET Core Debug Blazor Web Assembly in Chrome** launch configuration (requires Chrome).</span></span> <span data-ttu-id="e804a-136">Pour plus d’informations, consultez <xref:blazor/debug#visual-studio-code>.</span><span class="sxs-lookup"><span data-stu-id="e804a-136">For more information, see <xref:blazor/debug#visual-studio-code>.</span></span>

1. <span data-ttu-id="e804a-137">Dans un navigateur, accédez à `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="e804a-137">In a browser, navigate to `https://localhost:5001`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e804a-138">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="e804a-138">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="e804a-139">Le serveur éblouissant est pris en charge dans Visual Studio pour Mac.</span><span class="sxs-lookup"><span data-stu-id="e804a-139">Blazor Server is supported in Visual Studio for Mac.</span></span> <span data-ttu-id="e804a-140">Le webassembly éblouissant n’est pas pris en charge pour le moment.</span><span class="sxs-lookup"><span data-stu-id="e804a-140">Blazor WebAssembly isn't supported at this time.</span></span> <span data-ttu-id="e804a-141">Pour générer des applications webassembly éblouissantes sur macOS, suivez les instructions de l’onglet **CLI .net Core** .</span><span class="sxs-lookup"><span data-stu-id="e804a-141">To build Blazor WebAssembly apps on macOS, follow the guidance on the **.NET Core CLI** tab.</span></span>

1. <span data-ttu-id="e804a-142">Installez [Visual Studio pour Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="e804a-142">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="e804a-143">Sélectionnez **fichier** > **nouvelle solution** ou créer un **nouveau projet**.</span><span class="sxs-lookup"><span data-stu-id="e804a-143">Select **File** > **New Solution** or create a **New Project**.</span></span>

1. <span data-ttu-id="e804a-144">Dans la barre latérale, sélectionnez**application** **Web et console** > .</span><span class="sxs-lookup"><span data-stu-id="e804a-144">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="e804a-145">Sélectionnez le modèle **application de serveur éblouissant** .</span><span class="sxs-lookup"><span data-stu-id="e804a-145">Select the **Blazor Server App** template.</span></span> <span data-ttu-id="e804a-146">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="e804a-146">Select **Next**.</span></span>

   <span data-ttu-id="e804a-147">Pour plus d’informations sur le modèle d’hébergement de serveur <xref:blazor/hosting-models>éblouissant, consultez.</span><span class="sxs-lookup"><span data-stu-id="e804a-147">For information on the Blazor Server hosting model, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="e804a-148">Vérifiez que la version cible de .NET **Framework** est définie sur **.net Core 3,1** , puis sélectionnez **suivant**.</span><span class="sxs-lookup"><span data-stu-id="e804a-148">Confirm that the **Target Framework** is set to **.NET Core 3.1** and select **Next**.</span></span>

1. <span data-ttu-id="e804a-149">Dans le champ **nom du projet** , nommez `WebApplication1`l’application.</span><span class="sxs-lookup"><span data-stu-id="e804a-149">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="e804a-150">Sélectionnez **Create** (Créer).</span><span class="sxs-lookup"><span data-stu-id="e804a-150">Select **Create**.</span></span>

1. <span data-ttu-id="e804a-151"> > Sélectionnez **exécute\r\*\*\**exécuter sans débogage** pour exécuter l’application *sans le débogueur*.</span><span class="sxs-lookup"><span data-stu-id="e804a-151">Select **Run** > **Run Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="e804a-152">Exécutez l’application avec le bouton **Démarrer le débogage** ou l’exécution (&#9654;) pour exécuter l’application *avec le débogueur*.</span><span class="sxs-lookup"><span data-stu-id="e804a-152">Run the app with **Start Debugging** or the Run (&#9654;) button to run the app *with the debugger*.</span></span>

<span data-ttu-id="e804a-153">Si une invite s’affiche pour faire confiance au certificat de développement, approuvez le certificat et continuez.</span><span class="sxs-lookup"><span data-stu-id="e804a-153">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="e804a-154">Les mots de passe utilisateur et trousseau sont requis pour approuver le certificat.</span><span class="sxs-lookup"><span data-stu-id="e804a-154">The user and keychain passwords are required to trust the certificate.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="e804a-155">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="e804a-155">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="e804a-156">Installez le [Kit de développement logiciel (SDK) .net Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="e804a-156">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="e804a-157">Si vous le souhaitez, vous pouvez installer le modèle d’aperçu de [Webassembly éblouissant](xref:blazor/hosting-models#blazor-webassembly) en exécutant la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="e804a-157">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) preview template by running the following command:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-rc1.20223.4
   ```

   > [!NOTE]
   > <span data-ttu-id="e804a-158">La [version kit SDK .net Core 3.1.201 ou ultérieure](https://dotnet.microsoft.com/download/dotnet-core/3.1) est **requise** pour utiliser le modèle webassembly 3,2 Preview 4.</span><span class="sxs-lookup"><span data-stu-id="e804a-158">The [.NET Core SDK version 3.1.201 or later](https://dotnet.microsoft.com/download/dotnet-core/3.1) is **required** to use the 3.2 Preview 4 Blazor WebAssembly template.</span></span> <span data-ttu-id="e804a-159">Confirmez la version de kit SDK .NET Core installée `dotnet --version` en exécutant dans un interpréteur de commandes.</span><span class="sxs-lookup"><span data-stu-id="e804a-159">Confirm the installed .NET Core SDK version by running `dotnet --version` in a command shell.</span></span>

1. <span data-ttu-id="e804a-160">Pour une expérience de serveur éblouissant, exécutez les commandes suivantes dans une interface de commande :</span><span class="sxs-lookup"><span data-stu-id="e804a-160">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="e804a-161">Pour une expérience de webassembly éblouissant, exécutez les commandes suivantes dans une interface de commande :</span><span class="sxs-lookup"><span data-stu-id="e804a-161">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="e804a-162">Pour plus d’informations sur les deux modèles d’hébergement éblouissants, le *serveur éblouissant* et le *webassembly éblouissant*, consultez <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="e804a-162">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="e804a-163">Dans un navigateur, accédez à `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="e804a-163">In a browser, navigate to `https://localhost:5001`.</span></span>

---

<span data-ttu-id="e804a-164">Plusieurs pages sont disponibles à partir des onglets de la barre latérale :</span><span class="sxs-lookup"><span data-stu-id="e804a-164">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="e804a-165">Accueil</span><span class="sxs-lookup"><span data-stu-id="e804a-165">Home</span></span>
* <span data-ttu-id="e804a-166">Compteur</span><span class="sxs-lookup"><span data-stu-id="e804a-166">Counter</span></span>
* <span data-ttu-id="e804a-167">Extraire les données</span><span class="sxs-lookup"><span data-stu-id="e804a-167">Fetch data</span></span>

<span data-ttu-id="e804a-168">Sur la page Counter, sélectionnez le bouton **Click me** pour incrémenter le compteur sans actualisation de la page.</span><span class="sxs-lookup"><span data-stu-id="e804a-168">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="e804a-169">L’incrémentation d’un compteur dans une page Web nécessite normalement l’écriture Blazor de JavaScript, mais avec vous pouvez utiliser C#.</span><span class="sxs-lookup"><span data-stu-id="e804a-169">Incrementing a counter in a webpage normally requires writing JavaScript, but with Blazor you can use C#.</span></span>

<span data-ttu-id="e804a-170">*Pages/Counter.razor* :</span><span class="sxs-lookup"><span data-stu-id="e804a-170">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="e804a-171">Une demande de `/counter` dans le navigateur, comme spécifié par la `@page` directive en haut, fait en sorte `Counter` que le composant restitue son contenu.</span><span class="sxs-lookup"><span data-stu-id="e804a-171">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="e804a-172">Les composants sont rendus dans une représentation en mémoire de l’arborescence de rendu qui peut ensuite être utilisée pour mettre à jour l’interface utilisateur de manière flexible et efficace.</span><span class="sxs-lookup"><span data-stu-id="e804a-172">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="e804a-173">Chaque fois que le bouton **Click Me** est sélectionné :</span><span class="sxs-lookup"><span data-stu-id="e804a-173">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="e804a-174">L' `onclick` événement est déclenché.</span><span class="sxs-lookup"><span data-stu-id="e804a-174">The `onclick` event is fired.</span></span>
* <span data-ttu-id="e804a-175">La méthode `IncrementCount` est appelée.</span><span class="sxs-lookup"><span data-stu-id="e804a-175">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="e804a-176">`currentCount` Est incrémenté.</span><span class="sxs-lookup"><span data-stu-id="e804a-176">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="e804a-177">Le composant est de nouveau restitué.</span><span class="sxs-lookup"><span data-stu-id="e804a-177">The component is rendered again.</span></span>

<span data-ttu-id="e804a-178">Le runtime compare le nouveau contenu au contenu précédent et applique uniquement le contenu modifié à l’Document Object Model (DOM).</span><span class="sxs-lookup"><span data-stu-id="e804a-178">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="e804a-179">Ajoutez un composant à un autre composant à l’aide de la syntaxe HTML.</span><span class="sxs-lookup"><span data-stu-id="e804a-179">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="e804a-180">Par exemple, ajoutez le `Counter` composant à la page d’accueil de l’application `<Counter />` en ajoutant un `Index` élément au composant.</span><span class="sxs-lookup"><span data-stu-id="e804a-180">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="e804a-181">*Pages/Index.razor* :</span><span class="sxs-lookup"><span data-stu-id="e804a-181">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="e804a-182">Exécutez l'application.</span><span class="sxs-lookup"><span data-stu-id="e804a-182">Run the app.</span></span> <span data-ttu-id="e804a-183">La page d’accueil possède son propre compteur fourni `Counter` par le composant.</span><span class="sxs-lookup"><span data-stu-id="e804a-183">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="e804a-184">Les paramètres de composant sont spécifiés à l’aide d’attributs ou de [contenu enfant](xref:blazor/components#child-content), ce qui vous permet de définir des propriétés sur le composant enfant.</span><span class="sxs-lookup"><span data-stu-id="e804a-184">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="e804a-185">Pour ajouter un paramètre au `Counter` composant, mettez à jour le bloc `@code` du composant :</span><span class="sxs-lookup"><span data-stu-id="e804a-185">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="e804a-186">Ajoutez une propriété publique pour `IncrementAmount` avec un `[Parameter]` attribut.</span><span class="sxs-lookup"><span data-stu-id="e804a-186">Add a public property for `IncrementAmount` with a `[Parameter]` attribute.</span></span>
* <span data-ttu-id="e804a-187">Modifiez la méthode `IncrementCount` pour utiliser `IncrementAmount` lorsque vous augmentez la valeur de `currentCount`.</span><span class="sxs-lookup"><span data-stu-id="e804a-187">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="e804a-188">*Pages/Counter.razor* :</span><span class="sxs-lookup"><span data-stu-id="e804a-188">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="e804a-189">Spécifiez `IncrementAmount` dans l' `Index` élément du `<Counter>` composant à l’aide d’un attribut.</span><span class="sxs-lookup"><span data-stu-id="e804a-189">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="e804a-190">*Pages/Index.razor* :</span><span class="sxs-lookup"><span data-stu-id="e804a-190">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="e804a-191">Exécutez l'application.</span><span class="sxs-lookup"><span data-stu-id="e804a-191">Run the app.</span></span> <span data-ttu-id="e804a-192">Le `Index` composant a son propre compteur qui est incrémenté de dix chaque fois que le bouton **Click Me** est sélectionné.</span><span class="sxs-lookup"><span data-stu-id="e804a-192">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="e804a-193">Le `Counter` composant (*Counter. Razor*) de `/counter` continue à être incrémenté d’une unité.</span><span class="sxs-lookup"><span data-stu-id="e804a-193">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="e804a-194">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="e804a-194">Next steps</span></span>

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="e804a-195">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="e804a-195">Additional resources</span></span>

* <xref:blazor/templates>
* <xref:signalr/introduction>
