---
title: Génération Identity de modèles automatique dans les projets ASP.net Core
author: rick-anderson
description: Découvrez comment générer une Identity structure dans un projet ASP.net core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/1/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/scaffold-identity
ms.openlocfilehash: 6f1ff69863e14c73e90496ea61188387f5267b19
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82768388"
---
# <a name="scaffold-identity-in-aspnet-core-projects"></a>Génération Identity de modèles automatique dans les projets ASP.net Core

Par [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

ASP.net Core fournit [ASP.net Core Identity ](xref:security/authentication/identity) sous forme de [ Razor bibliothèque de classes](xref:razor-pages/ui-class). Les applications qui Identity incluent peuvent appliquer l’échafaudage pour ajouter de manière sélective le code source contenu Identity Razor dans la bibliothèque de classes (RCL). Vous pouvez souhaiter générer le code source afin de pouvoir modifier le code et changer le comportement. Par exemple, vous pouvez demander au générateur de modèles automatique de générer le code utilisé dans l’inscription. Le code généré est prioritaire sur le même code dans Identity le RCL. Pour obtenir le contrôle total de l’interface utilisateur et ne pas utiliser le RCL par défaut, consultez la section [créer une source d’interface utilisateur d’identité complète](#full).

Les applications qui n’incluent **pas** l’authentification peuvent appliquer l’échafaudage pour ajouter Identity le package RCL. Vous avez la possibilité de sélectionner Identity du code à générer.

Bien que le générateur de modèles génère la majeure partie du code nécessaire, vous devez mettre à jour votre projet pour terminer le processus. Ce document explique les étapes nécessaires à la réalisation Identity d’une mise à jour de génération de modèles automatique.

Nous vous recommandons d’utiliser un système de contrôle de code source qui affiche les différences entre les fichiers et vous permet d’annuler les modifications. Inspectez les modifications après avoir Identity exécuté le générateur de modèles.

Les services sont requis lorsque vous utilisez l' [authentification à deux facteurs](xref:security/authentication/identity-enable-qrcodes), la [confirmation de compte et la récupération de mot de passe](xref:security/authentication/accconfirm), ainsi que d’autres fonctionnalités de sécurité avec Identity. Les services ou stubs de service ne sont Identitypas générés lors de la génération de modèles automatique. Les services pour activer ces fonctionnalités doivent être ajoutés manuellement. Par exemple, consultez [exiger une confirmation par courrier électronique](xref:security/authentication/accconfirm#require-email-confirmation).

Lors de la Identity génération de modèles automatique avec un nouveau contexte de données dans un projet avec des comptes individuels existants :

* Dans `Startup.ConfigureServices`, supprimez les appels à :
  * `AddDbContext`
  * `AddDefaultIdentity`

Par exemple, `AddDbContext` et `AddDefaultIdentity` sont commentés dans le code suivant :

[!code-csharp[](scaffold-identity/3.1sample/StartupRemove.cs?name=snippet)]

Le code précédent fait un commentaire sur le code qui est dupliqué dans *AreasIdentity//IdentityHostingStartup.cs*

En règle générale, les applications créées avec des comptes individuels ne doivent ***pas*** créer de contexte de données.

## <a name="scaffold-identity-into-an-empty-project"></a>Identité de l’échafaudage dans un projet vide

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Mettez à `Startup` jour la classe avec un code similaire à ce qui suit :

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a>Identité de l’échafaudage dans un Razor projet sans autorisation existante

<!--  Updated for 3.0
set projNam=RPnoAuth
set projType=webapp

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef database drop
dotnet ef migrations add CreateIdentitySchema0
dotnet ef database update
-->

<!-- ERROR
There is already an object named 'AspNetRoles' in the database.

Fixed via dotnet ef database drop
before dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Identityest configuré dans *AreasIdentity//IdentityHostingStartup.cs*. Pour plus d’informations, consultez [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a>Migrations, UseAuthentication et disposition

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a>Activer l’authentification

Mettez à `Startup` jour la classe avec un code similaire à ce qui suit :

[!code-csharp[](scaffold-identity/3.1sample/StartupRP.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a>Modifications de la disposition

Facultatif : ajoutez la connexion partielle (`_LoginPartial`) au fichier de disposition :

[!code-html[Main](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a>Identité de l’échafaudage dans un Razor projet avec autorisation

<!--
Use >=2.1: dotnet new webapp -au Individual -o RPauth
Use = 2.0: dotnet new razor -au Individual -o RPauth
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]
Certaines Identity options sont configurées dans *zonesIdentity//IdentityHostingStartup.cs*. Pour plus d’informations, consultez [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a>Identité de l’échafaudage dans un projet MVC sans autorisation existante

<!--
set projNam=MvcNoAuth
set projType=mvc
set version=2.1.0

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v %version%
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Facultatif : ajoutez la connexion partielle (`_LoginPartial`) au fichier *views/shared/_Layout. cshtml* :

[!code-html[Main](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

* Déplacez le fichier *pages/Shared/_LoginPartial. cshtml* vers *views/shared/_LoginPartial. cshtml*

Identityest configuré dans *AreasIdentity//IdentityHostingStartup.cs*. Pour plus d’informations, consultez IHostingStartup.

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

Mettez à `Startup` jour la classe avec un code similaire à ce qui suit :

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a>Identité de l’échafaudage dans un projet MVC avec autorisation

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a>Créer une source d’interface utilisateur d’identité complète

Pour conserver le contrôle total de Identity l’interface utilisateur, Identity exécutez le générateur de modèles automatique et sélectionnez **remplacer tous les fichiers**.

Le code en surbrillance suivant montre les modifications pour Identity remplacer l' Identity interface utilisateur par défaut par dans une application Web ASP.net Core 2,1. Vous pouvez le faire pour avoir le contrôle total de l' Identity interface utilisateur.

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

La valeur Identity par défaut est remplacée dans le code suivant :

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

Le code suivant définit les [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)et [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

Inscrire une `IEmailSender` implémentation, par exemple :

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a>Désactiver la page de Registre

Pour désactiver l’inscription des utilisateurs :

* Génération Identityde modèles automatique. Incluez Account. Register, Account. Login et Account. RegisterConfirmation. Par exemple :

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* Mettre à jour les *zones/Identity/pages/Account/Register.cshtml.cs* afin que les utilisateurs ne puissent pas s’inscrire depuis ce point de terminaison :

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* Mettez à jour les *zones/Identity/pages/Account/Register.cshtml* pour qu’ils soient cohérents avec les modifications précédentes :

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* Commentez ou supprimez le lien d’inscription de *Areas/Identity/pages/Account/login.cshtml*

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* Mettez à jour la page *zones/Identity/pages/Account/RegisterConfirmation* .

  * Supprimez le code et les liens du fichier cshtml.
  * Supprimez le code de confirmation `PageModel`du :

  ```csharp
   [AllowAnonymous]
    public class RegisterConfirmationModel : PageModel
    {
        public IActionResult OnGet()
        {  
            return Page();
        }
    }
  ```
  
### <a name="use-another-app-to-add-users"></a>Utiliser une autre application pour ajouter des utilisateurs

Fournissez un mécanisme pour ajouter des utilisateurs en dehors de l’application Web. Les options permettant d’ajouter des utilisateurs sont les suivantes :

* Une application Web d’administration dédiée.
* Application console.

Le code suivant décrit une approche de l’ajout d’utilisateurs :

* Une liste d’utilisateurs est lue en mémoire.
* Un mot de passe fort unique est généré pour chaque utilisateur.
* L’utilisateur est ajouté à la Identity base de données.
* L’utilisateur est averti et a demandé à modifier le mot de passe.

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

L’exemple de code suivant présente l’ajout d’un utilisateur :

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

Une approche similaire peut être suivie pour les scénarios de production.

## <a name="prevent-publish-of-static-identity-assets"></a>Empêcher la publication d' Identity actifs statiques

Pour empêcher la publication Identity des ressources statiques sur la racine <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>Web, consultez.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Modifications apportées au code d’authentification pour ASP.NET Core 2,1 et versions ultérieures](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.net Core 2,1 et versions ultérieures fournissent [ Identity ASP.net Core](xref:security/authentication/identity) sous forme de [ Razor bibliothèque de classes](xref:razor-pages/ui-class). Les applications qui Identity incluent peuvent appliquer l’échafaudage pour ajouter de manière sélective le code source contenu Identity Razor dans la bibliothèque de classes (RCL). Vous pouvez souhaiter générer le code source afin de pouvoir modifier le code et changer le comportement. Par exemple, vous pouvez demander au générateur de modèles automatique de générer le code utilisé dans l’inscription. Le code généré est prioritaire sur le même code dans Identity le RCL. Pour obtenir le contrôle total de l’interface utilisateur et ne pas utiliser le RCL par défaut, consultez la section [créer une source d’interface utilisateur d’identité complète](#full).

Les applications qui n’incluent **pas** l’authentification peuvent appliquer l’échafaudage pour ajouter Identity le package RCL. Vous avez la possibilité de sélectionner Identity du code à générer.

Bien que le générateur de modèles génère la plus grande partie du code nécessaire, vous devez mettre à jour votre projet pour terminer le processus. Ce document explique les étapes nécessaires à la réalisation Identity d’une mise à jour de génération de modèles automatique.

Lorsque l' Identity échafaudage est exécuté, un fichier *ScaffoldingReadme. txt* est créé dans le répertoire du projet. Le fichier *ScaffoldingReadme. txt* contient des instructions générales sur ce qui est nécessaire pour Identity effectuer la mise à jour de la génération de modèles automatique. Ce document contient des instructions plus complètes que le fichier *ScaffoldingReadme. txt* .

Nous vous recommandons d’utiliser un système de contrôle de code source qui affiche les différences entre les fichiers et vous permet d’annuler les modifications. Inspectez les modifications après avoir Identity exécuté le générateur de modèles.

> [!NOTE]
> Les services sont requis lorsque vous utilisez l' [authentification à deux facteurs](xref:security/authentication/identity-enable-qrcodes), la [confirmation de compte et la récupération de mot de passe](xref:security/authentication/accconfirm), ainsi que d’autres fonctionnalités de sécurité avec Identity. Les services ou stubs de service ne sont Identitypas générés lors de la génération de modèles automatique. Les services pour activer ces fonctionnalités doivent être ajoutés manuellement. Par exemple, consultez [exiger une confirmation par courrier électronique](xref:security/authentication/accconfirm#require-email-confirmation).

## <a name="scaffold-identity-into-an-empty-project"></a>Identité de l’échafaudage dans un projet vide

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Ajoutez les appels en surbrillance `Startup` suivants à la classe :

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a>Identité de l’échafaudage dans un Razor projet sans autorisation existante

<!--  Updated for 3.0
set projNam=RPnoAuth
set projType=webapp

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Identityest configuré dans *AreasIdentity//IdentityHostingStartup.cs*. Pour plus d’informations, consultez [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a>Migrations, UseAuthentication et disposition

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a>Activer l’authentification

Dans la `Configure` méthode de la `Startup` classe, appelez [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) après `UseStaticFiles`:

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a>Modifications de la disposition

Facultatif : ajoutez la connexion partielle (`_LoginPartial`) au fichier de disposition :

[!code-html[Main](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a>Identité de l’échafaudage dans un Razor projet avec autorisation

<!--
Use >=2.1: dotnet new webapp -au Individual -o RPauth
Use = 2.0: dotnet new razor -au Individual -o RPauth
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]
Certaines Identity options sont configurées dans *zonesIdentity//IdentityHostingStartup.cs*. Pour plus d’informations, consultez [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a>Identité de l’échafaudage dans un projet MVC sans autorisation existante

<!--
set projNam=MvcNoAuth
set projType=mvc
set version=2.1.0

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v %version%
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Facultatif : ajoutez la connexion partielle (`_LoginPartial`) au fichier *views/shared/_Layout. cshtml* :

[!code-html[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* Déplacez le fichier *pages/Shared/_LoginPartial. cshtml* vers *views/shared/_LoginPartial. cshtml*

Identityest configuré dans *AreasIdentity//IdentityHostingStartup.cs*. Pour plus d’informations, consultez IHostingStartup.

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

Appeler [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) après `UseStaticFiles`:

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a>Identité de l’échafaudage dans un projet MVC avec autorisation

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

Supprimez le dossier *pages/Shared* et les fichiers de ce dossier.

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a>Créer une source d’interface utilisateur d’identité complète

Pour conserver le contrôle total de Identity l’interface utilisateur, Identity exécutez le générateur de modèles automatique et sélectionnez **remplacer tous les fichiers**.

Le code en surbrillance suivant montre les modifications pour Identity remplacer l' Identity interface utilisateur par défaut par dans une application Web ASP.net Core 2,1. Vous pouvez le faire pour avoir le contrôle total de l' Identity interface utilisateur.

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

La valeur Identity par défaut est remplacée dans le code suivant :

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

Le code suivant définit les [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)et [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

Inscrire une `IEmailSender` implémentation, par exemple :

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a>Désactiver la page de Registre

Pour désactiver l’inscription des utilisateurs :

* Génération Identityde modèles automatique. Incluez Account. Register, Account. Login et Account. RegisterConfirmation. Par exemple :

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* Mettre à jour les *zones/Identity/pages/Account/Register.cshtml.cs* afin que les utilisateurs ne puissent pas s’inscrire depuis ce point de terminaison :

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* Mettez à jour les *zones/Identity/pages/Account/Register.cshtml* pour qu’ils soient cohérents avec les modifications précédentes :

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* Commentez ou supprimez le lien d’inscription de *Areas/Identity/pages/Account/login.cshtml*

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* Mettez à jour la page *zones/Identity/pages/Account/RegisterConfirmation* .

  * Supprimez le code et les liens du fichier cshtml.
  * Supprimez le code de confirmation `PageModel`du :

  ```csharp
   [AllowAnonymous]
    public class RegisterConfirmationModel : PageModel
    {
        public IActionResult OnGet()
        {  
            return Page();
        }
    }
  ```
  
### <a name="use-another-app-to-add-users"></a>Utiliser une autre application pour ajouter des utilisateurs

Fournissez un mécanisme pour ajouter des utilisateurs en dehors de l’application Web. Les options permettant d’ajouter des utilisateurs sont les suivantes :

* Une application Web d’administration dédiée.
* Application console.

Le code suivant décrit une approche de l’ajout d’utilisateurs :

* Une liste d’utilisateurs est lue en mémoire.
* Un mot de passe fort unique est généré pour chaque utilisateur.
* L’utilisateur est ajouté à la Identity base de données.
* L’utilisateur est averti et a demandé à modifier le mot de passe.

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

L’exemple de code suivant présente l’ajout d’un utilisateur :

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

Une approche similaire peut être suivie pour les scénarios de production.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Modifications apportées au code d’authentification pour ASP.NET Core 2,1 et versions ultérieures](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end