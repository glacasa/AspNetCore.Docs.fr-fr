---
title: ASP.NET Core de débogageBlazor WebAssembly
author: guardrex
description: Découvrez comment déboguer des Blazor applications.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/06/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/debug
ms.openlocfilehash: c48eb19c5a1759aace112e2afb1637c649173a3d
ms.sourcegitcommit: fa89d6553378529ae86b388689ac2c6f38281bb9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86059901"
---
# <a name="debug-aspnet-core-blazor-webassembly"></a><span data-ttu-id="b2b04-103">ASP.NET Core de débogageBlazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="b2b04-103">Debug ASP.NET Core Blazor WebAssembly</span></span>

[<span data-ttu-id="b2b04-104">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="b2b04-104">Daniel Roth</span></span>](https://github.com/danroth27)

Blazor WebAssembly<span data-ttu-id="b2b04-105">les applications peuvent être déboguées à l’aide des outils de développement de navigateur dans les navigateurs basés sur le chrome (Edge/chrome).</span><span class="sxs-lookup"><span data-stu-id="b2b04-105"> apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span> <span data-ttu-id="b2b04-106">Vous pouvez également déboguer votre application à l’aide de Visual Studio ou Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="b2b04-106">Alternatively, you can debug your app using Visual Studio or Visual Studio Code.</span></span>

<span data-ttu-id="b2b04-107">Les scénarios disponibles sont les suivants :</span><span class="sxs-lookup"><span data-stu-id="b2b04-107">Available scenarios include:</span></span>

* <span data-ttu-id="b2b04-108">Définir et supprimer des points d’arrêt.</span><span class="sxs-lookup"><span data-stu-id="b2b04-108">Set and remove breakpoints.</span></span>
* <span data-ttu-id="b2b04-109">Exécutez l’application avec prise en charge du débogage dans Visual Studio et Visual Studio Code (prise en charge de<kbd>F5</kbd> ).</span><span class="sxs-lookup"><span data-stu-id="b2b04-109">Run the app with debugging support in Visual Studio and Visual Studio Code (<kbd>F5</kbd> support).</span></span>
* <span data-ttu-id="b2b04-110">Une étape (<kbd>F10</kbd>) à l’aide du code.</span><span class="sxs-lookup"><span data-stu-id="b2b04-110">Single-step (<kbd>F10</kbd>) through the code.</span></span>
* <span data-ttu-id="b2b04-111">Reprendre l’exécution du code avec <kbd>F8</kbd> dans un navigateur ou <kbd>F5</kbd> dans Visual Studio ou Visual Studio code.</span><span class="sxs-lookup"><span data-stu-id="b2b04-111">Resume code execution with <kbd>F8</kbd> in a browser or <kbd>F5</kbd> in Visual Studio or Visual Studio Code.</span></span>
* <span data-ttu-id="b2b04-112">Dans l' *affichage des variables locales,* observez les valeurs des variables locales.</span><span class="sxs-lookup"><span data-stu-id="b2b04-112">In the *Locals* display, observe the values of local variables.</span></span>
* <span data-ttu-id="b2b04-113">Consultez la pile des appels, y compris les chaînes d’appel qui passent de JavaScript à .NET et de .NET à JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b2b04-113">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="b2b04-114">Pour le moment, vous *ne pouvez pas*:</span><span class="sxs-lookup"><span data-stu-id="b2b04-114">For now, you *can't*:</span></span>

* <span data-ttu-id="b2b04-115">Arrêt sur les exceptions non gérées.</span><span class="sxs-lookup"><span data-stu-id="b2b04-115">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="b2b04-116">Atteindre les points d’arrêt pendant le démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="b2b04-116">Hit breakpoints during app startup.</span></span>

<span data-ttu-id="b2b04-117">Nous continuerons à améliorer l’expérience de débogage dans les versions à venir.</span><span class="sxs-lookup"><span data-stu-id="b2b04-117">We will continue to improve the debugging experience in upcoming releases.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="b2b04-118">Prérequis</span><span class="sxs-lookup"><span data-stu-id="b2b04-118">Prerequisites</span></span>

<span data-ttu-id="b2b04-119">Le débogage requiert l’un des navigateurs suivants :</span><span class="sxs-lookup"><span data-stu-id="b2b04-119">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="b2b04-120">Microsoft Edge (version 80 ou ultérieure)</span><span class="sxs-lookup"><span data-stu-id="b2b04-120">Microsoft Edge (version 80 or later)</span></span>
* <span data-ttu-id="b2b04-121">Google Chrome (version 70 ou ultérieure)</span><span class="sxs-lookup"><span data-stu-id="b2b04-121">Google Chrome (version 70 or later)</span></span>

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a><span data-ttu-id="b2b04-122">Activer le débogage pour Visual Studio et Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b2b04-122">Enable debugging for Visual Studio and Visual Studio Code</span></span>

<span data-ttu-id="b2b04-123">Pour activer le débogage d’une Blazor WebAssembly application existante, mettez à jour le `launchSettings.json` fichier dans le projet de démarrage pour inclure la `inspectUri` propriété suivante dans chaque profil de lancement :</span><span class="sxs-lookup"><span data-stu-id="b2b04-123">To enable debugging for an existing Blazor WebAssembly app, update the `launchSettings.json` file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="b2b04-124">Une fois mis à jour, le `launchSettings.json` fichier doit ressembler à l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="b2b04-124">Once updated, the `launchSettings.json` file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="b2b04-125">La `inspectUri` propriété :</span><span class="sxs-lookup"><span data-stu-id="b2b04-125">The `inspectUri` property:</span></span>

* <span data-ttu-id="b2b04-126">Permet à l’IDE de détecter que l’application est une Blazor WebAssembly application.</span><span class="sxs-lookup"><span data-stu-id="b2b04-126">Enables the IDE to detect that the app is a Blazor WebAssembly app.</span></span>
* <span data-ttu-id="b2b04-127">Indique à l’infrastructure de débogage de script de se connecter au navigateur via le Blazor proxy de débogage de.</span><span class="sxs-lookup"><span data-stu-id="b2b04-127">Instructs the script debugging infrastructure to connect to the browser through Blazor's debugging proxy.</span></span>

<span data-ttu-id="b2b04-128">Les valeurs d’espace réservé pour le protocole WebSockets ( `wsProtocol` ), l’hôte ( `url.hostname` ), le port ( `url.port` ) et l’URI de l’inspecteur sur le navigateur lancé ( `browserInspectUri` ) sont fournies par l’infrastructure.</span><span class="sxs-lookup"><span data-stu-id="b2b04-128">The placeholder values for the WebSockets protocol (`wsProtocol`), host (`url.hostname`), port (`url.port`), and inspector URI on the launched browser (`browserInspectUri`) are provided by the framework.</span></span>

## <a name="visual-studio"></a><span data-ttu-id="b2b04-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b2b04-129">Visual Studio</span></span>

<span data-ttu-id="b2b04-130">Pour déboguer une Blazor WebAssembly application dans Visual Studio :</span><span class="sxs-lookup"><span data-stu-id="b2b04-130">To debug a Blazor WebAssembly app in Visual Studio:</span></span>

1. <span data-ttu-id="b2b04-131">Créez une nouvelle ASP.NET Core application hébergée Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="b2b04-131">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="b2b04-132">Appuyez sur <kbd>F5</kbd> pour exécuter l’application dans le débogueur.</span><span class="sxs-lookup"><span data-stu-id="b2b04-132">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>
1. <span data-ttu-id="b2b04-133">Définissez un point d’arrêt dans `Pages/Counter.razor` la `IncrementCount` méthode.</span><span class="sxs-lookup"><span data-stu-id="b2b04-133">Set a breakpoint in `Pages/Counter.razor` in the `IncrementCount` method.</span></span>
1. <span data-ttu-id="b2b04-134">Accédez à l' **`Counter`** onglet et sélectionnez le bouton pour atteindre le point d’arrêt :</span><span class="sxs-lookup"><span data-stu-id="b2b04-134">Browse to the **`Counter`** tab and select the button to hit the breakpoint:</span></span>

   ![Compteur de débogage](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. <span data-ttu-id="b2b04-136">Examinez la valeur du `currentCount` champ dans la fenêtre variables locales :</span><span class="sxs-lookup"><span data-stu-id="b2b04-136">Check out the value of the `currentCount` field in the locals window:</span></span>

   ![Afficher les variables locales](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. <span data-ttu-id="b2b04-138">Appuyez sur <kbd>F5</kbd> pour poursuivre l’exécution.</span><span class="sxs-lookup"><span data-stu-id="b2b04-138">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="b2b04-139">Lors du débogage de votre Blazor WebAssembly application, vous pouvez également déboguer votre code serveur :</span><span class="sxs-lookup"><span data-stu-id="b2b04-139">While debugging your Blazor WebAssembly app, you can also debug your server code:</span></span>

1. <span data-ttu-id="b2b04-140">Définissez un point d’arrêt dans la `Pages/FetchData.razor` page de <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="b2b04-140">Set a breakpoint in the `Pages/FetchData.razor` page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="b2b04-141">Définissez un point d’arrêt dans la `WeatherForecastController` `Get` méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="b2b04-141">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="b2b04-142">Accédez à l' **`Fetch Data`** onglet pour atteindre le premier point d’arrêt dans le `FetchData` composant juste avant qu’il envoie une requête HTTP au serveur :</span><span class="sxs-lookup"><span data-stu-id="b2b04-142">Browse to the **`Fetch Data`** tab to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server:</span></span>

   ![Déboguer extraire des données](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. <span data-ttu-id="b2b04-144">Appuyez sur <kbd>F5</kbd> pour poursuivre l’exécution, puis appuyez sur le point d’arrêt sur le serveur dans le `WeatherForecastController` :</span><span class="sxs-lookup"><span data-stu-id="b2b04-144">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`:</span></span>

   ![Serveur de débogage](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. <span data-ttu-id="b2b04-146">Appuyez de nouveau sur <kbd>F5</kbd> pour permettre à l’exécution de se poursuivre et consultez le tableau prévisions météo rendu.</span><span class="sxs-lookup"><span data-stu-id="b2b04-146">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered.</span></span>

<a id="vscode"></a>

## <a name="visual-studio-code"></a><span data-ttu-id="b2b04-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b2b04-147">Visual Studio Code</span></span>

<span data-ttu-id="b2b04-148">Pour plus d’informations sur l’installation de Visual Studio Code pour le Blazor développement d’applications, consultez <xref:blazor/tooling> .</span><span class="sxs-lookup"><span data-stu-id="b2b04-148">For information on installing Visual Studio Code for Blazor app development, see <xref:blazor/tooling>.</span></span>

### <a name="debug-standalone-blazor-webassembly"></a><span data-ttu-id="b2b04-149">Déboguer autonomeBlazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="b2b04-149">Debug standalone Blazor WebAssembly</span></span>

1. <span data-ttu-id="b2b04-150">Ouvrez l' Blazor WebAssembly application autonome dans vs code.</span><span class="sxs-lookup"><span data-stu-id="b2b04-150">Open the standalone Blazor WebAssembly app in VS Code.</span></span>

   <span data-ttu-id="b2b04-151">Si vous recevez la notification suivante indiquant qu’une configuration supplémentaire est requise pour activer le débogage :</span><span class="sxs-lookup"><span data-stu-id="b2b04-151">If you receive the following notification that additional setup is required to enable debugging:</span></span>
   
   * <span data-ttu-id="b2b04-152">Vérifiez que vous avez installé les extensions appropriées.</span><span class="sxs-lookup"><span data-stu-id="b2b04-152">Confirm that you have the correct extensions installed.</span></span>
   * <span data-ttu-id="b2b04-153">Confirmez que le débogage de l’aperçu JavaScript est activé.</span><span class="sxs-lookup"><span data-stu-id="b2b04-153">Confirm that JavaScript preview debugging is enabled.</span></span>
   * <span data-ttu-id="b2b04-154">Rechargez la fenêtre.</span><span class="sxs-lookup"><span data-stu-id="b2b04-154">Reload the window.</span></span>

   ![Configuration supplémentaire requise](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

1. <span data-ttu-id="b2b04-156">Démarrez le débogage à l’aide du raccourci clavier <kbd>F5</kbd> ou de l’élément de menu.</span><span class="sxs-lookup"><span data-stu-id="b2b04-156">Start debugging using the <kbd>F5</kbd> keyboard shortcut or the menu item.</span></span>

1. <span data-ttu-id="b2b04-157">Quand vous y êtes invité, sélectionnez l’option de \*\* Blazor WebAssembly débogage\*\* pour démarrer le débogage.</span><span class="sxs-lookup"><span data-stu-id="b2b04-157">When prompted, select the **Blazor WebAssembly Debug** option to start debugging.</span></span>

   ![Liste des options de débogage disponibles](index/_static/blazor-vscode-debugtypes.png)

1. <span data-ttu-id="b2b04-159">L’application autonome est lancée et un navigateur de débogage est ouvert.</span><span class="sxs-lookup"><span data-stu-id="b2b04-159">The standalone app is launched, and a debugging browser is opened.</span></span>

1. <span data-ttu-id="b2b04-160">Définissez un point d’arrêt dans la `IncrementCount` méthode du `Counter` composant, puis sélectionnez le bouton pour atteindre le point d’arrêt :</span><span class="sxs-lookup"><span data-stu-id="b2b04-160">Set a breakpoint in the `IncrementCount` method in the `Counter` component and then select the button to hit the breakpoint:</span></span>

   ![Déboguer le compteur dans VS Code](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

### <a name="debug-hosted-blazor-webassembly"></a><span data-ttu-id="b2b04-162">Débogage hébergéBlazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="b2b04-162">Debug hosted Blazor WebAssembly</span></span>

1. <span data-ttu-id="b2b04-163">Ouvrez l’application hébergée Blazor WebAssembly dans vs code.</span><span class="sxs-lookup"><span data-stu-id="b2b04-163">Open the hosted Blazor WebAssembly app in VS Code.</span></span>

1. <span data-ttu-id="b2b04-164">Si aucune configuration de lancement n’est définie pour le projet, la notification suivante s’affiche.</span><span class="sxs-lookup"><span data-stu-id="b2b04-164">If there's no launch configuration set for the project, the following notification appears.</span></span> <span data-ttu-id="b2b04-165">Sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="b2b04-165">Select **Yes**.</span></span>

   ![Ajouter les ressources requises](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. <span data-ttu-id="b2b04-167">Dans la fenêtre de sélection, sélectionnez le projet *serveur* dans la solution hébergée.</span><span class="sxs-lookup"><span data-stu-id="b2b04-167">In the selection window, select the *Server* project within the hosted solution.</span></span>

<span data-ttu-id="b2b04-168">Un `launch.json` fichier est généré à l’aide de la configuration de lancement pour le lancement du débogueur.</span><span class="sxs-lookup"><span data-stu-id="b2b04-168">A `launch.json` file is generated with the launch configuration for launching the debugger.</span></span>

### <a name="attach-to-an-existing-debugging-session"></a><span data-ttu-id="b2b04-169">Attacher à une session de débogage existante</span><span class="sxs-lookup"><span data-stu-id="b2b04-169">Attach to an existing debugging session</span></span>

<span data-ttu-id="b2b04-170">Pour attacher une application en cours d’exécution Blazor , créez un `launch.json` fichier avec la configuration suivante :</span><span class="sxs-lookup"><span data-stu-id="b2b04-170">To attach to a running Blazor app, create a `launch.json` file with the following configuration:</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing Blazor WebAssembly Application"
}
```

> [!NOTE]
> <span data-ttu-id="b2b04-171">L’attachement à une session de débogage est pris en charge uniquement pour les applications autonomes.</span><span class="sxs-lookup"><span data-stu-id="b2b04-171">Attaching to a debugging session is only supported for standalone apps.</span></span> <span data-ttu-id="b2b04-172">Pour utiliser le débogage de pile complète, vous devez lancer l’application à partir de VS Code.</span><span class="sxs-lookup"><span data-stu-id="b2b04-172">To use full-stack debugging, you must launch the app from VS Code.</span></span>

### <a name="launch-configuration-options"></a><span data-ttu-id="b2b04-173">Lancer les options de configuration</span><span class="sxs-lookup"><span data-stu-id="b2b04-173">Launch configuration options</span></span>

<span data-ttu-id="b2b04-174">Les options de configuration de lancement suivantes sont prises en charge pour le `blazorwasm` type de débogage.</span><span class="sxs-lookup"><span data-stu-id="b2b04-174">The following launch configuration options are supported for the `blazorwasm` debug type.</span></span>

| <span data-ttu-id="b2b04-175">Option</span><span class="sxs-lookup"><span data-stu-id="b2b04-175">Option</span></span>    | <span data-ttu-id="b2b04-176">Description</span><span class="sxs-lookup"><span data-stu-id="b2b04-176">Description</span></span> |
| --------- | ----------- |
| `request` | <span data-ttu-id="b2b04-177">Utilisez `launch` pour lancer et attacher une session de débogage à une Blazor WebAssembly application ou `attach` pour attacher une session de débogage à une application déjà en cours d’exécution.</span><span class="sxs-lookup"><span data-stu-id="b2b04-177">Use `launch` to launch and attach a debugging session to a Blazor WebAssembly app or `attach` to attach a debugging session to an already-running app.</span></span> |
| `url`     | <span data-ttu-id="b2b04-178">URL à ouvrir dans le navigateur lors du débogage.</span><span class="sxs-lookup"><span data-stu-id="b2b04-178">The URL to open in the browser when debugging.</span></span> <span data-ttu-id="b2b04-179">La valeur par défaut est `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="b2b04-179">Defaults to `https://localhost:5001`.</span></span> |
| `browser` | <span data-ttu-id="b2b04-180">Navigateur à lancer pour la session de débogage.</span><span class="sxs-lookup"><span data-stu-id="b2b04-180">The browser to launch for the debugging session.</span></span> <span data-ttu-id="b2b04-181">A la valeur `edge` ou `chrome`.</span><span class="sxs-lookup"><span data-stu-id="b2b04-181">Set to `edge` or `chrome`.</span></span> <span data-ttu-id="b2b04-182">La valeur par défaut est `chrome`.</span><span class="sxs-lookup"><span data-stu-id="b2b04-182">Defaults to `chrome`.</span></span> |
| `trace`   | <span data-ttu-id="b2b04-183">Utilisé pour générer des journaux à partir du débogueur JS.</span><span class="sxs-lookup"><span data-stu-id="b2b04-183">Used to generate logs from the JS debugger.</span></span> <span data-ttu-id="b2b04-184">Définissez sur `true` pour générer des journaux.</span><span class="sxs-lookup"><span data-stu-id="b2b04-184">Set to `true` to generate logs.</span></span> |
| `hosted`  | <span data-ttu-id="b2b04-185">Doit avoir la valeur en `true` cas de lancement et de débogage d’une application hébergée Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="b2b04-185">Must be set to `true` if launching and debugging a hosted Blazor WebAssembly app.</span></span> |
| `webRoot` | <span data-ttu-id="b2b04-186">Spécifie le chemin d’accès absolu du serveur Web.</span><span class="sxs-lookup"><span data-stu-id="b2b04-186">Specifies the absolute path of the web server.</span></span> <span data-ttu-id="b2b04-187">Doit être défini si une application est servie à partir d’un sous-itinéraire.</span><span class="sxs-lookup"><span data-stu-id="b2b04-187">Should be set if an app is served from a sub-route.</span></span> |
| `timeout` | <span data-ttu-id="b2b04-188">Nombre de millisecondes d’attente de l’attachement de la session de débogage.</span><span class="sxs-lookup"><span data-stu-id="b2b04-188">The number of milliseconds to wait for the debugging session to attach.</span></span> <span data-ttu-id="b2b04-189">La valeur par défaut est 30 000 millisecondes (30 secondes).</span><span class="sxs-lookup"><span data-stu-id="b2b04-189">Defaults to 30,000 milliseconds (30 seconds).</span></span> |
| `program` | <span data-ttu-id="b2b04-190">Référence au fichier exécutable pour exécuter le serveur de l’application hébergée.</span><span class="sxs-lookup"><span data-stu-id="b2b04-190">A reference to the executable to run the server of the hosted app.</span></span> <span data-ttu-id="b2b04-191">Doit être défini si `hosted` a la valeur `true` .</span><span class="sxs-lookup"><span data-stu-id="b2b04-191">Must be set if `hosted` is `true`.</span></span> |
| `cwd`     | <span data-ttu-id="b2b04-192">Répertoire de travail dans lequel l’application doit être lancée.</span><span class="sxs-lookup"><span data-stu-id="b2b04-192">The working directory to launch the app under.</span></span> <span data-ttu-id="b2b04-193">Doit être défini si `hosted` a la valeur `true` .</span><span class="sxs-lookup"><span data-stu-id="b2b04-193">Must be set if `hosted` is `true`.</span></span> |
| `env`     | <span data-ttu-id="b2b04-194">Variables d’environnement à fournir au processus lancé.</span><span class="sxs-lookup"><span data-stu-id="b2b04-194">The environment variables to provide to the launched process.</span></span> <span data-ttu-id="b2b04-195">Applicable uniquement si `hosted` a la valeur `true` .</span><span class="sxs-lookup"><span data-stu-id="b2b04-195">Only applicable if `hosted` is set to `true`.</span></span> |

### <a name="example-launch-configurations"></a><span data-ttu-id="b2b04-196">Exemples de configurations de lancement</span><span class="sxs-lookup"><span data-stu-id="b2b04-196">Example launch configurations</span></span>

#### <a name="launch-and-debug-a-standalone-blazor-webassembly-app"></a><span data-ttu-id="b2b04-197">Lancer et déboguer une Blazor WebAssembly application autonome</span><span class="sxs-lookup"><span data-stu-id="b2b04-197">Launch and debug a standalone Blazor WebAssembly app</span></span>

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

#### <a name="attach-to-a-running-app-at-a-specified-url"></a><span data-ttu-id="b2b04-198">Attacher à une application en cours d’exécution à une URL spécifiée</span><span class="sxs-lookup"><span data-stu-id="b2b04-198">Attach to a running app at a specified URL</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

#### <a name="launch-and-debug-a-hosted-blazor-webassembly-app"></a><span data-ttu-id="b2b04-199">Lancer et déboguer une application hébergée Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="b2b04-199">Launch and debug a hosted Blazor WebAssembly app</span></span>

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug Hosted App",
  "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/MyHostedApp.Server.dll",
  "cwd": "${workspaceFolder}"
}
```

## <a name="debug-in-the-browser"></a><span data-ttu-id="b2b04-200">Déboguer dans le navigateur</span><span class="sxs-lookup"><span data-stu-id="b2b04-200">Debug in the browser</span></span>

1. <span data-ttu-id="b2b04-201">Exécutez une version Debug de l’application dans l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="b2b04-201">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="b2b04-202">Lancez un navigateur et accédez à l’URL de l’application (par exemple, `https://localhost:5001` ).</span><span class="sxs-lookup"><span data-stu-id="b2b04-202">Launch a browser and navigate to the app's URL (for example, `https://localhost:5001`).</span></span>

1. <span data-ttu-id="b2b04-203">Dans le navigateur, essayez de commencer le débogage à distance en appuyant sur <kbd>MAJ</kbd> + <kbd>ALT</kbd> + <kbd>D</kbd>.</span><span class="sxs-lookup"><span data-stu-id="b2b04-203">In the browser, attempt to commence remote debugging by pressing <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.</span></span>

   <span data-ttu-id="b2b04-204">Le navigateur doit s’exécuter avec le débogage à distance activé, qui n’est pas le paramètre par défaut.</span><span class="sxs-lookup"><span data-stu-id="b2b04-204">The browser must be running with remote debugging enabled, which isn't the default.</span></span> <span data-ttu-id="b2b04-205">Si le débogage distant est désactivé, une page d’erreur **Impossible de trouver un onglet de navigateur pouvant être débogué** est affichée avec des instructions pour lancer le navigateur avec le port de débogage ouvert.</span><span class="sxs-lookup"><span data-stu-id="b2b04-205">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is rendered with instructions for launching the browser with the debugging port open.</span></span> <span data-ttu-id="b2b04-206">Suivez les instructions de votre navigateur pour ouvrir une nouvelle fenêtre de navigateur.</span><span class="sxs-lookup"><span data-stu-id="b2b04-206">Follow the instructions for your browser, which opens a new browser window.</span></span> <span data-ttu-id="b2b04-207">Fermez la fenêtre de navigateur précédente.</span><span class="sxs-lookup"><span data-stu-id="b2b04-207">Close the previous browser window.</span></span>

1. <span data-ttu-id="b2b04-208">Une fois que le navigateur est en cours d’exécution avec le débogage distant activé, le raccourci clavier de débogage (<kbd>MAJ</kbd> + <kbd>ALT</kbd> + <kbd>D</kbd>) ouvre un nouvel onglet du débogueur.</span><span class="sxs-lookup"><span data-stu-id="b2b04-208">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut (<kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>) opens a new debugger tab.</span></span>

1. <span data-ttu-id="b2b04-209">Après un moment, l’onglet **sources** affiche une liste des assemblys .net de l’application dans le `file://` nœud.</span><span class="sxs-lookup"><span data-stu-id="b2b04-209">After a moment, the **Sources** tab shows a list of the app's .NET assemblies within the `file://` node.</span></span>

1. <span data-ttu-id="b2b04-210">Dans le code du composant ( `.razor` fichiers) et les fichiers de code C# ( `.cs` ), les points d’arrêt que vous définissez sont atteints lors de l’exécution du code.</span><span class="sxs-lookup"><span data-stu-id="b2b04-210">In component code (`.razor` files) and C# code files (`.cs`), breakpoints that you set are hit when code executes.</span></span> <span data-ttu-id="b2b04-211">Une fois le point d’arrêt atteint, une seule étape (<kbd>F10</kbd>) passe par l’exécution du code ou de la reprise (<kbd>F8</kbd>).</span><span class="sxs-lookup"><span data-stu-id="b2b04-211">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

Blazor<span data-ttu-id="b2b04-212">fournit un proxy de débogage qui implémente le [protocole chrome devtools](https://chromedevtools.github.io/devtools-protocol/) et augmente le protocole avec. Informations spécifiques à .net.</span><span class="sxs-lookup"><span data-stu-id="b2b04-212"> provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="b2b04-213">Quand le raccourci clavier de débogage est enfoncé, Blazor pointe le devtools chrome au niveau du proxy.</span><span class="sxs-lookup"><span data-stu-id="b2b04-213">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="b2b04-214">Le proxy se connecte à la fenêtre du navigateur que vous cherchez à déboguer (par conséquent, il est nécessaire d’activer le débogage distant).</span><span class="sxs-lookup"><span data-stu-id="b2b04-214">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="b2b04-215">Mappages des sources du navigateur</span><span class="sxs-lookup"><span data-stu-id="b2b04-215">Browser source maps</span></span>

<span data-ttu-id="b2b04-216">Les mappages de source de navigateur permettent au navigateur de mapper les fichiers compilés à leurs fichiers sources d’origine et sont couramment utilisés pour le débogage côté client.</span><span class="sxs-lookup"><span data-stu-id="b2b04-216">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="b2b04-217">Toutefois, Blazor ne mappe actuellement pas C# directement à JavaScript/WASM.</span><span class="sxs-lookup"><span data-stu-id="b2b04-217">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="b2b04-218">Au lieu de cela, Blazor fait l’interprétation du langage intermédiaire dans le navigateur, les mappages de source ne sont donc pas pertinents.</span><span class="sxs-lookup"><span data-stu-id="b2b04-218">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="b2b04-219">Résoudre les problèmes</span><span class="sxs-lookup"><span data-stu-id="b2b04-219">Troubleshoot</span></span>

<span data-ttu-id="b2b04-220">Si vous rencontrez des erreurs, les conseils suivants peuvent vous aider :</span><span class="sxs-lookup"><span data-stu-id="b2b04-220">If you're running into errors, the following tips may help:</span></span>

* <span data-ttu-id="b2b04-221">Dans l’onglet **débogueur** , ouvrez les outils de développement de votre navigateur.</span><span class="sxs-lookup"><span data-stu-id="b2b04-221">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="b2b04-222">Dans la console, exécutez `localStorage.clear()` pour supprimer tous les points d’arrêt.</span><span class="sxs-lookup"><span data-stu-id="b2b04-222">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
* <span data-ttu-id="b2b04-223">Confirmez que vous avez installé et approuvé le certificat de développement ASP.NET Core HTTPs.</span><span class="sxs-lookup"><span data-stu-id="b2b04-223">Confirm that you've installed and trusted the ASP.NET Core HTTPS development certificate.</span></span> <span data-ttu-id="b2b04-224">Pour plus d’informations, consultez <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span><span class="sxs-lookup"><span data-stu-id="b2b04-224">For more information, see <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span></span>
* <span data-ttu-id="b2b04-225">Visual Studio requiert l’option **activer le débogage JavaScript pour ASP.net (chrome, Edge et IE)** dans **Outils**  >  **options**  >  **débogage**  >  **général**.</span><span class="sxs-lookup"><span data-stu-id="b2b04-225">Visual Studio requires the **Enable JavaScript debugging for ASP.NET (Chrome, Edge and IE)** option in **Tools** > **Options** > **Debugging** > **General**.</span></span> <span data-ttu-id="b2b04-226">Il s’agit du paramètre par défaut pour Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="b2b04-226">This is the default setting for Visual Studio.</span></span> <span data-ttu-id="b2b04-227">Si le débogage ne fonctionne pas, vérifiez que l’option est sélectionnée.</span><span class="sxs-lookup"><span data-stu-id="b2b04-227">If debugging isn't working, confirm that the option is selected.</span></span>
