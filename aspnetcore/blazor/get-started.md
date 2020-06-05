---
title: Prise en main de ASP.NET CoreBlazor
author: guardrex
description: Commencez Blazor par créer une Blazor application avec les outils de votre choix.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/31/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/get-started
ms.openlocfilehash: 08229283882928c4cc733de19840d25872846c97
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/05/2020
ms.locfileid: "84452029"
---
# <a name="get-started-with-aspnet-core-blazor"></a><span data-ttu-id="1cd3f-103">Prise en main de ASP.NET CoreBlazor</span><span class="sxs-lookup"><span data-stu-id="1cd3f-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="1cd3f-104">Par [Daniel Roth](https://github.com/danroth27) et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="1cd3f-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="1cd3f-105">Pour commencer à utiliser Blazor , suivez les instructions de votre choix d’outils :</span><span class="sxs-lookup"><span data-stu-id="1cd3f-105">To get started with Blazor, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1cd3f-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1cd3f-106">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="1cd3f-107">Installez la dernière version de [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) avec la charge de travail **développement Web et ASP.net** .</span><span class="sxs-lookup"><span data-stu-id="1cd3f-107">Install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

1. <span data-ttu-id="1cd3f-108">Créez un projet.</span><span class="sxs-lookup"><span data-stu-id="1cd3f-108">Create a new project.</span></span>

1. <span data-ttu-id="1cd3f-109">Sélectionnez \*\* Blazor application\*\*.</span><span class="sxs-lookup"><span data-stu-id="1cd3f-109">Select **Blazor App**.</span></span> <span data-ttu-id="1cd3f-110">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="1cd3f-110">Select **Next**.</span></span>

1. <span data-ttu-id="1cd3f-111">Indiquez un nom de projet dans le champ **Nom du projet**, ou acceptez le nom de projet par défaut.</span><span class="sxs-lookup"><span data-stu-id="1cd3f-111">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="1cd3f-112">Confirmez que l’entrée d' **emplacement** est correcte ou indiquez un emplacement pour le projet.</span><span class="sxs-lookup"><span data-stu-id="1cd3f-112">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="1cd3f-113">Sélectionnez **Create** (Créer).</span><span class="sxs-lookup"><span data-stu-id="1cd3f-113">Select **Create**.</span></span>

