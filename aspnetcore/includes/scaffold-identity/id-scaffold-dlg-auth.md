::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a5186-101">Exécutez l’échafaudage d’identité :</span><span class="sxs-lookup"><span data-stu-id="a5186-101">Run the Identity scaffolder:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a5186-102">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a5186-102">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a5186-103">Dans **Explorateur de solutions**, cliquez avec le bouton droit sur le projet > **Ajouter** > **un nouvel élément de génération de modèles**automatique.</span><span class="sxs-lookup"><span data-stu-id="a5186-103">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="a5186-104">Dans le volet gauche de la boîte de dialogue **Ajouter une structure** , sélectionnez **identité** > **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="a5186-104">From the left pane of the **Add Scaffold** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="a5186-105">Dans la boîte de dialogue **Ajouter une identité** , sélectionnez les options souhaitées.</span><span class="sxs-lookup"><span data-stu-id="a5186-105">In the **Add Identity** dialog, select the options you want.</span></span>
  * <span data-ttu-id="a5186-106">Sélectionnez votre page de disposition existante afin que votre fichier de disposition ne soit pas remplacé par un balisage incorrect.</span><span class="sxs-lookup"><span data-stu-id="a5186-106">Select your existing layout page so your layout file isn't overwritten with incorrect markup.</span></span> <span data-ttu-id="a5186-107">Lorsqu’un fichier \* \_ Layout. cshtml\* existant est sélectionné, il n’est **pas** remplacé.</span><span class="sxs-lookup"><span data-stu-id="a5186-107">When an existing *\_Layout.cshtml* file is selected, it is **not** overwritten.</span></span> <span data-ttu-id="a5186-108">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="a5186-108">For example:</span></span>
    * <span data-ttu-id="a5186-109">`~/Pages/Shared/_Layout.cshtml`pour les projets de serveur Razor Pages ou éblouissants avec l’infrastructure Razor Pages existante</span><span class="sxs-lookup"><span data-stu-id="a5186-109">`~/Pages/Shared/_Layout.cshtml` for Razor Pages or Blazor Server projects with existing Razor Pages infrastructure</span></span>
    * <span data-ttu-id="a5186-110">`~/Views/Shared/_Layout.cshtml`pour les projets MVC ou les projets serveur éblouissants avec une infrastructure MVC existante</span><span class="sxs-lookup"><span data-stu-id="a5186-110">`~/Views/Shared/_Layout.cshtml` for MVC projects or Blazor Server projects with existing MVC infrastructure</span></span>
* <span data-ttu-id="a5186-111">Pour utiliser le contexte de données existant, sélectionnez au moins un fichier à substituer.</span><span class="sxs-lookup"><span data-stu-id="a5186-111">To use your existing data context, select at least one file to override.</span></span> <span data-ttu-id="a5186-112">Vous devez sélectionner au moins un fichier pour ajouter votre contexte de données.</span><span class="sxs-lookup"><span data-stu-id="a5186-112">You must select at least one file to add your data context.</span></span>
  * <span data-ttu-id="a5186-113">Sélectionnez votre classe de contexte de données.</span><span class="sxs-lookup"><span data-stu-id="a5186-113">Select your data context class.</span></span>
  * <span data-ttu-id="a5186-114">Sélectionnez **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="a5186-114">Select **Add**.</span></span>
* <span data-ttu-id="a5186-115">Pour créer un nouveau contexte utilisateur et éventuellement créer une classe d’utilisateur personnalisée pour l’identité :</span><span class="sxs-lookup"><span data-stu-id="a5186-115">To create a new user context and possibly create a custom user class for Identity:</span></span>
  * <span data-ttu-id="a5186-116">Sélectionnez le **+** bouton pour créer une **classe de contexte de données**.</span><span class="sxs-lookup"><span data-stu-id="a5186-116">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="a5186-117">Acceptez la valeur par défaut ou spécifiez une classe (par exemple, `MyApplication.Data.ApplicationDbContext` ).</span><span class="sxs-lookup"><span data-stu-id="a5186-117">Accept the default value or specify a class (for example, `MyApplication.Data.ApplicationDbContext`).</span></span>
  * <span data-ttu-id="a5186-118">Sélectionnez **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="a5186-118">Select **Add**.</span></span>

<span data-ttu-id="a5186-119">Remarque : Si vous créez un nouveau contexte utilisateur, vous n’êtes pas obligé de sélectionner un fichier à remplacer.</span><span class="sxs-lookup"><span data-stu-id="a5186-119">Note: If you're creating a new user context, you don't have to select a file to override.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="a5186-120">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="a5186-120">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="a5186-121">Si vous n’avez pas déjà installé le générateur de modèles automatique ASP.NET Core, installez-le maintenant :</span><span class="sxs-lookup"><span data-stu-id="a5186-121">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="a5186-122">Ajoutez les références de package NuGet nécessaires au fichier projet (*. csproj*).</span><span class="sxs-lookup"><span data-stu-id="a5186-122">Add required NuGet package references to the project file (*.csproj*).</span></span> <span data-ttu-id="a5186-123">Exécutez les commandes suivantes dans le répertoire du projet :</span><span class="sxs-lookup"><span data-stu-id="a5186-123">Run the following commands in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

