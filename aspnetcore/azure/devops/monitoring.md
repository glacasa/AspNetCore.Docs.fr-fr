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
# <a name="monitor-and-debug"></a>Surveiller et débouger

Après avoir déployé l’application et construit un pipeline DevOps, il est important de comprendre comment surveiller et dépanner l’application.

Dans cette section, vous accomplirez les tâches suivantes :

* Trouvez des données de surveillance et de dépannage de base dans le portail Azure
* Découvrez comment Azure Monitor fournit un aperçu plus approfondi des mesures sur tous les services Azure
* Connectez l’application web avec Application Insights pour le profilage d’applications
* Activez l’enregistrement et apprenez où télécharger des journaux
* Journaux de flux en temps réel
* Découvrez où mettre en place des alertes
* Découvrez les applications Web Azure App Service de débogage à distance.

## <a name="basic-monitoring-and-troubleshooting"></a>Surveillance et dépannage de base

Les applications Web App Service sont facilement surveillées en temps réel. Le portail Azure rend des mesures dans des graphiques et des graphiques faciles à comprendre.

1. Ouvrez le [portail Azure,](https://portal.azure.com)puis naviguez vers le *service\<d’applications mywebapp unique_number.\> *

1. **L’onglet Aperçu** affiche des informations utiles « en un coup d’œil », y compris des graphiques affichant des mesures récentes.

    ![Capture d’écran affichant le panneau d’aperçu](./media/monitoring/overview.png)

    * **Http 5xx**: Comptez des erreurs côté serveur, généralement des exceptions dans ASP.NET code Core.
    * **Données :** Entrée de données entrant dans votre application web.
    * **Données Out**: Les données s’éloignent de votre application web vers les clients.
    * **Demandes**: Comptez des demandes HTTP.
    * **Temps de réponse moyen**: Temps moyen pour l’application web pour répondre aux demandes HTTP.

    Plusieurs outils en libre-service pour le dépannage et l’optimisation se trouvent également sur cette page.

    ![Capture d’écran montrant des outils en libre-service](./media/monitoring/wizards.png)

    * **Diagnostiquer et résoudre des problèmes** est un dépanneur en libre-service.
    * **Application Insights** est pour le profilage des performances et le comportement de l’application, et est discuté plus tard dans cette section.
    * **App Service Advisor** fait des recommandations pour régler votre expérience d’application.

## <a name="advanced-monitoring"></a>Surveillance avancée

[Azure Monitor](/azure/monitoring-and-diagnostics/) est le service centralisé de surveillance de toutes les mesures et de réglage des alertes sur les services Azure. Au sein d’Azure Monitor, les administrateurs peuvent suivre les performances et identifier les tendances. Chaque service Azure offre son propre [ensemble de mesures](/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftwebsites-excluding-functions) à Azure Monitor.

## <a name="profile-with-application-insights"></a>Profil avec Insights d’application

[Application Insights](/azure/application-insights/app-insights-overview) est un service Azure pour analyser les performances et la stabilité des applications Web et la façon dont les utilisateurs les utilisent. Les données d’Application Insights sont plus larges et plus profondes que celle d’Azure Monitor. Les données peuvent fournir aux développeurs et aux administrateurs des informations clés pour améliorer les applications. Les aperçus d’applications peuvent être ajoutés à une ressource Azure App Service sans modification de code.

1. Ouvrez le [portail Azure,](https://portal.azure.com)puis naviguez vers le *service\<d’applications mywebapp unique_number.\> *
1. À partir de l’onglet **Aperçu,** cliquez sur la tuile **Application Insights.**

    ![Vignette Application Insights](./media/monitoring/app-insights.png)

1. Sélectionnez le nouveau bouton Radio **De ressource Créer.** Utilisez le nom de ressource par défaut et sélectionnez l’emplacement de la ressource Application Insights. L’emplacement n’a pas besoin de correspondre à celui de votre application web.

    ![Configuration d’applications Insights](./media/monitoring/new-app-insights.png)

1. Pour **Runtime/Framework**, sélectionnez **ASP.NET Core**. Acceptez les paramètres par défaut.
1. Sélectionnez **OK**. Si invité à confirmer, sélectionnez **Continuer**.
1. Une fois la ressource créée, cliquez sur le nom de la ressource Application Insights pour naviguer directement vers la page Applications Insights.

    ![La nouvelle ressource d’applications Insights est prête](./media/monitoring/new-app-insights-done.png)

Au fur et à mesure que l’application est utilisée, les données s’accumulent. Sélectionnez **Refresh** pour recharger la lame avec de nouvelles données.

![Onglet d’aperçu d’applications Insights](./media/monitoring/app-insights-overview.png)

Application Insights fournit des informations utiles côté serveur sans configuration supplémentaire. Pour obtenir le plus de valeur de Appa idées, [instrumentez votre application avec l’Application Insights SDK](/azure/application-insights/app-insights-asp-net-core). Lorsqu’il est correctement configuré, le service assure une surveillance de bout en bout sur le serveur Web et le navigateur, y compris les performances côté client. Pour plus d’informations, consultez la [documentation application Insights](/azure/application-insights/app-insights-overview).

## <a name="logging"></a>Journalisation

Les journaux de serveur Web et d’applications sont désactivés par défaut dans Azure App Service. Activez les journaux avec les étapes suivantes :

1. Ouvrez le [portail Azure](https://portal.azure.com)et naviguez vers le *service d’applications\<mywebapp unique_number.\> *
1. Dans le menu à gauche, faites défiler vers le bas à la section **Surveillance.** Sélectionnez **les journaux Diagnostics**.

    ![Lien de journaux diagnostiques](./media/monitoring/logging.png)

1. Activez **l’enregistrement des applications (Filesystem)**. En cas d’ense de compte, cliquez sur la case pour installer les extensions pour activer la connexion de l’application dans l’application web.
1. Définissez **la connexion du serveur Web** au système de **fichiers**.
1. Entrez la **période de rétention** en quelques jours. Par exemple, 30.
1. Cliquez sur **Enregistrer**.

ASP.NET les journaux core et web server (App Service) sont générés pour l’application web. Ils peuvent être téléchargés à l’aide des informations FTP/FTPS affichées. Le mot de passe est le même que les informations d’identification de déploiement créées plus tôt dans ce guide. Les journaux peuvent être [diffusés directement sur votre machine locale avec PowerShell ou Azure CLI](/azure/app-service/web-sites-enable-diagnostic-log#download). Les journaux peuvent également être [consultés dans Application Insights](/azure/app-service/web-sites-enable-diagnostic-log#how-to-view-logs-in-application-insights).

## <a name="log-streaming"></a>Diffusion de journaux

Les journaux d’applications et de serveurs Web peuvent être diffusés en temps réel via le portail.

1. Ouvrez le [portail Azure](https://portal.azure.com)et naviguez vers le *service d’applications\<mywebapp unique_number.\> *
1. Dans le menu à gauche, faites défiler vers le bas à la section **Surveillance** et sélectionnez **le flux de journal**.

    ![Capture d’écran affichant le lien de flux de journal](./media/monitoring/log-stream.png)

Les journaux peuvent également être [diffusés via Azure CLI ou Azure PowerShell](/azure/app-service/web-sites-enable-diagnostic-log#streamlogs), y compris via la coquille Cloud.

## <a name="alerts"></a>Alertes

Azure Monitor fournit également des [alertes](/azure/monitoring-and-diagnostics/insights-alerts-portal) en temps réel basées sur des mesures, des événements administratifs et d’autres critères.

> *Remarque : L’alerte actuelle sur les paramètres des applications Web n’est disponible que dans le service Alerts (classique).*

Le [service Alerts (classique)](/azure/monitoring-and-diagnostics/monitor-quick-resource-metric-alert-portal) peut être trouvé dans Azure Monitor ou dans la section **Surveillance** des paramètres app Service.

![Liaison Alertes (classique)](./media/monitoring/alerts.png)

## <a name="live-debugging"></a>Débogage en direct

Azure App Service peut être [déboqué à distance avec Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug) lorsque les journaux ne fournissent pas suffisamment d’informations. Cependant, le débogage à distance nécessite que l’application soit compilée avec des symboles de déboguer. Le débogage ne devrait pas se faire en production, sauf en dernier recours.

## <a name="conclusion"></a>Conclusion

Dans cette section, vous avez accompli les tâches suivantes :

* Trouvez des données de surveillance et de dépannage de base dans le portail Azure
* Découvrez comment Azure Monitor fournit un aperçu plus approfondi des mesures sur tous les services Azure
* Connectez l’application web avec Application Insights pour le profilage d’applications
* Activez l’enregistrement et apprenez où télécharger des journaux
* Journaux de flux en temps réel
* Découvrez où mettre en place des alertes
* Découvrez les applications Web Azure App Service de débogage à distance.

## <a name="additional-reading"></a>Documentation supplémentaire

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [Surveiller les performances de l’application web Azure avec Application Insights](/azure/application-insights/app-insights-azure-web-apps)
* [Activer la journalisation des diagnostics pour les applications web dans Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log)
* [Dépanner une application web dans le Service d’application Microsoft Azure à l’aide de Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [Créer des alertes de métriques classiques dans Azure Monitor pour les services Azure - Portail Azure](/azure/monitoring-and-diagnostics/insights-alerts-portal)
