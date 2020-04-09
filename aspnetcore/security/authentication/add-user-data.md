---
title: Ajouter, télécharger et supprimer les données utilisateur à Identity dans le cadre d’un projet ASP.NET Core
author: rick-anderson
description: Découvrez comment ajouter des données utilisateur personnalisées à Identity dans le cadre d’un projet ASP.NET Core. Supprimer les données par GDPR.
ms.author: riande
ms.date: 03/26/2020
ms.custom: mvc, seodec18
uid: security/authentication/add-user-data
ms.openlocfilehash: 76b83df22381429feab80056c36dbdac1e5f20c7
ms.sourcegitcommit: 1d8f1396ccc66a0c3fcb5e5f36ea29b50db6d92a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80501228"
---
# <a name="add-download-and-delete-custom-user-data-to-identity-in-an-aspnet-core-project"></a>Ajouter, télécharger et supprimer les données personnalisées de l’utilisateur à Identity dans le cadre d’un projet ASP.NET Core

Par [Rick Anderson](https://twitter.com/RickAndMSFT)

Cet article montre comment :

* Ajoutez des données utilisateur personnalisées à une application web ASP.NET Core.
* Marquez le modèle de <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute> données utilisateur personnalisé avec l’attribut de sorte qu’il est automatiquement disponible pour le téléchargement et la suppression. Rendre les données capables d’être téléchargées et supprimées permet de répondre aux exigences [du GDPR.](xref:security/gdpr)

L’échantillon de projet est créé à partir d’une application Web Razor Pages, mais les instructions sont similaires pour une application web ASP.NET Core MVC.

[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data) ([comment télécharger](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Prérequis

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [](~/includes/3.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [](~/includes/2.2-SDK.md)]

::: moniker-end

## <a name="create-a-razor-web-app"></a>Créer une application web Razor

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

::: moniker range=">= aspnetcore-3.0"

* Dans Visual Studio, dans le menu **Fichier**, sélectionnez **Nouveau** > **Projet**. Nommez le projet **WebApp1** si vous le souhaitez correspondre à l’espace nom du code [d’échantillon de téléchargement.](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data)
* Sélectionnez **ASP.NET’application** > Web de **base OK**
* Sélectionnez **ASP.NET Core 3.0** dans le dropdown
* Sélectionnez **Web Application** > **OK**
* Générez et exécutez le projet.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* Dans Visual Studio, dans le menu **Fichier**, sélectionnez **Nouveau** > **Projet**. Nommez le projet **WebApp1** si vous le souhaitez correspondre à l’espace nom du code [d’échantillon de téléchargement.](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data)
* Sélectionnez **ASP.NET’application** > Web de **base OK**
* Sélectionnez **ASP.NET Core 2.2** dans le dropdown
* Sélectionnez **Web Application** > **OK**
* Générez et exécutez le projet.

::: moniker-end


# <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli)

```dotnetcli
dotnet new webapp -o WebApp1
```

---

## <a name="run-the-identity-scaffolder"></a>Exécuter l’échafaudage d’identité

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* De **Solution Explorer**, cliquez à droite sur le projet > **Ajouter** > **un nouvel article échafaudé**.
* De la vitre gauche du dialogue **Ajouter échafaudage,** sélectionnez **Identity** > **Add**.
* Dans le dialogue **Add Identity,** les options suivantes :
  * Sélectionnez le fichier de mise en page existant */Pages/Shared/_Layout.cshtml*
  * Sélectionnez les fichiers suivants pour remplacer :
    * **Compte/enregistrement**
    * **Compte/Gestion/Index**
  * Sélectionnez **+** le bouton pour créer une nouvelle **classe de contexte de données**. Acceptez le type **(WebApp1.Models.WebApp1Context** si le projet est nommé **WebApp1**).
  * Sélectionnez **+** le bouton pour créer une nouvelle **classe d’utilisateurs**. Acceptez le type (**WebApp1User** si le projet est nommé **WebApp1**) > **Ajouter**.
* Sélectionnez **Ajouter**.

# <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli)

Si vous n’avez pas déjà installé l’échafaudage ASP.NET Core, installez-le dès maintenant :

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

Ajoutez une référence de paquet à [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) au fichier projet (.csproj). Exécuter la commande suivante dans l’annuaire du projet:

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

Exécutez la commande suivante pour énumérer les options d’échafaudage d’identité :

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

Dans le dossier du projet, exécutez l’échafaudage Identity :

```dotnetcli
dotnet aspnet-codegenerator identity -u WebApp1User -fi Account.Register;Account.Manage.Index
```

---

Suivez l’instruction dans [Migrations, UseAuthentication et layout](xref:security/authentication/scaffold-identity#efm) pour effectuer les étapes suivantes :

* Créez une migration et mettez à jour la base de données.
* Ajout de `UseAuthentication` à `Startup.Configure`.
* Ajouter `<partial name="_LoginPartial" />` au fichier de mise en page.
* Testez l’application :
  * Inscrire un utilisateur
  * Sélectionnez le nouveau nom d’utilisateur (à côté du lien **Logout).** Vous devrez peut-être élargir la fenêtre ou sélectionner l’icône de barre de navigation pour afficher le nom d’utilisateur et d’autres liens.
  * Sélectionnez l’onglet **Données personnelles.**
  * Sélectionnez le bouton **Télécharger** et examinez le fichier *PersonalData.json.*
  * Testez le bouton **Supprimer,** qui supprime l’enregistrement sur l’utilisateur.

## <a name="add-custom-user-data-to-the-identity-db"></a>Ajouter des données personnalisées aux données d’identité

Mettre `IdentityUser` à jour la classe dérivée avec des propriétés personnalisées. Si vous avez nommé le projet WebApp1, le fichier est nommé *Areas/Identity/Data/WebApp1User.cs*. Mettre à jour le fichier avec le code suivant :

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

Les propriétés avec l’attribut [PersonalData](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute) sont les :

* Supprimé lorsque les *appels Zones/Identité/Pages/Account/Manage/DeletePersonalData.cshtml* Razor Page `UserManager.Delete`.
* Inclus dans les données téléchargées par les *zones/identité/pages/Compte/Manage/DownloadPersonalData.cshtml* Razor Page.

### <a name="update-the-accountmanageindexcshtml-page"></a>Mettre à jour la page Compte/Gérer/Index.cshtml

Mettre `InputModel` à jour le code in *Areas/Identity/Pages/Account/Manage/Index.cshtml.cs* avec le code mis en évidence suivant :

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=24-32,48-49,96-104,106)]

Mettre à jour les *domaines/identité/pages/Compte/Gestion/Index.cshtml* avec la balisage mis en évidence ci-après :

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=18-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=28-36,63-64,98-106,119)]

