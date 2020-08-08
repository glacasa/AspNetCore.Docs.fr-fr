---
title: Stockage sécurisé des secrets d’application en développement dans ASP.NET Core
author: rick-anderson
description: Découvrez comment stocker et récupérer des informations sensibles en tant que secrets d’application lors du développement d’une application ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 4/20/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/app-secrets
ms.openlocfilehash: 917e698d34a5d4b6c2c3f4737c08f1a590f5df1a
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88017946"
---
# <a name="safe-storage-of-app-secrets-in-development-in-aspnet-core"></a><span data-ttu-id="e981c-103">Stockage sécurisé des secrets d’application en développement dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e981c-103">Safe storage of app secrets in development in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e981c-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), [Daniel Roth](https://github.com/danroth27)et [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="e981c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="e981c-105">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e981c-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="e981c-106">Ce document décrit les techniques de stockage et de récupération des données sensibles lors du développement d’une application ASP.NET Core sur un ordinateur de développement.</span><span class="sxs-lookup"><span data-stu-id="e981c-106">This document explains techniques for storing and retrieving sensitive data during development of an ASP.NET Core app on a development machine.</span></span> <span data-ttu-id="e981c-107">Ne stockez jamais de mots de passe ou d’autres données sensibles dans le code source.</span><span class="sxs-lookup"><span data-stu-id="e981c-107">Never store passwords or other sensitive data in source code.</span></span> <span data-ttu-id="e981c-108">Les secrets de production ne doivent pas être utilisés à des fins de développement ou de test.</span><span class="sxs-lookup"><span data-stu-id="e981c-108">Production secrets shouldn't be used for development or test.</span></span> <span data-ttu-id="e981c-109">Les secrets ne doivent pas être déployés avec l’application.</span><span class="sxs-lookup"><span data-stu-id="e981c-109">Secrets shouldn't be deployed with the app.</span></span> <span data-ttu-id="e981c-110">Au lieu de cela, les secrets doivent être mis à disposition dans l’environnement de production par un moyen contrôlé, comme les variables d’environnement, les Azure Key Vault, etc. Vous pouvez stocker et protéger les secrets de test et de production Azure à l’aide du [fournisseur de configuration Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="e981c-110">Instead, secrets should be made available in the production environment through a controlled means like environment variables, Azure Key Vault, etc. You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

## <a name="environment-variables"></a><span data-ttu-id="e981c-111">Variables d'environnement</span><span class="sxs-lookup"><span data-stu-id="e981c-111">Environment variables</span></span>

<span data-ttu-id="e981c-112">Les variables d’environnement sont utilisées pour éviter le stockage des secrets d’application dans le code ou dans des fichiers de configuration locaux.</span><span class="sxs-lookup"><span data-stu-id="e981c-112">Environment variables are used to avoid storage of app secrets in code or in local configuration files.</span></span> <span data-ttu-id="e981c-113">Les variables d’environnement remplacent les valeurs de configuration pour toutes les sources de configuration spécifiées précédemment.</span><span class="sxs-lookup"><span data-stu-id="e981c-113">Environment variables override configuration values for all previously specified configuration sources.</span></span>

<span data-ttu-id="e981c-114">Prenons l’exemple d’une application Web ASP.NET Core dans laquelle la sécurité **des comptes d’utilisateur individuels** est activée.</span><span class="sxs-lookup"><span data-stu-id="e981c-114">Consider an ASP.NET Core web app in which **Individual User Accounts** security is enabled.</span></span> <span data-ttu-id="e981c-115">Une chaîne de connexion de base de données par défaut est incluse dans le fichier *appsettings.js* du projet avec la clé `DefaultConnection` .</span><span class="sxs-lookup"><span data-stu-id="e981c-115">A default database connection string is included in the project's *appsettings.json* file with the key `DefaultConnection`.</span></span> <span data-ttu-id="e981c-116">La chaîne de connexion par défaut est pour la base de données locale, qui s’exécute en mode utilisateur et ne nécessite pas de mot de passe.</span><span class="sxs-lookup"><span data-stu-id="e981c-116">The default connection string is for LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="e981c-117">Pendant le déploiement de l’application, la `DefaultConnection` valeur de clé peut être remplacée par la valeur d’une variable d’environnement.</span><span class="sxs-lookup"><span data-stu-id="e981c-117">During app deployment, the `DefaultConnection` key value can be overridden with an environment variable's value.</span></span> <span data-ttu-id="e981c-118">La variable d’environnement peut stocker la chaîne de connexion complète avec des informations d’identification sensibles.</span><span class="sxs-lookup"><span data-stu-id="e981c-118">The environment variable may store the complete connection string with sensitive credentials.</span></span>

> [!WARNING]
> <span data-ttu-id="e981c-119">Les variables d’environnement sont généralement stockées en texte brut et non chiffré.</span><span class="sxs-lookup"><span data-stu-id="e981c-119">Environment variables are generally stored in plain, unencrypted text.</span></span> <span data-ttu-id="e981c-120">Si l’ordinateur ou le processus est compromis, les variables d’environnement sont accessibles aux parties non fiables.</span><span class="sxs-lookup"><span data-stu-id="e981c-120">If the machine or process is compromised, environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="e981c-121">Des mesures supplémentaires pour empêcher la divulgation de secrets d’utilisateur peuvent être nécessaires.</span><span class="sxs-lookup"><span data-stu-id="e981c-121">Additional measures to prevent disclosure of user secrets may be required.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a><span data-ttu-id="e981c-122">Gestionnaire de secret</span><span class="sxs-lookup"><span data-stu-id="e981c-122">Secret Manager</span></span>

<span data-ttu-id="e981c-123">L’outil secret Manager stocke les données sensibles pendant le développement d’un projet ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e981c-123">The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.</span></span> <span data-ttu-id="e981c-124">Dans ce contexte, un élément de données sensibles est un secret d’application.</span><span class="sxs-lookup"><span data-stu-id="e981c-124">In this context, a piece of sensitive data is an app secret.</span></span> <span data-ttu-id="e981c-125">Les secrets de l’application sont stockés dans un emplacement distinct de l’arborescence du projet.</span><span class="sxs-lookup"><span data-stu-id="e981c-125">App secrets are stored in a separate location from the project tree.</span></span> <span data-ttu-id="e981c-126">Les secrets de l’application sont associés à un projet spécifique ou partagés entre plusieurs projets.</span><span class="sxs-lookup"><span data-stu-id="e981c-126">The app secrets are associated with a specific project or shared across several projects.</span></span> <span data-ttu-id="e981c-127">Les secrets de l’application ne sont pas archivés dans le contrôle de code source.</span><span class="sxs-lookup"><span data-stu-id="e981c-127">The app secrets aren't checked into source control.</span></span>

