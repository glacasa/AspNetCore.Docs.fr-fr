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
# <a name="continuous-integration-and-deployment"></a><span data-ttu-id="73361-103">Intégration et déploiement continus</span><span class="sxs-lookup"><span data-stu-id="73361-103">Continuous integration and deployment</span></span>

<span data-ttu-id="73361-104">Dans le chapitre précédent, vous avez créé un référentiel Git local pour l’application Simple Feed Reader.</span><span class="sxs-lookup"><span data-stu-id="73361-104">In the previous chapter, you created a local Git repository for the Simple Feed Reader app.</span></span> <span data-ttu-id="73361-105">Dans ce chapitre, vous publierez ce code à un référentiel GitHub et construira un pipeline Azure DevOps Services à l’aide d’Azure Pipelines.</span><span class="sxs-lookup"><span data-stu-id="73361-105">In this chapter, you'll publish that code to a GitHub repository and construct an Azure DevOps Services pipeline using Azure Pipelines.</span></span> <span data-ttu-id="73361-106">Le pipeline permet des constructions et des déploiements continus de l’application.</span><span class="sxs-lookup"><span data-stu-id="73361-106">The pipeline enables continuous builds and deployments of the app.</span></span> <span data-ttu-id="73361-107">Tout engagement dans le référentiel GitHub déclenche une build et un déploiement dans la fente de mise en scène de l’application Web Azure.</span><span class="sxs-lookup"><span data-stu-id="73361-107">Any commit to the GitHub repository triggers a build and a deployment to the Azure Web App's staging slot.</span></span>

<span data-ttu-id="73361-108">Dans cette section, vous accomplirez les tâches suivantes :</span><span class="sxs-lookup"><span data-stu-id="73361-108">In this section, you'll complete the following tasks:</span></span>

* <span data-ttu-id="73361-109">Publier le code de l’application sur GitHub</span><span class="sxs-lookup"><span data-stu-id="73361-109">Publish the app's code to GitHub</span></span>
* <span data-ttu-id="73361-110">Déconnecter le déploiement local de Git</span><span class="sxs-lookup"><span data-stu-id="73361-110">Disconnect local Git deployment</span></span>
* <span data-ttu-id="73361-111">Créer une organisation Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="73361-111">Create an Azure DevOps organization</span></span>
* <span data-ttu-id="73361-112">Créer un projet d’équipe dans Azure DevOps Services</span><span class="sxs-lookup"><span data-stu-id="73361-112">Create a team project in Azure DevOps Services</span></span>
* <span data-ttu-id="73361-113">Créer une définition de build</span><span class="sxs-lookup"><span data-stu-id="73361-113">Create a build definition</span></span>
* <span data-ttu-id="73361-114">Créer un pipeline de mise en production</span><span class="sxs-lookup"><span data-stu-id="73361-114">Create a release pipeline</span></span>
* <span data-ttu-id="73361-115">Valider les modifications apportées à GitHub et les déployer automatiquement dans Azure</span><span class="sxs-lookup"><span data-stu-id="73361-115">Commit changes to GitHub and automatically deploy to Azure</span></span>
* <span data-ttu-id="73361-116">Examiner le pipeline Azure Pipelines</span><span class="sxs-lookup"><span data-stu-id="73361-116">Examine the Azure Pipelines pipeline</span></span>

## <a name="publish-the-apps-code-to-github"></a><span data-ttu-id="73361-117">Publier le code de l’application sur GitHub</span><span class="sxs-lookup"><span data-stu-id="73361-117">Publish the app's code to GitHub</span></span>

1. <span data-ttu-id="73361-118">Ouvrez une fenêtre de `https://github.com`navigateur, et naviguez vers .</span><span class="sxs-lookup"><span data-stu-id="73361-118">Open a browser window, and navigate to `https://github.com`.</span></span>
1. <span data-ttu-id="73361-119">Cliquez **+** sur la baisse de l’en-tête et sélectionnez **nouveau référentiel**:</span><span class="sxs-lookup"><span data-stu-id="73361-119">Click the **+** drop-down in the header, and select **New repository**:</span></span>

    ![Option GitHub Nouveau dépôt](media/cicd/github-new-repo.png)

1. <span data-ttu-id="73361-121">Sélectionnez votre compte dans le **décrochage du propriétaire** et entrez le lecteur simple *d’alimentation* dans la boîte à texte nom **de dépôt.**</span><span class="sxs-lookup"><span data-stu-id="73361-121">Select your account in the **Owner** drop-down, and enter *simple-feed-reader* in the **Repository name** textbox.</span></span>
1. <span data-ttu-id="73361-122">Cliquez sur le bouton **Dépôt Créer.**</span><span class="sxs-lookup"><span data-stu-id="73361-122">Click the **Create repository** button.</span></span>
1. <span data-ttu-id="73361-123">Ouvrez la coque de commande de votre machine locale.</span><span class="sxs-lookup"><span data-stu-id="73361-123">Open your local machine's command shell.</span></span> <span data-ttu-id="73361-124">Naviguez vers l’annuaire dans lequel le référentiel Git *simple-feed-reader* est stocké.</span><span class="sxs-lookup"><span data-stu-id="73361-124">Navigate to the directory in which the *simple-feed-reader* Git repository is stored.</span></span>
1. <span data-ttu-id="73361-125">Renommer *l’origine* existante à distance *en amont.*</span><span class="sxs-lookup"><span data-stu-id="73361-125">Rename the existing *origin* remote to *upstream*.</span></span> <span data-ttu-id="73361-126">Exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="73361-126">Execute the following command:</span></span>

    ```console
    git remote rename origin upstream
    ```

1. <span data-ttu-id="73361-127">Ajoutez une nouvelle *télécommande d’origine* pointant vers votre copie du référentiel sur GitHub.</span><span class="sxs-lookup"><span data-stu-id="73361-127">Add a new *origin* remote pointing to your copy of the repository on GitHub.</span></span> <span data-ttu-id="73361-128">Exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="73361-128">Execute the following command:</span></span>

    ```console
    git remote add origin https://github.com/<GitHub_username>/simple-feed-reader/
    ```

1. <span data-ttu-id="73361-129">Publiez votre référentiel Git local au nouveau référentiel GitHub.</span><span class="sxs-lookup"><span data-stu-id="73361-129">Publish your local Git repository to the newly created GitHub repository.</span></span> <span data-ttu-id="73361-130">Exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="73361-130">Execute the following command:</span></span>

    ```console
    git push -u origin master
    ```

1. <span data-ttu-id="73361-131">Ouvrez une fenêtre de `https://github.com/<GitHub_username>/simple-feed-reader/`navigateur, et naviguez vers .</span><span class="sxs-lookup"><span data-stu-id="73361-131">Open a browser window, and navigate to `https://github.com/<GitHub_username>/simple-feed-reader/`.</span></span> <span data-ttu-id="73361-132">Validez que votre code apparaît dans le référentiel GitHub.</span><span class="sxs-lookup"><span data-stu-id="73361-132">Validate that your code appears in the GitHub repository.</span></span>

## <a name="disconnect-local-git-deployment"></a><span data-ttu-id="73361-133">Déconnecter le déploiement local de Git</span><span class="sxs-lookup"><span data-stu-id="73361-133">Disconnect local Git deployment</span></span>

