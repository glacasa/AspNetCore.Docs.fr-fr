---
<span data-ttu-id="f93bd-101">titre : « Debug ASP.NET Core Blazor Webassembly » auteur : guardrex Description : « Découvrez comment déboguer des Blazor applications. »</span><span class="sxs-lookup"><span data-stu-id="f93bd-101">title: 'Debug ASP.NET Core Blazor WebAssembly' author: guardrex description: 'Learn how to debug Blazor apps.'</span></span>
<span data-ttu-id="f93bd-102">monikerRange : ' >= aspnetcore-3,1 'ms. Author : Riande ms. Custom : MVC ms. Date : 05/31/2020 No-Loc :</span><span class="sxs-lookup"><span data-stu-id="f93bd-102">monikerRange: '>= aspnetcore-3.1' ms.author: riande ms.custom: mvc ms.date: 05/31/2020 no-loc:</span></span>
- <span data-ttu-id="f93bd-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f93bd-103">'Blazor'</span></span>
- <span data-ttu-id="f93bd-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f93bd-104">'Identity'</span></span>
- <span data-ttu-id="f93bd-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f93bd-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="f93bd-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f93bd-106">'Razor'</span></span>
- <span data-ttu-id="f93bd-107">' SignalR 'UID : éblouissant/débogage</span><span class="sxs-lookup"><span data-stu-id="f93bd-107">'SignalR' uid: blazor/debug</span></span>

---
# <a name="debug-aspnet-core-blazor-webassembly"></a><span data-ttu-id="f93bd-108">Déboguer ASP.NET Core Blazor Webassembly</span><span class="sxs-lookup"><span data-stu-id="f93bd-108">Debug ASP.NET Core Blazor WebAssembly</span></span>