1. <span data-ttu-id="1cd3f-114">Pour une Blazor expérience Webassembly, choisissez le modèle \*\* Blazor application webassembly\*\* .</span><span class="sxs-lookup"><span data-stu-id="1cd3f-114">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="1cd3f-115">Pour une Blazor expérience serveur, choisissez le modèle \*\* Blazor application serveur\*\* .</span><span class="sxs-lookup"><span data-stu-id="1cd3f-115">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="1cd3f-116">Sélectionnez **Create** (Créer).</span><span class="sxs-lookup"><span data-stu-id="1cd3f-116">Select **Create**.</span></span>

   <span data-ttu-id="1cd3f-117">Pour plus d’informations sur les deux Blazor modèles d’hébergement, \* Blazor webassembly\* et \* Blazor Server\*, consultez <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="1cd3f-117">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="1cd3f-118">Appuyez sur <kbd>CTRL</kbd> + <kbd>F5</kbd> pour exécuter l’application.</span><span class="sxs-lookup"><span data-stu-id="1cd3f-118">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="1cd3f-119">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1cd3f-119">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="1cd3f-120">Installez la dernière version du [Kit de développement logiciel (SDK) .net Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="1cd3f-120">Install the latest version of the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span> <span data-ttu-id="1cd3f-121">Si vous avez déjà installé le kit de développement logiciel (SDK), vous pouvez déterminer la version installée en exécutant la commande suivante dans une interface de commande :</span><span class="sxs-lookup"><span data-stu-id="1cd3f-121">If you previously installed the SDK, you can determine your installed version by executing the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet --version
   ```

1. <span data-ttu-id="1cd3f-122">Installez la dernière version de [Visual Studio code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="1cd3f-122">Install the latest version of [Visual Studio Code](https://code.visualstudio.com/).</span></span>

1. <span data-ttu-id="1cd3f-123">Installez la dernière [extension C# for Visual Studio code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) et l’extension de [débogueur JavaScript (nocturne)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) avec la `debug.javascript.usePreview` valeur `true` .</span><span class="sxs-lookup"><span data-stu-id="1cd3f-123">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

  <span data-ttu-id="1cd3f-124">Pour définir `debug.javascript.usePreview` à `true` l’aide de l’interface utilisateur vs code, ouvrez paramètres des préférences de **fichier**  >  **Preferences**  >  **Settings** et recherchez `debug javascript use preview` .</span><span class="sxs-lookup"><span data-stu-id="1cd3f-124">To set `debug.javascript.usePreview` to `true` using the VS Code UI, open **File** > **Preferences** > **Settings** and search for `debug javascript use preview`.</span></span> <span data-ttu-id="1cd3f-125">Activez la case à cocher **utiliser le nouveau débogueur JavaScript en préversion pour node. js et chrome**.</span><span class="sxs-lookup"><span data-stu-id="1cd3f-125">Select the check box for **Use the new in-preview JavaScript debugger for Node.js and Chrome**.</span></span>

1. <span data-ttu-id="1cd3f-126">Pour une Blazor expérience Webassembly, exécutez la commande suivante dans une interface de commande :</span><span class="sxs-lookup"><span data-stu-id="1cd3f-126">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   <span data-ttu-id="1cd3f-127">Pour une Blazor expérience serveur, exécutez la commande suivante dans une interface de commande :</span><span class="sxs-lookup"><span data-stu-id="1cd3f-127">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="1cd3f-128">Pour plus d’informations sur les deux Blazor modèles d’hébergement, \* Blazor webassembly\* et \* Blazor Server\*, consultez <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="1cd3f-128">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="1cd3f-129">Ouvrez le dossier *WebApplication1* dans Visual Studio code.</span><span class="sxs-lookup"><span data-stu-id="1cd3f-129">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="1cd3f-130">L’IDE demande que vous ajoutiez des ressources pour générer et déboguer le projet.</span><span class="sxs-lookup"><span data-stu-id="1cd3f-130">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="1cd3f-131">Sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="1cd3f-131">Select **Yes**.</span></span>

1. <span data-ttu-id="1cd3f-132">Appuyez sur <kbd>CTRL</kbd> + <kbd>F5</kbd> pour exécuter l’application.</span><span class="sxs-lookup"><span data-stu-id="1cd3f-132">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1cd3f-133">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="1cd3f-133">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="1cd3f-134">Installez [Visual Studio pour Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="1cd3f-134">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="1cd3f-135">Sélectionnez **fichier**  >  **nouvelle solution** ou créer un **nouveau** projet à partir de la **fenêtre démarrer**.</span><span class="sxs-lookup"><span data-stu-id="1cd3f-135">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="1cd3f-136">Dans la barre latérale, sélectionnez application **Web et console**  >  **App**.</span><span class="sxs-lookup"><span data-stu-id="1cd3f-136">In the sidebar, select **Web and Console** > **App**.</span></span>

   <span data-ttu-id="1cd3f-137">Pour une Blazor expérience Webassembly, choisissez le modèle \*\* Blazor application webassembly\*\* .</span><span class="sxs-lookup"><span data-stu-id="1cd3f-137">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="1cd3f-138">Pour une Blazor expérience serveur, choisissez le modèle \*\* Blazor application serveur\*\* .</span><span class="sxs-lookup"><span data-stu-id="1cd3f-138">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="1cd3f-139">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="1cd3f-139">Select **Next**.</span></span>

   <span data-ttu-id="1cd3f-140">Pour plus d’informations sur les deux Blazor modèles d’hébergement, \* Blazor webassembly\* et \* Blazor Server\*, consultez <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="1cd3f-140">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="1cd3f-141">Vérifiez les configurations suivantes :</span><span class="sxs-lookup"><span data-stu-id="1cd3f-141">Confirm the following configurations:</span></span>

   * <span data-ttu-id="1cd3f-142">**Framework cible** défini sur **.net Core 3,1**.</span><span class="sxs-lookup"><span data-stu-id="1cd3f-142">**Target Framework** set to **.NET Core 3.1**.</span></span>
   * <span data-ttu-id="1cd3f-143">**L’authentification** est définie sur **aucune authentification**.</span><span class="sxs-lookup"><span data-stu-id="1cd3f-143">**Authentication** set to **No Authentication**.</span></span>
   
   <span data-ttu-id="1cd3f-144">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="1cd3f-144">Select **Next**.</span></span>

1. <span data-ttu-id="1cd3f-145">Dans le champ **nom du projet** , nommez l’application `WebApplication1` .</span><span class="sxs-lookup"><span data-stu-id="1cd3f-145">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="1cd3f-146">Sélectionnez **Create** (Créer).</span><span class="sxs-lookup"><span data-stu-id="1cd3f-146">Select **Create**.</span></span>

1. <span data-ttu-id="1cd3f-147">Sélectionnez **exécuter**  >  **Démarrer sans débogage** pour exécuter l’application *sans le débogueur*.</span><span class="sxs-lookup"><span data-stu-id="1cd3f-147">Select **Run** > **Start Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="1cd3f-148">Exécutez l’application avec **Run**  >  le bouton exécuter**Démarrer le débogage** ou exécuter (&#9654;) pour exécuter l’application *avec le débogueur*.</span><span class="sxs-lookup"><span data-stu-id="1cd3f-148">Run the app with **Run** > **Start Debugging** or the Run (&#9654;) button to run the app *with the debugger*.</span></span>

<span data-ttu-id="1cd3f-149">Si une invite s’affiche pour faire confiance au certificat de développement, approuvez le certificat et continuez.</span><span class="sxs-lookup"><span data-stu-id="1cd3f-149">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="1cd3f-150">Les mots de passe utilisateur et trousseau sont requis pour approuver le certificat.</span><span class="sxs-lookup"><span data-stu-id="1cd3f-150">The user and keychain passwords are required to trust the certificate.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="1cd3f-151">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="1cd3f-151">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="1cd3f-152">Installez la dernière version du [Kit de développement logiciel (SDK) .net Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="1cd3f-152">Install the latest version of the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span> <span data-ttu-id="1cd3f-153">Si vous avez déjà installé le kit de développement logiciel (SDK), vous pouvez déterminer la version installée en exécutant la commande suivante dans une interface de commande :</span><span class="sxs-lookup"><span data-stu-id="1cd3f-153">If you previously installed the SDK, you can determine your installed version by executing the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet --version
   ```

