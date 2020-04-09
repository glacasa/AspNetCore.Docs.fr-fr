---
title: Publier une application ASP.NET Core sur Azure avec Visual Studio
author: rick-anderson
description: Découvrez comment publier une application ASP.NET Core sur Azure App Service à l’aide de Visual Studio.
ms.author: riande
ms.custom: mvc
ms.date: 07/10/2019
uid: tutorials/publish-to-azure-webapp-using-vs
ms.openlocfilehash: 7fc3644df3dcb957f2537538aaa9506c6b38a480
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78662203"
---
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio"></a>Publier une application ASP.NET Core sur Azure avec Visual Studio

Par [Rick Anderson](https://twitter.com/RickAndMSFT)
::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

::: moniker-end


Voir [Publiez une application Web à Azure App Service en utilisant Visual Studio pour Mac](https://docs.microsoft.com/visualstudio/mac/publish-app-svc?view=vsmac-2019) si vous travaillez sur macOS.

Pour résoudre un problème de déploiement App Service, consultez <xref:test/troubleshoot-azure-iis>.

## <a name="set-up"></a>Configurer

* Ouvrez un [compte Azure gratuit](https://azure.microsoft.com/free/dotnet/) si vous n’en avez pas. 

## <a name="create-a-web-app"></a>Créer une application web

Dans la page de démarrage de Visual Studio, sélectionnez **Fichier > Nouveau > Projet...**

![Menu Fichier](publish-to-azure-webapp-using-vs/_static/file_new_project.png)

Renseignez la boîte de dialogue **Nouveau projet** :

* Dans la vitre gauche, sélectionnez **.NET Core**.
* Dans le volet central, sélectionnez **Application web ASP.NET Core**.
* Sélectionnez **OK**.

![Boîte de dialogue Nouveau projet](publish-to-azure-webapp-using-vs/_static/new_prj.png)

Dans la boîte de dialogue **Nouvelle application web ASP.NET Core** :

* Sélectionnez **application Web**.
* Sélectionnez **Modifier l’authentification**.

![Boîte de dialogue Nouveau projet](publish-to-azure-webapp-using-vs/_static/new_prj_2.png)

La boîte de dialogue **Modifier l’authentification** s’affiche. 

* Sélectionnez **Comptes d’utilisateur individuels**.
* Sélectionnez **OK** pour revenir à la **nouvelle application web ASP.NET Core**, puis sélectionnez à nouveau **OK**.

![Boîte de dialogue Nouvelle application web ASP.NET Core](publish-to-azure-webapp-using-vs/_static/new_prj_auth.png) 

Visual Studio crée la solution.

## <a name="run-the-app"></a>Exécuter l’application

* Appuyez sur CTRL+F5 pour exécuter le projet.
* Testez les liens **À propos de** et **Contact**.

![Application web ouverte dans Microsoft Edge sur localhost](publish-to-azure-webapp-using-vs/_static/show.png)

### <a name="register-a-user"></a>Inscrire un utilisateur

* Sélectionnez **S’inscrire**, puis inscrivez un nouvel utilisateur. Vous pouvez utiliser une adresse e-mail fictive. Quand vous effectuez l’envoi, la page affiche l’erreur suivante :

    *« Erreur interne du serveur : une opération de base de données a échoué pendant le traitement de la demande. Exception SQL : Impossible d’ouvrir la base de données. L’application des migrations existantes pour le contexte de la DB de demande peut résoudre ce problème.*
* Sélectionnez **Appliquer les migrations**, puis, une fois la page mise à jour, actualisez-la.

![Erreur de serveur interne : Une opération de base de données a échoué lors du traitement de la requête. Exception SQL : Impossible d’ouvrir la base de données. Appliquer des migrations existantes pour le contexte de base de données d’application peut résoudre ce problème.](publish-to-azure-webapp-using-vs/_static/mig.png)

L’application affiche l’adresse e-mail utilisée pour inscrire le nouvel utilisateur et un lien **Se déconnecter**.

![Application web ouverte dans Microsoft Edge. Le lien S’inscrire est remplacé par le texte Hello email@domain.com!](publish-to-azure-webapp-using-vs/_static/hello.png)

## <a name="deploy-the-app-to-azure"></a>Déploiement de l’application dans Azure

Cliquez à droite sur le projet dans Solution Explorer et **sélectionnez Publier...**.

![Menu contextuel ouvert avec le lien Publier mis en surbrillance](publish-to-azure-webapp-using-vs/_static/pub.png)

Dans la boîte de dialogue **Publier** :

* Cliquez sur **Microsoft Azure App Service**.
* Sélectionnez l’icône d’engrenage, puis **Créer un profil**.
* Sélectionnez **Créer un profil**.

![Boîte de dialogue Publier](publish-to-azure-webapp-using-vs/_static/maas1.png)

### <a name="create-azure-resources"></a>Créer des ressources Azure

Le dialogue **Create App Service** apparaît :

* Entrez votre abonnement.
* Les champs d’entrée **Nom de l’application**, **Groupe de ressources** et **Plan App Service** sont renseignés. Vous pouvez conserver ces noms ou les changer.

![Boîte de dialogue App Service](publish-to-azure-webapp-using-vs/_static/newrg1.png)

* Sélectionnez l’onglet **Services** pour créer une base de données.

* Sélectionnez **+** l’icône verte pour créer une nouvelle base de données SQL

![New SQL Database](publish-to-azure-webapp-using-vs/_static/sql.png)

* Sélectionnez **Nouveau...** dans la boîte de dialogue **Configurer la base de données SQL** pour créer une base de données.

![Nouvelle base de données et nouveau serveur SQL](publish-to-azure-webapp-using-vs/_static/conf.png)

La boîte de dialogue **Configurer le serveur SQL Server** s’affiche.

* Entrez un nom d’utilisateur et un mot de passe administrateur, puis sélectionnez **OK**. Vous pouvez conserver le nom par défaut **du serveur**. 

> [!NOTE]
> « admin » n’est pas autorisé comme nom d’utilisateur administrateur.

![Boîte de dialogue Configurer le serveur SQL Server](publish-to-azure-webapp-using-vs/_static/conf_servername.png)

* Sélectionnez **OK**.

Visual Studio retourne la boîte de dialogue **Créer App Service**.

* Sélectionnez **Créer** dans la boîte de dialogue **Créer App Service**.

![Boîte de dialogue Configurer la base de données SQL](publish-to-azure-webapp-using-vs/_static/conf_final.png)

Visual Studio crée l’application web et SQL Server sur Azure. Cette étape peut prendre quelques minutes. Pour plus d’informations sur les ressources créées, consultez [Ressources supplémentaires](#additional-resources).

Quand le déploiement est terminé, sélectionnez **Paramètres** :

![Boîte de dialogue Configurer le serveur SQL Server](publish-to-azure-webapp-using-vs/_static/set.png)

Dans la page **Paramètres** de la boîte de dialogue **Publier** :

* Élargissez les **bases de données** et vérifiez Utilisez cette chaîne de connexion au moment de **l’exécution**.
* Élargir **les migrations-cadres de l’entité** et vérifier **Appliquer cette migration sur publier**.

* Sélectionnez **Enregistrer**. Visual Studio retourne à la boîte de dialogue **Publier**. 

![Boîte de dialogue Publier : panneau Paramètres](publish-to-azure-webapp-using-vs/_static/pubs.png)

Cliquez sur **Publier**. Visual Studio publie votre application sur Azure. Quand le déploiement est terminé, l’application est ouverte dans un navigateur.

### <a name="test-your-app-in-azure"></a>Tester votre application dans Azure

* Testez les liens **About** et **Contact**

* Enregistre un nouvel utilisateur

![Application web ouverte dans Microsoft Edge sur Azure App Service](publish-to-azure-webapp-using-vs/_static/register.png)

### <a name="update-the-app"></a>Mettre à jour l’application

* Modifiez la page Razor *Pages/About.cshtml*, puis changez son contenu. Par exemple, vous pouvez modifier le paragraphe pour indiquer « Hello ASP.NET Core! » :

    [!code-html[About](publish-to-azure-webapp-using-vs/sample/about.cshtml?highlight=9&range=1-9)]

* Cliquez avec le bouton droit sur le projet, puis sélectionnez **Publier**.

![Menu contextuel ouvert avec le lien Publier mis en surbrillance](publish-to-azure-webapp-using-vs/_static/pub.png)

* Une fois l’application publiée, vérifiez que les changements apportés sont disponibles sur Azure.

![Vérifiez que la tâche est terminée](publish-to-azure-webapp-using-vs/_static/final.png)

### <a name="clean-up"></a>Nettoyer

Après avoir testé l’application, accédez au [portail Azure](https://portal.azure.com/), puis supprimez l’application.

* Sélectionnez **Groupes de ressources**, puis sélectionnez le groupe de ressources que vous avez créé.

![Portail Azure : Groupes de ressources dans le menu de la barre latérale](publish-to-azure-webapp-using-vs/_static/portalrg.png)

* Dans la page **Groupes de ressources**, sélectionnez **Supprimer**.

![Portail Azure : page Groupes de ressources](publish-to-azure-webapp-using-vs/_static/rgd.png)

* Entrez le nom du groupe de ressources, puis sélectionnez **Supprimer**. Votre application et toutes les autres ressources créées dans ce didacticiel sont désormais supprimées d’Azure.

### <a name="next-steps"></a>Étapes suivantes

* <xref:host-and-deploy/azure-apps/azure-continuous-deployment>

## <a name="additional-resources"></a>Ressources supplémentaires

* Pour Visual Studio Code, consultez [Profils de publication](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles).
* [Azure App Service](/azure/app-service/app-service-web-overview)
* [Groupes de ressources Azure](/azure/azure-resource-manager/resource-group-overview#resource-groups)
* [Azure SQL Database](/azure/sql-database/)
* <xref:host-and-deploy/visual-studio-publish-profiles>
* <xref:test/troubleshoot-azure-iis>
