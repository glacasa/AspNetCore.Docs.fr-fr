---
title: Présentation de Identity sur ASP.net Core
author: rick-anderson
description: Utilisez Identity avec une application ASP.net core. Découvrez comment définir les exigences de mot de passe (RequireDigit, RequiredLength, RequiredUniqueChars, etc.).
ms.author: riande
ms.date: 7/15/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/identity
ms.openlocfilehash: dd3296db568700a363c427398f02239846a46ada
ms.sourcegitcommit: 384833762c614851db653b841cc09fbc944da463
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/17/2020
ms.locfileid: "86445427"
---
# <a name="introduction-to-identity-on-aspnet-core"></a><span data-ttu-id="3f1c2-104">Présentation de Identity sur ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="3f1c2-104">Introduction to Identity on ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3f1c2-105">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="3f1c2-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="3f1c2-106">ASP.NET Core Identity :</span><span class="sxs-lookup"><span data-stu-id="3f1c2-106">ASP.NET Core Identity:</span></span>

* <span data-ttu-id="3f1c2-107">Est une API qui prend en charge la fonctionnalité de connexion de l’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="3f1c2-107">Is an API that supports user interface (UI) login functionality.</span></span>
* <span data-ttu-id="3f1c2-108">Gère les utilisateurs, les mots de passe, les données de profil, les rôles, les revendications, les jetons, la confirmation par e-mail et bien plus encore.</span><span class="sxs-lookup"><span data-stu-id="3f1c2-108">Manages users, passwords, profile data, roles, claims, tokens, email confirmation, and more.</span></span>

<span data-ttu-id="3f1c2-109">Les utilisateurs peuvent créer un compte avec les informations de connexion stockées dans Identity ou ils peuvent utiliser un fournisseur de connexion externe.</span><span class="sxs-lookup"><span data-stu-id="3f1c2-109">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="3f1c2-110">Les fournisseurs de connexion externes pris en charge incluent [Facebook, Google, Microsoft Account et Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="3f1c2-110">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="3f1c2-111">Le [ Identity code source](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) est disponible sur GitHub.</span><span class="sxs-lookup"><span data-stu-id="3f1c2-111">The [Identity source code](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) is available on GitHub.</span></span> <span data-ttu-id="3f1c2-112">[Structure Identity ](xref:security/authentication/scaffold-identity) et affichez les fichiers générés pour examiner l’interaction du modèle avec Identity .</span><span class="sxs-lookup"><span data-stu-id="3f1c2-112">[Scaffold Identity](xref:security/authentication/scaffold-identity) and view the generated files to review the template interaction with Identity.</span></span>

Identity<span data-ttu-id="3f1c2-113">est généralement configurée à l’aide d’une base de données SQL Server pour stocker les noms d’utilisateur, les mots de passe et les données de profil.</span><span class="sxs-lookup"><span data-stu-id="3f1c2-113"> is typically configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="3f1c2-114">Vous pouvez également utiliser un autre magasin persistant, par exemple, le stockage table Azure.</span><span class="sxs-lookup"><span data-stu-id="3f1c2-114">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="3f1c2-115">Dans cette rubrique, vous allez apprendre à utiliser Identity pour inscrire, se connecter et déconnecter un utilisateur.</span><span class="sxs-lookup"><span data-stu-id="3f1c2-115">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="3f1c2-116">Remarque : les modèles traitent le nom d’utilisateur et l’adresse de messagerie comme les utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="3f1c2-116">Note: the templates treat username and email as the same for users.</span></span> <span data-ttu-id="3f1c2-117">Pour obtenir des instructions plus détaillées sur la création d’applications qui utilisent Identity , consultez [étapes suivantes](#next).</span><span class="sxs-lookup"><span data-stu-id="3f1c2-117">For more detailed instructions about creating apps that use Identity, see [Next Steps](#next).</span></span>

<span data-ttu-id="3f1c2-118">[Plateforme d’identité Microsoft](/azure/active-directory/develop/) :</span><span class="sxs-lookup"><span data-stu-id="3f1c2-118">[Microsoft identity platform](/azure/active-directory/develop/) is:</span></span>

* <span data-ttu-id="3f1c2-119">Évolution de la plateforme de développement Azure Active Directory (Azure AD).</span><span class="sxs-lookup"><span data-stu-id="3f1c2-119">An evolution of the Azure Active Directory (Azure AD) developer platform.</span></span>
* <span data-ttu-id="3f1c2-120">Non lié à ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="3f1c2-120">Unrelated to ASP.NET Core Identity.</span></span>

[!INCLUDE[](~/includes/Identity<span data-ttu-id="3f1c2-121">Server4.md)]</span><span class="sxs-lookup"><span data-stu-id="3f1c2-121">Server4.md)]</span></span>