> [!WARNING]
> <span data-ttu-id="e981c-128">L’outil Gestionnaire de secret ne chiffre pas les secrets stockés et ne doit pas être traité comme un magasin approuvé.</span><span class="sxs-lookup"><span data-stu-id="e981c-128">The Secret Manager tool doesn't encrypt the stored secrets and shouldn't be treated as a trusted store.</span></span> <span data-ttu-id="e981c-129">À des fins de développement uniquement.</span><span class="sxs-lookup"><span data-stu-id="e981c-129">It's for development purposes only.</span></span> <span data-ttu-id="e981c-130">Les clés et les valeurs sont stockées dans un fichier de configuration JSON dans le répertoire du profil utilisateur.</span><span class="sxs-lookup"><span data-stu-id="e981c-130">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="e981c-131">Fonctionnement de l’outil secret Manager</span><span class="sxs-lookup"><span data-stu-id="e981c-131">How the Secret Manager tool works</span></span>

<span data-ttu-id="e981c-132">L’outil Gestionnaire de secret résume les détails de l’implémentation, tels que l’emplacement et la manière dont les valeurs sont stockées.</span><span class="sxs-lookup"><span data-stu-id="e981c-132">The Secret Manager tool abstracts away the implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="e981c-133">Vous pouvez utiliser l’outil sans connaître ces détails d’implémentation.</span><span class="sxs-lookup"><span data-stu-id="e981c-133">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="e981c-134">Les valeurs sont stockées dans un fichier de configuration JSON dans un dossier de profil utilisateur protégé du système sur l’ordinateur local :</span><span class="sxs-lookup"><span data-stu-id="e981c-134">The values are stored in a JSON configuration file in a system-protected user profile folder on the local machine:</span></span>

# <a name="windows"></a>[<span data-ttu-id="e981c-135">Windows</span><span class="sxs-lookup"><span data-stu-id="e981c-135">Windows</span></span>](#tab/windows)

