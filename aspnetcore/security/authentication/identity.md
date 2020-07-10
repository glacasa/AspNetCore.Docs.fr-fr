---
title: Présentation de Identity sur ASP.net Core
author: rick-anderson
description: Utilisez Identity avec une application ASP.net core. Découvrez comment définir les exigences de mot de passe (RequireDigit, RequiredLength, RequiredUniqueChars, etc.).
ms.author: riande
ms.date: 01/15/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/identity
ms.openlocfilehash: 6ac565bfa4862168fa143417ab5a81c51b620f16
ms.sourcegitcommit: 50e7c970f327dbe92d45eaf4c21caa001c9106d0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/10/2020
ms.locfileid: "86212449"
---
# <a name="introduction-to-identity-on-aspnet-core"></a><span data-ttu-id="01683-104">Présentation de Identity sur ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="01683-104">Introduction to Identity on ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="01683-105">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="01683-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="01683-106">ASP.NET Core Identity :</span><span class="sxs-lookup"><span data-stu-id="01683-106">ASP.NET Core Identity:</span></span>

* <span data-ttu-id="01683-107">Est une API qui prend en charge la fonctionnalité de connexion de l’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="01683-107">Is an API that supports user interface (UI) login functionality.</span></span>
* <span data-ttu-id="01683-108">Gère les utilisateurs, les mots de passe, les données de profil, les rôles, les revendications, les jetons, la confirmation par e-mail et bien plus encore.</span><span class="sxs-lookup"><span data-stu-id="01683-108">Manages users, passwords, profile data, roles, claims, tokens, email confirmation, and more.</span></span>

<span data-ttu-id="01683-109">Les utilisateurs peuvent créer un compte avec les informations de connexion stockées dans Identity ou ils peuvent utiliser un fournisseur de connexion externe.</span><span class="sxs-lookup"><span data-stu-id="01683-109">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="01683-110">Les fournisseurs de connexion externes pris en charge incluent [Facebook, Google, Microsoft Account et Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="01683-110">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="01683-111">Le [ Identity code source](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) est disponible sur GitHub.</span><span class="sxs-lookup"><span data-stu-id="01683-111">The [Identity source code](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) is available on GitHub.</span></span> <span data-ttu-id="01683-112">[Structure Identity ](xref:security/authentication/scaffold-identity) et affichez les fichiers générés pour examiner l’interaction du modèle avec Identity .</span><span class="sxs-lookup"><span data-stu-id="01683-112">[Scaffold Identity](xref:security/authentication/scaffold-identity) and view the generated files to review the template interaction with Identity.</span></span>

Identity<span data-ttu-id="01683-113">est généralement configurée à l’aide d’une base de données SQL Server pour stocker les noms d’utilisateur, les mots de passe et les données de profil.</span><span class="sxs-lookup"><span data-stu-id="01683-113"> is typically configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="01683-114">Vous pouvez également utiliser un autre magasin persistant, par exemple, le stockage table Azure.</span><span class="sxs-lookup"><span data-stu-id="01683-114">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="01683-115">Dans cette rubrique, vous allez apprendre à utiliser Identity pour inscrire, se connecter et déconnecter un utilisateur.</span><span class="sxs-lookup"><span data-stu-id="01683-115">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="01683-116">Remarque : les modèles traitent le nom d’utilisateur et l’adresse de messagerie comme les utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="01683-116">Note: the templates treat username and email as the same for users.</span></span> <span data-ttu-id="01683-117">Pour obtenir des instructions plus détaillées sur la création d’applications qui utilisent Identity , consultez la section étapes suivantes à la fin de cet article.</span><span class="sxs-lookup"><span data-stu-id="01683-117">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<span data-ttu-id="01683-118">[Plateforme d’identité Microsoft](/azure/active-directory/develop/) :</span><span class="sxs-lookup"><span data-stu-id="01683-118">[Microsoft identity platform](/azure/active-directory/develop/) is:</span></span>

* <span data-ttu-id="01683-119">Évolution de la plateforme de développement Azure Active Directory (Azure AD).</span><span class="sxs-lookup"><span data-stu-id="01683-119">An evolution of the Azure Active Directory (Azure AD) developer platform.</span></span>
* <span data-ttu-id="01683-120">Non lié à ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="01683-120">Unrelated to ASP.NET Core Identity.</span></span>

[!INCLUDE[](~/includes/Identity<span data-ttu-id="01683-121">Server4.md)]</span><span class="sxs-lookup"><span data-stu-id="01683-121">Server4.md)]</span></span>

