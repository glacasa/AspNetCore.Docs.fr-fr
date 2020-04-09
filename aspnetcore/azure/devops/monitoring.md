---
title: Moniteur et débogé - DevOps avec ASP.NET Core et Azure
author: CamSoper
description: Suivi et débogage de votre code dans le cadre d’une solution DevOps avec ASP.NET Core et Azure
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 07/10/2019
uid: azure/devops/monitor
ms.openlocfilehash: 1d8ed99f4387dbc99929164c558cc2ce14bd9ea0
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78659501"
---
# <a name="monitor-and-debug"></a><span data-ttu-id="8919c-103">Surveiller et débouger</span><span class="sxs-lookup"><span data-stu-id="8919c-103">Monitor and debug</span></span>

<span data-ttu-id="8919c-104">Après avoir déployé l’application et construit un pipeline DevOps, il est important de comprendre comment surveiller et dépanner l’application.</span><span class="sxs-lookup"><span data-stu-id="8919c-104">Having deployed the app and built a DevOps pipeline, it's important to understand how to monitor and troubleshoot the app.</span></span>

<span data-ttu-id="8919c-105">Dans cette section, vous accomplirez les tâches suivantes :</span><span class="sxs-lookup"><span data-stu-id="8919c-105">In this section, you'll complete the following tasks:</span></span>

* <span data-ttu-id="8919c-106">Trouvez des données de surveillance et de dépannage de base dans le portail Azure</span><span class="sxs-lookup"><span data-stu-id="8919c-106">Find basic monitoring and troubleshooting data in the Azure portal</span></span>
* <span data-ttu-id="8919c-107">Découvrez comment Azure Monitor fournit un aperçu plus approfondi des mesures sur tous les services Azure</span><span class="sxs-lookup"><span data-stu-id="8919c-107">Learn how Azure Monitor provides a deeper look at metrics across all Azure services</span></span>
* <span data-ttu-id="8919c-108">Connectez l’application web avec Application Insights pour le profilage d’applications</span><span class="sxs-lookup"><span data-stu-id="8919c-108">Connect the web app with Application Insights for app profiling</span></span>
* <span data-ttu-id="8919c-109">Activez l’enregistrement et apprenez où télécharger des journaux</span><span class="sxs-lookup"><span data-stu-id="8919c-109">Turn on logging and learn where to download logs</span></span>
* <span data-ttu-id="8919c-110">Journaux de flux en temps réel</span><span class="sxs-lookup"><span data-stu-id="8919c-110">Stream logs in real time</span></span>
* <span data-ttu-id="8919c-111">Découvrez où mettre en place des alertes</span><span class="sxs-lookup"><span data-stu-id="8919c-111">Learn where to set up alerts</span></span>
* <span data-ttu-id="8919c-112">Découvrez les applications Web Azure App Service de débogage à distance.</span><span class="sxs-lookup"><span data-stu-id="8919c-112">Learn about remote debugging Azure App Service web apps.</span></span>

## <a name="basic-monitoring-and-troubleshooting"></a><span data-ttu-id="8919c-113">Surveillance et dépannage de base</span><span class="sxs-lookup"><span data-stu-id="8919c-113">Basic monitoring and troubleshooting</span></span>

<span data-ttu-id="8919c-114">Les applications Web App Service sont facilement surveillées en temps réel.</span><span class="sxs-lookup"><span data-stu-id="8919c-114">App Service web apps are easily monitored in real time.</span></span> <span data-ttu-id="8919c-115">Le portail Azure rend des mesures dans des graphiques et des graphiques faciles à comprendre.</span><span class="sxs-lookup"><span data-stu-id="8919c-115">The Azure portal renders metrics in easy-to-understand charts and graphs.</span></span>

