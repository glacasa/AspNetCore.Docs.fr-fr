---
<span data-ttu-id="260cd-101">titre : « journalisation et diagnostics dans ASP.NET Core SignalR » Auteur : Description : « Découvrez Comment collecter des diagnostics à partir de votre SignalR application ASP.net core. »</span><span class="sxs-lookup"><span data-stu-id="260cd-101">title: 'Logging and diagnostics in ASP.NET Core SignalR' author: description: 'Learn how to gather diagnostics from your ASP.NET Core SignalR app.'</span></span>
<span data-ttu-id="260cd-102">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="260cd-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="260cd-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="260cd-103">'Blazor'</span></span>
- <span data-ttu-id="260cd-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="260cd-104">'Identity'</span></span>
- <span data-ttu-id="260cd-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="260cd-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="260cd-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="260cd-106">'Razor'</span></span>
- <span data-ttu-id="260cd-107">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="260cd-107">'SignalR' uid:</span></span> 

---
# <a name="logging-and-diagnostics-in-aspnet-core-signalr"></a><span data-ttu-id="260cd-108">Journalisation et diagnostics dans ASP.NET CoreSignalR</span><span class="sxs-lookup"><span data-stu-id="260cd-108">Logging and diagnostics in ASP.NET Core SignalR</span></span>