1. <span data-ttu-id="1cd3f-154">Pour une Blazor expérience Webassembly, exécutez les commandes suivantes dans un interpréteur de commandes :</span><span class="sxs-lookup"><span data-stu-id="1cd3f-154">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="1cd3f-155">Pour une Blazor expérience serveur, exécutez les commandes suivantes dans un interpréteur de commandes :</span><span class="sxs-lookup"><span data-stu-id="1cd3f-155">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="1cd3f-156">Pour plus d’informations sur les deux Blazor modèles d’hébergement, \* Blazor webassembly\* et \* Blazor Server\*, consultez <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="1cd3f-156">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="1cd3f-157">Dans un navigateur, accédez à `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="1cd3f-157">In a browser, navigate to `https://localhost:5001`.</span></span>

---

<span data-ttu-id="1cd3f-158">Plusieurs pages sont disponibles à partir des onglets de la barre latérale :</span><span class="sxs-lookup"><span data-stu-id="1cd3f-158">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="1cd3f-159">Accueil</span><span class="sxs-lookup"><span data-stu-id="1cd3f-159">Home</span></span>
* <span data-ttu-id="1cd3f-160">Compteur</span><span class="sxs-lookup"><span data-stu-id="1cd3f-160">Counter</span></span>
* <span data-ttu-id="1cd3f-161">Extraire les données</span><span class="sxs-lookup"><span data-stu-id="1cd3f-161">Fetch data</span></span>

<span data-ttu-id="1cd3f-162">Sur la page Counter, sélectionnez le bouton **Click me** pour incrémenter le compteur sans actualisation de la page.</span><span class="sxs-lookup"><span data-stu-id="1cd3f-162">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="1cd3f-163">L’incrémentation d’un compteur dans une page Web nécessite normalement l’écriture de JavaScript, mais avec Blazor vous pouvez utiliser C#.</span><span class="sxs-lookup"><span data-stu-id="1cd3f-163">Incrementing a counter in a webpage normally requires writing JavaScript, but with Blazor you can use C#.</span></span>

