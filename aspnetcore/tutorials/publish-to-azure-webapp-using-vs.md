---
<span data-ttu-id="913c1-101">titre : Auteur : Description : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="913c1-101">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="913c1-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="913c1-102">'Blazor'</span></span>
- <span data-ttu-id="913c1-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="913c1-103">'Identity'</span></span>
- <span data-ttu-id="913c1-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="913c1-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="913c1-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="913c1-105">'Razor'</span></span>
- <span data-ttu-id="913c1-106">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="913c1-106">'SignalR' uid:</span></span> 

---
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio"></a><span data-ttu-id="913c1-107">Publier une application ASP.NET Core sur Azure avec Visual Studio</span><span class="sxs-lookup"><span data-stu-id="913c1-107">Publish an ASP.NET Core app to Azure with Visual Studio</span></span>

<span data-ttu-id="913c1-108">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="913c1-108">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>
::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

::: moniker-end


<span data-ttu-id="913c1-109">Consultez [publier une application Web sur Azure App service à l’aide de Visual Studio pour Mac](https://docs.microsoft.com/visualstudio/mac/publish-app-svc?view=vsmac-2019) si vous travaillez sur MacOS.</span><span class="sxs-lookup"><span data-stu-id="913c1-109">See [Publish a Web app to Azure App Service using Visual Studio for Mac](https://docs.microsoft.com/visualstudio/mac/publish-app-svc?view=vsmac-2019) if you are working on macOS.</span></span>

<span data-ttu-id="913c1-110">Pour résoudre un problème de déploiement App Service, consultez <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="913c1-110">To troubleshoot an App Service deployment issue, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="set-up"></a><span data-ttu-id="913c1-111">Configurer</span><span class="sxs-lookup"><span data-stu-id="913c1-111">Set up</span></span>

* <span data-ttu-id="913c1-112">Ouvrez un [compte Azure gratuit](https://azure.microsoft.com/free/dotnet/) si vous n’en avez pas.</span><span class="sxs-lookup"><span data-stu-id="913c1-112">Open a [free Azure account](https://azure.microsoft.com/free/dotnet/) if you don't have one.</span></span> 

## <a name="create-a-web-app"></a><span data-ttu-id="913c1-113">Créer une application web</span><span class="sxs-lookup"><span data-stu-id="913c1-113">Create a web app</span></span>

<span data-ttu-id="913c1-114">Dans la page de démarrage de Visual Studio, sélectionnez **Fichier > Nouveau > Projet...**</span><span class="sxs-lookup"><span data-stu-id="913c1-114">In the Visual Studio Start Page, select **File > New > Project...**</span></span>

![Menu Fichier](publish-to-azure-webapp-using-vs/_static/file_new_project.png)

<span data-ttu-id="913c1-116">Renseignez la boîte de dialogue **Nouveau projet** :</span><span class="sxs-lookup"><span data-stu-id="913c1-116">Complete the **New Project** dialog:</span></span>

* <span data-ttu-id="913c1-117">Sélectionnez **Application web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="913c1-117">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="913c1-118">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="913c1-118">Select **Next**.</span></span>

![Boîte de dialogue Nouveau projet](publish-to-azure-webapp-using-vs/_static/new_prj.png)

<span data-ttu-id="913c1-120">Dans la boîte de dialogue **Nouvelle application web ASP.NET Core** :</span><span class="sxs-lookup"><span data-stu-id="913c1-120">In the **New ASP.NET Core Web Application** dialog:</span></span>

* <span data-ttu-id="913c1-121">Sélectionnez **application Web**.</span><span class="sxs-lookup"><span data-stu-id="913c1-121">Select **Web Application**.</span></span>
* <span data-ttu-id="913c1-122">Sélectionnez **modifier** sous authentification.</span><span class="sxs-lookup"><span data-stu-id="913c1-122">Select **Change** under Authentication.</span></span>

![Boîte de dialogue Nouveau ASP.NET Core Web](publish-to-azure-webapp-using-vs/_static/new_prj_2.png)

<span data-ttu-id="913c1-124">La boîte de dialogue **Modifier l’authentification** s’affiche.</span><span class="sxs-lookup"><span data-stu-id="913c1-124">The **Change Authentication** dialog appears.</span></span> 

* <span data-ttu-id="913c1-125">Sélectionnez **Comptes d’utilisateur individuels**.</span><span class="sxs-lookup"><span data-stu-id="913c1-125">Select **Individual User Accounts**.</span></span>
* <span data-ttu-id="913c1-126">Sélectionnez **OK** pour revenir à la **nouvelle application Web ASP.net Core**, puis sélectionnez **créer**.</span><span class="sxs-lookup"><span data-stu-id="913c1-126">Select **OK** to return to the **New ASP.NET Core Web Application**, then select **Create**.</span></span>

![Boîte de dialogue Nouvelle application web ASP.NET Core](publish-to-azure-webapp-using-vs/_static/new_prj_auth.png) 

<span data-ttu-id="913c1-128">Visual Studio crée la solution.</span><span class="sxs-lookup"><span data-stu-id="913c1-128">Visual Studio creates the solution.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="913c1-129">Exécuter l’application</span><span class="sxs-lookup"><span data-stu-id="913c1-129">Run the app</span></span>

* <span data-ttu-id="913c1-130">Appuyez sur CTRL+F5 pour exécuter le projet.</span><span class="sxs-lookup"><span data-stu-id="913c1-130">Press CTRL+F5 to run the project.</span></span>
* <span data-ttu-id="913c1-131">Testez le lien de **confidentialité** .</span><span class="sxs-lookup"><span data-stu-id="913c1-131">Test the **Privacy** link.</span></span>

![Application web ouverte dans Microsoft Edge sur localhost](publish-to-azure-webapp-using-vs/_static/show.png)

### <a name="register-a-user"></a><span data-ttu-id="913c1-133">Inscrire un utilisateur</span><span class="sxs-lookup"><span data-stu-id="913c1-133">Register a user</span></span>

* <span data-ttu-id="913c1-134">Sélectionnez **S’inscrire**, puis inscrivez un nouvel utilisateur.</span><span class="sxs-lookup"><span data-stu-id="913c1-134">Select **Register** and register a new user.</span></span> <span data-ttu-id="913c1-135">Vous pouvez utiliser une adresse e-mail fictive.</span><span class="sxs-lookup"><span data-stu-id="913c1-135">You can use a fictitious email address.</span></span> <span data-ttu-id="913c1-136">Quand vous effectuez l’envoi, la page affiche l’erreur suivante :</span><span class="sxs-lookup"><span data-stu-id="913c1-136">When you submit, the page displays the following error:</span></span>

    <span data-ttu-id="913c1-137">*«Une opération de base de données a échoué lors du traitement de la demande. L’application de migrations existantes pour le contexte de la base de la base de la base de de l’application peut résoudre ce problème*</span><span class="sxs-lookup"><span data-stu-id="913c1-137">*"A database operation failed while processing the request. Applying existing migrations for Application DB context may resolve this issue."*</span></span>
* <span data-ttu-id="913c1-138">Sélectionnez **Appliquer les migrations**, puis, une fois la page mise à jour, actualisez-la.</span><span class="sxs-lookup"><span data-stu-id="913c1-138">Select **Apply Migrations** and, once the page updates, refresh the page.</span></span>

![Une opération de base de données a échoué lors du traitement de la requête.](publish-to-azure-webapp-using-vs/_static/mig.png)

<span data-ttu-id="913c1-141">L’application affiche l’e-mail utilisé pour inscrire le nouvel utilisateur et un lien de **déconnexion** .</span><span class="sxs-lookup"><span data-stu-id="913c1-141">The app displays the email used to register the new user and a **Logout** link.</span></span>

![Application web ouverte dans Microsoft Edge.](publish-to-azure-webapp-using-vs/_static/hello.png)

## <a name="deploy-the-app-to-azure"></a><span data-ttu-id="913c1-144">Déploiement de l’application dans Azure</span><span class="sxs-lookup"><span data-stu-id="913c1-144">Deploy the app to Azure</span></span>

<span data-ttu-id="913c1-145">Dans Explorateur de solutions, cliquez avec le bouton droit sur le projet, puis sélectionnez **publier...**.</span><span class="sxs-lookup"><span data-stu-id="913c1-145">Right-click on the project in Solution Explorer and select **Publish...**.</span></span>

![Menu contextuel ouvert avec le lien Publier mis en surbrillance](publish-to-azure-webapp-using-vs/_static/pub.png)

<span data-ttu-id="913c1-147">Dans la boîte de dialogue **Publier** :</span><span class="sxs-lookup"><span data-stu-id="913c1-147">In the **Publish** dialog:</span></span>

* <span data-ttu-id="913c1-148">Sélectionnez **Azure**.</span><span class="sxs-lookup"><span data-stu-id="913c1-148">Select **Azure**.</span></span>
* <span data-ttu-id="913c1-149">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="913c1-149">Select **Next**.</span></span>

![Boîte de dialogue Publier](publish-to-azure-webapp-using-vs/_static/maas1.png)

<span data-ttu-id="913c1-151">Dans la boîte de dialogue **Publier** :</span><span class="sxs-lookup"><span data-stu-id="913c1-151">In the **Publish** dialog:</span></span>

* <span data-ttu-id="913c1-152">Sélectionnez **Azure App service (Linux)**.</span><span class="sxs-lookup"><span data-stu-id="913c1-152">Select **Azure App Service (Linux)**.</span></span>
* <span data-ttu-id="913c1-153">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="913c1-153">Select **Next**.</span></span>

![Boîte de dialogue publier : sélectionner un service Azure](publish-to-azure-webapp-using-vs/_static/maas2.png)

<span data-ttu-id="913c1-155">Dans la boîte de dialogue **publier** , sélectionnez **créer une Azure App service...**</span><span class="sxs-lookup"><span data-stu-id="913c1-155">In the **Publish** dialog select **Create a new Azure App Service...**</span></span>

![Boîte de dialogue publier : sélectionner une instance de service Azure](publish-to-azure-webapp-using-vs/_static/maas3.png)

<span data-ttu-id="913c1-157">La boîte de dialogue **créer un app service** s’affiche :</span><span class="sxs-lookup"><span data-stu-id="913c1-157">The **Create App Service** dialog appears:</span></span>

* <span data-ttu-id="913c1-158">Les champs d’entrée **Nom de l’application**, **Groupe de ressources** et **Plan App Service** sont renseignés.</span><span class="sxs-lookup"><span data-stu-id="913c1-158">The **App Name**, **Resource Group**, and **App Service Plan** entry fields are populated.</span></span> <span data-ttu-id="913c1-159">Vous pouvez conserver ces noms ou les changer.</span><span class="sxs-lookup"><span data-stu-id="913c1-159">You can keep these names or change them.</span></span>
* <span data-ttu-id="913c1-160">Sélectionnez **Create** (Créer).</span><span class="sxs-lookup"><span data-stu-id="913c1-160">Select **Create**.</span></span>

![Boîte de dialogue Créer App Service](publish-to-azure-webapp-using-vs/_static/newrg1.png)

<span data-ttu-id="913c1-162">Une fois la création terminée, la boîte de dialogue est fermée automatiquement et la boîte de dialogue **publier** se met à nouveau en place :</span><span class="sxs-lookup"><span data-stu-id="913c1-162">After creation is completed the dialog is automatically closed and the **Publish** dialog gets focus again:</span></span>

* <span data-ttu-id="913c1-163">La nouvelle instance qui vient d’être créée est automatiquement sélectionnée.</span><span class="sxs-lookup"><span data-stu-id="913c1-163">The new instance that was just created is automatically selected.</span></span>
* <span data-ttu-id="913c1-164">Sélectionnez **Terminer**.</span><span class="sxs-lookup"><span data-stu-id="913c1-164">Select **Finish**.</span></span>

![Boîte de dialogue publier : sélectionner une instance de App Service](publish-to-azure-webapp-using-vs/_static/select_as.png)

<span data-ttu-id="913c1-166">Vous voyez ensuite la page **Résumé du profil de publication** .</span><span class="sxs-lookup"><span data-stu-id="913c1-166">Next you see the **Publish Profile summary** page.</span></span> <span data-ttu-id="913c1-167">Visual Studio a détecté que cette application nécessite une base de données SQL Server et qu’elle vous demande de la configurer.</span><span class="sxs-lookup"><span data-stu-id="913c1-167">Visual Studio has detected that this application requires a SQL Server database and it's asking you to configure it.</span></span> <span data-ttu-id="913c1-168">Sélectionnez **Configurer**.</span><span class="sxs-lookup"><span data-stu-id="913c1-168">Select **Configure**.</span></span>

![Page Résumé du profil de publication : configurer la dépendance SQL Server](publish-to-azure-webapp-using-vs/_static/sql.png)

<span data-ttu-id="913c1-170">La boîte de dialogue **configurer la dépendance** s’affiche :</span><span class="sxs-lookup"><span data-stu-id="913c1-170">The **Configure dependency** dialog appears:</span></span>

* <span data-ttu-id="913c1-171">Sélectionnez **Azure SQL Database**.</span><span class="sxs-lookup"><span data-stu-id="913c1-171">Select **Azure SQL Database**.</span></span>
* <span data-ttu-id="913c1-172">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="913c1-172">Select **Next**.</span></span>

![Boîte de dialogue configurer SQL Server dépendance](publish-to-azure-webapp-using-vs/_static/sql1.png)

<span data-ttu-id="913c1-174">Dans la boîte **de dialogue Configurer la base de données SQL Azure** , sélectionnez **créer un SQL Database**</span><span class="sxs-lookup"><span data-stu-id="913c1-174">In the **Configure Azure SQL database** dialog select **Create a SQL Database**</span></span>

![Boîte de dialogue Configurer Azure SQL Database](publish-to-azure-webapp-using-vs/_static/sql2.png)

<span data-ttu-id="913c1-176">L' **Azure SQL Database créer** s’affiche :</span><span class="sxs-lookup"><span data-stu-id="913c1-176">The **Create Azure SQL Database** appears:</span></span>

* <span data-ttu-id="913c1-177">Les champs **nom de la base de données**, **groupe de ressources**, **serveur de base de données** et entrée du **plan de App service** sont remplis.</span><span class="sxs-lookup"><span data-stu-id="913c1-177">The **Database name**, **Resource Group**, **Database server** and **App Service Plan** entry fields are populated.</span></span> <span data-ttu-id="913c1-178">Vous pouvez conserver ces valeurs ou les modifier.</span><span class="sxs-lookup"><span data-stu-id="913c1-178">You can keep these values or change them.</span></span>
* <span data-ttu-id="913c1-179">Entrez le **nom d’utilisateur** et le **mot de passe** de l’administrateur de base de données pour le **serveur de base de données** sélectionné (Notez que le compte que vous utilisez doit disposer des autorisations nécessaires pour créer la nouvelle base de données SQL Azure)</span><span class="sxs-lookup"><span data-stu-id="913c1-179">Enter the **Database administrator username** and **Database administrator password** for the selected **Database server** (note the account you use must have the necessary permissions to create the new Azure SQL database)</span></span>
* <span data-ttu-id="913c1-180">Sélectionnez **Create** (Créer).</span><span class="sxs-lookup"><span data-stu-id="913c1-180">Select **Create**.</span></span>

![Boîte de dialogue Nouveau Azure SQL Database](publish-to-azure-webapp-using-vs/_static/sql_create.png)

<span data-ttu-id="913c1-182">Une fois la création terminée, la boîte de dialogue est fermée automatiquement et la boîte de dialogue **configurer Azure SQL Database** est à nouveau activée :</span><span class="sxs-lookup"><span data-stu-id="913c1-182">After creation is completed the dialog is automatically closed and the **Configure Azure SQL Database** dialog gets focus again:</span></span>

* <span data-ttu-id="913c1-183">La nouvelle instance qui vient d’être créée est automatiquement sélectionnée.</span><span class="sxs-lookup"><span data-stu-id="913c1-183">The new instance that was just created is automatically selected.</span></span>
* <span data-ttu-id="913c1-184">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="913c1-184">Select **Next**.</span></span>

![Boîte de dialogue Configurer Azure SQL Database](publish-to-azure-webapp-using-vs/_static/sql_select.png)

<span data-ttu-id="913c1-186">À l’étape suivante de la boîte de dialogue **configurer Azure SQL Database** :</span><span class="sxs-lookup"><span data-stu-id="913c1-186">In the next step of the **Configure Azure SQL Database** dialog:</span></span>

* <span data-ttu-id="913c1-187">Entrez les champs **nom d’utilisateur** et **mot de passe de** connexion à la base de données.</span><span class="sxs-lookup"><span data-stu-id="913c1-187">Enter the **Database connection user name** and **Database connection password** fields.</span></span> <span data-ttu-id="913c1-188">Il s’agit des détails que votre application utilisera pour se connecter à la base de données au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="913c1-188">These are the details your application will use to connect to the database at runtime.</span></span> <span data-ttu-id="913c1-189">La meilleure pratique consiste à éviter d’utiliser les mêmes détails que le nom d’utilisateur administrateur & mot de passe utilisé à l’étape précédente.</span><span class="sxs-lookup"><span data-stu-id="913c1-189">Best practice is to avoid using the same details as the admin username & password used in the previous step.</span></span>
* <span data-ttu-id="913c1-190">Sélectionnez **Terminer**.</span><span class="sxs-lookup"><span data-stu-id="913c1-190">Select **Finish**.</span></span>

![Boîte de dialogue Configurer Azure SQL Database, détails de la chaîne de connexion](publish-to-azure-webapp-using-vs/_static/sql_connection.png)

<span data-ttu-id="913c1-192">Dans la page **Résumé du profil de publication** , sélectionnez **paramètres**:</span><span class="sxs-lookup"><span data-stu-id="913c1-192">In the **Publish Profile summary** page select **Settings**:</span></span>

![Page Résumé du profil de publication : modifier les paramètres](publish-to-azure-webapp-using-vs/_static/pp_configured.png)

<span data-ttu-id="913c1-194">Dans la page **Paramètres** de la boîte de dialogue **Publier** :</span><span class="sxs-lookup"><span data-stu-id="913c1-194">On the **Settings** page of the **Publish** dialog:</span></span>

* <span data-ttu-id="913c1-195">Développez **bases de données** et cochez **la case utiliser cette chaîne de connexion au moment de l’exécution**.</span><span class="sxs-lookup"><span data-stu-id="913c1-195">Expand **Databases** and check **Use this connection string at runtime**.</span></span>
* <span data-ttu-id="913c1-196">Développez **Entity Framework migrations** et cochez **appliquer cette migration lors de la publication**.</span><span class="sxs-lookup"><span data-stu-id="913c1-196">Expand **Entity Framework Migrations** and check **Apply this migration on publish**.</span></span>

* <span data-ttu-id="913c1-197">Sélectionnez **Enregistrer**.</span><span class="sxs-lookup"><span data-stu-id="913c1-197">Select **Save**.</span></span> <span data-ttu-id="913c1-198">Visual Studio retourne à la boîte de dialogue **Publier**.</span><span class="sxs-lookup"><span data-stu-id="913c1-198">Visual Studio returns to the **Publish** dialog.</span></span> 

![Boîte de dialogue Publier : panneau Paramètres](publish-to-azure-webapp-using-vs/_static/pp_settings.png)

<span data-ttu-id="913c1-200">Cliquez sur **Publier**.</span><span class="sxs-lookup"><span data-stu-id="913c1-200">Click **Publish**.</span></span> <span data-ttu-id="913c1-201">Visual Studio publie votre application sur Azure.</span><span class="sxs-lookup"><span data-stu-id="913c1-201">Visual Studio publishes your app to Azure.</span></span> <span data-ttu-id="913c1-202">Quand le déploiement est terminé, l’application est ouverte dans un navigateur.</span><span class="sxs-lookup"><span data-stu-id="913c1-202">When the deployment completes, the app is opened in a browser.</span></span>

![Boîte de dialogue Publier : panneau Paramètres](publish-to-azure-webapp-using-vs/_static/pp_publish.png)

### <a name="update-the-app"></a><span data-ttu-id="913c1-204">Mettre à jour l’application</span><span class="sxs-lookup"><span data-stu-id="913c1-204">Update the app</span></span>

* <span data-ttu-id="913c1-205">Modifiez la page *pages/index. cshtml* Razor et modifiez son contenu.</span><span class="sxs-lookup"><span data-stu-id="913c1-205">Edit the *Pages/Index.cshtml* Razor page and change its contents.</span></span> <span data-ttu-id="913c1-206">Par exemple, vous pouvez modifier le paragraphe pour indiquer « Hello ASP.NET Core! » :</span><span class="sxs-lookup"><span data-stu-id="913c1-206">For example, you can modify the paragraph to say "Hello ASP.NET Core!":</span></span>

    [!code-html[Index](publish-to-azure-webapp-using-vs/sample/index.cshtml?highlight=10&range=1-12)]

* <span data-ttu-id="913c1-207">Sélectionnez à nouveau **publier** dans la page **Résumé du profil de publication** .</span><span class="sxs-lookup"><span data-stu-id="913c1-207">Select **Publish** from the **Publish Profile summary** page again.</span></span>

![Page Résumé du profil de publication](publish-to-azure-webapp-using-vs/_static/pp_publish.png)

* <span data-ttu-id="913c1-209">Une fois l’application publiée, vérifiez que les changements apportés sont disponibles sur Azure.</span><span class="sxs-lookup"><span data-stu-id="913c1-209">After the app is published, verify the changes you made are available on Azure.</span></span>

![Vérifiez que la tâche est terminée](publish-to-azure-webapp-using-vs/_static/final.png)

### <a name="clean-up"></a><span data-ttu-id="913c1-211">Nettoyer</span><span class="sxs-lookup"><span data-stu-id="913c1-211">Clean up</span></span>

<span data-ttu-id="913c1-212">Après avoir testé l’application, accédez au [portail Azure](https://portal.azure.com/), puis supprimez l’application.</span><span class="sxs-lookup"><span data-stu-id="913c1-212">When you have finished testing the app, go to the [Azure portal](https://portal.azure.com/) and delete the app.</span></span>

* <span data-ttu-id="913c1-213">Sélectionnez **Groupes de ressources**, puis sélectionnez le groupe de ressources que vous avez créé.</span><span class="sxs-lookup"><span data-stu-id="913c1-213">Select **Resource groups**, then select the resource group you created.</span></span>

![Portail Azure : Groupes de ressources dans le menu de la barre latérale](publish-to-azure-webapp-using-vs/_static/portalrg.png)

* <span data-ttu-id="913c1-215">Dans la page **Groupes de ressources**, sélectionnez **Supprimer**.</span><span class="sxs-lookup"><span data-stu-id="913c1-215">In the **Resource groups** page, select **Delete**.</span></span>

![Portail Azure : page Groupes de ressources](publish-to-azure-webapp-using-vs/_static/rgd.png)

* <span data-ttu-id="913c1-217">Entrez le nom du groupe de ressources, puis sélectionnez **Supprimer**.</span><span class="sxs-lookup"><span data-stu-id="913c1-217">Enter the name of the resource group and select **Delete**.</span></span> <span data-ttu-id="913c1-218">Votre application et toutes les autres ressources créées dans ce didacticiel sont désormais supprimées d’Azure.</span><span class="sxs-lookup"><span data-stu-id="913c1-218">Your app and all other resources created in this tutorial are now deleted from Azure.</span></span>

### <a name="next-steps"></a><span data-ttu-id="913c1-219">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="913c1-219">Next steps</span></span>

* <xref:host-and-deploy/azure-apps/azure-continuous-deployment>

## <a name="additional-resources"></a><span data-ttu-id="913c1-220">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="913c1-220">Additional resources</span></span>

* <span data-ttu-id="913c1-221">Pour Visual Studio Code, consultez [Profils de publication](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles).</span><span class="sxs-lookup"><span data-stu-id="913c1-221">For Visual Studio Code, see [Publish profiles](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles).</span></span>
* [<span data-ttu-id="913c1-222">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="913c1-222">Azure App Service</span></span>](/azure/app-service/app-service-web-overview)
* [<span data-ttu-id="913c1-223">Groupes de ressources Azure</span><span class="sxs-lookup"><span data-stu-id="913c1-223">Azure resource groups</span></span>](/azure/azure-resource-manager/resource-group-overview#resource-groups)
* [<span data-ttu-id="913c1-224">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="913c1-224">Azure SQL Database</span></span>](/azure/sql-database/)
* <xref:host-and-deploy/visual-studio-publish-profiles>
* <xref:test/troubleshoot-azure-iis>
