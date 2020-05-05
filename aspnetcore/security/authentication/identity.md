---
title: Présentation de Identity sur ASP.net Core
author: rick-anderson
description: Utilisez Identity avec une application ASP.net core. Découvrez comment définir les exigences de mot de passe (RequireDigit, RequiredLength, RequiredUniqueChars, etc.).
ms.author: riande
ms.date: 01/15/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/identity
ms.openlocfilehash: d596a8357c5c812b94950809eedf35718328747c
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777005"
---
# <a name="introduction-to-identity-on-aspnet-core"></a><span data-ttu-id="b629d-104">Présentation de l’identité sur ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b629d-104">Introduction to Identity on ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b629d-105">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="b629d-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="b629d-106">ASP.NET Core l’identité :</span><span class="sxs-lookup"><span data-stu-id="b629d-106">ASP.NET Core Identity:</span></span>

* <span data-ttu-id="b629d-107">Est une API qui prend en charge la fonctionnalité de connexion de l’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="b629d-107">Is an API that supports user interface (UI) login functionality.</span></span>
* <span data-ttu-id="b629d-108">Gère les utilisateurs, les mots de passe, les données de profil, les rôles, les revendications, les jetons, la confirmation par e-mail et bien plus encore.</span><span class="sxs-lookup"><span data-stu-id="b629d-108">Manages users, passwords, profile data, roles, claims, tokens, email confirmation, and more.</span></span>