<span data-ttu-id="1cd3f-164">*Pages/Counter.razor* :</span><span class="sxs-lookup"><span data-stu-id="1cd3f-164">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="1cd3f-165">Une demande de `/counter` dans le navigateur, comme spécifié par la `@page` directive en haut, fait en sorte que le `Counter` composant restitue son contenu.</span><span class="sxs-lookup"><span data-stu-id="1cd3f-165">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="1cd3f-166">Les composants sont rendus dans une représentation en mémoire de l’arborescence de rendu qui peut ensuite être utilisée pour mettre à jour l’interface utilisateur de manière flexible et efficace.</span><span class="sxs-lookup"><span data-stu-id="1cd3f-166">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="1cd3f-167">Chaque fois que le bouton **Click Me** est sélectionné :</span><span class="sxs-lookup"><span data-stu-id="1cd3f-167">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="1cd3f-168">L' `onclick` événement est déclenché.</span><span class="sxs-lookup"><span data-stu-id="1cd3f-168">The `onclick` event is fired.</span></span>
* <span data-ttu-id="1cd3f-169">La méthode `IncrementCount` est appelée.</span><span class="sxs-lookup"><span data-stu-id="1cd3f-169">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="1cd3f-170">`currentCount`Est incrémenté.</span><span class="sxs-lookup"><span data-stu-id="1cd3f-170">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="1cd3f-171">Le composant est de nouveau restitué.</span><span class="sxs-lookup"><span data-stu-id="1cd3f-171">The component is rendered again.</span></span>

<span data-ttu-id="1cd3f-172">Le runtime compare le nouveau contenu au contenu précédent et applique uniquement le contenu modifié à l’Document Object Model (DOM).</span><span class="sxs-lookup"><span data-stu-id="1cd3f-172">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="1cd3f-173">Ajoutez un composant à un autre composant à l’aide de la syntaxe HTML.</span><span class="sxs-lookup"><span data-stu-id="1cd3f-173">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="1cd3f-174">Par exemple, ajoutez le `Counter` composant à la page d’accueil de l’application en ajoutant un `<Counter />` élément au `Index` composant.</span><span class="sxs-lookup"><span data-stu-id="1cd3f-174">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="1cd3f-175">*Pages/Index.razor* :</span><span class="sxs-lookup"><span data-stu-id="1cd3f-175">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="1cd3f-176">Exécutez l'application.</span><span class="sxs-lookup"><span data-stu-id="1cd3f-176">Run the app.</span></span> <span data-ttu-id="1cd3f-177">La page d’accueil possède son propre compteur fourni par le `Counter` composant.</span><span class="sxs-lookup"><span data-stu-id="1cd3f-177">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="1cd3f-178">Les paramètres de composant sont spécifiés à l’aide d’attributs ou de [contenu enfant](xref:blazor/components#child-content), ce qui vous permet de définir des propriétés sur le composant enfant.</span><span class="sxs-lookup"><span data-stu-id="1cd3f-178">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="1cd3f-179">Pour ajouter un paramètre au `Counter` composant, mettez à jour le bloc du composant `@code` :</span><span class="sxs-lookup"><span data-stu-id="1cd3f-179">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="1cd3f-180">Ajoutez une propriété publique pour `IncrementAmount` avec un [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribut.</span><span class="sxs-lookup"><span data-stu-id="1cd3f-180">Add a public property for `IncrementAmount` with a [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribute.</span></span>
* <span data-ttu-id="1cd3f-181">Modifiez la méthode `IncrementCount` pour utiliser `IncrementAmount` lorsque vous augmentez la valeur de `currentCount`.</span><span class="sxs-lookup"><span data-stu-id="1cd3f-181">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="1cd3f-182">*Pages/Counter.razor* :</span><span class="sxs-lookup"><span data-stu-id="1cd3f-182">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="1cd3f-183">Spécifiez `IncrementAmount` dans l' `Index` élément du composant `<Counter>` à l’aide d’un attribut.</span><span class="sxs-lookup"><span data-stu-id="1cd3f-183">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="1cd3f-184">*Pages/Index.razor* :</span><span class="sxs-lookup"><span data-stu-id="1cd3f-184">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="1cd3f-185">Exécutez l'application.</span><span class="sxs-lookup"><span data-stu-id="1cd3f-185">Run the app.</span></span> <span data-ttu-id="1cd3f-186">Le `Index` composant a son propre compteur qui est incrémenté de dix chaque fois que le bouton **Click Me** est sélectionné.</span><span class="sxs-lookup"><span data-stu-id="1cd3f-186">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="1cd3f-187">Le `Counter` composant (*Counter. Razor*) de `/counter` continue à être incrémenté d’une unité.</span><span class="sxs-lookup"><span data-stu-id="1cd3f-187">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="1cd3f-188">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="1cd3f-188">Next steps</span></span>

<span data-ttu-id="1cd3f-189">Créer une Blazor application pas à pas :</span><span class="sxs-lookup"><span data-stu-id="1cd3f-189">Build a Blazor app step-by-step:</span></span>

> [!div class="nextstepaction"]
> <xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="1cd3f-190">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="1cd3f-190">Additional resources</span></span>

* <xref:blazor/templates>
* <xref:signalr/introduction>
