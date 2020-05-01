---
title: Identité de l’échafaudage dans les projets ASP.NET Core
author: rick-anderson
description: Découvrez comment générer une structure d’identité dans un projet ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/1/2020
uid: security/authentication/scaffold-identity
ms.openlocfilehash: ac95035b114274ddaa6ccb0b5b6e3da98885e39e
ms.sourcegitcommit: 6318d2bdd63116e178c34492a904be85ec9ac108
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2020
ms.locfileid: "82604725"
---
# <a name="scaffold-identity-in-aspnet-core-projects"></a><span data-ttu-id="a9116-103">Identité de l’échafaudage dans les projets ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a9116-103">Scaffold Identity in ASP.NET Core projects</span></span>

<span data-ttu-id="a9116-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="a9116-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a9116-105">ASP.NET Core fournit [ASP.net Core identité](xref:security/authentication/identity) sous forme de [bibliothèque de classes Razor](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="a9116-105">ASP.NET Core provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="a9116-106">Les applications qui incluent une identité peuvent appliquer l’échafaudage pour ajouter de manière sélective le code source contenu dans la bibliothèque de classes Razor d’identité (RCL).</span><span class="sxs-lookup"><span data-stu-id="a9116-106">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="a9116-107">Vous pouvez souhaiter générer le code source afin de pouvoir modifier le code et changer le comportement.</span><span class="sxs-lookup"><span data-stu-id="a9116-107">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="a9116-108">Par exemple, vous pouvez demander au générateur de modèles automatique de générer le code utilisé dans l’inscription.</span><span class="sxs-lookup"><span data-stu-id="a9116-108">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="a9116-109">Le code généré est prioritaire sur le même code dans la bibliothèque de classes Razor d’identité.</span><span class="sxs-lookup"><span data-stu-id="a9116-109">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="a9116-110">Pour obtenir le contrôle total de l’interface utilisateur et ne pas utiliser le RCL par défaut, consultez la section [créer une source d’interface utilisateur d’identité complète](#full).</span><span class="sxs-lookup"><span data-stu-id="a9116-110">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="a9116-111">Les applications qui n’incluent **pas** l’authentification peuvent appliquer l’échafaudage pour ajouter le package d’identité RCL.</span><span class="sxs-lookup"><span data-stu-id="a9116-111">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="a9116-112">Vous pouvez sélectionner le code d’identité à générer.</span><span class="sxs-lookup"><span data-stu-id="a9116-112">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="a9116-113">Bien que le générateur de modèles génère la majeure partie du code nécessaire, vous devez mettre à jour votre projet pour terminer le processus.</span><span class="sxs-lookup"><span data-stu-id="a9116-113">Although the scaffolder generates most of the necessary code, you need to update your project to complete the process.</span></span> <span data-ttu-id="a9116-114">Ce document explique les étapes nécessaires pour effectuer une mise à jour de l’échafaudage d’identité.</span><span class="sxs-lookup"><span data-stu-id="a9116-114">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="a9116-115">Nous vous recommandons d’utiliser un système de contrôle de code source qui affiche les différences entre les fichiers et vous permet d’annuler les modifications.</span><span class="sxs-lookup"><span data-stu-id="a9116-115">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="a9116-116">Inspectez les modifications après avoir exécuté l’échafaudage d’identité.</span><span class="sxs-lookup"><span data-stu-id="a9116-116">Inspect the changes after running the Identity scaffolder.</span></span>

