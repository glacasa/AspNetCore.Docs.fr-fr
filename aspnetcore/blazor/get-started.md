---
title: Démarrer avec ASP.NET CoreBlazor
author: guardrex
description: Démarssez-vous Blazor Blazor par la construction d’une application avec l’outillage de votre choix.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
no-loc:
- Blazor
- SignalR
uid: blazor/get-started
ms.openlocfilehash: c49209afde21046a6bc0b197cc4b8d93b164b23e
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80471816"
---
# <a name="get-started-with-aspnet-core-blazor"></a><span data-ttu-id="f4cc5-103">Lancez-vous avec ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="f4cc5-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="f4cc5-104">Par [Daniel Roth](https://github.com/danroth27) et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="f4cc5-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="f4cc5-105">Pour commencer avec Blazor, suivez les conseils pour votre choix d’outillage:</span><span class="sxs-lookup"><span data-stu-id="f4cc5-105">To get started with Blazor, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f4cc5-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f4cc5-106">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="f4cc5-107">Pour créer des applications Blazor Server, installez la dernière version de [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) avec la charge de travail **ASP.NET et le développement web.**</span><span class="sxs-lookup"><span data-stu-id="f4cc5-107">To create Blazor Server apps, install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="f4cc5-108">Pour créer des applications Blazor Server et Blazor WebAssembly, installez le dernier aperçu de [Visual Studio 2019](https://visualstudio.microsoft.com/vs/preview/) avec la charge de travail **ASP.NET et le développement web.**</span><span class="sxs-lookup"><span data-stu-id="f4cc5-108">To create Blazor Server and Blazor WebAssembly apps, install the latest preview of [Visual Studio 2019](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="f4cc5-109">Pour plus d’informations sur les deux modèles d’hébergement Blazor, <xref:blazor/hosting-models> *Blazor WebAssembly* et *Blazor Server*, voir .</span><span class="sxs-lookup"><span data-stu-id="f4cc5-109">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="f4cc5-110">Créez un projet.</span><span class="sxs-lookup"><span data-stu-id="f4cc5-110">Create a new project.</span></span>

1. <span data-ttu-id="f4cc5-111">Sélectionnez **Blazor App**.</span><span class="sxs-lookup"><span data-stu-id="f4cc5-111">Select **Blazor App**.</span></span> <span data-ttu-id="f4cc5-112">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="f4cc5-112">Select **Next**.</span></span>

1. <span data-ttu-id="f4cc5-113">Indiquez un nom de projet dans le champ **Nom du projet**, ou acceptez le nom de projet par défaut.</span><span class="sxs-lookup"><span data-stu-id="f4cc5-113">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="f4cc5-114">Confirmez que **l’entrée de localisation** est correcte ou fournissez un emplacement pour le projet.</span><span class="sxs-lookup"><span data-stu-id="f4cc5-114">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="f4cc5-115">Sélectionnez **Create** (Créer).</span><span class="sxs-lookup"><span data-stu-id="f4cc5-115">Select **Create**.</span></span>

1. <span data-ttu-id="f4cc5-116">Pour une expérience WebAssembly Blazor (Visual Studio 16.6 Aperçu 2 ou plus tard), choisissez le modèle **Blazor WebAssembly App.**</span><span class="sxs-lookup"><span data-stu-id="f4cc5-116">For a Blazor WebAssembly experience (Visual Studio 16.6 Preview 2 or later), choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="f4cc5-117">Pour une expérience Blazor Server (Visual Studio 16.4 ou plus tard), choisissez le modèle **Blazor Server App.**</span><span class="sxs-lookup"><span data-stu-id="f4cc5-117">For a Blazor Server experience (Visual Studio 16.4 or later), choose the **Blazor Server App** template.</span></span> <span data-ttu-id="f4cc5-118">Sélectionnez **Create** (Créer).</span><span class="sxs-lookup"><span data-stu-id="f4cc5-118">Select **Create**.</span></span>

