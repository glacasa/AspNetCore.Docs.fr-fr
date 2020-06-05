<span data-ttu-id="7744d-101">Exécutez l’échafaudage d’identité :</span><span class="sxs-lookup"><span data-stu-id="7744d-101">Run the Identity scaffolder:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7744d-102">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7744d-102">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7744d-103">Dans **Explorateur de solutions**, cliquez avec le bouton droit sur le projet > **Ajouter**  >  **un nouvel élément de génération de modèles**automatique.</span><span class="sxs-lookup"><span data-stu-id="7744d-103">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="7744d-104">Dans le volet gauche de la boîte de dialogue **Ajouter un nouvel élément de structure** , sélectionnez **identité**  >  **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="7744d-104">From the left pane of the **Add New Scaffolded Item** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="7744d-105">Dans la boîte de dialogue **Ajouter une identité** , sélectionnez les options souhaitées.</span><span class="sxs-lookup"><span data-stu-id="7744d-105">In the **Add Identity** dialog, select the options you want.</span></span>
  * <span data-ttu-id="7744d-106">Sélectionnez votre page de disposition existante, ou votre fichier de disposition sera remplacé par un balisage incorrect :</span><span class="sxs-lookup"><span data-stu-id="7744d-106">Select your existing layout page, or your layout file will be overwritten with incorrect markup:</span></span>
    * <span data-ttu-id="7744d-107">`~/Pages/Shared/_Layout.cshtml`pour Razor Pages</span><span class="sxs-lookup"><span data-stu-id="7744d-107">`~/Pages/Shared/_Layout.cshtml` for Razor Pages</span></span>
    * <span data-ttu-id="7744d-108">`~/Views/Shared/_Layout.cshtml`pour les projets MVC</span><span class="sxs-lookup"><span data-stu-id="7744d-108">`~/Views/Shared/_Layout.cshtml` for MVC projects</span></span>
    * <span data-ttu-id="7744d-109">Les applications serveur éblouissantes créées à partir du modèle de serveur éblouissant ( `blazorserver` ) ne sont pas configurées pour Razor pages ou MVC par défaut.</span><span class="sxs-lookup"><span data-stu-id="7744d-109">Blazor Server apps created from the Blazor Server template (`blazorserver`) aren't configured for Razor Pages or MVC by default.</span></span> <span data-ttu-id="7744d-110">Ne renseignez pas l’entrée page de disposition.</span><span class="sxs-lookup"><span data-stu-id="7744d-110">Leave the layout page entry blank.</span></span>
  * <span data-ttu-id="7744d-111">Sélectionnez le **+** bouton pour créer une **classe de contexte de données**.</span><span class="sxs-lookup"><span data-stu-id="7744d-111">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="7744d-112">Acceptez la valeur par défaut ou spécifiez une classe (par exemple, `MyApplication.Data.ApplicationDbContext` ).</span><span class="sxs-lookup"><span data-stu-id="7744d-112">Accept the default value or specify a class (for example, `MyApplication.Data.ApplicationDbContext`).</span></span>
* <span data-ttu-id="7744d-113">Sélectionnez **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="7744d-113">Select **Add**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="7744d-114">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="7744d-114">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="7744d-115">Si vous n’avez pas déjà installé le générateur de modèles automatique ASP.NET Core, installez-le maintenant :</span><span class="sxs-lookup"><span data-stu-id="7744d-115">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="7744d-116">Ajoutez les références de package NuGet nécessaires au fichier projet (*. csproj*).</span><span class="sxs-lookup"><span data-stu-id="7744d-116">Add required NuGet package references to the project file (*.csproj*).</span></span> <span data-ttu-id="7744d-117">Exécutez les commandes suivantes dans le répertoire du projet :</span><span class="sxs-lookup"><span data-stu-id="7744d-117">Run the following commands in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

<span data-ttu-id="7744d-118">Exécutez la commande suivante pour répertorier les options d’identification de l’échafaudage :</span><span class="sxs-lookup"><span data-stu-id="7744d-118">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

[!INCLUDE[](~/includes/scaffoldTFM.md)]

<span data-ttu-id="7744d-119">Dans le dossier du projet, exécutez l’échafaudage d’identité avec les options souhaitées.</span><span class="sxs-lookup"><span data-stu-id="7744d-119">In the project folder, run the Identity scaffolder with the options you want.</span></span> <span data-ttu-id="7744d-120">Par exemple, pour configurer l’identité avec l’interface utilisateur par défaut et le nombre minimal de fichiers, exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="7744d-120">For example, to setup identity with the default UI and the minimum number of files, run the following command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity --useDefaultUI
```

---