Mettre à jour les *domaines/identité/pages/Compte/Gestion/Index.cshtml* avec la balisage mis en évidence ci-après :

[!code-cshtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=35-42)]

::: moniker-end

### <a name="update-the-accountregistercshtml-page"></a>Mettre à jour la page Compte/Register.cshtml

Mettre `InputModel` à jour le code in *Areas/Identity/Pages/Account/Register.cshtml.cs* avec le code mis en évidence suivant :

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=30-38,70-71)]

Mettre à jour les *zones/identité/pages/Compte/Register.cshtml* avec la balisage mis en évidence ci-après :

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=28-36,67,66)]

Mettre à jour les *zones/identité/pages/Compte/Register.cshtml* avec la balisage mis en évidence ci-après :

[!code-cshtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end


Créez le projet.

### <a name="add-a-migration-for-the-custom-user-data"></a>Ajouter une migration pour les données utilisateur personnalisées

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Dans la **console**Visual Studio Package Manager :

```powershell
Add-Migration CustomUserData
Update-Database
```

# <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CustomUserData
dotnet ef database update
```

---

## <a name="test-create-view-download-delete-custom-user-data"></a>Testez, visualiser, télécharger, supprimer les données personnalisées de l’utilisateur

Testez l’application :

* Enregistrez un nouvel utilisateur.
* Afficher les données personnalisées de l’utilisateur sur la `/Identity/Account/Manage` page.
* Téléchargez et visualisons `/Identity/Account/Manage/PersonalData` les données personnelles des utilisateurs à partir de la page.

## <a name="add-claims-to-identity-using-iuserclaimsprincipalfactoryapplicationuser"></a>Ajouter des revendications à Identity à l’aide d’IUserClaimsPrincipalFactory<ApplicationUser>

Des réclamations supplémentaires peuvent être ajoutées à `IUserClaimsPrincipalFactory<T>` ASP.NET’identité de base en utilisant l’interface. Cette classe peut être ajoutée `Startup.ConfigureServices` à l’application dans la méthode. Ajoutez la mise en œuvre personnalisée de la classe comme suit :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    services.AddScoped<IUserClaimsPrincipalFactory<ApplicationUser>, 
        AdditionalUserClaimsPrincipalFactory>();
```

Le code de `ApplicationUser` démonstration utilise la classe. Cette classe `IsAdmin` ajoute une propriété qui est utilisée pour ajouter la réclamation supplémentaire.

```csharp
public class ApplicationUser : IdentityUser
{
    public bool IsAdmin { get; set; }
}
```

`AdditionalUserClaimsPrincipalFactory` implémente l'interface `UserClaimsPrincipalFactory`. Une nouvelle revendication de `ClaimsPrincipal`rôle est ajoutée à la .

```csharp
public class AdditionalUserClaimsPrincipalFactory 
        : UserClaimsPrincipalFactory<ApplicationUser, IdentityRole>
{
    public AdditionalUserClaimsPrincipalFactory( 
        UserManager<ApplicationUser> userManager,
        RoleManager<IdentityRole> roleManager, 
        IOptions<IdentityOptions> optionsAccessor) 
        : base(userManager, roleManager, optionsAccessor)
    {}

    public async override Task<ClaimsPrincipal> CreateAsync(ApplicationUser user)
    {
        var principal = await base.CreateAsync(user);
        var identity = (ClaimsIdentity)principal.Identity;

        var claims = new List<Claim>();
        if (user.IsAdmin)
        {
            claims.Add(new Claim(JwtClaimTypes.Role, "admin"));
        }
        else
        {
            claims.Add(new Claim(JwtClaimTypes.Role, "user"));
        }

        identity.AddClaims(claims);
        return principal;
    }
}
```

La réclamation supplémentaire peut ensuite être utilisée dans l’application. Dans une page `IAuthorizationService` Razor, l’instance peut être utilisée pour accéder à la valeur de réclamation.

```cshtml
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService

@if ((await AuthorizationService.AuthorizeAsync(User, "IsAdmin")).Succeeded)
{
    <ul class="mr-auto navbar-nav">
        <li class="nav-item">
            <a class="nav-link" asp-controller="Admin" asp-action="Index">ADMIN</a>
        </li>
    </ul>
}
```