<span data-ttu-id="e981c-136">Chemin d’accès au système de fichiers :</span><span class="sxs-lookup"><span data-stu-id="e981c-136">File system path:</span></span>

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[<span data-ttu-id="e981c-137">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="e981c-137">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="e981c-138">Chemin d’accès au système de fichiers :</span><span class="sxs-lookup"><span data-stu-id="e981c-138">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

<span data-ttu-id="e981c-139">Dans les chemins d’accès de fichier précédents, remplacez `<user_secrets_id>` par la `UserSecretsId` valeur spécifiée dans le fichier *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="e981c-139">In the preceding file paths, replace `<user_secrets_id>` with the `UserSecretsId` value specified in the *.csproj* file.</span></span>

<span data-ttu-id="e981c-140">N’écrivez pas de code dépendant de l’emplacement ou du format des données enregistrées avec l’outil secret Manager.</span><span class="sxs-lookup"><span data-stu-id="e981c-140">Don't write code that depends on the location or format of data saved with the Secret Manager tool.</span></span> <span data-ttu-id="e981c-141">Ces détails d’implémentation peuvent changer.</span><span class="sxs-lookup"><span data-stu-id="e981c-141">These implementation details may change.</span></span> <span data-ttu-id="e981c-142">Par exemple, les valeurs secrètes ne sont pas chiffrées, mais peuvent être dans le futur.</span><span class="sxs-lookup"><span data-stu-id="e981c-142">For example, the secret values aren't encrypted, but could be in the future.</span></span>

## <a name="enable-secret-storage"></a><span data-ttu-id="e981c-143">Activer le stockage secret</span><span class="sxs-lookup"><span data-stu-id="e981c-143">Enable secret storage</span></span>

<span data-ttu-id="e981c-144">L’outil Gestionnaire de secret fonctionne sur les paramètres de configuration spécifiques au projet stockés dans votre profil utilisateur.</span><span class="sxs-lookup"><span data-stu-id="e981c-144">The Secret Manager tool operates on project-specific configuration settings stored in your user profile.</span></span>

<span data-ttu-id="e981c-145">L’outil Gestionnaire de secret comprend une `init` commande dans kit SDK .net Core 3.0.100 ou version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="e981c-145">The Secret Manager tool includes an `init` command in .NET Core SDK 3.0.100 or later.</span></span> <span data-ttu-id="e981c-146">Pour utiliser des secrets d’utilisateur, exécutez la commande suivante dans le répertoire du projet :</span><span class="sxs-lookup"><span data-stu-id="e981c-146">To use user secrets, run the following command in the project directory:</span></span>

```dotnetcli
dotnet user-secrets init
```

<span data-ttu-id="e981c-147">La commande précédente ajoute un `UserSecretsId` élément dans un `PropertyGroup` du fichier *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="e981c-147">The preceding command adds a `UserSecretsId` element within a `PropertyGroup` of the *.csproj* file.</span></span> <span data-ttu-id="e981c-148">Par défaut, le texte interne de `UserSecretsId` est un GUID.</span><span class="sxs-lookup"><span data-stu-id="e981c-148">By default, the inner text of `UserSecretsId` is a GUID.</span></span> <span data-ttu-id="e981c-149">Le texte interne est arbitraire, mais il est unique pour le projet.</span><span class="sxs-lookup"><span data-stu-id="e981c-149">The inner text is arbitrary, but is unique to the project.</span></span>

[!code-xml[](app-secrets/samples/3.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

<span data-ttu-id="e981c-150">Dans Visual Studio, cliquez avec le bouton droit sur le projet dans Explorateur de solutions, puis sélectionnez **gérer les secrets d’utilisateur** dans le menu contextuel.</span><span class="sxs-lookup"><span data-stu-id="e981c-150">In Visual Studio, right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="e981c-151">Ce geste ajoute un `UserSecretsId` élément, rempli avec un GUID, au fichier *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="e981c-151">This gesture adds a `UserSecretsId` element, populated with a GUID, to the *.csproj* file.</span></span>

## <a name="set-a-secret"></a><span data-ttu-id="e981c-152">Définir une clé secrète</span><span class="sxs-lookup"><span data-stu-id="e981c-152">Set a secret</span></span>

<span data-ttu-id="e981c-153">Définissez un secret d’application comprenant une clé et sa valeur.</span><span class="sxs-lookup"><span data-stu-id="e981c-153">Define an app secret consisting of a key and its value.</span></span> <span data-ttu-id="e981c-154">Le secret est associé à la valeur du projet `UserSecretsId` .</span><span class="sxs-lookup"><span data-stu-id="e981c-154">The secret is associated with the project's `UserSecretsId` value.</span></span> <span data-ttu-id="e981c-155">Par exemple, exécutez la commande suivante à partir du répertoire dans lequel se trouve le fichier *. csproj* :</span><span class="sxs-lookup"><span data-stu-id="e981c-155">For example, run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

<span data-ttu-id="e981c-156">Dans l’exemple précédent, le signe deux-points indique qu' `Movies` il s’agit d’un littéral d’objet avec une `ServiceApiKey` propriété.</span><span class="sxs-lookup"><span data-stu-id="e981c-156">In the preceding example, the colon denotes that `Movies` is an object literal with a `ServiceApiKey` property.</span></span>

<span data-ttu-id="e981c-157">L’outil Gestionnaire de secret peut également être utilisé à partir d’autres annuaires.</span><span class="sxs-lookup"><span data-stu-id="e981c-157">The Secret Manager tool can be used from other directories too.</span></span> <span data-ttu-id="e981c-158">Utilisez l' `--project` option pour fournir le chemin d’accès au système de fichiers où se trouve le fichier *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="e981c-158">Use the `--project` option to supply the file system path at which the *.csproj* file exists.</span></span> <span data-ttu-id="e981c-159">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="e981c-159">For example:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a><span data-ttu-id="e981c-160">Aplatissement de la structure JSON dans Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e981c-160">JSON structure flattening in Visual Studio</span></span>

<span data-ttu-id="e981c-161">Le geste **gérer les secrets** de l’utilisateur de Visual Studio ouvre un *secrets.jssur* le fichier dans l’éditeur de texte.</span><span class="sxs-lookup"><span data-stu-id="e981c-161">Visual Studio's **Manage User Secrets** gesture opens a *secrets.json* file in the text editor.</span></span> <span data-ttu-id="e981c-162">Remplacez le contenu de *secrets.js* par les paires clé-valeur à stocker.</span><span class="sxs-lookup"><span data-stu-id="e981c-162">Replace the contents of *secrets.json* with the key-value pairs to be stored.</span></span> <span data-ttu-id="e981c-163">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="e981c-163">For example:</span></span>

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="e981c-164">La structure JSON est aplatie après des modifications via `dotnet user-secrets remove` ou `dotnet user-secrets set` .</span><span class="sxs-lookup"><span data-stu-id="e981c-164">The JSON structure is flattened after modifications via `dotnet user-secrets remove` or `dotnet user-secrets set`.</span></span> <span data-ttu-id="e981c-165">Par exemple, l’exécution de `dotnet user-secrets remove "Movies:ConnectionString"` réduit le `Movies` littéral d’objet.</span><span class="sxs-lookup"><span data-stu-id="e981c-165">For example, running `dotnet user-secrets remove "Movies:ConnectionString"` collapses the `Movies` object literal.</span></span> <span data-ttu-id="e981c-166">Le fichier modifié ressemble à ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="e981c-166">The modified file resembles the following:</span></span>

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a><span data-ttu-id="e981c-167">Définir plusieurs secrets</span><span class="sxs-lookup"><span data-stu-id="e981c-167">Set multiple secrets</span></span>

<span data-ttu-id="e981c-168">Un lot de secrets peut être défini par la canalisation JSON à la `set` commande.</span><span class="sxs-lookup"><span data-stu-id="e981c-168">A batch of secrets can be set by piping JSON to the `set` command.</span></span> <span data-ttu-id="e981c-169">Dans l’exemple suivant, le *input.js* le contenu du fichier est dirigé vers la `set` commande.</span><span class="sxs-lookup"><span data-stu-id="e981c-169">In the following example, the *input.json* file's contents are piped to the `set` command.</span></span>

# <a name="windows"></a>[<span data-ttu-id="e981c-170">Windows</span><span class="sxs-lookup"><span data-stu-id="e981c-170">Windows</span></span>](#tab/windows)

<span data-ttu-id="e981c-171">Ouvrez une interface de commande, puis exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="e981c-171">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[<span data-ttu-id="e981c-172">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="e981c-172">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="e981c-173">Ouvrez une interface de commande, puis exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="e981c-173">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a><span data-ttu-id="e981c-174">Accéder à une clé secrète</span><span class="sxs-lookup"><span data-stu-id="e981c-174">Access a secret</span></span>

<span data-ttu-id="e981c-175">L' [API de Configuration ASP.net Core](xref:fundamentals/configuration/index) permet d’accéder aux secrets du gestionnaire de secret.</span><span class="sxs-lookup"><span data-stu-id="e981c-175">The [ASP.NET Core Configuration API](xref:fundamentals/configuration/index) provides access to Secret Manager secrets.</span></span>

<span data-ttu-id="e981c-176">La source de configuration des secrets de l’utilisateur est automatiquement ajoutée en mode de développement lorsque le projet appelle <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> pour initialiser une nouvelle instance de l’hôte avec des valeurs par défaut préconfigurées.</span><span class="sxs-lookup"><span data-stu-id="e981c-176">The user secrets configuration source is automatically added in development mode when the project calls <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> to initialize a new instance of the host with preconfigured defaults.</span></span> <span data-ttu-id="e981c-177">`CreateDefaultBuilder`appelle <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> lorsque <xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName> est <xref:Microsoft.Extensions.Hosting.EnvironmentName.Development> :</span><span class="sxs-lookup"><span data-stu-id="e981c-177">`CreateDefaultBuilder` calls <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> when the <xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName> is <xref:Microsoft.Extensions.Hosting.EnvironmentName.Development>:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program.cs?name=snippet_CreateHostBuilder&highlight=2)]

<span data-ttu-id="e981c-178">Quand `CreateDefaultBuilder` n’est pas appelé, ajoutez explicitement la source de configuration des secrets de l’utilisateur en appelant <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> .</span><span class="sxs-lookup"><span data-stu-id="e981c-178">When `CreateDefaultBuilder` isn't called, add the user secrets configuration source explicitly by calling <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A>.</span></span> <span data-ttu-id="e981c-179">Appelez `AddUserSecrets` uniquement lorsque l’application s’exécute dans l’environnement de développement, comme illustré dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="e981c-179">Call `AddUserSecrets` only when the app runs in the Development environment, as shown in the following example:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program2.cs?name=snippet_Host&highlight=6-9)]

<span data-ttu-id="e981c-180">Les secrets de l’utilisateur peuvent être récupérés via l' `Configuration` API :</span><span class="sxs-lookup"><span data-stu-id="e981c-180">User secrets can be retrieved via the `Configuration` API:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a><span data-ttu-id="e981c-181">Mapper les secrets à un POCO</span><span class="sxs-lookup"><span data-stu-id="e981c-181">Map secrets to a POCO</span></span>

<span data-ttu-id="e981c-182">Le mappage d’un littéral d’objet entier à un POCO (une classe .NET simple avec des propriétés) est utile pour l’agrégation des propriétés associées.</span><span class="sxs-lookup"><span data-stu-id="e981c-182">Mapping an entire object literal to a POCO (a simple .NET class with properties) is useful for aggregating related properties.</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="e981c-183">Pour mapper les secrets précédents à un POCO, utilisez la `Configuration` fonctionnalité de [liaison de graphique d’objets](xref:fundamentals/configuration/index#bind-to-an-object-graph) de l’API.</span><span class="sxs-lookup"><span data-stu-id="e981c-183">To map the preceding secrets to a POCO, use the `Configuration` API's [object graph binding](xref:fundamentals/configuration/index#bind-to-an-object-graph) feature.</span></span> <span data-ttu-id="e981c-184">Le code suivant est lié à un `MovieSettings` poco personnalisé et accède à la `ServiceApiKey` valeur de la propriété :</span><span class="sxs-lookup"><span data-stu-id="e981c-184">The following code binds to a custom `MovieSettings` POCO and accesses the `ServiceApiKey` property value:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

<span data-ttu-id="e981c-185">Les `Movies:ConnectionString` `Movies:ServiceApiKey` secrets et sont mappés aux propriétés respectives dans `MovieSettings` :</span><span class="sxs-lookup"><span data-stu-id="e981c-185">The `Movies:ConnectionString` and `Movies:ServiceApiKey` secrets are mapped to the respective properties in `MovieSettings`:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a><span data-ttu-id="e981c-186">Remplacement de chaîne par des secrets</span><span class="sxs-lookup"><span data-stu-id="e981c-186">String replacement with secrets</span></span>

<span data-ttu-id="e981c-187">Le stockage des mots de passe en texte brut n’est pas sécurisé.</span><span class="sxs-lookup"><span data-stu-id="e981c-187">Storing passwords in plain text is insecure.</span></span> <span data-ttu-id="e981c-188">Par exemple, une chaîne de connexion de base de données stockée dans *appsettings.jssur* peut inclure un mot de passe pour l’utilisateur spécifié :</span><span class="sxs-lookup"><span data-stu-id="e981c-188">For example, a database connection string stored in *appsettings.json* may include a password for the specified user:</span></span>

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

<span data-ttu-id="e981c-189">Une approche plus sécurisée consiste à stocker le mot de passe en tant que secret.</span><span class="sxs-lookup"><span data-stu-id="e981c-189">A more secure approach is to store the password as a secret.</span></span> <span data-ttu-id="e981c-190">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="e981c-190">For example:</span></span>

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

<span data-ttu-id="e981c-191">Supprimez la `Password` paire clé-valeur de la chaîne de connexion dans *appsettings.jssur*.</span><span class="sxs-lookup"><span data-stu-id="e981c-191">Remove the `Password` key-value pair from the connection string in *appsettings.json*.</span></span> <span data-ttu-id="e981c-192">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="e981c-192">For example:</span></span>

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings.json?highlight=3)]

<span data-ttu-id="e981c-193">La valeur du secret peut être définie sur la <xref:System.Data.SqlClient.SqlConnectionStringBuilder> propriété d’un objet <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> pour terminer la chaîne de connexion :</span><span class="sxs-lookup"><span data-stu-id="e981c-193">The secret's value can be set on a <xref:System.Data.SqlClient.SqlConnectionStringBuilder> object's <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> property to complete the connection string:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a><span data-ttu-id="e981c-194">Répertorier les secrets</span><span class="sxs-lookup"><span data-stu-id="e981c-194">List the secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="e981c-195">Exécutez la commande suivante à partir du répertoire dans lequel se trouve le fichier *. csproj* :</span><span class="sxs-lookup"><span data-stu-id="e981c-195">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets list
```

<span data-ttu-id="e981c-196">Vous obtenez la sortie suivante :</span><span class="sxs-lookup"><span data-stu-id="e981c-196">The following output appears:</span></span>

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

<span data-ttu-id="e981c-197">Dans l’exemple précédent, un signe deux-points dans les noms de clé désigne la hiérarchie d’objets dans *secrets.jssur*.</span><span class="sxs-lookup"><span data-stu-id="e981c-197">In the preceding example, a colon in the key names denotes the object hierarchy within *secrets.json*.</span></span>

## <a name="remove-a-single-secret"></a><span data-ttu-id="e981c-198">Supprimer une seule clé secrète</span><span class="sxs-lookup"><span data-stu-id="e981c-198">Remove a single secret</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="e981c-199">Exécutez la commande suivante à partir du répertoire dans lequel se trouve le fichier *. csproj* :</span><span class="sxs-lookup"><span data-stu-id="e981c-199">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

<span data-ttu-id="e981c-200">L'secrets.jsde l’application *sur* le fichier a été modifiée pour supprimer la paire clé-valeur associée à la `MoviesConnectionString` clé :</span><span class="sxs-lookup"><span data-stu-id="e981c-200">The app's *secrets.json* file was modified to remove the key-value pair associated with the `MoviesConnectionString` key:</span></span>

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="e981c-201">`dotnet user-secrets list`affiche le message suivant :</span><span class="sxs-lookup"><span data-stu-id="e981c-201">`dotnet user-secrets list` displays the following message:</span></span>

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a><span data-ttu-id="e981c-202">Supprimer tous les secrets</span><span class="sxs-lookup"><span data-stu-id="e981c-202">Remove all secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="e981c-203">Exécutez la commande suivante à partir du répertoire dans lequel se trouve le fichier *. csproj* :</span><span class="sxs-lookup"><span data-stu-id="e981c-203">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets clear
```

<span data-ttu-id="e981c-204">Tous les secrets d’utilisateur de l’application ont été supprimés de l' *secrets.jssur* le fichier :</span><span class="sxs-lookup"><span data-stu-id="e981c-204">All user secrets for the app have been deleted from the *secrets.json* file:</span></span>

```json
{}
```

<span data-ttu-id="e981c-205">`dotnet user-secrets list`L’exécution affiche le message suivant :</span><span class="sxs-lookup"><span data-stu-id="e981c-205">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a><span data-ttu-id="e981c-206">Ressources complémentaires</span><span class="sxs-lookup"><span data-stu-id="e981c-206">Additional resources</span></span>

* <span data-ttu-id="e981c-207">Pour plus d’informations sur l’accès au gestionnaire de secret à partir d’IIS, consultez [ce problème](https://github.com/dotnet/AspNetCore.Docs/issues/16328) .</span><span class="sxs-lookup"><span data-stu-id="e981c-207">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/16328) for information on accessing Secret Manager from IIS.</span></span>
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e981c-208">Par [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27)et [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="e981c-208">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="e981c-209">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e981c-209">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="e981c-210">Ce document décrit les techniques de stockage et de récupération des données sensibles lors du développement d’une application ASP.NET Core sur un ordinateur de développement.</span><span class="sxs-lookup"><span data-stu-id="e981c-210">This document explains techniques for storing and retrieving sensitive data during development of an ASP.NET Core app on a development machine.</span></span> <span data-ttu-id="e981c-211">Ne stockez jamais de mots de passe ou d’autres données sensibles dans le code source.</span><span class="sxs-lookup"><span data-stu-id="e981c-211">Never store passwords or other sensitive data in source code.</span></span> <span data-ttu-id="e981c-212">Les secrets de production ne doivent pas être utilisés à des fins de développement ou de test.</span><span class="sxs-lookup"><span data-stu-id="e981c-212">Production secrets shouldn't be used for development or test.</span></span> <span data-ttu-id="e981c-213">Les secrets ne doivent pas être déployés avec l’application.</span><span class="sxs-lookup"><span data-stu-id="e981c-213">Secrets shouldn't be deployed with the app.</span></span> <span data-ttu-id="e981c-214">Au lieu de cela, les secrets doivent être mis à disposition dans l’environnement de production par un moyen contrôlé, comme les variables d’environnement, les Azure Key Vault, etc. Vous pouvez stocker et protéger les secrets de test et de production Azure à l’aide du [fournisseur de configuration Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="e981c-214">Instead, secrets should be made available in the production environment through a controlled means like environment variables, Azure Key Vault, etc. You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

## <a name="environment-variables"></a><span data-ttu-id="e981c-215">Variables d'environnement</span><span class="sxs-lookup"><span data-stu-id="e981c-215">Environment variables</span></span>

<span data-ttu-id="e981c-216">Les variables d’environnement sont utilisées pour éviter le stockage des secrets d’application dans le code ou dans des fichiers de configuration locaux.</span><span class="sxs-lookup"><span data-stu-id="e981c-216">Environment variables are used to avoid storage of app secrets in code or in local configuration files.</span></span> <span data-ttu-id="e981c-217">Les variables d’environnement remplacent les valeurs de configuration pour toutes les sources de configuration spécifiées précédemment.</span><span class="sxs-lookup"><span data-stu-id="e981c-217">Environment variables override configuration values for all previously specified configuration sources.</span></span>

<span data-ttu-id="e981c-218">Prenons l’exemple d’une application Web ASP.NET Core dans laquelle la sécurité **des comptes d’utilisateur individuels** est activée.</span><span class="sxs-lookup"><span data-stu-id="e981c-218">Consider an ASP.NET Core web app in which **Individual User Accounts** security is enabled.</span></span> <span data-ttu-id="e981c-219">Une chaîne de connexion de base de données par défaut est incluse dans le fichier *appsettings.js* du projet avec la clé `DefaultConnection` .</span><span class="sxs-lookup"><span data-stu-id="e981c-219">A default database connection string is included in the project's *appsettings.json* file with the key `DefaultConnection`.</span></span> <span data-ttu-id="e981c-220">La chaîne de connexion par défaut est pour la base de données locale, qui s’exécute en mode utilisateur et ne nécessite pas de mot de passe.</span><span class="sxs-lookup"><span data-stu-id="e981c-220">The default connection string is for LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="e981c-221">Pendant le déploiement de l’application, la `DefaultConnection` valeur de clé peut être remplacée par la valeur d’une variable d’environnement.</span><span class="sxs-lookup"><span data-stu-id="e981c-221">During app deployment, the `DefaultConnection` key value can be overridden with an environment variable's value.</span></span> <span data-ttu-id="e981c-222">La variable d’environnement peut stocker la chaîne de connexion complète avec des informations d’identification sensibles.</span><span class="sxs-lookup"><span data-stu-id="e981c-222">The environment variable may store the complete connection string with sensitive credentials.</span></span>

> [!WARNING]
> <span data-ttu-id="e981c-223">Les variables d’environnement sont généralement stockées en texte brut et non chiffré.</span><span class="sxs-lookup"><span data-stu-id="e981c-223">Environment variables are generally stored in plain, unencrypted text.</span></span> <span data-ttu-id="e981c-224">Si l’ordinateur ou le processus est compromis, les variables d’environnement sont accessibles aux parties non fiables.</span><span class="sxs-lookup"><span data-stu-id="e981c-224">If the machine or process is compromised, environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="e981c-225">Des mesures supplémentaires pour empêcher la divulgation de secrets d’utilisateur peuvent être nécessaires.</span><span class="sxs-lookup"><span data-stu-id="e981c-225">Additional measures to prevent disclosure of user secrets may be required.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a><span data-ttu-id="e981c-226">Gestionnaire de secret</span><span class="sxs-lookup"><span data-stu-id="e981c-226">Secret Manager</span></span>

<span data-ttu-id="e981c-227">L’outil secret Manager stocke les données sensibles pendant le développement d’un projet ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e981c-227">The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.</span></span> <span data-ttu-id="e981c-228">Dans ce contexte, un élément de données sensibles est un secret d’application.</span><span class="sxs-lookup"><span data-stu-id="e981c-228">In this context, a piece of sensitive data is an app secret.</span></span> <span data-ttu-id="e981c-229">Les secrets de l’application sont stockés dans un emplacement distinct de l’arborescence du projet.</span><span class="sxs-lookup"><span data-stu-id="e981c-229">App secrets are stored in a separate location from the project tree.</span></span> <span data-ttu-id="e981c-230">Les secrets de l’application sont associés à un projet spécifique ou partagés entre plusieurs projets.</span><span class="sxs-lookup"><span data-stu-id="e981c-230">The app secrets are associated with a specific project or shared across several projects.</span></span> <span data-ttu-id="e981c-231">Les secrets de l’application ne sont pas archivés dans le contrôle de code source.</span><span class="sxs-lookup"><span data-stu-id="e981c-231">The app secrets aren't checked into source control.</span></span>

> [!WARNING]
> <span data-ttu-id="e981c-232">L’outil Gestionnaire de secret ne chiffre pas les secrets stockés et ne doit pas être traité comme un magasin approuvé.</span><span class="sxs-lookup"><span data-stu-id="e981c-232">The Secret Manager tool doesn't encrypt the stored secrets and shouldn't be treated as a trusted store.</span></span> <span data-ttu-id="e981c-233">À des fins de développement uniquement.</span><span class="sxs-lookup"><span data-stu-id="e981c-233">It's for development purposes only.</span></span> <span data-ttu-id="e981c-234">Les clés et les valeurs sont stockées dans un fichier de configuration JSON dans le répertoire du profil utilisateur.</span><span class="sxs-lookup"><span data-stu-id="e981c-234">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="e981c-235">Fonctionnement de l’outil secret Manager</span><span class="sxs-lookup"><span data-stu-id="e981c-235">How the Secret Manager tool works</span></span>

<span data-ttu-id="e981c-236">L’outil Gestionnaire de secret résume les détails de l’implémentation, tels que l’emplacement et la manière dont les valeurs sont stockées.</span><span class="sxs-lookup"><span data-stu-id="e981c-236">The Secret Manager tool abstracts away the implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="e981c-237">Vous pouvez utiliser l’outil sans connaître ces détails d’implémentation.</span><span class="sxs-lookup"><span data-stu-id="e981c-237">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="e981c-238">Les valeurs sont stockées dans un fichier de configuration JSON dans un dossier de profil utilisateur protégé du système sur l’ordinateur local :</span><span class="sxs-lookup"><span data-stu-id="e981c-238">The values are stored in a JSON configuration file in a system-protected user profile folder on the local machine:</span></span>

# <a name="windows"></a>[<span data-ttu-id="e981c-239">Windows</span><span class="sxs-lookup"><span data-stu-id="e981c-239">Windows</span></span>](#tab/windows)

<span data-ttu-id="e981c-240">Chemin d’accès au système de fichiers :</span><span class="sxs-lookup"><span data-stu-id="e981c-240">File system path:</span></span>

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[<span data-ttu-id="e981c-241">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="e981c-241">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="e981c-242">Chemin d’accès au système de fichiers :</span><span class="sxs-lookup"><span data-stu-id="e981c-242">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

<span data-ttu-id="e981c-243">Dans les chemins d’accès de fichier précédents, remplacez `<user_secrets_id>` par la `UserSecretsId` valeur spécifiée dans le fichier *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="e981c-243">In the preceding file paths, replace `<user_secrets_id>` with the `UserSecretsId` value specified in the *.csproj* file.</span></span>

<span data-ttu-id="e981c-244">N’écrivez pas de code dépendant de l’emplacement ou du format des données enregistrées avec l’outil secret Manager.</span><span class="sxs-lookup"><span data-stu-id="e981c-244">Don't write code that depends on the location or format of data saved with the Secret Manager tool.</span></span> <span data-ttu-id="e981c-245">Ces détails d’implémentation peuvent changer.</span><span class="sxs-lookup"><span data-stu-id="e981c-245">These implementation details may change.</span></span> <span data-ttu-id="e981c-246">Par exemple, les valeurs secrètes ne sont pas chiffrées, mais peuvent être dans le futur.</span><span class="sxs-lookup"><span data-stu-id="e981c-246">For example, the secret values aren't encrypted, but could be in the future.</span></span>

## <a name="enable-secret-storage"></a><span data-ttu-id="e981c-247">Activer le stockage secret</span><span class="sxs-lookup"><span data-stu-id="e981c-247">Enable secret storage</span></span>

<span data-ttu-id="e981c-248">L’outil Gestionnaire de secret fonctionne sur les paramètres de configuration spécifiques au projet stockés dans votre profil utilisateur.</span><span class="sxs-lookup"><span data-stu-id="e981c-248">The Secret Manager tool operates on project-specific configuration settings stored in your user profile.</span></span>

<span data-ttu-id="e981c-249">Pour utiliser des secrets d’utilisateur, définissez un `UserSecretsId` élément dans un `PropertyGroup` du fichier *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="e981c-249">To use user secrets, define a `UserSecretsId` element within a `PropertyGroup` of the *.csproj* file.</span></span> <span data-ttu-id="e981c-250">Le texte interne de `UserSecretsId` est arbitraire, mais il est unique pour le projet.</span><span class="sxs-lookup"><span data-stu-id="e981c-250">The inner text of `UserSecretsId` is arbitrary, but is unique to the project.</span></span> <span data-ttu-id="e981c-251">Les développeurs génèrent généralement un GUID pour le `UserSecretsId` .</span><span class="sxs-lookup"><span data-stu-id="e981c-251">Developers typically generate a GUID for the `UserSecretsId`.</span></span>

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

> [!TIP]
> <span data-ttu-id="e981c-252">Dans Visual Studio, cliquez avec le bouton droit sur le projet dans Explorateur de solutions, puis sélectionnez **gérer les secrets d’utilisateur** dans le menu contextuel.</span><span class="sxs-lookup"><span data-stu-id="e981c-252">In Visual Studio, right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="e981c-253">Ce geste ajoute un `UserSecretsId` élément, rempli avec un GUID, au fichier *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="e981c-253">This gesture adds a `UserSecretsId` element, populated with a GUID, to the *.csproj* file.</span></span>

## <a name="set-a-secret"></a><span data-ttu-id="e981c-254">Définir une clé secrète</span><span class="sxs-lookup"><span data-stu-id="e981c-254">Set a secret</span></span>

<span data-ttu-id="e981c-255">Définissez un secret d’application comprenant une clé et sa valeur.</span><span class="sxs-lookup"><span data-stu-id="e981c-255">Define an app secret consisting of a key and its value.</span></span> <span data-ttu-id="e981c-256">Le secret est associé à la valeur du projet `UserSecretsId` .</span><span class="sxs-lookup"><span data-stu-id="e981c-256">The secret is associated with the project's `UserSecretsId` value.</span></span> <span data-ttu-id="e981c-257">Par exemple, exécutez la commande suivante à partir du répertoire dans lequel se trouve le fichier *. csproj* :</span><span class="sxs-lookup"><span data-stu-id="e981c-257">For example, run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

<span data-ttu-id="e981c-258">Dans l’exemple précédent, le signe deux-points indique qu' `Movies` il s’agit d’un littéral d’objet avec une `ServiceApiKey` propriété.</span><span class="sxs-lookup"><span data-stu-id="e981c-258">In the preceding example, the colon denotes that `Movies` is an object literal with a `ServiceApiKey` property.</span></span>

<span data-ttu-id="e981c-259">L’outil Gestionnaire de secret peut également être utilisé à partir d’autres annuaires.</span><span class="sxs-lookup"><span data-stu-id="e981c-259">The Secret Manager tool can be used from other directories too.</span></span> <span data-ttu-id="e981c-260">Utilisez l' `--project` option pour fournir le chemin d’accès au système de fichiers où se trouve le fichier *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="e981c-260">Use the `--project` option to supply the file system path at which the *.csproj* file exists.</span></span> <span data-ttu-id="e981c-261">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="e981c-261">For example:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a><span data-ttu-id="e981c-262">Aplatissement de la structure JSON dans Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e981c-262">JSON structure flattening in Visual Studio</span></span>

<span data-ttu-id="e981c-263">Le geste **gérer les secrets** de l’utilisateur de Visual Studio ouvre un *secrets.jssur* le fichier dans l’éditeur de texte.</span><span class="sxs-lookup"><span data-stu-id="e981c-263">Visual Studio's **Manage User Secrets** gesture opens a *secrets.json* file in the text editor.</span></span> <span data-ttu-id="e981c-264">Remplacez le contenu de *secrets.js* par les paires clé-valeur à stocker.</span><span class="sxs-lookup"><span data-stu-id="e981c-264">Replace the contents of *secrets.json* with the key-value pairs to be stored.</span></span> <span data-ttu-id="e981c-265">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="e981c-265">For example:</span></span>

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="e981c-266">La structure JSON est aplatie après des modifications via `dotnet user-secrets remove` ou `dotnet user-secrets set` .</span><span class="sxs-lookup"><span data-stu-id="e981c-266">The JSON structure is flattened after modifications via `dotnet user-secrets remove` or `dotnet user-secrets set`.</span></span> <span data-ttu-id="e981c-267">Par exemple, l’exécution de `dotnet user-secrets remove "Movies:ConnectionString"` réduit le `Movies` littéral d’objet.</span><span class="sxs-lookup"><span data-stu-id="e981c-267">For example, running `dotnet user-secrets remove "Movies:ConnectionString"` collapses the `Movies` object literal.</span></span> <span data-ttu-id="e981c-268">Le fichier modifié ressemble à ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="e981c-268">The modified file resembles the following:</span></span>

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a><span data-ttu-id="e981c-269">Définir plusieurs secrets</span><span class="sxs-lookup"><span data-stu-id="e981c-269">Set multiple secrets</span></span>

<span data-ttu-id="e981c-270">Un lot de secrets peut être défini par la canalisation JSON à la `set` commande.</span><span class="sxs-lookup"><span data-stu-id="e981c-270">A batch of secrets can be set by piping JSON to the `set` command.</span></span> <span data-ttu-id="e981c-271">Dans l’exemple suivant, le *input.js* le contenu du fichier est dirigé vers la `set` commande.</span><span class="sxs-lookup"><span data-stu-id="e981c-271">In the following example, the *input.json* file's contents are piped to the `set` command.</span></span>

# <a name="windows"></a>[<span data-ttu-id="e981c-272">Windows</span><span class="sxs-lookup"><span data-stu-id="e981c-272">Windows</span></span>](#tab/windows)

<span data-ttu-id="e981c-273">Ouvrez une interface de commande, puis exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="e981c-273">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[<span data-ttu-id="e981c-274">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="e981c-274">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="e981c-275">Ouvrez une interface de commande, puis exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="e981c-275">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a><span data-ttu-id="e981c-276">Accéder à une clé secrète</span><span class="sxs-lookup"><span data-stu-id="e981c-276">Access a secret</span></span>

<span data-ttu-id="e981c-277">L' [API de Configuration ASP.net Core](xref:fundamentals/configuration/index) permet d’accéder aux secrets du gestionnaire de secret.</span><span class="sxs-lookup"><span data-stu-id="e981c-277">The [ASP.NET Core Configuration API](xref:fundamentals/configuration/index) provides access to Secret Manager secrets.</span></span>

<span data-ttu-id="e981c-278">Si votre projet cible .NET Framework, installez le [Microsoft.Extensions.Configfiguration. ](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets)Package NuGet UserSecrets.</span><span class="sxs-lookup"><span data-stu-id="e981c-278">If your project targets .NET Framework, install the [Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet package.</span></span>

<span data-ttu-id="e981c-279">Dans ASP.NET Core 2,0 ou version ultérieure, la source de configuration des secrets de l’utilisateur est automatiquement ajoutée en mode de développement lorsque le projet appelle <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> pour initialiser une nouvelle instance de l’hôte avec des valeurs par défaut préconfigurées.</span><span class="sxs-lookup"><span data-stu-id="e981c-279">In ASP.NET Core 2.0 or later, the user secrets configuration source is automatically added in development mode when the project calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> to initialize a new instance of the host with preconfigured defaults.</span></span> <span data-ttu-id="e981c-280">`CreateDefaultBuilder`appelle <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> lorsque <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> est <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development> :</span><span class="sxs-lookup"><span data-stu-id="e981c-280">`CreateDefaultBuilder` calls <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> when the <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> is <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Program.cs?name=snippet_CreateWebHostBuilder&highlight=2)]

<span data-ttu-id="e981c-281">Quand `CreateDefaultBuilder` n’est pas appelé, ajoutez explicitement la source de configuration des secrets de l’utilisateur en appelant <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> dans le `Startup` constructeur.</span><span class="sxs-lookup"><span data-stu-id="e981c-281">When `CreateDefaultBuilder` isn't called, add the user secrets configuration source explicitly by calling <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> in the `Startup` constructor.</span></span> <span data-ttu-id="e981c-282">Appelez `AddUserSecrets` uniquement lorsque l’application s’exécute dans l’environnement de développement, comme illustré dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="e981c-282">Call `AddUserSecrets` only when the app runs in the Development environment, as shown in the following example:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_StartupConstructor&highlight=12)]

<span data-ttu-id="e981c-283">Les secrets de l’utilisateur peuvent être récupérés via l' `Configuration` API :</span><span class="sxs-lookup"><span data-stu-id="e981c-283">User secrets can be retrieved via the `Configuration` API:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a><span data-ttu-id="e981c-284">Mapper les secrets à un POCO</span><span class="sxs-lookup"><span data-stu-id="e981c-284">Map secrets to a POCO</span></span>

<span data-ttu-id="e981c-285">Le mappage d’un littéral d’objet entier à un POCO (une classe .NET simple avec des propriétés) est utile pour l’agrégation des propriétés associées.</span><span class="sxs-lookup"><span data-stu-id="e981c-285">Mapping an entire object literal to a POCO (a simple .NET class with properties) is useful for aggregating related properties.</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="e981c-286">Pour mapper les secrets précédents à un POCO, utilisez la `Configuration` fonctionnalité de [liaison de graphique d’objets](xref:fundamentals/configuration/index#bind-to-an-object-graph) de l’API.</span><span class="sxs-lookup"><span data-stu-id="e981c-286">To map the preceding secrets to a POCO, use the `Configuration` API's [object graph binding](xref:fundamentals/configuration/index#bind-to-an-object-graph) feature.</span></span> <span data-ttu-id="e981c-287">Le code suivant est lié à un `MovieSettings` poco personnalisé et accède à la `ServiceApiKey` valeur de la propriété :</span><span class="sxs-lookup"><span data-stu-id="e981c-287">The following code binds to a custom `MovieSettings` POCO and accesses the `ServiceApiKey` property value:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

<span data-ttu-id="e981c-288">Les `Movies:ConnectionString` `Movies:ServiceApiKey` secrets et sont mappés aux propriétés respectives dans `MovieSettings` :</span><span class="sxs-lookup"><span data-stu-id="e981c-288">The `Movies:ConnectionString` and `Movies:ServiceApiKey` secrets are mapped to the respective properties in `MovieSettings`:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a><span data-ttu-id="e981c-289">Remplacement de chaîne par des secrets</span><span class="sxs-lookup"><span data-stu-id="e981c-289">String replacement with secrets</span></span>

<span data-ttu-id="e981c-290">Le stockage des mots de passe en texte brut n’est pas sécurisé.</span><span class="sxs-lookup"><span data-stu-id="e981c-290">Storing passwords in plain text is insecure.</span></span> <span data-ttu-id="e981c-291">Par exemple, une chaîne de connexion de base de données stockée dans *appsettings.jssur* peut inclure un mot de passe pour l’utilisateur spécifié :</span><span class="sxs-lookup"><span data-stu-id="e981c-291">For example, a database connection string stored in *appsettings.json* may include a password for the specified user:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

<span data-ttu-id="e981c-292">Une approche plus sécurisée consiste à stocker le mot de passe en tant que secret.</span><span class="sxs-lookup"><span data-stu-id="e981c-292">A more secure approach is to store the password as a secret.</span></span> <span data-ttu-id="e981c-293">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="e981c-293">For example:</span></span>

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

<span data-ttu-id="e981c-294">Supprimez la `Password` paire clé-valeur de la chaîne de connexion dans *appsettings.jssur*.</span><span class="sxs-lookup"><span data-stu-id="e981c-294">Remove the `Password` key-value pair from the connection string in *appsettings.json*.</span></span> <span data-ttu-id="e981c-295">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="e981c-295">For example:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

<span data-ttu-id="e981c-296">La valeur du secret peut être définie sur la <xref:System.Data.SqlClient.SqlConnectionStringBuilder> propriété d’un objet <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> pour terminer la chaîne de connexion :</span><span class="sxs-lookup"><span data-stu-id="e981c-296">The secret's value can be set on a <xref:System.Data.SqlClient.SqlConnectionStringBuilder> object's <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> property to complete the connection string:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a><span data-ttu-id="e981c-297">Répertorier les secrets</span><span class="sxs-lookup"><span data-stu-id="e981c-297">List the secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="e981c-298">Exécutez la commande suivante à partir du répertoire dans lequel se trouve le fichier *. csproj* :</span><span class="sxs-lookup"><span data-stu-id="e981c-298">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets list
```

<span data-ttu-id="e981c-299">Vous obtenez la sortie suivante :</span><span class="sxs-lookup"><span data-stu-id="e981c-299">The following output appears:</span></span>

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

<span data-ttu-id="e981c-300">Dans l’exemple précédent, un signe deux-points dans les noms de clé désigne la hiérarchie d’objets dans *secrets.jssur*.</span><span class="sxs-lookup"><span data-stu-id="e981c-300">In the preceding example, a colon in the key names denotes the object hierarchy within *secrets.json*.</span></span>

## <a name="remove-a-single-secret"></a><span data-ttu-id="e981c-301">Supprimer une seule clé secrète</span><span class="sxs-lookup"><span data-stu-id="e981c-301">Remove a single secret</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="e981c-302">Exécutez la commande suivante à partir du répertoire dans lequel se trouve le fichier *. csproj* :</span><span class="sxs-lookup"><span data-stu-id="e981c-302">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

<span data-ttu-id="e981c-303">L'secrets.jsde l’application *sur* le fichier a été modifiée pour supprimer la paire clé-valeur associée à la `MoviesConnectionString` clé :</span><span class="sxs-lookup"><span data-stu-id="e981c-303">The app's *secrets.json* file was modified to remove the key-value pair associated with the `MoviesConnectionString` key:</span></span>

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="e981c-304">`dotnet user-secrets list`L’exécution affiche le message suivant :</span><span class="sxs-lookup"><span data-stu-id="e981c-304">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a><span data-ttu-id="e981c-305">Supprimer tous les secrets</span><span class="sxs-lookup"><span data-stu-id="e981c-305">Remove all secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="e981c-306">Exécutez la commande suivante à partir du répertoire dans lequel se trouve le fichier *. csproj* :</span><span class="sxs-lookup"><span data-stu-id="e981c-306">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets clear
```

<span data-ttu-id="e981c-307">Tous les secrets d’utilisateur de l’application ont été supprimés de l' *secrets.jssur* le fichier :</span><span class="sxs-lookup"><span data-stu-id="e981c-307">All user secrets for the app have been deleted from the *secrets.json* file:</span></span>

```json
{}
```

<span data-ttu-id="e981c-308">`dotnet user-secrets list`L’exécution affiche le message suivant :</span><span class="sxs-lookup"><span data-stu-id="e981c-308">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a><span data-ttu-id="e981c-309">Ressources complémentaires</span><span class="sxs-lookup"><span data-stu-id="e981c-309">Additional resources</span></span>

* <span data-ttu-id="e981c-310">Pour plus d’informations sur l’accès au gestionnaire de secret à partir d’IIS, consultez [ce problème](https://github.com/dotnet/AspNetCore.Docs/issues/16328) .</span><span class="sxs-lookup"><span data-stu-id="e981c-310">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/16328) for information on accessing Secret Manager from IIS.</span></span>
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end