<span data-ttu-id="01683-122">[Affichez ou téléchargez l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([procédure de téléchargement)](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="01683-122">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="01683-123">Créer une application Web avec l’authentification</span><span class="sxs-lookup"><span data-stu-id="01683-123">Create a Web app with authentication</span></span>

<span data-ttu-id="01683-124">Créez un projet d’application Web ASP.NET Core avec des comptes d’utilisateur individuels.</span><span class="sxs-lookup"><span data-stu-id="01683-124">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="01683-125">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="01683-125">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="01683-126">Sélectionnez **fichier** > **nouveau** > **projet**.</span><span class="sxs-lookup"><span data-stu-id="01683-126">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="01683-127">Sélectionnez **Application web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="01683-127">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="01683-128">Nommez le projet **application Web 1** pour avoir le même espace de noms que le projet à télécharger.</span><span class="sxs-lookup"><span data-stu-id="01683-128">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="01683-129">Cliquez sur **OK**.</span><span class="sxs-lookup"><span data-stu-id="01683-129">Click **OK**.</span></span>
* <span data-ttu-id="01683-130">Sélectionnez une **application Web**ASP.net Core, puis sélectionnez **modifier l’authentification**.</span><span class="sxs-lookup"><span data-stu-id="01683-130">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="01683-131">Sélectionnez **comptes d’utilisateur individuels** , puis cliquez sur **OK**.</span><span class="sxs-lookup"><span data-stu-id="01683-131">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="01683-132">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="01683-132">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

<span data-ttu-id="01683-133">La commande précédente crée une Razor application Web à l’aide de sqlite.</span><span class="sxs-lookup"><span data-stu-id="01683-133">The preceding command creates a Razor web app using SQLite.</span></span> <span data-ttu-id="01683-134">Pour créer l’application Web avec la base de données locale, exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="01683-134">To create the web app with LocalDB, run the following command:</span></span>

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

<span data-ttu-id="01683-135">Le projet généré fournit [ASP.net Core Identity ](xref:security/authentication/identity) sous forme de [ Razor bibliothèque de classes](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="01683-135">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="01683-136">La Identity Razor bibliothèque de classes expose des points de terminaison avec la `Identity` zone.</span><span class="sxs-lookup"><span data-stu-id="01683-136">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="01683-137">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="01683-137">For example:</span></span>

* <span data-ttu-id="01683-138">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="01683-138">/Identity/Account/Login</span></span>
* <span data-ttu-id="01683-139">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="01683-139">/Identity/Account/Logout</span></span>
* <span data-ttu-id="01683-140">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="01683-140">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="01683-141">Appliquer des migrations</span><span class="sxs-lookup"><span data-stu-id="01683-141">Apply migrations</span></span>

<span data-ttu-id="01683-142">Appliquez les migrations pour initialiser la base de données.</span><span class="sxs-lookup"><span data-stu-id="01683-142">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="01683-143">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="01683-143">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="01683-144">Exécutez la commande suivante dans la console du gestionnaire de package (PMC) :</span><span class="sxs-lookup"><span data-stu-id="01683-144">Run the following command in the Package Manager Console (PMC):</span></span>

`PM> Update-Database`

# <a name="net-core-cli"></a>[<span data-ttu-id="01683-145">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="01683-145">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="01683-146">Les migrations ne sont pas nécessaires pour cette étape lors de l’utilisation de SQLite.</span><span class="sxs-lookup"><span data-stu-id="01683-146">Migrations are not necessary at this step when using SQLite.</span></span>

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="01683-147">Pour la base de données locale, exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="01683-147">For LocalDB, run the following command:</span></span>

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="01683-148">Registre de test et connexion</span><span class="sxs-lookup"><span data-stu-id="01683-148">Test Register and Login</span></span>

<span data-ttu-id="01683-149">Exécutez l’application et inscrivez un utilisateur.</span><span class="sxs-lookup"><span data-stu-id="01683-149">Run the app and register a user.</span></span> <span data-ttu-id="01683-150">Selon la taille de votre écran, vous devrez peut-être sélectionner le bouton bascule de navigation pour afficher les liens de **connexion** et de **Registre** .</span><span class="sxs-lookup"><span data-stu-id="01683-150">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="01683-151">Configurer les Identity services</span><span class="sxs-lookup"><span data-stu-id="01683-151">Configure Identity services</span></span>

<span data-ttu-id="01683-152">Les services sont ajoutés dans `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="01683-152">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="01683-153">Le modèle par défaut consiste à appeler toutes les méthodes `Add{Service}`, puis toutes les méthodes `services.Configure{Service}`.</span><span class="sxs-lookup"><span data-stu-id="01683-153">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=10-99)]

<span data-ttu-id="01683-154">Le code en surbrillance précédent configure Identity avec les valeurs d’option par défaut.</span><span class="sxs-lookup"><span data-stu-id="01683-154">The preceding highlighted code configures Identity with default option values.</span></span> <span data-ttu-id="01683-155">Les services sont mis à la disposition de l’application via l' [injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="01683-155">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

Identity<span data-ttu-id="01683-156">est activé en appelant <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> .</span><span class="sxs-lookup"><span data-stu-id="01683-156"> is enabled by calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>.</span></span> <span data-ttu-id="01683-157">`UseAuthentication`Ajoute l' [intergiciel (middleware](xref:fundamentals/middleware/index) ) d’authentification au pipeline de requête.</span><span class="sxs-lookup"><span data-stu-id="01683-157">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

<span data-ttu-id="01683-158">L’application générée par un modèle n’utilise pas [d’autorisation](xref:security/authorization/secure-data).</span><span class="sxs-lookup"><span data-stu-id="01683-158">The template-generated app doesn't use [authorization](xref:security/authorization/secure-data).</span></span> <span data-ttu-id="01683-159">`app.UseAuthorization`est inclus pour s’assurer qu’il est ajouté dans le bon ordre si l’application ajoute une autorisation.</span><span class="sxs-lookup"><span data-stu-id="01683-159">`app.UseAuthorization` is included to ensure it's added in the correct order should the app add authorization.</span></span> <span data-ttu-id="01683-160">`UseRouting`, `UseAuthentication` , `UseAuthorization` et `UseEndpoints` doivent être appelés dans l’ordre indiqué dans le code précédent.</span><span class="sxs-lookup"><span data-stu-id="01683-160">`UseRouting`, `UseAuthentication`, `UseAuthorization`, and `UseEndpoints` must be called in the order shown in the preceding code.</span></span>

<span data-ttu-id="01683-161">Pour plus d’informations sur `IdentityOptions` et `Startup` , consultez <xref:Microsoft.AspNetCore.Identity.IdentityOptions> et démarrage de l' [application](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="01683-161">For more information on `IdentityOptions` and `Startup`, see <xref:Microsoft.AspNetCore.Identity.IdentityOptions> and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="01683-162">Registre de génération de modèles, connexion et déconnexion</span><span class="sxs-lookup"><span data-stu-id="01683-162">Scaffold Register, Login, and LogOut</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="01683-163">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="01683-163">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="01683-164">Ajoutez les fichiers Register, login et LogOut.</span><span class="sxs-lookup"><span data-stu-id="01683-164">Add the Register, Login, and LogOut files.</span></span> <span data-ttu-id="01683-165">Suivez l' [identité de l’échafaudage dans un Razor projet avec des instructions d’autorisation](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) pour générer le code présenté dans cette section.</span><span class="sxs-lookup"><span data-stu-id="01683-165">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="01683-166">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="01683-166">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="01683-167">Si vous avez créé le projet avec le nom **application Web 1**, exécutez les commandes suivantes.</span><span class="sxs-lookup"><span data-stu-id="01683-167">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="01683-168">Sinon, utilisez l’espace de noms correct pour `ApplicationDbContext` :</span><span class="sxs-lookup"><span data-stu-id="01683-168">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="01683-169">PowerShell utilise un point-virgule comme séparateur de commande.</span><span class="sxs-lookup"><span data-stu-id="01683-169">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="01683-170">Quand vous utilisez PowerShell, échappez les points-virgules dans la liste de fichiers ou placez la liste de fichiers entre guillemets doubles, comme le montre l’exemple précédent.</span><span class="sxs-lookup"><span data-stu-id="01683-170">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

<span data-ttu-id="01683-171">Pour plus d’informations sur la génération de modèles automatique Identity , consultez identification de l' [échafaudage dans un Razor projet avec autorisation](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span><span class="sxs-lookup"><span data-stu-id="01683-171">For more information on scaffolding Identity, see [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="01683-172">Examiner le registre</span><span class="sxs-lookup"><span data-stu-id="01683-172">Examine Register</span></span>

<span data-ttu-id="01683-173">Quand un utilisateur clique sur le lien **Register** , l' `RegisterModel.OnPostAsync` action est appelée.</span><span class="sxs-lookup"><span data-stu-id="01683-173">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="01683-174">L’utilisateur est créé par [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) sur l' `_userManager` objet :</span><span class="sxs-lookup"><span data-stu-id="01683-174">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object:</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

<span data-ttu-id="01683-175">Si l’utilisateur a été créé avec succès, l’utilisateur est connecté par l’appel à `_signInManager.SignInAsync` .</span><span class="sxs-lookup"><span data-stu-id="01683-175">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="01683-176">Consultez [confirmation du compte](xref:security/authentication/accconfirm#prevent-login-at-registration) pour connaître les étapes permettant d’empêcher la connexion immédiate lors de l’inscription.</span><span class="sxs-lookup"><span data-stu-id="01683-176">See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="01683-177">Se connecter</span><span class="sxs-lookup"><span data-stu-id="01683-177">Log in</span></span>

<span data-ttu-id="01683-178">Le formulaire de connexion s’affiche lorsque :</span><span class="sxs-lookup"><span data-stu-id="01683-178">The Login form is displayed when:</span></span>

* <span data-ttu-id="01683-179">Le lien **connexion** est sélectionné.</span><span class="sxs-lookup"><span data-stu-id="01683-179">The **Log in** link is selected.</span></span>
* <span data-ttu-id="01683-180">Un utilisateur tente d’accéder à une page restreinte à laquelle il n’est pas autorisé à accéder **ou** lorsqu’il n’a pas été authentifié par le système.</span><span class="sxs-lookup"><span data-stu-id="01683-180">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="01683-181">Lorsque le formulaire de la page de connexion est envoyé, l' `OnPostAsync` action est appelée.</span><span class="sxs-lookup"><span data-stu-id="01683-181">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="01683-182">`PasswordSignInAsync`est appelé sur l' `_signInManager` objet.</span><span class="sxs-lookup"><span data-stu-id="01683-182">`PasswordSignInAsync` is called on the `_signInManager` object.</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="01683-183">Pour plus d’informations sur la façon de prendre des décisions d’autorisation, consultez <xref:security/authorization/introduction> .</span><span class="sxs-lookup"><span data-stu-id="01683-183">For information on how to make authorization decisions, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="01683-184">Se déconnecter</span><span class="sxs-lookup"><span data-stu-id="01683-184">Log out</span></span>

<span data-ttu-id="01683-185">Le lien de **déconnexion** appelle l' `LogoutModel.OnPost` action.</span><span class="sxs-lookup"><span data-stu-id="01683-185">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Logout.cshtml.cs?highlight=36)]

<span data-ttu-id="01683-186">Dans le code précédent, le code `return RedirectToPage();` doit être une redirection afin que le navigateur exécute une nouvelle demande et que l’identité de l’utilisateur soit mise à jour.</span><span class="sxs-lookup"><span data-stu-id="01683-186">In the preceding code, the code `return RedirectToPage();` needs to be a redirect so that the browser performs a new request and the identity for the user gets updated.</span></span>

<span data-ttu-id="01683-187">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) efface les revendications de l’utilisateur stockées dans un cookie.</span><span class="sxs-lookup"><span data-stu-id="01683-187">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="01683-188">La publication est spécifiée dans *pages/Shared/_LoginPartial. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="01683-188">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-cshtml[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-identity"></a><span data-ttu-id="01683-189">TestIdentity</span><span class="sxs-lookup"><span data-stu-id="01683-189">Test Identity</span></span>

<span data-ttu-id="01683-190">Les modèles de projet Web par défaut autorisent l’accès anonyme aux pages d’hébergement.</span><span class="sxs-lookup"><span data-stu-id="01683-190">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="01683-191">Pour tester Identity , ajoutez [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) :</span><span class="sxs-lookup"><span data-stu-id="01683-191">To test Identity, add [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute):</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="01683-192">Si vous êtes connecté, déconnectez-vous. Exécutez l’application et sélectionnez le lien **confidentialité** .</span><span class="sxs-lookup"><span data-stu-id="01683-192">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="01683-193">Vous êtes redirigé vers la page de connexion.</span><span class="sxs-lookup"><span data-stu-id="01683-193">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="01683-194">ExplorerIdentity</span><span class="sxs-lookup"><span data-stu-id="01683-194">Explore Identity</span></span>

<span data-ttu-id="01683-195">Pour explorer Identity plus en détail :</span><span class="sxs-lookup"><span data-stu-id="01683-195">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="01683-196">Créer une source d’interface utilisateur d’identité complète</span><span class="sxs-lookup"><span data-stu-id="01683-196">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="01683-197">Examinez la source de chaque page et parcourez le débogueur.</span><span class="sxs-lookup"><span data-stu-id="01683-197">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a>Identity<span data-ttu-id="01683-198">-</span><span class="sxs-lookup"><span data-stu-id="01683-198"> Components</span></span>

<span data-ttu-id="01683-199">Tous les Identity packages NuGet dépendants sont inclus dans le [ASP.net Core Framework partagé](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span><span class="sxs-lookup"><span data-stu-id="01683-199">All the Identity-dependent NuGet packages are included in the [ASP.NET Core shared framework](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span></span>

<span data-ttu-id="01683-200">Le package principal pour Identity est [Microsoft. AspNetCore. Identity ](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)</span><span class="sxs-lookup"><span data-stu-id="01683-200">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="01683-201">Ce package contient l’ensemble principal d’interfaces pour ASP.NET Core Identity , et est inclus dans `Microsoft.AspNetCore.Identity.EntityFrameworkCore` .</span><span class="sxs-lookup"><span data-stu-id="01683-201">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="01683-202">Migration vers ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="01683-202">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="01683-203">Pour plus d’informations et pour obtenir des conseils sur la migration de votre Identity magasin existant, consultez [ Identity migrer l’authentification et ](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="01683-203">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="01683-204">Définition de la force du mot de passe</span><span class="sxs-lookup"><span data-stu-id="01683-204">Setting password strength</span></span>

<span data-ttu-id="01683-205">Consultez [configuration](#pw) d’un exemple qui définit la configuration minimale requise pour le mot de passe.</span><span class="sxs-lookup"><span data-stu-id="01683-205">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="01683-206">AddDefault Identity et ajouterIdentity</span><span class="sxs-lookup"><span data-stu-id="01683-206">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="01683-207"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*>a été introduite dans ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="01683-207"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="01683-208">L’appel de `AddDefaultIdentity` est similaire à l’appel de ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="01683-208">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="01683-209">Pour plus d’informations, consultez [ Identity source AddDefault](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .</span><span class="sxs-lookup"><span data-stu-id="01683-209">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="prevent-publish-of-static-identity-assets"></a><span data-ttu-id="01683-210">Empêcher la publication d' Identity actifs statiques</span><span class="sxs-lookup"><span data-stu-id="01683-210">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="01683-211">Pour empêcher la publication Identity des ressources statiques (feuilles de style et fichiers JavaScript pour l' Identity interface utilisateur) sur la racine Web, ajoutez la `ResolveStaticWebAssetsInputsDependsOn` propriété et la `RemoveIdentityAssets` cible suivantes au fichier projet de l’application :</span><span class="sxs-lookup"><span data-stu-id="01683-211">To prevent publishing static Identity assets (stylesheets and JavaScript files for Identity UI) to the web root, add the following `ResolveStaticWebAssetsInputsDependsOn` property and `RemoveIdentityAssets` target to the app's project file:</span></span>

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

## <a name="next-steps"></a><span data-ttu-id="01683-212">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="01683-212">Next Steps</span></span>

* <span data-ttu-id="01683-213">[IdentityCode source ASP.net Core](https://github.com/dotnet/aspnetcore/tree/master/src/Identity)</span><span class="sxs-lookup"><span data-stu-id="01683-213">[ASP.NET Core Identity source code](https://github.com/dotnet/aspnetcore/tree/master/src/Identity)</span></span>
* <span data-ttu-id="01683-214">Pour plus d’informations sur la configuration de à l’aide de SQLite, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/5131) Identity .</span><span class="sxs-lookup"><span data-stu-id="01683-214">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* <span data-ttu-id="01683-215">[ConfigurerIdentity](xref:security/authentication/identity-configuration)</span><span class="sxs-lookup"><span data-stu-id="01683-215">[Configure Identity](xref:security/authentication/identity-configuration)</span></span>
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="01683-216">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="01683-216">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="01683-217">ASP.NET Core Identity est un système d’appartenance qui ajoute des fonctionnalités de connexion à des applications ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="01683-217">ASP.NET Core Identity is a membership system that adds login functionality to ASP.NET Core apps.</span></span> <span data-ttu-id="01683-218">Les utilisateurs peuvent créer un compte avec les informations de connexion stockées dans Identity ou ils peuvent utiliser un fournisseur de connexion externe.</span><span class="sxs-lookup"><span data-stu-id="01683-218">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="01683-219">Les fournisseurs de connexion externes pris en charge incluent [Facebook, Google, Microsoft Account et Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="01683-219">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

Identity<span data-ttu-id="01683-220">peut être configuré à l’aide d’une base de données SQL Server pour stocker les noms d’utilisateur, les mots de passe et les données de profil.</span><span class="sxs-lookup"><span data-stu-id="01683-220"> can be configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="01683-221">Vous pouvez également utiliser un autre magasin persistant, par exemple, le stockage table Azure.</span><span class="sxs-lookup"><span data-stu-id="01683-221">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="01683-222">[Affichez ou téléchargez l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([procédure de téléchargement)](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="01683-222">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="01683-223">Dans cette rubrique, vous allez apprendre à utiliser Identity pour inscrire, se connecter et déconnecter un utilisateur.</span><span class="sxs-lookup"><span data-stu-id="01683-223">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="01683-224">Pour obtenir des instructions plus détaillées sur la création d’applications qui utilisent Identity , consultez la section étapes suivantes à la fin de cet article.</span><span class="sxs-lookup"><span data-stu-id="01683-224">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<a name="adi"></a>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="01683-225">AddDefault Identity et ajouterIdentity</span><span class="sxs-lookup"><span data-stu-id="01683-225">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="01683-226"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*>a été introduite dans ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="01683-226"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="01683-227">L’appel de `AddDefaultIdentity` est similaire à l’appel de ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="01683-227">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="01683-228">Pour plus d’informations, consultez [ Identity source AddDefault](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .</span><span class="sxs-lookup"><span data-stu-id="01683-228">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="01683-229">Créer une application Web avec l’authentification</span><span class="sxs-lookup"><span data-stu-id="01683-229">Create a Web app with authentication</span></span>

<span data-ttu-id="01683-230">Créez un projet d’application Web ASP.NET Core avec des comptes d’utilisateur individuels.</span><span class="sxs-lookup"><span data-stu-id="01683-230">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="01683-231">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="01683-231">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="01683-232">Sélectionnez **fichier** > **nouveau** > **projet**.</span><span class="sxs-lookup"><span data-stu-id="01683-232">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="01683-233">Sélectionnez **Application web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="01683-233">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="01683-234">Nommez le projet **application Web 1** pour avoir le même espace de noms que le projet à télécharger.</span><span class="sxs-lookup"><span data-stu-id="01683-234">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="01683-235">Cliquez sur **OK**.</span><span class="sxs-lookup"><span data-stu-id="01683-235">Click **OK**.</span></span>
* <span data-ttu-id="01683-236">Sélectionnez une **application Web**ASP.net Core, puis sélectionnez **modifier l’authentification**.</span><span class="sxs-lookup"><span data-stu-id="01683-236">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="01683-237">Sélectionnez **comptes d’utilisateur individuels** , puis cliquez sur **OK**.</span><span class="sxs-lookup"><span data-stu-id="01683-237">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="01683-238">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="01683-238">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

<span data-ttu-id="01683-239">Le projet généré fournit [ASP.net Core Identity ](xref:security/authentication/identity) sous forme de [ Razor bibliothèque de classes](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="01683-239">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="01683-240">La Identity Razor bibliothèque de classes expose des points de terminaison avec la `Identity` zone.</span><span class="sxs-lookup"><span data-stu-id="01683-240">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="01683-241">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="01683-241">For example:</span></span>

* <span data-ttu-id="01683-242">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="01683-242">/Identity/Account/Login</span></span>
* <span data-ttu-id="01683-243">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="01683-243">/Identity/Account/Logout</span></span>
* <span data-ttu-id="01683-244">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="01683-244">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="01683-245">Appliquer des migrations</span><span class="sxs-lookup"><span data-stu-id="01683-245">Apply migrations</span></span>

<span data-ttu-id="01683-246">Appliquez les migrations pour initialiser la base de données.</span><span class="sxs-lookup"><span data-stu-id="01683-246">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="01683-247">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="01683-247">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="01683-248">Exécutez la commande suivante dans la console du gestionnaire de package (PMC) :</span><span class="sxs-lookup"><span data-stu-id="01683-248">Run the following command in the Package Manager Console (PMC):</span></span>

```powershell
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="01683-249">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="01683-249">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="01683-250">Registre de test et connexion</span><span class="sxs-lookup"><span data-stu-id="01683-250">Test Register and Login</span></span>

<span data-ttu-id="01683-251">Exécutez l’application et inscrivez un utilisateur.</span><span class="sxs-lookup"><span data-stu-id="01683-251">Run the app and register a user.</span></span> <span data-ttu-id="01683-252">Selon la taille de votre écran, vous devrez peut-être sélectionner le bouton bascule de navigation pour afficher les liens de **connexion** et de **Registre** .</span><span class="sxs-lookup"><span data-stu-id="01683-252">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="01683-253">Configurer les Identity services</span><span class="sxs-lookup"><span data-stu-id="01683-253">Configure Identity services</span></span>

<span data-ttu-id="01683-254">Les services sont ajoutés dans `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="01683-254">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="01683-255">Le modèle par défaut consiste à appeler toutes les méthodes `Add{Service}`, puis toutes les méthodes `services.Configure{Service}`.</span><span class="sxs-lookup"><span data-stu-id="01683-255">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

<span data-ttu-id="01683-256">Le code précédent configure Identity avec les valeurs d’option par défaut.</span><span class="sxs-lookup"><span data-stu-id="01683-256">The preceding code configures Identity with default option values.</span></span> <span data-ttu-id="01683-257">Les services sont mis à la disposition de l’application via l' [injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="01683-257">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

Identity<span data-ttu-id="01683-258">est activé en appelant [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="01683-258"> is enabled by calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="01683-259">`UseAuthentication`Ajoute l' [intergiciel (middleware](xref:fundamentals/middleware/index) ) d’authentification au pipeline de requête.</span><span class="sxs-lookup"><span data-stu-id="01683-259">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

<span data-ttu-id="01683-260">Pour plus d’informations, consultez la [ Identity classe options](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) et le démarrage de l' [application](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="01683-260">For more information, see the [IdentityOptions Class](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="01683-261">Registre de génération de modèles, connexion et déconnexion</span><span class="sxs-lookup"><span data-stu-id="01683-261">Scaffold Register, Login, and LogOut</span></span>

<span data-ttu-id="01683-262">Suivez l' [identité de l’échafaudage dans un Razor projet avec des instructions d’autorisation](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) pour générer le code présenté dans cette section.</span><span class="sxs-lookup"><span data-stu-id="01683-262">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="01683-263">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="01683-263">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="01683-264">Ajoutez les fichiers Register, login et LogOut.</span><span class="sxs-lookup"><span data-stu-id="01683-264">Add the Register, Login, and LogOut files.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="01683-265">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="01683-265">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="01683-266">Si vous avez créé le projet avec le nom **application Web 1**, exécutez les commandes suivantes.</span><span class="sxs-lookup"><span data-stu-id="01683-266">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="01683-267">Sinon, utilisez l’espace de noms correct pour `ApplicationDbContext` :</span><span class="sxs-lookup"><span data-stu-id="01683-267">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="01683-268">PowerShell utilise un point-virgule comme séparateur de commande.</span><span class="sxs-lookup"><span data-stu-id="01683-268">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="01683-269">Quand vous utilisez PowerShell, échappez les points-virgules dans la liste de fichiers ou placez la liste de fichiers entre guillemets doubles, comme le montre l’exemple précédent.</span><span class="sxs-lookup"><span data-stu-id="01683-269">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="01683-270">Examiner le registre</span><span class="sxs-lookup"><span data-stu-id="01683-270">Examine Register</span></span>

<span data-ttu-id="01683-271">Quand un utilisateur clique sur le lien **Register** , l' `RegisterModel.OnPostAsync` action est appelée.</span><span class="sxs-lookup"><span data-stu-id="01683-271">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="01683-272">L’utilisateur est créé par [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) sur l' `_userManager` objet :</span><span class="sxs-lookup"><span data-stu-id="01683-272">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object:</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

<span data-ttu-id="01683-273">Si l’utilisateur a été créé avec succès, l’utilisateur est connecté par l’appel à `_signInManager.SignInAsync` .</span><span class="sxs-lookup"><span data-stu-id="01683-273">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="01683-274">**Remarque :** Consultez [confirmation du compte](xref:security/authentication/accconfirm#prevent-login-at-registration) pour connaître les étapes permettant d’empêcher la connexion immédiate lors de l’inscription.</span><span class="sxs-lookup"><span data-stu-id="01683-274">**Note:** See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="01683-275">Se connecter</span><span class="sxs-lookup"><span data-stu-id="01683-275">Log in</span></span>

<span data-ttu-id="01683-276">Le formulaire de connexion s’affiche lorsque :</span><span class="sxs-lookup"><span data-stu-id="01683-276">The Login form is displayed when:</span></span>

* <span data-ttu-id="01683-277">Le lien **connexion** est sélectionné.</span><span class="sxs-lookup"><span data-stu-id="01683-277">The **Log in** link is selected.</span></span>
* <span data-ttu-id="01683-278">Un utilisateur tente d’accéder à une page restreinte à laquelle il n’est pas autorisé à accéder **ou** lorsqu’il n’a pas été authentifié par le système.</span><span class="sxs-lookup"><span data-stu-id="01683-278">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="01683-279">Lorsque le formulaire de la page de connexion est envoyé, l' `OnPostAsync` action est appelée.</span><span class="sxs-lookup"><span data-stu-id="01683-279">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="01683-280">`PasswordSignInAsync`est appelé sur l' `_signInManager` objet.</span><span class="sxs-lookup"><span data-stu-id="01683-280">`PasswordSignInAsync` is called on the `_signInManager` object.</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="01683-281">Pour plus d’informations sur la façon de prendre des décisions d’autorisation, consultez <xref:security/authorization/introduction> .</span><span class="sxs-lookup"><span data-stu-id="01683-281">For information on how to make authorization decisions, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="01683-282">Se déconnecter</span><span class="sxs-lookup"><span data-stu-id="01683-282">Log out</span></span>

<span data-ttu-id="01683-283">Le lien de **déconnexion** appelle l' `LogoutModel.OnPost` action.</span><span class="sxs-lookup"><span data-stu-id="01683-283">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

<span data-ttu-id="01683-284">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) efface les revendications de l’utilisateur stockées dans un cookie.</span><span class="sxs-lookup"><span data-stu-id="01683-284">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="01683-285">La publication est spécifiée dans *pages/Shared/_LoginPartial. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="01683-285">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-cshtml[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-identity"></a><span data-ttu-id="01683-286">TestIdentity</span><span class="sxs-lookup"><span data-stu-id="01683-286">Test Identity</span></span>

<span data-ttu-id="01683-287">Les modèles de projet Web par défaut autorisent l’accès anonyme aux pages d’hébergement.</span><span class="sxs-lookup"><span data-stu-id="01683-287">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="01683-288">Pour tester Identity , ajoutez [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) à la page confidentialité.</span><span class="sxs-lookup"><span data-stu-id="01683-288">To test Identity, add [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) to the Privacy page.</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="01683-289">Si vous êtes connecté, déconnectez-vous. Exécutez l’application et sélectionnez le lien **confidentialité** .</span><span class="sxs-lookup"><span data-stu-id="01683-289">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="01683-290">Vous êtes redirigé vers la page de connexion.</span><span class="sxs-lookup"><span data-stu-id="01683-290">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="01683-291">ExplorerIdentity</span><span class="sxs-lookup"><span data-stu-id="01683-291">Explore Identity</span></span>

<span data-ttu-id="01683-292">Pour explorer Identity plus en détail :</span><span class="sxs-lookup"><span data-stu-id="01683-292">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="01683-293">Créer une source d’interface utilisateur d’identité complète</span><span class="sxs-lookup"><span data-stu-id="01683-293">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="01683-294">Examinez la source de chaque page et parcourez le débogueur.</span><span class="sxs-lookup"><span data-stu-id="01683-294">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a>Identity<span data-ttu-id="01683-295">-</span><span class="sxs-lookup"><span data-stu-id="01683-295"> Components</span></span>

<span data-ttu-id="01683-296">Tous les Identity packages NuGet dépendants sont inclus dans le sous- [package Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="01683-296">All the Identity dependent NuGet packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="01683-297">Le package principal pour Identity est [Microsoft. AspNetCore. Identity ](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)</span><span class="sxs-lookup"><span data-stu-id="01683-297">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="01683-298">Ce package contient l’ensemble principal d’interfaces pour ASP.NET Core Identity , et est inclus dans `Microsoft.AspNetCore.Identity.EntityFrameworkCore` .</span><span class="sxs-lookup"><span data-stu-id="01683-298">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="01683-299">Migration vers ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="01683-299">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="01683-300">Pour plus d’informations et pour obtenir des conseils sur la migration de votre Identity magasin existant, consultez [ Identity migrer l’authentification et ](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="01683-300">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="01683-301">Définition de la force du mot de passe</span><span class="sxs-lookup"><span data-stu-id="01683-301">Setting password strength</span></span>

<span data-ttu-id="01683-302">Consultez [configuration](#pw) d’un exemple qui définit la configuration minimale requise pour le mot de passe.</span><span class="sxs-lookup"><span data-stu-id="01683-302">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="next-steps"></a><span data-ttu-id="01683-303">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="01683-303">Next Steps</span></span>

* <span data-ttu-id="01683-304">Pour plus d’informations sur la configuration de à l’aide de SQLite, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/5131) Identity .</span><span class="sxs-lookup"><span data-stu-id="01683-304">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* <span data-ttu-id="01683-305">[ConfigurerIdentity](xref:security/authentication/identity-configuration)</span><span class="sxs-lookup"><span data-stu-id="01683-305">[Configure Identity](xref:security/authentication/identity-configuration)</span></span>
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end