<span data-ttu-id="260cd-109">Par [Andrew Stanton-infirmière](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="260cd-109">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="260cd-110">Cet article fournit des conseils pour la collecte de diagnostics à partir de votre SignalR application ASP.net Core pour aider à résoudre les problèmes.</span><span class="sxs-lookup"><span data-stu-id="260cd-110">This article provides guidance for gathering diagnostics from your ASP.NET Core SignalR app to help troubleshoot issues.</span></span>

## <a name="server-side-logging"></a><span data-ttu-id="260cd-111">Journalisation côté serveur</span><span class="sxs-lookup"><span data-stu-id="260cd-111">Server-side logging</span></span>

> [!WARNING]
> <span data-ttu-id="260cd-112">Les journaux côté serveur peuvent contenir des informations sensibles de votre application.</span><span class="sxs-lookup"><span data-stu-id="260cd-112">Server-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="260cd-113">**Ne jamais** poster des journaux bruts à partir d’applications de production vers des forums publics tels que github.</span><span class="sxs-lookup"><span data-stu-id="260cd-113">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="260cd-114">Étant donné que SignalR fait partie de ASP.net Core, il utilise le système de journalisation ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="260cd-114">Since SignalR is part of ASP.NET Core, it uses the ASP.NET Core logging system.</span></span> <span data-ttu-id="260cd-115">Dans la configuration par défaut, SignalR enregistre très peu d’informations, mais cela peut être configuré.</span><span class="sxs-lookup"><span data-stu-id="260cd-115">In the default configuration, SignalR logs very little information, but this can configured.</span></span> <span data-ttu-id="260cd-116">Pour plus d’informations sur la configuration de la journalisation des ASP.NET Core, consultez la documentation sur [ASP.net Core Logging](xref:fundamentals/logging/index#configuration) .</span><span class="sxs-lookup"><span data-stu-id="260cd-116">See the documentation on [ASP.NET Core logging](xref:fundamentals/logging/index#configuration) for details on configuring ASP.NET Core logging.</span></span>

SignalR<span data-ttu-id="260cd-117">utilise deux catégories d’enregistreur d’événements :</span><span class="sxs-lookup"><span data-stu-id="260cd-117"> uses two logger categories:</span></span>

* <span data-ttu-id="260cd-118">`Microsoft.AspNetCore.SignalR`: Pour les journaux liés aux protocoles de concentrateur, l’activation de hubs, l’appel de méthodes et d’autres activités liées au Hub.</span><span class="sxs-lookup"><span data-stu-id="260cd-118">`Microsoft.AspNetCore.SignalR`: For logs related to Hub Protocols, activating Hubs, invoking methods, and other Hub-related activities.</span></span>
* <span data-ttu-id="260cd-119">`Microsoft.AspNetCore.Http.Connections`: Pour les journaux liés aux transports, tels que les WebSockets, l’interrogation longue, les événements envoyés par le serveur et l’infrastructure de bas niveau SignalR .</span><span class="sxs-lookup"><span data-stu-id="260cd-119">`Microsoft.AspNetCore.Http.Connections`: For logs related to transports, such as WebSockets, Long Polling, Server-Sent Events, and low-level SignalR infrastructure.</span></span>

<span data-ttu-id="260cd-120">Pour activer les journaux détaillés à partir de SignalR , configurez les deux préfixes précédents au `Debug` niveau de votre fichier *appSettings. JSON* en ajoutant les éléments suivants à la `LogLevel` sous-section dans `Logging` :</span><span class="sxs-lookup"><span data-stu-id="260cd-120">To enable detailed logs from SignalR, configure both of the preceding prefixes to the `Debug` level in your *appsettings.json* file by adding the following items to the `LogLevel` sub-section in `Logging`:</span></span>

[!code-json[](diagnostics/logging-config.json?highlight=7-8)]

<span data-ttu-id="260cd-121">Vous pouvez également configurer cela dans le code de votre `CreateWebHostBuilder` méthode :</span><span class="sxs-lookup"><span data-stu-id="260cd-121">You can also configure this in code in your `CreateWebHostBuilder` method:</span></span>

[!code-csharp[](diagnostics/logging-config-code.cs?highlight=5-6)]

<span data-ttu-id="260cd-122">Si vous n’utilisez pas la configuration basée sur JSON, définissez les valeurs de configuration suivantes dans votre système de configuration :</span><span class="sxs-lookup"><span data-stu-id="260cd-122">If you aren't using JSON-based configuration, set the following configuration values in your configuration system:</span></span>

* `Logging:LogLevel:Microsoft.AspNetCore.SignalR` = `Debug`
* `Logging:LogLevel:Microsoft.AspNetCore.Http.Connections` = `Debug`

<span data-ttu-id="260cd-123">Consultez la documentation de votre système de configuration pour déterminer comment spécifier les valeurs de configuration imbriquées.</span><span class="sxs-lookup"><span data-stu-id="260cd-123">Check the documentation for your configuration system to determine how to specify nested configuration values.</span></span> <span data-ttu-id="260cd-124">Par exemple, lors de l’utilisation de variables d’environnement, deux `_` caractères sont utilisés à la place de `:` (par exemple, `Logging__LogLevel__Microsoft.AspNetCore.SignalR` ).</span><span class="sxs-lookup"><span data-stu-id="260cd-124">For example, when using environment variables, two `_` characters are used instead of the `:` (for example, `Logging__LogLevel__Microsoft.AspNetCore.SignalR`).</span></span>

<span data-ttu-id="260cd-125">Nous vous recommandons d’utiliser le `Debug` niveau lorsque vous collectez des diagnostics plus détaillés pour votre application.</span><span class="sxs-lookup"><span data-stu-id="260cd-125">We recommend using the `Debug` level when gathering more detailed diagnostics for your app.</span></span> <span data-ttu-id="260cd-126">Le `Trace` niveau produit des diagnostics de bas niveau et est rarement nécessaire pour diagnostiquer les problèmes dans votre application.</span><span class="sxs-lookup"><span data-stu-id="260cd-126">The `Trace` level produces very low-level diagnostics and is rarely needed to diagnose issues in your app.</span></span>

## <a name="access-server-side-logs"></a><span data-ttu-id="260cd-127">Accéder aux journaux côté serveur</span><span class="sxs-lookup"><span data-stu-id="260cd-127">Access server-side logs</span></span>

<span data-ttu-id="260cd-128">La façon dont vous accédez aux journaux côté serveur dépend de l’environnement dans lequel vous exécutez.</span><span class="sxs-lookup"><span data-stu-id="260cd-128">How you access server-side logs depends on the environment in which you're running.</span></span>

### <a name="as-a-console-app-outside-iis"></a><span data-ttu-id="260cd-129">En tant qu’application console en dehors d’IIS</span><span class="sxs-lookup"><span data-stu-id="260cd-129">As a console app outside IIS</span></span>

<span data-ttu-id="260cd-130">Si vous exécutez dans une application console, l’enregistreur d’événements de [console](xref:fundamentals/logging/index#console) doit être activé par défaut.</span><span class="sxs-lookup"><span data-stu-id="260cd-130">If you're running in a console app, the [Console logger](xref:fundamentals/logging/index#console) should be enabled by default.</span></span> SignalR<span data-ttu-id="260cd-131">les journaux s’affichent dans la console.</span><span class="sxs-lookup"><span data-stu-id="260cd-131"> logs will appear in the console.</span></span>

### <a name="within-iis-express-from-visual-studio"></a><span data-ttu-id="260cd-132">Dans IIS Express à partir de Visual Studio</span><span class="sxs-lookup"><span data-stu-id="260cd-132">Within IIS Express from Visual Studio</span></span>

<span data-ttu-id="260cd-133">Visual Studio affiche la sortie du journal dans la fenêtre **sortie** .</span><span class="sxs-lookup"><span data-stu-id="260cd-133">Visual Studio displays the log output in the **Output** window.</span></span> <span data-ttu-id="260cd-134">Sélectionnez l’option déroulante **ASP.net Core serveur Web** .</span><span class="sxs-lookup"><span data-stu-id="260cd-134">Select the **ASP.NET Core Web Server** drop down option.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="260cd-135">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="260cd-135">Azure App Service</span></span>

<span data-ttu-id="260cd-136">Activez l’option **journalisation des applications (système de fichiers)** dans la section **journaux de diagnostic** du portail Azure App service et configurez le **niveau** sur `Verbose` .</span><span class="sxs-lookup"><span data-stu-id="260cd-136">Enable the **Application Logging (Filesystem)** option in the **Diagnostics logs** section of the Azure App Service portal and configure the **Level** to `Verbose`.</span></span> <span data-ttu-id="260cd-137">Les journaux doivent être disponibles à partir du service de **streaming des journaux** et dans les journaux du système de fichiers du App service.</span><span class="sxs-lookup"><span data-stu-id="260cd-137">Logs should be available from the **Log streaming** service and in logs on the file system of the App Service.</span></span> <span data-ttu-id="260cd-138">Pour plus d’informations, consultez la page [streaming des journaux Azure](xref:fundamentals/logging/index#azure-log-streaming).</span><span class="sxs-lookup"><span data-stu-id="260cd-138">For more information, see [Azure log streaming](xref:fundamentals/logging/index#azure-log-streaming).</span></span>

### <a name="other-environments"></a><span data-ttu-id="260cd-139">Autres environnements</span><span class="sxs-lookup"><span data-stu-id="260cd-139">Other environments</span></span>

<span data-ttu-id="260cd-140">Si l’application est déployée dans un autre environnement (par exemple, docker, Kubernetes ou service Windows), consultez <xref:fundamentals/logging/index> pour plus d’informations sur la configuration des fournisseurs de journalisation appropriés pour l’environnement.</span><span class="sxs-lookup"><span data-stu-id="260cd-140">If the app is deployed to another environment (for example, Docker, Kubernetes, or Windows Service), see <xref:fundamentals/logging/index> for more information on how to configure logging providers suitable for the environment.</span></span>

## <a name="javascript-client-logging"></a><span data-ttu-id="260cd-141">Journalisation du client JavaScript</span><span class="sxs-lookup"><span data-stu-id="260cd-141">JavaScript client logging</span></span>

> [!WARNING]
> <span data-ttu-id="260cd-142">Les journaux côté client peuvent contenir des informations sensibles de votre application.</span><span class="sxs-lookup"><span data-stu-id="260cd-142">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="260cd-143">**Ne jamais** poster des journaux bruts à partir d’applications de production vers des forums publics tels que github.</span><span class="sxs-lookup"><span data-stu-id="260cd-143">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="260cd-144">Lorsque vous utilisez le client JavaScript, vous pouvez configurer les options de journalisation à l’aide `configureLogging` de la méthode sur `HubConnectionBuilder` :</span><span class="sxs-lookup"><span data-stu-id="260cd-144">When using the JavaScript client, you can configure logging options using the `configureLogging` method on `HubConnectionBuilder`:</span></span>

[!code-javascript[](diagnostics/logging-config-js.js?highlight=3)]

<span data-ttu-id="260cd-145">Pour désactiver entièrement la journalisation, spécifiez `signalR.LogLevel.None` dans la `configureLogging` méthode.</span><span class="sxs-lookup"><span data-stu-id="260cd-145">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="260cd-146">Le tableau suivant montre les niveaux de journal disponibles pour le client JavaScript.</span><span class="sxs-lookup"><span data-stu-id="260cd-146">The following table shows log levels available to the JavaScript client.</span></span> <span data-ttu-id="260cd-147">La définition du niveau de journal sur l’une de ces valeurs active la journalisation à ce niveau et tous les niveaux au-dessus de celui-ci dans la table.</span><span class="sxs-lookup"><span data-stu-id="260cd-147">Setting the log level to one of these values enables logging at that level and all levels above it in the table.</span></span>

| <span data-ttu-id="260cd-148">Level</span><span class="sxs-lookup"><span data-stu-id="260cd-148">Level</span></span> | <span data-ttu-id="260cd-149">Description</span><span class="sxs-lookup"><span data-stu-id="260cd-149">Description</span></span> |
| ----- | ---
<span data-ttu-id="260cd-150">titre : « journalisation et diagnostics dans ASP.NET Core SignalR » Auteur : Description : « Découvrez Comment collecter des diagnostics à partir de votre SignalR application ASP.net core. »</span><span class="sxs-lookup"><span data-stu-id="260cd-150">title: 'Logging and diagnostics in ASP.NET Core SignalR' author: description: 'Learn how to gather diagnostics from your ASP.NET Core SignalR app.'</span></span>
<span data-ttu-id="260cd-151">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="260cd-151">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="260cd-152">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="260cd-152">'Blazor'</span></span>
- <span data-ttu-id="260cd-153">'Identity'</span><span class="sxs-lookup"><span data-stu-id="260cd-153">'Identity'</span></span>
- <span data-ttu-id="260cd-154">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="260cd-154">'Let's Encrypt'</span></span>
- <span data-ttu-id="260cd-155">'Razor'</span><span class="sxs-lookup"><span data-stu-id="260cd-155">'Razor'</span></span>
- <span data-ttu-id="260cd-156">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="260cd-156">'SignalR' uid:</span></span> 

-
<span data-ttu-id="260cd-157">titre : « journalisation et diagnostics dans ASP.NET Core SignalR » Auteur : Description : « Découvrez Comment collecter des diagnostics à partir de votre SignalR application ASP.net core. »</span><span class="sxs-lookup"><span data-stu-id="260cd-157">title: 'Logging and diagnostics in ASP.NET Core SignalR' author: description: 'Learn how to gather diagnostics from your ASP.NET Core SignalR app.'</span></span>
<span data-ttu-id="260cd-158">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="260cd-158">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="260cd-159">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="260cd-159">'Blazor'</span></span>
- <span data-ttu-id="260cd-160">'Identity'</span><span class="sxs-lookup"><span data-stu-id="260cd-160">'Identity'</span></span>
- <span data-ttu-id="260cd-161">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="260cd-161">'Let's Encrypt'</span></span>
- <span data-ttu-id="260cd-162">'Razor'</span><span class="sxs-lookup"><span data-stu-id="260cd-162">'Razor'</span></span>
- <span data-ttu-id="260cd-163">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="260cd-163">'SignalR' uid:</span></span> 

-
<span data-ttu-id="260cd-164">titre : « journalisation et diagnostics dans ASP.NET Core SignalR » Auteur : Description : « Découvrez Comment collecter des diagnostics à partir de votre SignalR application ASP.net core. »</span><span class="sxs-lookup"><span data-stu-id="260cd-164">title: 'Logging and diagnostics in ASP.NET Core SignalR' author: description: 'Learn how to gather diagnostics from your ASP.NET Core SignalR app.'</span></span>
<span data-ttu-id="260cd-165">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="260cd-165">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="260cd-166">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="260cd-166">'Blazor'</span></span>
- <span data-ttu-id="260cd-167">'Identity'</span><span class="sxs-lookup"><span data-stu-id="260cd-167">'Identity'</span></span>
- <span data-ttu-id="260cd-168">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="260cd-168">'Let's Encrypt'</span></span>
- <span data-ttu-id="260cd-169">'Razor'</span><span class="sxs-lookup"><span data-stu-id="260cd-169">'Razor'</span></span>
- <span data-ttu-id="260cd-170">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="260cd-170">'SignalR' uid:</span></span> 

<span data-ttu-id="260cd-171">------ | | `None` | Aucun message n’est enregistré.</span><span class="sxs-lookup"><span data-stu-id="260cd-171">------ | | `None` | No messages are logged.</span></span> <span data-ttu-id="260cd-172">| | `Critical` | Messages indiquant un échec dans l’ensemble de l’application.</span><span class="sxs-lookup"><span data-stu-id="260cd-172">| | `Critical` | Messages that indicate a failure in the entire app.</span></span> <span data-ttu-id="260cd-173">| | `Error` | Messages indiquant un échec dans l’opération en cours.</span><span class="sxs-lookup"><span data-stu-id="260cd-173">| | `Error` | Messages that indicate a failure in the current operation.</span></span> <span data-ttu-id="260cd-174">| | `Warning` | Messages indiquant un problème non fatal.</span><span class="sxs-lookup"><span data-stu-id="260cd-174">| | `Warning` | Messages that indicate a non-fatal problem.</span></span> <span data-ttu-id="260cd-175">| | `Information` | Messages d’information.</span><span class="sxs-lookup"><span data-stu-id="260cd-175">| | `Information` | Informational messages.</span></span> <span data-ttu-id="260cd-176">| | `Debug` | Messages de diagnostic utiles pour le débogage.</span><span class="sxs-lookup"><span data-stu-id="260cd-176">| | `Debug` | Diagnostic messages useful for debugging.</span></span> <span data-ttu-id="260cd-177">| | `Trace` | Messages de diagnostic très détaillés conçus pour diagnostiquer des problèmes spécifiques.</span><span class="sxs-lookup"><span data-stu-id="260cd-177">| | `Trace` | Very detailed diagnostic messages designed for diagnosing specific issues.</span></span> |

<span data-ttu-id="260cd-178">Une fois que vous avez configuré le niveau de détail, les journaux sont écrits dans la console du navigateur (ou la sortie standard dans une application NodeJS).</span><span class="sxs-lookup"><span data-stu-id="260cd-178">Once you've configured the verbosity, the logs will be written to the Browser Console (or Standard Output in a NodeJS app).</span></span>

<span data-ttu-id="260cd-179">Si vous souhaitez envoyer des journaux à un système de journalisation personnalisé, vous pouvez fournir un objet JavaScript qui implémente l' `ILogger` interface.</span><span class="sxs-lookup"><span data-stu-id="260cd-179">If you want to send logs to a custom logging system, you can provide a JavaScript object implementing the `ILogger` interface.</span></span> <span data-ttu-id="260cd-180">La seule méthode qui doit être implémentée est `log` , qui prend le niveau de l’événement et le message associé à l’événement.</span><span class="sxs-lookup"><span data-stu-id="260cd-180">The only method that needs to be implemented is `log`, which takes the level of the event and the message associated with the event.</span></span> <span data-ttu-id="260cd-181">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="260cd-181">For example:</span></span>

[!code-typescript[](diagnostics/custom-logger.ts?highlight=3-7,13)]

## <a name="net-client-logging"></a><span data-ttu-id="260cd-182">Journalisation du client .NET</span><span class="sxs-lookup"><span data-stu-id="260cd-182">.NET client logging</span></span>

> [!WARNING]
> <span data-ttu-id="260cd-183">Les journaux côté client peuvent contenir des informations sensibles de votre application.</span><span class="sxs-lookup"><span data-stu-id="260cd-183">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="260cd-184">**Ne jamais** poster des journaux bruts à partir d’applications de production vers des forums publics tels que github.</span><span class="sxs-lookup"><span data-stu-id="260cd-184">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="260cd-185">Pour récupérer des journaux à partir du client .NET, vous pouvez utiliser la `ConfigureLogging` méthode sur `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="260cd-185">To get logs from the .NET client, you can use the `ConfigureLogging` method on `HubConnectionBuilder`.</span></span> <span data-ttu-id="260cd-186">Cela fonctionne de la même façon que la `ConfigureLogging` méthode sur `WebHostBuilder` et `HostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="260cd-186">This works the same way as the `ConfigureLogging` method on `WebHostBuilder` and `HostBuilder`.</span></span> <span data-ttu-id="260cd-187">Vous pouvez configurer les mêmes fournisseurs de journalisation que ceux que vous utilisez dans ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="260cd-187">You can configure the same logging providers you use in ASP.NET Core.</span></span> <span data-ttu-id="260cd-188">Toutefois, vous devez installer et activer manuellement les packages NuGet pour les différents fournisseurs de journalisation.</span><span class="sxs-lookup"><span data-stu-id="260cd-188">However, you have to manually install and enable the NuGet packages for the individual logging providers.</span></span>

### <a name="console-logging"></a><span data-ttu-id="260cd-189">Écriture dans le journal de la console</span><span class="sxs-lookup"><span data-stu-id="260cd-189">Console logging</span></span>

<span data-ttu-id="260cd-190">Pour activer la journalisation de la console, ajoutez le package [Microsoft. extensions. Logging. console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) .</span><span class="sxs-lookup"><span data-stu-id="260cd-190">In order to enable Console logging, add the [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) package.</span></span> <span data-ttu-id="260cd-191">Ensuite, utilisez la `AddConsole` méthode pour configurer le journal de la console :</span><span class="sxs-lookup"><span data-stu-id="260cd-191">Then, use the `AddConsole` method to configure the console logger:</span></span>

[!code-csharp[](diagnostics/net-client-console-log.cs?highlight=6)]

### <a name="debug-output-window-logging"></a><span data-ttu-id="260cd-192">Journalisation de la fenêtre sortie du débogage</span><span class="sxs-lookup"><span data-stu-id="260cd-192">Debug output window logging</span></span>

<span data-ttu-id="260cd-193">Vous pouvez également configurer les journaux pour accéder à la fenêtre **sortie** dans Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="260cd-193">You can also configure logs to go to the **Output** window in Visual Studio.</span></span> <span data-ttu-id="260cd-194">Installez le package [Microsoft. extensions. Logging. Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) et utilisez la `AddDebug` méthode :</span><span class="sxs-lookup"><span data-stu-id="260cd-194">Install the [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) package and use the `AddDebug` method:</span></span>

[!code-csharp[](diagnostics/net-client-debug-log.cs?highlight=6)]

### <a name="other-logging-providers"></a><span data-ttu-id="260cd-195">Autres fournisseurs de journalisation</span><span class="sxs-lookup"><span data-stu-id="260cd-195">Other logging providers</span></span>

SignalR<span data-ttu-id="260cd-196">prend en charge d’autres fournisseurs de journalisation tels que Serilog, Seq, NLog ou tout autre système de journalisation qui s’intègre à `Microsoft.Extensions.Logging` .</span><span class="sxs-lookup"><span data-stu-id="260cd-196"> supports other logging providers such as Serilog, Seq, NLog, or any other logging system that integrates with `Microsoft.Extensions.Logging`.</span></span> <span data-ttu-id="260cd-197">Si votre système de journalisation fournit un `ILoggerProvider` , vous pouvez l’inscrire auprès des `AddProvider` éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="260cd-197">If your logging system provides an `ILoggerProvider`, you can register it with `AddProvider`:</span></span>

[!code-csharp[](diagnostics/net-client-custom-log.cs?highlight=6)]

### <a name="control-verbosity"></a><span data-ttu-id="260cd-198">Commentaires du contrôle</span><span class="sxs-lookup"><span data-stu-id="260cd-198">Control verbosity</span></span>

<span data-ttu-id="260cd-199">Si vous vous connectez à partir d’autres emplacements dans votre application, le fait de modifier le niveau par défaut en `Debug` peut être trop détaillé.</span><span class="sxs-lookup"><span data-stu-id="260cd-199">If you are logging from other places in your app, changing the default level to `Debug` may be too verbose.</span></span> <span data-ttu-id="260cd-200">Vous pouvez utiliser un filtre pour configurer le niveau de journalisation des SignalR journaux.</span><span class="sxs-lookup"><span data-stu-id="260cd-200">You can use a Filter to configure the logging level for SignalR logs.</span></span> <span data-ttu-id="260cd-201">Cela peut être fait dans le code, à peu près de la même façon que sur le serveur :</span><span class="sxs-lookup"><span data-stu-id="260cd-201">This can be done in code, in much the same way as on the server:</span></span>

[!code-csharp[Controlling verbosity in .NET client](diagnostics/logging-config-client-code.cs?highlight=9-10)]

## <a name="network-traces"></a><span data-ttu-id="260cd-202">Suivis réseau</span><span class="sxs-lookup"><span data-stu-id="260cd-202">Network traces</span></span>

> [!WARNING]
> <span data-ttu-id="260cd-203">Une trace réseau contient le contenu complet de chaque message envoyé par votre application.</span><span class="sxs-lookup"><span data-stu-id="260cd-203">A network trace contains the full contents of every message sent by your app.</span></span> <span data-ttu-id="260cd-204">**Ne** publiez jamais de traces de réseau brutes à partir d’applications de production vers des forums publics tels que github.</span><span class="sxs-lookup"><span data-stu-id="260cd-204">**Never** post raw network traces from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="260cd-205">Si vous rencontrez un problème, un suivi réseau peut parfois fournir de nombreuses informations utiles.</span><span class="sxs-lookup"><span data-stu-id="260cd-205">If you encounter an issue, a network trace can sometimes provide a lot of helpful information.</span></span> <span data-ttu-id="260cd-206">Cela s’avère particulièrement utile si vous envisagez de faire un problème dans notre suivi des problèmes.</span><span class="sxs-lookup"><span data-stu-id="260cd-206">This is particularly useful if you're going to file an issue on our issue tracker.</span></span>

## <a name="collect-a-network-trace-with-fiddler-preferred-option"></a><span data-ttu-id="260cd-207">Collecter un suivi réseau avec Fiddler (option recommandée)</span><span class="sxs-lookup"><span data-stu-id="260cd-207">Collect a network trace with Fiddler (preferred option)</span></span>

<span data-ttu-id="260cd-208">Cette méthode fonctionne pour toutes les applications.</span><span class="sxs-lookup"><span data-stu-id="260cd-208">This method works for all apps.</span></span>

<span data-ttu-id="260cd-209">Fiddler est un outil très puissant pour la collecte de traces HTTP.</span><span class="sxs-lookup"><span data-stu-id="260cd-209">Fiddler is a very powerful tool for collecting HTTP traces.</span></span> <span data-ttu-id="260cd-210">Installez-le à partir de [Telerik.com/Fiddler](https://www.telerik.com/fiddler), lancez-le, puis exécutez votre application et reproduisez le problème.</span><span class="sxs-lookup"><span data-stu-id="260cd-210">Install it from [telerik.com/fiddler](https://www.telerik.com/fiddler), launch it, and then run your app and reproduce the issue.</span></span> <span data-ttu-id="260cd-211">Fiddler est disponible pour Windows et il existe des versions bêta pour macOS et Linux.</span><span class="sxs-lookup"><span data-stu-id="260cd-211">Fiddler is available for Windows, and there are beta versions for macOS and Linux.</span></span>

<span data-ttu-id="260cd-212">Si vous vous connectez à l’aide de HTTPs, vous devez vous assurer que Fiddler peut déchiffrer le trafic HTTPs.</span><span class="sxs-lookup"><span data-stu-id="260cd-212">If you connect using HTTPS, there are some extra steps to ensure Fiddler can decrypt the HTTPS traffic.</span></span> <span data-ttu-id="260cd-213">Pour plus d’informations, consultez la [documentation de Fiddler](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).</span><span class="sxs-lookup"><span data-stu-id="260cd-213">For more details, see the [Fiddler documentation](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).</span></span>

<span data-ttu-id="260cd-214">Une fois que vous avez collecté la trace, vous pouvez exporter la trace en choisissant **fichier**  >  **Enregistrer**  >  **toutes les sessions** dans la barre de menus.</span><span class="sxs-lookup"><span data-stu-id="260cd-214">Once you've collected the trace, you can export the trace by choosing **File** > **Save** > **All Sessions** from the menu bar.</span></span>

![Exportation de toutes les sessions de Fiddler](diagnostics/fiddler-export.png)

## <a name="collect-a-network-trace-with-tcpdump-macos-and-linux-only"></a><span data-ttu-id="260cd-216">Collecter un suivi réseau avec tcpdump (macOS et Linux uniquement)</span><span class="sxs-lookup"><span data-stu-id="260cd-216">Collect a network trace with tcpdump (macOS and Linux only)</span></span>

<span data-ttu-id="260cd-217">Cette méthode fonctionne pour toutes les applications.</span><span class="sxs-lookup"><span data-stu-id="260cd-217">This method works for all apps.</span></span>

<span data-ttu-id="260cd-218">Vous pouvez collecter des traces TCP brutes à l’aide de tcpdump en exécutant la commande suivante à partir d’une interface de commande.</span><span class="sxs-lookup"><span data-stu-id="260cd-218">You can collect raw TCP traces using tcpdump by running the following command from a command shell.</span></span> <span data-ttu-id="260cd-219">Vous devrez peut-être `root` ou préfixer la commande avec `sudo` si vous recevez une erreur d’autorisation :</span><span class="sxs-lookup"><span data-stu-id="260cd-219">You may need to be `root` or prefix the command with `sudo` if you get a permissions error:</span></span>

```console
tcpdump -i [interface] -w trace.pcap
```

<span data-ttu-id="260cd-220">Remplacez `[interface]` par l’interface réseau sur laquelle vous souhaitez effectuer la capture.</span><span class="sxs-lookup"><span data-stu-id="260cd-220">Replace `[interface]` with the network interface you wish to capture on.</span></span> <span data-ttu-id="260cd-221">En règle générale, cela ressemble `/dev/eth0` à (pour votre interface Ethernet standard) ou `/dev/lo0` (pour le trafic localhost).</span><span class="sxs-lookup"><span data-stu-id="260cd-221">Usually, this is something like `/dev/eth0` (for your standard Ethernet interface) or `/dev/lo0` (for localhost traffic).</span></span> <span data-ttu-id="260cd-222">Pour plus d’informations, consultez la `tcpdump` page man sur votre système hôte.</span><span class="sxs-lookup"><span data-stu-id="260cd-222">For more information, see the `tcpdump` man page on your host system.</span></span>

## <a name="collect-a-network-trace-in-the-browser"></a><span data-ttu-id="260cd-223">Collecter un suivi réseau dans le navigateur</span><span class="sxs-lookup"><span data-stu-id="260cd-223">Collect a network trace in the browser</span></span>

<span data-ttu-id="260cd-224">Cette méthode fonctionne uniquement pour les applications basées sur un navigateur.</span><span class="sxs-lookup"><span data-stu-id="260cd-224">This method only works for browser-based apps.</span></span>

<span data-ttu-id="260cd-225">La plupart des Outils de développement de navigateur possèdent un onglet « réseau » qui vous permet de capturer l’activité réseau entre le navigateur et le serveur.</span><span class="sxs-lookup"><span data-stu-id="260cd-225">Most browser Developer Tools have a "Network" tab that allows you to capture network activity between the browser and the server.</span></span> <span data-ttu-id="260cd-226">Toutefois, ces traces n’incluent pas les messages d’événement WebSocket et envoyés par le serveur.</span><span class="sxs-lookup"><span data-stu-id="260cd-226">However, these traces don't include WebSocket and Server-Sent Event messages.</span></span> <span data-ttu-id="260cd-227">Si vous utilisez ces transports, l’utilisation d’un outil tel que Fiddler ou TcpDump (décrit ci-dessous) est une meilleure approche.</span><span class="sxs-lookup"><span data-stu-id="260cd-227">If you are using those transports, using a tool like Fiddler or TcpDump (described below) is a better approach.</span></span>

### <a name="microsoft-edge-and-internet-explorer"></a><span data-ttu-id="260cd-228">Microsoft Edge et Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="260cd-228">Microsoft Edge and Internet Explorer</span></span>

<span data-ttu-id="260cd-229">(Les instructions sont les mêmes pour Edge et Internet Explorer)</span><span class="sxs-lookup"><span data-stu-id="260cd-229">(The instructions are the same for both Edge and Internet Explorer)</span></span>

1. <span data-ttu-id="260cd-230">Appuyez sur F12 pour ouvrir les outils de développement</span><span class="sxs-lookup"><span data-stu-id="260cd-230">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="260cd-231">Cliquez sur l’onglet réseau.</span><span class="sxs-lookup"><span data-stu-id="260cd-231">Click the Network Tab</span></span>
3. <span data-ttu-id="260cd-232">Actualisez la page (si nécessaire) et reproduisez le problème</span><span class="sxs-lookup"><span data-stu-id="260cd-232">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="260cd-233">Cliquez sur l’icône Enregistrer dans la barre d’outils pour exporter la trace en tant que fichier « QAR » :</span><span class="sxs-lookup"><span data-stu-id="260cd-233">Click the Save icon in the toolbar to export the trace as a "HAR" file:</span></span>

![Icône Enregistrer dans l’onglet Réseau des outils de développement Microsoft Edge](diagnostics/ie-edge-har-export.png)

### <a name="google-chrome"></a><span data-ttu-id="260cd-235">Google Chrome</span><span class="sxs-lookup"><span data-stu-id="260cd-235">Google Chrome</span></span>

1. <span data-ttu-id="260cd-236">Appuyez sur F12 pour ouvrir les outils de développement</span><span class="sxs-lookup"><span data-stu-id="260cd-236">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="260cd-237">Cliquez sur l’onglet réseau.</span><span class="sxs-lookup"><span data-stu-id="260cd-237">Click the Network Tab</span></span>
3. <span data-ttu-id="260cd-238">Actualisez la page (si nécessaire) et reproduisez le problème</span><span class="sxs-lookup"><span data-stu-id="260cd-238">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="260cd-239">Cliquez avec le bouton droit n’importe où dans la liste des requêtes, puis choisissez « Enregistrer en tant que fichier \ avec le contenu » :</span><span class="sxs-lookup"><span data-stu-id="260cd-239">Right click anywhere in the list of requests and choose "Save as HAR with content":</span></span>

![Option « Enregistrer en tant que fichier... avec du contenu » dans l’onglet Réseau des outils de développement Google Chrome](diagnostics/chrome-har-export.png)

### <a name="mozilla-firefox"></a><span data-ttu-id="260cd-241">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="260cd-241">Mozilla Firefox</span></span>

1. <span data-ttu-id="260cd-242">Appuyez sur F12 pour ouvrir les outils de développement</span><span class="sxs-lookup"><span data-stu-id="260cd-242">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="260cd-243">Cliquez sur l’onglet réseau.</span><span class="sxs-lookup"><span data-stu-id="260cd-243">Click the Network Tab</span></span>
3. <span data-ttu-id="260cd-244">Actualisez la page (si nécessaire) et reproduisez le problème</span><span class="sxs-lookup"><span data-stu-id="260cd-244">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="260cd-245">Cliquez avec le bouton droit n’importe où dans la liste des requêtes, puis choisissez « Enregistrer tout comme QAR ».</span><span class="sxs-lookup"><span data-stu-id="260cd-245">Right click anywhere in the list of requests and choose "Save All As HAR"</span></span>

![Option « Enregistrer tout comme QAR » dans l’onglet Réseau des outils de développement Mozilla Firefox](diagnostics/firefox-har-export.png)

## <a name="attach-diagnostics-files-to-github-issues"></a><span data-ttu-id="260cd-247">Joindre les fichiers de diagnostic aux problèmes GitHub</span><span class="sxs-lookup"><span data-stu-id="260cd-247">Attach diagnostics files to GitHub issues</span></span>

<span data-ttu-id="260cd-248">Vous pouvez joindre des fichiers de diagnostic à des problèmes de GitHub en les renommant afin qu’ils aient une `.txt` extension, puis en les faisant glisser et en les déposant sur le problème.</span><span class="sxs-lookup"><span data-stu-id="260cd-248">You can attach Diagnostics files to GitHub issues by renaming them so they have a `.txt` extension and then dragging and dropping them on to the issue.</span></span>

> [!NOTE]
> <span data-ttu-id="260cd-249">Ne collez pas le contenu des fichiers journaux ou des traces réseau dans un problème GitHub.</span><span class="sxs-lookup"><span data-stu-id="260cd-249">Please don't paste the content of log files or network traces into a GitHub issue.</span></span> <span data-ttu-id="260cd-250">Ces journaux et suivis peuvent être assez volumineux, et GitHub les tronque généralement.</span><span class="sxs-lookup"><span data-stu-id="260cd-250">These logs and traces can be quite large, and GitHub usually truncates them.</span></span>

![Glissement des fichiers journaux sur un problème GitHub](diagnostics/attaching-diagnostics-files.png)

## <a name="additional-resources"></a><span data-ttu-id="260cd-252">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="260cd-252">Additional resources</span></span>

* <xref:signalr/configuration>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
