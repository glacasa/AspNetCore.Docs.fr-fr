---
title: Publier une application ASP.NET Core sur Azure avec Visual Studio
author: rick-anderson
description: Découvrez comment publier une application ASP.NET Core sur Azure App Service à l’aide de Visual Studio.
ms.author: riande
ms.custom: mvc
ms.date: 07/10/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/publish-to-azure-webapp-using-vs
ms.openlocfilehash: 634fb821ef0478dbcf57fdbb991a2e8bbb9402f1
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777083"
---
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio"></a><span data-ttu-id="b3c5b-103">Publier une application ASP.NET Core sur Azure avec Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b3c5b-103">Publish an ASP.NET Core app to Azure with Visual Studio</span></span>

<span data-ttu-id="b3c5b-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="b3c5b-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>
::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

::: moniker-end


<span data-ttu-id="b3c5b-105">Consultez [publier une application Web sur Azure App service à l’aide de Visual Studio pour Mac](https://docs.microsoft.com/visualstudio/mac/publish-app-svc?view=vsmac-2019) si vous travaillez sur MacOS.</span><span class="sxs-lookup"><span data-stu-id="b3c5b-105">See [Publish a Web app to Azure App Service using Visual Studio for Mac](https://docs.microsoft.com/visualstudio/mac/publish-app-svc?view=vsmac-2019) if you are working on macOS.</span></span>

<span data-ttu-id="b3c5b-106">Pour résoudre un problème de déploiement App Service, consultez <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="b3c5b-106">To troubleshoot an App Service deployment issue, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="set-up"></a><span data-ttu-id="b3c5b-107">Configurer</span><span class="sxs-lookup"><span data-stu-id="b3c5b-107">Set up</span></span>

* <span data-ttu-id="b3c5b-108">Ouvrez un [compte Azure gratuit](https://azure.microsoft.com/free/dotnet/) si vous n’en avez pas.</span><span class="sxs-lookup"><span data-stu-id="b3c5b-108">Open a [free Azure account](https://azure.microsoft.com/free/dotnet/) if you don't have one.</span></span> 

## <a name="create-a-web-app"></a><span data-ttu-id="b3c5b-109">Créer une application web</span><span class="sxs-lookup"><span data-stu-id="b3c5b-109">Create a web app</span></span>

<span data-ttu-id="b3c5b-110">Dans la page de démarrage de Visual Studio, sélectionnez **Fichier > Nouveau > Projet...**</span><span class="sxs-lookup"><span data-stu-id="b3c5b-110">In the Visual Studio Start Page, select **File > New > Project...**</span></span>

![Menu Fichier](publish-to-azure-webapp-using-vs/_static/file_new_project.png)

<span data-ttu-id="b3c5b-112">Renseignez la boîte de dialogue **Nouveau projet** :</span><span class="sxs-lookup"><span data-stu-id="b3c5b-112">Complete the **New Project** dialog:</span></span>

* <span data-ttu-id="b3c5b-113">Dans le volet gauche, sélectionnez **.net Core**.</span><span class="sxs-lookup"><span data-stu-id="b3c5b-113">In the left pane, select **.NET Core**.</span></span>
* <span data-ttu-id="b3c5b-114">Dans le volet central, sélectionnez **Application web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="b3c5b-114">In the center pane, select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="b3c5b-115">Sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="b3c5b-115">Select **OK**.</span></span>

![Boîte de dialogue Nouveau projet](publish-to-azure-webapp-using-vs/_static/new_prj.png)

<span data-ttu-id="b3c5b-117">Dans la boîte de dialogue **Nouvelle application web ASP.NET Core** :</span><span class="sxs-lookup"><span data-stu-id="b3c5b-117">In the **New ASP.NET Core Web Application** dialog:</span></span>

* <span data-ttu-id="b3c5b-118">Sélectionnez **application Web**.</span><span class="sxs-lookup"><span data-stu-id="b3c5b-118">Select **Web Application**.</span></span>
* <span data-ttu-id="b3c5b-119">Sélectionnez **Modifier l’authentification**.</span><span class="sxs-lookup"><span data-stu-id="b3c5b-119">Select **Change Authentication**.</span></span>

![Boîte de dialogue Nouveau projet](publish-to-azure-webapp-using-vs/_static/new_prj_2.png)

<span data-ttu-id="b3c5b-121">La boîte de dialogue **Modifier l’authentification** s’affiche.</span><span class="sxs-lookup"><span data-stu-id="b3c5b-121">The **Change Authentication** dialog appears.</span></span> 

* <span data-ttu-id="b3c5b-122">Sélectionnez **Comptes d’utilisateur individuels**.</span><span class="sxs-lookup"><span data-stu-id="b3c5b-122">Select **Individual User Accounts**.</span></span>
* <span data-ttu-id="b3c5b-123">Sélectionnez **OK** pour revenir à la **nouvelle application web ASP.NET Core**, puis sélectionnez à nouveau **OK**.</span><span class="sxs-lookup"><span data-stu-id="b3c5b-123">Select **OK** to return to the **New ASP.NET Core Web Application**, then select **OK** again.</span></span>

![Boîte de dialogue Nouvelle application web ASP.NET Core](publish-to-azure-webapp-using-vs/_static/new_prj_auth.png) 

<span data-ttu-id="b3c5b-125">Visual Studio crée la solution.</span><span class="sxs-lookup"><span data-stu-id="b3c5b-125">Visual Studio creates the solution.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="b3c5b-126">Exécuter l’application</span><span class="sxs-lookup"><span data-stu-id="b3c5b-126">Run the app</span></span>

* <span data-ttu-id="b3c5b-127">Appuyez sur CTRL+F5 pour exécuter le projet.</span><span class="sxs-lookup"><span data-stu-id="b3c5b-127">Press CTRL+F5 to run the project.</span></span>
* <span data-ttu-id="b3c5b-128">Testez les liens **À propos de** et **Contact**.</span><span class="sxs-lookup"><span data-stu-id="b3c5b-128">Test the **About** and **Contact** links.</span></span>

![Application web ouverte dans Microsoft Edge sur localhost](publish-to-azure-webapp-using-vs/_static/show.png)

### <a name="register-a-user"></a><span data-ttu-id="b3c5b-130">Inscrire un utilisateur</span><span class="sxs-lookup"><span data-stu-id="b3c5b-130">Register a user</span></span>

* <span data-ttu-id="b3c5b-131">Sélectionnez **S’inscrire**, puis inscrivez un nouvel utilisateur.</span><span class="sxs-lookup"><span data-stu-id="b3c5b-131">Select **Register** and register a new user.</span></span> <span data-ttu-id="b3c5b-132">Vous pouvez utiliser une adresse e-mail fictive.</span><span class="sxs-lookup"><span data-stu-id="b3c5b-132">You can use a fictitious email address.</span></span> <span data-ttu-id="b3c5b-133">Quand vous effectuez l’envoi, la page affiche l’erreur suivante :</span><span class="sxs-lookup"><span data-stu-id="b3c5b-133">When you submit, the page displays the following error:</span></span>

    <span data-ttu-id="b3c5b-134">*«Erreur de serveur interne : une opération de base de données a échoué lors du traitement de la demande. Exception SQL : impossible d’ouvrir la base de données. L’application de migrations existantes pour le contexte de la base de la base de la base de de l’application peut résoudre ce problème*</span><span class="sxs-lookup"><span data-stu-id="b3c5b-134">*"Internal Server Error: A database operation failed while processing the request. SQL exception: Cannot open the database. Applying existing migrations for Application DB context may resolve this issue."*</span></span>
* <span data-ttu-id="b3c5b-135">Sélectionnez **Appliquer les migrations**, puis, une fois la page mise à jour, actualisez-la.</span><span class="sxs-lookup"><span data-stu-id="b3c5b-135">Select **Apply Migrations** and, once the page updates, refresh the page.</span></span>

![Erreur de serveur interne : Une opération de base de données a échoué lors du traitement de la requête.](publish-to-azure-webapp-using-vs/_static/mig.png)

<span data-ttu-id="b3c5b-139">L’application affiche l’adresse e-mail utilisée pour inscrire le nouvel utilisateur et un lien **Se déconnecter**.</span><span class="sxs-lookup"><span data-stu-id="b3c5b-139">The app displays the email used to register the new user and a **Log out** link.</span></span>

![Application web ouverte dans Microsoft Edge.](publish-to-azure-webapp-using-vs/_static/hello.png)

## <a name="deploy-the-app-to-azure"></a><span data-ttu-id="b3c5b-142">Déploiement de l’application dans Azure</span><span class="sxs-lookup"><span data-stu-id="b3c5b-142">Deploy the app to Azure</span></span>

<span data-ttu-id="b3c5b-143">Dans Explorateur de solutions, cliquez avec le bouton droit sur le projet, puis sélectionnez **publier...**.</span><span class="sxs-lookup"><span data-stu-id="b3c5b-143">Right-click on the project in Solution Explorer and select **Publish...**.</span></span>

![Menu contextuel ouvert avec le lien Publier mis en surbrillance](publish-to-azure-webapp-using-vs/_static/pub.png)

<span data-ttu-id="b3c5b-145">Dans la boîte de dialogue **Publier** :</span><span class="sxs-lookup"><span data-stu-id="b3c5b-145">In the **Publish** dialog:</span></span>

* <span data-ttu-id="b3c5b-146">Cliquez sur **Microsoft Azure App Service**.</span><span class="sxs-lookup"><span data-stu-id="b3c5b-146">Select **Microsoft Azure App Service**.</span></span>
* <span data-ttu-id="b3c5b-147">Sélectionnez l’icône d’engrenage, puis **Créer un profil**.</span><span class="sxs-lookup"><span data-stu-id="b3c5b-147">Select the gear icon and then select **Create Profile**.</span></span>
* <span data-ttu-id="b3c5b-148">Sélectionnez **Créer un profil**.</span><span class="sxs-lookup"><span data-stu-id="b3c5b-148">Select **Create Profile**.</span></span>

![Boîte de dialogue Publier](publish-to-azure-webapp-using-vs/_static/maas1.png)

### <a name="create-azure-resources"></a><span data-ttu-id="b3c5b-150">Créer des ressources Azure</span><span class="sxs-lookup"><span data-stu-id="b3c5b-150">Create Azure resources</span></span>

<span data-ttu-id="b3c5b-151">La boîte de dialogue **créer un app service** s’affiche :</span><span class="sxs-lookup"><span data-stu-id="b3c5b-151">The **Create App Service** dialog appears:</span></span>

* <span data-ttu-id="b3c5b-152">Entrez votre abonnement.</span><span class="sxs-lookup"><span data-stu-id="b3c5b-152">Enter your subscription.</span></span>
* <span data-ttu-id="b3c5b-153">Les champs d’entrée **Nom de l’application**, **Groupe de ressources** et **Plan App Service** sont renseignés.</span><span class="sxs-lookup"><span data-stu-id="b3c5b-153">The **App Name**, **Resource Group**, and **App Service Plan** entry fields are populated.</span></span> <span data-ttu-id="b3c5b-154">Vous pouvez conserver ces noms ou les changer.</span><span class="sxs-lookup"><span data-stu-id="b3c5b-154">You can keep these names or change them.</span></span>

![Boîte de dialogue App Service](publish-to-azure-webapp-using-vs/_static/newrg1.png)

* <span data-ttu-id="b3c5b-156">Sélectionnez l’onglet **Services** pour créer une base de données.</span><span class="sxs-lookup"><span data-stu-id="b3c5b-156">Select the **Services** tab to create a new database.</span></span>

* <span data-ttu-id="b3c5b-157">Sélectionnez l’icône **+** verte pour créer une SQL Database</span><span class="sxs-lookup"><span data-stu-id="b3c5b-157">Select the green **+** icon to create a new SQL Database</span></span>

![New SQL Database](publish-to-azure-webapp-using-vs/_static/sql.png)

* <span data-ttu-id="b3c5b-159">Sélectionnez **Nouveau...** dans la boîte de dialogue **Configurer la base de données SQL** pour créer une base de données.</span><span class="sxs-lookup"><span data-stu-id="b3c5b-159">Select **New...** on the **Configure SQL Database** dialog to create a new database.</span></span>

![Nouvelle base de données et nouveau serveur SQL](publish-to-azure-webapp-using-vs/_static/conf.png)

<span data-ttu-id="b3c5b-161">La boîte de dialogue **Configurer le serveur SQL Server** s’affiche.</span><span class="sxs-lookup"><span data-stu-id="b3c5b-161">The **Configure SQL Server** dialog appears.</span></span>

* <span data-ttu-id="b3c5b-162">Entrez un nom d’utilisateur et un mot de passe administrateur, puis sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="b3c5b-162">Enter an administrator user name and password, and then select **OK**.</span></span> <span data-ttu-id="b3c5b-163">Vous pouvez conserver le **nom du serveur**par défaut.</span><span class="sxs-lookup"><span data-stu-id="b3c5b-163">You can keep the default **Server Name**.</span></span> 

> [!NOTE]
> <span data-ttu-id="b3c5b-164">« admin » n’est pas autorisé comme nom d’utilisateur administrateur.</span><span class="sxs-lookup"><span data-stu-id="b3c5b-164">"admin" isn't allowed as the administrator user name.</span></span>

![Boîte de dialogue Configurer le serveur SQL Server](publish-to-azure-webapp-using-vs/_static/conf_servername.png)

* <span data-ttu-id="b3c5b-166">Sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="b3c5b-166">Select **OK**.</span></span>

<span data-ttu-id="b3c5b-167">Visual Studio retourne la boîte de dialogue **Créer App Service**.</span><span class="sxs-lookup"><span data-stu-id="b3c5b-167">Visual Studio returns to the **Create App Service** dialog.</span></span>

* <span data-ttu-id="b3c5b-168">Sélectionnez **Créer** dans la boîte de dialogue **Créer App Service**.</span><span class="sxs-lookup"><span data-stu-id="b3c5b-168">Select **Create** on the **Create App Service** dialog.</span></span>

![Boîte de dialogue Configurer la base de données SQL](publish-to-azure-webapp-using-vs/_static/conf_final.png)

<span data-ttu-id="b3c5b-170">Visual Studio crée l’application web et SQL Server sur Azure.</span><span class="sxs-lookup"><span data-stu-id="b3c5b-170">Visual Studio creates the Web app and SQL Server on Azure.</span></span> <span data-ttu-id="b3c5b-171">Cette étape peut prendre quelques minutes.</span><span class="sxs-lookup"><span data-stu-id="b3c5b-171">This step can take a few minutes.</span></span> <span data-ttu-id="b3c5b-172">Pour plus d’informations sur les ressources créées, consultez [Ressources supplémentaires](#additional-resources).</span><span class="sxs-lookup"><span data-stu-id="b3c5b-172">For information on the resources created, see [Additional resources](#additional-resources).</span></span>

<span data-ttu-id="b3c5b-173">Quand le déploiement est terminé, sélectionnez **Paramètres** :</span><span class="sxs-lookup"><span data-stu-id="b3c5b-173">When deployment completes, select **Settings**:</span></span>

![Boîte de dialogue Configurer le serveur SQL Server](publish-to-azure-webapp-using-vs/_static/set.png)

<span data-ttu-id="b3c5b-175">Dans la page **Paramètres** de la boîte de dialogue **Publier** :</span><span class="sxs-lookup"><span data-stu-id="b3c5b-175">On the **Settings** page of the **Publish** dialog:</span></span>

* <span data-ttu-id="b3c5b-176">Développez **bases de données** et cochez **la case utiliser cette chaîne de connexion au moment de l’exécution**.</span><span class="sxs-lookup"><span data-stu-id="b3c5b-176">Expand **Databases** and check **Use this connection string at runtime**.</span></span>
* <span data-ttu-id="b3c5b-177">Développez **Entity Framework migrations** et cochez **appliquer cette migration lors de la publication**.</span><span class="sxs-lookup"><span data-stu-id="b3c5b-177">Expand **Entity Framework Migrations** and check **Apply this migration on publish**.</span></span>

* <span data-ttu-id="b3c5b-178">Sélectionnez **Enregistrer**.</span><span class="sxs-lookup"><span data-stu-id="b3c5b-178">Select **Save**.</span></span> <span data-ttu-id="b3c5b-179">Visual Studio retourne à la boîte de dialogue **Publier**.</span><span class="sxs-lookup"><span data-stu-id="b3c5b-179">Visual Studio returns to the **Publish** dialog.</span></span> 

![Boîte de dialogue Publier : panneau Paramètres](publish-to-azure-webapp-using-vs/_static/pubs.png)

<span data-ttu-id="b3c5b-181">Cliquez sur **Publier**.</span><span class="sxs-lookup"><span data-stu-id="b3c5b-181">Click **Publish**.</span></span> <span data-ttu-id="b3c5b-182">Visual Studio publie votre application sur Azure.</span><span class="sxs-lookup"><span data-stu-id="b3c5b-182">Visual Studio publishes your app to Azure.</span></span> <span data-ttu-id="b3c5b-183">Quand le déploiement est terminé, l’application est ouverte dans un navigateur.</span><span class="sxs-lookup"><span data-stu-id="b3c5b-183">When the deployment completes, the app is opened in a browser.</span></span>

### <a name="test-your-app-in-azure"></a><span data-ttu-id="b3c5b-184">Tester votre application dans Azure</span><span class="sxs-lookup"><span data-stu-id="b3c5b-184">Test your app in Azure</span></span>

* <span data-ttu-id="b3c5b-185">Tester les liens **à propos** de et **contact**</span><span class="sxs-lookup"><span data-stu-id="b3c5b-185">Test the **About** and **Contact** links</span></span>

* <span data-ttu-id="b3c5b-186">Enregistre un nouvel utilisateur</span><span class="sxs-lookup"><span data-stu-id="b3c5b-186">Register a new user</span></span>

![Application web ouverte dans Microsoft Edge sur Azure App Service](publish-to-azure-webapp-using-vs/_static/register.png)

### <a name="update-the-app"></a><span data-ttu-id="b3c5b-188">Mettre à jour l’application</span><span class="sxs-lookup"><span data-stu-id="b3c5b-188">Update the app</span></span>

* <span data-ttu-id="b3c5b-189">Modifiez la page *pages/about. cshtml* Razor et modifiez son contenu.</span><span class="sxs-lookup"><span data-stu-id="b3c5b-189">Edit the *Pages/About.cshtml* Razor page and change its contents.</span></span> <span data-ttu-id="b3c5b-190">Par exemple, vous pouvez modifier le paragraphe pour indiquer « Hello ASP.NET Core! » :</span><span class="sxs-lookup"><span data-stu-id="b3c5b-190">For example, you can modify the paragraph to say "Hello ASP.NET Core!":</span></span>

    [!code-html[About](publish-to-azure-webapp-using-vs/sample/about.cshtml?highlight=9&range=1-9)]

* <span data-ttu-id="b3c5b-191">Cliquez avec le bouton droit sur le projet, puis sélectionnez **Publier**.</span><span class="sxs-lookup"><span data-stu-id="b3c5b-191">Right-click on the project and select **Publish...** again.</span></span>

![Menu contextuel ouvert avec le lien Publier mis en surbrillance](publish-to-azure-webapp-using-vs/_static/pub.png)

* <span data-ttu-id="b3c5b-193">Une fois l’application publiée, vérifiez que les changements apportés sont disponibles sur Azure.</span><span class="sxs-lookup"><span data-stu-id="b3c5b-193">After the app is published, verify the changes you made are available on Azure.</span></span>

![Vérifiez que la tâche est terminée](publish-to-azure-webapp-using-vs/_static/final.png)

### <a name="clean-up"></a><span data-ttu-id="b3c5b-195">Nettoyer</span><span class="sxs-lookup"><span data-stu-id="b3c5b-195">Clean up</span></span>

<span data-ttu-id="b3c5b-196">Après avoir testé l’application, accédez au [portail Azure](https://portal.azure.com/), puis supprimez l’application.</span><span class="sxs-lookup"><span data-stu-id="b3c5b-196">When you have finished testing the app, go to the [Azure portal](https://portal.azure.com/) and delete the app.</span></span>

* <span data-ttu-id="b3c5b-197">Sélectionnez **Groupes de ressources**, puis sélectionnez le groupe de ressources que vous avez créé.</span><span class="sxs-lookup"><span data-stu-id="b3c5b-197">Select **Resource groups**, then select the resource group you created.</span></span>

![Portail Azure : Groupes de ressources dans le menu de la barre latérale](publish-to-azure-webapp-using-vs/_static/portalrg.png)

* <span data-ttu-id="b3c5b-199">Dans la page **Groupes de ressources**, sélectionnez **Supprimer**.</span><span class="sxs-lookup"><span data-stu-id="b3c5b-199">In the **Resource groups** page, select **Delete**.</span></span>

![Portail Azure : page Groupes de ressources](publish-to-azure-webapp-using-vs/_static/rgd.png)

* <span data-ttu-id="b3c5b-201">Entrez le nom du groupe de ressources, puis sélectionnez **Supprimer**.</span><span class="sxs-lookup"><span data-stu-id="b3c5b-201">Enter the name of the resource group and select **Delete**.</span></span> <span data-ttu-id="b3c5b-202">Votre application et toutes les autres ressources créées dans ce didacticiel sont désormais supprimées d’Azure.</span><span class="sxs-lookup"><span data-stu-id="b3c5b-202">Your app and all other resources created in this tutorial are now deleted from Azure.</span></span>

### <a name="next-steps"></a><span data-ttu-id="b3c5b-203">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="b3c5b-203">Next steps</span></span>

* <xref:host-and-deploy/azure-apps/azure-continuous-deployment>

## <a name="additional-resources"></a><span data-ttu-id="b3c5b-204">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="b3c5b-204">Additional resources</span></span>

* <span data-ttu-id="b3c5b-205">Pour Visual Studio Code, consultez [Profils de publication](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles).</span><span class="sxs-lookup"><span data-stu-id="b3c5b-205">For Visual Studio Code, see [Publish profiles](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles).</span></span>
* [<span data-ttu-id="b3c5b-206">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="b3c5b-206">Azure App Service</span></span>](/azure/app-service/app-service-web-overview)
* [<span data-ttu-id="b3c5b-207">Groupes de ressources Azure</span><span class="sxs-lookup"><span data-stu-id="b3c5b-207">Azure resource groups</span></span>](/azure/azure-resource-manager/resource-group-overview#resource-groups)
* [<span data-ttu-id="b3c5b-208">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="b3c5b-208">Azure SQL Database</span></span>](/azure/sql-database/)
* <xref:host-and-deploy/visual-studio-publish-profiles>
* <xref:test/troubleshoot-azure-iis>
