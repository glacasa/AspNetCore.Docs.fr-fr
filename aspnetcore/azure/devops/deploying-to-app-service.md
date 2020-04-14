---
title: Déployer une application sur App Service - DevOps avec ASP.NET Core et Azure
author: CamSoper
description: Déployez une application ASP.NET Core à Azure App Service, première étape pour DevOps avec ASP.NET Core et Azure.
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 10/24/2018
uid: azure/devops/deploy-to-app-service
ms.openlocfilehash: d7ee3e42d320d35c2aaff6e097203c45289ec5b1
ms.sourcegitcommit: fbdb8b9ab5a52656384b117ff6e7c92ae070813c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81228125"
---
# <a name="deploy-an-app-to-app-service"></a>Déployer une application sur App Service

[Azure App Service](/azure/app-service/) est la plate-forme d’hébergement Web d’Azure. Le déploiement d’une application web sur Azure App Service peut se faire manuellement ou par un processus automatisé. Cette section du guide traite des méthodes de déploiement qui peuvent être déclenchées manuellement ou par script à l’aide de la ligne de commande, ou déclenchées manuellement à l’aide de Visual Studio.

Dans cette section, vous accomplirez les tâches suivantes :

* Téléchargez et construisez l’application d’échantillon.
* Créez une application Web Azure App Service à l’aide de la coquille Cloud Azure.
* Déployez l’application d’échantillon à Azure à l’aide de Git.
* Déployez une modification de l’application à l’aide de Visual Studio.
* Ajoutez une fente de mise en scène à l’application web.
* Déployez une mise à jour de la fente de mise en scène.
* Procédez à l’échange des emplacements de préproduction et de production.

## <a name="download-and-test-the-app"></a>Télécharger et tester l’application