<span data-ttu-id="a5186-124">Exécutez la commande suivante pour répertorier les options d’identification de l’échafaudage :</span><span class="sxs-lookup"><span data-stu-id="a5186-124">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

[!INCLUDE[](~/includes/scaffoldTFM.md)]

<span data-ttu-id="a5186-125">Dans le dossier du projet, exécutez l’échafaudage d’identité avec les options souhaitées.</span><span class="sxs-lookup"><span data-stu-id="a5186-125">In the project folder, run the Identity scaffolder with the options you want.</span></span> <span data-ttu-id="a5186-126">Par exemple, pour configurer l’identité avec l’interface utilisateur par défaut et le nombre minimal de fichiers, exécutez la commande suivante.</span><span class="sxs-lookup"><span data-stu-id="a5186-126">For example, to setup identity with the default UI and the minimum number of files, run the following command.</span></span> <span data-ttu-id="a5186-127">Utilisez le nom complet correct pour votre contexte de base de connaissances :</span><span class="sxs-lookup"><span data-stu-id="a5186-127">Use the correct fully qualified name for your DB context:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login"
```

<span data-ttu-id="a5186-128">PowerShell utilise un point-virgule comme séparateur de commande.</span><span class="sxs-lookup"><span data-stu-id="a5186-128">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="a5186-129">Quand vous utilisez PowerShell, échapper les points-virgules dans la liste de fichiers ou placer la liste de fichiers entre guillemets doubles.</span><span class="sxs-lookup"><span data-stu-id="a5186-129">When using PowerShell, escape the semi-colons in the file list or put the file list in double quotes.</span></span> <span data-ttu-id="a5186-130">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="a5186-130">For example:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="a5186-131">Si vous exécutez l’échafaudage d’identité sans spécifier l' `--files` indicateur ou l' `--useDefaultUI` indicateur, toutes les pages d’interface utilisateur d’identité disponibles seront créées dans votre projet.</span><span class="sxs-lookup"><span data-stu-id="a5186-131">If you run the Identity scaffolder without specifying the `--files` flag or the `--useDefaultUI` flag, all the available Identity UI pages will be created in your project.</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a5186-132">Exécutez l’échafaudage d’identité :</span><span class="sxs-lookup"><span data-stu-id="a5186-132">Run the Identity scaffolder:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a5186-133">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a5186-133">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a5186-134">Dans **Explorateur de solutions**, cliquez avec le bouton droit sur le projet > **Ajouter** > **un nouvel élément de génération de modèles**automatique.</span><span class="sxs-lookup"><span data-stu-id="a5186-134">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="a5186-135">Dans le volet gauche de la boîte de dialogue **Ajouter une structure** , sélectionnez **identité** > **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="a5186-135">From the left pane of the **Add Scaffold** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="a5186-136">Dans la boîte de dialogue **Ajouter une identité** , sélectionnez les options souhaitées.</span><span class="sxs-lookup"><span data-stu-id="a5186-136">In the **Add Identity** dialog, select the options you want.</span></span>
  * <span data-ttu-id="a5186-137">Sélectionnez votre page de disposition existante, ou votre fichier de disposition sera remplacé par un balisage incorrect.</span><span class="sxs-lookup"><span data-stu-id="a5186-137">Select your existing layout page, or your layout file will be overwritten with incorrect markup.</span></span> <span data-ttu-id="a5186-138">Lorsqu’un fichier \* \_ Layout. cshtml\* existant est sélectionné, il n’est **pas** remplacé.</span><span class="sxs-lookup"><span data-stu-id="a5186-138">When an existing *\_Layout.cshtml* file is selected, it is **not** overwritten.</span></span> <span data-ttu-id="a5186-139">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="a5186-139">For example:</span></span>
    * <span data-ttu-id="a5186-140">`~/Pages/Shared/_Layout.cshtml`pour Razor Pages</span><span class="sxs-lookup"><span data-stu-id="a5186-140">`~/Pages/Shared/_Layout.cshtml` for Razor Pages</span></span>
    * <span data-ttu-id="a5186-141">`~/Views/Shared/_Layout.cshtml`pour les projets MVC</span><span class="sxs-lookup"><span data-stu-id="a5186-141">`~/Views/Shared/_Layout.cshtml` for MVC projects</span></span>
* <span data-ttu-id="a5186-142">Pour utiliser le contexte de données existant, sélectionnez au moins un fichier à substituer.</span><span class="sxs-lookup"><span data-stu-id="a5186-142">To use your existing data context, select at least one file to override.</span></span> <span data-ttu-id="a5186-143">Vous devez sélectionner au moins un fichier pour ajouter votre contexte de données.</span><span class="sxs-lookup"><span data-stu-id="a5186-143">You must select at least one file to add your data context.</span></span>
  * <span data-ttu-id="a5186-144">Sélectionnez votre classe de contexte de données.</span><span class="sxs-lookup"><span data-stu-id="a5186-144">Select your data context class.</span></span>
  * <span data-ttu-id="a5186-145">Sélectionnez **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="a5186-145">Select **Add**.</span></span>
* <span data-ttu-id="a5186-146">Pour créer un nouveau contexte utilisateur et éventuellement créer une classe d’utilisateur personnalisée pour l’identité :</span><span class="sxs-lookup"><span data-stu-id="a5186-146">To create a new user context and possibly create a custom user class for Identity:</span></span>
  * <span data-ttu-id="a5186-147">Sélectionnez le **+** bouton pour créer une **classe de contexte de données**.</span><span class="sxs-lookup"><span data-stu-id="a5186-147">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="a5186-148">Acceptez la valeur par défaut ou spécifiez une classe (par exemple, `MyApplication.Data.ApplicationDbContext` ).</span><span class="sxs-lookup"><span data-stu-id="a5186-148">Accept the default value or specify a class (for example, `MyApplication.Data.ApplicationDbContext`).</span></span>
  * <span data-ttu-id="a5186-149">Sélectionnez **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="a5186-149">Select **Add**.</span></span>

<span data-ttu-id="a5186-150">Remarque : Si vous créez un nouveau contexte utilisateur, vous n’êtes pas obligé de sélectionner un fichier à remplacer.</span><span class="sxs-lookup"><span data-stu-id="a5186-150">Note: If you're creating a new user context, you don't have to select a file to override.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="a5186-151">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="a5186-151">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="a5186-152">Si vous n’avez pas déjà installé le générateur de modèles automatique ASP.NET Core, installez-le maintenant :</span><span class="sxs-lookup"><span data-stu-id="a5186-152">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="a5186-153">Ajoutez une référence de package à [Microsoft. VisualStudio. Web. CodeGeneration. Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) dans le fichier projet (*. csproj*).</span><span class="sxs-lookup"><span data-stu-id="a5186-153">Add a package reference to [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) to the project file (*.csproj*).</span></span> <span data-ttu-id="a5186-154">Exécutez les commandes suivantes dans le répertoire du projet :</span><span class="sxs-lookup"><span data-stu-id="a5186-154">Run the following commands in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

<span data-ttu-id="a5186-155">Exécutez la commande suivante pour répertorier les options d’identification de l’échafaudage :</span><span class="sxs-lookup"><span data-stu-id="a5186-155">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

<span data-ttu-id="a5186-156">Dans le dossier du projet, exécutez l’échafaudage d’identité avec les options souhaitées.</span><span class="sxs-lookup"><span data-stu-id="a5186-156">In the project folder, run the Identity scaffolder with the options you want.</span></span> <span data-ttu-id="a5186-157">Par exemple, pour configurer l’identité avec l’interface utilisateur par défaut et le nombre minimal de fichiers, exécutez la commande suivante.</span><span class="sxs-lookup"><span data-stu-id="a5186-157">For example, to setup identity with the default UI and the minimum number of files, run the following command.</span></span> <span data-ttu-id="a5186-158">Utilisez le nom complet correct pour votre contexte de base de connaissances :</span><span class="sxs-lookup"><span data-stu-id="a5186-158">Use the correct fully qualified name for your DB context:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login"
```

<span data-ttu-id="a5186-159">PowerShell utilise un point-virgule comme séparateur de commande.</span><span class="sxs-lookup"><span data-stu-id="a5186-159">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="a5186-160">Quand vous utilisez PowerShell, échapper les points-virgules dans la liste de fichiers ou placer la liste de fichiers entre guillemets doubles.</span><span class="sxs-lookup"><span data-stu-id="a5186-160">When using PowerShell, escape the semi-colons in the file list or put the file list in double quotes.</span></span> <span data-ttu-id="a5186-161">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="a5186-161">For example:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="a5186-162">Si vous exécutez l’échafaudage d’identité sans spécifier l' `--files` indicateur ou l' `--useDefaultUI` indicateur, toutes les pages d’interface utilisateur d’identité disponibles seront créées dans votre projet.</span><span class="sxs-lookup"><span data-stu-id="a5186-162">If you run the Identity scaffolder without specifying the `--files` flag or the `--useDefaultUI` flag, all the available Identity UI pages will be created in your project.</span></span>

---

::: moniker-end