1. <span data-ttu-id="8919c-116">Ouvrez le [portail Azure,](https://portal.azure.com)puis naviguez vers le \*service\<d’applications mywebapp unique_number.\> \*</span><span class="sxs-lookup"><span data-stu-id="8919c-116">Open the [Azure portal](https://portal.azure.com), and then navigate to the *mywebapp\<unique_number\>* App Service.</span></span>

1. <span data-ttu-id="8919c-117">**L’onglet Aperçu** affiche des informations utiles « en un coup d’œil », y compris des graphiques affichant des mesures récentes.</span><span class="sxs-lookup"><span data-stu-id="8919c-117">The **Overview** tab displays useful "at-a-glance" information, including graphs displaying recent metrics.</span></span>

    ![Capture d’écran affichant le panneau d’aperçu](./media/monitoring/overview.png)

    * <span data-ttu-id="8919c-119">**Http 5xx**: Comptez des erreurs côté serveur, généralement des exceptions dans ASP.NET code Core.</span><span class="sxs-lookup"><span data-stu-id="8919c-119">**Http 5xx**: Count of server-side errors, usually exceptions in ASP.NET Core code.</span></span>
    * <span data-ttu-id="8919c-120">**Données :** Entrée de données entrant dans votre application web.</span><span class="sxs-lookup"><span data-stu-id="8919c-120">**Data In**: Data ingress coming into your web app.</span></span>
    * <span data-ttu-id="8919c-121">**Données Out**: Les données s’éloignent de votre application web vers les clients.</span><span class="sxs-lookup"><span data-stu-id="8919c-121">**Data Out**: Data egress from your web app to clients.</span></span>
    * <span data-ttu-id="8919c-122">**Demandes**: Comptez des demandes HTTP.</span><span class="sxs-lookup"><span data-stu-id="8919c-122">**Requests**: Count of HTTP requests.</span></span>
    * <span data-ttu-id="8919c-123">**Temps de réponse moyen**: Temps moyen pour l’application web pour répondre aux demandes HTTP.</span><span class="sxs-lookup"><span data-stu-id="8919c-123">**Average Response Time**: Average time for the web app to respond to HTTP requests.</span></span>

    <span data-ttu-id="8919c-124">Plusieurs outils en libre-service pour le dépannage et l’optimisation se trouvent également sur cette page.</span><span class="sxs-lookup"><span data-stu-id="8919c-124">Several self-service tools for troubleshooting and optimization are also found on this page.</span></span>

    ![Capture d’écran montrant des outils en libre-service](./media/monitoring/wizards.png)

    * <span data-ttu-id="8919c-126">**Diagnostiquer et résoudre des problèmes** est un dépanneur en libre-service.</span><span class="sxs-lookup"><span data-stu-id="8919c-126">**Diagnose and solve problems** is a self-service troubleshooter.</span></span>
    * <span data-ttu-id="8919c-127">**Application Insights** est pour le profilage des performances et le comportement de l’application, et est discuté plus tard dans cette section.</span><span class="sxs-lookup"><span data-stu-id="8919c-127">**Application Insights** is for profiling performance and app behavior, and is discussed later in this section.</span></span>
    * <span data-ttu-id="8919c-128">**App Service Advisor** fait des recommandations pour régler votre expérience d’application.</span><span class="sxs-lookup"><span data-stu-id="8919c-128">**App Service Advisor** makes recommendations to tune your app experience.</span></span>

## <a name="advanced-monitoring"></a><span data-ttu-id="8919c-129">Surveillance avancée</span><span class="sxs-lookup"><span data-stu-id="8919c-129">Advanced monitoring</span></span>

<span data-ttu-id="8919c-130">[Azure Monitor](/azure/monitoring-and-diagnostics/) est le service centralisé de surveillance de toutes les mesures et de réglage des alertes sur les services Azure.</span><span class="sxs-lookup"><span data-stu-id="8919c-130">[Azure Monitor](/azure/monitoring-and-diagnostics/) is the centralized service for monitoring all metrics and setting alerts across Azure services.</span></span> <span data-ttu-id="8919c-131">Au sein d’Azure Monitor, les administrateurs peuvent suivre les performances et identifier les tendances.</span><span class="sxs-lookup"><span data-stu-id="8919c-131">Within Azure Monitor, administrators can granularly track performance and identify trends.</span></span> <span data-ttu-id="8919c-132">Chaque service Azure offre son propre [ensemble de mesures](/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftwebsites-excluding-functions) à Azure Monitor.</span><span class="sxs-lookup"><span data-stu-id="8919c-132">Each Azure service offers its own [set of metrics](/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftwebsites-excluding-functions) to Azure Monitor.</span></span>

## <a name="profile-with-application-insights"></a><span data-ttu-id="8919c-133">Profil avec Insights d’application</span><span class="sxs-lookup"><span data-stu-id="8919c-133">Profile with Application Insights</span></span>

<span data-ttu-id="8919c-134">[Application Insights](/azure/application-insights/app-insights-overview) est un service Azure pour analyser les performances et la stabilité des applications Web et la façon dont les utilisateurs les utilisent.</span><span class="sxs-lookup"><span data-stu-id="8919c-134">[Application Insights](/azure/application-insights/app-insights-overview) is an Azure service for analyzing the performance and stability of web apps and how users use them.</span></span> <span data-ttu-id="8919c-135">Les données d’Application Insights sont plus larges et plus profondes que celle d’Azure Monitor.</span><span class="sxs-lookup"><span data-stu-id="8919c-135">The data from Application Insights is broader and deeper than that of Azure Monitor.</span></span> <span data-ttu-id="8919c-136">Les données peuvent fournir aux développeurs et aux administrateurs des informations clés pour améliorer les applications.</span><span class="sxs-lookup"><span data-stu-id="8919c-136">The data can provide developers and administrators with key information for improving apps.</span></span> <span data-ttu-id="8919c-137">Les aperçus d’applications peuvent être ajoutés à une ressource Azure App Service sans modification de code.</span><span class="sxs-lookup"><span data-stu-id="8919c-137">Application Insights can be added to an Azure App Service resource without code changes.</span></span>

1. <span data-ttu-id="8919c-138">Ouvrez le [portail Azure,](https://portal.azure.com)puis naviguez vers le \*service\<d’applications mywebapp unique_number.\> \*</span><span class="sxs-lookup"><span data-stu-id="8919c-138">Open the [Azure portal](https://portal.azure.com), and then navigate to the *mywebapp\<unique_number\>* App Service.</span></span>
1. <span data-ttu-id="8919c-139">À partir de l’onglet **Aperçu,** cliquez sur la tuile **Application Insights.**</span><span class="sxs-lookup"><span data-stu-id="8919c-139">From the **Overview** tab, click the **Application Insights** tile.</span></span>

    ![Vignette Application Insights](./media/monitoring/app-insights.png)

1. <span data-ttu-id="8919c-141">Sélectionnez le nouveau bouton Radio **De ressource Créer.**</span><span class="sxs-lookup"><span data-stu-id="8919c-141">Select the **Create new resource** radio button.</span></span> <span data-ttu-id="8919c-142">Utilisez le nom de ressource par défaut et sélectionnez l’emplacement de la ressource Application Insights.</span><span class="sxs-lookup"><span data-stu-id="8919c-142">Use the default resource name, and select the location for the Application Insights resource.</span></span> <span data-ttu-id="8919c-143">L’emplacement n’a pas besoin de correspondre à celui de votre application web.</span><span class="sxs-lookup"><span data-stu-id="8919c-143">The location doesn't need to match that of your web app.</span></span>

    ![Configuration d’applications Insights](./media/monitoring/new-app-insights.png)

1. <span data-ttu-id="8919c-145">Pour **Runtime/Framework**, sélectionnez **ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="8919c-145">For **Runtime/Framework**, select **ASP.NET Core**.</span></span> <span data-ttu-id="8919c-146">Acceptez les paramètres par défaut.</span><span class="sxs-lookup"><span data-stu-id="8919c-146">Accept the default settings.</span></span>
1. <span data-ttu-id="8919c-147">Sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="8919c-147">Select **OK**.</span></span> <span data-ttu-id="8919c-148">Si invité à confirmer, sélectionnez **Continuer**.</span><span class="sxs-lookup"><span data-stu-id="8919c-148">If prompted to confirm, select **Continue**.</span></span>
1. <span data-ttu-id="8919c-149">Une fois la ressource créée, cliquez sur le nom de la ressource Application Insights pour naviguer directement vers la page Applications Insights.</span><span class="sxs-lookup"><span data-stu-id="8919c-149">After the resource has been created, click the name of Application Insights resource to navigate directly to the Application Insights page.</span></span>

    ![La nouvelle ressource d’applications Insights est prête](./media/monitoring/new-app-insights-done.png)

<span data-ttu-id="8919c-151">Au fur et à mesure que l’application est utilisée, les données s’accumulent.</span><span class="sxs-lookup"><span data-stu-id="8919c-151">As the app is used, data accumulates.</span></span> <span data-ttu-id="8919c-152">Sélectionnez **Refresh** pour recharger la lame avec de nouvelles données.</span><span class="sxs-lookup"><span data-stu-id="8919c-152">Select **Refresh** to reload the blade with new data.</span></span>

![Onglet d’aperçu d’applications Insights](./media/monitoring/app-insights-overview.png)

<span data-ttu-id="8919c-154">Application Insights fournit des informations utiles côté serveur sans configuration supplémentaire.</span><span class="sxs-lookup"><span data-stu-id="8919c-154">Application Insights provides useful server-side information with no additional configuration.</span></span> <span data-ttu-id="8919c-155">Pour obtenir le plus de valeur de Appa idées, [instrumentez votre application avec l’Application Insights SDK](/azure/application-insights/app-insights-asp-net-core).</span><span class="sxs-lookup"><span data-stu-id="8919c-155">To get the most value from Application Insights, [instrument your app with the Application Insights SDK](/azure/application-insights/app-insights-asp-net-core).</span></span> <span data-ttu-id="8919c-156">Lorsqu’il est correctement configuré, le service assure une surveillance de bout en bout sur le serveur Web et le navigateur, y compris les performances côté client.</span><span class="sxs-lookup"><span data-stu-id="8919c-156">When properly configured, the service provides end-to-end monitoring across the web server and browser, including client-side performance.</span></span> <span data-ttu-id="8919c-157">Pour plus d’informations, consultez la [documentation application Insights](/azure/application-insights/app-insights-overview).</span><span class="sxs-lookup"><span data-stu-id="8919c-157">For more information, see the [Application Insights documentation](/azure/application-insights/app-insights-overview).</span></span>

## <a name="logging"></a><span data-ttu-id="8919c-158">Journalisation</span><span class="sxs-lookup"><span data-stu-id="8919c-158">Logging</span></span>

<span data-ttu-id="8919c-159">Les journaux de serveur Web et d’applications sont désactivés par défaut dans Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="8919c-159">Web server and app logs are disabled by default in Azure App Service.</span></span> <span data-ttu-id="8919c-160">Activez les journaux avec les étapes suivantes :</span><span class="sxs-lookup"><span data-stu-id="8919c-160">Enable the logs with the following steps:</span></span>

1. <span data-ttu-id="8919c-161">Ouvrez le [portail Azure](https://portal.azure.com)et naviguez vers le \*service d’applications\<mywebapp unique_number.\> \*</span><span class="sxs-lookup"><span data-stu-id="8919c-161">Open the [Azure portal](https://portal.azure.com), and navigate to the *mywebapp\<unique_number\>* App Service.</span></span>
1. <span data-ttu-id="8919c-162">Dans le menu à gauche, faites défiler vers le bas à la section **Surveillance.**</span><span class="sxs-lookup"><span data-stu-id="8919c-162">In the menu to the left, scroll down to the **Monitoring** section.</span></span> <span data-ttu-id="8919c-163">Sélectionnez **les journaux Diagnostics**.</span><span class="sxs-lookup"><span data-stu-id="8919c-163">Select **Diagnostics logs**.</span></span>

    ![Lien de journaux diagnostiques](./media/monitoring/logging.png)

1. <span data-ttu-id="8919c-165">Activez **l’enregistrement des applications (Filesystem)**.</span><span class="sxs-lookup"><span data-stu-id="8919c-165">Turn on **Application Logging (Filesystem)**.</span></span> <span data-ttu-id="8919c-166">En cas d’ense de compte, cliquez sur la case pour installer les extensions pour activer la connexion de l’application dans l’application web.</span><span class="sxs-lookup"><span data-stu-id="8919c-166">If prompted, click the box to install the extensions to enable app logging in the web app.</span></span>
1. <span data-ttu-id="8919c-167">Définissez **la connexion du serveur Web** au système de **fichiers**.</span><span class="sxs-lookup"><span data-stu-id="8919c-167">Set **Web server logging** to **File System**.</span></span>
1. <span data-ttu-id="8919c-168">Entrez la **période de rétention** en quelques jours.</span><span class="sxs-lookup"><span data-stu-id="8919c-168">Enter the **Retention Period** in days.</span></span> <span data-ttu-id="8919c-169">Par exemple, 30.</span><span class="sxs-lookup"><span data-stu-id="8919c-169">For example, 30.</span></span>
1. <span data-ttu-id="8919c-170">Cliquez sur **Enregistrer**.</span><span class="sxs-lookup"><span data-stu-id="8919c-170">Click **Save**.</span></span>

<span data-ttu-id="8919c-171">ASP.NET les journaux core et web server (App Service) sont générés pour l’application web.</span><span class="sxs-lookup"><span data-stu-id="8919c-171">ASP.NET Core and web server (App Service) logs are generated for the web app.</span></span> <span data-ttu-id="8919c-172">Ils peuvent être téléchargés à l’aide des informations FTP/FTPS affichées.</span><span class="sxs-lookup"><span data-stu-id="8919c-172">They can be downloaded using the FTP/FTPS information displayed.</span></span> <span data-ttu-id="8919c-173">Le mot de passe est le même que les informations d’identification de déploiement créées plus tôt dans ce guide.</span><span class="sxs-lookup"><span data-stu-id="8919c-173">The password is the same as the deployment credentials created earlier in this guide.</span></span> <span data-ttu-id="8919c-174">Les journaux peuvent être [diffusés directement sur votre machine locale avec PowerShell ou Azure CLI](/azure/app-service/web-sites-enable-diagnostic-log#download).</span><span class="sxs-lookup"><span data-stu-id="8919c-174">The logs can be [streamed directly to your local machine with PowerShell or Azure CLI](/azure/app-service/web-sites-enable-diagnostic-log#download).</span></span> <span data-ttu-id="8919c-175">Les journaux peuvent également être [consultés dans Application Insights](/azure/app-service/web-sites-enable-diagnostic-log#how-to-view-logs-in-application-insights).</span><span class="sxs-lookup"><span data-stu-id="8919c-175">Logs can also be [viewed in Application Insights](/azure/app-service/web-sites-enable-diagnostic-log#how-to-view-logs-in-application-insights).</span></span>

## <a name="log-streaming"></a><span data-ttu-id="8919c-176">Diffusion de journaux</span><span class="sxs-lookup"><span data-stu-id="8919c-176">Log streaming</span></span>

<span data-ttu-id="8919c-177">Les journaux d’applications et de serveurs Web peuvent être diffusés en temps réel via le portail.</span><span class="sxs-lookup"><span data-stu-id="8919c-177">App and web server logs can be streamed in real time through the portal.</span></span>

1. <span data-ttu-id="8919c-178">Ouvrez le [portail Azure](https://portal.azure.com)et naviguez vers le \*service d’applications\<mywebapp unique_number.\> \*</span><span class="sxs-lookup"><span data-stu-id="8919c-178">Open the [Azure portal](https://portal.azure.com), and navigate to the *mywebapp\<unique_number\>* App Service.</span></span>
1. <span data-ttu-id="8919c-179">Dans le menu à gauche, faites défiler vers le bas à la section **Surveillance** et sélectionnez **le flux de journal**.</span><span class="sxs-lookup"><span data-stu-id="8919c-179">In the menu to the left, scroll down to the **Monitoring** section and select **Log stream**.</span></span>

    ![Capture d’écran affichant le lien de flux de journal](./media/monitoring/log-stream.png)

<span data-ttu-id="8919c-181">Les journaux peuvent également être [diffusés via Azure CLI ou Azure PowerShell](/azure/app-service/web-sites-enable-diagnostic-log#streamlogs), y compris via la coquille Cloud.</span><span class="sxs-lookup"><span data-stu-id="8919c-181">Logs can also be [streamed via Azure CLI or Azure PowerShell](/azure/app-service/web-sites-enable-diagnostic-log#streamlogs), including through the Cloud Shell.</span></span>

## <a name="alerts"></a><span data-ttu-id="8919c-182">Alertes</span><span class="sxs-lookup"><span data-stu-id="8919c-182">Alerts</span></span>

<span data-ttu-id="8919c-183">Azure Monitor fournit également des [alertes](/azure/monitoring-and-diagnostics/insights-alerts-portal) en temps réel basées sur des mesures, des événements administratifs et d’autres critères.</span><span class="sxs-lookup"><span data-stu-id="8919c-183">Azure Monitor also provides [real time alerts](/azure/monitoring-and-diagnostics/insights-alerts-portal) based on metrics, administrative events, and other criteria.</span></span>

> <span data-ttu-id="8919c-184">*Remarque : L’alerte actuelle sur les paramètres des applications Web n’est disponible que dans le service Alerts (classique).*</span><span class="sxs-lookup"><span data-stu-id="8919c-184">*Note: Currently alerting on web app metrics is only available in the Alerts (classic) service.*</span></span>

<span data-ttu-id="8919c-185">Le [service Alerts (classique)](/azure/monitoring-and-diagnostics/monitor-quick-resource-metric-alert-portal) peut être trouvé dans Azure Monitor ou dans la section **Surveillance** des paramètres app Service.</span><span class="sxs-lookup"><span data-stu-id="8919c-185">The [Alerts (classic) service](/azure/monitoring-and-diagnostics/monitor-quick-resource-metric-alert-portal) can be found in Azure Monitor or under the **Monitoring** section of the App Service settings.</span></span>

![Liaison Alertes (classique)](./media/monitoring/alerts.png)

## <a name="live-debugging"></a><span data-ttu-id="8919c-187">Débogage en direct</span><span class="sxs-lookup"><span data-stu-id="8919c-187">Live debugging</span></span>

<span data-ttu-id="8919c-188">Azure App Service peut être [déboqué à distance avec Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug) lorsque les journaux ne fournissent pas suffisamment d’informations.</span><span class="sxs-lookup"><span data-stu-id="8919c-188">Azure App Service can be [debugged remotely with Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug) when logs don't provide enough information.</span></span> <span data-ttu-id="8919c-189">Cependant, le débogage à distance nécessite que l’application soit compilée avec des symboles de déboguer.</span><span class="sxs-lookup"><span data-stu-id="8919c-189">However, remote debugging requires the app to be compiled with debug symbols.</span></span> <span data-ttu-id="8919c-190">Le débogage ne devrait pas se faire en production, sauf en dernier recours.</span><span class="sxs-lookup"><span data-stu-id="8919c-190">Debugging shouldn't be done in production, except as a last resort.</span></span>

## <a name="conclusion"></a><span data-ttu-id="8919c-191">Conclusion</span><span class="sxs-lookup"><span data-stu-id="8919c-191">Conclusion</span></span>

<span data-ttu-id="8919c-192">Dans cette section, vous avez accompli les tâches suivantes :</span><span class="sxs-lookup"><span data-stu-id="8919c-192">In this section, you completed the following tasks:</span></span>

* <span data-ttu-id="8919c-193">Trouvez des données de surveillance et de dépannage de base dans le portail Azure</span><span class="sxs-lookup"><span data-stu-id="8919c-193">Find basic monitoring and troubleshooting data in the Azure portal</span></span>
* <span data-ttu-id="8919c-194">Découvrez comment Azure Monitor fournit un aperçu plus approfondi des mesures sur tous les services Azure</span><span class="sxs-lookup"><span data-stu-id="8919c-194">Learn how Azure Monitor provides a deeper look at metrics across all Azure services</span></span>
* <span data-ttu-id="8919c-195">Connectez l’application web avec Application Insights pour le profilage d’applications</span><span class="sxs-lookup"><span data-stu-id="8919c-195">Connect the web app with Application Insights for app profiling</span></span>
* <span data-ttu-id="8919c-196">Activez l’enregistrement et apprenez où télécharger des journaux</span><span class="sxs-lookup"><span data-stu-id="8919c-196">Turn on logging and learn where to download logs</span></span>
* <span data-ttu-id="8919c-197">Journaux de flux en temps réel</span><span class="sxs-lookup"><span data-stu-id="8919c-197">Stream logs in real time</span></span>
* <span data-ttu-id="8919c-198">Découvrez où mettre en place des alertes</span><span class="sxs-lookup"><span data-stu-id="8919c-198">Learn where to set up alerts</span></span>
* <span data-ttu-id="8919c-199">Découvrez les applications Web Azure App Service de débogage à distance.</span><span class="sxs-lookup"><span data-stu-id="8919c-199">Learn about remote debugging Azure App Service web apps.</span></span>

## <a name="additional-reading"></a><span data-ttu-id="8919c-200">Documentation supplémentaire</span><span class="sxs-lookup"><span data-stu-id="8919c-200">Additional reading</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [<span data-ttu-id="8919c-201">Surveiller les performances de l’application web Azure avec Application Insights</span><span class="sxs-lookup"><span data-stu-id="8919c-201">Monitor Azure web app performance with Application Insights</span></span>](/azure/application-insights/app-insights-azure-web-apps)
* [<span data-ttu-id="8919c-202">Activer la journalisation des diagnostics pour les applications web dans Azure App Service</span><span class="sxs-lookup"><span data-stu-id="8919c-202">Enable diagnostics logging for web apps in Azure App Service</span></span>](/azure/app-service/web-sites-enable-diagnostic-log)
* [<span data-ttu-id="8919c-203">Dépanner une application web dans le Service d’application Microsoft Azure à l’aide de Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8919c-203">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="8919c-204">Créer des alertes de métriques classiques dans Azure Monitor pour les services Azure - Portail Azure</span><span class="sxs-lookup"><span data-stu-id="8919c-204">Create classic metric alerts in Azure Monitor for Azure services - Azure portal</span></span>](/azure/monitoring-and-diagnostics/insights-alerts-portal)
