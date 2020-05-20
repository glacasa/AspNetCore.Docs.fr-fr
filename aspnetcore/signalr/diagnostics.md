---
title: Journalisation et diagnostics dans ASP.NET CoreSignalR
author: anurse
description: Découvrez Comment collecter des diagnostics à partir de votre SignalR application ASP.net core.
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: signalr
ms.date: 11/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/diagnostics
ms.openlocfilehash: 0dda4fb55b1e2275d9cdb2af0b55824b12121dee
ms.sourcegitcommit: 16b3abec1ed70f9a206f0cfa7cf6404eebaf693d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/17/2020
ms.locfileid: "83444215"
---
# <a name="logging-and-diagnostics-in-aspnet-core-signalr"></a><span data-ttu-id="1cd71-103">Journalisation et diagnostics dans ASP.NET Core Signalr</span><span class="sxs-lookup"><span data-stu-id="1cd71-103">Logging and diagnostics in ASP.NET Core SignalR</span></span>

<span data-ttu-id="1cd71-104">Par [Andrew Stanton-infirmière](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="1cd71-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="1cd71-105">Cet article fournit des conseils sur la collecte de diagnostics à partir de votre application ASP.NET Core Signalr pour vous aider à résoudre les problèmes.</span><span class="sxs-lookup"><span data-stu-id="1cd71-105">This article provides guidance for gathering diagnostics from your ASP.NET Core SignalR app to help troubleshoot issues.</span></span>

## <a name="server-side-logging"></a><span data-ttu-id="1cd71-106">Journalisation côté serveur</span><span class="sxs-lookup"><span data-stu-id="1cd71-106">Server-side logging</span></span>

> [!WARNING]
> <span data-ttu-id="1cd71-107">Les journaux côté serveur peuvent contenir des informations sensibles de votre application.</span><span class="sxs-lookup"><span data-stu-id="1cd71-107">Server-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="1cd71-108">**Ne jamais** poster des journaux bruts à partir d’applications de production vers des forums publics tels que github.</span><span class="sxs-lookup"><span data-stu-id="1cd71-108">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="1cd71-109">Étant donné que Signalr fait partie de ASP.NET Core, il utilise le système de journalisation ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1cd71-109">Since SignalR is part of ASP.NET Core, it uses the ASP.NET Core logging system.</span></span> <span data-ttu-id="1cd71-110">Dans la configuration par défaut, Signalr enregistre très peu d’informations, mais cela peut être configuré.</span><span class="sxs-lookup"><span data-stu-id="1cd71-110">In the default configuration, SignalR logs very little information, but this can configured.</span></span> <span data-ttu-id="1cd71-111">Pour plus d’informations sur la configuration de la journalisation des ASP.NET Core, consultez la documentation sur [ASP.net Core Logging](xref:fundamentals/logging/index#configuration) .</span><span class="sxs-lookup"><span data-stu-id="1cd71-111">See the documentation on [ASP.NET Core logging](xref:fundamentals/logging/index#configuration) for details on configuring ASP.NET Core logging.</span></span>

<span data-ttu-id="1cd71-112">Signalr utilise deux catégories d’enregistreur d’événements :</span><span class="sxs-lookup"><span data-stu-id="1cd71-112">SignalR uses two logger categories:</span></span>

* <span data-ttu-id="1cd71-113">`Microsoft.AspNetCore.SignalR`&ndash;pour les journaux liés aux protocoles de concentrateur, l’activation de hubs, l’appel de méthodes et d’autres activités liées au Hub.</span><span class="sxs-lookup"><span data-stu-id="1cd71-113">`Microsoft.AspNetCore.SignalR` &ndash; for logs related to Hub Protocols, activating Hubs, invoking methods, and other Hub-related activities.</span></span>
* <span data-ttu-id="1cd71-114">`Microsoft.AspNetCore.Http.Connections`&ndash;pour les journaux liés à des transports tels que WebSockets, l’interrogation longue et les événements envoyés par le serveur et l’infrastructure signalr de bas niveau.</span><span class="sxs-lookup"><span data-stu-id="1cd71-114">`Microsoft.AspNetCore.Http.Connections` &ndash; for logs related to transports such as WebSockets, Long Polling and Server-Sent Events and low-level SignalR infrastructure.</span></span>

<span data-ttu-id="1cd71-115">Pour activer les journaux détaillés à partir de Signalr, configurez les deux préfixes précédents au `Debug` niveau de votre fichier *appSettings. JSON* en ajoutant les éléments suivants à la `LogLevel` sous-section dans `Logging` :</span><span class="sxs-lookup"><span data-stu-id="1cd71-115">To enable detailed logs from SignalR, configure both of the preceding prefixes to the `Debug` level in your *appsettings.json* file by adding the following items to the `LogLevel` sub-section in `Logging`:</span></span>

[!code-json[](diagnostics/logging-config.json?highlight=7-8)]

<span data-ttu-id="1cd71-116">Vous pouvez également configurer cela dans le code de votre `CreateWebHostBuilder` méthode :</span><span class="sxs-lookup"><span data-stu-id="1cd71-116">You can also configure this in code in your `CreateWebHostBuilder` method:</span></span>

[!code-csharp[](diagnostics/logging-config-code.cs?highlight=5-6)]

<span data-ttu-id="1cd71-117">Si vous n’utilisez pas la configuration basée sur JSON, définissez les valeurs de configuration suivantes dans votre système de configuration :</span><span class="sxs-lookup"><span data-stu-id="1cd71-117">If you aren't using JSON-based configuration, set the following configuration values in your configuration system:</span></span>

* `Logging:LogLevel:Microsoft.AspNetCore.SignalR` = `Debug`
* `Logging:LogLevel:Microsoft.AspNetCore.Http.Connections` = `Debug`

<span data-ttu-id="1cd71-118">Consultez la documentation de votre système de configuration pour déterminer comment spécifier les valeurs de configuration imbriquées.</span><span class="sxs-lookup"><span data-stu-id="1cd71-118">Check the documentation for your configuration system to determine how to specify nested configuration values.</span></span> <span data-ttu-id="1cd71-119">Par exemple, lors de l’utilisation de variables d’environnement, deux `_` caractères sont utilisés à la place de `:` (par exemple, `Logging__LogLevel__Microsoft.AspNetCore.SignalR` ).</span><span class="sxs-lookup"><span data-stu-id="1cd71-119">For example, when using environment variables, two `_` characters are used instead of the `:` (for example, `Logging__LogLevel__Microsoft.AspNetCore.SignalR`).</span></span>

<span data-ttu-id="1cd71-120">Nous vous recommandons d’utiliser le `Debug` niveau lorsque vous collectez des diagnostics plus détaillés pour votre application.</span><span class="sxs-lookup"><span data-stu-id="1cd71-120">We recommend using the `Debug` level when gathering more detailed diagnostics for your app.</span></span> <span data-ttu-id="1cd71-121">Le `Trace` niveau produit des diagnostics de bas niveau et est rarement nécessaire pour diagnostiquer les problèmes dans votre application.</span><span class="sxs-lookup"><span data-stu-id="1cd71-121">The `Trace` level produces very low-level diagnostics and is rarely needed to diagnose issues in your app.</span></span>

## <a name="access-server-side-logs"></a><span data-ttu-id="1cd71-122">Accéder aux journaux côté serveur</span><span class="sxs-lookup"><span data-stu-id="1cd71-122">Access server-side logs</span></span>

<span data-ttu-id="1cd71-123">La façon dont vous accédez aux journaux côté serveur dépend de l’environnement dans lequel vous exécutez.</span><span class="sxs-lookup"><span data-stu-id="1cd71-123">How you access server-side logs depends on the environment in which you're running.</span></span>

### <a name="as-a-console-app-outside-iis"></a><span data-ttu-id="1cd71-124">En tant qu’application console en dehors d’IIS</span><span class="sxs-lookup"><span data-stu-id="1cd71-124">As a console app outside IIS</span></span>

<span data-ttu-id="1cd71-125">Si vous exécutez dans une application console, l’enregistreur d’événements de [console](xref:fundamentals/logging/index#console) doit être activé par défaut.</span><span class="sxs-lookup"><span data-stu-id="1cd71-125">If you're running in a console app, the [Console logger](xref:fundamentals/logging/index#console) should be enabled by default.</span></span> <span data-ttu-id="1cd71-126">Les journaux signalr s’affichent dans la console.</span><span class="sxs-lookup"><span data-stu-id="1cd71-126">SignalR logs will appear in the console.</span></span>

### <a name="within-iis-express-from-visual-studio"></a><span data-ttu-id="1cd71-127">Dans IIS Express à partir de Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1cd71-127">Within IIS Express from Visual Studio</span></span>

<span data-ttu-id="1cd71-128">Visual Studio affiche la sortie du journal dans la fenêtre **sortie** .</span><span class="sxs-lookup"><span data-stu-id="1cd71-128">Visual Studio displays the log output in the **Output** window.</span></span> <span data-ttu-id="1cd71-129">Sélectionnez l’option déroulante **ASP.net Core serveur Web** .</span><span class="sxs-lookup"><span data-stu-id="1cd71-129">Select the **ASP.NET Core Web Server** drop down option.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="1cd71-130">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="1cd71-130">Azure App Service</span></span>

<span data-ttu-id="1cd71-131">Activez l’option **journalisation des applications (système de fichiers)** dans la section **journaux de diagnostic** du portail Azure App service et configurez le **niveau** sur `Verbose` .</span><span class="sxs-lookup"><span data-stu-id="1cd71-131">Enable the **Application Logging (Filesystem)** option in the **Diagnostics logs** section of the Azure App Service portal and configure the **Level** to `Verbose`.</span></span> <span data-ttu-id="1cd71-132">Les journaux doivent être disponibles à partir du service de **streaming des journaux** et dans les journaux du système de fichiers du App service.</span><span class="sxs-lookup"><span data-stu-id="1cd71-132">Logs should be available from the **Log streaming** service and in logs on the file system of the App Service.</span></span> <span data-ttu-id="1cd71-133">Pour plus d’informations, consultez la page [streaming des journaux Azure](xref:fundamentals/logging/index#azure-log-streaming).</span><span class="sxs-lookup"><span data-stu-id="1cd71-133">For more information, see [Azure log streaming](xref:fundamentals/logging/index#azure-log-streaming).</span></span>

### <a name="other-environments"></a><span data-ttu-id="1cd71-134">Autres environnements</span><span class="sxs-lookup"><span data-stu-id="1cd71-134">Other environments</span></span>

<span data-ttu-id="1cd71-135">Si l’application est déployée dans un autre environnement (par exemple, docker, Kubernetes ou service Windows), consultez <xref:fundamentals/logging/index> pour plus d’informations sur la configuration des fournisseurs de journalisation appropriés pour l’environnement.</span><span class="sxs-lookup"><span data-stu-id="1cd71-135">If the app is deployed to another environment (for example, Docker, Kubernetes, or Windows Service), see <xref:fundamentals/logging/index> for more information on how to configure logging providers suitable for the environment.</span></span>

## <a name="javascript-client-logging"></a><span data-ttu-id="1cd71-136">Journalisation du client JavaScript</span><span class="sxs-lookup"><span data-stu-id="1cd71-136">JavaScript client logging</span></span>

> [!WARNING]
> <span data-ttu-id="1cd71-137">Les journaux côté client peuvent contenir des informations sensibles de votre application.</span><span class="sxs-lookup"><span data-stu-id="1cd71-137">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="1cd71-138">**Ne jamais** poster des journaux bruts à partir d’applications de production vers des forums publics tels que github.</span><span class="sxs-lookup"><span data-stu-id="1cd71-138">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="1cd71-139">Lorsque vous utilisez le client JavaScript, vous pouvez configurer les options de journalisation à l’aide `configureLogging` de la méthode sur `HubConnectionBuilder` :</span><span class="sxs-lookup"><span data-stu-id="1cd71-139">When using the JavaScript client, you can configure logging options using the `configureLogging` method on `HubConnectionBuilder`:</span></span>

[!code-javascript[](diagnostics/logging-config-js.js?highlight=3)]

<span data-ttu-id="1cd71-140">Pour désactiver entièrement la journalisation, spécifiez `signalR.LogLevel.None` dans la `configureLogging` méthode.</span><span class="sxs-lookup"><span data-stu-id="1cd71-140">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="1cd71-141">Le tableau suivant montre les niveaux de journal disponibles pour le client JavaScript.</span><span class="sxs-lookup"><span data-stu-id="1cd71-141">The following table shows log levels available to the JavaScript client.</span></span> <span data-ttu-id="1cd71-142">La définition du niveau de journal sur l’une de ces valeurs active la journalisation à ce niveau et tous les niveaux au-dessus de celui-ci dans la table.</span><span class="sxs-lookup"><span data-stu-id="1cd71-142">Setting the log level to one of these values enables logging at that level and all levels above it in the table.</span></span>

| <span data-ttu-id="1cd71-143">Level</span><span class="sxs-lookup"><span data-stu-id="1cd71-143">Level</span></span> | <span data-ttu-id="1cd71-144">Description</span><span class="sxs-lookup"><span data-stu-id="1cd71-144">Description</span></span> |
| ----- | ----------- |
| `None` | <span data-ttu-id="1cd71-145">Aucun message n’est enregistré.</span><span class="sxs-lookup"><span data-stu-id="1cd71-145">No messages are logged.</span></span> |
| `Critical` | <span data-ttu-id="1cd71-146">Messages indiquant un échec dans l’ensemble de l’application.</span><span class="sxs-lookup"><span data-stu-id="1cd71-146">Messages that indicate a failure in the entire app.</span></span> |
| `Error` | <span data-ttu-id="1cd71-147">Messages indiquant un échec dans l’opération en cours.</span><span class="sxs-lookup"><span data-stu-id="1cd71-147">Messages that indicate a failure in the current operation.</span></span> |
| `Warning` | <span data-ttu-id="1cd71-148">Messages indiquant un problème non fatal.</span><span class="sxs-lookup"><span data-stu-id="1cd71-148">Messages that indicate a non-fatal problem.</span></span> |
| `Information` | <span data-ttu-id="1cd71-149">Messages d’information.</span><span class="sxs-lookup"><span data-stu-id="1cd71-149">Informational messages.</span></span> |
| `Debug` | <span data-ttu-id="1cd71-150">Messages de diagnostic utiles pour le débogage.</span><span class="sxs-lookup"><span data-stu-id="1cd71-150">Diagnostic messages useful for debugging.</span></span> |
| `Trace` | <span data-ttu-id="1cd71-151">Messages de diagnostic très détaillés conçus pour diagnostiquer des problèmes spécifiques.</span><span class="sxs-lookup"><span data-stu-id="1cd71-151">Very detailed diagnostic messages designed for diagnosing specific issues.</span></span> |

<span data-ttu-id="1cd71-152">Une fois que vous avez configuré le niveau de détail, les journaux sont écrits dans la console du navigateur (ou la sortie standard dans une application NodeJS).</span><span class="sxs-lookup"><span data-stu-id="1cd71-152">Once you've configured the verbosity, the logs will be written to the Browser Console (or Standard Output in a NodeJS app).</span></span>

<span data-ttu-id="1cd71-153">Si vous souhaitez envoyer des journaux à un système de journalisation personnalisé, vous pouvez fournir un objet JavaScript qui implémente l' `ILogger` interface.</span><span class="sxs-lookup"><span data-stu-id="1cd71-153">If you want to send logs to a custom logging system, you can provide a JavaScript object implementing the `ILogger` interface.</span></span> <span data-ttu-id="1cd71-154">La seule méthode qui doit être implémentée est `log` , qui prend le niveau de l’événement et le message associé à l’événement.</span><span class="sxs-lookup"><span data-stu-id="1cd71-154">The only method that needs to be implemented is `log`, which takes the level of the event and the message associated with the event.</span></span> <span data-ttu-id="1cd71-155">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="1cd71-155">For example:</span></span>

[!code-typescript[](diagnostics/custom-logger.ts?highlight=3-7,13)]

## <a name="net-client-logging"></a><span data-ttu-id="1cd71-156">Journalisation du client .NET</span><span class="sxs-lookup"><span data-stu-id="1cd71-156">.NET client logging</span></span>

> [!WARNING]
> <span data-ttu-id="1cd71-157">Les journaux côté client peuvent contenir des informations sensibles de votre application.</span><span class="sxs-lookup"><span data-stu-id="1cd71-157">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="1cd71-158">**Ne jamais** poster des journaux bruts à partir d’applications de production vers des forums publics tels que github.</span><span class="sxs-lookup"><span data-stu-id="1cd71-158">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="1cd71-159">Pour récupérer des journaux à partir du client .NET, vous pouvez utiliser la `ConfigureLogging` méthode sur `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="1cd71-159">To get logs from the .NET client, you can use the `ConfigureLogging` method on `HubConnectionBuilder`.</span></span> <span data-ttu-id="1cd71-160">Cela fonctionne de la même façon que la `ConfigureLogging` méthode sur `WebHostBuilder` et `HostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="1cd71-160">This works the same way as the `ConfigureLogging` method on `WebHostBuilder` and `HostBuilder`.</span></span> <span data-ttu-id="1cd71-161">Vous pouvez configurer les mêmes fournisseurs de journalisation que ceux que vous utilisez dans ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1cd71-161">You can configure the same logging providers you use in ASP.NET Core.</span></span> <span data-ttu-id="1cd71-162">Toutefois, vous devez installer et activer manuellement les packages NuGet pour les différents fournisseurs de journalisation.</span><span class="sxs-lookup"><span data-stu-id="1cd71-162">However, you have to manually install and enable the NuGet packages for the individual logging providers.</span></span>

### <a name="console-logging"></a><span data-ttu-id="1cd71-163">Écriture dans le journal de la console</span><span class="sxs-lookup"><span data-stu-id="1cd71-163">Console logging</span></span>

<span data-ttu-id="1cd71-164">Pour activer la journalisation de la console, ajoutez le package [Microsoft. extensions. Logging. console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) .</span><span class="sxs-lookup"><span data-stu-id="1cd71-164">In order to enable Console logging, add the [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) package.</span></span> <span data-ttu-id="1cd71-165">Ensuite, utilisez la `AddConsole` méthode pour configurer le journal de la console :</span><span class="sxs-lookup"><span data-stu-id="1cd71-165">Then, use the `AddConsole` method to configure the console logger:</span></span>

[!code-csharp[](diagnostics/net-client-console-log.cs?highlight=6)]

### <a name="debug-output-window-logging"></a><span data-ttu-id="1cd71-166">Journalisation de la fenêtre sortie du débogage</span><span class="sxs-lookup"><span data-stu-id="1cd71-166">Debug output window logging</span></span>

<span data-ttu-id="1cd71-167">Vous pouvez également configurer les journaux pour accéder à la fenêtre **sortie** dans Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="1cd71-167">You can also configure logs to go to the **Output** window in Visual Studio.</span></span> <span data-ttu-id="1cd71-168">Installez le package [Microsoft. extensions. Logging. Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) et utilisez la `AddDebug` méthode :</span><span class="sxs-lookup"><span data-stu-id="1cd71-168">Install the [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) package and use the `AddDebug` method:</span></span>

[!code-csharp[](diagnostics/net-client-debug-log.cs?highlight=6)]

### <a name="other-logging-providers"></a><span data-ttu-id="1cd71-169">Autres fournisseurs de journalisation</span><span class="sxs-lookup"><span data-stu-id="1cd71-169">Other logging providers</span></span>

SignalR<span data-ttu-id="1cd71-170">prend en charge d’autres fournisseurs de journalisation tels que Serilog, Seq, NLog ou tout autre système de journalisation qui s’intègre à `Microsoft.Extensions.Logging` .</span><span class="sxs-lookup"><span data-stu-id="1cd71-170"> supports other logging providers such as Serilog, Seq, NLog, or any other logging system that integrates with `Microsoft.Extensions.Logging`.</span></span> <span data-ttu-id="1cd71-171">Si votre système de journalisation fournit un `ILoggerProvider` , vous pouvez l’inscrire auprès des `AddProvider` éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="1cd71-171">If your logging system provides an `ILoggerProvider`, you can register it with `AddProvider`:</span></span>

[!code-csharp[](diagnostics/net-client-custom-log.cs?highlight=6)]

### <a name="control-verbosity"></a><span data-ttu-id="1cd71-172">Commentaires du contrôle</span><span class="sxs-lookup"><span data-stu-id="1cd71-172">Control verbosity</span></span>

<span data-ttu-id="1cd71-173">Si vous vous connectez à partir d’autres emplacements dans votre application, le fait de modifier le niveau par défaut en `Debug` peut être trop détaillé.</span><span class="sxs-lookup"><span data-stu-id="1cd71-173">If you are logging from other places in your app, changing the default level to `Debug` may be too verbose.</span></span> <span data-ttu-id="1cd71-174">Vous pouvez utiliser un filtre pour configurer le niveau de journalisation des SignalR journaux.</span><span class="sxs-lookup"><span data-stu-id="1cd71-174">You can use a Filter to configure the logging level for SignalR logs.</span></span> <span data-ttu-id="1cd71-175">Cela peut être fait dans le code, à peu près de la même façon que sur le serveur :</span><span class="sxs-lookup"><span data-stu-id="1cd71-175">This can be done in code, in much the same way as on the server:</span></span>

[!code-csharp[Controlling verbosity in .NET client](diagnostics/logging-config-client-code.cs?highlight=9-10)]

## <a name="network-traces"></a><span data-ttu-id="1cd71-176">Suivis réseau</span><span class="sxs-lookup"><span data-stu-id="1cd71-176">Network traces</span></span>

> [!WARNING]
> <span data-ttu-id="1cd71-177">Une trace réseau contient le contenu complet de chaque message envoyé par votre application.</span><span class="sxs-lookup"><span data-stu-id="1cd71-177">A network trace contains the full contents of every message sent by your app.</span></span> <span data-ttu-id="1cd71-178">**Ne** publiez jamais de traces de réseau brutes à partir d’applications de production vers des forums publics tels que github.</span><span class="sxs-lookup"><span data-stu-id="1cd71-178">**Never** post raw network traces from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="1cd71-179">Si vous rencontrez un problème, un suivi réseau peut parfois fournir de nombreuses informations utiles.</span><span class="sxs-lookup"><span data-stu-id="1cd71-179">If you encounter an issue, a network trace can sometimes provide a lot of helpful information.</span></span> <span data-ttu-id="1cd71-180">Cela s’avère particulièrement utile si vous envisagez de faire un problème dans notre suivi des problèmes.</span><span class="sxs-lookup"><span data-stu-id="1cd71-180">This is particularly useful if you're going to file an issue on our issue tracker.</span></span>

## <a name="collect-a-network-trace-with-fiddler-preferred-option"></a><span data-ttu-id="1cd71-181">Collecter un suivi réseau avec Fiddler (option recommandée)</span><span class="sxs-lookup"><span data-stu-id="1cd71-181">Collect a network trace with Fiddler (preferred option)</span></span>

<span data-ttu-id="1cd71-182">Cette méthode fonctionne pour toutes les applications.</span><span class="sxs-lookup"><span data-stu-id="1cd71-182">This method works for all apps.</span></span>

<span data-ttu-id="1cd71-183">Fiddler est un outil très puissant pour la collecte de traces HTTP.</span><span class="sxs-lookup"><span data-stu-id="1cd71-183">Fiddler is a very powerful tool for collecting HTTP traces.</span></span> <span data-ttu-id="1cd71-184">Installez-le à partir de [Telerik.com/Fiddler](https://www.telerik.com/fiddler), lancez-le, puis exécutez votre application et reproduisez le problème.</span><span class="sxs-lookup"><span data-stu-id="1cd71-184">Install it from [telerik.com/fiddler](https://www.telerik.com/fiddler), launch it, and then run your app and reproduce the issue.</span></span> <span data-ttu-id="1cd71-185">Fiddler est disponible pour Windows et il existe des versions bêta pour macOS et Linux.</span><span class="sxs-lookup"><span data-stu-id="1cd71-185">Fiddler is available for Windows, and there are beta versions for macOS and Linux.</span></span>

<span data-ttu-id="1cd71-186">Si vous vous connectez à l’aide de HTTPs, vous devez vous assurer que Fiddler peut déchiffrer le trafic HTTPs.</span><span class="sxs-lookup"><span data-stu-id="1cd71-186">If you connect using HTTPS, there are some extra steps to ensure Fiddler can decrypt the HTTPS traffic.</span></span> <span data-ttu-id="1cd71-187">Pour plus d’informations, consultez la [documentation de Fiddler](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).</span><span class="sxs-lookup"><span data-stu-id="1cd71-187">For more details, see the [Fiddler documentation](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).</span></span>

<span data-ttu-id="1cd71-188">Une fois que vous avez collecté la trace, vous pouvez exporter la trace en choisissant **fichier**  >  **Enregistrer**  >  **toutes les sessions** dans la barre de menus.</span><span class="sxs-lookup"><span data-stu-id="1cd71-188">Once you've collected the trace, you can export the trace by choosing **File** > **Save** > **All Sessions** from the menu bar.</span></span>

![Exportation de toutes les sessions de Fiddler](diagnostics/fiddler-export.png)

## <a name="collect-a-network-trace-with-tcpdump-macos-and-linux-only"></a><span data-ttu-id="1cd71-190">Collecter un suivi réseau avec tcpdump (macOS et Linux uniquement)</span><span class="sxs-lookup"><span data-stu-id="1cd71-190">Collect a network trace with tcpdump (macOS and Linux only)</span></span>

<span data-ttu-id="1cd71-191">Cette méthode fonctionne pour toutes les applications.</span><span class="sxs-lookup"><span data-stu-id="1cd71-191">This method works for all apps.</span></span>

<span data-ttu-id="1cd71-192">Vous pouvez collecter des traces TCP brutes à l’aide de tcpdump en exécutant la commande suivante à partir d’une interface de commande.</span><span class="sxs-lookup"><span data-stu-id="1cd71-192">You can collect raw TCP traces using tcpdump by running the following command from a command shell.</span></span> <span data-ttu-id="1cd71-193">Vous devrez peut-être `root` ou préfixer la commande avec `sudo` si vous recevez une erreur d’autorisation :</span><span class="sxs-lookup"><span data-stu-id="1cd71-193">You may need to be `root` or prefix the command with `sudo` if you get a permissions error:</span></span>

```console
tcpdump -i [interface] -w trace.pcap
```

<span data-ttu-id="1cd71-194">Remplacez `[interface]` par l’interface réseau sur laquelle vous souhaitez effectuer la capture.</span><span class="sxs-lookup"><span data-stu-id="1cd71-194">Replace `[interface]` with the network interface you wish to capture on.</span></span> <span data-ttu-id="1cd71-195">En règle générale, cela ressemble `/dev/eth0` à (pour votre interface Ethernet standard) ou `/dev/lo0` (pour le trafic localhost).</span><span class="sxs-lookup"><span data-stu-id="1cd71-195">Usually, this is something like `/dev/eth0` (for your standard Ethernet interface) or `/dev/lo0` (for localhost traffic).</span></span> <span data-ttu-id="1cd71-196">Pour plus d’informations, consultez la `tcpdump` page man sur votre système hôte.</span><span class="sxs-lookup"><span data-stu-id="1cd71-196">For more information, see the `tcpdump` man page on your host system.</span></span>

## <a name="collect-a-network-trace-in-the-browser"></a><span data-ttu-id="1cd71-197">Collecter un suivi réseau dans le navigateur</span><span class="sxs-lookup"><span data-stu-id="1cd71-197">Collect a network trace in the browser</span></span>

<span data-ttu-id="1cd71-198">Cette méthode fonctionne uniquement pour les applications basées sur un navigateur.</span><span class="sxs-lookup"><span data-stu-id="1cd71-198">This method only works for browser-based apps.</span></span>

<span data-ttu-id="1cd71-199">La plupart des Outils de développement de navigateur possèdent un onglet « réseau » qui vous permet de capturer l’activité réseau entre le navigateur et le serveur.</span><span class="sxs-lookup"><span data-stu-id="1cd71-199">Most browser Developer Tools have a "Network" tab that allows you to capture network activity between the browser and the server.</span></span> <span data-ttu-id="1cd71-200">Toutefois, ces traces n’incluent pas les messages d’événement WebSocket et envoyés par le serveur.</span><span class="sxs-lookup"><span data-stu-id="1cd71-200">However, these traces don't include WebSocket and Server-Sent Event messages.</span></span> <span data-ttu-id="1cd71-201">Si vous utilisez ces transports, l’utilisation d’un outil tel que Fiddler ou TcpDump (décrit ci-dessous) est une meilleure approche.</span><span class="sxs-lookup"><span data-stu-id="1cd71-201">If you are using those transports, using a tool like Fiddler or TcpDump (described below) is a better approach.</span></span>

### <a name="microsoft-edge-and-internet-explorer"></a><span data-ttu-id="1cd71-202">Microsoft Edge et Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="1cd71-202">Microsoft Edge and Internet Explorer</span></span>

<span data-ttu-id="1cd71-203">(Les instructions sont les mêmes pour Edge et Internet Explorer)</span><span class="sxs-lookup"><span data-stu-id="1cd71-203">(The instructions are the same for both Edge and Internet Explorer)</span></span>

1. <span data-ttu-id="1cd71-204">Appuyez sur F12 pour ouvrir les outils de développement</span><span class="sxs-lookup"><span data-stu-id="1cd71-204">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="1cd71-205">Cliquez sur l’onglet réseau.</span><span class="sxs-lookup"><span data-stu-id="1cd71-205">Click the Network Tab</span></span>
3. <span data-ttu-id="1cd71-206">Actualisez la page (si nécessaire) et reproduisez le problème</span><span class="sxs-lookup"><span data-stu-id="1cd71-206">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="1cd71-207">Cliquez sur l’icône Enregistrer dans la barre d’outils pour exporter la trace en tant que fichier « QAR » :</span><span class="sxs-lookup"><span data-stu-id="1cd71-207">Click the Save icon in the toolbar to export the trace as a "HAR" file:</span></span>

![Icône Enregistrer dans l’onglet Réseau des outils de développement Microsoft Edge](diagnostics/ie-edge-har-export.png)

### <a name="google-chrome"></a><span data-ttu-id="1cd71-209">Google Chrome</span><span class="sxs-lookup"><span data-stu-id="1cd71-209">Google Chrome</span></span>

1. <span data-ttu-id="1cd71-210">Appuyez sur F12 pour ouvrir les outils de développement</span><span class="sxs-lookup"><span data-stu-id="1cd71-210">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="1cd71-211">Cliquez sur l’onglet réseau.</span><span class="sxs-lookup"><span data-stu-id="1cd71-211">Click the Network Tab</span></span>
3. <span data-ttu-id="1cd71-212">Actualisez la page (si nécessaire) et reproduisez le problème</span><span class="sxs-lookup"><span data-stu-id="1cd71-212">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="1cd71-213">Cliquez avec le bouton droit n’importe où dans la liste des requêtes, puis choisissez « Enregistrer en tant que fichier \ avec le contenu » :</span><span class="sxs-lookup"><span data-stu-id="1cd71-213">Right click anywhere in the list of requests and choose "Save as HAR with content":</span></span>

![Option « Enregistrer en tant que fichier... avec du contenu » dans l’onglet Réseau des outils de développement Google Chrome](diagnostics/chrome-har-export.png)

### <a name="mozilla-firefox"></a><span data-ttu-id="1cd71-215">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="1cd71-215">Mozilla Firefox</span></span>

1. <span data-ttu-id="1cd71-216">Appuyez sur F12 pour ouvrir les outils de développement</span><span class="sxs-lookup"><span data-stu-id="1cd71-216">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="1cd71-217">Cliquez sur l’onglet réseau.</span><span class="sxs-lookup"><span data-stu-id="1cd71-217">Click the Network Tab</span></span>
3. <span data-ttu-id="1cd71-218">Actualisez la page (si nécessaire) et reproduisez le problème</span><span class="sxs-lookup"><span data-stu-id="1cd71-218">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="1cd71-219">Cliquez avec le bouton droit n’importe où dans la liste des requêtes, puis choisissez « Enregistrer tout comme QAR ».</span><span class="sxs-lookup"><span data-stu-id="1cd71-219">Right click anywhere in the list of requests and choose "Save All As HAR"</span></span>

![Option « Enregistrer tout comme QAR » dans l’onglet Réseau des outils de développement Mozilla Firefox](diagnostics/firefox-har-export.png)

## <a name="attach-diagnostics-files-to-github-issues"></a><span data-ttu-id="1cd71-221">Joindre les fichiers de diagnostic aux problèmes GitHub</span><span class="sxs-lookup"><span data-stu-id="1cd71-221">Attach diagnostics files to GitHub issues</span></span>

<span data-ttu-id="1cd71-222">Vous pouvez joindre des fichiers de diagnostic à des problèmes de GitHub en les renommant afin qu’ils aient une `.txt` extension, puis en les faisant glisser et en les déposant sur le problème.</span><span class="sxs-lookup"><span data-stu-id="1cd71-222">You can attach Diagnostics files to GitHub issues by renaming them so they have a `.txt` extension and then dragging and dropping them on to the issue.</span></span>

> [!NOTE]
> <span data-ttu-id="1cd71-223">Ne collez pas le contenu des fichiers journaux ou des traces réseau dans un problème GitHub.</span><span class="sxs-lookup"><span data-stu-id="1cd71-223">Please don't paste the content of log files or network traces into a GitHub issue.</span></span> <span data-ttu-id="1cd71-224">Ces journaux et suivis peuvent être assez volumineux, et GitHub les tronque généralement.</span><span class="sxs-lookup"><span data-stu-id="1cd71-224">These logs and traces can be quite large, and GitHub usually truncates them.</span></span>

![Glissement des fichiers journaux sur un problème GitHub](diagnostics/attaching-diagnostics-files.png)

## <a name="additional-resources"></a><span data-ttu-id="1cd71-226">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="1cd71-226">Additional resources</span></span>

* <xref:signalr/configuration>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
