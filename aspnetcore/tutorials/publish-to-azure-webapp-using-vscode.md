---
title: Publier une application ASP.NET Core sur Azure avec Visual Studio Code
author: ricardoserradas
description: Découvrez comment publier une application ASP.NET Core sur Azure App Service à l’aide de Visual Studio Code.
ms.author: ricardoserradas
ms.custom: mvc
ms.date: 04/16/2019
uid: tutorials/publish-to-azure-webapp-using-vscode
ms.openlocfilehash: 64d82835f6a47a458802692c99658b964c07f807
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64889604"
---
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio-code"></a><span data-ttu-id="197eb-103">Publier une application ASP.NET Core sur Azure avec Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="197eb-103">Publish an ASP.NET Core app to Azure with Visual Studio Code</span></span>

<span data-ttu-id="197eb-104">Par [Ricardo Serradas](https://twitter.com/ricardoserradas)</span><span class="sxs-lookup"><span data-stu-id="197eb-104">By [Ricardo Serradas](https://twitter.com/ricardoserradas)</span></span>

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

<span data-ttu-id="197eb-105">Pour résoudre un problème de déploiement App Service, consultez <xref:host-and-deploy/azure-apps/troubleshoot>.</span><span class="sxs-lookup"><span data-stu-id="197eb-105">To troubleshoot an App Service deployment issue, see <xref:host-and-deploy/azure-apps/troubleshoot>.</span></span>

## <a name="intro"></a><span data-ttu-id="197eb-106">Introduction</span><span class="sxs-lookup"><span data-stu-id="197eb-106">Intro</span></span>

<span data-ttu-id="197eb-107">Dans ce tutoriel, vous allez apprendre à créer une application ASP.Net Core MVC et à la déployer dans Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="197eb-107">With this tutorial, you'll learn how to create an ASP.Net Core MVC Application and deploy it within Visual Studio Code.</span></span>

## <a name="set-up"></a><span data-ttu-id="197eb-108">Installer</span><span class="sxs-lookup"><span data-stu-id="197eb-108">Set up</span></span>

- <span data-ttu-id="197eb-109">Ouvrez un [compte Azure gratuit](https://azure.microsoft.com/free/dotnet/) si vous n’en avez pas.</span><span class="sxs-lookup"><span data-stu-id="197eb-109">Open a [free Azure account](https://azure.microsoft.com/free/dotnet/) if you don't have one.</span></span>
- <span data-ttu-id="197eb-110">Installez le [SDK .NET Core](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="197eb-110">Install [.NET Core SDK](https://dotnet.microsoft.com/download)</span></span>
- <span data-ttu-id="197eb-111">Installez [Visual Studio Code](https://code.visualstudio.com/Download)</span><span class="sxs-lookup"><span data-stu-id="197eb-111">Install [Visual Studio Code](https://code.visualstudio.com/Download)</span></span>
  - <span data-ttu-id="197eb-112">Installez l’[extension C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) dans Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="197eb-112">Install the [C# Extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) to Visual Studio Code</span></span>
  - <span data-ttu-id="197eb-113">Installez l’[extension Azure App Service](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) dans Visual Studio Code et configurez-la avant de poursuivre.</span><span class="sxs-lookup"><span data-stu-id="197eb-113">Instal the [Azure App Service Extension](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) to Visual Studio Code and configure it before proceeding</span></span>

## <a name="create-an-aspnet-core-mvc-project"></a><span data-ttu-id="197eb-114">Créer un projet ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="197eb-114">Create an ASP.Net Core MVC project</span></span>

<span data-ttu-id="197eb-115">À l’aide d’un terminal, accédez au dossier dans lequel vous souhaitez créer le projet, puis exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="197eb-115">Using a terminal, navigate to the folder you want the project to be created on and use the following command:</span></span>

```cmd
> dotnet new mvc
```

<span data-ttu-id="197eb-116">Vous aurez une structure de dossier similaire à celle-ci :</span><span class="sxs-lookup"><span data-stu-id="197eb-116">You'll have a folder structure similar to the following:</span></span>

```cmd
      appsettings.Development.json
      appsettings.json
<DIR> Controllers
<DIR> Models
      netcore-vscode.csproj
<DIR> obj
      Program.cs
<DIR> Properties
      Startup.cs
<DIR> Views
<DIR> wwwroot
```

## <a name="open-it-with-visual-studio-code"></a><span data-ttu-id="197eb-117">Ouvrir le projet avec Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="197eb-117">Open it with Visual Studio Code</span></span>

<span data-ttu-id="197eb-118">Une fois le projet créé, vous pouvez l’ouvrir avec Visual Studio Code en utilisant l’une des options ci-dessous :</span><span class="sxs-lookup"><span data-stu-id="197eb-118">After your project is created, you can open it with Visual Studio Code by using one of the options below:</span></span>

### <a name="through-the-command-line"></a><span data-ttu-id="197eb-119">Via la ligne de commande</span><span class="sxs-lookup"><span data-stu-id="197eb-119">Through the command line</span></span>

<span data-ttu-id="197eb-120">Exécutez la commande suivante dans le dossier où vous avez créé le projet :</span><span class="sxs-lookup"><span data-stu-id="197eb-120">Use the following command within the folder you created the project:</span></span>

```cmd
> code .
```

<span data-ttu-id="197eb-121">Si la commande ci-dessous ne fonctionne pas, vérifiez que votre installation est configurée correctement en référençant [ce lien](https://code.visualstudio.com/docs/setup/setup-overview#_cross-platform).</span><span class="sxs-lookup"><span data-stu-id="197eb-121">If the command below does not work, check if your installation is configured properly by referencing [this link](https://code.visualstudio.com/docs/setup/setup-overview#_cross-platform).</span></span>

### <a name="through-visual-studio-code-interface"></a><span data-ttu-id="197eb-122">Via l’interface Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="197eb-122">Through Visual Studio Code interface</span></span>

- <span data-ttu-id="197eb-123">Ouvrez Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="197eb-123">Open Visual Studio Code</span></span>
- <span data-ttu-id="197eb-124">Dans le menu, sélectionnez `File > Open Folder`.</span><span class="sxs-lookup"><span data-stu-id="197eb-124">On the menu, select `File > Open Folder`</span></span>
- <span data-ttu-id="197eb-125">Sélectionnez la racine du dossier dans lequel vous avez créé le projet MVC.</span><span class="sxs-lookup"><span data-stu-id="197eb-125">Select the root of the folder you created the MVC Project</span></span>

<span data-ttu-id="197eb-126">Lorsque vous ouvrez le dossier du projet, un message s’affiche, indiquant que les composants nécessaires à la génération et au débogage sont manquants.</span><span class="sxs-lookup"><span data-stu-id="197eb-126">When you open the project folder, you'll receive a message saying that required assets to build and debug are missing.</span></span> <span data-ttu-id="197eb-127">Acceptez l’aide qui vous est fournie pour les ajouter.</span><span class="sxs-lookup"><span data-stu-id="197eb-127">Accept the help to add them.</span></span>

![Interface Visual Studio Code avec le projet chargé](publish-to-azure-webapp-using-vscode/_static/folder-structure-restore-netcore.jpg)

<span data-ttu-id="197eb-129">Un dossier `.vscode` sera créé sous la structure de projets.</span><span class="sxs-lookup"><span data-stu-id="197eb-129">A `.vscode` folder will be created under the project structure.</span></span> <span data-ttu-id="197eb-130">Il contiendra les fichiers suivants :</span><span class="sxs-lookup"><span data-stu-id="197eb-130">It will contain the following files:</span></span>

```cmd
launch.json
tasks.json
```

<span data-ttu-id="197eb-131">Il s’agit des fichiers d’utilitaire qui vous aident à générer et à déboguer votre application web .NET Core.</span><span class="sxs-lookup"><span data-stu-id="197eb-131">These are utility files to help you build and debug your .NET Core Web App.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="197eb-132">Exécuter l'application</span><span class="sxs-lookup"><span data-stu-id="197eb-132">Run the app</span></span>

<span data-ttu-id="197eb-133">Avant de déployer l’application sur Azure, vérifiez qu’elle s’exécute correctement sur votre ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="197eb-133">Before we deploy the app to Azure, make sure it is running properly on your local machine.</span></span>

- <span data-ttu-id="197eb-134">Appuyez sur F5 pour exécuter le projet.</span><span class="sxs-lookup"><span data-stu-id="197eb-134">Press F5 to run the project</span></span>

<span data-ttu-id="197eb-135">Votre application web commence à s’exécuter sous un nouvel onglet de votre navigateur par défaut.</span><span class="sxs-lookup"><span data-stu-id="197eb-135">Your web app will start running on a new tab of your default browser.</span></span> <span data-ttu-id="197eb-136">Dès le démarrage de l’application, un avertissement lié à la confidentialité peut s’afficher.</span><span class="sxs-lookup"><span data-stu-id="197eb-136">You may notice a privacy warning as soon as it starts.</span></span> <span data-ttu-id="197eb-137">Cela est dû au fait que votre application démarre soit avec HTTP, soit avec HTTPS, et qu’elle navigue vers le point de terminaison HTTPS par défaut.</span><span class="sxs-lookup"><span data-stu-id="197eb-137">This is because your app will start either using HTTP and HTTPS, and it navigates to the HTTPS endpoint by default.</span></span>

![Avertissement de confidentialité pendant le débogage local de l’application](publish-to-azure-webapp-using-vscode/_static/run-webapp-https-warning.jpg)

<span data-ttu-id="197eb-139">Pour conserver la session de débogage, cliquez sur `Advanced`, puis sur `Continue to localhost (unsafe)`.</span><span class="sxs-lookup"><span data-stu-id="197eb-139">To keep the debugging session, click `Advanced` and then `Continue to localhost (unsafe)`.</span></span>

## <a name="generate-the-deployment-package-locally"></a><span data-ttu-id="197eb-140">Générer le package de déploiement localement</span><span class="sxs-lookup"><span data-stu-id="197eb-140">Generate the deployment package locally</span></span>

- <span data-ttu-id="197eb-141">Ouvrez le terminal Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="197eb-141">Open Visual Studio Code terminal</span></span>
- <span data-ttu-id="197eb-142">Utilisez la commande suivante pour générer un package `Release` dans un sous-dossier appelé `publish` :</span><span class="sxs-lookup"><span data-stu-id="197eb-142">Use the following command to generate a `Release` package to a sub folder called `publish`:</span></span>
  - `dotnet publish -c Release -o ./publish`
- <span data-ttu-id="197eb-143">Un dossier `publish` sera créé sous la structure de projets.</span><span class="sxs-lookup"><span data-stu-id="197eb-143">A new `publish` folder will be created under the project structure</span></span>

![Publier la structure du dossier de publication](publish-to-azure-webapp-using-vscode/_static/publish-folder.jpg)

## <a name="publish-to-azure-app-service"></a><span data-ttu-id="197eb-145">Publier sur Azure App Service</span><span class="sxs-lookup"><span data-stu-id="197eb-145">Publish to Azure App Service</span></span>

<span data-ttu-id="197eb-146">Grâce à l’extension Azure App Service pour Visual Studio Code, suivez les étapes ci-dessous pour publier le site web directement sur Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="197eb-146">Leveraging the Azure App Service extension for Visual Studio Code, follow the steps below to publish the website directly to the Azure App Service.</span></span>

### <a name="if-youre-creating-a-new-web-app"></a><span data-ttu-id="197eb-147">Si vous créez une nouvelle application web</span><span class="sxs-lookup"><span data-stu-id="197eb-147">If you're creating a new Web App</span></span>

- <span data-ttu-id="197eb-148">Cliquez avec le bouton droit de la souris sur le dossier `publish`, puis sélectionnez `Deploy to Web App...`.</span><span class="sxs-lookup"><span data-stu-id="197eb-148">Right click the `publish` folder and select `Deploy to Web App...`</span></span>
- <span data-ttu-id="197eb-149">Sélectionnez l’abonnement dans lequel vous souhaitez créer l’application web.</span><span class="sxs-lookup"><span data-stu-id="197eb-149">Select the subscription you want to create the Web App</span></span>
- <span data-ttu-id="197eb-150">Sélectionnez `Create New Web App`.</span><span class="sxs-lookup"><span data-stu-id="197eb-150">Select `Create New Web App`</span></span>
- <span data-ttu-id="197eb-151">Entrez un nom pour l’application web.</span><span class="sxs-lookup"><span data-stu-id="197eb-151">Enter a name for the Web App</span></span>

<span data-ttu-id="197eb-152">L’extension crée la nouvelle application web et démarre automatiquement le déploiement du package dans l’application.</span><span class="sxs-lookup"><span data-stu-id="197eb-152">The extension will create the new Web App and will automatically start deploying the package to it.</span></span> <span data-ttu-id="197eb-153">Une fois le déploiement terminé, cliquez sur `Browse Website` pour valider le déploiement.</span><span class="sxs-lookup"><span data-stu-id="197eb-153">Once the deployment is finished, click `Browse Website` to validate the deployment.</span></span>

![Message de déploiement réussi](publish-to-azure-webapp-using-vscode/_static/deployment-succeeded-message.jpg)

<span data-ttu-id="197eb-155">Une fois que vous cliquez sur `Browse Website`, vous y reviendrez à l’aide de votre navigateur par défaut :</span><span class="sxs-lookup"><span data-stu-id="197eb-155">Once you click `Browse Website`, you'll navigate to it using your default browser:</span></span>

![Déploiement réussi de l’application web](publish-to-azure-webapp-using-vscode/_static/new-webapp-deployed.jpg)

### <a name="if-youre-deploying-to-an-existing-web-app"></a><span data-ttu-id="197eb-157">Si vous effectuez un déploiement dans une application web existante</span><span class="sxs-lookup"><span data-stu-id="197eb-157">If you're deploying to an existing Web App</span></span>

- <span data-ttu-id="197eb-158">Cliquez avec le bouton droit de la souris sur le dossier `publish`, puis sélectionnez `Deploy to Web App...`.</span><span class="sxs-lookup"><span data-stu-id="197eb-158">Right click the `publish` folder and select `Deploy to Web App...`</span></span>
- <span data-ttu-id="197eb-159">Sélectionnez l’abonnement où se trouve l’application web existante.</span><span class="sxs-lookup"><span data-stu-id="197eb-159">Select the subscription the existing Web App resides</span></span>
- <span data-ttu-id="197eb-160">Sélectionnez l’application web dans la liste.</span><span class="sxs-lookup"><span data-stu-id="197eb-160">Select the Web App from the list</span></span>
- <span data-ttu-id="197eb-161">Visual Studio Code vous demande si vous souhaitez remplacer le contenu existant.</span><span class="sxs-lookup"><span data-stu-id="197eb-161">Visual Studio Code will ask you if you want to overwrite the existing content.</span></span> <span data-ttu-id="197eb-162">Cliquez sur `Deploy` pour confirmer.</span><span class="sxs-lookup"><span data-stu-id="197eb-162">Click `Deploy` to confirm</span></span>

<span data-ttu-id="197eb-163">L’extension déploiera le contenu mis à jour dans l’application web.</span><span class="sxs-lookup"><span data-stu-id="197eb-163">The extension will deploy the updated content to the Web App.</span></span> <span data-ttu-id="197eb-164">Une fois le déploiement terminé, cliquez sur `Browse Website` pour valider le déploiement.</span><span class="sxs-lookup"><span data-stu-id="197eb-164">Once it's done, click `Browse Website` to validate the deployment.</span></span>

![Déploiement réussi de l’application web existante](publish-to-azure-webapp-using-vscode/_static/existing-webapp-deployed.jpg)

## <a name="next-steps"></a><span data-ttu-id="197eb-166">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="197eb-166">Next steps</span></span>

- [<span data-ttu-id="197eb-167">Créer votre premier pipeline Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="197eb-167">Create your first Azure DevOps pipeline</span></span>](/azure/devops/pipelines/create-first-pipeline)

## <a name="additional-resources"></a><span data-ttu-id="197eb-168">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="197eb-168">Additional resources</span></span>

- [<span data-ttu-id="197eb-169">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="197eb-169">Azure App Service</span></span>](/azure/app-service/app-service-web-overview)
- [<span data-ttu-id="197eb-170">Groupes de ressources Azure</span><span class="sxs-lookup"><span data-stu-id="197eb-170">Azure resource groups</span></span>](/azure/azure-resource-manager/resource-group-overview#resource-groups)