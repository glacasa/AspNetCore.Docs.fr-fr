---
title: Déployer une application sur App Service - DevOps avec ASP.NET Core et Azure
author: CamSoper
description: Déployez une application ASP.NET Core à Azure App Service, première étape pour DevOps avec ASP.NET Core et Azure.
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 10/24/2018
uid: azure/devops/deploy-to-app-service
ms.openlocfilehash: df41f296e9c4e1eff6e31d45b29ec30ee1e20cf4
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78657744"
---
# <a name="deploy-an-app-to-app-service"></a><span data-ttu-id="6279b-103">Déployer une application sur App Service</span><span class="sxs-lookup"><span data-stu-id="6279b-103">Deploy an app to App Service</span></span>

<span data-ttu-id="6279b-104">[Azure App Service](/azure/app-service/) est la plate-forme d’hébergement Web d’Azure.</span><span class="sxs-lookup"><span data-stu-id="6279b-104">[Azure App Service](/azure/app-service/) is Azure's web hosting platform.</span></span> <span data-ttu-id="6279b-105">Le déploiement d’une application web sur Azure App Service peut se faire manuellement ou par un processus automatisé.</span><span class="sxs-lookup"><span data-stu-id="6279b-105">Deploying a web app to Azure App Service can be done manually or by an automated process.</span></span> <span data-ttu-id="6279b-106">Cette section du guide traite des méthodes de déploiement qui peuvent être déclenchées manuellement ou par script à l’aide de la ligne de commande, ou déclenchées manuellement à l’aide de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="6279b-106">This section of the guide discusses deployment methods that can be triggered manually or by script using the command line, or triggered manually using Visual Studio.</span></span>

<span data-ttu-id="6279b-107">Dans cette section, vous accomplirez les tâches suivantes :</span><span class="sxs-lookup"><span data-stu-id="6279b-107">In this section, you'll accomplish the following tasks:</span></span>

* <span data-ttu-id="6279b-108">Téléchargez et construisez l’application d’échantillon.</span><span class="sxs-lookup"><span data-stu-id="6279b-108">Download and build the sample app.</span></span>
* <span data-ttu-id="6279b-109">Créez une application Web Azure App Service à l’aide de la coquille Cloud Azure.</span><span class="sxs-lookup"><span data-stu-id="6279b-109">Create an Azure App Service Web App using the Azure Cloud Shell.</span></span>
* <span data-ttu-id="6279b-110">Déployez l’application d’échantillon à Azure à l’aide de Git.</span><span class="sxs-lookup"><span data-stu-id="6279b-110">Deploy the sample app to Azure using Git.</span></span>
* <span data-ttu-id="6279b-111">Déployez une modification de l’application à l’aide de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="6279b-111">Deploy a change to the app using Visual Studio.</span></span>
* <span data-ttu-id="6279b-112">Ajoutez une fente de mise en scène à l’application web.</span><span class="sxs-lookup"><span data-stu-id="6279b-112">Add a staging slot to the web app.</span></span>
* <span data-ttu-id="6279b-113">Déployez une mise à jour de la fente de mise en scène.</span><span class="sxs-lookup"><span data-stu-id="6279b-113">Deploy an update to the staging slot.</span></span>
* <span data-ttu-id="6279b-114">Procédez à l’échange des emplacements de préproduction et de production.</span><span class="sxs-lookup"><span data-stu-id="6279b-114">Swap the staging and production slots.</span></span>

## <a name="download-and-test-the-app"></a><span data-ttu-id="6279b-115">Télécharger et tester l’application</span><span class="sxs-lookup"><span data-stu-id="6279b-115">Download and test the app</span></span>