<span data-ttu-id="3f1c2-122">[Affichez ou téléchargez l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([procédure de téléchargement)](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="3f1c2-122">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="3f1c2-123">Créer une application Web avec l’authentification</span><span class="sxs-lookup"><span data-stu-id="3f1c2-123">Create a Web app with authentication</span></span>

<span data-ttu-id="3f1c2-124">Créez un projet d’application Web ASP.NET Core avec des comptes d’utilisateur individuels.</span><span class="sxs-lookup"><span data-stu-id="3f1c2-124">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3f1c2-125">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3f1c2-125">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3f1c2-126">Sélectionnez **fichier** > **nouveau** > **projet**.</span><span class="sxs-lookup"><span data-stu-id="3f1c2-126">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="3f1c2-127">Sélectionnez **Application web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="3f1c2-127">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="3f1c2-128">Nommez le projet **application Web 1** pour avoir le même espace de noms que le projet à télécharger.</span><span class="sxs-lookup"><span data-stu-id="3f1c2-128">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="3f1c2-129">Cliquez sur **OK**.</span><span class="sxs-lookup"><span data-stu-id="3f1c2-129">Click **OK**.</span></span>
* <span data-ttu-id="3f1c2-130">Sélectionnez une **application Web**ASP.net Core, puis sélectionnez **modifier l’authentification**.</span><span class="sxs-lookup"><span data-stu-id="3f1c2-130">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="3f1c2-131">Sélectionnez **comptes d’utilisateur individuels** , puis cliquez sur **OK**.</span><span class="sxs-lookup"><span data-stu-id="3f1c2-131">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="3f1c2-132">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="3f1c2-132">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

<span data-ttu-id="3f1c2-133">La commande précédente crée une Razor application Web à l’aide de sqlite.</span><span class="sxs-lookup"><span data-stu-id="3f1c2-133">The preceding command creates a Razor web app using SQLite.</span></span> <span data-ttu-id="3f1c2-134">Pour créer l’application Web avec la base de données locale, exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="3f1c2-134">To create the web app with LocalDB, run the following command:</span></span>

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

<span data-ttu-id="3f1c2-135">Le projet généré fournit [ASP.net Core Identity ](xref:security/authentication/identity) sous forme de [ Razor bibliothèque de classes](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="3f1c2-135">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="3f1c2-136">La Identity Razor bibliothèque de classes expose des points de terminaison avec la `Identity` zone.</span><span class="sxs-lookup"><span data-stu-id="3f1c2-136">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="3f1c2-137">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="3f1c2-137">For example:</span></span>

* <span data-ttu-id="3f1c2-138">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="3f1c2-138">/Identity/Account/Login</span></span>
* <span data-ttu-id="3f1c2-139">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="3f1c2-139">/Identity/Account/Logout</span></span>
* <span data-ttu-id="3f1c2-140">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="3f1c2-140">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="3f1c2-141">Appliquer des migrations</span><span class="sxs-lookup"><span data-stu-id="3f1c2-141">Apply migrations</span></span>

<span data-ttu-id="3f1c2-142">Appliquez les migrations pour initialiser la base de données.</span><span class="sxs-lookup"><span data-stu-id="3f1c2-142">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3f1c2-143">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3f1c2-143">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3f1c2-144">Exécutez la commande suivante dans la console du gestionnaire de package (PMC) :</span><span class="sxs-lookup"><span data-stu-id="3f1c2-144">Run the following command in the Package Manager Console (PMC):</span></span>

`PM> Update-Database`

# <a name="net-core-cli"></a>[<span data-ttu-id="3f1c2-145">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="3f1c2-145">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="3f1c2-146">Les migrations ne sont pas nécessaires pour cette étape lors de l’utilisation de SQLite.</span><span class="sxs-lookup"><span data-stu-id="3f1c2-146">Migrations are not necessary at this step when using SQLite.</span></span>

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="3f1c2-147">Pour la base de données locale, exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="3f1c2-147">For LocalDB, run the following command:</span></span>

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="3f1c2-148">Registre de test et connexion</span><span class="sxs-lookup"><span data-stu-id="3f1c2-148">Test Register and Login</span></span>

<span data-ttu-id="3f1c2-149">Exécutez l’application et inscrivez un utilisateur.</span><span class="sxs-lookup"><span data-stu-id="3f1c2-149">Run the app and register a user.</span></span> <span data-ttu-id="3f1c2-150">Selon la taille de votre écran, vous devrez peut-être sélectionner le bouton bascule de navigation pour afficher les liens de **connexion** et de **Registre** .</span><span class="sxs-lookup"><span data-stu-id="3f1c2-150">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="3f1c2-151">Configurer les Identity services</span><span class="sxs-lookup"><span data-stu-id="3f1c2-151">Configure Identity services</span></span>

<span data-ttu-id="3f1c2-152">Les services sont ajoutés dans `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="3f1c2-152">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="3f1c2-153">Le modèle par défaut consiste à appeler toutes les méthodes `Add{Service}`, puis toutes les méthodes `services.Configure{Service}`.</span><span class="sxs-lookup"><span data-stu-id="3f1c2-153">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=11-99)]

<span data-ttu-id="3f1c2-154">Le code en surbrillance précédent configure Identity avec les valeurs d’option par défaut.</span><span class="sxs-lookup"><span data-stu-id="3f1c2-154">The preceding highlighted code configures Identity with default option values.</span></span> <span data-ttu-id="3f1c2-155">Les services sont mis à la disposition de l’application via l' [injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="3f1c2-155">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

Identity<span data-ttu-id="3f1c2-156">est activé en appelant <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> .</span><span class="sxs-lookup"><span data-stu-id="3f1c2-156"> is enabled by calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>.</span></span> <span data-ttu-id="3f1c2-157">`UseAuthentication`Ajoute l' [intergiciel (middleware](xref:fundamentals/middleware/index) ) d’authentification au pipeline de requête.</span><span class="sxs-lookup"><span data-stu-id="3f1c2-157">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

<span data-ttu-id="3f1c2-158">L’application générée par un modèle n’utilise pas [d’autorisation](xref:security/authorization/secure-data).</span><span class="sxs-lookup"><span data-stu-id="3f1c2-158">The template-generated app doesn't use [authorization](xref:security/authorization/secure-data).</span></span> <span data-ttu-id="3f1c2-159">`app.UseAuthorization`est inclus pour s’assurer qu’il est ajouté dans le bon ordre si l’application ajoute une autorisation.</span><span class="sxs-lookup"><span data-stu-id="3f1c2-159">`app.UseAuthorization` is included to ensure it's added in the correct order should the app add authorization.</span></span> <span data-ttu-id="3f1c2-160">`UseRouting`, `UseAuthentication` , `UseAuthorization` et `UseEndpoints` doivent être appelés dans l’ordre indiqué dans le code précédent.</span><span class="sxs-lookup"><span data-stu-id="3f1c2-160">`UseRouting`, `UseAuthentication`, `UseAuthorization`, and `UseEndpoints` must be called in the order shown in the preceding code.</span></span>

<span data-ttu-id="3f1c2-161">Pour plus d’informations sur `IdentityOptions` et `Startup` , consultez <xref:Microsoft.AspNetCore.Identity.IdentityOptions> et démarrage de l' [application](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="3f1c2-161">For more information on `IdentityOptions` and `Startup`, see <xref:Microsoft.AspNetCore.Identity.IdentityOptions> and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-logout-and-registerconfirmation"></a><span data-ttu-id="3f1c2-162">Registre de génération de modèles, connexion, déconnexion et RegisterConfirmation</span><span class="sxs-lookup"><span data-stu-id="3f1c2-162">Scaffold Register, Login, LogOut, and RegisterConfirmation</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3f1c2-163">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3f1c2-163">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3f1c2-164">Ajoutez les `Register` fichiers,, `Login` `LogOut` et `RegisterConfirmation` .</span><span class="sxs-lookup"><span data-stu-id="3f1c2-164">Add the `Register`, `Login`, `LogOut`, and `RegisterConfirmation` files.</span></span> <span data-ttu-id="3f1c2-165">Suivez l' [identité de l’échafaudage dans un Razor projet avec des instructions d’autorisation](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) pour générer le code présenté dans cette section.</span><span class="sxs-lookup"><span data-stu-id="3f1c2-165">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="3f1c2-166">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="3f1c2-166">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="3f1c2-167">Si vous avez créé le projet avec le nom **application Web 1**, exécutez les commandes suivantes.</span><span class="sxs-lookup"><span data-stu-id="3f1c2-167">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="3f1c2-168">Sinon, utilisez l’espace de noms correct pour `ApplicationDbContext` :</span><span class="sxs-lookup"><span data-stu-id="3f1c2-168">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout;Account.RegisterConfirmation"
```

<span data-ttu-id="3f1c2-169">PowerShell utilise un point-virgule comme séparateur de commande.</span><span class="sxs-lookup"><span data-stu-id="3f1c2-169">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="3f1c2-170">Quand vous utilisez PowerShell, échappez les points-virgules dans la liste de fichiers ou placez la liste de fichiers entre guillemets doubles, comme le montre l’exemple précédent.</span><span class="sxs-lookup"><span data-stu-id="3f1c2-170">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

<span data-ttu-id="3f1c2-171">Pour plus d’informations sur la génération de modèles automatique Identity , consultez identification de l' [échafaudage dans un Razor projet avec autorisation](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span><span class="sxs-lookup"><span data-stu-id="3f1c2-171">For more information on scaffolding Identity, see [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="3f1c2-172">Examiner le registre</span><span class="sxs-lookup"><span data-stu-id="3f1c2-172">Examine Register</span></span>

<span data-ttu-id="3f1c2-173">Quand un utilisateur clique sur le bouton **Register** de la `Register` page, l' `RegisterModel.OnPostAsync` action est appelée.</span><span class="sxs-lookup"><span data-stu-id="3f1c2-173">When a user clicks the **Register** button on the `Register` page, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="3f1c2-174">L’utilisateur est créé par [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) sur l' `_userManager` objet :</span><span class="sxs-lookup"><span data-stu-id="3f1c2-174">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object:</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

<!-- .NET 5 fixes this, see
https://github.com/dotnet/aspnetcore/blob/master/src/Identity/UI/src/Areas/Identity/Pages/V4/Account/RegisterConfirmation.cshtml.cs#L74-L77
-->
[!INCLUDE[](~/includes/disableVer.md)]

### <a name="log-in"></a><span data-ttu-id="3f1c2-175">Se connecter</span><span class="sxs-lookup"><span data-stu-id="3f1c2-175">Log in</span></span>

<span data-ttu-id="3f1c2-176">Le formulaire de connexion s’affiche lorsque :</span><span class="sxs-lookup"><span data-stu-id="3f1c2-176">The Login form is displayed when:</span></span>

* <span data-ttu-id="3f1c2-177">Le lien **connexion** est sélectionné.</span><span class="sxs-lookup"><span data-stu-id="3f1c2-177">The **Log in** link is selected.</span></span>
* <span data-ttu-id="3f1c2-178">Un utilisateur tente d’accéder à une page restreinte à laquelle il n’est pas autorisé à accéder **ou** lorsqu’il n’a pas été authentifié par le système.</span><span class="sxs-lookup"><span data-stu-id="3f1c2-178">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="3f1c2-179">Lorsque le formulaire de la page de connexion est envoyé, l' `OnPostAsync` action est appelée.</span><span class="sxs-lookup"><span data-stu-id="3f1c2-179">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="3f1c2-180">`PasswordSignInAsync`est appelé sur l' `_signInManager` objet.</span><span class="sxs-lookup"><span data-stu-id="3f1c2-180">`PasswordSignInAsync` is called on the `_signInManager` object.</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="3f1c2-181">Pour plus d’informations sur la façon de prendre des décisions d’autorisation, consultez <xref:security/authorization/introduction> .</span><span class="sxs-lookup"><span data-stu-id="3f1c2-181">For information on how to make authorization decisions, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="3f1c2-182">Se déconnecter</span><span class="sxs-lookup"><span data-stu-id="3f1c2-182">Log out</span></span>

<span data-ttu-id="3f1c2-183">Le lien de **déconnexion** appelle l' `LogoutModel.OnPost` action.</span><span class="sxs-lookup"><span data-stu-id="3f1c2-183">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Logout.cshtml.cs?highlight=36)]

<span data-ttu-id="3f1c2-184">Dans le code précédent, le code `return RedirectToPage();` doit être une redirection afin que le navigateur exécute une nouvelle demande et que l’identité de l’utilisateur soit mise à jour.</span><span class="sxs-lookup"><span data-stu-id="3f1c2-184">In the preceding code, the code `return RedirectToPage();` needs to be a redirect so that the browser performs a new request and the identity for the user gets updated.</span></span>

<span data-ttu-id="3f1c2-185">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) efface les revendications de l’utilisateur stockées dans un cookie.</span><span class="sxs-lookup"><span data-stu-id="3f1c2-185">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="3f1c2-186">La publication est spécifiée dans *pages/Shared/_LoginPartial. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="3f1c2-186">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-cshtml[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-identity"></a><span data-ttu-id="3f1c2-187">TestIdentity</span><span class="sxs-lookup"><span data-stu-id="3f1c2-187">Test Identity</span></span>

<span data-ttu-id="3f1c2-188">Les modèles de projet Web par défaut autorisent l’accès anonyme aux pages d’hébergement.</span><span class="sxs-lookup"><span data-stu-id="3f1c2-188">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="3f1c2-189">Pour tester Identity , ajoutez [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) :</span><span class="sxs-lookup"><span data-stu-id="3f1c2-189">To test Identity, add [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute):</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="3f1c2-190">Si vous êtes connecté, déconnectez-vous. Exécutez l’application et sélectionnez le lien **confidentialité** .</span><span class="sxs-lookup"><span data-stu-id="3f1c2-190">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="3f1c2-191">Vous êtes redirigé vers la page de connexion.</span><span class="sxs-lookup"><span data-stu-id="3f1c2-191">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="3f1c2-192">ExplorerIdentity</span><span class="sxs-lookup"><span data-stu-id="3f1c2-192">Explore Identity</span></span>

<span data-ttu-id="3f1c2-193">Pour explorer Identity plus en détail :</span><span class="sxs-lookup"><span data-stu-id="3f1c2-193">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="3f1c2-194">Créer une source d’interface utilisateur d’identité complète</span><span class="sxs-lookup"><span data-stu-id="3f1c2-194">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="3f1c2-195">Examinez la source de chaque page et parcourez le débogueur.</span><span class="sxs-lookup"><span data-stu-id="3f1c2-195">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a>Identity<span data-ttu-id="3f1c2-196">-</span><span class="sxs-lookup"><span data-stu-id="3f1c2-196"> Components</span></span>

<span data-ttu-id="3f1c2-197">Tous les Identity packages NuGet dépendants sont inclus dans le [ASP.net Core Framework partagé](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span><span class="sxs-lookup"><span data-stu-id="3f1c2-197">All the Identity-dependent NuGet packages are included in the [ASP.NET Core shared framework](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span></span>

<span data-ttu-id="3f1c2-198">Le package principal pour Identity est [Microsoft. AspNetCore. Identity ](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)</span><span class="sxs-lookup"><span data-stu-id="3f1c2-198">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="3f1c2-199">Ce package contient l’ensemble principal d’interfaces pour ASP.NET Core Identity , et est inclus dans `Microsoft.AspNetCore.Identity.EntityFrameworkCore` .</span><span class="sxs-lookup"><span data-stu-id="3f1c2-199">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="3f1c2-200">Migration vers ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="3f1c2-200">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="3f1c2-201">Pour plus d’informations et pour obtenir des conseils sur la migration de votre Identity magasin existant, consultez [ Identity migrer l’authentification et ](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="3f1c2-201">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="3f1c2-202">Définition de la force du mot de passe</span><span class="sxs-lookup"><span data-stu-id="3f1c2-202">Setting password strength</span></span>

<span data-ttu-id="3f1c2-203">Consultez [configuration](#pw) d’un exemple qui définit la configuration minimale requise pour le mot de passe.</span><span class="sxs-lookup"><span data-stu-id="3f1c2-203">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="3f1c2-204">AddDefault Identity et ajouterIdentity</span><span class="sxs-lookup"><span data-stu-id="3f1c2-204">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="3f1c2-205"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*>a été introduite dans ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="3f1c2-205"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="3f1c2-206">L’appel de `AddDefaultIdentity` est similaire à l’appel de ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="3f1c2-206">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="3f1c2-207">Pour plus d’informations, consultez [ Identity source AddDefault](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .</span><span class="sxs-lookup"><span data-stu-id="3f1c2-207">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="prevent-publish-of-static-identity-assets"></a><span data-ttu-id="3f1c2-208">Empêcher la publication d' Identity actifs statiques</span><span class="sxs-lookup"><span data-stu-id="3f1c2-208">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="3f1c2-209">Pour empêcher la publication Identity des ressources statiques (feuilles de style et fichiers JavaScript pour l' Identity interface utilisateur) sur la racine Web, ajoutez la `ResolveStaticWebAssetsInputsDependsOn` propriété et la `RemoveIdentityAssets` cible suivantes au fichier projet de l’application :</span><span class="sxs-lookup"><span data-stu-id="3f1c2-209">To prevent publishing static Identity assets (stylesheets and JavaScript files for Identity UI) to the web root, add the following `ResolveStaticWebAssetsInputsDependsOn` property and `RemoveIdentityAssets` target to the app's project file:</span></span>

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

<a name="next"></a>

## <a name="next-steps"></a><span data-ttu-id="3f1c2-210">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="3f1c2-210">Next Steps</span></span>

* <span data-ttu-id="3f1c2-211">[IdentityCode source ASP.net Core](https://github.com/dotnet/aspnetcore/tree/master/src/Identity)</span><span class="sxs-lookup"><span data-stu-id="3f1c2-211">[ASP.NET Core Identity source code](https://github.com/dotnet/aspnetcore/tree/master/src/Identity)</span></span>
* <span data-ttu-id="3f1c2-212">Pour plus d’informations sur la configuration de à l’aide de SQLite, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/5131) Identity .</span><span class="sxs-lookup"><span data-stu-id="3f1c2-212">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* <span data-ttu-id="3f1c2-213">[ConfigurerIdentity](xref:security/authentication/identity-configuration)</span><span class="sxs-lookup"><span data-stu-id="3f1c2-213">[Configure Identity](xref:security/authentication/identity-configuration)</span></span>
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="3f1c2-214">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="3f1c2-214">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="3f1c2-215">ASP.NET Core Identity est un système d’appartenance qui ajoute des fonctionnalités de connexion à des applications ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="3f1c2-215">ASP.NET Core Identity is a membership system that adds login functionality to ASP.NET Core apps.</span></span> <span data-ttu-id="3f1c2-216">Les utilisateurs peuvent créer un compte avec les informations de connexion stockées dans Identity ou ils peuvent utiliser un fournisseur de connexion externe.</span><span class="sxs-lookup"><span data-stu-id="3f1c2-216">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="3f1c2-217">Les fournisseurs de connexion externes pris en charge incluent [Facebook, Google, Microsoft Account et Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="3f1c2-217">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

Identity<span data-ttu-id="3f1c2-218">peut être configuré à l’aide d’une base de données SQL Server pour stocker les noms d’utilisateur, les mots de passe et les données de profil.</span><span class="sxs-lookup"><span data-stu-id="3f1c2-218"> can be configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="3f1c2-219">Vous pouvez également utiliser un autre magasin persistant, par exemple, le stockage table Azure.</span><span class="sxs-lookup"><span data-stu-id="3f1c2-219">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="3f1c2-220">[Affichez ou téléchargez l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([procédure de téléchargement)](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="3f1c2-220">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="3f1c2-221">Dans cette rubrique, vous allez apprendre à utiliser Identity pour inscrire, se connecter et déconnecter un utilisateur.</span><span class="sxs-lookup"><span data-stu-id="3f1c2-221">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="3f1c2-222">Pour obtenir des instructions plus détaillées sur la création d’applications qui utilisent Identity , consultez la section étapes suivantes à la fin de cet article.</span><span class="sxs-lookup"><span data-stu-id="3f1c2-222">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<a name="adi"></a>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="3f1c2-223">AddDefault Identity et ajouterIdentity</span><span class="sxs-lookup"><span data-stu-id="3f1c2-223">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="3f1c2-224"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*>a été introduite dans ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="3f1c2-224"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="3f1c2-225">L’appel de `AddDefaultIdentity` est similaire à l’appel de ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="3f1c2-225">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="3f1c2-226">Pour plus d’informations, consultez [ Identity source AddDefault](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .</span><span class="sxs-lookup"><span data-stu-id="3f1c2-226">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="3f1c2-227">Créer une application Web avec l’authentification</span><span class="sxs-lookup"><span data-stu-id="3f1c2-227">Create a Web app with authentication</span></span>

<span data-ttu-id="3f1c2-228">Créez un projet d’application Web ASP.NET Core avec des comptes d’utilisateur individuels.</span><span class="sxs-lookup"><span data-stu-id="3f1c2-228">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3f1c2-229">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3f1c2-229">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3f1c2-230">Sélectionnez **fichier** > **nouveau** > **projet**.</span><span class="sxs-lookup"><span data-stu-id="3f1c2-230">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="3f1c2-231">Sélectionnez **Application web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="3f1c2-231">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="3f1c2-232">Nommez le projet **application Web 1** pour avoir le même espace de noms que le projet à télécharger.</span><span class="sxs-lookup"><span data-stu-id="3f1c2-232">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="3f1c2-233">Cliquez sur **OK**.</span><span class="sxs-lookup"><span data-stu-id="3f1c2-233">Click **OK**.</span></span>
* <span data-ttu-id="3f1c2-234">Sélectionnez une **application Web**ASP.net Core, puis sélectionnez **modifier l’authentification**.</span><span class="sxs-lookup"><span data-stu-id="3f1c2-234">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="3f1c2-235">Sélectionnez **comptes d’utilisateur individuels** , puis cliquez sur **OK**.</span><span class="sxs-lookup"><span data-stu-id="3f1c2-235">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="3f1c2-236">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="3f1c2-236">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

<span data-ttu-id="3f1c2-237">Le projet généré fournit [ASP.net Core Identity ](xref:security/authentication/identity) sous forme de [ Razor bibliothèque de classes](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="3f1c2-237">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="3f1c2-238">La Identity Razor bibliothèque de classes expose des points de terminaison avec la `Identity` zone.</span><span class="sxs-lookup"><span data-stu-id="3f1c2-238">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="3f1c2-239">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="3f1c2-239">For example:</span></span>

* <span data-ttu-id="3f1c2-240">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="3f1c2-240">/Identity/Account/Login</span></span>
* <span data-ttu-id="3f1c2-241">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="3f1c2-241">/Identity/Account/Logout</span></span>
* <span data-ttu-id="3f1c2-242">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="3f1c2-242">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="3f1c2-243">Appliquer des migrations</span><span class="sxs-lookup"><span data-stu-id="3f1c2-243">Apply migrations</span></span>

<span data-ttu-id="3f1c2-244">Appliquez les migrations pour initialiser la base de données.</span><span class="sxs-lookup"><span data-stu-id="3f1c2-244">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3f1c2-245">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3f1c2-245">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3f1c2-246">Exécutez la commande suivante dans la console du gestionnaire de package (PMC) :</span><span class="sxs-lookup"><span data-stu-id="3f1c2-246">Run the following command in the Package Manager Console (PMC):</span></span>

```powershell
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="3f1c2-247">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="3f1c2-247">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="3f1c2-248">Registre de test et connexion</span><span class="sxs-lookup"><span data-stu-id="3f1c2-248">Test Register and Login</span></span>

<span data-ttu-id="3f1c2-249">Exécutez l’application et inscrivez un utilisateur.</span><span class="sxs-lookup"><span data-stu-id="3f1c2-249">Run the app and register a user.</span></span> <span data-ttu-id="3f1c2-250">Selon la taille de votre écran, vous devrez peut-être sélectionner le bouton bascule de navigation pour afficher les liens de **connexion** et de **Registre** .</span><span class="sxs-lookup"><span data-stu-id="3f1c2-250">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="3f1c2-251">Configurer les Identity services</span><span class="sxs-lookup"><span data-stu-id="3f1c2-251">Configure Identity services</span></span>

<span data-ttu-id="3f1c2-252">Les services sont ajoutés dans `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="3f1c2-252">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="3f1c2-253">Le modèle par défaut consiste à appeler toutes les méthodes `Add{Service}`, puis toutes les méthodes `services.Configure{Service}`.</span><span class="sxs-lookup"><span data-stu-id="3f1c2-253">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

<span data-ttu-id="3f1c2-254">Le code précédent configure Identity avec les valeurs d’option par défaut.</span><span class="sxs-lookup"><span data-stu-id="3f1c2-254">The preceding code configures Identity with default option values.</span></span> <span data-ttu-id="3f1c2-255">Les services sont mis à la disposition de l’application via l' [injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="3f1c2-255">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

Identity<span data-ttu-id="3f1c2-256">est activé en appelant [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="3f1c2-256"> is enabled by calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="3f1c2-257">`UseAuthentication`Ajoute l' [intergiciel (middleware](xref:fundamentals/middleware/index) ) d’authentification au pipeline de requête.</span><span class="sxs-lookup"><span data-stu-id="3f1c2-257">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

<span data-ttu-id="3f1c2-258">Pour plus d’informations, consultez la [ Identity classe options](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) et le démarrage de l' [application](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="3f1c2-258">For more information, see the [IdentityOptions Class](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="3f1c2-259">Registre de génération de modèles, connexion et déconnexion</span><span class="sxs-lookup"><span data-stu-id="3f1c2-259">Scaffold Register, Login, and LogOut</span></span>

<span data-ttu-id="3f1c2-260">Suivez l' [identité de l’échafaudage dans un Razor projet avec des instructions d’autorisation](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) pour générer le code présenté dans cette section.</span><span class="sxs-lookup"><span data-stu-id="3f1c2-260">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3f1c2-261">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3f1c2-261">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3f1c2-262">Ajoutez les fichiers Register, login et LogOut.</span><span class="sxs-lookup"><span data-stu-id="3f1c2-262">Add the Register, Login, and LogOut files.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="3f1c2-263">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="3f1c2-263">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="3f1c2-264">Si vous avez créé le projet avec le nom **application Web 1**, exécutez les commandes suivantes.</span><span class="sxs-lookup"><span data-stu-id="3f1c2-264">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="3f1c2-265">Sinon, utilisez l’espace de noms correct pour `ApplicationDbContext` :</span><span class="sxs-lookup"><span data-stu-id="3f1c2-265">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="3f1c2-266">PowerShell utilise un point-virgule comme séparateur de commande.</span><span class="sxs-lookup"><span data-stu-id="3f1c2-266">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="3f1c2-267">Quand vous utilisez PowerShell, échappez les points-virgules dans la liste de fichiers ou placez la liste de fichiers entre guillemets doubles, comme le montre l’exemple précédent.</span><span class="sxs-lookup"><span data-stu-id="3f1c2-267">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="3f1c2-268">Examiner le registre</span><span class="sxs-lookup"><span data-stu-id="3f1c2-268">Examine Register</span></span>

<span data-ttu-id="3f1c2-269">Quand un utilisateur clique sur le lien **Register** , l' `RegisterModel.OnPostAsync` action est appelée.</span><span class="sxs-lookup"><span data-stu-id="3f1c2-269">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="3f1c2-270">L’utilisateur est créé par [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) sur l' `_userManager` objet :</span><span class="sxs-lookup"><span data-stu-id="3f1c2-270">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object:</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

<span data-ttu-id="3f1c2-271">Si l’utilisateur a été créé avec succès, l’utilisateur est connecté par l’appel à `_signInManager.SignInAsync` .</span><span class="sxs-lookup"><span data-stu-id="3f1c2-271">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="3f1c2-272">**Remarque :** Consultez [confirmation du compte](xref:security/authentication/accconfirm#prevent-login-at-registration) pour connaître les étapes permettant d’empêcher la connexion immédiate lors de l’inscription.</span><span class="sxs-lookup"><span data-stu-id="3f1c2-272">**Note:** See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="3f1c2-273">Se connecter</span><span class="sxs-lookup"><span data-stu-id="3f1c2-273">Log in</span></span>

<span data-ttu-id="3f1c2-274">Le formulaire de connexion s’affiche lorsque :</span><span class="sxs-lookup"><span data-stu-id="3f1c2-274">The Login form is displayed when:</span></span>

* <span data-ttu-id="3f1c2-275">Le lien **connexion** est sélectionné.</span><span class="sxs-lookup"><span data-stu-id="3f1c2-275">The **Log in** link is selected.</span></span>
* <span data-ttu-id="3f1c2-276">Un utilisateur tente d’accéder à une page restreinte à laquelle il n’est pas autorisé à accéder **ou** lorsqu’il n’a pas été authentifié par le système.</span><span class="sxs-lookup"><span data-stu-id="3f1c2-276">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="3f1c2-277">Lorsque le formulaire de la page de connexion est envoyé, l' `OnPostAsync` action est appelée.</span><span class="sxs-lookup"><span data-stu-id="3f1c2-277">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="3f1c2-278">`PasswordSignInAsync`est appelé sur l' `_signInManager` objet.</span><span class="sxs-lookup"><span data-stu-id="3f1c2-278">`PasswordSignInAsync` is called on the `_signInManager` object.</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="3f1c2-279">Pour plus d’informations sur la façon de prendre des décisions d’autorisation, consultez <xref:security/authorization/introduction> .</span><span class="sxs-lookup"><span data-stu-id="3f1c2-279">For information on how to make authorization decisions, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="3f1c2-280">Se déconnecter</span><span class="sxs-lookup"><span data-stu-id="3f1c2-280">Log out</span></span>

<span data-ttu-id="3f1c2-281">Le lien de **déconnexion** appelle l' `LogoutModel.OnPost` action.</span><span class="sxs-lookup"><span data-stu-id="3f1c2-281">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

<span data-ttu-id="3f1c2-282">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) efface les revendications de l’utilisateur stockées dans un cookie.</span><span class="sxs-lookup"><span data-stu-id="3f1c2-282">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="3f1c2-283">La publication est spécifiée dans *pages/Shared/_LoginPartial. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="3f1c2-283">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-cshtml[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-identity"></a><span data-ttu-id="3f1c2-284">TestIdentity</span><span class="sxs-lookup"><span data-stu-id="3f1c2-284">Test Identity</span></span>

<span data-ttu-id="3f1c2-285">Les modèles de projet Web par défaut autorisent l’accès anonyme aux pages d’hébergement.</span><span class="sxs-lookup"><span data-stu-id="3f1c2-285">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="3f1c2-286">Pour tester Identity , ajoutez [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) à la page confidentialité.</span><span class="sxs-lookup"><span data-stu-id="3f1c2-286">To test Identity, add [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) to the Privacy page.</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="3f1c2-287">Si vous êtes connecté, déconnectez-vous. Exécutez l’application et sélectionnez le lien **confidentialité** .</span><span class="sxs-lookup"><span data-stu-id="3f1c2-287">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="3f1c2-288">Vous êtes redirigé vers la page de connexion.</span><span class="sxs-lookup"><span data-stu-id="3f1c2-288">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="3f1c2-289">ExplorerIdentity</span><span class="sxs-lookup"><span data-stu-id="3f1c2-289">Explore Identity</span></span>

<span data-ttu-id="3f1c2-290">Pour explorer Identity plus en détail :</span><span class="sxs-lookup"><span data-stu-id="3f1c2-290">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="3f1c2-291">Créer une source d’interface utilisateur d’identité complète</span><span class="sxs-lookup"><span data-stu-id="3f1c2-291">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="3f1c2-292">Examinez la source de chaque page et parcourez le débogueur.</span><span class="sxs-lookup"><span data-stu-id="3f1c2-292">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a>Identity<span data-ttu-id="3f1c2-293">-</span><span class="sxs-lookup"><span data-stu-id="3f1c2-293"> Components</span></span>

<span data-ttu-id="3f1c2-294">Tous les Identity packages NuGet dépendants sont inclus dans le sous- [package Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="3f1c2-294">All the Identity dependent NuGet packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="3f1c2-295">Le package principal pour Identity est [Microsoft. AspNetCore. Identity ](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)</span><span class="sxs-lookup"><span data-stu-id="3f1c2-295">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="3f1c2-296">Ce package contient l’ensemble principal d’interfaces pour ASP.NET Core Identity , et est inclus dans `Microsoft.AspNetCore.Identity.EntityFrameworkCore` .</span><span class="sxs-lookup"><span data-stu-id="3f1c2-296">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="3f1c2-297">Migration vers ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="3f1c2-297">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="3f1c2-298">Pour plus d’informations et pour obtenir des conseils sur la migration de votre Identity magasin existant, consultez [ Identity migrer l’authentification et ](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="3f1c2-298">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="3f1c2-299">Définition de la force du mot de passe</span><span class="sxs-lookup"><span data-stu-id="3f1c2-299">Setting password strength</span></span>

<span data-ttu-id="3f1c2-300">Consultez [configuration](#pw) d’un exemple qui définit la configuration minimale requise pour le mot de passe.</span><span class="sxs-lookup"><span data-stu-id="3f1c2-300">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="next-steps"></a><span data-ttu-id="3f1c2-301">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="3f1c2-301">Next Steps</span></span>

* <span data-ttu-id="3f1c2-302">Pour plus d’informations sur la configuration de à l’aide de SQLite, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/5131) Identity .</span><span class="sxs-lookup"><span data-stu-id="3f1c2-302">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* <span data-ttu-id="3f1c2-303">[ConfigurerIdentity](xref:security/authentication/identity-configuration)</span><span class="sxs-lookup"><span data-stu-id="3f1c2-303">[Configure Identity](xref:security/authentication/identity-configuration)</span></span>
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end
