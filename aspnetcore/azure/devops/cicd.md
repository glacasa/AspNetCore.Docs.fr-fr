---
title: Intégration et déploiement continus - DevOps avec ASP.NET Core et Azure
author: CamSoper
description: Intégration et déploiement continus à DevOps avec ASP.NET Core et Azure
ms.author: scaddie
ms.date: 10/24/2018
ms.custom: mvc, seodec18
uid: azure/devops/cicd
ms.openlocfilehash: 5fdf52235b49119503885f92c370dc588e809ffe
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78655833"
---
# <a name="continuous-integration-and-deployment"></a>Intégration et déploiement continus

Dans le chapitre précédent, vous avez créé un référentiel Git local pour l’application Simple Feed Reader. Dans ce chapitre, vous publierez ce code à un référentiel GitHub et construira un pipeline Azure DevOps Services à l’aide d’Azure Pipelines. Le pipeline permet des constructions et des déploiements continus de l’application. Tout engagement dans le référentiel GitHub déclenche une build et un déploiement dans la fente de mise en scène de l’application Web Azure.

Dans cette section, vous accomplirez les tâches suivantes :

* Publier le code de l’application sur GitHub
* Déconnecter le déploiement local de Git
* Créer une organisation Azure DevOps
* Créer un projet d’équipe dans Azure DevOps Services
* Créer une définition de build
* Créer un pipeline de mise en production
* Valider les modifications apportées à GitHub et les déployer automatiquement dans Azure
* Examiner le pipeline Azure Pipelines

## <a name="publish-the-apps-code-to-github"></a>Publier le code de l’application sur GitHub

1. Ouvrez une fenêtre de `https://github.com`navigateur, et naviguez vers .
1. Cliquez **+** sur la baisse de l’en-tête et sélectionnez **nouveau référentiel**:

    ![Option GitHub Nouveau dépôt](media/cicd/github-new-repo.png)

1. Sélectionnez votre compte dans le **décrochage du propriétaire** et entrez le lecteur simple *d’alimentation* dans la boîte à texte nom **de dépôt.**
1. Cliquez sur le bouton **Dépôt Créer.**
1. Ouvrez la coque de commande de votre machine locale. Naviguez vers l’annuaire dans lequel le référentiel Git *simple-feed-reader* est stocké.
1. Renommer *l’origine* existante à distance *en amont.* Exécutez la commande suivante :

    ```console
    git remote rename origin upstream
    ```

1. Ajoutez une nouvelle *télécommande d’origine* pointant vers votre copie du référentiel sur GitHub. Exécutez la commande suivante :

    ```console
    git remote add origin https://github.com/<GitHub_username>/simple-feed-reader/
    ```

1. Publiez votre référentiel Git local au nouveau référentiel GitHub. Exécutez la commande suivante :

    ```console
    git push -u origin master
    ```

1. Ouvrez une fenêtre de `https://github.com/<GitHub_username>/simple-feed-reader/`navigateur, et naviguez vers . Validez que votre code apparaît dans le référentiel GitHub.

## <a name="disconnect-local-git-deployment"></a>Déconnecter le déploiement local de Git

Retirez le déploiement local de Git avec les étapes suivantes. Azure Pipelines (un service Azure DevOps) remplace et augmente cette fonctionnalité.