<span data-ttu-id="6279b-116">L’application utilisée dans ce guide est une application ASP.NET Core pré-construite, [Simple Feed Reader](https://github.com/Azure-Samples/simple-feed-reader/).</span><span class="sxs-lookup"><span data-stu-id="6279b-116">The app used in this guide is a pre-built ASP.NET Core app, [Simple Feed Reader](https://github.com/Azure-Samples/simple-feed-reader/).</span></span> <span data-ttu-id="6279b-117">Il s’agit d’une `Microsoft.SyndicationFeed.ReaderWriter` application Razor Pages qui utilise l’API pour récupérer un flux RSS/Atom et afficher les actualités d’une liste.</span><span class="sxs-lookup"><span data-stu-id="6279b-117">It's a Razor Pages app that uses the `Microsoft.SyndicationFeed.ReaderWriter` API to retrieve an RSS/Atom feed and display the news items in a list.</span></span>

<span data-ttu-id="6279b-118">N’hésitez pas à revoir le code, mais il est important de comprendre qu’il n’y a rien de spécial dans cette application.</span><span class="sxs-lookup"><span data-stu-id="6279b-118">Feel free to review the code, but it's important to understand that there's nothing special about this app.</span></span> <span data-ttu-id="6279b-119">C’est juste une application simple ASP.NET Core à des fins d’illustration.</span><span class="sxs-lookup"><span data-stu-id="6279b-119">It's just a simple ASP.NET Core app for illustrative purposes.</span></span>

<span data-ttu-id="6279b-120">À partir d’une coque de commande, téléchargez le code, construisez le projet et exécutez-le comme suit.</span><span class="sxs-lookup"><span data-stu-id="6279b-120">From a command shell, download the code, build the project, and run it as follows.</span></span>

> <span data-ttu-id="6279b-121">*Remarque : Les utilisateurs de Linux/macOS devraient apporter des modifications`/`appropriées pour les`\`chemins, par exemple, en utilisant la barre oblique avant () plutôt que la barre oblique arrière ().*</span><span class="sxs-lookup"><span data-stu-id="6279b-121">*Note: Linux/macOS users should make appropriate changes for paths, e.g., using forward slash (`/`) rather than back slash (`\`).*</span></span>

1. <span data-ttu-id="6279b-122">Clonez le code à un dossier sur votre machine locale.</span><span class="sxs-lookup"><span data-stu-id="6279b-122">Clone the code to a folder on your local machine.</span></span>

    ```console
    git clone https://github.com/Azure-Samples/simple-feed-reader/
    ```

2. <span data-ttu-id="6279b-123">Changez votre dossier de travail pour le dossier *simple-feed-reader* qui a été créé.</span><span class="sxs-lookup"><span data-stu-id="6279b-123">Change your working folder to the *simple-feed-reader* folder that was created.</span></span>

    ```console
    cd .\simple-feed-reader\SimpleFeedReader
    ```

3. <span data-ttu-id="6279b-124">Restaurer les paquets et construire la solution.</span><span class="sxs-lookup"><span data-stu-id="6279b-124">Restore the packages, and build the solution.</span></span>

    ```dotnetcli
    dotnet build
    ```

4. <span data-ttu-id="6279b-125">Exécutez l'application.</span><span class="sxs-lookup"><span data-stu-id="6279b-125">Run the app.</span></span>

    ```dotnetcli
    dotnet run
    ```

    ![La commande de course dotnet est réussie](./media/deploying-to-app-service/dotnet-run.png)

5. <span data-ttu-id="6279b-127">Ouvrez un navigateur et accédez à `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="6279b-127">Open a browser and navigate to `http://localhost:5000`.</span></span> <span data-ttu-id="6279b-128">L’application vous permet de taper ou coller une URL de flux de syndication et de consulter une liste d’actualités.</span><span class="sxs-lookup"><span data-stu-id="6279b-128">The app allows you to type or paste a syndication feed URL and view a list of news items.</span></span>

     ![L’application affichant le contenu d’un flux RSS](./media/deploying-to-app-service/app-in-browser.png)

6. <span data-ttu-id="6279b-130">Une fois que vous êtes satisfait de l’application fonctionne correctement, l’arrêter en appuyant sur **Ctrl**+**C** dans la coque de commande.</span><span class="sxs-lookup"><span data-stu-id="6279b-130">Once you're satisfied the app is working correctly, shut it down by pressing **Ctrl**+**C** in the command shell.</span></span>

## <a name="create-the-azure-app-service-web-app"></a><span data-ttu-id="6279b-131">Créez l’application Web Azure App</span><span class="sxs-lookup"><span data-stu-id="6279b-131">Create the Azure App Service Web App</span></span>

<span data-ttu-id="6279b-132">Pour déployer l’application, vous devrez créer une [application Web](/azure/app-service/app-service-web-overview)App Service .</span><span class="sxs-lookup"><span data-stu-id="6279b-132">To deploy the app, you'll need to create an App Service [Web App](/azure/app-service/app-service-web-overview).</span></span> <span data-ttu-id="6279b-133">Après la création de l’application Web, vous y serez déployé à partir de votre machine locale à l’aide de Git.</span><span class="sxs-lookup"><span data-stu-id="6279b-133">After creation of the Web App, you'll deploy to it from your local machine using Git.</span></span>

1. <span data-ttu-id="6279b-134">Connectez-vous à [Azure Cloud Shell](https://shell.azure.com/bash).</span><span class="sxs-lookup"><span data-stu-id="6279b-134">Sign in to the [Azure Cloud Shell](https://shell.azure.com/bash).</span></span> <span data-ttu-id="6279b-135">Remarque : Lorsque vous vous connectez pour la première fois, Cloud Shell invite à créer un compte de stockage pour les fichiers de configuration.</span><span class="sxs-lookup"><span data-stu-id="6279b-135">Note: When you sign in for the first time, Cloud Shell prompts to create a storage account for configuration files.</span></span> <span data-ttu-id="6279b-136">Acceptez les défauts ou fournissez un nom unique.</span><span class="sxs-lookup"><span data-stu-id="6279b-136">Accept the defaults or provide a unique name.</span></span>

2. <span data-ttu-id="6279b-137">Utilisez la coquille Cloud pour les étapes suivantes.</span><span class="sxs-lookup"><span data-stu-id="6279b-137">Use the Cloud Shell for the following steps.</span></span>

    <span data-ttu-id="6279b-138">a.</span><span class="sxs-lookup"><span data-stu-id="6279b-138">a.</span></span> <span data-ttu-id="6279b-139">Déclarez une variable pour stocker le nom de votre application web.</span><span class="sxs-lookup"><span data-stu-id="6279b-139">Declare a variable to store your web app's name.</span></span> <span data-ttu-id="6279b-140">Le nom doit être unique à utiliser dans l’URL par défaut.</span><span class="sxs-lookup"><span data-stu-id="6279b-140">The name must be unique to be used in the default URL.</span></span> <span data-ttu-id="6279b-141">L’utilisation de la `$RANDOM` fonction Bash pour construire `webappname99999`le nom garantit un caractère unique et aboutit au format .</span><span class="sxs-lookup"><span data-stu-id="6279b-141">Using the `$RANDOM` Bash function to construct the name guarantees uniqueness and results in the format `webappname99999`.</span></span>

    ```console
    webappname=mywebapp$RANDOM
    ```

    <span data-ttu-id="6279b-142">b.</span><span class="sxs-lookup"><span data-stu-id="6279b-142">b.</span></span> <span data-ttu-id="6279b-143">Créez un groupe de ressources.</span><span class="sxs-lookup"><span data-stu-id="6279b-143">Create a resource group.</span></span> <span data-ttu-id="6279b-144">Les groupes de ressources fournissent un moyen d’agréger les ressources Azure à gérer en tant que groupe.</span><span class="sxs-lookup"><span data-stu-id="6279b-144">Resource groups provide a means to aggregate Azure resources to be managed as a group.</span></span>

    ```azure-cli
    az group create --location centralus --name AzureTutorial
    ```

    <span data-ttu-id="6279b-145">La `az` commande invoque [l’Azure CLI](/cli/azure/).</span><span class="sxs-lookup"><span data-stu-id="6279b-145">The `az` command invokes the [Azure CLI](/cli/azure/).</span></span> <span data-ttu-id="6279b-146">Le CLI peut être exécuté localement, mais l’utiliser dans le Cloud Shell permet d’économiser du temps et de la configuration.</span><span class="sxs-lookup"><span data-stu-id="6279b-146">The CLI can be run locally, but using it in the Cloud Shell saves time and configuration.</span></span>

    <span data-ttu-id="6279b-147">c.</span><span class="sxs-lookup"><span data-stu-id="6279b-147">c.</span></span> <span data-ttu-id="6279b-148">Créez un plan app Service dans le niveau S1.</span><span class="sxs-lookup"><span data-stu-id="6279b-148">Create an App Service plan in the S1 tier.</span></span> <span data-ttu-id="6279b-149">Un plan App Service est un regroupement d’applications Web qui partagent le même niveau de prix.</span><span class="sxs-lookup"><span data-stu-id="6279b-149">An App Service plan is a grouping of web apps that share the same pricing tier.</span></span> <span data-ttu-id="6279b-150">Le niveau S1 n’est pas gratuit, mais il est nécessaire pour la fonction de fentes de mise en scène.</span><span class="sxs-lookup"><span data-stu-id="6279b-150">The S1 tier isn't free, but it's required for the staging slots feature.</span></span>

    ```azure-cli
    az appservice plan create --name $webappname --resource-group AzureTutorial --sku S1
    ```

    <span data-ttu-id="6279b-151">d.</span><span class="sxs-lookup"><span data-stu-id="6279b-151">d.</span></span> <span data-ttu-id="6279b-152">Créez la ressource web app à l’aide du plan App Service dans le même groupe de ressources.</span><span class="sxs-lookup"><span data-stu-id="6279b-152">Create the web app resource using the App Service plan in the same resource group.</span></span>

    ```azure-cli
    az webapp create --name $webappname --resource-group AzureTutorial --plan $webappname
    ```

    <span data-ttu-id="6279b-153">e.</span><span class="sxs-lookup"><span data-stu-id="6279b-153">e.</span></span> <span data-ttu-id="6279b-154">Définissez les informations d’identification de déploiement.</span><span class="sxs-lookup"><span data-stu-id="6279b-154">Set the deployment credentials.</span></span> <span data-ttu-id="6279b-155">Ces informations d’identification de déploiement s’appliquent à toutes les applications web de votre abonnement.</span><span class="sxs-lookup"><span data-stu-id="6279b-155">These deployment credentials apply to all the web apps in your subscription.</span></span> <span data-ttu-id="6279b-156">N’utilisez pas de caractères spéciaux dans le nom d’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="6279b-156">Don't use special characters in the user name.</span></span>

    ```azure-cli
    az webapp deployment user set --user-name REPLACE_WITH_USER_NAME --password REPLACE_WITH_PASSWORD
    ```

    <span data-ttu-id="6279b-157">f.</span><span class="sxs-lookup"><span data-stu-id="6279b-157">f.</span></span> <span data-ttu-id="6279b-158">Configurez l’application web pour accepter les déploiements de Git locaux et afficher l’URL de *déploiement Git*.</span><span class="sxs-lookup"><span data-stu-id="6279b-158">Configure the web app to accept deployments from local Git and display the *Git deployment URL*.</span></span> <span data-ttu-id="6279b-159">**Notez cette URL pour référence plus tard**.</span><span class="sxs-lookup"><span data-stu-id="6279b-159">**Note this URL for reference later**.</span></span>

    ```azure-cli
    echo Git deployment URL: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --query url --output tsv)
    ```

    <span data-ttu-id="6279b-160">g.</span><span class="sxs-lookup"><span data-stu-id="6279b-160">g.</span></span> <span data-ttu-id="6279b-161">Affichez *l’URL de l’application Web*.</span><span class="sxs-lookup"><span data-stu-id="6279b-161">Display the *web app URL*.</span></span> <span data-ttu-id="6279b-162">Naviguez vers cette URL pour voir l’application web vierge.</span><span class="sxs-lookup"><span data-stu-id="6279b-162">Browse to this URL to see the blank web app.</span></span> <span data-ttu-id="6279b-163">**Notez cette URL pour référence plus tard**.</span><span class="sxs-lookup"><span data-stu-id="6279b-163">**Note this URL for reference later**.</span></span>

    ```console
    echo Web app URL: http://$webappname.azurewebsites.net
    ```

3. <span data-ttu-id="6279b-164">À l’aide d’une coque de commande sur votre machine locale, naviguez vers le dossier de projet de l’application web (par exemple, `.\simple-feed-reader\SimpleFeedReader`).</span><span class="sxs-lookup"><span data-stu-id="6279b-164">Using a command shell on your local machine, navigate to the web app's project folder (for example, `.\simple-feed-reader\SimpleFeedReader`).</span></span> <span data-ttu-id="6279b-165">Exécutez les commandes suivantes pour configurer Git pour pousser à l’URL de déploiement :</span><span class="sxs-lookup"><span data-stu-id="6279b-165">Execute the following commands to set up Git to push to the deployment URL:</span></span>

    <span data-ttu-id="6279b-166">a.</span><span class="sxs-lookup"><span data-stu-id="6279b-166">a.</span></span> <span data-ttu-id="6279b-167">Ajoutez l’URL à distance au référentiel local.</span><span class="sxs-lookup"><span data-stu-id="6279b-167">Add the remote URL to the local repository.</span></span>

    ```console
    git remote add azure-prod GIT_DEPLOYMENT_URL
    ```

    <span data-ttu-id="6279b-168">b.</span><span class="sxs-lookup"><span data-stu-id="6279b-168">b.</span></span> <span data-ttu-id="6279b-169">Poussez la branche *principale* locale jusqu’à la branche *principale* de la télécommande *azure-prod.*</span><span class="sxs-lookup"><span data-stu-id="6279b-169">Push the local *master* branch to the *azure-prod* remote's *master* branch.</span></span>

    ```console
    git push azure-prod master
    ```

    <span data-ttu-id="6279b-170">Vous serez invité pour les informations de déploiement que vous avez créées plus tôt.</span><span class="sxs-lookup"><span data-stu-id="6279b-170">You'll be prompted for the deployment credentials you created earlier.</span></span> <span data-ttu-id="6279b-171">Observez la sortie dans la coque de commande.</span><span class="sxs-lookup"><span data-stu-id="6279b-171">Observe the output in the command shell.</span></span> <span data-ttu-id="6279b-172">Azure construit l’application ASP.NET Core à distance.</span><span class="sxs-lookup"><span data-stu-id="6279b-172">Azure builds the ASP.NET Core app remotely.</span></span>

4. <span data-ttu-id="6279b-173">Dans un navigateur, naviguez vers *l’URL de l’application Web* et notez que l’application a été construite et déployée.</span><span class="sxs-lookup"><span data-stu-id="6279b-173">In a browser, navigate to the *Web app URL* and note the app has been built and deployed.</span></span> <span data-ttu-id="6279b-174">D’autres modifications peuvent être apportées au `git commit`référentiel local Git avec .</span><span class="sxs-lookup"><span data-stu-id="6279b-174">Additional changes can be committed to the local Git repository with `git commit`.</span></span> <span data-ttu-id="6279b-175">Ces changements sont poussés à `git push` Azure avec la commande précédente.</span><span class="sxs-lookup"><span data-stu-id="6279b-175">These changes are pushed to Azure with the preceding `git push` command.</span></span>

## <a name="deployment-with-visual-studio"></a><span data-ttu-id="6279b-176">Déploiement avec Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6279b-176">Deployment with Visual Studio</span></span>

> <span data-ttu-id="6279b-177">*Remarque : Cette section ne s’applique qu’à Windows. Les utilisateurs de Linux et de macOS doivent faire la modification décrite à l’étape 2 ci-dessous. Enregistrer le fichier, et de commettre la `git commit`modification au référentiel local avec . Enfin, pousser le `git push`changement avec , comme dans la première section.*</span><span class="sxs-lookup"><span data-stu-id="6279b-177">*Note: This section applies to Windows only. Linux and macOS users should make the change described in step 2 below. Save the file, and commit the change to the local repository with `git commit`. Finally, push the change with `git push`, as in the first section.*</span></span>

<span data-ttu-id="6279b-178">L’application a déjà été déployée à partir de la coque de commande.</span><span class="sxs-lookup"><span data-stu-id="6279b-178">The app has already been deployed from the command shell.</span></span> <span data-ttu-id="6279b-179">Utilisons les outils intégrés de Visual Studio pour déployer une mise à jour de l’application.</span><span class="sxs-lookup"><span data-stu-id="6279b-179">Let's use Visual Studio's integrated tools to deploy an update to the app.</span></span> <span data-ttu-id="6279b-180">Dans les coulisses, Visual Studio accomplit la même chose que l’outillage de la ligne de commande, mais dans l’interface utilisateur familière de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="6279b-180">Behind the scenes, Visual Studio accomplishes the same thing as the command line tooling, but within Visual Studio's familiar UI.</span></span>

1. <span data-ttu-id="6279b-181">Ouvrez *SimpleFeedReader.sln* dans Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="6279b-181">Open *SimpleFeedReader.sln* in Visual Studio.</span></span>
2. <span data-ttu-id="6279b-182">Dans Solution Explorer, ouvrez *Pages-Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="6279b-182">In Solution Explorer, open *Pages\Index.cshtml*.</span></span> <span data-ttu-id="6279b-183">Remplacez `<h2>Simple Feed Reader</h2>` par `<h2>Simple Feed Reader - V2</h2>`.</span><span class="sxs-lookup"><span data-stu-id="6279b-183">Change `<h2>Simple Feed Reader</h2>` to `<h2>Simple Feed Reader - V2</h2>`.</span></span>
3. <span data-ttu-id="6279b-184">Appuyez sur **Ctrl**+**Shift**+**B** pour construire l’application.</span><span class="sxs-lookup"><span data-stu-id="6279b-184">Press **Ctrl**+**Shift**+**B** to build the app.</span></span>
4. <span data-ttu-id="6279b-185">Dans Solution Explorer, cliquez à droite sur le projet et cliquez sur **Publier**.</span><span class="sxs-lookup"><span data-stu-id="6279b-185">In Solution Explorer, right-click on the project and click **Publish**.</span></span>

    ![Capture d’écran montrant Right-click, Publier](./media/deploying-to-app-service/publish.png)
5. <span data-ttu-id="6279b-187">Visual Studio peut créer une nouvelle ressource App Service, mais cette mise à jour sera publiée sur le déploiement existant.</span><span class="sxs-lookup"><span data-stu-id="6279b-187">Visual Studio can create a new App Service resource, but this update will be published over the existing deployment.</span></span> <span data-ttu-id="6279b-188">Dans le Pick, un dialogue **cible de publication,** sélectionnez le service **d’application** de la liste sur la gauche, puis **sélectionnez Select Existing**.</span><span class="sxs-lookup"><span data-stu-id="6279b-188">In the **Pick a publish target** dialog, select **App Service** from the list on the left, and then select **Select Existing**.</span></span> <span data-ttu-id="6279b-189">Cliquez sur **Publier**.</span><span class="sxs-lookup"><span data-stu-id="6279b-189">Click **Publish**.</span></span>
6. <span data-ttu-id="6279b-190">Dans le dialogue **App Service,** confirmez que le compte Microsoft ou Organisation utilisé pour créer votre abonnement Azure s’affiche en haut à droite.</span><span class="sxs-lookup"><span data-stu-id="6279b-190">In the **App Service** dialog, confirm that the Microsoft or Organizational account used to create your Azure subscription is displayed in the upper right.</span></span> <span data-ttu-id="6279b-191">Si ce n’est pas le cas, cliquez sur le drop-down et ajoutez-le.</span><span class="sxs-lookup"><span data-stu-id="6279b-191">If it's not, click the drop-down and add it.</span></span>
7. <span data-ttu-id="6279b-192">Confirmez que **l’abonnement** Azure correct est sélectionné.</span><span class="sxs-lookup"><span data-stu-id="6279b-192">Confirm that the correct Azure **Subscription** is selected.</span></span> <span data-ttu-id="6279b-193">Pour **voir**, sélectionnez **Resource Group**.</span><span class="sxs-lookup"><span data-stu-id="6279b-193">For **View**, select **Resource Group**.</span></span> <span data-ttu-id="6279b-194">Élargissez le groupe de ressources **AzureTutorial** et sélectionnez ensuite l’application web existante.</span><span class="sxs-lookup"><span data-stu-id="6279b-194">Expand the **AzureTutorial** resource group and then select the existing web app.</span></span> <span data-ttu-id="6279b-195">Cliquez sur **OK**.</span><span class="sxs-lookup"><span data-stu-id="6279b-195">Click **OK**.</span></span>

    ![Capture d’écran montrant le dialogue de Publish App Service](./media/deploying-to-app-service/publish-dialog.png)

<span data-ttu-id="6279b-197">Visual Studio construit et déploie l’application à Azure.</span><span class="sxs-lookup"><span data-stu-id="6279b-197">Visual Studio builds and deploys the app to Azure.</span></span> <span data-ttu-id="6279b-198">Naviguez vers l’URL de l’application Web.</span><span class="sxs-lookup"><span data-stu-id="6279b-198">Browse to the web app URL.</span></span> <span data-ttu-id="6279b-199">Validez `<h2>` que la modification de l’élément est en direct.</span><span class="sxs-lookup"><span data-stu-id="6279b-199">Validate that the `<h2>` element modification is live.</span></span>

![L’application avec le titre modifié](./media/deploying-to-app-service/app-v2.png)

## <a name="deployment-slots"></a><span data-ttu-id="6279b-201">Emplacements de déploiement</span><span class="sxs-lookup"><span data-stu-id="6279b-201">Deployment slots</span></span>

<span data-ttu-id="6279b-202">Les emplacements de déploiement prennent en charge la mise en scène des modifications sans avoir d’impact sur l’application en cours d’exécution en production.</span><span class="sxs-lookup"><span data-stu-id="6279b-202">Deployment slots support the staging of changes without impacting the app running in production.</span></span> <span data-ttu-id="6279b-203">Une fois que la version mise en scène de l’application est validée par une équipe d’assurance de la qualité, les créneaux de production et de mise en scène peuvent être échangés.</span><span class="sxs-lookup"><span data-stu-id="6279b-203">Once the staged version of the app is validated by a quality assurance team, the production and staging slots can be swapped.</span></span> <span data-ttu-id="6279b-204">L’application en mise en scène est promue à la production de cette manière.</span><span class="sxs-lookup"><span data-stu-id="6279b-204">The app in staging is promoted to production in this manner.</span></span> <span data-ttu-id="6279b-205">Les étapes suivantes créent une fente de mise en scène, en déployant quelques modifications et échangent la fente de mise en scène avec la production après vérification.</span><span class="sxs-lookup"><span data-stu-id="6279b-205">The following steps create a staging slot, deploy some changes to it, and swap the staging slot with production after verification.</span></span>

1. <span data-ttu-id="6279b-206">Connectez-vous à [l’Azure Cloud Shell](https://shell.azure.com/bash), si ce n’est déjà connecté.</span><span class="sxs-lookup"><span data-stu-id="6279b-206">Sign in to the [Azure Cloud Shell](https://shell.azure.com/bash), if not already signed in.</span></span>
2. <span data-ttu-id="6279b-207">Créez la fente de mise en scène.</span><span class="sxs-lookup"><span data-stu-id="6279b-207">Create the staging slot.</span></span>

    <span data-ttu-id="6279b-208">a.</span><span class="sxs-lookup"><span data-stu-id="6279b-208">a.</span></span> <span data-ttu-id="6279b-209">Créez une fente de déploiement avec la *mise en scène*du nom .</span><span class="sxs-lookup"><span data-stu-id="6279b-209">Create a deployment slot with the name *staging*.</span></span>

    ```azure-cli
    az webapp deployment slot create --name $webappname --resource-group AzureTutorial --slot staging
    ```

    <span data-ttu-id="6279b-210">b.</span><span class="sxs-lookup"><span data-stu-id="6279b-210">b.</span></span> <span data-ttu-id="6279b-211">Configurez la fente de mise en scène pour utiliser le déploiement à partir de Git local et obtenir l’URL de déploiement **de mise en scène.**</span><span class="sxs-lookup"><span data-stu-id="6279b-211">Configure the staging slot to use deployment from local Git and get the **staging** deployment URL.</span></span> <span data-ttu-id="6279b-212">**Notez cette URL pour référence plus tard**.</span><span class="sxs-lookup"><span data-stu-id="6279b-212">**Note this URL for reference later**.</span></span>

    ```azure-cli
    echo Git deployment URL for staging: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --slot staging --query url --output tsv)
    ```

    <span data-ttu-id="6279b-213">c.</span><span class="sxs-lookup"><span data-stu-id="6279b-213">c.</span></span> <span data-ttu-id="6279b-214">Affichez l’URL de la fente de mise en scène.</span><span class="sxs-lookup"><span data-stu-id="6279b-214">Display the staging slot's URL.</span></span> <span data-ttu-id="6279b-215">Naviguez vers l’URL pour voir la fente de mise en scène vide.</span><span class="sxs-lookup"><span data-stu-id="6279b-215">Browse to the URL to see the empty staging slot.</span></span> <span data-ttu-id="6279b-216">**Notez cette URL pour référence plus tard**.</span><span class="sxs-lookup"><span data-stu-id="6279b-216">**Note this URL for reference later**.</span></span>

    ```console
    echo Staging web app URL: http://$webappname-staging.azurewebsites.net
    ```

3. <span data-ttu-id="6279b-217">Dans un éditeur de texte ou Visual Studio, modifiez à `<h2>` nouveau `<h2>Simple Feed Reader - V3</h2>` *Pages/Index.cshtml* afin que l’élément se lit et enregistre le fichier.</span><span class="sxs-lookup"><span data-stu-id="6279b-217">In a text editor or Visual Studio, modify *Pages/Index.cshtml* again so that the `<h2>` element reads `<h2>Simple Feed Reader - V3</h2>` and save the file.</span></span>

4. <span data-ttu-id="6279b-218">Engagez le fichier sur le référentiel local Git, en utilisant soit la page **Modifications** dans l’onglet *Team Explorer* de Visual Studio, soit en entrant le suivant à l’aide de la coque de commande de la machine locale :</span><span class="sxs-lookup"><span data-stu-id="6279b-218">Commit the file to the local Git repository, using either the **Changes** page in Visual Studio's *Team Explorer* tab, or by entering the following using the local machine's command shell:</span></span>

    ```console
    git commit -a -m "upgraded to V3"
    ```

5. <span data-ttu-id="6279b-219">À l’aide de la coque de commande de la machine locale, ajoutez l’URL de déploiement de mise en scène comme télécommande Git et poussez les modifications validées :</span><span class="sxs-lookup"><span data-stu-id="6279b-219">Using the local machine's command shell, add the staging deployment URL as a Git remote and push the committed changes:</span></span>

    <span data-ttu-id="6279b-220">a.</span><span class="sxs-lookup"><span data-stu-id="6279b-220">a.</span></span> <span data-ttu-id="6279b-221">Ajoutez l’URL à distance pour la mise en scène du référentiel local Git.</span><span class="sxs-lookup"><span data-stu-id="6279b-221">Add the remote URL for staging to the local Git repository.</span></span>

    ```console
    git remote add azure-staging <Git_staging_deployment_URL>
    ```

    <span data-ttu-id="6279b-222">b.</span><span class="sxs-lookup"><span data-stu-id="6279b-222">b.</span></span> <span data-ttu-id="6279b-223">Poussez la branche *principale* locale jusqu’à la branche *principale* de la télécommande *azur-mise en scène.*</span><span class="sxs-lookup"><span data-stu-id="6279b-223">Push the local *master* branch to the *azure-staging* remote's *master* branch.</span></span>

    ```console
    git push azure-staging master
    ```

    <span data-ttu-id="6279b-224">Attendez pendant qu’Azure construit et déploie l’application.</span><span class="sxs-lookup"><span data-stu-id="6279b-224">Wait while Azure builds and deploys the app.</span></span>

6. <span data-ttu-id="6279b-225">Pour vérifier que V3 a été déployé sur la fente de mise en scène, ouvrez deux fenêtres de navigateur.</span><span class="sxs-lookup"><span data-stu-id="6279b-225">To verify that V3 has been deployed to the staging slot, open two browser windows.</span></span> <span data-ttu-id="6279b-226">Dans une fenêtre, naviguez vers l’URL originale de l’application Web.</span><span class="sxs-lookup"><span data-stu-id="6279b-226">In one window, navigate to the original web app URL.</span></span> <span data-ttu-id="6279b-227">Dans l’autre fenêtre, naviguez vers l’URL de l’application web de mise en scène.</span><span class="sxs-lookup"><span data-stu-id="6279b-227">In the other window, navigate to the staging web app URL.</span></span> <span data-ttu-id="6279b-228">L’URL de production sert V2 de l’application.</span><span class="sxs-lookup"><span data-stu-id="6279b-228">The production URL serves V2 of the app.</span></span> <span data-ttu-id="6279b-229">L’URL de mise en scène sert V3 de l’application.</span><span class="sxs-lookup"><span data-stu-id="6279b-229">The staging URL serves V3 of the app.</span></span>

    ![Capture d’écran comparant les fenêtres du navigateur](./media/deploying-to-app-service/ready-to-swap.png)

7. <span data-ttu-id="6279b-231">Dans le Cloud Shell, entrez la fente de mise en scène vérifiée/réchauffée en production.</span><span class="sxs-lookup"><span data-stu-id="6279b-231">In the Cloud Shell, swap the verified/warmed-up staging slot into production.</span></span>

    ```azure-cli
    az webapp deployment slot swap --name $webappname --resource-group AzureTutorial --slot staging
    ```

8. <span data-ttu-id="6279b-232">Vérifiez que l’échange s’est produit en rafraîchissant les deux fenêtres du navigateur.</span><span class="sxs-lookup"><span data-stu-id="6279b-232">Verify that the swap occurred by refreshing the two browser windows.</span></span>

    ![Comparaison des fenêtres du navigateur après l’échange](./media/deploying-to-app-service/swapped.png)

## <a name="summary"></a><span data-ttu-id="6279b-234">Résumé</span><span class="sxs-lookup"><span data-stu-id="6279b-234">Summary</span></span>

<span data-ttu-id="6279b-235">Dans cette section, les tâches suivantes ont été accomplies :</span><span class="sxs-lookup"><span data-stu-id="6279b-235">In this section, the following tasks were completed:</span></span>

* <span data-ttu-id="6279b-236">Téléchargé et construit l’application d’échantillon.</span><span class="sxs-lookup"><span data-stu-id="6279b-236">Downloaded and built the sample app.</span></span>
* <span data-ttu-id="6279b-237">Création d’une application Web Azure App Service à l’aide de la coquille Cloud Azure.</span><span class="sxs-lookup"><span data-stu-id="6279b-237">Created an Azure App Service Web App using the Azure Cloud Shell.</span></span>
* <span data-ttu-id="6279b-238">Déploiement de l’application d’échantillon à Azure à l’aide de Git.</span><span class="sxs-lookup"><span data-stu-id="6279b-238">Deployed the sample app to Azure using Git.</span></span>
* <span data-ttu-id="6279b-239">A déployé une modification de l’application à l’aide de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="6279b-239">Deployed a change to the app using Visual Studio.</span></span>
* <span data-ttu-id="6279b-240">Ajout d’une fente de mise en scène à l’application web.</span><span class="sxs-lookup"><span data-stu-id="6279b-240">Added a staging slot to the web app.</span></span>
* <span data-ttu-id="6279b-241">Déploiement d’une mise à jour de la fente de mise en scène.</span><span class="sxs-lookup"><span data-stu-id="6279b-241">Deployed an update to the staging slot.</span></span>
* <span data-ttu-id="6279b-242">Échangé les fentes de mise en scène et de production.</span><span class="sxs-lookup"><span data-stu-id="6279b-242">Swapped the staging and production slots.</span></span>

<span data-ttu-id="6279b-243">Dans la section suivante, vous apprendrez à construire un pipeline DevOps avec Azure Pipelines.</span><span class="sxs-lookup"><span data-stu-id="6279b-243">In the next section, you'll learn how to build a DevOps pipeline with Azure Pipelines.</span></span>

## <a name="additional-reading"></a><span data-ttu-id="6279b-244">Documentation supplémentaire</span><span class="sxs-lookup"><span data-stu-id="6279b-244">Additional reading</span></span>

* [<span data-ttu-id="6279b-245">Vue d'ensemble de Web Apps</span><span class="sxs-lookup"><span data-stu-id="6279b-245">Web Apps overview</span></span>](/azure/app-service/app-service-web-overview)
* [<span data-ttu-id="6279b-246">Construire une application web .NET Core et SQL Database dans Azure App Service</span><span class="sxs-lookup"><span data-stu-id="6279b-246">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* [<span data-ttu-id="6279b-247">Configurer les informations d’identification de déploiement pour Azure App Service</span><span class="sxs-lookup"><span data-stu-id="6279b-247">Configure deployment credentials for Azure App Service</span></span>](/azure/app-service/app-service-deployment-credentials)
* [<span data-ttu-id="6279b-248">Configurer des environnements intermédiaires dans Azure App Service</span><span class="sxs-lookup"><span data-stu-id="6279b-248">Set up staging environments in Azure App Service</span></span>](/azure/app-service/web-sites-staged-publishing)