<span data-ttu-id="73361-134">Retirez le déploiement local de Git avec les étapes suivantes.</span><span class="sxs-lookup"><span data-stu-id="73361-134">Remove the local Git deployment with the following steps.</span></span> <span data-ttu-id="73361-135">Azure Pipelines (un service Azure DevOps) remplace et augmente cette fonctionnalité.</span><span class="sxs-lookup"><span data-stu-id="73361-135">Azure Pipelines (an Azure DevOps service) both replaces and augments that functionality.</span></span>

1. <span data-ttu-id="73361-136">Ouvrez le [portail Azure](https://portal.azure.com/)et naviguez vers la *mise en scène (mywebapp\<\>unique_number/mise en scène)* Web App.</span><span class="sxs-lookup"><span data-stu-id="73361-136">Open the [Azure portal](https://portal.azure.com/), and navigate to the *staging (mywebapp\<unique_number\>/staging)* Web App.</span></span> <span data-ttu-id="73361-137">L’application Web peut être rapidement localisée en entrant la *mise en scène* dans la boîte de recherche du portail :</span><span class="sxs-lookup"><span data-stu-id="73361-137">The Web App can be quickly located by entering *staging* in the portal's search box:</span></span>

    ![mise en scène du terme de recherche d’applications Web](media/cicd/portal-search-box.png)

1. <span data-ttu-id="73361-139">Cliquez sur **Deployment Center**.</span><span class="sxs-lookup"><span data-stu-id="73361-139">Click **Deployment Center**.</span></span> <span data-ttu-id="73361-140">Un nouveau panneau apparaît.</span><span class="sxs-lookup"><span data-stu-id="73361-140">A new panel appears.</span></span> <span data-ttu-id="73361-141">Cliquez **sur Disconnect** pour supprimer la configuration locale de contrôle des sources Git qui a été ajoutée dans le chapitre précédent.</span><span class="sxs-lookup"><span data-stu-id="73361-141">Click **Disconnect** to remove the local Git source control configuration that was added in the previous chapter.</span></span> <span data-ttu-id="73361-142">Confirmez l’opération de suppression en cliquant sur le bouton **Oui.**</span><span class="sxs-lookup"><span data-stu-id="73361-142">Confirm the removal operation by clicking the **Yes** button.</span></span>
1. <span data-ttu-id="73361-143">Naviguez vers le *service d’applications<unique_number<mywebapp>.*</span><span class="sxs-lookup"><span data-stu-id="73361-143">Navigate to the *mywebapp<unique_number>* App Service.</span></span> <span data-ttu-id="73361-144">Pour rappel, la boîte de recherche du portail peut être utilisée pour localiser rapidement le service d’application.</span><span class="sxs-lookup"><span data-stu-id="73361-144">As a reminder, the portal's search box can be used to quickly locate the App Service.</span></span>
1. <span data-ttu-id="73361-145">Cliquez sur **Deployment Center**.</span><span class="sxs-lookup"><span data-stu-id="73361-145">Click **Deployment Center**.</span></span> <span data-ttu-id="73361-146">Un nouveau panneau apparaît.</span><span class="sxs-lookup"><span data-stu-id="73361-146">A new panel appears.</span></span> <span data-ttu-id="73361-147">Cliquez **sur Disconnect** pour supprimer la configuration locale de contrôle des sources Git qui a été ajoutée dans le chapitre précédent.</span><span class="sxs-lookup"><span data-stu-id="73361-147">Click **Disconnect** to remove the local Git source control configuration that was added in the previous chapter.</span></span> <span data-ttu-id="73361-148">Confirmez l’opération de suppression en cliquant sur le bouton **Oui.**</span><span class="sxs-lookup"><span data-stu-id="73361-148">Confirm the removal operation by clicking the **Yes** button.</span></span>

## <a name="create-an-azure-devops-organization"></a><span data-ttu-id="73361-149">Créer une organisation Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="73361-149">Create an Azure DevOps organization</span></span>

1. <span data-ttu-id="73361-150">Ouvrez un navigateur et naviguez vers la [page de création de l’organisation Azure DevOps](https://go.microsoft.com/fwlink/?LinkId=307137).</span><span class="sxs-lookup"><span data-stu-id="73361-150">Open a browser, and navigate to the [Azure DevOps organization creation page](https://go.microsoft.com/fwlink/?LinkId=307137).</span></span>
1. <span data-ttu-id="73361-151">Tapez un nom unique dans la boîte à **texte Pick un nom mémorable** pour former l’URL pour accéder à votre organisation Azure DevOps.</span><span class="sxs-lookup"><span data-stu-id="73361-151">Type a unique name into the **Pick a memorable name** textbox to form the URL for accessing your Azure DevOps organization.</span></span>
1. <span data-ttu-id="73361-152">Sélectionnez le bouton radio **Git,** puisque le code est hébergé dans un référentiel GitHub.</span><span class="sxs-lookup"><span data-stu-id="73361-152">Select the **Git** radio button, since the code is hosted in a GitHub repository.</span></span>
1. <span data-ttu-id="73361-153">Cliquez sur le bouton **Continuer**.</span><span class="sxs-lookup"><span data-stu-id="73361-153">Click the **Continue** button.</span></span> <span data-ttu-id="73361-154">Après une courte attente, un compte et un projet d’équipe, nommé *MyFirstProject*, sont créés.</span><span class="sxs-lookup"><span data-stu-id="73361-154">After a short wait, an account and a team project, named *MyFirstProject*, are created.</span></span>

    ![Page de création d’organisation Azure DevOps](media/cicd/vsts-account-creation.png)

1. <span data-ttu-id="73361-156">Ouvrez l’e-mail de confirmation indiquant que l’organisation et le projet Azure DevOps sont prêts à être utilisés.</span><span class="sxs-lookup"><span data-stu-id="73361-156">Open the confirmation email indicating that the Azure DevOps organization and project are ready for use.</span></span> <span data-ttu-id="73361-157">Cliquez **sur** le bouton Démarrer votre projet :</span><span class="sxs-lookup"><span data-stu-id="73361-157">Click the **Start your project** button:</span></span>

    ![Démarrez le bouton de votre projet](media/cicd/vsts-start-project.png)

1. <span data-ttu-id="73361-159">Un navigateur s’ouvre à \* \<\>account_name .visualstudio.com\*.</span><span class="sxs-lookup"><span data-stu-id="73361-159">A browser opens to *\<account_name\>.visualstudio.com*.</span></span> <span data-ttu-id="73361-160">Cliquez sur le lien *MyFirstProject* pour commencer à configurer le pipeline DevOps du projet.</span><span class="sxs-lookup"><span data-stu-id="73361-160">Click the *MyFirstProject* link to begin configuring the project's DevOps pipeline.</span></span>

## <a name="configure-the-azure-pipelines-pipeline"></a><span data-ttu-id="73361-161">Configurer le pipeline Azure Pipelines</span><span class="sxs-lookup"><span data-stu-id="73361-161">Configure the Azure Pipelines pipeline</span></span>

<span data-ttu-id="73361-162">Il y a trois étapes distinctes à compléter.</span><span class="sxs-lookup"><span data-stu-id="73361-162">There are three distinct steps to complete.</span></span> <span data-ttu-id="73361-163">L’achèvement des étapes dans les trois sections suivantes se traduit par un pipeline opérationnel DevOps.</span><span class="sxs-lookup"><span data-stu-id="73361-163">Completing the steps in the following three sections results in an operational DevOps pipeline.</span></span>

### <a name="grant-azure-devops-access-to-the-github-repository"></a><span data-ttu-id="73361-164">Accorder l’accès à Azure DevOps au dépôt GitHub</span><span class="sxs-lookup"><span data-stu-id="73361-164">Grant Azure DevOps access to the GitHub repository</span></span>

1. <span data-ttu-id="73361-165">Étendre le **code ou construire à partir d’un** accordéon de dépôt externe.</span><span class="sxs-lookup"><span data-stu-id="73361-165">Expand the **or build code from an external repository** accordion.</span></span> <span data-ttu-id="73361-166">Cliquez sur le bouton **Setup Build** :</span><span class="sxs-lookup"><span data-stu-id="73361-166">Click the **Setup Build** button:</span></span>

    ![Bouton Configurer la build](media/cicd/vsts-setup-build.png)

1. <span data-ttu-id="73361-168">Sélectionnez l’option **GitHub** dans la section **Sélectionnez une source** :</span><span class="sxs-lookup"><span data-stu-id="73361-168">Select the **GitHub** option from the **Select a source** section:</span></span>

    ![Sélectionnez une source - GitHub](media/cicd/vsts-select-source.png)

1. <span data-ttu-id="73361-170">L’autorisation est requise avant qu’Azure DevOps puisse accéder à votre référentiel GitHub.</span><span class="sxs-lookup"><span data-stu-id="73361-170">Authorization is required before Azure DevOps can access your GitHub repository.</span></span> <span data-ttu-id="73361-171">Entrez *<GitHub_username connexion GitHub>* dans la boîte à texte nom de **connexion.**</span><span class="sxs-lookup"><span data-stu-id="73361-171">Enter *<GitHub_username> GitHub connection* in the **Connection name** textbox.</span></span> <span data-ttu-id="73361-172">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="73361-172">For example:</span></span>

    ![Nom de connexion GitHub](media/cicd/vsts-repo-authz.png)

1. <span data-ttu-id="73361-174">Si l’authentification à deux facteurs est activée sur votre compte GitHub, un jeton d’accès personnel est nécessaire.</span><span class="sxs-lookup"><span data-stu-id="73361-174">If two-factor authentication is enabled on your GitHub account, a personal access token is required.</span></span> <span data-ttu-id="73361-175">Dans ce cas, cliquez sur **l’Autoriser avec un lien symbolique d’accès personnel GitHub.**</span><span class="sxs-lookup"><span data-stu-id="73361-175">In that case, click the **Authorize with a GitHub personal access token** link.</span></span> <span data-ttu-id="73361-176">Consultez les [instructions officielles de création personnelle de jetons GitHub](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/) pour obtenir de l’aide.</span><span class="sxs-lookup"><span data-stu-id="73361-176">See the [official GitHub personal access token creation instructions](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/) for help.</span></span> <span data-ttu-id="73361-177">Seule *la* portée des autorisations est nécessaire.</span><span class="sxs-lookup"><span data-stu-id="73361-177">Only the *repo* scope of permissions is needed.</span></span> <span data-ttu-id="73361-178">Sinon, cliquez sur **l’autoriser à l’aide du bouton OAuth.**</span><span class="sxs-lookup"><span data-stu-id="73361-178">Otherwise, click the **Authorize using OAuth** button.</span></span>
1. <span data-ttu-id="73361-179">Lorsqu’on vous le demande, connectez-vous à votre compte GitHub.</span><span class="sxs-lookup"><span data-stu-id="73361-179">When prompted, sign in to your GitHub account.</span></span> <span data-ttu-id="73361-180">Ensuite, sélectionnez Autoriser pour accorder l’accès à votre organisation Azure DevOps.</span><span class="sxs-lookup"><span data-stu-id="73361-180">Then select Authorize to grant access to your Azure DevOps organization.</span></span> <span data-ttu-id="73361-181">En cas de succès, un nouveau critère de service est créé.</span><span class="sxs-lookup"><span data-stu-id="73361-181">If successful, a new service endpoint is created.</span></span>
1. <span data-ttu-id="73361-182">Cliquez sur le bouton ellipsis à côté du bouton **Repository.**</span><span class="sxs-lookup"><span data-stu-id="73361-182">Click the ellipsis button next to the **Repository** button.</span></span> <span data-ttu-id="73361-183">Sélectionnez le *<GitHub_username>/simple-feed-reader* repository de la liste.</span><span class="sxs-lookup"><span data-stu-id="73361-183">Select the *<GitHub_username>/simple-feed-reader* repository from the list.</span></span> <span data-ttu-id="73361-184">Cliquez sur le bouton **Sélectionner**.</span><span class="sxs-lookup"><span data-stu-id="73361-184">Click the **Select** button.</span></span>
1. <span data-ttu-id="73361-185">Sélectionnez la branche *principale* de la branche Par défaut pour les builds de chute **manuelles et planifiées.**</span><span class="sxs-lookup"><span data-stu-id="73361-185">Select the *master* branch from the **Default branch for manual and scheduled builds** drop-down.</span></span> <span data-ttu-id="73361-186">Cliquez sur le bouton **Continuer**.</span><span class="sxs-lookup"><span data-stu-id="73361-186">Click the **Continue** button.</span></span> <span data-ttu-id="73361-187">La page de sélection du modèle apparaît.</span><span class="sxs-lookup"><span data-stu-id="73361-187">The template selection page appears.</span></span>

### <a name="create-the-build-definition"></a><span data-ttu-id="73361-188">Créer la définition de build</span><span class="sxs-lookup"><span data-stu-id="73361-188">Create the build definition</span></span>

1. <span data-ttu-id="73361-189">À partir de la page de sélection du modèle, entrez *ASP.NET Core* dans la zone de recherche :</span><span class="sxs-lookup"><span data-stu-id="73361-189">From the template selection page, enter *ASP.NET Core* in the search box:</span></span>

    ![recherche ASP.NET Core sur la page du modèle](media/cicd/vsts-template-selection.png)

1. <span data-ttu-id="73361-191">Les résultats de recherche de modèle apparaissent.</span><span class="sxs-lookup"><span data-stu-id="73361-191">The template search results appear.</span></span> <span data-ttu-id="73361-192">Planer au-dessus du modèle **ASP.NET Core** et cliquez sur le bouton **Appliquer.**</span><span class="sxs-lookup"><span data-stu-id="73361-192">Hover over the **ASP.NET Core** template, and click the **Apply** button.</span></span>
1. <span data-ttu-id="73361-193">**L’onglet Tâches** de la définition de construction apparaît.</span><span class="sxs-lookup"><span data-stu-id="73361-193">The **Tasks** tab of the build definition appears.</span></span> <span data-ttu-id="73361-194">Cliquez sur l’onglet **Déclencheurs.**</span><span class="sxs-lookup"><span data-stu-id="73361-194">Click the **Triggers** tab.</span></span>
1. <span data-ttu-id="73361-195">Vérifiez la boîte **d’intégration continue Enable.**</span><span class="sxs-lookup"><span data-stu-id="73361-195">Check the **Enable continuous integration** box.</span></span> <span data-ttu-id="73361-196">Dans la section **des filtres de la Direction,** confirmer que le **drop-down type** est configuré pour *inclure*.</span><span class="sxs-lookup"><span data-stu-id="73361-196">Under the **Branch filters** section, confirm that the **Type** drop-down is set to *Include*.</span></span> <span data-ttu-id="73361-197">Définissez la **baisse de spécification de la Direction** à *maîtriser*.</span><span class="sxs-lookup"><span data-stu-id="73361-197">Set the **Branch specification** drop-down to *master*.</span></span>

    ![Activer les paramètres d’intégration continue](media/cicd/vsts-enable-ci.png)

    <span data-ttu-id="73361-199">Ces paramètres provoquent un déclencheur de build lorsque toute modification est poussée à la branche *principale* du référentiel GitHub.</span><span class="sxs-lookup"><span data-stu-id="73361-199">These settings cause a build to trigger when any change is pushed to the *master* branch of the GitHub repository.</span></span> <span data-ttu-id="73361-200">L’intégration continue est testée dans les [modifications Commit de GitHub et déployée automatiquement dans la section Azure.](#commit-changes-to-github-and-automatically-deploy-to-azure)</span><span class="sxs-lookup"><span data-stu-id="73361-200">Continuous integration is tested in the [Commit changes to GitHub and automatically deploy to Azure](#commit-changes-to-github-and-automatically-deploy-to-azure) section.</span></span>

1. <span data-ttu-id="73361-201">Cliquez sur le bouton **Enregistrer & file d’attente** et sélectionnez **l’option Enregistrer** :</span><span class="sxs-lookup"><span data-stu-id="73361-201">Click the **Save & queue** button, and select the **Save** option:</span></span>

    ![Bouton Enregistrer](media/cicd/vsts-save-build.png)

1. <span data-ttu-id="73361-203">Le dialogue modal suivant apparaît :</span><span class="sxs-lookup"><span data-stu-id="73361-203">The following modal dialog appears:</span></span>

    ![Enregistrer la définition de construction - dialogue modal](media/cicd/vsts-save-modal.png)

    <span data-ttu-id="73361-205">Utilisez le dossier *\\*par défaut de , et cliquez sur le bouton **Enregistrer.**</span><span class="sxs-lookup"><span data-stu-id="73361-205">Use the default folder of *\\*, and click the **Save** button.</span></span>

### <a name="create-the-release-pipeline"></a><span data-ttu-id="73361-206">Créer le pipeline de mise en production</span><span class="sxs-lookup"><span data-stu-id="73361-206">Create the release pipeline</span></span>

1. <span data-ttu-id="73361-207">Cliquez sur l’onglet **Communiqués** de votre projet d’équipe.</span><span class="sxs-lookup"><span data-stu-id="73361-207">Click the **Releases** tab of your team project.</span></span> <span data-ttu-id="73361-208">Cliquez sur le nouveau bouton **de pipeline.**</span><span class="sxs-lookup"><span data-stu-id="73361-208">Click the **New pipeline** button.</span></span>

    ![Onglet De libération - Nouveau bouton de définition](media/cicd/vsts-new-release-definition.png)

    <span data-ttu-id="73361-210">Le volet de sélection de modèle apparaît.</span><span class="sxs-lookup"><span data-stu-id="73361-210">The template selection pane appears.</span></span>

1. <span data-ttu-id="73361-211">À partir de la page de sélection du modèle, entrez *App Service* dans la zone de recherche :</span><span class="sxs-lookup"><span data-stu-id="73361-211">From the template selection page, enter *App Service* in the search box:</span></span>

    ![Libérer la boîte de recherche de modèle de pipeline](media/cicd/vsts-release-template-search.png)

1. <span data-ttu-id="73361-213">Les résultats de recherche de modèle apparaissent.</span><span class="sxs-lookup"><span data-stu-id="73361-213">The template search results appear.</span></span> <span data-ttu-id="73361-214">Survolez le **déploiement du service d’application Azure avec le** modèle de fente et cliquez sur le bouton **Appliquer.**</span><span class="sxs-lookup"><span data-stu-id="73361-214">Hover over the **Azure App Service Deployment with Slot** template, and click the **Apply** button.</span></span> <span data-ttu-id="73361-215">**L’onglet Pipeline** du pipeline de rejet apparaît.</span><span class="sxs-lookup"><span data-stu-id="73361-215">The **Pipeline** tab of the release pipeline appears.</span></span>

    ![Déblocage de l’onglet pipeline](media/cicd/vsts-release-definition-pipeline.png)

1. <span data-ttu-id="73361-217">Cliquez sur le bouton **Ajouter** dans la boîte **Artefacts.**</span><span class="sxs-lookup"><span data-stu-id="73361-217">Click the **Add** button in the **Artifacts** box.</span></span> <span data-ttu-id="73361-218">Le panneau **d’artefact Add** apparaît :</span><span class="sxs-lookup"><span data-stu-id="73361-218">The **Add artifact** panel appears:</span></span>

    ![Pipeline de libération - Ajouter un panneau d’artefact](media/cicd/vsts-release-add-artifact.png)

1. <span data-ttu-id="73361-220">Sélectionnez la tuile **Build** de la section **source de type** Source.</span><span class="sxs-lookup"><span data-stu-id="73361-220">Select the **Build** tile from the **Source type** section.</span></span> <span data-ttu-id="73361-221">Ce type permet de relier le pipeline de version à la définition de construction.</span><span class="sxs-lookup"><span data-stu-id="73361-221">This type allows for the linking of the release pipeline to the build definition.</span></span>
1. <span data-ttu-id="73361-222">Sélectionnez *MyFirstProject* de l’abandon **du projet.**</span><span class="sxs-lookup"><span data-stu-id="73361-222">Select *MyFirstProject* from the **Project** drop-down.</span></span>
1. <span data-ttu-id="73361-223">Sélectionnez le nom de définition de construction, *MyFirstProject-ASP.NET Core-CI*, à partir de la **source (définition de construction)** drop-down.</span><span class="sxs-lookup"><span data-stu-id="73361-223">Select the build definition name, *MyFirstProject-ASP.NET Core-CI*, from the **Source (Build definition)** drop-down.</span></span>
1. <span data-ttu-id="73361-224">Sélectionnez *les dernières nouveautés* de la **version par défaut.**</span><span class="sxs-lookup"><span data-stu-id="73361-224">Select *Latest* from the **Default version** drop-down.</span></span> <span data-ttu-id="73361-225">Cette option construit les artefacts produits par la dernière série de la définition de construction.</span><span class="sxs-lookup"><span data-stu-id="73361-225">This option builds the artifacts produced by the latest run of the build definition.</span></span>
1. <span data-ttu-id="73361-226">Remplacez le texte dans la boîte **à texte d’alias Source** par *Drop*.</span><span class="sxs-lookup"><span data-stu-id="73361-226">Replace the text in the **Source alias** textbox with *Drop*.</span></span>
1. <span data-ttu-id="73361-227">Cliquez sur le bouton **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="73361-227">Click the **Add** button.</span></span> <span data-ttu-id="73361-228">Les mises à jour de la section Artefacts pour afficher les **modifications.**</span><span class="sxs-lookup"><span data-stu-id="73361-228">The **Artifacts** section updates to display the changes.</span></span>
1. <span data-ttu-id="73361-229">Cliquez sur l’icône de l’éclair pour activer des déploiements continus :</span><span class="sxs-lookup"><span data-stu-id="73361-229">Click the lightning bolt icon to enable continuous deployments:</span></span>

    ![Déchargez les artefacts du pipeline - icône de l’éclair](media/cicd/vsts-artifacts-lightning-bolt.png)

    <span data-ttu-id="73361-231">Avec cette option activée, un déploiement se produit chaque fois qu’une nouvelle version est disponible.</span><span class="sxs-lookup"><span data-stu-id="73361-231">With this option enabled, a deployment occurs each time a new build is available.</span></span>
1. <span data-ttu-id="73361-232">Un panneau **de déclenchement de déploiement continu** apparaît à droite.</span><span class="sxs-lookup"><span data-stu-id="73361-232">A **Continuous deployment trigger** panel appears to the right.</span></span> <span data-ttu-id="73361-233">Cliquez sur le bouton basculement pour activer la fonctionnalité.</span><span class="sxs-lookup"><span data-stu-id="73361-233">Click the toggle button to enable the feature.</span></span> <span data-ttu-id="73361-234">Il n’est pas nécessaire d’activer la **demande Pull déclencheur**.</span><span class="sxs-lookup"><span data-stu-id="73361-234">It isn't necessary to enable the **Pull request trigger**.</span></span>
1. <span data-ttu-id="73361-235">Cliquez sur la chute **Add** dans la section **filtres de branche Build.**</span><span class="sxs-lookup"><span data-stu-id="73361-235">Click the **Add** drop-down in the **Build branch filters** section.</span></span> <span data-ttu-id="73361-236">Choisissez **l’option de branche par défaut de** la définition de build.</span><span class="sxs-lookup"><span data-stu-id="73361-236">Choose the **Build Definition's default branch** option.</span></span> <span data-ttu-id="73361-237">Ce filtre ne déclenche la version que pour une version à partir de la branche *principale* du référentiel GitHub.</span><span class="sxs-lookup"><span data-stu-id="73361-237">This filter causes the release to trigger only for a build from the GitHub repository's *master* branch.</span></span>
1. <span data-ttu-id="73361-238">Cliquez sur le bouton **Enregistrer**.</span><span class="sxs-lookup"><span data-stu-id="73361-238">Click the **Save** button.</span></span> <span data-ttu-id="73361-239">Cliquez sur le bouton **OK** dans le dialogue modal **Save** résultant.</span><span class="sxs-lookup"><span data-stu-id="73361-239">Click the **OK** button in the resulting **Save** modal dialog.</span></span>
1. <span data-ttu-id="73361-240">Cliquez sur la case **Environnement 1.**</span><span class="sxs-lookup"><span data-stu-id="73361-240">Click the **Environment 1** box.</span></span> <span data-ttu-id="73361-241">Un panneau **environnement** apparaît à droite.</span><span class="sxs-lookup"><span data-stu-id="73361-241">An **Environment** panel appears to the right.</span></span> <span data-ttu-id="73361-242">Changer le texte *environnement 1* dans la boîte à **texte nom environnement** à la *production*.</span><span class="sxs-lookup"><span data-stu-id="73361-242">Change the *Environment 1* text in the **Environment name** textbox to *Production*.</span></span>

   ![Pipeline de libération - Boîte à texte nom de l’environnement](media/cicd/vsts-environment-name-textbox.png)

1. <span data-ttu-id="73361-244">Cliquez sur la **phase 1, 2 tâches** lien dans la boîte **de production:**</span><span class="sxs-lookup"><span data-stu-id="73361-244">Click the **1 phase, 2 tasks** link in the **Production** box:</span></span>

    ![Pipeline de sortie - Liaison de l’environnement de production.png](media/cicd/vsts-production-link.png)

    <span data-ttu-id="73361-246">**L’onglet Tâches** de l’environnement apparaît.</span><span class="sxs-lookup"><span data-stu-id="73361-246">The **Tasks** tab of the environment appears.</span></span>
1. <span data-ttu-id="73361-247">Cliquez sur le **service d’application Déployer Azure pour la** tâche de fente.</span><span class="sxs-lookup"><span data-stu-id="73361-247">Click the **Deploy Azure App Service to Slot** task.</span></span> <span data-ttu-id="73361-248">Ses paramètres apparaissent dans un panneau à droite.</span><span class="sxs-lookup"><span data-stu-id="73361-248">Its settings appear in a panel to the right.</span></span>
1. <span data-ttu-id="73361-249">Sélectionnez l’abonnement Azure associé au service App à partir de **l’abonnement Azure.**</span><span class="sxs-lookup"><span data-stu-id="73361-249">Select the Azure subscription associated with the App Service from the **Azure subscription** drop-down.</span></span> <span data-ttu-id="73361-250">Une fois sélectionné, cliquez sur le bouton **Autoriser.**</span><span class="sxs-lookup"><span data-stu-id="73361-250">Once selected, click the **Authorize** button.</span></span>
1. <span data-ttu-id="73361-251">Sélectionnez *l’application Web* à partir du **drop-down de type App.**</span><span class="sxs-lookup"><span data-stu-id="73361-251">Select *Web App* from the **App type** drop-down.</span></span>
1. <span data-ttu-id="73361-252">Sélectionnez *mywebapp/<unique_number/>* du nom de **service App** drop-down.</span><span class="sxs-lookup"><span data-stu-id="73361-252">Select *mywebapp/<unique_number/>* from the **App service name** drop-down.</span></span>
1. <span data-ttu-id="73361-253">Sélectionnez *AzureTutorial* parmi le **groupe Resource** drop-down.</span><span class="sxs-lookup"><span data-stu-id="73361-253">Select *AzureTutorial* from the **Resource group** drop-down.</span></span>
1. <span data-ttu-id="73361-254">Sélectionnez *la mise en scène* à partir de la mise à jour de la **machine** à sous.</span><span class="sxs-lookup"><span data-stu-id="73361-254">Select *staging* from the **Slot** drop-down.</span></span>
1. <span data-ttu-id="73361-255">Cliquez sur le bouton **Enregistrer**.</span><span class="sxs-lookup"><span data-stu-id="73361-255">Click the **Save** button.</span></span>
1. <span data-ttu-id="73361-256">Planer au-dessus du nom du pipeline de version par défaut.</span><span class="sxs-lookup"><span data-stu-id="73361-256">Hover over the default release pipeline name.</span></span> <span data-ttu-id="73361-257">Cliquez sur l’icône du crayon pour l’éditer.</span><span class="sxs-lookup"><span data-stu-id="73361-257">Click the pencil icon to edit it.</span></span> <span data-ttu-id="73361-258">Utilisez *MyFirstProject-ASP.NET Core-CD* comme nom.</span><span class="sxs-lookup"><span data-stu-id="73361-258">Use *MyFirstProject-ASP.NET Core-CD* as the name.</span></span>

    ![Nom du pipeline de libération](media/cicd/vsts-release-definition-name.png)

1. <span data-ttu-id="73361-260">Cliquez sur le bouton **Enregistrer**.</span><span class="sxs-lookup"><span data-stu-id="73361-260">Click the **Save** button.</span></span>

## <a name="commit-changes-to-github-and-automatically-deploy-to-azure"></a><span data-ttu-id="73361-261">Valider les modifications apportées à GitHub et les déployer automatiquement dans Azure</span><span class="sxs-lookup"><span data-stu-id="73361-261">Commit changes to GitHub and automatically deploy to Azure</span></span>

1. <span data-ttu-id="73361-262">Ouvrez *SimpleFeedReader.sln* dans Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="73361-262">Open *SimpleFeedReader.sln* in Visual Studio.</span></span>
1. <span data-ttu-id="73361-263">Dans Solution Explorer, ouvrez *Pages-Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="73361-263">In Solution Explorer, open *Pages\Index.cshtml*.</span></span> <span data-ttu-id="73361-264">Remplacez `<h2>Simple Feed Reader - V3</h2>` par `<h2>Simple Feed Reader - V4</h2>`.</span><span class="sxs-lookup"><span data-stu-id="73361-264">Change `<h2>Simple Feed Reader - V3</h2>` to `<h2>Simple Feed Reader - V4</h2>`.</span></span>
1. <span data-ttu-id="73361-265">Appuyez sur **Ctrl**+**Shift**+**B** pour construire l’application.</span><span class="sxs-lookup"><span data-stu-id="73361-265">Press **Ctrl**+**Shift**+**B** to build the app.</span></span>
1. <span data-ttu-id="73361-266">Engagez le fichier dans le référentiel GitHub.</span><span class="sxs-lookup"><span data-stu-id="73361-266">Commit the file to the GitHub repository.</span></span> <span data-ttu-id="73361-267">Utilisez soit la page **Modifications** dans l’onglet *Team Explorer* de Visual Studio, soit exécutez ce qui suit à l’aide de la coque de commande de la machine locale :</span><span class="sxs-lookup"><span data-stu-id="73361-267">Use either the **Changes** page in Visual Studio's *Team Explorer* tab, or execute the following using the local machine's command shell:</span></span>

    ```console
    git commit -a -m "upgraded to V4"
    ```

1. <span data-ttu-id="73361-268">Poussez le changement dans la branche *principale* à la télécommande *d’origine* de votre référentiel GitHub :</span><span class="sxs-lookup"><span data-stu-id="73361-268">Push the change in the *master* branch to the *origin* remote of your GitHub repository:</span></span>

    ```console
    git push origin master
    ```

    <span data-ttu-id="73361-269">L’commit apparaît dans la branche *principale* du référentiel GitHub :</span><span class="sxs-lookup"><span data-stu-id="73361-269">The commit appears in the GitHub repository's *master* branch:</span></span>

    ![GitHub s’engager dans la branche maître](media/cicd/github-commit.png)

    <span data-ttu-id="73361-271">La construction est déclenchée, puisque l’intégration continue est activée dans l’onglet **Déclencheurs** de la définition de construction :</span><span class="sxs-lookup"><span data-stu-id="73361-271">The build is triggered, since continuous integration is enabled in the build definition's **Triggers** tab:</span></span>

    ![permettre une intégration continue](media/cicd/enable-ci.png)

1. <span data-ttu-id="73361-273">Naviguez vers **l’onglet File d’attente** de la page **Azure Pipelines** > **Builds** dans Azure DevOps Services.</span><span class="sxs-lookup"><span data-stu-id="73361-273">Navigate to the **Queued** tab of the **Azure Pipelines** > **Builds** page in Azure DevOps Services.</span></span> <span data-ttu-id="73361-274">La construction en file d’attente montre la branche et commit qui a déclenché la construction:</span><span class="sxs-lookup"><span data-stu-id="73361-274">The queued build shows the branch and commit that triggered the build:</span></span>

    ![construction en file d’attente](media/cicd/build-queued.png)

1. <span data-ttu-id="73361-276">Une fois la construction réussie, un déploiement à Azure se produit.</span><span class="sxs-lookup"><span data-stu-id="73361-276">Once the build succeeds, a deployment to Azure occurs.</span></span> <span data-ttu-id="73361-277">Naviguez vers l’application dans le navigateur.</span><span class="sxs-lookup"><span data-stu-id="73361-277">Navigate to the app in the browser.</span></span> <span data-ttu-id="73361-278">Notez que le texte "V4" apparaît dans le titre:</span><span class="sxs-lookup"><span data-stu-id="73361-278">Notice that the "V4" text appears in the heading:</span></span>

    ![application mise à jour](media/cicd/updated-app-v4.png)

## <a name="examine-the-azure-pipelines-pipeline"></a><span data-ttu-id="73361-280">Examiner le pipeline Azure Pipelines</span><span class="sxs-lookup"><span data-stu-id="73361-280">Examine the Azure Pipelines pipeline</span></span>

### <a name="build-definition"></a><span data-ttu-id="73361-281">Définition de build</span><span class="sxs-lookup"><span data-stu-id="73361-281">Build definition</span></span>

<span data-ttu-id="73361-282">Une définition de construction a été créée avec le nom *MyFirstProject-ASP.NET Core-CI*.</span><span class="sxs-lookup"><span data-stu-id="73361-282">A build definition was created with the name *MyFirstProject-ASP.NET Core-CI*.</span></span> <span data-ttu-id="73361-283">Une fois terminé, la construction produit un fichier *.zip* comprenant les actifs à publier.</span><span class="sxs-lookup"><span data-stu-id="73361-283">Upon completion, the build produces a *.zip* file including the assets to be published.</span></span> <span data-ttu-id="73361-284">Le gazoduc de sortie déploie ces actifs à Azure.</span><span class="sxs-lookup"><span data-stu-id="73361-284">The release pipeline deploys those assets to Azure.</span></span>

<span data-ttu-id="73361-285">L’onglet **Tâches** de la définition de construction répertorie les étapes individuelles utilisées.</span><span class="sxs-lookup"><span data-stu-id="73361-285">The build definition's **Tasks** tab lists the individual steps being used.</span></span> <span data-ttu-id="73361-286">Il y a cinq tâches de construction.</span><span class="sxs-lookup"><span data-stu-id="73361-286">There are five build tasks.</span></span>

![construire des tâches de définition](media/cicd/build-definition-tasks.png)

1. <span data-ttu-id="73361-288">**Restaurer** &mdash; Exécute `dotnet restore` la commande pour restaurer les paquets NuGet de l’application.</span><span class="sxs-lookup"><span data-stu-id="73361-288">**Restore** &mdash; Executes the `dotnet restore` command to restore the app's NuGet packages.</span></span> <span data-ttu-id="73361-289">L’alimentation par défaut utilisée est nuget.org.</span><span class="sxs-lookup"><span data-stu-id="73361-289">The default package feed used is nuget.org.</span></span>
1. <span data-ttu-id="73361-290">**Build** &mdash; Exécute `dotnet build --configuration release` la commande pour compiler le code de l’application.</span><span class="sxs-lookup"><span data-stu-id="73361-290">**Build** &mdash; Executes the `dotnet build --configuration release` command to compile the app's code.</span></span> <span data-ttu-id="73361-291">Cette `--configuration` option est utilisée pour produire une version optimisée du code, qui convient au déploiement dans un environnement de production.</span><span class="sxs-lookup"><span data-stu-id="73361-291">This `--configuration` option is used to produce an optimized version of the code, which is suitable for deployment to a production environment.</span></span> <span data-ttu-id="73361-292">Modifier la variable *BuildConfiguration* sur l’onglet **Variables** de la définition de construction si, par exemple, une configuration de débaillement est nécessaire.</span><span class="sxs-lookup"><span data-stu-id="73361-292">Modify the *BuildConfiguration* variable on the build definition's **Variables** tab if, for example, a debug configuration is needed.</span></span>
1. <span data-ttu-id="73361-293">**Test** &mdash; Exécute `dotnet test --configuration release --logger trx --results-directory <local_path_on_build_agent>` la commande pour exécuter les tests unitaires de l’application.</span><span class="sxs-lookup"><span data-stu-id="73361-293">**Test** &mdash; Executes the `dotnet test --configuration release --logger trx --results-directory <local_path_on_build_agent>` command to run the app's unit tests.</span></span> <span data-ttu-id="73361-294">Les tests unitaires sont exécutés `**/*Tests/*.csproj` dans n’importe quel projet CMD correspondant au modèle de glob.</span><span class="sxs-lookup"><span data-stu-id="73361-294">Unit tests are executed within any C# project matching the `**/*Tests/*.csproj` glob pattern.</span></span> <span data-ttu-id="73361-295">Les résultats des tests sont enregistrés dans un `--results-directory` fichier *.trx* à l’emplacement spécifié par l’option.</span><span class="sxs-lookup"><span data-stu-id="73361-295">Test results are saved in a *.trx* file at the location specified by the `--results-directory` option.</span></span> <span data-ttu-id="73361-296">En cas d’échec des tests, la construction échoue et n’est pas déployée.</span><span class="sxs-lookup"><span data-stu-id="73361-296">If any tests fail, the build fails and isn't deployed.</span></span>

    > [!NOTE]
    > <span data-ttu-id="73361-297">Pour vérifier le fonctionnement des tests unitaires, modifiez *SimpleFeedReader.Tests-Services-NewsServiceTests.cs* pour casser délibérément l’un des tests.</span><span class="sxs-lookup"><span data-stu-id="73361-297">To verify the unit tests work, modify *SimpleFeedReader.Tests\Services\NewsServiceTests.cs* to purposefully break one of the tests.</span></span> <span data-ttu-id="73361-298">Par exemple, `Assert.True(result.Count > 0);` `Assert.False(result.Count > 0);` changez `Returns_News_Stories_Given_Valid_Uri` dans la méthode.</span><span class="sxs-lookup"><span data-stu-id="73361-298">For example, change `Assert.True(result.Count > 0);` to `Assert.False(result.Count > 0);` in the `Returns_News_Stories_Given_Valid_Uri` method.</span></span> <span data-ttu-id="73361-299">Engagez et poussez la modification à GitHub.</span><span class="sxs-lookup"><span data-stu-id="73361-299">Commit and push the change to GitHub.</span></span> <span data-ttu-id="73361-300">La construction est déclenchée et échoue.</span><span class="sxs-lookup"><span data-stu-id="73361-300">The build is triggered and fails.</span></span> <span data-ttu-id="73361-301">L’état de l’état du pipeline de construction change à **l’échec**.</span><span class="sxs-lookup"><span data-stu-id="73361-301">The build pipeline status changes to **failed**.</span></span> <span data-ttu-id="73361-302">Retournez le changement, engagez-vous et poussez à nouveau.</span><span class="sxs-lookup"><span data-stu-id="73361-302">Revert the change, commit, and push again.</span></span> <span data-ttu-id="73361-303">La construction réussit.</span><span class="sxs-lookup"><span data-stu-id="73361-303">The build succeeds.</span></span>

1. <span data-ttu-id="73361-304">**Publier** &mdash; Exécute `dotnet publish --configuration release --output <local_path_on_build_agent>` la commande pour produire un fichier *.zip* avec les artefacts à déployer.</span><span class="sxs-lookup"><span data-stu-id="73361-304">**Publish** &mdash; Executes the `dotnet publish --configuration release --output <local_path_on_build_agent>` command to produce a *.zip* file with the artifacts to be deployed.</span></span> <span data-ttu-id="73361-305">L’option `--output` spécifie l’emplacement de publication du fichier *.zip.*</span><span class="sxs-lookup"><span data-stu-id="73361-305">The `--output` option specifies the publish location of the *.zip* file.</span></span> <span data-ttu-id="73361-306">Cet emplacement est spécifié en passant une `$(build.artifactstagingdirectory)` [variable prédéfinie](/azure/devops/pipelines/build/variables) nommée .</span><span class="sxs-lookup"><span data-stu-id="73361-306">That location is specified by passing a [predefined variable](/azure/devops/pipelines/build/variables) named `$(build.artifactstagingdirectory)`.</span></span> <span data-ttu-id="73361-307">Cette variable s’étend à un chemin local, comme *le travail de c: 'agent\_'1 'a*, sur l’agent de construction.</span><span class="sxs-lookup"><span data-stu-id="73361-307">That variable expands to a local path, such as *c:\agent\_work\1\a*, on the build agent.</span></span>
1. <span data-ttu-id="73361-308">**Publier Artifact** &mdash; Publie le fichier *.zip* produit par la tâche **Publier.**</span><span class="sxs-lookup"><span data-stu-id="73361-308">**Publish Artifact** &mdash; Publishes the *.zip* file produced by the **Publish** task.</span></span> <span data-ttu-id="73361-309">La tâche accepte l’emplacement du fichier *.zip* comme un `$(build.artifactstagingdirectory)`paramètre, qui est la variable prédéfinie .</span><span class="sxs-lookup"><span data-stu-id="73361-309">The task accepts the *.zip* file location as a parameter, which is the predefined variable `$(build.artifactstagingdirectory)`.</span></span> <span data-ttu-id="73361-310">Le fichier *.zip* est publié sous forme de dossier nommé *drop*.</span><span class="sxs-lookup"><span data-stu-id="73361-310">The *.zip* file is published as a folder named *drop*.</span></span>

<span data-ttu-id="73361-311">Cliquez sur le lien **sommaire** de la définition de build pour afficher une historique des builds avec la définition :</span><span class="sxs-lookup"><span data-stu-id="73361-311">Click the build definition's **Summary** link to view a history of builds with the definition:</span></span>

![Capture d’écran montrant l’historique de définition de construction](media/cicd/build-definition-summary.png)

<span data-ttu-id="73361-313">Sur la page résultante, cliquez sur le lien correspondant au numéro de build unique :</span><span class="sxs-lookup"><span data-stu-id="73361-313">On the resulting page, click the link corresponding to the unique build number:</span></span>

![Capture d’écran montrant la page de résumé de définition de construction](media/cicd/build-definition-completed.png)

<span data-ttu-id="73361-315">Un résumé de cette version spécifique est affiché.</span><span class="sxs-lookup"><span data-stu-id="73361-315">A summary of this specific build is displayed.</span></span> <span data-ttu-id="73361-316">Cliquez sur l’onglet **Artefacts,** et remarquez que le dossier *de chute* produit par la build est répertorié :</span><span class="sxs-lookup"><span data-stu-id="73361-316">Click the **Artifacts** tab, and notice the *drop* folder produced by the build is listed:</span></span>

![Capture d’écran montrant des artefacts de définition de construction - dossier de chute](media/cicd/build-definition-artifacts.png)

<span data-ttu-id="73361-318">Utilisez les liens **Télécharger** et **explorer** pour inspecter les artefacts publiés.</span><span class="sxs-lookup"><span data-stu-id="73361-318">Use the **Download** and **Explore** links to inspect the published artifacts.</span></span>

### <a name="release-pipeline"></a><span data-ttu-id="73361-319">Pipeline de mise en production</span><span class="sxs-lookup"><span data-stu-id="73361-319">Release pipeline</span></span>

<span data-ttu-id="73361-320">Un pipeline de version a été créé avec le nom *MyFirstProject-ASP.NET Core-CD*:</span><span class="sxs-lookup"><span data-stu-id="73361-320">A release pipeline was created with the name *MyFirstProject-ASP.NET Core-CD*:</span></span>

![Capture d’écran montrant l’aperçu du pipeline de libération](media/cicd/release-definition-overview.png)

<span data-ttu-id="73361-322">Les deux principaux composants du pipeline de rejet sont les **artefacts** et les **environnements**.</span><span class="sxs-lookup"><span data-stu-id="73361-322">The two major components of the release pipeline are the **Artifacts** and the **Environments**.</span></span> <span data-ttu-id="73361-323">En cliquant sur la case dans la section **Artefacts** révèle le panneau suivant :</span><span class="sxs-lookup"><span data-stu-id="73361-323">Clicking the box in the **Artifacts** section reveals the following panel:</span></span>

![Capture d’écran montrant des artefacts de pipeline de libération](media/cicd/release-definition-artifacts.png)

<span data-ttu-id="73361-325">La valeur **source (définition de build)** représente la définition de construction à laquelle ce pipeline de version est lié.</span><span class="sxs-lookup"><span data-stu-id="73361-325">The **Source (Build definition)** value represents the build definition to which this release pipeline is linked.</span></span> <span data-ttu-id="73361-326">Le fichier *.zip* produit par une série réussie de la définition de build est fourni à l’environnement *de production* pour le déploiement à Azure.</span><span class="sxs-lookup"><span data-stu-id="73361-326">The *.zip* file produced by a successful run of the build definition is provided to the *Production* environment for deployment to Azure.</span></span> <span data-ttu-id="73361-327">Cliquez sur la *phase 1, 2 tâches* lien dans la boîte d’environnement *de production* pour afficher les tâches de pipeline de sortie:</span><span class="sxs-lookup"><span data-stu-id="73361-327">Click the *1 phase, 2 tasks* link in the *Production* environment box to view the release pipeline tasks:</span></span>

![Capture d’écran montrant les tâches de pipeline de libération](media/cicd/release-definition-tasks.png)

<span data-ttu-id="73361-329">Le gazoduc de sortie se compose de deux tâches : *déployer le service d’application Azure pour slot* et gérer le service *d’application Azure - Slot Swap*.</span><span class="sxs-lookup"><span data-stu-id="73361-329">The release pipeline consists of two tasks: *Deploy Azure App Service to Slot* and *Manage Azure App Service - Slot Swap*.</span></span> <span data-ttu-id="73361-330">En cliquant sur la première tâche, vous révèlez la configuration de tâche suivante :</span><span class="sxs-lookup"><span data-stu-id="73361-330">Clicking the first task reveals the following task configuration:</span></span>

![Capture d’écran montrant la tâche de déploiement du pipeline de dégagement](media/cicd/release-definition-task1.png)

<span data-ttu-id="73361-332">L’abonnement Azure, le type de service, le nom de l’application Web, le groupe de ressources et la fente de déploiement sont définis dans la tâche de déploiement.</span><span class="sxs-lookup"><span data-stu-id="73361-332">The Azure subscription, service type, web app name, resource group, and deployment slot are defined in the deployment task.</span></span> <span data-ttu-id="73361-333">Le **paquet ou boîte** à texte de dossier retient le chemin de fichier *.zip* à extraire et à déployer à la fente de *mise en scène* de l’application web \*mywebapp\<unique_number.\> \*</span><span class="sxs-lookup"><span data-stu-id="73361-333">The **Package or folder** textbox holds the *.zip* file path to be extracted and deployed to the *staging* slot of the *mywebapp\<unique_number\>* web app.</span></span>

<span data-ttu-id="73361-334">En cliquant sur la tâche d’échange de créneaux horaires, la configuration de tâche suivante :</span><span class="sxs-lookup"><span data-stu-id="73361-334">Clicking the slot swap task reveals the following task configuration:</span></span>

![Capture d’écran montrant la tâche d’échange de fente de pipeline de libération](media/cicd/release-definition-task2.png)

<span data-ttu-id="73361-336">Les détails de l’abonnement, du groupe de ressources, du type de service, du nom de l’application Web et des créneaux de déploiement sont fournis.</span><span class="sxs-lookup"><span data-stu-id="73361-336">The subscription, resource group, service type, web app name, and deployment slot details are provided.</span></span> <span data-ttu-id="73361-337">Le **Swap avec la** case à cocher de production est coché.</span><span class="sxs-lookup"><span data-stu-id="73361-337">The **Swap with Production** check box is checked.</span></span> <span data-ttu-id="73361-338">Par conséquent, les bits déployés à la fente *de mise en scène* sont échangés dans l’environnement de production.</span><span class="sxs-lookup"><span data-stu-id="73361-338">Consequently, the bits deployed to the *staging* slot are swapped into the production environment.</span></span>

## <a name="additional-reading"></a><span data-ttu-id="73361-339">Documentation supplémentaire</span><span class="sxs-lookup"><span data-stu-id="73361-339">Additional reading</span></span>

* [<span data-ttu-id="73361-340">Créer son premier pipeline avec Azure Pipelines</span><span class="sxs-lookup"><span data-stu-id="73361-340">Create your first pipeline with Azure Pipelines</span></span>](/azure/devops/pipelines/get-started-yaml)
* [<span data-ttu-id="73361-341">Construire et .NET Projet de base</span><span class="sxs-lookup"><span data-stu-id="73361-341">Build and .NET Core project</span></span>](/azure/devops/pipelines/languages/dotnet-core)
* [<span data-ttu-id="73361-342">Déployer une application web avec Azure Pipelines</span><span class="sxs-lookup"><span data-stu-id="73361-342">Deploy a web app with Azure Pipelines</span></span>](/azure/devops/pipelines/targets/webapp)