<span data-ttu-id="a9116-117">Les services sont requis lors de l’utilisation de [l’authentification à deux facteurs](xref:security/authentication/identity-enable-qrcodes), la [confirmation de compte et la récupération de mot de passe](xref:security/authentication/accconfirm), ainsi que d’autres fonctionnalités de sécurité avec identité.</span><span class="sxs-lookup"><span data-stu-id="a9116-117">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="a9116-118">Les services ou stubs de service ne sont pas générés lors de la génération de modèles automatique d’identité.</span><span class="sxs-lookup"><span data-stu-id="a9116-118">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="a9116-119">Les services pour activer ces fonctionnalités doivent être ajoutés manuellement.</span><span class="sxs-lookup"><span data-stu-id="a9116-119">Services to enable these features must be added manually.</span></span> <span data-ttu-id="a9116-120">Par exemple, consultez [exiger une confirmation par courrier électronique](xref:security/authentication/accconfirm#require-email-confirmation).</span><span class="sxs-lookup"><span data-stu-id="a9116-120">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

<span data-ttu-id="a9116-121">Lors de la génération de modèles automatique d’identité avec un nouveau contexte de données dans un projet avec des comptes individuels existants :</span><span class="sxs-lookup"><span data-stu-id="a9116-121">When scaffolding Identity with a new data context into a project with existing individual accounts:</span></span>

* <span data-ttu-id="a9116-122">Dans `Startup.ConfigureServices`, supprimez les appels à :</span><span class="sxs-lookup"><span data-stu-id="a9116-122">In `Startup.ConfigureServices`, remove the calls to:</span></span>
  * `AddDbContext`
  * `AddDefaultIdentity`

<span data-ttu-id="a9116-123">Par exemple, `AddDbContext` et `AddDefaultIdentity` sont commentés dans le code suivant :</span><span class="sxs-lookup"><span data-stu-id="a9116-123">For example, `AddDbContext` and `AddDefaultIdentity` are commented out in the following code:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRemove.cs?name=snippet)]

<span data-ttu-id="a9116-124">Le code précédent fait un commentaire sur le code qui est dupliqué dans *Areas/Identity/IdentityHostingStartup. cs*</span><span class="sxs-lookup"><span data-stu-id="a9116-124">The preceeding code comments out the code that is duplicated in *Areas/Identity/IdentityHostingStartup.cs*</span></span>

<span data-ttu-id="a9116-125">En règle générale, les applications créées avec des comptes individuels ne doivent ***pas*** créer de contexte de données.</span><span class="sxs-lookup"><span data-stu-id="a9116-125">Typically, apps that were created with individual accounts should ***not*** create a new data context.</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="a9116-126">Identité de l’échafaudage dans un projet vide</span><span class="sxs-lookup"><span data-stu-id="a9116-126">Scaffold identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="a9116-127">Mettez à `Startup` jour la classe avec un code similaire à ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="a9116-127">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="a9116-128">Identité de l’échafaudage dans un projet Razor sans autorisation existante</span><span class="sxs-lookup"><span data-stu-id="a9116-128">Scaffold identity into a Razor project without existing authorization</span></span>

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