1. <span data-ttu-id="f4cc5-119">Appuyez sur <kbd>Ctrl</kbd>+<kbd>F5</kbd> pour exécuter l’application.</span><span class="sxs-lookup"><span data-stu-id="f4cc5-119">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f4cc5-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f4cc5-120">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="f4cc5-121">Installer le [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="f4cc5-121">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="f4cc5-122">Installez le modèle [d’aperçu WebAssembly De Blazor](xref:blazor/hosting-models#blazor-webassembly) en exécutant la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="f4cc5-122">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) preview template by running the following command:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview3.20168.3
   ```

   > [!NOTE]
   > <span data-ttu-id="f4cc5-123">La [version SDK Core .NET 3.1.201 ou plus tard](https://dotnet.microsoft.com/download/dotnet-core/3.1) est **nécessaire** pour utiliser le modèle WebAssembly 3.2 Preview 3 Blazor.</span><span class="sxs-lookup"><span data-stu-id="f4cc5-123">The [.NET Core SDK version 3.1.201 or later](https://dotnet.microsoft.com/download/dotnet-core/3.1) is **required** to use the 3.2 Preview 3 Blazor WebAssembly template.</span></span> <span data-ttu-id="f4cc5-124">Confirmez la version SDK core `dotnet --version` .NET installée en exécutant dans une coque de commande.</span><span class="sxs-lookup"><span data-stu-id="f4cc5-124">Confirm the installed .NET Core SDK version by running `dotnet --version` in a command shell.</span></span>

1. <span data-ttu-id="f4cc5-125">Installer [Visual Studio Code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="f4cc5-125">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

1. <span data-ttu-id="f4cc5-126">Installez le dernier [C pour l’extension visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) et l’extension [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) avec `debug.javascript.usePreview` ensemble à `true`.</span><span class="sxs-lookup"><span data-stu-id="f4cc5-126">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

1. <span data-ttu-id="f4cc5-127">Pour une expérience Blazor Server, exécutez la commande suivante dans une coque de commande :</span><span class="sxs-lookup"><span data-stu-id="f4cc5-127">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="f4cc5-128">Pour une expérience Blazor WebAssembly, exécutez la commande suivante dans une coque de commande :</span><span class="sxs-lookup"><span data-stu-id="f4cc5-128">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   <span data-ttu-id="f4cc5-129">Pour plus d’informations sur les deux modèles d’hébergement Blazor, <xref:blazor/hosting-models> *Blazor Server* et *Blazor WebAssembly*, voir .</span><span class="sxs-lookup"><span data-stu-id="f4cc5-129">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="f4cc5-130">Ouvrez le dossier *WebApplication1* dans Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="f4cc5-130">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="f4cc5-131">L’IDE vous demande d’ajouter des actifs pour construire et déboquer le projet.</span><span class="sxs-lookup"><span data-stu-id="f4cc5-131">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="f4cc5-132">Sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="f4cc5-132">Select **Yes**.</span></span>

1. <span data-ttu-id="f4cc5-133">Exécutez l’application à l’aide du débbugger Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="f4cc5-133">Run the app using the Visual Studio Code debugger.</span></span>

1. <span data-ttu-id="f4cc5-134">Dans un navigateur, accédez à `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="f4cc5-134">In a browser, navigate to `https://localhost:5001`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f4cc5-135">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="f4cc5-135">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="f4cc5-136">Blazor Server est pris en charge dans Visual Studio pour Mac.</span><span class="sxs-lookup"><span data-stu-id="f4cc5-136">Blazor Server is supported in Visual Studio for Mac.</span></span> <span data-ttu-id="f4cc5-137">Blazor WebAssembly n’est pas pris en charge pour le moment.</span><span class="sxs-lookup"><span data-stu-id="f4cc5-137">Blazor WebAssembly isn't supported at this time.</span></span> <span data-ttu-id="f4cc5-138">Pour créer des applications Blazor WebAssembly sur macOS, suivez les conseils sur l’onglet **CLI Core .NET.**</span><span class="sxs-lookup"><span data-stu-id="f4cc5-138">To build Blazor WebAssembly apps on macOS, follow the guidance on the **.NET Core CLI** tab.</span></span>