[<span data-ttu-id="f93bd-109">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="f93bd-109">Daniel Roth</span></span>](https://github.com/danroth27)

Blazor<span data-ttu-id="f93bd-110">Les applications webassembly peuvent être déboguées à l’aide des outils de développement de navigateur dans les navigateurs basés sur le chrome (Edge/chrome).</span><span class="sxs-lookup"><span data-stu-id="f93bd-110"> WebAssembly apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span> <span data-ttu-id="f93bd-111">Vous pouvez également déboguer votre application à l’aide de Visual Studio ou Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="f93bd-111">Alternatively, you can debug your app using Visual Studio or Visual Studio Code.</span></span>

<span data-ttu-id="f93bd-112">Les scénarios disponibles sont les suivants :</span><span class="sxs-lookup"><span data-stu-id="f93bd-112">Available scenarios include:</span></span>

* <span data-ttu-id="f93bd-113">Définir et supprimer des points d’arrêt.</span><span class="sxs-lookup"><span data-stu-id="f93bd-113">Set and remove breakpoints.</span></span>
* <span data-ttu-id="f93bd-114">Exécutez l’application avec prise en charge du débogage dans Visual Studio et Visual Studio Code (prise en charge de<kbd>F5</kbd> ).</span><span class="sxs-lookup"><span data-stu-id="f93bd-114">Run the app with debugging support in Visual Studio and Visual Studio Code (<kbd>F5</kbd> support).</span></span>
* <span data-ttu-id="f93bd-115">Une étape (<kbd>F10</kbd>) à l’aide du code.</span><span class="sxs-lookup"><span data-stu-id="f93bd-115">Single-step (<kbd>F10</kbd>) through the code.</span></span>
* <span data-ttu-id="f93bd-116">Reprendre l’exécution du code avec <kbd>F8</kbd> dans un navigateur ou <kbd>F5</kbd> dans Visual Studio ou Visual Studio code.</span><span class="sxs-lookup"><span data-stu-id="f93bd-116">Resume code execution with <kbd>F8</kbd> in a browser or <kbd>F5</kbd> in Visual Studio or Visual Studio Code.</span></span>
* <span data-ttu-id="f93bd-117">Dans l' *affichage des variables locales,* observez les valeurs des variables locales.</span><span class="sxs-lookup"><span data-stu-id="f93bd-117">In the *Locals* display, observe the values of local variables.</span></span>
* <span data-ttu-id="f93bd-118">Consultez la pile des appels, y compris les chaînes d’appel qui passent de JavaScript à .NET et de .NET à JavaScript.</span><span class="sxs-lookup"><span data-stu-id="f93bd-118">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="f93bd-119">Pour le moment, vous *ne pouvez pas*:</span><span class="sxs-lookup"><span data-stu-id="f93bd-119">For now, you *can't*:</span></span>

* <span data-ttu-id="f93bd-120">Arrêt sur les exceptions non gérées.</span><span class="sxs-lookup"><span data-stu-id="f93bd-120">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="f93bd-121">Atteindre les points d’arrêt pendant le démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="f93bd-121">Hit breakpoints during app startup.</span></span>

<span data-ttu-id="f93bd-122">Nous continuerons à améliorer l’expérience de débogage dans les versions à venir.</span><span class="sxs-lookup"><span data-stu-id="f93bd-122">We will continue to improve the debugging experience in upcoming releases.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f93bd-123">Prérequis</span><span class="sxs-lookup"><span data-stu-id="f93bd-123">Prerequisites</span></span>

<span data-ttu-id="f93bd-124">Le débogage requiert l’un des navigateurs suivants :</span><span class="sxs-lookup"><span data-stu-id="f93bd-124">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="f93bd-125">Microsoft Edge (version 80 ou ultérieure)</span><span class="sxs-lookup"><span data-stu-id="f93bd-125">Microsoft Edge (version 80 or later)</span></span>
* <span data-ttu-id="f93bd-126">Google Chrome (version 70 ou ultérieure)</span><span class="sxs-lookup"><span data-stu-id="f93bd-126">Google Chrome (version 70 or later)</span></span>

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a><span data-ttu-id="f93bd-127">Activer le débogage pour Visual Studio et Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f93bd-127">Enable debugging for Visual Studio and Visual Studio Code</span></span>

<span data-ttu-id="f93bd-128">Pour activer le débogage pour une Blazor application Webassembly existante, mettez à jour le fichier *launchSettings. JSON* dans le projet de démarrage pour inclure la `inspectUri` propriété suivante dans chaque profil de lancement :</span><span class="sxs-lookup"><span data-stu-id="f93bd-128">To enable debugging for an existing Blazor WebAssembly app, update the *launchSettings.json* file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="f93bd-129">Une fois mis à jour, le fichier *launchSettings. JSON* doit ressembler à l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="f93bd-129">Once updated, the *launchSettings.json* file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="f93bd-130">La `inspectUri` propriété :</span><span class="sxs-lookup"><span data-stu-id="f93bd-130">The `inspectUri` property:</span></span>

* <span data-ttu-id="f93bd-131">Permet à l’IDE de détecter que l’application est une Blazor application Webassembly.</span><span class="sxs-lookup"><span data-stu-id="f93bd-131">Enables the IDE to detect that the app is a Blazor WebAssembly app.</span></span>
* <span data-ttu-id="f93bd-132">Indique à l’infrastructure de débogage de script de se connecter au navigateur via le Blazor proxy de débogage de.</span><span class="sxs-lookup"><span data-stu-id="f93bd-132">Instructs the script debugging infrastructure to connect to the browser through Blazor's debugging proxy.</span></span>

<span data-ttu-id="f93bd-133">Les valeurs d’espace réservé pour le protocole WebSockets ( `wsProtocol` ), l’hôte ( `url.hostname` ), le port ( `url.port` ) et l’URI de l’inspecteur sur le navigateur lancé ( `browserInspectUri` ) sont fournies par l’infrastructure.</span><span class="sxs-lookup"><span data-stu-id="f93bd-133">The placeholder values for the WebSockets protocol (`wsProtocol`), host (`url.hostname`), port (`url.port`), and inspector URI on the launched browser (`browserInspectUri`) are provided by the framework.</span></span>

## <a name="visual-studio"></a><span data-ttu-id="f93bd-134">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f93bd-134">Visual Studio</span></span>

<span data-ttu-id="f93bd-135">Pour déboguer une Blazor application Webassembly dans Visual Studio :</span><span class="sxs-lookup"><span data-stu-id="f93bd-135">To debug a Blazor WebAssembly app in Visual Studio:</span></span>

1. <span data-ttu-id="f93bd-136">Créez une nouvelle ASP.NET Core Blazor application Webassembly hébergée.</span><span class="sxs-lookup"><span data-stu-id="f93bd-136">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="f93bd-137">Appuyez sur <kbd>F5</kbd> pour exécuter l’application dans le débogueur.</span><span class="sxs-lookup"><span data-stu-id="f93bd-137">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>
1. <span data-ttu-id="f93bd-138">Définissez un point d’arrêt dans *Counter. Razor* dans la `IncrementCount` méthode.</span><span class="sxs-lookup"><span data-stu-id="f93bd-138">Set a breakpoint in *Counter.razor* in the `IncrementCount` method.</span></span>
1. <span data-ttu-id="f93bd-139">Accédez à l’onglet **compteur** et sélectionnez le bouton pour atteindre le point d’arrêt :</span><span class="sxs-lookup"><span data-stu-id="f93bd-139">Browse to the **Counter** tab and select the button to hit the breakpoint:</span></span>

   ![Compteur de débogage](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. <span data-ttu-id="f93bd-141">Examinez la valeur du `currentCount` champ dans la fenêtre variables locales :</span><span class="sxs-lookup"><span data-stu-id="f93bd-141">Check out the value of the `currentCount` field in the locals window:</span></span>

   ![Afficher les variables locales](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. <span data-ttu-id="f93bd-143">Appuyez sur <kbd>F5</kbd> pour poursuivre l’exécution.</span><span class="sxs-lookup"><span data-stu-id="f93bd-143">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="f93bd-144">Lors du débogage de votre Blazor application Webassembly, vous pouvez également déboguer votre code serveur :</span><span class="sxs-lookup"><span data-stu-id="f93bd-144">While debugging your Blazor WebAssembly app, you can also debug your server code:</span></span>

1. <span data-ttu-id="f93bd-145">Définissez un point d’arrêt dans la page *fetchData. Razor* dans <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="f93bd-145">Set a breakpoint in the *FetchData.razor* page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="f93bd-146">Définissez un point d’arrêt dans la `WeatherForecastController` `Get` méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="f93bd-146">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="f93bd-147">Accédez à l’onglet **extraire les données** pour atteindre le premier point d’arrêt dans le `FetchData` composant juste avant qu’il envoie une requête HTTP au serveur :</span><span class="sxs-lookup"><span data-stu-id="f93bd-147">Browse to the **Fetch Data** tab to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server:</span></span>

   ![Déboguer extraire des données](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. <span data-ttu-id="f93bd-149">Appuyez sur <kbd>F5</kbd> pour poursuivre l’exécution, puis appuyez sur le point d’arrêt sur le serveur dans le `WeatherForecastController` :</span><span class="sxs-lookup"><span data-stu-id="f93bd-149">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`:</span></span>

   ![Serveur de débogage](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. <span data-ttu-id="f93bd-151">Appuyez de nouveau sur <kbd>F5</kbd> pour permettre à l’exécution de se poursuivre et consultez le tableau prévisions météo rendu.</span><span class="sxs-lookup"><span data-stu-id="f93bd-151">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered.</span></span>

<a id="vscode"></a>

## <a name="visual-studio-code"></a><span data-ttu-id="f93bd-152">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f93bd-152">Visual Studio Code</span></span>

<span data-ttu-id="f93bd-153">Pour déboguer une Blazor application Webassembly dans Visual Studio code :</span><span class="sxs-lookup"><span data-stu-id="f93bd-153">To debug a Blazor WebAssembly app in Visual Studio Code:</span></span>
 
<span data-ttu-id="f93bd-154">Installez l' [extension C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) et l’extension de [débogueur JavaScript (nocturne)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) avec la `debug.javascript.usePreview` valeur `true` .</span><span class="sxs-lookup"><span data-stu-id="f93bd-154">Install the [C# extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

![Extensions](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-extensions.png)

![Débogueur JS preview](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-js-use-preview.png)

### <a name="debug-standalone-blazor-webassembly"></a><span data-ttu-id="f93bd-157">Déboguer le Blazor Webassembly autonome</span><span class="sxs-lookup"><span data-stu-id="f93bd-157">Debug standalone Blazor WebAssembly</span></span>

1. <span data-ttu-id="f93bd-158">Ouvrez l' Blazor application Webassembly autonome dans vs code.</span><span class="sxs-lookup"><span data-stu-id="f93bd-158">Open the standalone Blazor WebAssembly app in VS Code.</span></span>

   <span data-ttu-id="f93bd-159">Si vous recevez la notification suivante indiquant qu’une configuration supplémentaire est requise pour activer le débogage :</span><span class="sxs-lookup"><span data-stu-id="f93bd-159">If you receive the following notification that additional setup is required to enable debugging:</span></span>
   
   * <span data-ttu-id="f93bd-160">Vérifiez que vous avez installé les extensions appropriées.</span><span class="sxs-lookup"><span data-stu-id="f93bd-160">Confirm that you have the correct extensions installed.</span></span>
   * <span data-ttu-id="f93bd-161">Confirmez que le débogage de l’aperçu JavaScript est activé.</span><span class="sxs-lookup"><span data-stu-id="f93bd-161">Confirm that JavaScript preview debugging is enabled.</span></span>
   * <span data-ttu-id="f93bd-162">Rechargez la fenêtre.</span><span class="sxs-lookup"><span data-stu-id="f93bd-162">Reload the window.</span></span>

   ![Configuration supplémentaire requise](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

1. <span data-ttu-id="f93bd-164">Démarrez le débogage à l’aide du raccourci clavier <kbd>F5</kbd> ou de l’élément de menu.</span><span class="sxs-lookup"><span data-stu-id="f93bd-164">Start debugging using the <kbd>F5</kbd> keyboard shortcut or the menu item.</span></span>

1. <span data-ttu-id="f93bd-165">Quand vous y êtes invité, sélectionnez l’option de \*\* Blazor débogage webassembly\*\* pour démarrer le débogage.</span><span class="sxs-lookup"><span data-stu-id="f93bd-165">When prompted, select the **Blazor WebAssembly Debug** option to start debugging.</span></span>

   ![Liste des options de débogage disponibles](index/_static/blazor-vscode-debugtypes.png)

1. <span data-ttu-id="f93bd-167">L’application autonome est lancée et un navigateur de débogage est ouvert.</span><span class="sxs-lookup"><span data-stu-id="f93bd-167">The standalone app is launched, and a debugging browser is opened.</span></span>

1. <span data-ttu-id="f93bd-168">Définissez un point d’arrêt dans la `IncrementCount` méthode du `Counter` composant, puis sélectionnez le bouton pour atteindre le point d’arrêt :</span><span class="sxs-lookup"><span data-stu-id="f93bd-168">Set a breakpoint in the `IncrementCount` method in the `Counter` component and then select the button to hit the breakpoint:</span></span>

   ![Déboguer le compteur dans VS Code](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

### <a name="debug-hosted-blazor-webassembly"></a><span data-ttu-id="f93bd-170">Déboguer le Blazor Webassembly hébergé</span><span class="sxs-lookup"><span data-stu-id="f93bd-170">Debug hosted Blazor WebAssembly</span></span>

1. <span data-ttu-id="f93bd-171">Ouvrez l' Blazor application Webassembly hébergée dans vs code.</span><span class="sxs-lookup"><span data-stu-id="f93bd-171">Open the hosted Blazor WebAssembly app in VS Code.</span></span>

1. <span data-ttu-id="f93bd-172">Si aucune configuration de lancement n’est définie pour le projet, la notification suivante s’affiche.</span><span class="sxs-lookup"><span data-stu-id="f93bd-172">If there's no launch configuration set for the project, the following notification appears.</span></span> <span data-ttu-id="f93bd-173">Sélectionnez **Oui**.</span><span class="sxs-lookup"><span data-stu-id="f93bd-173">Select **Yes**.</span></span>

   ![Ajouter les ressources requises](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. <span data-ttu-id="f93bd-175">Dans la fenêtre de sélection, sélectionnez le projet *serveur* dans la solution hébergée.</span><span class="sxs-lookup"><span data-stu-id="f93bd-175">In the selection window, select the *Server* project within the hosted solution.</span></span>

<span data-ttu-id="f93bd-176">Un fichier *Launch. JSON* est généré avec la configuration de lancement pour le lancement du débogueur.</span><span class="sxs-lookup"><span data-stu-id="f93bd-176">A *launch.json* file is generated with the launch configuration for launching the debugger.</span></span>

### <a name="attach-to-an-existing-debugging-session"></a><span data-ttu-id="f93bd-177">Attacher à une session de débogage existante</span><span class="sxs-lookup"><span data-stu-id="f93bd-177">Attach to an existing debugging session</span></span>

<span data-ttu-id="f93bd-178">Pour attacher une application en cours d’exécution Blazor , créez un fichier *Launch. JSON* avec la configuration suivante :</span><span class="sxs-lookup"><span data-stu-id="f93bd-178">To attach to a running Blazor app, create a *launch.json* file with the following configuration:</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing Blazor WebAssembly Application"
}
```

> [!NOTE]
> <span data-ttu-id="f93bd-179">L’attachement à une session de débogage est pris en charge uniquement pour les applications autonomes.</span><span class="sxs-lookup"><span data-stu-id="f93bd-179">Attaching to a debugging session is only supported for standalone apps.</span></span> <span data-ttu-id="f93bd-180">Pour utiliser le débogage de pile complète, vous devez lancer l’application à partir de VS Code.</span><span class="sxs-lookup"><span data-stu-id="f93bd-180">To use full-stack debugging, you must launch the app from VS Code.</span></span>

### <a name="launch-configuration-options"></a><span data-ttu-id="f93bd-181">Lancer les options de configuration</span><span class="sxs-lookup"><span data-stu-id="f93bd-181">Launch configuration options</span></span>

<span data-ttu-id="f93bd-182">Les options de configuration de lancement suivantes sont prises en charge pour le `blazorwasm` type de débogage.</span><span class="sxs-lookup"><span data-stu-id="f93bd-182">The following launch configuration options are supported for the `blazorwasm` debug type.</span></span>

| <span data-ttu-id="f93bd-183">Option</span><span class="sxs-lookup"><span data-stu-id="f93bd-183">Option</span></span>    | <span data-ttu-id="f93bd-184">Description</span><span class="sxs-lookup"><span data-stu-id="f93bd-184">Description</span></span> |
| --------- | ----------- |
| `request` | <span data-ttu-id="f93bd-185">Utilisez `launch` pour lancer et attacher une session de débogage à une Blazor application webassembly ou `attach` pour attacher une session de débogage à une application déjà en cours d’exécution.</span><span class="sxs-lookup"><span data-stu-id="f93bd-185">Use `launch` to launch and attach a debugging session to a Blazor WebAssembly app or `attach` to attach a debugging session to an already-running app.</span></span> |
| `url`     | <span data-ttu-id="f93bd-186">URL à ouvrir dans le navigateur lors du débogage.</span><span class="sxs-lookup"><span data-stu-id="f93bd-186">The URL to open in the browser when debugging.</span></span> <span data-ttu-id="f93bd-187">La valeur par défaut est `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="f93bd-187">Defaults to `https://localhost:5001`.</span></span> |
| `browser` | <span data-ttu-id="f93bd-188">Navigateur à lancer pour la session de débogage.</span><span class="sxs-lookup"><span data-stu-id="f93bd-188">The browser to launch for the debugging session.</span></span> <span data-ttu-id="f93bd-189">A la valeur `edge` ou `chrome`.</span><span class="sxs-lookup"><span data-stu-id="f93bd-189">Set to `edge` or `chrome`.</span></span> <span data-ttu-id="f93bd-190">La valeur par défaut est `chrome`.</span><span class="sxs-lookup"><span data-stu-id="f93bd-190">Defaults to `chrome`.</span></span> |
| `trace`   | <span data-ttu-id="f93bd-191">Utilisé pour générer des journaux à partir du débogueur JS.</span><span class="sxs-lookup"><span data-stu-id="f93bd-191">Used to generate logs from the JS debugger.</span></span> <span data-ttu-id="f93bd-192">Définissez sur `true` pour générer des journaux.</span><span class="sxs-lookup"><span data-stu-id="f93bd-192">Set to `true` to generate logs.</span></span> |
| `hosted`  | <span data-ttu-id="f93bd-193">Doit avoir la valeur `true` si vous lancez et déboguez une Blazor application webassembly hébergée.</span><span class="sxs-lookup"><span data-stu-id="f93bd-193">Must be set to `true` if launching and debugging a hosted Blazor WebAssembly app.</span></span> |
| `webRoot` | <span data-ttu-id="f93bd-194">Spécifie le chemin d’accès absolu du serveur Web.</span><span class="sxs-lookup"><span data-stu-id="f93bd-194">Specifies the absolute path of the web server.</span></span> <span data-ttu-id="f93bd-195">Doit être défini si une application est servie à partir d’un sous-itinéraire.</span><span class="sxs-lookup"><span data-stu-id="f93bd-195">Should be set if an app is served from a sub-route.</span></span> |
| `timeout` | <span data-ttu-id="f93bd-196">Nombre de millisecondes d’attente de l’attachement de la session de débogage.</span><span class="sxs-lookup"><span data-stu-id="f93bd-196">The number of milliseconds to wait for the debugging session to attach.</span></span> <span data-ttu-id="f93bd-197">La valeur par défaut est 30 000 millisecondes (30 secondes).</span><span class="sxs-lookup"><span data-stu-id="f93bd-197">Defaults to 30,000 milliseconds (30 seconds).</span></span> |
| `program` | <span data-ttu-id="f93bd-198">Référence au fichier exécutable pour exécuter le serveur de l’application hébergée.</span><span class="sxs-lookup"><span data-stu-id="f93bd-198">A reference to the executable to run the server of the hosted app.</span></span> <span data-ttu-id="f93bd-199">Doit être défini si `hosted` a la valeur `true` .</span><span class="sxs-lookup"><span data-stu-id="f93bd-199">Must be set if `hosted` is `true`.</span></span> |
| `cwd`     | <span data-ttu-id="f93bd-200">Répertoire de travail dans lequel l’application doit être lancée.</span><span class="sxs-lookup"><span data-stu-id="f93bd-200">The working directory to launch the app under.</span></span> <span data-ttu-id="f93bd-201">Doit être défini si `hosted` a la valeur `true` .</span><span class="sxs-lookup"><span data-stu-id="f93bd-201">Must be set if `hosted` is `true`.</span></span> |
| `env`     | <span data-ttu-id="f93bd-202">Variables d’environnement à fournir au processus lancé.</span><span class="sxs-lookup"><span data-stu-id="f93bd-202">The environment variables to provide to the launched process.</span></span> <span data-ttu-id="f93bd-203">Applicable uniquement si `hosted` a la valeur `true` .</span><span class="sxs-lookup"><span data-stu-id="f93bd-203">Only applicable if `hosted` is set to `true`.</span></span> |

### <a name="example-launch-configurations"></a><span data-ttu-id="f93bd-204">Exemples de configurations de lancement</span><span class="sxs-lookup"><span data-stu-id="f93bd-204">Example launch configurations</span></span>

#### <a name="launch-and-debug-a-standalone-blazor-webassembly-app"></a><span data-ttu-id="f93bd-205">Lancer et déboguer une Blazor application Webassembly autonome</span><span class="sxs-lookup"><span data-stu-id="f93bd-205">Launch and debug a standalone Blazor WebAssembly app</span></span>

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

#### <a name="attach-to-a-running-app-at-a-specified-url"></a><span data-ttu-id="f93bd-206">Attacher à une application en cours d’exécution à une URL spécifiée</span><span class="sxs-lookup"><span data-stu-id="f93bd-206">Attach to a running app at a specified URL</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

#### <a name="launch-and-debug-a-hosted-blazor-webassembly-app"></a><span data-ttu-id="f93bd-207">Lancer et déboguer une Blazor application Webassembly hébergée</span><span class="sxs-lookup"><span data-stu-id="f93bd-207">Launch and debug a hosted Blazor WebAssembly app</span></span>

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug Hosted App",
  "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/MyHostedApp.Server.dll",
  "cwd": "${workspaceFolder}"
}
```

## <a name="debug-in-the-browser"></a><span data-ttu-id="f93bd-208">Déboguer dans le navigateur</span><span class="sxs-lookup"><span data-stu-id="f93bd-208">Debug in the browser</span></span>

1. <span data-ttu-id="f93bd-209">Exécutez une version Debug de l’application dans l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="f93bd-209">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="f93bd-210">Appuyez sur <kbd>MAJ</kbd> + <kbd>ALT</kbd> + <kbd>D</kbd>.</span><span class="sxs-lookup"><span data-stu-id="f93bd-210">Press <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.</span></span>

1. <span data-ttu-id="f93bd-211">Le navigateur doit être exécuté avec le débogage distant activé.</span><span class="sxs-lookup"><span data-stu-id="f93bd-211">The browser must be run with remote debugging enabled.</span></span> <span data-ttu-id="f93bd-212">Si le débogage distant est désactivé, une page d’erreur **Impossible de trouver un onglet de navigateur pouvant être débogué** est générée.</span><span class="sxs-lookup"><span data-stu-id="f93bd-212">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is generated.</span></span> <span data-ttu-id="f93bd-213">La page d’erreur contient des instructions pour l’exécution du navigateur avec le port de débogage ouvert, afin que le Blazor proxy de débogage puisse se connecter à l’application.</span><span class="sxs-lookup"><span data-stu-id="f93bd-213">The error page contains instructions for running the browser with the debugging port open so that the Blazor debugging proxy can connect to the app.</span></span> <span data-ttu-id="f93bd-214">*Fermez toutes les instances de navigateur* et redémarrez le navigateur comme indiqué.</span><span class="sxs-lookup"><span data-stu-id="f93bd-214">*Close all browser instances* and restart the browser as instructed.</span></span>

<span data-ttu-id="f93bd-215">Une fois que le navigateur est en cours d’exécution avec le débogage distant activé, le raccourci clavier de débogage ouvre un nouvel onglet du débogueur. Après un moment, l’onglet **sources** affiche une liste des assemblys .net dans l’application.</span><span class="sxs-lookup"><span data-stu-id="f93bd-215">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut opens a new debugger tab. After a moment, the **Sources** tab shows a list of the .NET assemblies in the app.</span></span> <span data-ttu-id="f93bd-216">Développez chaque assembly et recherchez les fichiers sources *. cs* / *. Razor* disponibles pour le débogage.</span><span class="sxs-lookup"><span data-stu-id="f93bd-216">Expand each assembly and find the *.cs*/*.razor* source files available for debugging.</span></span> <span data-ttu-id="f93bd-217">Définissez des points d’arrêt, revenez à l’onglet de l’application, et les points d’arrêt sont atteints lorsque le code s’exécute.</span><span class="sxs-lookup"><span data-stu-id="f93bd-217">Set breakpoints, switch back to the app's tab, and the breakpoints are hit when the code executes.</span></span> <span data-ttu-id="f93bd-218">Une fois le point d’arrêt atteint, une seule étape (<kbd>F10</kbd>) passe par l’exécution du code ou de la reprise (<kbd>F8</kbd>).</span><span class="sxs-lookup"><span data-stu-id="f93bd-218">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

Blazor<span data-ttu-id="f93bd-219">fournit un proxy de débogage qui implémente le [protocole chrome devtools](https://chromedevtools.github.io/devtools-protocol/) et augmente le protocole avec. Informations spécifiques à .net.</span><span class="sxs-lookup"><span data-stu-id="f93bd-219"> provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="f93bd-220">Quand le raccourci clavier de débogage est enfoncé, Blazor pointe le devtools chrome au niveau du proxy.</span><span class="sxs-lookup"><span data-stu-id="f93bd-220">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="f93bd-221">Le proxy se connecte à la fenêtre du navigateur que vous cherchez à déboguer (par conséquent, il est nécessaire d’activer le débogage distant).</span><span class="sxs-lookup"><span data-stu-id="f93bd-221">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="f93bd-222">Mappages des sources du navigateur</span><span class="sxs-lookup"><span data-stu-id="f93bd-222">Browser source maps</span></span>

<span data-ttu-id="f93bd-223">Les mappages de source de navigateur permettent au navigateur de mapper les fichiers compilés à leurs fichiers sources d’origine et sont couramment utilisés pour le débogage côté client.</span><span class="sxs-lookup"><span data-stu-id="f93bd-223">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="f93bd-224">Toutefois, Blazor ne mappe actuellement pas C# directement à JavaScript/WASM.</span><span class="sxs-lookup"><span data-stu-id="f93bd-224">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="f93bd-225">Au lieu de cela, Blazor fait l’interprétation du langage intermédiaire dans le navigateur, les mappages de source ne sont donc pas pertinents.</span><span class="sxs-lookup"><span data-stu-id="f93bd-225">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="f93bd-226">Dépanner</span><span class="sxs-lookup"><span data-stu-id="f93bd-226">Troubleshoot</span></span>

<span data-ttu-id="f93bd-227">Si vous rencontrez des erreurs, les conseils suivants peuvent vous aider :</span><span class="sxs-lookup"><span data-stu-id="f93bd-227">If you're running into errors, the following tips may help:</span></span>

* <span data-ttu-id="f93bd-228">Dans l’onglet **débogueur** , ouvrez les outils de développement de votre navigateur.</span><span class="sxs-lookup"><span data-stu-id="f93bd-228">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="f93bd-229">Dans la console, exécutez `localStorage.clear()` pour supprimer tous les points d’arrêt.</span><span class="sxs-lookup"><span data-stu-id="f93bd-229">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
* <span data-ttu-id="f93bd-230">Confirmez que vous avez installé et approuvé le certificat de développement ASP.NET Core HTTPs.</span><span class="sxs-lookup"><span data-stu-id="f93bd-230">Confirm that you've installed and trusted the ASP.NET Core HTTPS development certificate.</span></span> <span data-ttu-id="f93bd-231">Pour plus d'informations, consultez <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span><span class="sxs-lookup"><span data-stu-id="f93bd-231">For more information, see <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span></span>
