---
title: ASP.NET Core de débogageBlazor WebAssembly
author: guardrex
description: Découvrez comment déboguer des Blazor applications.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/30/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/debug
ms.openlocfilehash: 225916411550cc8e89c604e1426316843bb0ff52
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88014540"
---
# <a name="debug-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="07d2f-103">ASP.NET Core de débogageBlazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="07d2f-103">Debug ASP.NET Core Blazor WebAssembly</span></span>

[<span data-ttu-id="07d2f-104">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="07d2f-104">Daniel Roth</span></span>](https://github.com/danroth27)

<span data-ttu-id="07d2f-105">Blazor WebAssemblyles applications peuvent être déboguées à l’aide des outils de développement de navigateur dans les navigateurs basés sur le chrome (Edge/chrome).</span><span class="sxs-lookup"><span data-stu-id="07d2f-105">Blazor WebAssembly apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span> <span data-ttu-id="07d2f-106">Vous pouvez également déboguer votre application à l’aide de Visual Studio ou Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="07d2f-106">Alternatively, you can debug your app using Visual Studio or Visual Studio Code.</span></span>

<span data-ttu-id="07d2f-107">Les scénarios disponibles sont les suivants :</span><span class="sxs-lookup"><span data-stu-id="07d2f-107">Available scenarios include:</span></span>

* <span data-ttu-id="07d2f-108">Définir et supprimer des points d’arrêt.</span><span class="sxs-lookup"><span data-stu-id="07d2f-108">Set and remove breakpoints.</span></span>
* <span data-ttu-id="07d2f-109">Exécutez l’application avec prise en charge du débogage dans Visual Studio et Visual Studio Code (prise en charge de<kbd>F5</kbd> ).</span><span class="sxs-lookup"><span data-stu-id="07d2f-109">Run the app with debugging support in Visual Studio and Visual Studio Code (<kbd>F5</kbd> support).</span></span>
* <span data-ttu-id="07d2f-110">Une étape (<kbd>F10</kbd>) à l’aide du code.</span><span class="sxs-lookup"><span data-stu-id="07d2f-110">Single-step (<kbd>F10</kbd>) through the code.</span></span>
* <span data-ttu-id="07d2f-111">Reprendre l’exécution du code avec <kbd>F8</kbd> dans un navigateur ou <kbd>F5</kbd> dans Visual Studio ou Visual Studio code.</span><span class="sxs-lookup"><span data-stu-id="07d2f-111">Resume code execution with <kbd>F8</kbd> in a browser or <kbd>F5</kbd> in Visual Studio or Visual Studio Code.</span></span>
* <span data-ttu-id="07d2f-112">Dans l' *affichage des variables locales,* observez les valeurs des variables locales.</span><span class="sxs-lookup"><span data-stu-id="07d2f-112">In the *Locals* display, observe the values of local variables.</span></span>
* <span data-ttu-id="07d2f-113">Consultez la pile des appels, y compris les chaînes d’appel qui passent de JavaScript à .NET et de .NET à JavaScript.</span><span class="sxs-lookup"><span data-stu-id="07d2f-113">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="07d2f-114">Pour le moment, vous *ne pouvez pas*:</span><span class="sxs-lookup"><span data-stu-id="07d2f-114">For now, you *can't*:</span></span>

* <span data-ttu-id="07d2f-115">Arrêt sur les exceptions non gérées.</span><span class="sxs-lookup"><span data-stu-id="07d2f-115">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="07d2f-116">Atteindre les points d’arrêt pendant le démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="07d2f-116">Hit breakpoints during app startup.</span></span>

<span data-ttu-id="07d2f-117">Nous continuerons à améliorer l’expérience de débogage dans les versions à venir.</span><span class="sxs-lookup"><span data-stu-id="07d2f-117">We will continue to improve the debugging experience in upcoming releases.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="07d2f-118">Prérequis</span><span class="sxs-lookup"><span data-stu-id="07d2f-118">Prerequisites</span></span>

<span data-ttu-id="07d2f-119">Le débogage requiert l’un des navigateurs suivants :</span><span class="sxs-lookup"><span data-stu-id="07d2f-119">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="07d2f-120">Google Chrome (version 70 ou ultérieure) (par défaut)</span><span class="sxs-lookup"><span data-stu-id="07d2f-120">Google Chrome (version 70 or later) (default)</span></span>
* <span data-ttu-id="07d2f-121">Microsoft Edge (version 80 ou ultérieure)</span><span class="sxs-lookup"><span data-stu-id="07d2f-121">Microsoft Edge (version 80 or later)</span></span>

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a><span data-ttu-id="07d2f-122">Activer le débogage pour Visual Studio et Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="07d2f-122">Enable debugging for Visual Studio and Visual Studio Code</span></span>

<span data-ttu-id="07d2f-123">Pour activer le débogage d’une Blazor WebAssembly application existante, mettez à jour le `launchSettings.json` fichier dans le projet de démarrage pour inclure la `inspectUri` propriété suivante dans chaque profil de lancement :</span><span class="sxs-lookup"><span data-stu-id="07d2f-123">To enable debugging for an existing Blazor WebAssembly app, update the `launchSettings.json` file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="07d2f-124">Une fois mis à jour, le `launchSettings.json` fichier doit ressembler à l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="07d2f-124">Once updated, the `launchSettings.json` file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="07d2f-125">La `inspectUri` propriété :</span><span class="sxs-lookup"><span data-stu-id="07d2f-125">The `inspectUri` property:</span></span>

* <span data-ttu-id="07d2f-126">Permet à l’IDE de détecter que l’application est une Blazor WebAssembly application.</span><span class="sxs-lookup"><span data-stu-id="07d2f-126">Enables the IDE to detect that the app is a Blazor WebAssembly app.</span></span>
* <span data-ttu-id="07d2f-127">Indique à l’infrastructure de débogage de script de se connecter au navigateur via le Blazor proxy de débogage de.</span><span class="sxs-lookup"><span data-stu-id="07d2f-127">Instructs the script debugging infrastructure to connect to the browser through Blazor's debugging proxy.</span></span>

<span data-ttu-id="07d2f-128">Les valeurs d’espace réservé pour le protocole WebSockets ( `wsProtocol` ), l’hôte ( `url.hostname` ), le port ( `url.port` ) et l’URI de l’inspecteur sur le navigateur lancé ( `browserInspectUri` ) sont fournies par l’infrastructure.</span><span class="sxs-lookup"><span data-stu-id="07d2f-128">The placeholder values for the WebSockets protocol (`wsProtocol`), host (`url.hostname`), port (`url.port`), and inspector URI on the launched browser (`browserInspectUri`) are provided by the framework.</span></span>

## <a name="visual-studio"></a><span data-ttu-id="07d2f-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="07d2f-129">Visual Studio</span></span>

<span data-ttu-id="07d2f-130">Pour déboguer une Blazor WebAssembly application dans Visual Studio :</span><span class="sxs-lookup"><span data-stu-id="07d2f-130">To debug a Blazor WebAssembly app in Visual Studio:</span></span>

1. <span data-ttu-id="07d2f-131">Créez une nouvelle ASP.NET Core application hébergée Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="07d2f-131">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="07d2f-132">Appuyez sur <kbd>F5</kbd> pour exécuter l’application dans le débogueur.</span><span class="sxs-lookup"><span data-stu-id="07d2f-132">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>

   > [!NOTE]
   > <span data-ttu-id="07d2f-133">**Exécuter sans débogage** (<kbd>CTRL</kbd> + <kbd>F5</kbd>) n’est pas pris en charge.</span><span class="sxs-lookup"><span data-stu-id="07d2f-133">**Start Without Debugging** (<kbd>Ctrl</kbd>+<kbd>F5</kbd>) isn't supported.</span></span>

1. <span data-ttu-id="07d2f-134">Définissez un point d’arrêt dans `Pages/Counter.razor` la `IncrementCount` méthode.</span><span class="sxs-lookup"><span data-stu-id="07d2f-134">Set a breakpoint in `Pages/Counter.razor` in the `IncrementCount` method.</span></span>
1. <span data-ttu-id="07d2f-135">Accédez à l' **`Counter`** onglet et sélectionnez le bouton pour atteindre le point d’arrêt :</span><span class="sxs-lookup"><span data-stu-id="07d2f-135">Browse to the **`Counter`** tab and select the button to hit the breakpoint:</span></span>

   ![Compteur de débogage](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. <span data-ttu-id="07d2f-137">Examinez la valeur du `currentCount` champ dans la fenêtre variables locales :</span><span class="sxs-lookup"><span data-stu-id="07d2f-137">Check out the value of the `currentCount` field in the locals window:</span></span>

   ![Afficher les variables locales](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. <span data-ttu-id="07d2f-139">Appuyez sur <kbd>F5</kbd> pour poursuivre l’exécution.</span><span class="sxs-lookup"><span data-stu-id="07d2f-139">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="07d2f-140">Lors du débogage de votre Blazor WebAssembly application, vous pouvez également déboguer votre code serveur :</span><span class="sxs-lookup"><span data-stu-id="07d2f-140">While debugging your Blazor WebAssembly app, you can also debug your server code:</span></span>

1. <span data-ttu-id="07d2f-141">Définissez un point d’arrêt dans la `Pages/FetchData.razor` page de <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="07d2f-141">Set a breakpoint in the `Pages/FetchData.razor` page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="07d2f-142">Définissez un point d’arrêt dans la `WeatherForecastController` `Get` méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="07d2f-142">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="07d2f-143">Accédez à l' **`Fetch Data`** onglet pour atteindre le premier point d’arrêt dans le `FetchData` composant juste avant qu’il envoie une requête HTTP au serveur :</span><span class="sxs-lookup"><span data-stu-id="07d2f-143">Browse to the **`Fetch Data`** tab to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server:</span></span>

   ![Déboguer extraire des données](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. <span data-ttu-id="07d2f-145">Appuyez sur <kbd>F5</kbd> pour poursuivre l’exécution, puis appuyez sur le point d’arrêt sur le serveur dans le `WeatherForecastController` :</span><span class="sxs-lookup"><span data-stu-id="07d2f-145">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`:</span></span>

   ![Serveur de débogage](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. <span data-ttu-id="07d2f-147">Appuyez de nouveau sur <kbd>F5</kbd> pour permettre à l’exécution de se poursuivre et consultez le tableau prévisions météo rendu.</span><span class="sxs-lookup"><span data-stu-id="07d2f-147">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered.</span></span>

<a id="vscode"></a>

## <a name="visual-studio-code"></a><span data-ttu-id="07d2f-148">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="07d2f-148">Visual Studio Code</span></span>

### <a name="debug-standalone-no-locblazor-webassembly"></a><span data-ttu-id="07d2f-149">Déboguer autonomeBlazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="07d2f-149">Debug standalone Blazor WebAssembly</span></span>

1. <span data-ttu-id="07d2f-150">Ouvrez l' Blazor WebAssembly application autonome dans vs code.</span><span class="sxs-lookup"><span data-stu-id="07d2f-150">Open the standalone Blazor WebAssembly app in VS Code.</span></span>

   <span data-ttu-id="07d2f-151">Vous pouvez recevoir la notification suivante indiquant qu’une configuration supplémentaire est requise pour activer le débogage :</span><span class="sxs-lookup"><span data-stu-id="07d2f-151">You may receive the following notification that additional setup is required to enable debugging:</span></span>
   
   ![Configuration supplémentaire requise](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)
   
   <span data-ttu-id="07d2f-153">Si vous recevez la notification :</span><span class="sxs-lookup"><span data-stu-id="07d2f-153">If you receive the notification:</span></span>

   * <span data-ttu-id="07d2f-154">Vérifiez que la dernière [extension C# pour Visual Studio code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) est installée.</span><span class="sxs-lookup"><span data-stu-id="07d2f-154">Confirm that the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) is installed.</span></span> <span data-ttu-id="07d2f-155">Pour inspecter les extensions installées, ouvrez **Afficher**les  >  **Extensions** à partir de la barre de menus ou sélectionnez l’icône **Extensions** dans l’encadré **activité** .</span><span class="sxs-lookup"><span data-stu-id="07d2f-155">To inspect the installed extensions, open **View** > **Extensions** from the menu bar or select the **Extensions** icon in the **Activity** sidebar.</span></span>
   * <span data-ttu-id="07d2f-156">Confirmez que le débogage de l’aperçu JavaScript est activé.</span><span class="sxs-lookup"><span data-stu-id="07d2f-156">Confirm that JavaScript preview debugging is enabled.</span></span> <span data-ttu-id="07d2f-157">Ouvrez les paramètres à partir de la barre de menus (paramètres préférences de**fichiers**  >  **Preferences**  >  **Settings**).</span><span class="sxs-lookup"><span data-stu-id="07d2f-157">Open the settings from the menu bar (**File** > **Preferences** > **Settings**).</span></span> <span data-ttu-id="07d2f-158">Recherchez à l’aide des mots clés `debug preview` .</span><span class="sxs-lookup"><span data-stu-id="07d2f-158">Search using the keywords `debug preview`.</span></span> <span data-ttu-id="07d2f-159">Dans les résultats de la recherche, vérifiez que la case à cocher **Déboguer > JavaScript : utiliser l’aperçu** est activée.</span><span class="sxs-lookup"><span data-stu-id="07d2f-159">In the search results, confirm that the check box for **Debug > JavaScript: Use Preview** is checked.</span></span> <span data-ttu-id="07d2f-160">Si l’option permettant d’activer le débogage de l’aperçu n’est pas présente, effectuez une mise à niveau vers la dernière version de VS Code ou installez l' [extension de débogueur JavaScript](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) (VS Code versions 1,46 ou antérieures).</span><span class="sxs-lookup"><span data-stu-id="07d2f-160">If the option to enable preview debugging isn't present, either upgrade to the latest version of VS Code or install the [JavaScript Debugger Extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) (VS Code versions 1.46 or earlier).</span></span>
   * <span data-ttu-id="07d2f-161">Rechargez la fenêtre.</span><span class="sxs-lookup"><span data-stu-id="07d2f-161">Reload the window.</span></span>

1. <span data-ttu-id="07d2f-162">Démarrez le débogage à l’aide du raccourci clavier <kbd>F5</kbd> ou de l’élément de menu.</span><span class="sxs-lookup"><span data-stu-id="07d2f-162">Start debugging using the <kbd>F5</kbd> keyboard shortcut or the menu item.</span></span>

   > [!NOTE]
   > <span data-ttu-id="07d2f-163">L' **exécution sans débogage** (<kbd>CTRL</kbd> + <kbd>F5</kbd>) n’est pas prise en charge.</span><span class="sxs-lookup"><span data-stu-id="07d2f-163">**Run Without Debugging** (<kbd>Ctrl</kbd>+<kbd>F5</kbd>) isn't supported.</span></span>

1. <span data-ttu-id="07d2f-164">Quand vous y êtes invité, sélectionnez l’option de \*\* Blazor WebAssembly débogage\*\* pour démarrer le débogage.</span><span class="sxs-lookup"><span data-stu-id="07d2f-164">When prompted, select the **Blazor WebAssembly Debug** option to start debugging.</span></span>

   ![Liste des options de débogage disponibles](index/_static/blazor-vscode-debugtypes.png)

1. <span data-ttu-id="07d2f-166">L’application autonome est lancée et un navigateur de débogage est ouvert.</span><span class="sxs-lookup"><span data-stu-id="07d2f-166">The standalone app is launched, and a debugging browser is opened.</span></span>

1. <span data-ttu-id="07d2f-167">Définissez un point d’arrêt dans la `IncrementCount` méthode du `Counter` composant, puis sélectionnez le bouton pour atteindre le point d’arrêt :</span><span class="sxs-lookup"><span data-stu-id="07d2f-167">Set a breakpoint in the `IncrementCount` method in the `Counter` component and then select the button to hit the breakpoint:</span></span>

   ![Déboguer le compteur dans VS Code](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

### <a name="debug-hosted-no-locblazor-webassembly"></a><span data-ttu-id="07d2f-169">Débogage hébergéBlazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="07d2f-169">Debug hosted Blazor WebAssembly</span></span>

1. <span data-ttu-id="07d2f-170">Ouvrez le Blazor WebAssembly dossier de solution de l’application hébergée dans vs code.</span><span class="sxs-lookup"><span data-stu-id="07d2f-170">Open the hosted Blazor WebAssembly app's solution folder in VS Code.</span></span>

1. <span data-ttu-id="07d2f-171">Si aucune configuration de lancement n’est définie pour le projet, la notification suivante s’affiche.</span><span class="sxs-lookup"><span data-stu-id="07d2f-171">If there's no launch configuration set for the project, the following notification appears.</span></span> <span data-ttu-id="07d2f-172">Sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="07d2f-172">Select **Yes**.</span></span>

   ![Ajouter les ressources requises](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. <span data-ttu-id="07d2f-174">Dans la palette de commandes en haut de la fenêtre, sélectionnez le projet *serveur* dans la solution hébergée.</span><span class="sxs-lookup"><span data-stu-id="07d2f-174">In the command palette at the top of the window, select the *Server* project within the hosted solution.</span></span>

<span data-ttu-id="07d2f-175">Un `launch.json` fichier est généré à l’aide de la configuration de lancement pour le lancement du débogueur.</span><span class="sxs-lookup"><span data-stu-id="07d2f-175">A `launch.json` file is generated with the launch configuration for launching the debugger.</span></span>

### <a name="attach-to-an-existing-debugging-session"></a><span data-ttu-id="07d2f-176">Attacher à une session de débogage existante</span><span class="sxs-lookup"><span data-stu-id="07d2f-176">Attach to an existing debugging session</span></span>

<span data-ttu-id="07d2f-177">Pour attacher une application en cours d’exécution Blazor , créez un `launch.json` fichier avec la configuration suivante :</span><span class="sxs-lookup"><span data-stu-id="07d2f-177">To attach to a running Blazor app, create a `launch.json` file with the following configuration:</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing Blazor WebAssembly Application"
}
```

> [!NOTE]
> <span data-ttu-id="07d2f-178">L’attachement à une session de débogage est pris en charge uniquement pour les applications autonomes.</span><span class="sxs-lookup"><span data-stu-id="07d2f-178">Attaching to a debugging session is only supported for standalone apps.</span></span> <span data-ttu-id="07d2f-179">Pour utiliser le débogage de pile complète, vous devez lancer l’application à partir de VS Code.</span><span class="sxs-lookup"><span data-stu-id="07d2f-179">To use full-stack debugging, you must launch the app from VS Code.</span></span>

### <a name="launch-configuration-options"></a><span data-ttu-id="07d2f-180">Lancer les options de configuration</span><span class="sxs-lookup"><span data-stu-id="07d2f-180">Launch configuration options</span></span>

<span data-ttu-id="07d2f-181">Les options de configuration de lancement suivantes sont prises en charge pour le `blazorwasm` type de débogage ( `.vscode/launch.json` ).</span><span class="sxs-lookup"><span data-stu-id="07d2f-181">The following launch configuration options are supported for the `blazorwasm` debug type (`.vscode/launch.json`).</span></span>

| <span data-ttu-id="07d2f-182">Option</span><span class="sxs-lookup"><span data-stu-id="07d2f-182">Option</span></span>    | <span data-ttu-id="07d2f-183">Description</span><span class="sxs-lookup"><span data-stu-id="07d2f-183">Description</span></span> |
| --------- | ----------- |
| `request` | <span data-ttu-id="07d2f-184">Utilisez `launch` pour lancer et attacher une session de débogage à une Blazor WebAssembly application ou `attach` pour attacher une session de débogage à une application déjà en cours d’exécution.</span><span class="sxs-lookup"><span data-stu-id="07d2f-184">Use `launch` to launch and attach a debugging session to a Blazor WebAssembly app or `attach` to attach a debugging session to an already-running app.</span></span> |
| `url`     | <span data-ttu-id="07d2f-185">URL à ouvrir dans le navigateur lors du débogage.</span><span class="sxs-lookup"><span data-stu-id="07d2f-185">The URL to open in the browser when debugging.</span></span> <span data-ttu-id="07d2f-186">La valeur par défaut est `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="07d2f-186">Defaults to `https://localhost:5001`.</span></span> |
| `browser` | <span data-ttu-id="07d2f-187">Navigateur à lancer pour la session de débogage.</span><span class="sxs-lookup"><span data-stu-id="07d2f-187">The browser to launch for the debugging session.</span></span> <span data-ttu-id="07d2f-188">A la valeur `edge` ou `chrome`.</span><span class="sxs-lookup"><span data-stu-id="07d2f-188">Set to `edge` or `chrome`.</span></span> <span data-ttu-id="07d2f-189">La valeur par défaut est `chrome`.</span><span class="sxs-lookup"><span data-stu-id="07d2f-189">Defaults to `chrome`.</span></span> |
| `trace`   | <span data-ttu-id="07d2f-190">Utilisé pour générer des journaux à partir du débogueur JS.</span><span class="sxs-lookup"><span data-stu-id="07d2f-190">Used to generate logs from the JS debugger.</span></span> <span data-ttu-id="07d2f-191">Définissez sur `true` pour générer des journaux.</span><span class="sxs-lookup"><span data-stu-id="07d2f-191">Set to `true` to generate logs.</span></span> |
| `hosted`  | <span data-ttu-id="07d2f-192">Doit avoir la valeur en `true` cas de lancement et de débogage d’une application hébergée Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="07d2f-192">Must be set to `true` if launching and debugging a hosted Blazor WebAssembly app.</span></span> |
| `webRoot` | <span data-ttu-id="07d2f-193">Spécifie le chemin d’accès absolu du serveur Web.</span><span class="sxs-lookup"><span data-stu-id="07d2f-193">Specifies the absolute path of the web server.</span></span> <span data-ttu-id="07d2f-194">Doit être défini si une application est servie à partir d’un sous-itinéraire.</span><span class="sxs-lookup"><span data-stu-id="07d2f-194">Should be set if an app is served from a sub-route.</span></span> |
| `timeout` | <span data-ttu-id="07d2f-195">Nombre de millisecondes d’attente de l’attachement de la session de débogage.</span><span class="sxs-lookup"><span data-stu-id="07d2f-195">The number of milliseconds to wait for the debugging session to attach.</span></span> <span data-ttu-id="07d2f-196">La valeur par défaut est 30 000 millisecondes (30 secondes).</span><span class="sxs-lookup"><span data-stu-id="07d2f-196">Defaults to 30,000 milliseconds (30 seconds).</span></span> |
| `program` | <span data-ttu-id="07d2f-197">Référence au fichier exécutable pour exécuter le serveur de l’application hébergée.</span><span class="sxs-lookup"><span data-stu-id="07d2f-197">A reference to the executable to run the server of the hosted app.</span></span> <span data-ttu-id="07d2f-198">Doit être défini si `hosted` a la valeur `true` .</span><span class="sxs-lookup"><span data-stu-id="07d2f-198">Must be set if `hosted` is `true`.</span></span> |
| `cwd`     | <span data-ttu-id="07d2f-199">Répertoire de travail dans lequel l’application doit être lancée.</span><span class="sxs-lookup"><span data-stu-id="07d2f-199">The working directory to launch the app under.</span></span> <span data-ttu-id="07d2f-200">Doit être défini si `hosted` a la valeur `true` .</span><span class="sxs-lookup"><span data-stu-id="07d2f-200">Must be set if `hosted` is `true`.</span></span> |
| `env`     | <span data-ttu-id="07d2f-201">Variables d’environnement à fournir au processus lancé.</span><span class="sxs-lookup"><span data-stu-id="07d2f-201">The environment variables to provide to the launched process.</span></span> <span data-ttu-id="07d2f-202">Applicable uniquement si `hosted` a la valeur `true` .</span><span class="sxs-lookup"><span data-stu-id="07d2f-202">Only applicable if `hosted` is set to `true`.</span></span> |

### <a name="example-launch-configurations"></a><span data-ttu-id="07d2f-203">Exemples de configurations de lancement</span><span class="sxs-lookup"><span data-stu-id="07d2f-203">Example launch configurations</span></span>

#### <a name="launch-and-debug-a-standalone-no-locblazor-webassembly-app"></a><span data-ttu-id="07d2f-204">Lancer et déboguer une Blazor WebAssembly application autonome</span><span class="sxs-lookup"><span data-stu-id="07d2f-204">Launch and debug a standalone Blazor WebAssembly app</span></span>

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

#### <a name="attach-to-a-running-app-at-a-specified-url"></a><span data-ttu-id="07d2f-205">Attacher à une application en cours d’exécution à une URL spécifiée</span><span class="sxs-lookup"><span data-stu-id="07d2f-205">Attach to a running app at a specified URL</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

#### <a name="launch-and-debug-a-hosted-no-locblazor-webassembly-app-with-microsoft-edge"></a><span data-ttu-id="07d2f-206">Lancer et déboguer une application hébergée Blazor WebAssembly avec Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="07d2f-206">Launch and debug a hosted Blazor WebAssembly app with Microsoft Edge</span></span>

<span data-ttu-id="07d2f-207">La configuration du navigateur est par défaut Google Chrome.</span><span class="sxs-lookup"><span data-stu-id="07d2f-207">Browser configuration defaults to Google Chrome.</span></span> <span data-ttu-id="07d2f-208">Lorsque vous utilisez Microsoft Edge pour le débogage, affectez à la valeur `browser` `edge` .</span><span class="sxs-lookup"><span data-stu-id="07d2f-208">When using Microsoft Edge for debugging, set `browser` to `edge`.</span></span> <span data-ttu-id="07d2f-209">Pour utiliser Google Chrome, vous ne devez pas définir l' `browser` option ou définir la valeur de l’option sur `chrome` .</span><span class="sxs-lookup"><span data-stu-id="07d2f-209">To use Google Chrome, either don't set the `browser` option or set the option's value to `chrome`.</span></span>

```json
{
  "name": "Launch and Debug Hosted Blazor WebAssembly App",
  "type": "blazorwasm",
  "request": "launch",
  "hosted": true,
  "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/MyHostedApp.Server.dll",
  "cwd": "${workspaceFolder}/Server",
  "browser": "edge"
}
```

<span data-ttu-id="07d2f-210">Dans l’exemple précédent, `MyHostedApp.Server.dll` est l’assembly de l’application *serveur* .</span><span class="sxs-lookup"><span data-stu-id="07d2f-210">In the preceding example, `MyHostedApp.Server.dll` is the *Server* app's assembly.</span></span> <span data-ttu-id="07d2f-211">Le `.vscode` dossier se trouve dans le dossier de la solution, en regard des `Client` `Server` dossiers, et `Shared` .</span><span class="sxs-lookup"><span data-stu-id="07d2f-211">The `.vscode` folder is located in the solution's folder next to the `Client`, `Server`, and `Shared` folders.</span></span>

## <a name="debug-in-the-browser"></a><span data-ttu-id="07d2f-212">Déboguer dans le navigateur</span><span class="sxs-lookup"><span data-stu-id="07d2f-212">Debug in the browser</span></span>

1. <span data-ttu-id="07d2f-213">Exécutez une version Debug de l’application dans l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="07d2f-213">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="07d2f-214">Lancez un navigateur et accédez à l’URL de l’application (par exemple, `https://localhost:5001` ).</span><span class="sxs-lookup"><span data-stu-id="07d2f-214">Launch a browser and navigate to the app's URL (for example, `https://localhost:5001`).</span></span>

1. <span data-ttu-id="07d2f-215">Dans le navigateur, essayez de commencer le débogage à distance en appuyant sur <kbd>MAJ</kbd> + <kbd>ALT</kbd> + <kbd>D</kbd>.</span><span class="sxs-lookup"><span data-stu-id="07d2f-215">In the browser, attempt to commence remote debugging by pressing <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.</span></span>

   <span data-ttu-id="07d2f-216">Le navigateur doit s’exécuter avec le débogage à distance activé, qui n’est pas le paramètre par défaut.</span><span class="sxs-lookup"><span data-stu-id="07d2f-216">The browser must be running with remote debugging enabled, which isn't the default.</span></span> <span data-ttu-id="07d2f-217">Si le débogage distant est désactivé, une page d’erreur **Impossible de trouver un onglet de navigateur pouvant être débogué** est affichée avec des instructions pour lancer le navigateur avec le port de débogage ouvert.</span><span class="sxs-lookup"><span data-stu-id="07d2f-217">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is rendered with instructions for launching the browser with the debugging port open.</span></span> <span data-ttu-id="07d2f-218">Suivez les instructions de votre navigateur pour ouvrir une nouvelle fenêtre de navigateur.</span><span class="sxs-lookup"><span data-stu-id="07d2f-218">Follow the instructions for your browser, which opens a new browser window.</span></span> <span data-ttu-id="07d2f-219">Fermez la fenêtre de navigateur précédente.</span><span class="sxs-lookup"><span data-stu-id="07d2f-219">Close the previous browser window.</span></span>

1. <span data-ttu-id="07d2f-220">Une fois que le navigateur est en cours d’exécution avec le débogage distant activé, le raccourci clavier de débogage (<kbd>MAJ</kbd> + <kbd>ALT</kbd> + <kbd>D</kbd>) ouvre un nouvel onglet du débogueur.</span><span class="sxs-lookup"><span data-stu-id="07d2f-220">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut (<kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>) opens a new debugger tab.</span></span>

1. <span data-ttu-id="07d2f-221">Après un moment, l’onglet **sources** affiche une liste des assemblys .net de l’application dans le `file://` nœud.</span><span class="sxs-lookup"><span data-stu-id="07d2f-221">After a moment, the **Sources** tab shows a list of the app's .NET assemblies within the `file://` node.</span></span>

1. <span data-ttu-id="07d2f-222">Dans le code du composant ( `.razor` fichiers) et les fichiers de code C# ( `.cs` ), les points d’arrêt que vous définissez sont atteints lors de l’exécution du code.</span><span class="sxs-lookup"><span data-stu-id="07d2f-222">In component code (`.razor` files) and C# code files (`.cs`), breakpoints that you set are hit when code executes.</span></span> <span data-ttu-id="07d2f-223">Une fois le point d’arrêt atteint, une seule étape (<kbd>F10</kbd>) passe par l’exécution du code ou de la reprise (<kbd>F8</kbd>).</span><span class="sxs-lookup"><span data-stu-id="07d2f-223">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

<span data-ttu-id="07d2f-224">Blazorfournit un proxy de débogage qui implémente le [protocole chrome devtools](https://chromedevtools.github.io/devtools-protocol/) et augmente le protocole avec. Informations spécifiques à .net.</span><span class="sxs-lookup"><span data-stu-id="07d2f-224">Blazor provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="07d2f-225">Quand le raccourci clavier de débogage est enfoncé, Blazor pointe le devtools chrome au niveau du proxy.</span><span class="sxs-lookup"><span data-stu-id="07d2f-225">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="07d2f-226">Le proxy se connecte à la fenêtre du navigateur que vous cherchez à déboguer (par conséquent, il est nécessaire d’activer le débogage distant).</span><span class="sxs-lookup"><span data-stu-id="07d2f-226">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="07d2f-227">Mappages des sources du navigateur</span><span class="sxs-lookup"><span data-stu-id="07d2f-227">Browser source maps</span></span>

<span data-ttu-id="07d2f-228">Les mappages de source de navigateur permettent au navigateur de mapper les fichiers compilés à leurs fichiers sources d’origine et sont couramment utilisés pour le débogage côté client.</span><span class="sxs-lookup"><span data-stu-id="07d2f-228">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="07d2f-229">Toutefois, Blazor ne mappe actuellement pas C# directement à JavaScript/WASM.</span><span class="sxs-lookup"><span data-stu-id="07d2f-229">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="07d2f-230">Au lieu de cela, Blazor fait l’interprétation du langage intermédiaire dans le navigateur, les mappages de source ne sont donc pas pertinents.</span><span class="sxs-lookup"><span data-stu-id="07d2f-230">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="07d2f-231">Dépanner</span><span class="sxs-lookup"><span data-stu-id="07d2f-231">Troubleshoot</span></span>

<span data-ttu-id="07d2f-232">Si vous rencontrez des erreurs, les conseils suivants peuvent vous aider :</span><span class="sxs-lookup"><span data-stu-id="07d2f-232">If you're running into errors, the following tips may help:</span></span>

* <span data-ttu-id="07d2f-233">Dans l’onglet **débogueur** , ouvrez les outils de développement de votre navigateur.</span><span class="sxs-lookup"><span data-stu-id="07d2f-233">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="07d2f-234">Dans la console, exécutez `localStorage.clear()` pour supprimer tous les points d’arrêt.</span><span class="sxs-lookup"><span data-stu-id="07d2f-234">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
* <span data-ttu-id="07d2f-235">Confirmez que vous avez installé et approuvé le certificat de développement ASP.NET Core HTTPs.</span><span class="sxs-lookup"><span data-stu-id="07d2f-235">Confirm that you've installed and trusted the ASP.NET Core HTTPS development certificate.</span></span> <span data-ttu-id="07d2f-236">Pour plus d'informations, consultez <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span><span class="sxs-lookup"><span data-stu-id="07d2f-236">For more information, see <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span></span>
* <span data-ttu-id="07d2f-237">Visual Studio requiert l’option **activer le débogage JavaScript pour ASP.net (chrome, Edge et IE)** dans **Outils**  >  **options**  >  **débogage**  >  **général**.</span><span class="sxs-lookup"><span data-stu-id="07d2f-237">Visual Studio requires the **Enable JavaScript debugging for ASP.NET (Chrome, Edge and IE)** option in **Tools** > **Options** > **Debugging** > **General**.</span></span> <span data-ttu-id="07d2f-238">Il s’agit du paramètre par défaut pour Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="07d2f-238">This is the default setting for Visual Studio.</span></span> <span data-ttu-id="07d2f-239">Si le débogage ne fonctionne pas, vérifiez que l’option est sélectionnée.</span><span class="sxs-lookup"><span data-stu-id="07d2f-239">If debugging isn't working, confirm that the option is selected.</span></span>

### <a name="breakpoints-in-oninitializedasync-not-hit"></a><span data-ttu-id="07d2f-240">Points d’arrêt `OnInitialized{Async}` non atteints</span><span class="sxs-lookup"><span data-stu-id="07d2f-240">Breakpoints in `OnInitialized{Async}` not hit</span></span>

<span data-ttu-id="07d2f-241">Le Blazor proxy de débogage du Framework prend un peu de temps, il est donc possible que les points d’arrêt dans la [ `OnInitialized{Async}` méthode Lifecycle](xref:blazor/components/lifecycle#component-initialization-methods) ne soient pas atteints.</span><span class="sxs-lookup"><span data-stu-id="07d2f-241">The Blazor framework's debugging proxy takes a short time to launch, so breakpoints in the [`OnInitialized{Async}` lifecycle method](xref:blazor/components/lifecycle#component-initialization-methods) might not be hit.</span></span> <span data-ttu-id="07d2f-242">Nous vous recommandons d’ajouter un délai au début du corps de la méthode pour permettre au proxy de débogage de se lancer avant que le point d’arrêt ne soit atteint.</span><span class="sxs-lookup"><span data-stu-id="07d2f-242">We recommend adding a delay at the start of the method body to give the debug proxy some time to launch before the breakpoint is hit.</span></span> <span data-ttu-id="07d2f-243">Vous pouvez inclure le délai en fonction d’une [ `if` directive de compilateur](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) pour vous assurer que le délai n’est pas présent pour une version Release de l’application.</span><span class="sxs-lookup"><span data-stu-id="07d2f-243">You can include the delay based on an [`if` compiler directive](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) to ensure that the delay isn't present for a release build of the app.</span></span>

<span data-ttu-id="07d2f-244"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span><span class="sxs-lookup"><span data-stu-id="07d2f-244"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span></span>

```csharp
protected override void OnInitialized()
{
#if DEBUG
    Thread.Sleep(10000)
#endif

    ...
}
```

<span data-ttu-id="07d2f-245"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>:</span><span class="sxs-lookup"><span data-stu-id="07d2f-245"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
#if DEBUG
    await Task.Delay(10000)
#endif

    ...
}
```