1. <span data-ttu-id="f4cc5-139">Installez [Visual Studio pour Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="f4cc5-139">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="f4cc5-140">Sélectionnez **File** > **New Solution** ou créez un nouveau **projet**.</span><span class="sxs-lookup"><span data-stu-id="f4cc5-140">Select **File** > **New Solution** or create a **New Project**.</span></span>

1. <span data-ttu-id="f4cc5-141">Dans la barre latérale, sélectionnez **.NET Core** > **App**.</span><span class="sxs-lookup"><span data-stu-id="f4cc5-141">In the sidebar, select **.NET Core** > **App**.</span></span>

1. <span data-ttu-id="f4cc5-142">Sélectionnez le modèle **Blazor Server App.**</span><span class="sxs-lookup"><span data-stu-id="f4cc5-142">Select the **Blazor Server App** template.</span></span> <span data-ttu-id="f4cc5-143">Sélectionnez **Create** (Créer).</span><span class="sxs-lookup"><span data-stu-id="f4cc5-143">Select **Create**.</span></span>

   <span data-ttu-id="f4cc5-144">Pour plus d’informations sur le <xref:blazor/hosting-models>modèle d’hébergement Blazor Server, voir .</span><span class="sxs-lookup"><span data-stu-id="f4cc5-144">For information on the Blazor Server hosting model, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="f4cc5-145">Définissez le **cadre cible** à **.NET Core 3.1** et **sélectionnez Next**.</span><span class="sxs-lookup"><span data-stu-id="f4cc5-145">Set the **Target Framework** to **.NET Core 3.1** and select **Next**.</span></span>

1. <span data-ttu-id="f4cc5-146">Dans le domaine du nom `WebApplication1`de **projet,** nommez l’application .</span><span class="sxs-lookup"><span data-stu-id="f4cc5-146">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="f4cc5-147">Sélectionnez **Create** (Créer).</span><span class="sxs-lookup"><span data-stu-id="f4cc5-147">Select **Create**.</span></span>

1. <span data-ttu-id="f4cc5-148">Sélectionnez **Run** > **Run Without Debugging** pour exécuter l’application sans le *débbugger*.</span><span class="sxs-lookup"><span data-stu-id="f4cc5-148">Select **Run** > **Run Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="f4cc5-149">Exécutez l’application avec **Start Debugging** pour exécuter l’application *avec le débogage*.</span><span class="sxs-lookup"><span data-stu-id="f4cc5-149">Run the app with **Start Debugging** to run the app *with the debugger*.</span></span>

<span data-ttu-id="f4cc5-150">Si une invite semble faire confiance au certificat de développement, faites confiance au certificat et continuez.</span><span class="sxs-lookup"><span data-stu-id="f4cc5-150">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="f4cc5-151">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="f4cc5-151">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="f4cc5-152">Installer le [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="f4cc5-152">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="f4cc5-153">Installez le modèle [d’aperçu WebAssembly De Blazor](xref:blazor/hosting-models#blazor-webassembly) en exécutant la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="f4cc5-153">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) preview template by running the following command:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview3.20168.3
   ```

   > [!NOTE]
   > <span data-ttu-id="f4cc5-154">La [version SDK Core .NET 3.1.201 ou plus tard](https://dotnet.microsoft.com/download/dotnet-core/3.1) est **nécessaire** pour utiliser le modèle WebAssembly 3.2 Preview 3 Blazor.</span><span class="sxs-lookup"><span data-stu-id="f4cc5-154">The [.NET Core SDK version 3.1.201 or later](https://dotnet.microsoft.com/download/dotnet-core/3.1) is **required** to use the 3.2 Preview 3 Blazor WebAssembly template.</span></span> <span data-ttu-id="f4cc5-155">Confirmez la version SDK core `dotnet --version` .NET installée en exécutant dans une coque de commande.</span><span class="sxs-lookup"><span data-stu-id="f4cc5-155">Confirm the installed .NET Core SDK version by running `dotnet --version` in a command shell.</span></span>

1. <span data-ttu-id="f4cc5-156">Pour une expérience Blazor Server, exécutez les commandes suivantes dans une coque de commande :</span><span class="sxs-lookup"><span data-stu-id="f4cc5-156">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="f4cc5-157">Pour une expérience Blazor WebAssembly, exécutez les commandes suivantes dans une coque de commande :</span><span class="sxs-lookup"><span data-stu-id="f4cc5-157">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="f4cc5-158">Pour plus d’informations sur les deux modèles d’hébergement Blazor, <xref:blazor/hosting-models> *Blazor Server* et *Blazor WebAssembly*, voir .</span><span class="sxs-lookup"><span data-stu-id="f4cc5-158">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="f4cc5-159">Dans un navigateur, accédez à `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="f4cc5-159">In a browser, navigate to `https://localhost:5001`.</span></span>

---

<span data-ttu-id="f4cc5-160">Plusieurs pages sont disponibles à partir des onglets dans la barre latérale:</span><span class="sxs-lookup"><span data-stu-id="f4cc5-160">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="f4cc5-161">Accueil</span><span class="sxs-lookup"><span data-stu-id="f4cc5-161">Home</span></span>
* <span data-ttu-id="f4cc5-162">Counter</span><span class="sxs-lookup"><span data-stu-id="f4cc5-162">Counter</span></span>
* <span data-ttu-id="f4cc5-163">Aller chercher des données</span><span class="sxs-lookup"><span data-stu-id="f4cc5-163">Fetch data</span></span>

<span data-ttu-id="f4cc5-164">Sur la page Counter, sélectionnez le bouton **Click me** pour incrémenter le compteur sans actualisation de la page.</span><span class="sxs-lookup"><span data-stu-id="f4cc5-164">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="f4cc5-165">Incrémenter un compteur dans une page Web nécessite Blazor normalement l’écriture JavaScript, mais avec vous pouvez utiliser C .</span><span class="sxs-lookup"><span data-stu-id="f4cc5-165">Incrementing a counter in a webpage normally requires writing JavaScript, but with Blazor you can use C#.</span></span>

<span data-ttu-id="f4cc5-166">*Pages/Counter.razor* :</span><span class="sxs-lookup"><span data-stu-id="f4cc5-166">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="f4cc5-167">Une demande `/counter` dans le navigateur, `@page` comme spécifié par la `Counter` directive en haut, provoque le composant à rendre son contenu.</span><span class="sxs-lookup"><span data-stu-id="f4cc5-167">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="f4cc5-168">Les composants se transforment en une représentation en mémoire de l’arbre de rendu qui peut ensuite être utilisée pour mettre à jour l’interface utilisateur d’une manière flexible et efficace.</span><span class="sxs-lookup"><span data-stu-id="f4cc5-168">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="f4cc5-169">Chaque fois que le bouton **Cliquez sur moi** est sélectionné :</span><span class="sxs-lookup"><span data-stu-id="f4cc5-169">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="f4cc5-170">L’événement `onclick` est déclenché.</span><span class="sxs-lookup"><span data-stu-id="f4cc5-170">The `onclick` event is fired.</span></span>
* <span data-ttu-id="f4cc5-171">La méthode `IncrementCount` est appelée.</span><span class="sxs-lookup"><span data-stu-id="f4cc5-171">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="f4cc5-172">Le `currentCount` est incrémenté.</span><span class="sxs-lookup"><span data-stu-id="f4cc5-172">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="f4cc5-173">Le composant est rendu à nouveau.</span><span class="sxs-lookup"><span data-stu-id="f4cc5-173">The component is rendered again.</span></span>

<span data-ttu-id="f4cc5-174">Le temps d’exécution compare le nouveau contenu au contenu précédent et applique uniquement le contenu modifié au modèle d’objet de document (DOM).</span><span class="sxs-lookup"><span data-stu-id="f4cc5-174">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="f4cc5-175">Ajoutez un composant à un autre composant à l’aide de la syntaxe HTML.</span><span class="sxs-lookup"><span data-stu-id="f4cc5-175">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="f4cc5-176">Par exemple, `Counter` ajoutez le composant à la page `<Counter />` d’accueil de l’application en ajoutant un élément au `Index` composant.</span><span class="sxs-lookup"><span data-stu-id="f4cc5-176">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="f4cc5-177">*Pages/Index.razor* :</span><span class="sxs-lookup"><span data-stu-id="f4cc5-177">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="f4cc5-178">Exécutez l'application.</span><span class="sxs-lookup"><span data-stu-id="f4cc5-178">Run the app.</span></span> <span data-ttu-id="f4cc5-179">La page d’accueil a `Counter` son propre compteur fourni par le composant.</span><span class="sxs-lookup"><span data-stu-id="f4cc5-179">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="f4cc5-180">Les paramètres des composants sont spécifiés à l’aide d’attributs ou [de contenu pour enfants,](xref:blazor/components#child-content)qui vous permettent de définir des propriétés sur la composante enfant.</span><span class="sxs-lookup"><span data-stu-id="f4cc5-180">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="f4cc5-181">Pour ajouter un `Counter` paramètre au composant, `@code` mettez à jour le bloc du composant :</span><span class="sxs-lookup"><span data-stu-id="f4cc5-181">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="f4cc5-182">Ajoutez une propriété `IncrementAmount` publique `[Parameter]` pour un attribut.</span><span class="sxs-lookup"><span data-stu-id="f4cc5-182">Add a public property for `IncrementAmount` with a `[Parameter]` attribute.</span></span>
* <span data-ttu-id="f4cc5-183">Modifiez la méthode `IncrementCount` pour utiliser `IncrementAmount` lorsque vous augmentez la valeur de `currentCount`.</span><span class="sxs-lookup"><span data-stu-id="f4cc5-183">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="f4cc5-184">*Pages/Counter.razor* :</span><span class="sxs-lookup"><span data-stu-id="f4cc5-184">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="f4cc5-185">Spécifier l’élément `IncrementAmount` de l’élément de `Index` `<Counter>` l’élément à l’aide d’un attribut.</span><span class="sxs-lookup"><span data-stu-id="f4cc5-185">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="f4cc5-186">*Pages/Index.razor* :</span><span class="sxs-lookup"><span data-stu-id="f4cc5-186">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="f4cc5-187">Exécutez l'application.</span><span class="sxs-lookup"><span data-stu-id="f4cc5-187">Run the app.</span></span> <span data-ttu-id="f4cc5-188">Le `Index` composant a son propre compteur qui incréments par dix chaque fois que le bouton **Cliquez sur moi** est sélectionné.</span><span class="sxs-lookup"><span data-stu-id="f4cc5-188">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="f4cc5-189">Le `Counter` composant (*Counter.razor*) à `/counter` continue d’incrément par un.</span><span class="sxs-lookup"><span data-stu-id="f4cc5-189">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="f4cc5-190">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="f4cc5-190">Next steps</span></span>

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="f4cc5-191">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="f4cc5-191">Additional resources</span></span>

* <xref:blazor/templates>
* <xref:signalr/introduction>