<span data-ttu-id="a9116-129">L’identité est configurée dans *Areas/Identity/IdentityHostingStartup. cs*.</span><span class="sxs-lookup"><span data-stu-id="a9116-129">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="a9116-130">Pour plus d’informations, consultez [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="a9116-130">for more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="a9116-131">Migrations, UseAuthentication et disposition</span><span class="sxs-lookup"><span data-stu-id="a9116-131">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="a9116-132">Activer l’authentification</span><span class="sxs-lookup"><span data-stu-id="a9116-132">Enable authentication</span></span>

<span data-ttu-id="a9116-133">Mettez à `Startup` jour la classe avec un code similaire à ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="a9116-133">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRP.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="a9116-134">Modifications de la disposition</span><span class="sxs-lookup"><span data-stu-id="a9116-134">Layout changes</span></span>

<span data-ttu-id="a9116-135">Facultatif : ajoutez la connexion partielle (`_LoginPartial`) au fichier de disposition :</span><span class="sxs-lookup"><span data-stu-id="a9116-135">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[Main](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="a9116-136">Identité de l’échafaudage dans un projet Razor avec autorisation</span><span class="sxs-lookup"><span data-stu-id="a9116-136">Scaffold identity into a Razor project with authorization</span></span>

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
<span data-ttu-id="a9116-137">Certaines options d’identité sont configurées dans *Areas/Identity/IdentityHostingStartup. cs*.</span><span class="sxs-lookup"><span data-stu-id="a9116-137">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="a9116-138">Pour plus d’informations, consultez [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="a9116-138">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="a9116-139">Identité de l’échafaudage dans un projet MVC sans autorisation existante</span><span class="sxs-lookup"><span data-stu-id="a9116-139">Scaffold identity into an MVC project without existing authorization</span></span>

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

<span data-ttu-id="a9116-140">Facultatif : ajoutez la connexion partielle (`_LoginPartial`) au fichier *views/shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="a9116-140">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[Main](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

* <span data-ttu-id="a9116-141">Déplacez le fichier *pages/Shared/_LoginPartial. cshtml* vers *views/shared/_LoginPartial. cshtml*</span><span class="sxs-lookup"><span data-stu-id="a9116-141">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="a9116-142">L’identité est configurée dans *Areas/Identity/IdentityHostingStartup. cs*.</span><span class="sxs-lookup"><span data-stu-id="a9116-142">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="a9116-143">Pour plus d’informations, consultez IHostingStartup.</span><span class="sxs-lookup"><span data-stu-id="a9116-143">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="a9116-144">Mettez à `Startup` jour la classe avec un code similaire à ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="a9116-144">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="a9116-145">Identité de l’échafaudage dans un projet MVC avec autorisation</span><span class="sxs-lookup"><span data-stu-id="a9116-145">Scaffold identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="a9116-146">Créer une source d’interface utilisateur d’identité complète</span><span class="sxs-lookup"><span data-stu-id="a9116-146">Create full identity UI source</span></span>

<span data-ttu-id="a9116-147">Pour conserver le contrôle total de l’interface utilisateur de l’identité, exécutez le générateur de modèles d’identité et sélectionnez **remplacer tous les fichiers**.</span><span class="sxs-lookup"><span data-stu-id="a9116-147">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="a9116-148">Le code en surbrillance suivant montre les modifications pour remplacer l’interface utilisateur d’identité par défaut par l’identité dans une application Web ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="a9116-148">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="a9116-149">Vous pouvez le faire pour avoir le contrôle total de l’interface utilisateur de l’identité.</span><span class="sxs-lookup"><span data-stu-id="a9116-149">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="a9116-150">L’identité par défaut est remplacée dans le code suivant :</span><span class="sxs-lookup"><span data-stu-id="a9116-150">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="a9116-151">Le code suivant définit les [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)et [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span><span class="sxs-lookup"><span data-stu-id="a9116-151">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="a9116-152">Inscrire une `IEmailSender` implémentation, par exemple :</span><span class="sxs-lookup"><span data-stu-id="a9116-152">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a><span data-ttu-id="a9116-153">Désactiver la page de Registre</span><span class="sxs-lookup"><span data-stu-id="a9116-153">Disable register page</span></span>

<span data-ttu-id="a9116-154">Pour désactiver l’inscription des utilisateurs :</span><span class="sxs-lookup"><span data-stu-id="a9116-154">To disable user registration:</span></span>

* <span data-ttu-id="a9116-155">Identité de l’échafaudage.</span><span class="sxs-lookup"><span data-stu-id="a9116-155">Scaffold Identity.</span></span> <span data-ttu-id="a9116-156">Incluez Account. Register, Account. Login et Account. RegisterConfirmation.</span><span class="sxs-lookup"><span data-stu-id="a9116-156">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="a9116-157">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="a9116-157">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="a9116-158">Mettez à jour *Areas/Identity/pages/Account/Register. cshtml. cs* afin que les utilisateurs ne puissent pas s’inscrire à partir de ce point de terminaison :</span><span class="sxs-lookup"><span data-stu-id="a9116-158">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="a9116-159">Mettez à jour *Areas/Identity/pages/Account/Register. cshtml* pour qu’il soit cohérent avec les modifications précédentes :</span><span class="sxs-lookup"><span data-stu-id="a9116-159">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="a9116-160">Commentez ou supprimez le lien d’inscription de *Areas/Identity/pages/Account/login. cshtml*</span><span class="sxs-lookup"><span data-stu-id="a9116-160">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="a9116-161">Mettez à jour la page *Areas/Identity/pages/Account/RegisterConfirmation* .</span><span class="sxs-lookup"><span data-stu-id="a9116-161">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="a9116-162">Supprimez le code et les liens du fichier cshtml.</span><span class="sxs-lookup"><span data-stu-id="a9116-162">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="a9116-163">Supprimez le code de confirmation `PageModel`du :</span><span class="sxs-lookup"><span data-stu-id="a9116-163">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="a9116-164">Utiliser une autre application pour ajouter des utilisateurs</span><span class="sxs-lookup"><span data-stu-id="a9116-164">Use another app to add users</span></span>

<span data-ttu-id="a9116-165">Fournissez un mécanisme pour ajouter des utilisateurs en dehors de l’application Web.</span><span class="sxs-lookup"><span data-stu-id="a9116-165">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="a9116-166">Les options permettant d’ajouter des utilisateurs sont les suivantes :</span><span class="sxs-lookup"><span data-stu-id="a9116-166">Options to add users include:</span></span>

* <span data-ttu-id="a9116-167">Une application Web d’administration dédiée.</span><span class="sxs-lookup"><span data-stu-id="a9116-167">A dedicated admin web app.</span></span>
* <span data-ttu-id="a9116-168">Application console.</span><span class="sxs-lookup"><span data-stu-id="a9116-168">A console app.</span></span>

<span data-ttu-id="a9116-169">Le code suivant décrit une approche de l’ajout d’utilisateurs :</span><span class="sxs-lookup"><span data-stu-id="a9116-169">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="a9116-170">Une liste d’utilisateurs est lue en mémoire.</span><span class="sxs-lookup"><span data-stu-id="a9116-170">A list of users is read into memory.</span></span>
* <span data-ttu-id="a9116-171">Un mot de passe fort unique est généré pour chaque utilisateur.</span><span class="sxs-lookup"><span data-stu-id="a9116-171">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="a9116-172">L’utilisateur est ajouté à la base de données d’identité.</span><span class="sxs-lookup"><span data-stu-id="a9116-172">The user is added to the Identity database.</span></span>
* <span data-ttu-id="a9116-173">L’utilisateur est averti et a demandé à modifier le mot de passe.</span><span class="sxs-lookup"><span data-stu-id="a9116-173">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="a9116-174">L’exemple de code suivant présente l’ajout d’un utilisateur :</span><span class="sxs-lookup"><span data-stu-id="a9116-174">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="a9116-175">Une approche similaire peut être suivie pour les scénarios de production.</span><span class="sxs-lookup"><span data-stu-id="a9116-175">A similar approach can be followed for production scenarios.</span></span>

## <a name="prevent-publish-of-static-identity-assets"></a><span data-ttu-id="a9116-176">Empêcher la publication de ressources d’identité statiques</span><span class="sxs-lookup"><span data-stu-id="a9116-176">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="a9116-177">Pour empêcher la publication de ressources d’identité statiques sur la <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>racine Web, consultez.</span><span class="sxs-lookup"><span data-stu-id="a9116-177">To prevent publishing static Identity assets to the web root, see <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a9116-178">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="a9116-178">Additional resources</span></span>

* [<span data-ttu-id="a9116-179">Modifications apportées au code d’authentification pour ASP.NET Core 2,1 et versions ultérieures</span><span class="sxs-lookup"><span data-stu-id="a9116-179">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a9116-180">ASP.NET Core 2,1 et versions ultérieures fournissent [ASP.net Core identité](xref:security/authentication/identity) sous forme de [bibliothèque de classes Razor](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="a9116-180">ASP.NET Core 2.1 and later provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="a9116-181">Les applications qui incluent une identité peuvent appliquer l’échafaudage pour ajouter de manière sélective le code source contenu dans la bibliothèque de classes Razor d’identité (RCL).</span><span class="sxs-lookup"><span data-stu-id="a9116-181">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="a9116-182">Vous pouvez souhaiter générer le code source afin de pouvoir modifier le code et changer le comportement.</span><span class="sxs-lookup"><span data-stu-id="a9116-182">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="a9116-183">Par exemple, vous pouvez demander au générateur de modèles automatique de générer le code utilisé dans l’inscription.</span><span class="sxs-lookup"><span data-stu-id="a9116-183">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="a9116-184">Le code généré est prioritaire sur le même code dans la bibliothèque de classes Razor d’identité.</span><span class="sxs-lookup"><span data-stu-id="a9116-184">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="a9116-185">Pour obtenir le contrôle total de l’interface utilisateur et ne pas utiliser le RCL par défaut, consultez la section [créer une source d’interface utilisateur d’identité complète](#full).</span><span class="sxs-lookup"><span data-stu-id="a9116-185">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="a9116-186">Les applications qui n’incluent **pas** l’authentification peuvent appliquer l’échafaudage pour ajouter le package d’identité RCL.</span><span class="sxs-lookup"><span data-stu-id="a9116-186">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="a9116-187">Vous pouvez sélectionner le code d’identité à générer.</span><span class="sxs-lookup"><span data-stu-id="a9116-187">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="a9116-188">Bien que le générateur de modèles génère la plus grande partie du code nécessaire, vous devez mettre à jour votre projet pour terminer le processus.</span><span class="sxs-lookup"><span data-stu-id="a9116-188">Although the scaffolder generates most of the necessary code, you'll have to update your project to complete the process.</span></span> <span data-ttu-id="a9116-189">Ce document explique les étapes nécessaires pour effectuer une mise à jour de l’échafaudage d’identité.</span><span class="sxs-lookup"><span data-stu-id="a9116-189">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="a9116-190">Lorsque l’échafaudage d’identité est exécuté, un fichier *ScaffoldingReadme. txt* est créé dans le répertoire du projet.</span><span class="sxs-lookup"><span data-stu-id="a9116-190">When the Identity scaffolder is run, a *ScaffoldingReadme.txt* file is created in the project directory.</span></span> <span data-ttu-id="a9116-191">Le fichier *ScaffoldingReadme. txt* contient des instructions générales sur ce qui est nécessaire pour effectuer la mise à jour de l’échafaudage d’identité.</span><span class="sxs-lookup"><span data-stu-id="a9116-191">The *ScaffoldingReadme.txt* file contains general instructions on what's needed to complete the Identity scaffolding update.</span></span> <span data-ttu-id="a9116-192">Ce document contient des instructions plus complètes que le fichier *ScaffoldingReadme. txt* .</span><span class="sxs-lookup"><span data-stu-id="a9116-192">This document contains more complete instructions than the *ScaffoldingReadme.txt* file.</span></span>

<span data-ttu-id="a9116-193">Nous vous recommandons d’utiliser un système de contrôle de code source qui affiche les différences entre les fichiers et vous permet d’annuler les modifications.</span><span class="sxs-lookup"><span data-stu-id="a9116-193">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="a9116-194">Inspectez les modifications après avoir exécuté l’échafaudage d’identité.</span><span class="sxs-lookup"><span data-stu-id="a9116-194">Inspect the changes after running the Identity scaffolder.</span></span>

> [!NOTE]
> <span data-ttu-id="a9116-195">Les services sont requis lors de l’utilisation de [l’authentification à deux facteurs](xref:security/authentication/identity-enable-qrcodes), la [confirmation de compte et la récupération de mot de passe](xref:security/authentication/accconfirm), ainsi que d’autres fonctionnalités de sécurité avec identité.</span><span class="sxs-lookup"><span data-stu-id="a9116-195">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="a9116-196">Les services ou stubs de service ne sont pas générés lors de la génération de modèles automatique d’identité.</span><span class="sxs-lookup"><span data-stu-id="a9116-196">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="a9116-197">Les services pour activer ces fonctionnalités doivent être ajoutés manuellement.</span><span class="sxs-lookup"><span data-stu-id="a9116-197">Services to enable these features must be added manually.</span></span> <span data-ttu-id="a9116-198">Par exemple, consultez [exiger une confirmation par courrier électronique](xref:security/authentication/accconfirm#require-email-confirmation).</span><span class="sxs-lookup"><span data-stu-id="a9116-198">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="a9116-199">Identité de l’échafaudage dans un projet vide</span><span class="sxs-lookup"><span data-stu-id="a9116-199">Scaffold identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="a9116-200">Ajoutez les appels en surbrillance `Startup` suivants à la classe :</span><span class="sxs-lookup"><span data-stu-id="a9116-200">Add the following highlighted calls to the `Startup` class:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="a9116-201">Identité de l’échafaudage dans un projet Razor sans autorisation existante</span><span class="sxs-lookup"><span data-stu-id="a9116-201">Scaffold identity into a Razor project without existing authorization</span></span>

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

<span data-ttu-id="a9116-202">L’identité est configurée dans *Areas/Identity/IdentityHostingStartup. cs*.</span><span class="sxs-lookup"><span data-stu-id="a9116-202">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="a9116-203">Pour plus d’informations, consultez [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="a9116-203">for more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="a9116-204">Migrations, UseAuthentication et disposition</span><span class="sxs-lookup"><span data-stu-id="a9116-204">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="a9116-205">Activer l’authentification</span><span class="sxs-lookup"><span data-stu-id="a9116-205">Enable authentication</span></span>

<span data-ttu-id="a9116-206">Dans la `Configure` méthode de la `Startup` classe, appelez [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) après `UseStaticFiles`:</span><span class="sxs-lookup"><span data-stu-id="a9116-206">In the `Configure` method of the `Startup` class, call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="a9116-207">Modifications de la disposition</span><span class="sxs-lookup"><span data-stu-id="a9116-207">Layout changes</span></span>

<span data-ttu-id="a9116-208">Facultatif : ajoutez la connexion partielle (`_LoginPartial`) au fichier de disposition :</span><span class="sxs-lookup"><span data-stu-id="a9116-208">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[Main](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="a9116-209">Identité de l’échafaudage dans un projet Razor avec autorisation</span><span class="sxs-lookup"><span data-stu-id="a9116-209">Scaffold identity into a Razor project with authorization</span></span>

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
<span data-ttu-id="a9116-210">Certaines options d’identité sont configurées dans *Areas/Identity/IdentityHostingStartup. cs*.</span><span class="sxs-lookup"><span data-stu-id="a9116-210">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="a9116-211">Pour plus d’informations, consultez [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="a9116-211">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="a9116-212">Identité de l’échafaudage dans un projet MVC sans autorisation existante</span><span class="sxs-lookup"><span data-stu-id="a9116-212">Scaffold identity into an MVC project without existing authorization</span></span>

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

<span data-ttu-id="a9116-213">Facultatif : ajoutez la connexion partielle (`_LoginPartial`) au fichier *views/shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="a9116-213">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* <span data-ttu-id="a9116-214">Déplacez le fichier *pages/Shared/_LoginPartial. cshtml* vers *views/shared/_LoginPartial. cshtml*</span><span class="sxs-lookup"><span data-stu-id="a9116-214">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="a9116-215">L’identité est configurée dans *Areas/Identity/IdentityHostingStartup. cs*.</span><span class="sxs-lookup"><span data-stu-id="a9116-215">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="a9116-216">Pour plus d’informations, consultez IHostingStartup.</span><span class="sxs-lookup"><span data-stu-id="a9116-216">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="a9116-217">Appeler [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) après `UseStaticFiles`:</span><span class="sxs-lookup"><span data-stu-id="a9116-217">Call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="a9116-218">Identité de l’échafaudage dans un projet MVC avec autorisation</span><span class="sxs-lookup"><span data-stu-id="a9116-218">Scaffold identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="a9116-219">Supprimez le dossier *pages/Shared* et les fichiers de ce dossier.</span><span class="sxs-lookup"><span data-stu-id="a9116-219">Delete the *Pages/Shared* folder and the files in that folder.</span></span>

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="a9116-220">Créer une source d’interface utilisateur d’identité complète</span><span class="sxs-lookup"><span data-stu-id="a9116-220">Create full identity UI source</span></span>

<span data-ttu-id="a9116-221">Pour conserver le contrôle total de l’interface utilisateur de l’identité, exécutez le générateur de modèles d’identité et sélectionnez **remplacer tous les fichiers**.</span><span class="sxs-lookup"><span data-stu-id="a9116-221">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="a9116-222">Le code en surbrillance suivant montre les modifications pour remplacer l’interface utilisateur d’identité par défaut par l’identité dans une application Web ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="a9116-222">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="a9116-223">Vous pouvez le faire pour avoir le contrôle total de l’interface utilisateur de l’identité.</span><span class="sxs-lookup"><span data-stu-id="a9116-223">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="a9116-224">L’identité par défaut est remplacée dans le code suivant :</span><span class="sxs-lookup"><span data-stu-id="a9116-224">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="a9116-225">Le code suivant définit les [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)et [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span><span class="sxs-lookup"><span data-stu-id="a9116-225">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="a9116-226">Inscrire une `IEmailSender` implémentation, par exemple :</span><span class="sxs-lookup"><span data-stu-id="a9116-226">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a><span data-ttu-id="a9116-227">Désactiver la page de Registre</span><span class="sxs-lookup"><span data-stu-id="a9116-227">Disable register page</span></span>

<span data-ttu-id="a9116-228">Pour désactiver l’inscription des utilisateurs :</span><span class="sxs-lookup"><span data-stu-id="a9116-228">To disable user registration:</span></span>

* <span data-ttu-id="a9116-229">Identité de l’échafaudage.</span><span class="sxs-lookup"><span data-stu-id="a9116-229">Scaffold Identity.</span></span> <span data-ttu-id="a9116-230">Incluez Account. Register, Account. Login et Account. RegisterConfirmation.</span><span class="sxs-lookup"><span data-stu-id="a9116-230">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="a9116-231">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="a9116-231">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="a9116-232">Mettez à jour *Areas/Identity/pages/Account/Register. cshtml. cs* afin que les utilisateurs ne puissent pas s’inscrire à partir de ce point de terminaison :</span><span class="sxs-lookup"><span data-stu-id="a9116-232">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="a9116-233">Mettez à jour *Areas/Identity/pages/Account/Register. cshtml* pour qu’il soit cohérent avec les modifications précédentes :</span><span class="sxs-lookup"><span data-stu-id="a9116-233">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="a9116-234">Commentez ou supprimez le lien d’inscription de *Areas/Identity/pages/Account/login. cshtml*</span><span class="sxs-lookup"><span data-stu-id="a9116-234">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="a9116-235">Mettez à jour la page *Areas/Identity/pages/Account/RegisterConfirmation* .</span><span class="sxs-lookup"><span data-stu-id="a9116-235">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="a9116-236">Supprimez le code et les liens du fichier cshtml.</span><span class="sxs-lookup"><span data-stu-id="a9116-236">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="a9116-237">Supprimez le code de confirmation `PageModel`du :</span><span class="sxs-lookup"><span data-stu-id="a9116-237">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="a9116-238">Utiliser une autre application pour ajouter des utilisateurs</span><span class="sxs-lookup"><span data-stu-id="a9116-238">Use another app to add users</span></span>

<span data-ttu-id="a9116-239">Fournissez un mécanisme pour ajouter des utilisateurs en dehors de l’application Web.</span><span class="sxs-lookup"><span data-stu-id="a9116-239">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="a9116-240">Les options permettant d’ajouter des utilisateurs sont les suivantes :</span><span class="sxs-lookup"><span data-stu-id="a9116-240">Options to add users include:</span></span>

* <span data-ttu-id="a9116-241">Une application Web d’administration dédiée.</span><span class="sxs-lookup"><span data-stu-id="a9116-241">A dedicated admin web app.</span></span>
* <span data-ttu-id="a9116-242">Application console.</span><span class="sxs-lookup"><span data-stu-id="a9116-242">A console app.</span></span>

<span data-ttu-id="a9116-243">Le code suivant décrit une approche de l’ajout d’utilisateurs :</span><span class="sxs-lookup"><span data-stu-id="a9116-243">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="a9116-244">Une liste d’utilisateurs est lue en mémoire.</span><span class="sxs-lookup"><span data-stu-id="a9116-244">A list of users is read into memory.</span></span>
* <span data-ttu-id="a9116-245">Un mot de passe fort unique est généré pour chaque utilisateur.</span><span class="sxs-lookup"><span data-stu-id="a9116-245">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="a9116-246">L’utilisateur est ajouté à la base de données d’identité.</span><span class="sxs-lookup"><span data-stu-id="a9116-246">The user is added to the Identity database.</span></span>
* <span data-ttu-id="a9116-247">L’utilisateur est averti et a demandé à modifier le mot de passe.</span><span class="sxs-lookup"><span data-stu-id="a9116-247">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="a9116-248">L’exemple de code suivant présente l’ajout d’un utilisateur :</span><span class="sxs-lookup"><span data-stu-id="a9116-248">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="a9116-249">Une approche similaire peut être suivie pour les scénarios de production.</span><span class="sxs-lookup"><span data-stu-id="a9116-249">A similar approach can be followed for production scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a9116-250">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="a9116-250">Additional resources</span></span>

* [<span data-ttu-id="a9116-251">Modifications apportées au code d’authentification pour ASP.NET Core 2,1 et versions ultérieures</span><span class="sxs-lookup"><span data-stu-id="a9116-251">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end