L’application utilisée dans ce guide est une application ASP.NET Core pré-construite, [Simple Feed Reader](https://github.com/Azure-Samples/simple-feed-reader/). Il s’agit d’une `Microsoft.SyndicationFeed.ReaderWriter` application Razor Pages qui utilise l’API pour récupérer un flux RSS/Atom et afficher les actualités d’une liste.

N’hésitez pas à revoir le code, mais il est important de comprendre qu’il n’y a rien de spécial dans cette application. C’est juste une application simple ASP.NET Core à des fins d’illustration.

À partir d’une coque de commande, téléchargez le code, construisez le projet et exécutez-le comme suit.

> *Remarque : Les utilisateurs de Linux/macOS devraient apporter des modifications`/`appropriées pour les`\`chemins, par exemple, en utilisant la barre oblique avant () plutôt que la barre oblique arrière ().*

1. Clonez le code à un dossier sur votre machine locale.

    ```console
    git clone https://github.com/Azure-Samples/simple-feed-reader/
    ```

2. Changez votre dossier de travail pour le dossier *simple-feed-reader* qui a été créé.

    ```console
    cd .\simple-feed-reader\SimpleFeedReader
    ```

3. Restaurer les paquets et construire la solution.

    ```dotnetcli
    dotnet build
    ```

4. Exécutez l'application.

    ```dotnetcli
    dotnet run
    ```

    ![La commande de course dotnet est réussie](./media/deploying-to-app-service/dotnet-run.png)

5. Ouvrez un navigateur et accédez à `http://localhost:5000`. L’application vous permet de taper ou coller une URL de flux de syndication et de consulter une liste d’actualités.

     ![L’application affichant le contenu d’un flux RSS](./media/deploying-to-app-service/app-in-browser.png)

6. Une fois que vous êtes satisfait de l’application fonctionne correctement, l’arrêter en appuyant sur **Ctrl**+**C** dans la coque de commande.

## <a name="create-the-azure-app-service-web-app"></a>Créez l’application Web Azure App

Pour déployer l’application, vous devrez créer une [application Web](/azure/app-service/app-service-web-overview)App Service . Après la création de l’application Web, vous y serez déployé à partir de votre machine locale à l’aide de Git.

1. Connectez-vous à [Azure Cloud Shell](https://shell.azure.com/bash). Remarque : Lorsque vous vous connectez pour la première fois, Cloud Shell invite à créer un compte de stockage pour les fichiers de configuration. Acceptez les défauts ou fournissez un nom unique.

2. Utilisez la coquille Cloud pour les étapes suivantes.

    a. Déclarez une variable pour stocker le nom de votre application web. Le nom doit être unique à utiliser dans l’URL par défaut. L’utilisation de la `$RANDOM` fonction Bash pour construire `webappname99999`le nom garantit un caractère unique et aboutit au format .

    ```console
    webappname=mywebapp$RANDOM
    ```

    b. Créez un groupe de ressources. Les groupes de ressources fournissent un moyen d’agréger les ressources Azure à gérer en tant que groupe.

    ```azurecli
    az group create --location centralus --name AzureTutorial
    ```

    La `az` commande invoque [l’Azure CLI](/cli/azure/). Le CLI peut être exécuté localement, mais l’utiliser dans le Cloud Shell permet d’économiser du temps et de la configuration.

    c. Créez un plan app Service dans le niveau S1. Un plan App Service est un regroupement d’applications Web qui partagent le même niveau de prix. Le niveau S1 n’est pas gratuit, mais il est nécessaire pour la fonction de fentes de mise en scène.

    ```azurecli
    az appservice plan create --name $webappname --resource-group AzureTutorial --sku S1
    ```

    d. Créez la ressource web app à l’aide du plan App Service dans le même groupe de ressources.

    ```azurecli
    az webapp create --name $webappname --resource-group AzureTutorial --plan $webappname
    ```

    e. Définissez les informations d’identification de déploiement. Ces informations d’identification de déploiement s’appliquent à toutes les applications web de votre abonnement. N’utilisez pas de caractères spéciaux dans le nom d’utilisateur.

    ```azurecli
    az webapp deployment user set --user-name REPLACE_WITH_USER_NAME --password REPLACE_WITH_PASSWORD
    ```

    f. Configurez l’application web pour accepter les déploiements de Git locaux et afficher l’URL de *déploiement Git*. **Notez cette URL pour référence plus tard**.

    ```azurecli
    echo Git deployment URL: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --query url --output tsv)
    ```

    g. Affichez *l’URL de l’application Web*. Naviguez vers cette URL pour voir l’application web vierge. **Notez cette URL pour référence plus tard**.

    ```console
    echo Web app URL: http://$webappname.azurewebsites.net
    ```

3. À l’aide d’une coque de commande sur votre machine locale, naviguez vers le dossier de projet de l’application web (par exemple, `.\simple-feed-reader\SimpleFeedReader`). Exécutez les commandes suivantes pour configurer Git pour pousser à l’URL de déploiement :

    a. Ajoutez l’URL à distance au référentiel local.

    ```console
    git remote add azure-prod GIT_DEPLOYMENT_URL
    ```

    b. Poussez la branche *principale* locale jusqu’à la branche *principale* de la télécommande *azure-prod.*

    ```console
    git push azure-prod master
    ```

    Vous serez invité pour les informations de déploiement que vous avez créées plus tôt. Observez la sortie dans la coque de commande. Azure construit l’application ASP.NET Core à distance.

4. Dans un navigateur, naviguez vers *l’URL de l’application Web* et notez que l’application a été construite et déployée. D’autres modifications peuvent être apportées au `git commit`référentiel local Git avec . Ces changements sont poussés à `git push` Azure avec la commande précédente.

## <a name="deployment-with-visual-studio"></a>Déploiement avec Visual Studio

> *Remarque : Cette section ne s’applique qu’à Windows. Les utilisateurs de Linux et de macOS doivent faire la modification décrite à l’étape 2 ci-dessous. Enregistrer le fichier, et de commettre la `git commit`modification au référentiel local avec . Enfin, pousser le `git push`changement avec , comme dans la première section.*

L’application a déjà été déployée à partir de la coque de commande. Utilisons les outils intégrés de Visual Studio pour déployer une mise à jour de l’application. Dans les coulisses, Visual Studio accomplit la même chose que l’outillage de la ligne de commande, mais dans l’interface utilisateur familière de Visual Studio.

1. Ouvrez *SimpleFeedReader.sln* dans Visual Studio.
2. Dans Solution Explorer, ouvrez *Pages-Index.cshtml*. Remplacez `<h2>Simple Feed Reader</h2>` par `<h2>Simple Feed Reader - V2</h2>`.
3. Appuyez sur **Ctrl**+**Shift**+**B** pour construire l’application.
4. Dans Solution Explorer, cliquez à droite sur le projet et cliquez sur **Publier**.

    ![Capture d’écran montrant Right-click, Publier](./media/deploying-to-app-service/publish.png)
5. Visual Studio peut créer une nouvelle ressource App Service, mais cette mise à jour sera publiée sur le déploiement existant. Dans le Pick, un dialogue **cible de publication,** sélectionnez le service **d’application** de la liste sur la gauche, puis **sélectionnez Select Existing**. Cliquez sur **Publier**.
6. Dans le dialogue **App Service,** confirmez que le compte Microsoft ou Organisation utilisé pour créer votre abonnement Azure s’affiche en haut à droite. Si ce n’est pas le cas, cliquez sur le drop-down et ajoutez-le.
7. Confirmez que **l’abonnement** Azure correct est sélectionné. Pour **voir**, sélectionnez **Resource Group**. Élargissez le groupe de ressources **AzureTutorial** et sélectionnez ensuite l’application web existante. Cliquez sur **OK**.

    ![Capture d’écran montrant le dialogue de Publish App Service](./media/deploying-to-app-service/publish-dialog.png)

Visual Studio construit et déploie l’application à Azure. Naviguez vers l’URL de l’application Web. Validez `<h2>` que la modification de l’élément est en direct.

![L’application avec le titre modifié](./media/deploying-to-app-service/app-v2.png)

## <a name="deployment-slots"></a>Emplacements de déploiement

Les emplacements de déploiement prennent en charge la mise en scène des modifications sans avoir d’impact sur l’application en cours d’exécution en production. Une fois que la version mise en scène de l’application est validée par une équipe d’assurance de la qualité, les créneaux de production et de mise en scène peuvent être échangés. L’application en mise en scène est promue à la production de cette manière. Les étapes suivantes créent une fente de mise en scène, en déployant quelques modifications et échangent la fente de mise en scène avec la production après vérification.

1. Connectez-vous à [l’Azure Cloud Shell](https://shell.azure.com/bash), si ce n’est déjà connecté.
2. Créez la fente de mise en scène.

    a. Créez une fente de déploiement avec la *mise en scène*du nom .

    ```azurecli
    az webapp deployment slot create --name $webappname --resource-group AzureTutorial --slot staging
    ```

    b. Configurez la fente de mise en scène pour utiliser le déploiement à partir de Git local et obtenir l’URL de déploiement **de mise en scène.** **Notez cette URL pour référence plus tard**.

    ```azurecli
    echo Git deployment URL for staging: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --slot staging --query url --output tsv)
    ```

    c. Affichez l’URL de la fente de mise en scène. Naviguez vers l’URL pour voir la fente de mise en scène vide. **Notez cette URL pour référence plus tard**.

    ```console
    echo Staging web app URL: http://$webappname-staging.azurewebsites.net
    ```

3. Dans un éditeur de texte ou Visual Studio, modifiez à `<h2>` nouveau `<h2>Simple Feed Reader - V3</h2>` *Pages/Index.cshtml* afin que l’élément se lit et enregistre le fichier.

4. Engagez le fichier sur le référentiel local Git, en utilisant soit la page **Modifications** dans l’onglet *Team Explorer* de Visual Studio, soit en entrant le suivant à l’aide de la coque de commande de la machine locale :

    ```console
    git commit -a -m "upgraded to V3"
    ```

5. À l’aide de la coque de commande de la machine locale, ajoutez l’URL de déploiement de mise en scène comme télécommande Git et poussez les modifications validées :

    a. Ajoutez l’URL à distance pour la mise en scène du référentiel local Git.

    ```console
    git remote add azure-staging <Git_staging_deployment_URL>
    ```

    b. Poussez la branche *principale* locale jusqu’à la branche *principale* de la télécommande *azur-mise en scène.*

    ```console
    git push azure-staging master
    ```

    Attendez pendant qu’Azure construit et déploie l’application.

6. Pour vérifier que V3 a été déployé sur la fente de mise en scène, ouvrez deux fenêtres de navigateur. Dans une fenêtre, naviguez vers l’URL originale de l’application Web. Dans l’autre fenêtre, naviguez vers l’URL de l’application web de mise en scène. L’URL de production sert V2 de l’application. L’URL de mise en scène sert V3 de l’application.

    ![Capture d’écran comparant les fenêtres du navigateur](./media/deploying-to-app-service/ready-to-swap.png)

7. Dans le Cloud Shell, entrez la fente de mise en scène vérifiée/réchauffée en production.

    ```azurecli
    az webapp deployment slot swap --name $webappname --resource-group AzureTutorial --slot staging
    ```

8. Vérifiez que l’échange s’est produit en rafraîchissant les deux fenêtres du navigateur.

    ![Comparaison des fenêtres du navigateur après l’échange](./media/deploying-to-app-service/swapped.png)

## <a name="summary"></a>Récapitulatif

Dans cette section, les tâches suivantes ont été accomplies :

* Téléchargé et construit l’application d’échantillon.
* Création d’une application Web Azure App Service à l’aide de la coquille Cloud Azure.
* Déploiement de l’application d’échantillon à Azure à l’aide de Git.
* A déployé une modification de l’application à l’aide de Visual Studio.
* Ajout d’une fente de mise en scène à l’application web.
* Déploiement d’une mise à jour de la fente de mise en scène.
* Échangé les fentes de mise en scène et de production.

Dans la section suivante, vous apprendrez à construire un pipeline DevOps avec Azure Pipelines.

## <a name="additional-reading"></a>Documentation supplémentaire

* [Vue d'ensemble de Web Apps](/azure/app-service/app-service-web-overview)
* [Construire une application web .NET Core et SQL Database dans Azure App Service](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* [Configurer les informations d’identification de déploiement pour Azure App Service](/azure/app-service/app-service-deployment-credentials)
* [Configurer des environnements intermédiaires dans Azure App Service](/azure/app-service/web-sites-staged-publishing)