1. Ouvrez le [portail Azure](https://portal.azure.com/)et naviguez vers la *mise en scène (mywebapp\<\>unique_number/mise en scène)* Web App. L’application Web peut être rapidement localisée en entrant la *mise en scène* dans la boîte de recherche du portail :

    ![mise en scène du terme de recherche d’applications Web](media/cicd/portal-search-box.png)

1. Cliquez sur **Deployment Center**. Un nouveau panneau apparaît. Cliquez **sur Disconnect** pour supprimer la configuration locale de contrôle des sources Git qui a été ajoutée dans le chapitre précédent. Confirmez l’opération de suppression en cliquant sur le bouton **Oui.**
1. Naviguez vers le *service d’applications<unique_number<mywebapp>.* Pour rappel, la boîte de recherche du portail peut être utilisée pour localiser rapidement le service d’application.
1. Cliquez sur **Deployment Center**. Un nouveau panneau apparaît. Cliquez **sur Disconnect** pour supprimer la configuration locale de contrôle des sources Git qui a été ajoutée dans le chapitre précédent. Confirmez l’opération de suppression en cliquant sur le bouton **Oui.**

## <a name="create-an-azure-devops-organization"></a>Créer une organisation Azure DevOps

1. Ouvrez un navigateur et naviguez vers la [page de création de l’organisation Azure DevOps](https://go.microsoft.com/fwlink/?LinkId=307137).
1. Tapez un nom unique dans la boîte à **texte Pick un nom mémorable** pour former l’URL pour accéder à votre organisation Azure DevOps.
1. Sélectionnez le bouton radio **Git,** puisque le code est hébergé dans un référentiel GitHub.
1. Cliquez sur le bouton **Continuer**. Après une courte attente, un compte et un projet d’équipe, nommé *MyFirstProject*, sont créés.

    ![Page de création d’organisation Azure DevOps](media/cicd/vsts-account-creation.png)

1. Ouvrez l’e-mail de confirmation indiquant que l’organisation et le projet Azure DevOps sont prêts à être utilisés. Cliquez **sur** le bouton Démarrer votre projet :

    ![Démarrez le bouton de votre projet](media/cicd/vsts-start-project.png)

1. Un navigateur s’ouvre à * \<\>account_name .visualstudio.com*. Cliquez sur le lien *MyFirstProject* pour commencer à configurer le pipeline DevOps du projet.

## <a name="configure-the-azure-pipelines-pipeline"></a>Configurer le pipeline Azure Pipelines

Il y a trois étapes distinctes à compléter. L’achèvement des étapes dans les trois sections suivantes se traduit par un pipeline opérationnel DevOps.

### <a name="grant-azure-devops-access-to-the-github-repository"></a>Accorder l’accès à Azure DevOps au dépôt GitHub

1. Étendre le **code ou construire à partir d’un** accordéon de dépôt externe. Cliquez sur le bouton **Setup Build** :

    ![Bouton Configurer la build](media/cicd/vsts-setup-build.png)

1. Sélectionnez l’option **GitHub** dans la section **Sélectionnez une source** :

    ![Sélectionnez une source - GitHub](media/cicd/vsts-select-source.png)

1. L’autorisation est requise avant qu’Azure DevOps puisse accéder à votre référentiel GitHub. Entrez *<GitHub_username connexion GitHub>* dans la boîte à texte nom de **connexion.** Par exemple :

    ![Nom de connexion GitHub](media/cicd/vsts-repo-authz.png)

1. Si l’authentification à deux facteurs est activée sur votre compte GitHub, un jeton d’accès personnel est nécessaire. Dans ce cas, cliquez sur **l’Autoriser avec un lien symbolique d’accès personnel GitHub.** Consultez les [instructions officielles de création personnelle de jetons GitHub](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/) pour obtenir de l’aide. Seule *la* portée des autorisations est nécessaire. Sinon, cliquez sur **l’autoriser à l’aide du bouton OAuth.**
1. Lorsqu’on vous le demande, connectez-vous à votre compte GitHub. Ensuite, sélectionnez Autoriser pour accorder l’accès à votre organisation Azure DevOps. En cas de succès, un nouveau critère de service est créé.
1. Cliquez sur le bouton ellipsis à côté du bouton **Repository.** Sélectionnez le *<GitHub_username>/simple-feed-reader* repository de la liste. Cliquez sur le bouton **Sélectionner**.
1. Sélectionnez la branche *principale* de la branche Par défaut pour les builds de chute **manuelles et planifiées.** Cliquez sur le bouton **Continuer**. La page de sélection du modèle apparaît.

### <a name="create-the-build-definition"></a>Créer la définition de build

1. À partir de la page de sélection du modèle, entrez *ASP.NET Core* dans la zone de recherche :

    ![recherche ASP.NET Core sur la page du modèle](media/cicd/vsts-template-selection.png)

1. Les résultats de recherche de modèle apparaissent. Planer au-dessus du modèle **ASP.NET Core** et cliquez sur le bouton **Appliquer.**
1. **L’onglet Tâches** de la définition de construction apparaît. Cliquez sur l’onglet **Déclencheurs.**
1. Vérifiez la boîte **d’intégration continue Enable.** Dans la section **des filtres de la Direction,** confirmer que le **drop-down type** est configuré pour *inclure*. Définissez la **baisse de spécification de la Direction** à *maîtriser*.

    ![Activer les paramètres d’intégration continue](media/cicd/vsts-enable-ci.png)

    Ces paramètres provoquent un déclencheur de build lorsque toute modification est poussée à la branche *principale* du référentiel GitHub. L’intégration continue est testée dans les [modifications Commit de GitHub et déployée automatiquement dans la section Azure.](#commit-changes-to-github-and-automatically-deploy-to-azure)

1. Cliquez sur le bouton **Enregistrer & file d’attente** et sélectionnez **l’option Enregistrer** :

    ![Bouton Enregistrer](media/cicd/vsts-save-build.png)

1. Le dialogue modal suivant apparaît :

    ![Enregistrer la définition de construction - dialogue modal](media/cicd/vsts-save-modal.png)

    Utilisez le dossier *\\*par défaut de , et cliquez sur le bouton **Enregistrer.**

### <a name="create-the-release-pipeline"></a>Créer le pipeline de mise en production

1. Cliquez sur l’onglet **Communiqués** de votre projet d’équipe. Cliquez sur le nouveau bouton **de pipeline.**

    ![Onglet De libération - Nouveau bouton de définition](media/cicd/vsts-new-release-definition.png)

    Le volet de sélection de modèle apparaît.

1. À partir de la page de sélection du modèle, entrez *App Service* dans la zone de recherche :

    ![Libérer la boîte de recherche de modèle de pipeline](media/cicd/vsts-release-template-search.png)

1. Les résultats de recherche de modèle apparaissent. Survolez le **déploiement du service d’application Azure avec le** modèle de fente et cliquez sur le bouton **Appliquer.** **L’onglet Pipeline** du pipeline de rejet apparaît.

    ![Déblocage de l’onglet pipeline](media/cicd/vsts-release-definition-pipeline.png)

1. Cliquez sur le bouton **Ajouter** dans la boîte **Artefacts.** Le panneau **d’artefact Add** apparaît :

    ![Pipeline de libération - Ajouter un panneau d’artefact](media/cicd/vsts-release-add-artifact.png)

1. Sélectionnez la tuile **Build** de la section **source de type** Source. Ce type permet de relier le pipeline de version à la définition de construction.
1. Sélectionnez *MyFirstProject* de l’abandon **du projet.**
1. Sélectionnez le nom de définition de construction, *MyFirstProject-ASP.NET Core-CI*, à partir de la **source (définition de construction)** drop-down.
1. Sélectionnez *les dernières nouveautés* de la **version par défaut.** Cette option construit les artefacts produits par la dernière série de la définition de construction.
1. Remplacez le texte dans la boîte **à texte d’alias Source** par *Drop*.
1. Cliquez sur le bouton **Ajouter**. Les mises à jour de la section Artefacts pour afficher les **modifications.**
1. Cliquez sur l’icône de l’éclair pour activer des déploiements continus :

    ![Déchargez les artefacts du pipeline - icône de l’éclair](media/cicd/vsts-artifacts-lightning-bolt.png)

    Avec cette option activée, un déploiement se produit chaque fois qu’une nouvelle version est disponible.
1. Un panneau **de déclenchement de déploiement continu** apparaît à droite. Cliquez sur le bouton basculement pour activer la fonctionnalité. Il n’est pas nécessaire d’activer la **demande Pull déclencheur**.
1. Cliquez sur la chute **Add** dans la section **filtres de branche Build.** Choisissez **l’option de branche par défaut de** la définition de build. Ce filtre ne déclenche la version que pour une version à partir de la branche *principale* du référentiel GitHub.
1. Cliquez sur le bouton **Enregistrer**. Cliquez sur le bouton **OK** dans le dialogue modal **Save** résultant.
1. Cliquez sur la case **Environnement 1.** Un panneau **environnement** apparaît à droite. Changer le texte *environnement 1* dans la boîte à **texte nom environnement** à la *production*.

   ![Pipeline de libération - Boîte à texte nom de l’environnement](media/cicd/vsts-environment-name-textbox.png)

1. Cliquez sur la **phase 1, 2 tâches** lien dans la boîte **de production:**

    ![Pipeline de sortie - Liaison de l’environnement de production.png](media/cicd/vsts-production-link.png)

    **L’onglet Tâches** de l’environnement apparaît.
1. Cliquez sur le **service d’application Déployer Azure pour la** tâche de fente. Ses paramètres apparaissent dans un panneau à droite.
1. Sélectionnez l’abonnement Azure associé au service App à partir de **l’abonnement Azure.** Une fois sélectionné, cliquez sur le bouton **Autoriser.**
1. Sélectionnez *l’application Web* à partir du **drop-down de type App.**
1. Sélectionnez *mywebapp/<unique_number/>* du nom de **service App** drop-down.
1. Sélectionnez *AzureTutorial* parmi le **groupe Resource** drop-down.
1. Sélectionnez *la mise en scène* à partir de la mise à jour de la **machine** à sous.
1. Cliquez sur le bouton **Enregistrer**.
1. Planer au-dessus du nom du pipeline de version par défaut. Cliquez sur l’icône du crayon pour l’éditer. Utilisez *MyFirstProject-ASP.NET Core-CD* comme nom.

    ![Nom du pipeline de libération](media/cicd/vsts-release-definition-name.png)

1. Cliquez sur le bouton **Enregistrer**.

## <a name="commit-changes-to-github-and-automatically-deploy-to-azure"></a>Valider les modifications apportées à GitHub et les déployer automatiquement dans Azure

1. Ouvrez *SimpleFeedReader.sln* dans Visual Studio.
1. Dans Solution Explorer, ouvrez *Pages-Index.cshtml*. Remplacez `<h2>Simple Feed Reader - V3</h2>` par `<h2>Simple Feed Reader - V4</h2>`.
1. Appuyez sur **Ctrl**+**Shift**+**B** pour construire l’application.
1. Engagez le fichier dans le référentiel GitHub. Utilisez soit la page **Modifications** dans l’onglet *Team Explorer* de Visual Studio, soit exécutez ce qui suit à l’aide de la coque de commande de la machine locale :

    ```console
    git commit -a -m "upgraded to V4"
    ```

1. Poussez le changement dans la branche *principale* à la télécommande *d’origine* de votre référentiel GitHub :

    ```console
    git push origin master
    ```

    L’commit apparaît dans la branche *principale* du référentiel GitHub :

    ![GitHub s’engager dans la branche maître](media/cicd/github-commit.png)

    La construction est déclenchée, puisque l’intégration continue est activée dans l’onglet **Déclencheurs** de la définition de construction :

    ![permettre une intégration continue](media/cicd/enable-ci.png)

1. Naviguez vers **l’onglet File d’attente** de la page **Azure Pipelines** > **Builds** dans Azure DevOps Services. La construction en file d’attente montre la branche et commit qui a déclenché la construction:

    ![construction en file d’attente](media/cicd/build-queued.png)

1. Une fois la construction réussie, un déploiement à Azure se produit. Naviguez vers l’application dans le navigateur. Notez que le texte "V4" apparaît dans le titre:

    ![application mise à jour](media/cicd/updated-app-v4.png)

## <a name="examine-the-azure-pipelines-pipeline"></a>Examiner le pipeline Azure Pipelines

### <a name="build-definition"></a>Définition de build

Une définition de construction a été créée avec le nom *MyFirstProject-ASP.NET Core-CI*. Une fois terminé, la construction produit un fichier *.zip* comprenant les actifs à publier. Le gazoduc de sortie déploie ces actifs à Azure.

L’onglet **Tâches** de la définition de construction répertorie les étapes individuelles utilisées. Il y a cinq tâches de construction.

![construire des tâches de définition](media/cicd/build-definition-tasks.png)

1. **Restaurer** &mdash; Exécute `dotnet restore` la commande pour restaurer les paquets NuGet de l’application. L’alimentation par défaut utilisée est nuget.org.
1. **Build** &mdash; Exécute `dotnet build --configuration release` la commande pour compiler le code de l’application. Cette `--configuration` option est utilisée pour produire une version optimisée du code, qui convient au déploiement dans un environnement de production. Modifier la variable *BuildConfiguration* sur l’onglet **Variables** de la définition de construction si, par exemple, une configuration de débaillement est nécessaire.
1. **Test** &mdash; Exécute `dotnet test --configuration release --logger trx --results-directory <local_path_on_build_agent>` la commande pour exécuter les tests unitaires de l’application. Les tests unitaires sont exécutés `**/*Tests/*.csproj` dans n’importe quel projet CMD correspondant au modèle de glob. Les résultats des tests sont enregistrés dans un `--results-directory` fichier *.trx* à l’emplacement spécifié par l’option. En cas d’échec des tests, la construction échoue et n’est pas déployée.

    > [!NOTE]
    > Pour vérifier le fonctionnement des tests unitaires, modifiez *SimpleFeedReader.Tests-Services-NewsServiceTests.cs* pour casser délibérément l’un des tests. Par exemple, `Assert.True(result.Count > 0);` `Assert.False(result.Count > 0);` changez `Returns_News_Stories_Given_Valid_Uri` dans la méthode. Engagez et poussez la modification à GitHub. La construction est déclenchée et échoue. L’état de l’état du pipeline de construction change à **l’échec**. Retournez le changement, engagez-vous et poussez à nouveau. La construction réussit.

1. **Publier** &mdash; Exécute `dotnet publish --configuration release --output <local_path_on_build_agent>` la commande pour produire un fichier *.zip* avec les artefacts à déployer. L’option `--output` spécifie l’emplacement de publication du fichier *.zip.* Cet emplacement est spécifié en passant une `$(build.artifactstagingdirectory)` [variable prédéfinie](/azure/devops/pipelines/build/variables) nommée . Cette variable s’étend à un chemin local, comme *le travail de c: 'agent\_'1 'a*, sur l’agent de construction.
1. **Publier Artifact** &mdash; Publie le fichier *.zip* produit par la tâche **Publier.** La tâche accepte l’emplacement du fichier *.zip* comme un `$(build.artifactstagingdirectory)`paramètre, qui est la variable prédéfinie . Le fichier *.zip* est publié sous forme de dossier nommé *drop*.

Cliquez sur le lien **sommaire** de la définition de build pour afficher une historique des builds avec la définition :

![Capture d’écran montrant l’historique de définition de construction](media/cicd/build-definition-summary.png)

Sur la page résultante, cliquez sur le lien correspondant au numéro de build unique :

![Capture d’écran montrant la page de résumé de définition de construction](media/cicd/build-definition-completed.png)

Un résumé de cette version spécifique est affiché. Cliquez sur l’onglet **Artefacts,** et remarquez que le dossier *de chute* produit par la build est répertorié :

![Capture d’écran montrant des artefacts de définition de construction - dossier de chute](media/cicd/build-definition-artifacts.png)

Utilisez les liens **Télécharger** et **explorer** pour inspecter les artefacts publiés.

### <a name="release-pipeline"></a>Pipeline de mise en production

Un pipeline de version a été créé avec le nom *MyFirstProject-ASP.NET Core-CD*:

![Capture d’écran montrant l’aperçu du pipeline de libération](media/cicd/release-definition-overview.png)

Les deux principaux composants du pipeline de rejet sont les **artefacts** et les **environnements**. En cliquant sur la case dans la section **Artefacts** révèle le panneau suivant :

![Capture d’écran montrant des artefacts de pipeline de libération](media/cicd/release-definition-artifacts.png)

La valeur **source (définition de build)** représente la définition de construction à laquelle ce pipeline de version est lié. Le fichier *.zip* produit par une série réussie de la définition de build est fourni à l’environnement *de production* pour le déploiement à Azure. Cliquez sur la *phase 1, 2 tâches* lien dans la boîte d’environnement *de production* pour afficher les tâches de pipeline de sortie:

![Capture d’écran montrant les tâches de pipeline de libération](media/cicd/release-definition-tasks.png)

Le gazoduc de sortie se compose de deux tâches : *déployer le service d’application Azure pour slot* et gérer le service *d’application Azure - Slot Swap*. En cliquant sur la première tâche, vous révèlez la configuration de tâche suivante :

![Capture d’écran montrant la tâche de déploiement du pipeline de dégagement](media/cicd/release-definition-task1.png)

L’abonnement Azure, le type de service, le nom de l’application Web, le groupe de ressources et la fente de déploiement sont définis dans la tâche de déploiement. Le **paquet ou boîte** à texte de dossier retient le chemin de fichier *.zip* à extraire et à déployer à la fente de *mise en scène* de l’application web *mywebapp\<unique_number.\> *

En cliquant sur la tâche d’échange de créneaux horaires, la configuration de tâche suivante :

![Capture d’écran montrant la tâche d’échange de fente de pipeline de libération](media/cicd/release-definition-task2.png)

Les détails de l’abonnement, du groupe de ressources, du type de service, du nom de l’application Web et des créneaux de déploiement sont fournis. Le **Swap avec la** case à cocher de production est coché. Par conséquent, les bits déployés à la fente *de mise en scène* sont échangés dans l’environnement de production.

## <a name="additional-reading"></a>Documentation supplémentaire

* [Créer son premier pipeline avec Azure Pipelines](/azure/devops/pipelines/get-started-yaml)
* [Construire et .NET Projet de base](/azure/devops/pipelines/languages/dotnet-core)
* [Déployer une application web avec Azure Pipelines](/azure/devops/pipelines/targets/webapp)