<span data-ttu-id="b629d-109">Les utilisateurs peuvent créer un compte avec les informations de connexion stockées dans l’identité, ou ils peuvent utiliser un fournisseur de connexion externe.</span><span class="sxs-lookup"><span data-stu-id="b629d-109">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="b629d-110">Les fournisseurs de connexion externes pris en charge incluent [Facebook, Google, Microsoft Account et Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="b629d-110">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="b629d-111">Le [code source](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) de l’identité est disponible sur GitHub.</span><span class="sxs-lookup"><span data-stu-id="b629d-111">The [Identity source code](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) is available on GitHub.</span></span> <span data-ttu-id="b629d-112">[Identification de l’échafaudage](xref:security/authentication/scaffold-identity) et affichage des fichiers générés pour examiner l’interaction du modèle avec l’identité.</span><span class="sxs-lookup"><span data-stu-id="b629d-112">[Scaffold Identity](xref:security/authentication/scaffold-identity) and view the generated files to review the template interaction with Identity.</span></span>

<span data-ttu-id="b629d-113">L’identité est généralement configurée à l’aide d’une base de données SQL Server pour stocker les noms d’utilisateur, les mots de passe et les données de profil.</span><span class="sxs-lookup"><span data-stu-id="b629d-113">Identity is typically configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="b629d-114">Vous pouvez également utiliser un autre magasin persistant, par exemple, le stockage table Azure.</span><span class="sxs-lookup"><span data-stu-id="b629d-114">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="b629d-115">Dans cette rubrique, vous allez apprendre à utiliser l’identité pour vous inscrire, vous connecter et déconnecter un utilisateur.</span><span class="sxs-lookup"><span data-stu-id="b629d-115">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="b629d-116">Remarque : les modèles traitent le nom d’utilisateur et l’adresse de messagerie comme les utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="b629d-116">Note: the templates treat username and email as the same for users.</span></span> <span data-ttu-id="b629d-117">Pour obtenir des instructions plus détaillées sur la création d’applications qui utilisent l’identité, consultez la section étapes suivantes à la fin de cet article.</span><span class="sxs-lookup"><span data-stu-id="b629d-117">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<span data-ttu-id="b629d-118">[Plateforme d’identité Microsoft](/azure/active-directory/develop/) :</span><span class="sxs-lookup"><span data-stu-id="b629d-118">[Microsoft identity platform](/azure/active-directory/develop/) is:</span></span>

* <span data-ttu-id="b629d-119">Évolution de la plateforme de développement Azure Active Directory (Azure AD).</span><span class="sxs-lookup"><span data-stu-id="b629d-119">An evolution of the Azure Active Directory (Azure AD) developer platform.</span></span>
* <span data-ttu-id="b629d-120">Sans rapport avec ASP.NET Core identité.</span><span class="sxs-lookup"><span data-stu-id="b629d-120">Unrelated to ASP.NET Core Identity.</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

<span data-ttu-id="b629d-121">[Affichez ou téléchargez l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([procédure de téléchargement)](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="b629d-121">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="b629d-122">Créer une application Web avec l’authentification</span><span class="sxs-lookup"><span data-stu-id="b629d-122">Create a Web app with authentication</span></span>

<span data-ttu-id="b629d-123">Créez un projet d’application Web ASP.NET Core avec des comptes d’utilisateur individuels.</span><span class="sxs-lookup"><span data-stu-id="b629d-123">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b629d-124">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b629d-124">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b629d-125">Sélectionnez **fichier** > **nouveau** > **projet**.</span><span class="sxs-lookup"><span data-stu-id="b629d-125">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="b629d-126">Sélectionnez **Application web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="b629d-126">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="b629d-127">Nommez le projet **application Web 1** pour avoir le même espace de noms que le projet à télécharger.</span><span class="sxs-lookup"><span data-stu-id="b629d-127">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="b629d-128">Cliquez sur **OK**.</span><span class="sxs-lookup"><span data-stu-id="b629d-128">Click **OK**.</span></span>
* <span data-ttu-id="b629d-129">Sélectionnez une **application Web**ASP.net Core, puis sélectionnez **modifier l’authentification**.</span><span class="sxs-lookup"><span data-stu-id="b629d-129">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="b629d-130">Sélectionnez **comptes d’utilisateur individuels** , puis cliquez sur **OK**.</span><span class="sxs-lookup"><span data-stu-id="b629d-130">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="b629d-131">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="b629d-131">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

<span data-ttu-id="b629d-132">La commande précédente crée une application Web Razor à l’aide de SQLite.</span><span class="sxs-lookup"><span data-stu-id="b629d-132">The preceding command creates a Razor web app using SQLite.</span></span> <span data-ttu-id="b629d-133">Pour créer l’application Web avec la base de données locale, exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="b629d-133">To create the web app with LocalDB, run the following command:</span></span>

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

<span data-ttu-id="b629d-134">Le projet généré fournit une [identité de ASP.net Core](xref:security/authentication/identity) sous forme de bibliothèque de [classes Razor](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="b629d-134">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="b629d-135">La bibliothèque de classes Razor d’identité expose des points `Identity` de terminaison avec la zone.</span><span class="sxs-lookup"><span data-stu-id="b629d-135">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="b629d-136">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="b629d-136">For example:</span></span>

* <span data-ttu-id="b629d-137">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="b629d-137">/Identity/Account/Login</span></span>
* <span data-ttu-id="b629d-138">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="b629d-138">/Identity/Account/Logout</span></span>
* <span data-ttu-id="b629d-139">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="b629d-139">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="b629d-140">Appliquer des migrations</span><span class="sxs-lookup"><span data-stu-id="b629d-140">Apply migrations</span></span>

<span data-ttu-id="b629d-141">Appliquez les migrations pour initialiser la base de données.</span><span class="sxs-lookup"><span data-stu-id="b629d-141">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b629d-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b629d-142">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b629d-143">Exécutez la commande suivante dans la console du gestionnaire de package (PMC) :</span><span class="sxs-lookup"><span data-stu-id="b629d-143">Run the following command in the Package Manager Console (PMC):</span></span>

`PM> Update-Database`

# <a name="net-core-cli"></a>[<span data-ttu-id="b629d-144">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="b629d-144">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="b629d-145">Les migrations ne sont pas nécessaires pour cette étape lors de l’utilisation de SQLite.</span><span class="sxs-lookup"><span data-stu-id="b629d-145">Migrations are not necessary at this step when using SQLite.</span></span> <span data-ttu-id="b629d-146">Pour la base de données locale, exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="b629d-146">For LocalDB, run the following command:</span></span>

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="b629d-147">Registre de test et connexion</span><span class="sxs-lookup"><span data-stu-id="b629d-147">Test Register and Login</span></span>

<span data-ttu-id="b629d-148">Exécutez l’application et inscrivez un utilisateur.</span><span class="sxs-lookup"><span data-stu-id="b629d-148">Run the app and register a user.</span></span> <span data-ttu-id="b629d-149">Selon la taille de votre écran, vous devrez peut-être sélectionner le bouton bascule de navigation pour afficher les liens de **connexion** et de **Registre** .</span><span class="sxs-lookup"><span data-stu-id="b629d-149">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="b629d-150">Configurer les services d’identité</span><span class="sxs-lookup"><span data-stu-id="b629d-150">Configure Identity services</span></span>

<span data-ttu-id="b629d-151">Les services sont ajoutés `ConfigureServices`dans.</span><span class="sxs-lookup"><span data-stu-id="b629d-151">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="b629d-152">Le modèle par défaut consiste à appeler toutes les méthodes `Add{Service}`, puis toutes les méthodes `services.Configure{Service}`.</span><span class="sxs-lookup"><span data-stu-id="b629d-152">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=10-99)]

<span data-ttu-id="b629d-153">Le code en surbrillance précédent configure l’identité avec les valeurs d’option par défaut.</span><span class="sxs-lookup"><span data-stu-id="b629d-153">The preceding highlighted code configures Identity with default option values.</span></span> <span data-ttu-id="b629d-154">Les services sont mis à la disposition de l’application via l' [injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="b629d-154">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="b629d-155">L’identité est activée en <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>appelant.</span><span class="sxs-lookup"><span data-stu-id="b629d-155">Identity is enabled by calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>.</span></span> <span data-ttu-id="b629d-156">`UseAuthentication`Ajoute l' [intergiciel (middleware](xref:fundamentals/middleware/index) ) d’authentification au pipeline de requête.</span><span class="sxs-lookup"><span data-stu-id="b629d-156">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

<span data-ttu-id="b629d-157">L’application générée par un modèle n’utilise pas [d’autorisation](xref:security/authorization/secure-data).</span><span class="sxs-lookup"><span data-stu-id="b629d-157">The template-generated app doesn't use [authorization](xref:security/authorization/secure-data).</span></span> <span data-ttu-id="b629d-158">`app.UseAuthorization`est inclus pour s’assurer qu’il est ajouté dans le bon ordre si l’application ajoute une autorisation.</span><span class="sxs-lookup"><span data-stu-id="b629d-158">`app.UseAuthorization` is included to ensure it's added in the correct order should the app add authorization.</span></span> <span data-ttu-id="b629d-159">`UseRouting`, `UseAuthentication`, `UseAuthorization`et `UseEndpoints` doivent être appelés dans l’ordre indiqué dans le code précédent.</span><span class="sxs-lookup"><span data-stu-id="b629d-159">`UseRouting`, `UseAuthentication`, `UseAuthorization`, and `UseEndpoints` must be called in the order shown in the preceding code.</span></span>

<span data-ttu-id="b629d-160">Pour plus d’informations `IdentityOptions` sur `Startup`et, <xref:Microsoft.AspNetCore.Identity.IdentityOptions> consultez et démarrage de l' [application](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="b629d-160">For more information on `IdentityOptions` and `Startup`, see <xref:Microsoft.AspNetCore.Identity.IdentityOptions> and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="b629d-161">Registre de génération de modèles, connexion et déconnexion</span><span class="sxs-lookup"><span data-stu-id="b629d-161">Scaffold Register, Login, and LogOut</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b629d-162">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b629d-162">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b629d-163">Ajoutez les fichiers Register, login et LogOut.</span><span class="sxs-lookup"><span data-stu-id="b629d-163">Add the Register, Login, and LogOut files.</span></span> <span data-ttu-id="b629d-164">Suivez l' [identité de l’échafaudage dans un projet Razor avec des instructions d’autorisation](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) pour générer le code présenté dans cette section.</span><span class="sxs-lookup"><span data-stu-id="b629d-164">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="b629d-165">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="b629d-165">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="b629d-166">Si vous avez créé le projet avec le nom **application Web 1**, exécutez les commandes suivantes.</span><span class="sxs-lookup"><span data-stu-id="b629d-166">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="b629d-167">Sinon, utilisez l’espace de noms correct `ApplicationDbContext`pour :</span><span class="sxs-lookup"><span data-stu-id="b629d-167">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="b629d-168">PowerShell utilise un point-virgule comme séparateur de commande.</span><span class="sxs-lookup"><span data-stu-id="b629d-168">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="b629d-169">Quand vous utilisez PowerShell, échappez les points-virgules dans la liste de fichiers ou placez la liste de fichiers entre guillemets doubles, comme le montre l’exemple précédent.</span><span class="sxs-lookup"><span data-stu-id="b629d-169">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

<span data-ttu-id="b629d-170">Pour plus d’informations sur la génération de modèles automatique d’identité, consultez identification de l' [échafaudage dans un projet Razor avec autorisation](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span><span class="sxs-lookup"><span data-stu-id="b629d-170">For more information on scaffolding Identity, see [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="b629d-171">Examiner le registre</span><span class="sxs-lookup"><span data-stu-id="b629d-171">Examine Register</span></span>

<span data-ttu-id="b629d-172">Quand un utilisateur clique **Register** sur le lien register `RegisterModel.OnPostAsync` , l’action est appelée.</span><span class="sxs-lookup"><span data-stu-id="b629d-172">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="b629d-173">L’utilisateur est créé par [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) sur l' `_userManager` objet.</span><span class="sxs-lookup"><span data-stu-id="b629d-173">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object.</span></span> <span data-ttu-id="b629d-174">`_userManager`est fourni par l’injection de dépendances) :</span><span class="sxs-lookup"><span data-stu-id="b629d-174">`_userManager` is provided by dependency injection):</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

<span data-ttu-id="b629d-175">Si l’utilisateur a été créé avec succès, l’utilisateur est connecté par l’appel `_signInManager.SignInAsync`à.</span><span class="sxs-lookup"><span data-stu-id="b629d-175">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="b629d-176">Consultez [confirmation du compte](xref:security/authentication/accconfirm#prevent-login-at-registration) pour connaître les étapes permettant d’empêcher la connexion immédiate lors de l’inscription.</span><span class="sxs-lookup"><span data-stu-id="b629d-176">See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="b629d-177">Se connecter</span><span class="sxs-lookup"><span data-stu-id="b629d-177">Log in</span></span>

<span data-ttu-id="b629d-178">Le formulaire de connexion s’affiche lorsque :</span><span class="sxs-lookup"><span data-stu-id="b629d-178">The Login form is displayed when:</span></span>

* <span data-ttu-id="b629d-179">Le lien **connexion** est sélectionné.</span><span class="sxs-lookup"><span data-stu-id="b629d-179">The **Log in** link is selected.</span></span>
* <span data-ttu-id="b629d-180">Un utilisateur tente d’accéder à une page restreinte à laquelle il n’est pas autorisé à accéder **ou** lorsqu’il n’a pas été authentifié par le système.</span><span class="sxs-lookup"><span data-stu-id="b629d-180">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="b629d-181">Lorsque le formulaire de la page de connexion est envoyé, `OnPostAsync` l’action est appelée.</span><span class="sxs-lookup"><span data-stu-id="b629d-181">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="b629d-182">`PasswordSignInAsync`est appelé sur l' `_signInManager` objet (fourni par l’injection de dépendances).</span><span class="sxs-lookup"><span data-stu-id="b629d-182">`PasswordSignInAsync` is called on the `_signInManager` object (provided by dependency injection).</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="b629d-183">La classe `Controller` de base expose `User` une propriété qui est accessible à partir des méthodes de contrôleur.</span><span class="sxs-lookup"><span data-stu-id="b629d-183">The base `Controller` class exposes a `User` property that can be accessed from controller methods.</span></span> <span data-ttu-id="b629d-184">Par exemple, vous pouvez énumérer `User.Claims` et prendre des décisions d’autorisation.</span><span class="sxs-lookup"><span data-stu-id="b629d-184">For instance, you can enumerate `User.Claims` and make authorization decisions.</span></span> <span data-ttu-id="b629d-185">Pour plus d’informations, consultez <xref:security/authorization/introduction>.</span><span class="sxs-lookup"><span data-stu-id="b629d-185">For more information, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="b629d-186">Se déconnecter</span><span class="sxs-lookup"><span data-stu-id="b629d-186">Log out</span></span>

<span data-ttu-id="b629d-187">Le lien de **déconnexion** appelle l' `LogoutModel.OnPost` action.</span><span class="sxs-lookup"><span data-stu-id="b629d-187">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Logout.cshtml.cs?highlight=36)]

<span data-ttu-id="b629d-188">Dans le code précédent, le code `return RedirectToPage();` doit être une redirection afin que le navigateur exécute une nouvelle demande et que l’identité de l’utilisateur soit mise à jour.</span><span class="sxs-lookup"><span data-stu-id="b629d-188">In the preceding code, the code `return RedirectToPage();` needs to be a redirect so that the browser performs a new request and the identity for the user gets updated.</span></span>

<span data-ttu-id="b629d-189">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) efface les revendications de l’utilisateur stockées dans un cookie.</span><span class="sxs-lookup"><span data-stu-id="b629d-189">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="b629d-190">La publication est spécifiée dans *pages/Shared/_LoginPartial. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="b629d-190">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-identity"></a><span data-ttu-id="b629d-191">Identité du test</span><span class="sxs-lookup"><span data-stu-id="b629d-191">Test Identity</span></span>

<span data-ttu-id="b629d-192">Les modèles de projet Web par défaut autorisent l’accès anonyme aux pages d’hébergement.</span><span class="sxs-lookup"><span data-stu-id="b629d-192">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="b629d-193">Pour tester l’identité, [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)ajoutez :</span><span class="sxs-lookup"><span data-stu-id="b629d-193">To test Identity, add [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute):</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="b629d-194">Si vous êtes connecté, déconnectez-vous. Exécutez l’application et sélectionnez le lien **confidentialité** .</span><span class="sxs-lookup"><span data-stu-id="b629d-194">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="b629d-195">Vous êtes redirigé vers la page de connexion.</span><span class="sxs-lookup"><span data-stu-id="b629d-195">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="b629d-196">Explorer l’identité</span><span class="sxs-lookup"><span data-stu-id="b629d-196">Explore Identity</span></span>

<span data-ttu-id="b629d-197">Pour explorer l’identité plus en détail :</span><span class="sxs-lookup"><span data-stu-id="b629d-197">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="b629d-198">Créer une source d’interface utilisateur d’identité complète</span><span class="sxs-lookup"><span data-stu-id="b629d-198">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="b629d-199">Examinez la source de chaque page et parcourez le débogueur.</span><span class="sxs-lookup"><span data-stu-id="b629d-199">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a><span data-ttu-id="b629d-200">Composants d’identité</span><span class="sxs-lookup"><span data-stu-id="b629d-200">Identity Components</span></span>

<span data-ttu-id="b629d-201">Tous les packages NuGet dépendants de l’identité sont inclus dans le [ASP.net Core Framework partagé](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span><span class="sxs-lookup"><span data-stu-id="b629d-201">All the Identity-dependent NuGet packages are included in the [ASP.NET Core shared framework](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span></span>

<span data-ttu-id="b629d-202">Le package principal pour l’identité est [Microsoft. AspNetCore. Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span><span class="sxs-lookup"><span data-stu-id="b629d-202">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="b629d-203">Ce package contient l’ensemble principal d’interfaces pour ASP.NET Core identité et est inclus dans `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span><span class="sxs-lookup"><span data-stu-id="b629d-203">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="b629d-204">Migration vers ASP.NET Core identité</span><span class="sxs-lookup"><span data-stu-id="b629d-204">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="b629d-205">Pour plus d’informations et de conseils sur la migration de votre magasin d’identités existant, consultez [migrer l’authentification et l’identité](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="b629d-205">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="b629d-206">Définition de la force du mot de passe</span><span class="sxs-lookup"><span data-stu-id="b629d-206">Setting password strength</span></span>

<span data-ttu-id="b629d-207">Consultez [configuration](#pw) d’un exemple qui définit la configuration minimale requise pour le mot de passe.</span><span class="sxs-lookup"><span data-stu-id="b629d-207">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="b629d-208">AddDefaultIdentity et AddIdentity</span><span class="sxs-lookup"><span data-stu-id="b629d-208">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="b629d-209"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*>a été introduite dans ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="b629d-209"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="b629d-210">L' `AddDefaultIdentity` appel de est similaire à l’appel de ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="b629d-210">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="b629d-211">Pour plus d’informations, consultez [source AddDefaultIdentity](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .</span><span class="sxs-lookup"><span data-stu-id="b629d-211">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="prevent-publish-of-static-identity-assets"></a><span data-ttu-id="b629d-212">Empêcher la publication de ressources d’identité statiques</span><span class="sxs-lookup"><span data-stu-id="b629d-212">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="b629d-213">Pour empêcher la publication de ressources d’identité statiques (feuilles de style et fichiers JavaScript pour l’interface utilisateur d’identité) `ResolveStaticWebAssetsInputsDependsOn` sur la `RemoveIdentityAssets` racine Web, ajoutez la propriété et la cible suivantes au fichier projet de l’application :</span><span class="sxs-lookup"><span data-stu-id="b629d-213">To prevent publishing static Identity assets (stylesheets and JavaScript files for Identity UI) to the web root, add the following `ResolveStaticWebAssetsInputsDependsOn` property and `RemoveIdentityAssets` target to the app's project file:</span></span>

```xml
<PropertyGroup>
  <ResolveStaticWebAssetsInputsDependsOn>RemoveIdentityAssets</ResolveStaticWebAssetsInputsDependsOn>
</PropertyGroup>

<Target Name="RemoveIdentityAssets">
  <ItemGroup>
    <StaticWebAsset Remove="@(StaticWebAsset)" Condition="%(SourceId) == 'Microsoft.AspNetCore.Identity.UI'" />
  </ItemGroup>
</Target>
```

## <a name="next-steps"></a><span data-ttu-id="b629d-214">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="b629d-214">Next Steps</span></span>

* <span data-ttu-id="b629d-215">Pour plus d’informations sur la configuration de l’identité à l’aide de SQLite, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/5131) .</span><span class="sxs-lookup"><span data-stu-id="b629d-215">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* [<span data-ttu-id="b629d-216">Configurer Identity</span><span class="sxs-lookup"><span data-stu-id="b629d-216">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b629d-217">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="b629d-217">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="b629d-218">ASP.NET Core identité est un système d’appartenance qui ajoute des fonctionnalités de connexion aux applications ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b629d-218">ASP.NET Core Identity is a membership system that adds login functionality to ASP.NET Core apps.</span></span> <span data-ttu-id="b629d-219">Les utilisateurs peuvent créer un compte avec les informations de connexion stockées dans l’identité, ou ils peuvent utiliser un fournisseur de connexion externe.</span><span class="sxs-lookup"><span data-stu-id="b629d-219">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="b629d-220">Les fournisseurs de connexion externes pris en charge incluent [Facebook, Google, Microsoft Account et Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="b629d-220">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="b629d-221">L’identité peut être configurée à l’aide d’une base de données SQL Server pour stocker les noms d’utilisateur, les mots de passe et les données de profil.</span><span class="sxs-lookup"><span data-stu-id="b629d-221">Identity can be configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="b629d-222">Vous pouvez également utiliser un autre magasin persistant, par exemple, le stockage table Azure.</span><span class="sxs-lookup"><span data-stu-id="b629d-222">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="b629d-223">[Affichez ou téléchargez l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([procédure de téléchargement)](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="b629d-223">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="b629d-224">Dans cette rubrique, vous allez apprendre à utiliser l’identité pour vous inscrire, vous connecter et déconnecter un utilisateur.</span><span class="sxs-lookup"><span data-stu-id="b629d-224">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="b629d-225">Pour obtenir des instructions plus détaillées sur la création d’applications qui utilisent l’identité, consultez la section étapes suivantes à la fin de cet article.</span><span class="sxs-lookup"><span data-stu-id="b629d-225">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<a name="adi"></a>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="b629d-226">AddDefaultIdentity et AddIdentity</span><span class="sxs-lookup"><span data-stu-id="b629d-226">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="b629d-227"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*>a été introduite dans ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="b629d-227"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="b629d-228">L' `AddDefaultIdentity` appel de est similaire à l’appel de ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="b629d-228">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="b629d-229">Pour plus d’informations, consultez [source AddDefaultIdentity](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .</span><span class="sxs-lookup"><span data-stu-id="b629d-229">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="b629d-230">Créer une application Web avec l’authentification</span><span class="sxs-lookup"><span data-stu-id="b629d-230">Create a Web app with authentication</span></span>

<span data-ttu-id="b629d-231">Créez un projet d’application Web ASP.NET Core avec des comptes d’utilisateur individuels.</span><span class="sxs-lookup"><span data-stu-id="b629d-231">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b629d-232">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b629d-232">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b629d-233">Sélectionnez **fichier** > **nouveau** > **projet**.</span><span class="sxs-lookup"><span data-stu-id="b629d-233">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="b629d-234">Sélectionnez **Application web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="b629d-234">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="b629d-235">Nommez le projet **application Web 1** pour avoir le même espace de noms que le projet à télécharger.</span><span class="sxs-lookup"><span data-stu-id="b629d-235">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="b629d-236">Cliquez sur **OK**.</span><span class="sxs-lookup"><span data-stu-id="b629d-236">Click **OK**.</span></span>
* <span data-ttu-id="b629d-237">Sélectionnez une **application Web**ASP.net Core, puis sélectionnez **modifier l’authentification**.</span><span class="sxs-lookup"><span data-stu-id="b629d-237">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="b629d-238">Sélectionnez **comptes d’utilisateur individuels** , puis cliquez sur **OK**.</span><span class="sxs-lookup"><span data-stu-id="b629d-238">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="b629d-239">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="b629d-239">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

<span data-ttu-id="b629d-240">Le projet généré fournit une [identité de ASP.net Core](xref:security/authentication/identity) sous forme de bibliothèque de [classes Razor](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="b629d-240">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="b629d-241">La bibliothèque de classes Razor d’identité expose des points `Identity` de terminaison avec la zone.</span><span class="sxs-lookup"><span data-stu-id="b629d-241">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="b629d-242">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="b629d-242">For example:</span></span>

* <span data-ttu-id="b629d-243">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="b629d-243">/Identity/Account/Login</span></span>
* <span data-ttu-id="b629d-244">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="b629d-244">/Identity/Account/Logout</span></span>
* <span data-ttu-id="b629d-245">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="b629d-245">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="b629d-246">Appliquer des migrations</span><span class="sxs-lookup"><span data-stu-id="b629d-246">Apply migrations</span></span>

<span data-ttu-id="b629d-247">Appliquez les migrations pour initialiser la base de données.</span><span class="sxs-lookup"><span data-stu-id="b629d-247">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b629d-248">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b629d-248">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b629d-249">Exécutez la commande suivante dans la console du gestionnaire de package (PMC) :</span><span class="sxs-lookup"><span data-stu-id="b629d-249">Run the following command in the Package Manager Console (PMC):</span></span>

```powershell
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="b629d-250">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="b629d-250">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="b629d-251">Registre de test et connexion</span><span class="sxs-lookup"><span data-stu-id="b629d-251">Test Register and Login</span></span>

<span data-ttu-id="b629d-252">Exécutez l’application et inscrivez un utilisateur.</span><span class="sxs-lookup"><span data-stu-id="b629d-252">Run the app and register a user.</span></span> <span data-ttu-id="b629d-253">Selon la taille de votre écran, vous devrez peut-être sélectionner le bouton bascule de navigation pour afficher les liens de **connexion** et de **Registre** .</span><span class="sxs-lookup"><span data-stu-id="b629d-253">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="b629d-254">Configurer les services d’identité</span><span class="sxs-lookup"><span data-stu-id="b629d-254">Configure Identity services</span></span>

<span data-ttu-id="b629d-255">Les services sont ajoutés `ConfigureServices`dans.</span><span class="sxs-lookup"><span data-stu-id="b629d-255">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="b629d-256">Le modèle par défaut consiste à appeler toutes les méthodes `Add{Service}`, puis toutes les méthodes `services.Configure{Service}`.</span><span class="sxs-lookup"><span data-stu-id="b629d-256">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

<span data-ttu-id="b629d-257">Le code précédent configure l’identité avec les valeurs d’option par défaut.</span><span class="sxs-lookup"><span data-stu-id="b629d-257">The preceding code configures Identity with default option values.</span></span> <span data-ttu-id="b629d-258">Les services sont mis à la disposition de l’application via l' [injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="b629d-258">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="b629d-259">L’identité est activée en appelant [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="b629d-259">Identity is enabled by calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="b629d-260">`UseAuthentication`Ajoute l' [intergiciel (middleware](xref:fundamentals/middleware/index) ) d’authentification au pipeline de requête.</span><span class="sxs-lookup"><span data-stu-id="b629d-260">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

<span data-ttu-id="b629d-261">Pour plus d’informations, consultez la [classe IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) et le démarrage de l' [application](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="b629d-261">For more information, see the [IdentityOptions Class](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="b629d-262">Registre de génération de modèles, connexion et déconnexion</span><span class="sxs-lookup"><span data-stu-id="b629d-262">Scaffold Register, Login, and LogOut</span></span>

<span data-ttu-id="b629d-263">Suivez l' [identité de l’échafaudage dans un projet Razor avec des instructions d’autorisation](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) pour générer le code présenté dans cette section.</span><span class="sxs-lookup"><span data-stu-id="b629d-263">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b629d-264">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b629d-264">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b629d-265">Ajoutez les fichiers Register, login et LogOut.</span><span class="sxs-lookup"><span data-stu-id="b629d-265">Add the Register, Login, and LogOut files.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="b629d-266">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="b629d-266">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="b629d-267">Si vous avez créé le projet avec le nom **application Web 1**, exécutez les commandes suivantes.</span><span class="sxs-lookup"><span data-stu-id="b629d-267">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="b629d-268">Sinon, utilisez l’espace de noms correct `ApplicationDbContext`pour :</span><span class="sxs-lookup"><span data-stu-id="b629d-268">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="b629d-269">PowerShell utilise un point-virgule comme séparateur de commande.</span><span class="sxs-lookup"><span data-stu-id="b629d-269">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="b629d-270">Quand vous utilisez PowerShell, échappez les points-virgules dans la liste de fichiers ou placez la liste de fichiers entre guillemets doubles, comme le montre l’exemple précédent.</span><span class="sxs-lookup"><span data-stu-id="b629d-270">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="b629d-271">Examiner le registre</span><span class="sxs-lookup"><span data-stu-id="b629d-271">Examine Register</span></span>

<span data-ttu-id="b629d-272">Quand un utilisateur clique **Register** sur le lien register `RegisterModel.OnPostAsync` , l’action est appelée.</span><span class="sxs-lookup"><span data-stu-id="b629d-272">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="b629d-273">L’utilisateur est créé par [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) sur l' `_userManager` objet.</span><span class="sxs-lookup"><span data-stu-id="b629d-273">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object.</span></span> <span data-ttu-id="b629d-274">`_userManager`est fourni par l’injection de dépendances) :</span><span class="sxs-lookup"><span data-stu-id="b629d-274">`_userManager` is provided by dependency injection):</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

<span data-ttu-id="b629d-275">Si l’utilisateur a été créé avec succès, l’utilisateur est connecté par l’appel `_signInManager.SignInAsync`à.</span><span class="sxs-lookup"><span data-stu-id="b629d-275">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="b629d-276">**Remarque :** Consultez [confirmation du compte](xref:security/authentication/accconfirm#prevent-login-at-registration) pour connaître les étapes permettant d’empêcher la connexion immédiate lors de l’inscription.</span><span class="sxs-lookup"><span data-stu-id="b629d-276">**Note:** See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="b629d-277">Se connecter</span><span class="sxs-lookup"><span data-stu-id="b629d-277">Log in</span></span>

<span data-ttu-id="b629d-278">Le formulaire de connexion s’affiche lorsque :</span><span class="sxs-lookup"><span data-stu-id="b629d-278">The Login form is displayed when:</span></span>

* <span data-ttu-id="b629d-279">Le lien **connexion** est sélectionné.</span><span class="sxs-lookup"><span data-stu-id="b629d-279">The **Log in** link is selected.</span></span>
* <span data-ttu-id="b629d-280">Un utilisateur tente d’accéder à une page restreinte à laquelle il n’est pas autorisé à accéder **ou** lorsqu’il n’a pas été authentifié par le système.</span><span class="sxs-lookup"><span data-stu-id="b629d-280">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="b629d-281">Lorsque le formulaire de la page de connexion est envoyé, `OnPostAsync` l’action est appelée.</span><span class="sxs-lookup"><span data-stu-id="b629d-281">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="b629d-282">`PasswordSignInAsync`est appelé sur l' `_signInManager` objet (fourni par l’injection de dépendances).</span><span class="sxs-lookup"><span data-stu-id="b629d-282">`PasswordSignInAsync` is called on the `_signInManager` object (provided by dependency injection).</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="b629d-283">La classe `Controller` de base expose `User` une propriété à laquelle vous pouvez accéder à partir des méthodes de contrôleur.</span><span class="sxs-lookup"><span data-stu-id="b629d-283">The base `Controller` class exposes a `User` property that you can access from controller methods.</span></span> <span data-ttu-id="b629d-284">Par exemple, vous pouvez énumérer `User.Claims` et prendre des décisions d’autorisation.</span><span class="sxs-lookup"><span data-stu-id="b629d-284">For instance, you can enumerate `User.Claims` and make authorization decisions.</span></span> <span data-ttu-id="b629d-285">Pour plus d’informations, consultez <xref:security/authorization/introduction>.</span><span class="sxs-lookup"><span data-stu-id="b629d-285">For more information, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="b629d-286">Se déconnecter</span><span class="sxs-lookup"><span data-stu-id="b629d-286">Log out</span></span>

<span data-ttu-id="b629d-287">Le lien de **déconnexion** appelle l' `LogoutModel.OnPost` action.</span><span class="sxs-lookup"><span data-stu-id="b629d-287">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

<span data-ttu-id="b629d-288">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) efface les revendications de l’utilisateur stockées dans un cookie.</span><span class="sxs-lookup"><span data-stu-id="b629d-288">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="b629d-289">La publication est spécifiée dans *pages/Shared/_LoginPartial. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="b629d-289">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-identity"></a><span data-ttu-id="b629d-290">TestIdentity</span><span class="sxs-lookup"><span data-stu-id="b629d-290">Test Identity</span></span>

<span data-ttu-id="b629d-291">Les modèles de projet Web par défaut autorisent l’accès anonyme aux pages d’hébergement.</span><span class="sxs-lookup"><span data-stu-id="b629d-291">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="b629d-292">Pour tester Identity, ajoutez [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) à la page confidentialité.</span><span class="sxs-lookup"><span data-stu-id="b629d-292">To test Identity, add [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) to the Privacy page.</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="b629d-293">Si vous êtes connecté, déconnectez-vous. Exécutez l’application et sélectionnez le lien **confidentialité** .</span><span class="sxs-lookup"><span data-stu-id="b629d-293">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="b629d-294">Vous êtes redirigé vers la page de connexion.</span><span class="sxs-lookup"><span data-stu-id="b629d-294">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="b629d-295">ExplorerIdentity</span><span class="sxs-lookup"><span data-stu-id="b629d-295">Explore Identity</span></span>

<span data-ttu-id="b629d-296">Pour explorer Identity plus en détail :</span><span class="sxs-lookup"><span data-stu-id="b629d-296">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="b629d-297">Créer une source d’interface utilisateur d’identité complète</span><span class="sxs-lookup"><span data-stu-id="b629d-297">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="b629d-298">Examinez la source de chaque page et parcourez le débogueur.</span><span class="sxs-lookup"><span data-stu-id="b629d-298">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a>Identity<span data-ttu-id="b629d-299">-</span><span class="sxs-lookup"><span data-stu-id="b629d-299"> Components</span></span>

<span data-ttu-id="b629d-300">Tous les Identity packages NuGet dépendants sont inclus dans le sous- [package Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="b629d-300">All the Identity dependent NuGet packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="b629d-301">Le package principal pour Identity est [Microsoft. AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)</span><span class="sxs-lookup"><span data-stu-id="b629d-301">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="b629d-302">Ce package contient l’ensemble principal d’interfaces pour ASP.NET Core Identity, et est inclus dans `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span><span class="sxs-lookup"><span data-stu-id="b629d-302">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="b629d-303">Migration vers ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="b629d-303">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="b629d-304">Pour plus d’informations et pour obtenir des conseils Identity sur la migration de votre magasin existant, consultez [migrer l’authentification et Identity ](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="b629d-304">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="b629d-305">Définition de la force du mot de passe</span><span class="sxs-lookup"><span data-stu-id="b629d-305">Setting password strength</span></span>

<span data-ttu-id="b629d-306">Consultez [configuration](#pw) d’un exemple qui définit la configuration minimale requise pour le mot de passe.</span><span class="sxs-lookup"><span data-stu-id="b629d-306">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="next-steps"></a><span data-ttu-id="b629d-307">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="b629d-307">Next Steps</span></span>

* <span data-ttu-id="b629d-308">Pour plus d’informations sur la configuration de Identity à l’aide de SQLite, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/5131) .</span><span class="sxs-lookup"><span data-stu-id="b629d-308">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* <span data-ttu-id="b629d-309">[ConfigurerIdentity](xref:security/authentication/identity-configuration)</span><span class="sxs-lookup"><span data-stu-id="b629d-309">[Configure Identity](xref:security/authentication/identity-configuration)</span></span>
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end
