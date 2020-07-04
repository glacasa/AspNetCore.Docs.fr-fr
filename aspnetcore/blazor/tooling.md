---
title: Outils pour ASP.NET CoreBlazor
author: guardrex
description: En savoir plus sur les outils disponibles pour créer des Blazor applications.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/02/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/tooling
zone_pivot_groups: operating-systems-set-one
ms.openlocfilehash: 538257597ec5c6c705d8280a817c409debe22224
ms.sourcegitcommit: c6467f86c09b1f608b09d37d33c8c43de7ae8bc7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/03/2020
ms.locfileid: "85946818"
---
# <a name="tooling-for-aspnet-core-blazor"></a><span data-ttu-id="0131d-103">Outils pour ASP.NET CoreBlazor</span><span class="sxs-lookup"><span data-stu-id="0131d-103">Tooling for ASP.NET Core Blazor</span></span>

<span data-ttu-id="0131d-104">Par [Daniel Roth](https://github.com/danroth27) et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="0131d-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

::: zone pivot="os-windows"

1. <span data-ttu-id="0131d-105">Installez la dernière version de [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) avec la charge de travail **développement Web et ASP.net** .</span><span class="sxs-lookup"><span data-stu-id="0131d-105">Install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

1. <span data-ttu-id="0131d-106">Créez un projet.</span><span class="sxs-lookup"><span data-stu-id="0131d-106">Create a new project.</span></span>

1. <span data-ttu-id="0131d-107">Sélectionnez \*\* Blazor application\*\*.</span><span class="sxs-lookup"><span data-stu-id="0131d-107">Select **Blazor App**.</span></span> <span data-ttu-id="0131d-108">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="0131d-108">Select **Next**.</span></span>

1. <span data-ttu-id="0131d-109">Indiquez un nom de projet dans le champ **Nom du projet**, ou acceptez le nom de projet par défaut.</span><span class="sxs-lookup"><span data-stu-id="0131d-109">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="0131d-110">Confirmez que l’entrée d' **emplacement** est correcte ou indiquez un emplacement pour le projet.</span><span class="sxs-lookup"><span data-stu-id="0131d-110">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="0131d-111">Sélectionnez **Créer**.</span><span class="sxs-lookup"><span data-stu-id="0131d-111">Select **Create**.</span></span>

1. <span data-ttu-id="0131d-112">Pour une Blazor WebAssembly expérience, choisissez le modèle d' \*\* Blazor WebAssembly application\*\* .</span><span class="sxs-lookup"><span data-stu-id="0131d-112">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="0131d-113">Pour une Blazor Server expérience, choisissez le modèle d' \*\* Blazor Server application\*\* .</span><span class="sxs-lookup"><span data-stu-id="0131d-113">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="0131d-114">Sélectionnez **Créer**.</span><span class="sxs-lookup"><span data-stu-id="0131d-114">Select **Create**.</span></span>

   <span data-ttu-id="0131d-115">Pour plus d’informations sur les deux Blazor modèles d’hébergement, *Blazor WebAssembly* et *Blazor Server* , consultez <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="0131d-115">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="0131d-116">Appuyez sur <kbd>CTRL</kbd> + <kbd>F5</kbd> pour exécuter l’application.</span><span class="sxs-lookup"><span data-stu-id="0131d-116">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

::: zone-end

::: zone pivot="os-linux"

1. <span data-ttu-id="0131d-117">Installez la dernière version du [Kit de développement logiciel (SDK) .net Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="0131d-117">Install the latest version of the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span> <span data-ttu-id="0131d-118">Si vous avez déjà installé le kit de développement logiciel (SDK), vous pouvez déterminer la version installée en exécutant la commande suivante dans une interface de commande :</span><span class="sxs-lookup"><span data-stu-id="0131d-118">If you previously installed the SDK, you can determine your installed version by executing the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet --version
   ```

1. <span data-ttu-id="0131d-119">Installez la dernière version de [Visual Studio code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="0131d-119">Install the latest version of [Visual Studio Code](https://code.visualstudio.com/).</span></span>

1. <span data-ttu-id="0131d-120">Installez la dernière [extension C# for Visual Studio code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) et l’extension de [débogueur JavaScript (nocturne)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) avec la `debug.javascript.usePreview` valeur `true` .</span><span class="sxs-lookup"><span data-stu-id="0131d-120">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

   <span data-ttu-id="0131d-121">Pour définir `debug.javascript.usePreview` à `true` l’aide de l’interface utilisateur vs code, ouvrez paramètres des préférences de **fichier**  >  **Preferences**  >  **Settings** et recherchez `debug javascript use preview` .</span><span class="sxs-lookup"><span data-stu-id="0131d-121">To set `debug.javascript.usePreview` to `true` using the VS Code UI, open **File** > **Preferences** > **Settings** and search for `debug javascript use preview`.</span></span> <span data-ttu-id="0131d-122">Activez la case à cocher **utiliser le nouveau débogueur JavaScript en préversion pour Node.js et chrome**.</span><span class="sxs-lookup"><span data-stu-id="0131d-122">Select the check box for **Use the new in-preview JavaScript debugger for Node.js and Chrome**.</span></span>

1. <span data-ttu-id="0131d-123">Pour une Blazor WebAssembly expérience, exécutez la commande suivante dans une interface de commande :</span><span class="sxs-lookup"><span data-stu-id="0131d-123">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   <span data-ttu-id="0131d-124">Pour une Blazor Server expérience, exécutez la commande suivante dans une interface de commande :</span><span class="sxs-lookup"><span data-stu-id="0131d-124">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="0131d-125">Pour plus d’informations sur les deux Blazor modèles d’hébergement, *Blazor WebAssembly* et *Blazor Server* , consultez <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="0131d-125">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="0131d-126">Ouvrez le dossier `WebApplication1` dans Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="0131d-126">Open the `WebApplication1` folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="0131d-127">L’IDE demande que vous ajoutiez des ressources pour générer et déboguer le projet.</span><span class="sxs-lookup"><span data-stu-id="0131d-127">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="0131d-128">Sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="0131d-128">Select **Yes**.</span></span>

1. <span data-ttu-id="0131d-129">Appuyez sur <kbd>CTRL</kbd> + <kbd>F5</kbd> pour exécuter l’application.</span><span class="sxs-lookup"><span data-stu-id="0131d-129">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

::: zone-end

::: zone pivot="os-macos"

1. <span data-ttu-id="0131d-130">Installez [Visual Studio pour Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="0131d-130">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="0131d-131">Sélectionnez **fichier**  >  **nouvelle solution** ou créer un **nouveau** projet à partir de la **fenêtre démarrer**.</span><span class="sxs-lookup"><span data-stu-id="0131d-131">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="0131d-132">Dans la barre latérale, sélectionnez application **Web et console**  >  **App**.</span><span class="sxs-lookup"><span data-stu-id="0131d-132">In the sidebar, select **Web and Console** > **App**.</span></span>

   <span data-ttu-id="0131d-133">Pour une Blazor WebAssembly expérience, choisissez le modèle d' \*\* Blazor WebAssembly application\*\* .</span><span class="sxs-lookup"><span data-stu-id="0131d-133">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="0131d-134">Pour une Blazor Server expérience, choisissez le modèle d' \*\* Blazor Server application\*\* .</span><span class="sxs-lookup"><span data-stu-id="0131d-134">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="0131d-135">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="0131d-135">Select **Next**.</span></span>

   <span data-ttu-id="0131d-136">Pour plus d’informations sur les deux Blazor modèles d’hébergement, *Blazor WebAssembly* et *Blazor Server* , consultez <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="0131d-136">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="0131d-137">Vérifiez les configurations suivantes :</span><span class="sxs-lookup"><span data-stu-id="0131d-137">Confirm the following configurations:</span></span>

   * <span data-ttu-id="0131d-138">**Framework cible** défini sur **.net Core 3,1**.</span><span class="sxs-lookup"><span data-stu-id="0131d-138">**Target Framework** set to **.NET Core 3.1**.</span></span>
   * <span data-ttu-id="0131d-139">**L’authentification** est définie sur **aucune authentification**.</span><span class="sxs-lookup"><span data-stu-id="0131d-139">**Authentication** set to **No Authentication**.</span></span>
   
   <span data-ttu-id="0131d-140">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="0131d-140">Select **Next**.</span></span>

1. <span data-ttu-id="0131d-141">Dans le champ **nom du projet** , nommez l’application `WebApplication1` .</span><span class="sxs-lookup"><span data-stu-id="0131d-141">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="0131d-142">Sélectionnez **Créer**.</span><span class="sxs-lookup"><span data-stu-id="0131d-142">Select **Create**.</span></span>

1. <span data-ttu-id="0131d-143">Sélectionnez **exécuter**  >  **Démarrer sans débogage** pour exécuter l’application *sans le débogueur*.</span><span class="sxs-lookup"><span data-stu-id="0131d-143">Select **Run** > **Start Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="0131d-144">Exécutez l’application avec **Run**  >  le bouton exécuter**Démarrer le débogage** ou exécuter (&#9654;) pour exécuter l’application *avec le débogueur*.</span><span class="sxs-lookup"><span data-stu-id="0131d-144">Run the app with **Run** > **Start Debugging** or the Run (&#9654;) button to run the app *with the debugger*.</span></span>

<span data-ttu-id="0131d-145">Si une invite s’affiche pour faire confiance au certificat de développement, approuvez le certificat et continuez.</span><span class="sxs-lookup"><span data-stu-id="0131d-145">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="0131d-146">Les mots de passe utilisateur et trousseau sont requis pour approuver le certificat.</span><span class="sxs-lookup"><span data-stu-id="0131d-146">The user and keychain passwords are required to trust the certificate.</span></span>

::: zone-end
