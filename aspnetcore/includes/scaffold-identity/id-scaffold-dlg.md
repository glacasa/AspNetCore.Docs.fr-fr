Exécutez l’échafaudage d’identité :

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Dans **Explorateur de solutions**, cliquez avec le bouton droit sur le projet > **Ajouter**  >  **un nouvel élément de génération de modèles**automatique.
* Dans le volet gauche de la boîte de dialogue **Ajouter un nouvel élément de structure** , sélectionnez **identité**  >  **Ajouter**.
* Dans la boîte de dialogue **Ajouter une identité** , sélectionnez les options souhaitées.
  * Sélectionnez votre page de disposition existante, ou votre fichier de disposition sera remplacé par un balisage incorrect :
    * `~/Pages/Shared/_Layout.cshtml`pour Razor Pages
    * `~/Views/Shared/_Layout.cshtml`pour les projets MVC
    * Les applications serveur éblouissantes créées à partir du modèle de serveur éblouissant ( `blazorserver` ) ne sont pas configurées pour Razor pages ou MVC par défaut. Ne renseignez pas l’entrée page de disposition.
  * Sélectionnez le **+** bouton pour créer une **classe de contexte de données**. Acceptez la valeur par défaut ou spécifiez une classe (par exemple, `MyApplication.Data.ApplicationDbContext` ).
* Sélectionnez **Ajouter**.

# <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli)

Si vous n’avez pas déjà installé le générateur de modèles automatique ASP.NET Core, installez-le maintenant :

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

Ajoutez les références de package NuGet nécessaires au fichier projet (*. csproj*). Exécutez les commandes suivantes dans le répertoire du projet :

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

Exécutez la commande suivante pour répertorier les options d’identification de l’échafaudage :

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

[!INCLUDE[](~/includes/scaffoldTFM.md)]

Dans le dossier du projet, exécutez l’échafaudage d’identité avec les options souhaitées. Par exemple, pour configurer l’identité avec l’interface utilisateur par défaut et le nombre minimal de fichiers, exécutez la commande suivante :

```dotnetcli
dotnet aspnet-codegenerator identity --useDefaultUI
```

---
