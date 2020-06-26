---
title: Déployer des applications ASP.NET Core sur Azure App Service
author: bradygaster
description: Cet article contient des liens vers des ressources d’hébergement et de déploiement Azure.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 12/16/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/azure-apps/index
ms.openlocfilehash: 9ffeebbf8125ddac5d6e621e411c4e86c5bd34b1
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85399307"
---
# <a name="deploy-aspnet-core-apps-to-azure-app-service"></a>Déployer des applications ASP.NET Core sur Azure App Service

[Azure App Service](https://azure.microsoft.com/services/app-service/) est un [service de plateforme de cloud computing Microsoft](https://azure.microsoft.com/) qui permet d’héberger des applications web, notamment ASP.NET Core.

## <a name="useful-resources"></a>Ressources utiles

[App Service Documentation](/azure/app-service/) héberge la documentation, les tutoriels, les exemples, les guides pratiques et d’autres ressources Azure. Voici deux didacticiels importants qui abordent l’hébergement d’applications ASP.NET Core :

[Créer une application web ASP.NET Core dans Azure](/azure/app-service/app-service-web-get-started-dotnet)  
Utilisez Visual Studio pour créer et déployer une application web ASP.NET Core dans Azure App Service sur Windows.

[Créer une application ASP.NET Core dans App Service sur Linux](/azure/app-service/containers/quickstart-dotnetcore)  
Utilisez la ligne de commande pour créer et déployer une application web ASP.NET Core dans Azure App Service sur Linux.

Consultez le [tableau de bord ASP.net Core sur app service](https://aspnetcoreon.azurewebsites.net/) pour connaître la version de ASP.net Core disponible sur Azure App service.

Abonnez-vous au référentiel d' [annonces App service](https://github.com/Azure/app-service-announcements/) et surveillez les problèmes. L’équipe App Service publie régulièrement les annonces et les scénarios arrivant dans App Service.

Les articles suivants sont disponibles dans la documentation d’ASP.NET Core :

<xref:tutorials/publish-to-azure-webapp-using-vs>  
Découvrez comment publier une application ASP.NET Core sur Azure App Service à l’aide de Visual Studio.

<xref:host-and-deploy/azure-apps/azure-continuous-deployment>  
Découvrez comment créer une application web ASP.NET Core à l’aide de Visual Studio et comment la déployer sur Azure App Service en utilisant Git pour le déploiement continu.

[Créer votre premier pipeline](/azure/devops/pipelines/get-started-yaml)  
Configurez une build CI pour une application ASP.NET Core, puis créez une version de déploiement continu sur Azure App Service.

[Bac à sable Azure Web App](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)  
Découvrez les limitations d’exécution du runtime Azure App Service appliquées par la plateforme Azure Apps.

<xref:test/troubleshoot>  
Comprenez et résolvez les avertissements et les erreurs avec les projets ASP.NET Core.

## <a name="application-configuration"></a>Configuration de l’application

### <a name="platform"></a>Plateforme

L’architecture de la plateforme (x86/x64) d’une application App Services est définie dans les paramètres de l’application dans le portail Azure pour les applications hébergées sur un niveau d’hébergement de calcul (de base) de série A. Vérifiez que les paramètres de publication de l’application (par exemple, dans le [profil de publication Visual Studio (. pubxml)](xref:host-and-deploy/visual-studio-publish-profiles)) correspondent au paramètre dans la configuration de service de l’application dans le portail Azure.

::: moniker range=">= aspnetcore-2.2"

Des runtimes pour les applications 64 bits (x64) et 32 bits (x 86) sont présents sur Azure App Service. Le [SDK .NET Core](/dotnet/core/sdk) disponible sur App Service est 32 bits, mais vous pouvez déployer des applications 64 bits crées localement en utilisant la console [Kudu](https://github.com/projectkudu/kudu/wiki) ou le processus de publication de Visual Studio. Pour plus d’informations, consultez la section [Publier et déployer l’application](#publish-and-deploy-the-app).

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Concernant les applications avec des dépendances natives, des runtimes pour les applications 32 bits (x86) sont présents sur Azure App Service. Le [SDK .NET Core](/dotnet/core/sdk) disponible sur App Service est 32 bits.

::: moniker-end

Pour plus d’informations sur les composants .NET Core Framework et les méthodes de distribution, telles que les informations sur le Runtime .NET Core et le kit SDK .NET Core, consultez [à propos de .net Core : composition](/dotnet/core/about#composition).

### <a name="packages"></a>Paquets

Ajoutez les packages NuGet suivants pour fournir des fonctionnalités de journalisation automatique aux applications déployées sur Azure App Service :

* [Microsoft.AspNetCore.AzureAppServices.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServices.HostingStartup/) utilise [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration) pour intégrer la fonction d’éclairage ASP.NET Core dans Azure App Service. Les fonctionnalités de journalisation ajoutées sont fournies par le package `Microsoft.AspNetCore.AzureAppServicesIntegration`.
* [Microsoft.AspNetCore.AzureAppServicesIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServicesIntegration/) exécute [AddAzureWebAppDiagnostics](/dotnet/api/microsoft.extensions.logging.azureappservicesloggerfactoryextensions.addazurewebappdiagnostics) pour ajouter des fournisseurs de journalisation de diagnostics Azure App Service dans le package `Microsoft.Extensions.Logging.AzureAppServices`.
* [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/) fournit des implémentations de journaliseur pour prendre en charge les journaux de diagnostics et les fonctionnalités de streaming de journal Azure App Service.

Les packages précédents ne sont pas disponibles dans le [métapackage Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app). Les applications qui ciblent le .NET Framework ou référencent le métapackage `Microsoft.AspNetCore.App` doivent référencer explicitement les packages individuels dans le fichier projet de l’application.

## <a name="override-app-configuration-using-the-azure-portal"></a>Remplacer la configuration de l’application à l’aide du Portail Azure

::: moniker range=">= aspnetcore-3.0"

Les paramètres d’application dans le portail Azure vous permettent de définir des variables d’environnement pour l’application. Celles-ci peuvent être utilisées par le [Fournisseur de configuration des variables d’environnement](xref:fundamentals/configuration/index#environment-variables).

Quand un paramètre d’application est créé ou modifié dans le portail Azure et que le bouton **Enregistrer** est sélectionné, Azure App redémarre. La variable d’environnement est à la disposition de l’application après le redémarrage du service.

Quand une application utilise l' [hôte générique](xref:fundamentals/host/generic-host), les variables d’environnement sont chargées dans la configuration de l’application lorsque <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> est appelé pour générer l’hôte. Pour plus d’informations, voir <xref:fundamentals/host/generic-host> et [Fournisseur de configuration des variables d’environnement](xref:fundamentals/configuration/index#environment-variables).

::: moniker-end
::: moniker range="< aspnetcore-3.0"

Les paramètres d’application dans le portail Azure vous permettent de définir des variables d’environnement pour l’application. Celles-ci peuvent être utilisées par le [Fournisseur de configuration des variables d’environnement](xref:fundamentals/configuration/index#environment-variables-configuration-provider).

Quand un paramètre d’application est créé ou modifié dans le portail Azure et que le bouton **Enregistrer** est sélectionné, Azure App redémarre. La variable d’environnement est à la disposition de l’application après le redémarrage du service.

Quand une application utilise l' [hôte Web](xref:fundamentals/host/web-host), les variables d’environnement sont chargées dans la configuration de l’application lorsque <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> est appelé pour générer l’hôte. Pour plus d’informations, voir <xref:fundamentals/host/web-host> et [Fournisseur de configuration des variables d’environnement](xref:fundamentals/configuration/index#environment-variables-configuration-provider).

::: moniker-end

## <a name="proxy-server-and-load-balancer-scenarios"></a>Scénarios avec un serveur proxy et un équilibreur de charge

Le [middleware d’intégration IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components), qui configure le middleware des en-têtes transférés lors de l’hébergement [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), et le module ASP.NET Core sont configurés pour transférer le schéma (HTTP/HTTPS) et l’adresse IP distante d’où provient la requête. Une configuration supplémentaire peut être nécessaire pour les applications hébergées derrière des serveurs proxy et des équilibreurs de charge supplémentaires. Pour plus d’informations, consultez l’article [Configurer ASP.NET Core pour l’utilisation de serveurs proxy et d’équilibreurs de charge](xref:host-and-deploy/proxy-load-balancer).

## <a name="monitoring-and-logging"></a>Surveillance et journalisation

::: moniker range=">= aspnetcore-3.0"

Les applications ASP.NET Core déployées sur App Service reçoivent automatiquement une extension App Service, **Intégration de journalisation ASP.NET Core**. L’extension permet d’intégrer la journalisation dans les applications ASP.NET Core sur Azure App Service.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Les applications ASP.NET Core déployées sur App Service reçoivent automatiquement une extension App Service, **Extensions de journalisation ASP.NET Core**. L’extension permet d’intégrer la journalisation dans les applications ASP.NET Core sur Azure App Service.

::: moniker-end

Pour des informations de surveillance, de journalisation et de dépannage, consultez les articles suivants :

[Surveiller les applications dans Azure App Service](/azure/app-service/web-sites-monitor)  
Découvrez comment consulter les quotas et les métriques des applications et des plans App Service.

[Activer la journalisation des diagnostics pour les applications dans Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log)  
Découvrez comment activer et accéder à la journalisation des diagnostics pour les codes d’état HTTP, les requêtes en échec et les activités de serveur web.

<xref:fundamentals/error-handling>  
Découvrez les approches courantes permettant de gérer les erreurs dans les applications ASP.NET Core.

<xref:test/troubleshoot-azure-iis>  
Découvrez comment diagnostiquer les problèmes de déploiements Azure App Service avec les applications ASP.NET Core.

<xref:host-and-deploy/azure-iis-errors-reference>  
Découvrez les erreurs de configuration de déploiement courantes dans les applications hébergées par Azure App Service/IIS, ainsi que des conseils de dépannage.

## <a name="data-protection-key-ring-and-deployment-slots"></a>Porte-clés de Protection des données et emplacements de déploiement

Les [clés de Protection des données](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) sont persistantes dans le dossier *%HOME%\ASP.NET\DataProtection-Keys*. Ce dossier est alimenté par le stockage réseau et synchronisé sur tous les ordinateurs hébergeant l’application. Les clés ne sont pas protégées au repos. Ce dossier fournit le porte-clés à toutes les instances d’une application dans un seul emplacement de déploiement. Les emplacements de déploiement distincts, tels que Préproduction et Production, ne partagent pas de porte-clés.

Lors d’une permutation entre les emplacements de déploiement, aucun système utilisant la protection des données ne peut déchiffrer les données stockées à l’aide du porte-clés au sein de l’emplacement précédent. L’intergiciel (middleware) ASP.NET Cookie utilise la protection des données pour protéger ses cookies. Cela entraîne la déconnexion des utilisateurs des applications qui utilisent l’intergiciel ASP.NET Cookie standard. Pour une solution de porte-clés indépendante de l’emplacement, utilisez un fournisseur de porte-clés externe, tel que :

* Stockage Blob Azure
* Azure Key Vault
* Magasin SQL
* Le cache Redis

Pour plus d’informations, consultez <xref:security/data-protection/implementation/key-storage-providers>.
<a name="deploy-aspnet-core-preview-release-to-azure-app-service"></a>

## <a name="deploy-an-aspnet-core-app-that-uses-a-net-core-preview"></a>Déployer une application ASP.NET Core qui utilise une version préliminaire de .NET Core

Pour déployer une application qui utilise une version préliminaire de .NET Core, consultez les ressources suivantes. Ces approches sont également utilisées lorsque le runtime est disponible, mais que le kit de développement logiciel (SDK) n’a pas été installé sur Azure App Service.

* [Spécifier la version de kit SDK .NET Core à l’aide de Azure Pipelines](#specify-the-net-core-sdk-version-using-azure-pipelines)
* [Déployer une application de prévisualisation autonome](#deploy-a-self-contained-preview-app)
* [Utiliser Docker avec Web Apps pour conteneurs](#use-docker-with-web-apps-for-containers)
* [Installer l’extension de site de version Preview](#install-the-preview-site-extension)

Consultez le [tableau de bord ASP.net Core sur app service](https://aspnetcoreon.azurewebsites.net/) pour connaître la version de ASP.net Core disponible sur Azure App service.

### <a name="specify-the-net-core-sdk-version-using-azure-pipelines"></a>Spécifier la version de kit SDK .NET Core à l’aide de Azure Pipelines

Utilisez [Azure App service scénarios ci/CD](/azure/app-service/deploy-continuous-deployment) pour configurer une build d’intégration continue avec Azure DevOps. Une fois la build Azure DevOps créée, vous pouvez éventuellement configurer la build pour qu’elle utilise une version spécifique du kit de développement logiciel (SDK). 

#### <a name="specify-the-net-core-sdk-version"></a>Spécifier la version de kit SDK .NET Core

Lorsque vous utilisez le centre de déploiement App service pour créer une build Azure DevOps, le pipeline de build par défaut comprend des étapes pour `Restore` ,, `Build` `Test` et `Publish` . Pour spécifier la version du kit de développement logiciel (SDK), cliquez sur le bouton **Ajouter (+)** dans la liste des travaux de l’agent pour ajouter une nouvelle étape. Recherchez **Kit SDK .net Core** dans la barre de recherche. 

![Ajouter l’étape kit SDK .NET Core](index/add-sdk-step.png)

Déplacez l’étape dans la première position de la build afin que les étapes qui suivent utilisent la version spécifiée du kit SDK .NET Core. Spécifiez la version du kit SDK .NET Core. Dans cet exemple, le kit de développement logiciel (SDK) a la valeur `3.0.100` .

![Étape du SDK terminée](index/sdk-step-first-place.png)

Pour publier un [Déploiement autonome (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd), configurez la SCD à l' `Publish` étape et fournissez l' [identificateur de Runtime (RID)](/dotnet/core/rid-catalog).

![Publication autonome](index/self-contained.png)

### <a name="deploy-a-self-contained-preview-app"></a>Déployer une application en préversion autonome

Un [déploiement autonome (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) qui cible une préversion runtime exécute le runtime de la préversion dans le déploiement.

Pendant le déploiement d’une application autonome :

* Le site dans Azure App Service ne requiert pas l’[extension du site de préversion](#install-the-preview-site-extension).
* L’application doit être publiée suivant une autre approche que dans le cadre de la publication d’un [déploiement en fonction de l’infrastructure (FDD)](/dotnet/core/deploying#framework-dependent-deployments-fdd).

Suivez les instructions de la section [Déployer l’application autonome](#deploy-the-app-self-contained).

### <a name="use-docker-with-web-apps-for-containers"></a>Utiliser Docker avec Web Apps pour conteneurs

[Docker Hub](https://hub.docker.com/r/microsoft/aspnetcore/) contient les dernières images de Docker en préversion. Les images peuvent être utilisées comme images de base. Utilisez l’image pour effectuer un déploiement sur Web Apps pour conteneurs normalement.

### <a name="install-the-preview-site-extension"></a>Installer l’extension de site de version Preview

Si un problème se produit à l’aide de l’extension de site en version préliminaire, ouvrez un [problème dotnet/AspNetCore](https://github.com/dotnet/AspNetCore/issues).

1. Dans le portail Azure, accédez à App Service.
1. Sélectionnez l’application web.
1. Tapez « ex » dans la zone de recherche pour filtrer sur les « Extensions » ou faites défiler la liste outils de gestion.
1. Sélectionnez **Extensions**.
1. Sélectionnez **Ajouter**.
1. Sélectionnez l’extension **ASP.NET Core {X.Y} ({x64|x86}) Runtime** dans la liste, où `{X.Y}` correspond à la préversion d’ASP.NET Core et `{x64|x86}` spécifie la plateforme.
1. Sélectionnez **OK** pour accepter les conditions légales.
1. Sélectionnez **OK** pour installer l’extension.

Une fois l’opération effectuée, la dernière préversion de .NET Core est installée. Vérifiez l’installation :

1. Sélectionnez **Outils avancés**.
1. Sélectionnez **Go** dans **Outils avancés**.
1. Sélectionnez l’élément de menu PowerShell de la **console de débogage**  >  **PowerShell** .
1. À l’invite PowerShell, exécutez la commande suivante. Remplacez `{X.Y}` par la version du runtime ASP.NET Core et `{PLATFORM}` par la plateforme dans la commande :

   ```powershell
   Test-Path D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.{PLATFORM}\
   ```

   La commande retourne `True` quand le runtime de la préversion x64 est installée.

> [!NOTE]
> L’architecture de la plateforme (x86/x64) d’une application App Services est définie dans les paramètres de l’application dans le portail Azure pour les applications hébergées sur un niveau d’hébergement de calcul (de base) de série A. Vérifiez que les paramètres de publication de l’application (par exemple, dans le [profil de publication Visual Studio (. pubxml)](xref:host-and-deploy/visual-studio-publish-profiles)) correspondent au paramètre dans la configuration de service de l’application dans la portail Azure.
>
> Si l’application s’exécute en mode in-process et si la plateforme est configurée pour une architecture 64 bits (x64), le module ASP.NET Core utilise le runtime de la préversion 64 bits, le cas échéant. Installez l’extension d' **exécution de ASP.net Core {X. Y} (x64)** à l’aide du portail Azure.
>
> Après l’installation du runtime d’évaluation x64, exécutez la commande suivante dans la fenêtre de commande Azure Kudu PowerShell pour vérifier l’installation. Remplacez la version du runtime ASP.NET Core pour `{X.Y}` dans la commande suivante :
>
> ```powershell
> Test-Path D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64\
> ```
>
> La commande retourne `True` quand le runtime de la préversion x64 est installée.

> [!NOTE]
> Les **extensions ASP.NET Core** permettent d’activer des fonctionnalités supplémentaires pour ASP.NET Core sur Azure App Services, par exemple la journalisation Azure. L’extension est installée automatiquement quand vous effectuez le déploiement à partir de Visual Studio. Si l’extension n’est pas installée, installez-la pour l’application.

**Utiliser l’extension de site en préversion avec un modèle ARM**

Si un modèle ARM est utilisé pour créer et déployer des applications, le type de ressource `siteextensions` peut être utilisé pour ajouter l’extension de site à une application web. Par exemple :

[!code-json[](index/sample/arm.json?highlight=2)]

## <a name="publish-and-deploy-the-app"></a>Publier et déployer l’application

::: moniker range=">= aspnetcore-2.2"

Pour un déploiement de 64 bits :

* Utilisez un SDK .NET Core 64 bits pour générer une application 64 bits.
* Définissez **Plateforme** sur **64 bits** dans **Configuration** > **Paramètres généraux** d’App Service. L’application doit utiliser un plan de service De base ou supérieur pour permettre le choix du nombre de bits de la plateforme.

::: moniker-end

### <a name="deploy-the-app-framework-dependent"></a>Déployer l’application en fonction du framework

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Sélectionnez **générer**  >  **publier {nom de l’application}** dans la barre d’outils de Visual Studio ou cliquez avec le bouton droit sur le projet dans **Explorateur de solutions** puis sélectionnez **publier**.
1. Dans la boîte de dialogue **Choisir une cible de publication**, confirmez qu’**App Service** est sélectionné.
1. Sélectionnez **Avancé**. La boîte de dialogue **Publier** s’ouvre.
1. Dans la boîte de dialogue **Publier** :
   * Confirmez que la configuration **Mise en production** est sélectionnée.
   * Ouvrez la liste déroulante **Mode de déploiement** et sélectionnez **Dépendant du framework**.
   * Sélectionnez **Portable** comme **Runtime cible**.
   * Si vous devez supprimer des fichiers supplémentaires lors du déploiement, ouvrez **Options de publication de fichiers** et sélectionnez la case à cocher pour supprimer des fichiers supplémentaires à la destination.
   * Sélectionnez **Enregistrer**.
1. Créez un nouveau site ou mettez à jour un site existant en suivant les autres invites de l'Assistant de publication.

# <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli/)

1. Dans le fichier projet, ne spécifiez pas d’[Identificateur d’exécution (RID)](/dotnet/core/rid-catalog).

1. A partir d’un interpréteur de commandes, publiez l’application en configuration Release avec la commande [dotnet publish](/dotnet/core/tools/dotnet-publish). Dans l’exemple suivant, l’application est publiée en tant qu’application dépendante du framework :

   ```console
   dotnet publish --configuration Release
   ```

1. Déplacez le contenu du répertoire *bin/Release/{TARGET FRAMEWORK}/publish* vers le site dans App Service. Si vous faites glisser le contenu du dossier *publish* depuis votre disque dur local ou votre partage réseau directement vers App service dans la console [Kudu](https://github.com/projectkudu/kudu/wiki), faites glisser les fichiers vers le dossier `D:\home\site\wwwroot` dans la console Kudu.

---

### <a name="deploy-the-app-self-contained"></a>Déployer l’application autonome

Utilisez Visual Studio ou le CLI .NET Core pour un [Déploiement autonome (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Sélectionnez **générer**  >  **publier {nom de l’application}** dans la barre d’outils de Visual Studio ou cliquez avec le bouton droit sur le projet dans **Explorateur de solutions** puis sélectionnez **publier**.
1. Dans la boîte de dialogue **Choisir une cible de publication**, confirmez qu’**App Service** est sélectionné.
1. Sélectionnez **Avancé**. La boîte de dialogue **Publier** s’ouvre.
1. Dans la boîte de dialogue **Publier** :
   * Confirmez que la configuration **Mise en production** est sélectionnée.
   * Ouvrez la liste déroulante **Mode de déploiement** et sélectionnez **Autonome**.
   * Sélectionnez le runtime cible à partir de la liste déroulante **Runtime cible**. Par défaut, il s’agit de `win-x86`.
   * Si vous devez supprimer des fichiers supplémentaires lors du déploiement, ouvrez **Options de publication de fichiers** et sélectionnez la case à cocher pour supprimer des fichiers supplémentaires à la destination.
   * Sélectionnez **Enregistrer**.
1. Créez un nouveau site ou mettez à jour un site existant en suivant les autres invites de l'Assistant de publication.

# <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli/)

1. Dans le fichier projet, spécifiez un ou plusieurs [identificateurs de runtime (RID)](/dotnet/core/rid-catalog). Utilisez `<RuntimeIdentifier>` (singulier) pour un seul RID, ou `<RuntimeIdentifiers>` (pluriel) pour fournir une liste de RID délimitée par des points-virgules. Dans l’exemple suivant, le RID `win-x86` est spécifié :

   ```xml
   <PropertyGroup>
     <TargetFramework>{TARGET FRAMEWORK}</TargetFramework>
     <RuntimeIdentifier>win-x86</RuntimeIdentifier>
   </PropertyGroup>
   ```

1. A partir d'un interpréteur de commandes, publiez l'application dans la configuration Mise en production pour le runtime de l'hôte avec la commande [dotnet publish](/dotnet/core/tools/dotnet-publish). Dans l’exemple suivant, l’application est publiée pour le RID `win-x86`. Le RID fourni à l’option `--runtime` doit être fourni dans la propriété `<RuntimeIdentifier>` (ou `<RuntimeIdentifiers>`) du fichier projet.

   ```console
   dotnet publish --configuration Release --runtime win-x86 --self-contained
   ```

1. Déplacez le contenu du répertoire *bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* vers le site dans App Service. Si vous faites glisser le contenu du dossier *publish* depuis votre disque dur local ou votre partage réseau directement vers App service dans la console Kudu, faites glisser les fichiers vers le dossier `D:\home\site\wwwroot` dans la console Kudu.

---

## <a name="protocol-settings-https"></a>Paramètres de protocole (HTTPS)

Les liaisons de protocole sécurisées permettent de spécifier un certificat à utiliser pour répondre à des requêtes sur HTTPS. La liaison nécessite un certificat privé valide (*.pfx*) émis pour le nom d’hôte spécifique. Pour plus d’informations, consultez [Didacticiel : lier un certificat SSL personnalisé existant à Azure App service](/azure/app-service/app-service-web-tutorial-custom-ssl).

## <a name="transform-webconfig"></a>Transformer web.config

Si vous devez transformer *web.config* lors de la publication (par exemple, définir les variables d’environnement basées sur la configuration, le profil ou l’environnement), consultez <xref:host-and-deploy/iis/transform-webconfig>.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Vue d'ensemble d'App Service](/azure/app-service/app-service-web-overview)
* [Azure App Service: The Best Place to Host your .NET Apps (55-minute overview video)](https://channel9.msdn.com/events/dotnetConf/2017/T222)
* [Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)
* [Présentation des diagnostics Azure App Service](/azure/app-service/app-service-diagnostics)
* <xref:host-and-deploy/web-farm>

Azure App Service sur Windows Server utilise [IIS (Internet Information Services)](https://www.iis.net/). Les rubriques suivantes se rapportent à la technologie IIS sous-jacente :

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>
* [Windows Server – Contenu sur les versions actuelles et précédentes pour les administrateurs informatiques](/windows-server/windows-server-versions)
