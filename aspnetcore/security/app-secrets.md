---
title: Stockage sécurisé des secrets d’applications en développement dans ASP.NET Core
author: rick-anderson
description: Découvrez comment stocker et récupérer des informations sensibles comme secrets d’applications lors du développement d’une application ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 4/20/2020
uid: security/app-secrets
ms.openlocfilehash: c62c5e59ad0a72506fb72bda82aa821a4f1719c8
ms.sourcegitcommit: c9d1208e86160615b2d914cce74a839ae41297a8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81791572"
---
# <a name="safe-storage-of-app-secrets-in-development-in-aspnet-core"></a><span data-ttu-id="d1261-103">Stockage sécurisé des secrets d’applications en développement dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d1261-103">Safe storage of app secrets in development in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d1261-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT), Kirk [Larkin](https://twitter.com/serpent5), [Daniel Roth](https://github.com/danroth27), et Scott [Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="d1261-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="d1261-105">[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([comment télécharger](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d1261-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="d1261-106">Ce document explique les techniques de stockage et de récupération de données sensibles lors du développement d’une application ASP.NET Core sur une machine de développement.</span><span class="sxs-lookup"><span data-stu-id="d1261-106">This document explains techniques for storing and retrieving sensitive data during development of an ASP.NET Core app on a development machine.</span></span> <span data-ttu-id="d1261-107">Ne stockez jamais de mots de passe ou d’autres données sensibles dans le code source.</span><span class="sxs-lookup"><span data-stu-id="d1261-107">Never store passwords or other sensitive data in source code.</span></span> <span data-ttu-id="d1261-108">Les secrets de production ne doivent pas être utilisés pour le développement ou le test.</span><span class="sxs-lookup"><span data-stu-id="d1261-108">Production secrets shouldn't be used for development or test.</span></span> <span data-ttu-id="d1261-109">Les secrets ne doivent pas être déployés avec l’application.</span><span class="sxs-lookup"><span data-stu-id="d1261-109">Secrets shouldn't be deployed with the app.</span></span> <span data-ttu-id="d1261-110">Au lieu de cela, des secrets devraient être mis à disposition dans l’environnement de production par le biais d’un moyen contrôlé comme les variables de l’environnement, Azure Key Vault, etc. Vous pouvez stocker et protéger les secrets de test et de production Azure avec le [fournisseur de configuration Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="d1261-110">Instead, secrets should be made available in the production environment through a controlled means like environment variables, Azure Key Vault, etc. You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

## <a name="environment-variables"></a><span data-ttu-id="d1261-111">Variables d'environnement</span><span class="sxs-lookup"><span data-stu-id="d1261-111">Environment variables</span></span>

<span data-ttu-id="d1261-112">Les variables de l’environnement sont utilisées pour éviter le stockage des secrets d’application dans le code ou dans les fichiers de configuration locaux.</span><span class="sxs-lookup"><span data-stu-id="d1261-112">Environment variables are used to avoid storage of app secrets in code or in local configuration files.</span></span> <span data-ttu-id="d1261-113">Les variables de l’environnement remplacent les valeurs de configuration pour toutes les sources de configuration précédemment spécifiées.</span><span class="sxs-lookup"><span data-stu-id="d1261-113">Environment variables override configuration values for all previously specified configuration sources.</span></span>

<span data-ttu-id="d1261-114">Considérez une application web ASP.NET Core dans laquelle la sécurité **des comptes utilisateur individuels** est activée.</span><span class="sxs-lookup"><span data-stu-id="d1261-114">Consider an ASP.NET Core web app in which **Individual User Accounts** security is enabled.</span></span> <span data-ttu-id="d1261-115">Une chaîne de connexion de base de données par défaut est incluse `DefaultConnection`dans le fichier *appsettings.json* du projet avec la clé .</span><span class="sxs-lookup"><span data-stu-id="d1261-115">A default database connection string is included in the project's *appsettings.json* file with the key `DefaultConnection`.</span></span> <span data-ttu-id="d1261-116">La chaîne de connexion par défaut est pour LocalDB, qui s’exécute en mode utilisateur et ne nécessite pas de mot de passe.</span><span class="sxs-lookup"><span data-stu-id="d1261-116">The default connection string is for LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="d1261-117">Pendant le déploiement `DefaultConnection` de l’application, la valeur clé peut être remplacée par la valeur d’une variable d’environnement.</span><span class="sxs-lookup"><span data-stu-id="d1261-117">During app deployment, the `DefaultConnection` key value can be overridden with an environment variable's value.</span></span> <span data-ttu-id="d1261-118">La variable d’environnement peut stocker la chaîne de connexion complète avec des informations d’identification sensibles.</span><span class="sxs-lookup"><span data-stu-id="d1261-118">The environment variable may store the complete connection string with sensitive credentials.</span></span>

> [!WARNING]
> <span data-ttu-id="d1261-119">Les variables de l’environnement sont généralement stockées dans un texte simple et non chiffré.</span><span class="sxs-lookup"><span data-stu-id="d1261-119">Environment variables are generally stored in plain, unencrypted text.</span></span> <span data-ttu-id="d1261-120">Si la machine ou le processus est compromis, les variables de l’environnement peuvent être consultées par des parties non fiables.</span><span class="sxs-lookup"><span data-stu-id="d1261-120">If the machine or process is compromised, environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="d1261-121">Des mesures supplémentaires pour empêcher la divulgation de secrets d’utilisateur peuvent être nécessaires.</span><span class="sxs-lookup"><span data-stu-id="d1261-121">Additional measures to prevent disclosure of user secrets may be required.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a><span data-ttu-id="d1261-122">Gestionnaire secret</span><span class="sxs-lookup"><span data-stu-id="d1261-122">Secret Manager</span></span>

<span data-ttu-id="d1261-123">L’outil Secret Manager stocke des données sensibles lors du développement d’un projet ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d1261-123">The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.</span></span> <span data-ttu-id="d1261-124">Dans ce contexte, un élément de données sensibles est un secret d’application.</span><span class="sxs-lookup"><span data-stu-id="d1261-124">In this context, a piece of sensitive data is an app secret.</span></span> <span data-ttu-id="d1261-125">Les secrets de l’application sont stockés dans un endroit séparé de l’arbre du projet.</span><span class="sxs-lookup"><span data-stu-id="d1261-125">App secrets are stored in a separate location from the project tree.</span></span> <span data-ttu-id="d1261-126">Les secrets de l’application sont associés à un projet spécifique ou partagés sur plusieurs projets.</span><span class="sxs-lookup"><span data-stu-id="d1261-126">The app secrets are associated with a specific project or shared across several projects.</span></span> <span data-ttu-id="d1261-127">Les secrets de l’application ne sont pas vérifiés dans le contrôle source.</span><span class="sxs-lookup"><span data-stu-id="d1261-127">The app secrets aren't checked into source control.</span></span>

> [!WARNING]
> <span data-ttu-id="d1261-128">L’outil Secret Manager ne crypte pas les secrets stockés et ne doit pas être traité comme un magasin de confiance.</span><span class="sxs-lookup"><span data-stu-id="d1261-128">The Secret Manager tool doesn't encrypt the stored secrets and shouldn't be treated as a trusted store.</span></span> <span data-ttu-id="d1261-129">C’est uniquement à des fins de développement.</span><span class="sxs-lookup"><span data-stu-id="d1261-129">It's for development purposes only.</span></span> <span data-ttu-id="d1261-130">Les clés et les valeurs sont stockées dans un fichier de configuration JSON dans l’annuaire du profil utilisateur.</span><span class="sxs-lookup"><span data-stu-id="d1261-130">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="d1261-131">Fonctionnement de l’outil Secret Manager</span><span class="sxs-lookup"><span data-stu-id="d1261-131">How the Secret Manager tool works</span></span>

<span data-ttu-id="d1261-132">L’outil Secret Manager résume les détails de l’implémentation, tels que l’endroit et la façon dont les valeurs sont stockées.</span><span class="sxs-lookup"><span data-stu-id="d1261-132">The Secret Manager tool abstracts away the implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="d1261-133">Vous pouvez utiliser l’outil sans connaître ces détails d’implémentation.</span><span class="sxs-lookup"><span data-stu-id="d1261-133">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="d1261-134">Les valeurs sont stockées dans un fichier de configuration JSON dans un dossier de profil utilisateur protégé par le système sur la machine locale :</span><span class="sxs-lookup"><span data-stu-id="d1261-134">The values are stored in a JSON configuration file in a system-protected user profile folder on the local machine:</span></span>

# <a name="windows"></a>[<span data-ttu-id="d1261-135">Windows</span><span class="sxs-lookup"><span data-stu-id="d1261-135">Windows</span></span>](#tab/windows)

<span data-ttu-id="d1261-136">Chemin du système de fichiers :</span><span class="sxs-lookup"><span data-stu-id="d1261-136">File system path:</span></span>

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[<span data-ttu-id="d1261-137">Linux / macOS</span><span class="sxs-lookup"><span data-stu-id="d1261-137">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="d1261-138">Chemin du système de fichiers :</span><span class="sxs-lookup"><span data-stu-id="d1261-138">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

<span data-ttu-id="d1261-139">Dans les chemins de `<user_secrets_id>` fichier `UserSecretsId` précédents, remplacez par la valeur spécifiée dans le fichier *.csproj.*</span><span class="sxs-lookup"><span data-stu-id="d1261-139">In the preceding file paths, replace `<user_secrets_id>` with the `UserSecretsId` value specified in the *.csproj* file.</span></span>

<span data-ttu-id="d1261-140">N’écrivez pas de code qui dépend de l’emplacement ou du format des données enregistrées avec l’outil Secret Manager.</span><span class="sxs-lookup"><span data-stu-id="d1261-140">Don't write code that depends on the location or format of data saved with the Secret Manager tool.</span></span> <span data-ttu-id="d1261-141">Ces détails de mise en œuvre peuvent changer.</span><span class="sxs-lookup"><span data-stu-id="d1261-141">These implementation details may change.</span></span> <span data-ttu-id="d1261-142">Par exemple, les valeurs secrètes ne sont pas cryptées, mais pourraient l’être à l’avenir.</span><span class="sxs-lookup"><span data-stu-id="d1261-142">For example, the secret values aren't encrypted, but could be in the future.</span></span>

## <a name="enable-secret-storage"></a><span data-ttu-id="d1261-143">Activer le stockage secret</span><span class="sxs-lookup"><span data-stu-id="d1261-143">Enable secret storage</span></span>

<span data-ttu-id="d1261-144">L’outil Secret Manager fonctionne sur des paramètres de configuration spécifiques à un projet stockés dans votre profil d’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="d1261-144">The Secret Manager tool operates on project-specific configuration settings stored in your user profile.</span></span>

<span data-ttu-id="d1261-145">L’outil Secret `init` Manager comprend une commande en .NET Core SDK 3.0.100 ou plus tard.</span><span class="sxs-lookup"><span data-stu-id="d1261-145">The Secret Manager tool includes an `init` command in .NET Core SDK 3.0.100 or later.</span></span> <span data-ttu-id="d1261-146">Pour utiliser les secrets d’utilisateur, exécutez la commande suivante dans l’annuaire du projet :</span><span class="sxs-lookup"><span data-stu-id="d1261-146">To use user secrets, run the following command in the project directory:</span></span>

```dotnetcli
dotnet user-secrets init
```

<span data-ttu-id="d1261-147">La commande précédente `UserSecretsId` ajoute `PropertyGroup` un élément dans un fichier *.csproj.*</span><span class="sxs-lookup"><span data-stu-id="d1261-147">The preceding command adds a `UserSecretsId` element within a `PropertyGroup` of the *.csproj* file.</span></span> <span data-ttu-id="d1261-148">Par défaut, le `UserSecretsId` texte interne est un GUID.</span><span class="sxs-lookup"><span data-stu-id="d1261-148">By default, the inner text of `UserSecretsId` is a GUID.</span></span> <span data-ttu-id="d1261-149">Le texte intérieur est arbitraire, mais est unique au projet.</span><span class="sxs-lookup"><span data-stu-id="d1261-149">The inner text is arbitrary, but is unique to the project.</span></span>

[!code-xml[](app-secrets/samples/3.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

<span data-ttu-id="d1261-150">Dans Visual Studio, cliquez à droite sur le projet dans Solution Explorer, et **sélectionnez Gérer les secrets d’utilisateur** dans le menu contextuelle.</span><span class="sxs-lookup"><span data-stu-id="d1261-150">In Visual Studio, right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="d1261-151">Ce geste `UserSecretsId` ajoute un élément, peuplé d’un GUID, au fichier *.csproj.*</span><span class="sxs-lookup"><span data-stu-id="d1261-151">This gesture adds a `UserSecretsId` element, populated with a GUID, to the *.csproj* file.</span></span>

## <a name="set-a-secret"></a><span data-ttu-id="d1261-152">Définir un secret</span><span class="sxs-lookup"><span data-stu-id="d1261-152">Set a secret</span></span>

<span data-ttu-id="d1261-153">Définissez un secret d’application composé d’une clé et de sa valeur.</span><span class="sxs-lookup"><span data-stu-id="d1261-153">Define an app secret consisting of a key and its value.</span></span> <span data-ttu-id="d1261-154">Le secret est associé à `UserSecretsId` la valeur du projet.</span><span class="sxs-lookup"><span data-stu-id="d1261-154">The secret is associated with the project's `UserSecretsId` value.</span></span> <span data-ttu-id="d1261-155">Par exemple, exécutez la commande suivante à partir de l’annuaire dans lequel le fichier *.csproj* existe :</span><span class="sxs-lookup"><span data-stu-id="d1261-155">For example, run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

<span data-ttu-id="d1261-156">Dans l’exemple précédent, le `Movies` côlon dénote qui `ServiceApiKey` est un objet littéral avec une propriété.</span><span class="sxs-lookup"><span data-stu-id="d1261-156">In the preceding example, the colon denotes that `Movies` is an object literal with a `ServiceApiKey` property.</span></span>

<span data-ttu-id="d1261-157">L’outil Secret Manager peut également être utilisé à partir d’autres répertoires.</span><span class="sxs-lookup"><span data-stu-id="d1261-157">The Secret Manager tool can be used from other directories too.</span></span> <span data-ttu-id="d1261-158">Utilisez `--project` l’option pour fournir la trajectoire du système de fichiers à laquelle le fichier *.csproj* existe.</span><span class="sxs-lookup"><span data-stu-id="d1261-158">Use the `--project` option to supply the file system path at which the *.csproj* file exists.</span></span> <span data-ttu-id="d1261-159">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="d1261-159">For example:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a><span data-ttu-id="d1261-160">La structure JSON aplatit dans Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d1261-160">JSON structure flattening in Visual Studio</span></span>

<span data-ttu-id="d1261-161">Visual Studio’s **Manage User Secrets** geste ouvre un fichier *secrets.json* dans l’éditeur de texte.</span><span class="sxs-lookup"><span data-stu-id="d1261-161">Visual Studio's **Manage User Secrets** gesture opens a *secrets.json* file in the text editor.</span></span> <span data-ttu-id="d1261-162">Remplacez le contenu de *secrets.json* par les paires de valeur clé à stocker.</span><span class="sxs-lookup"><span data-stu-id="d1261-162">Replace the contents of *secrets.json* with the key-value pairs to be stored.</span></span> <span data-ttu-id="d1261-163">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="d1261-163">For example:</span></span>

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="d1261-164">La structure JSON est aplatie `dotnet user-secrets remove` `dotnet user-secrets set`après modifications via ou .</span><span class="sxs-lookup"><span data-stu-id="d1261-164">The JSON structure is flattened after modifications via `dotnet user-secrets remove` or `dotnet user-secrets set`.</span></span> <span data-ttu-id="d1261-165">Par exemple, `dotnet user-secrets remove "Movies:ConnectionString"` la `Movies` course s’effondre l’objet littéral.</span><span class="sxs-lookup"><span data-stu-id="d1261-165">For example, running `dotnet user-secrets remove "Movies:ConnectionString"` collapses the `Movies` object literal.</span></span> <span data-ttu-id="d1261-166">Le fichier modifié ressemble à ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="d1261-166">The modified file resembles the following:</span></span>

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a><span data-ttu-id="d1261-167">Définir plusieurs secrets</span><span class="sxs-lookup"><span data-stu-id="d1261-167">Set multiple secrets</span></span>

<span data-ttu-id="d1261-168">Un lot de secrets peut être réglé `set` en tuyautant JSON à la commande.</span><span class="sxs-lookup"><span data-stu-id="d1261-168">A batch of secrets can be set by piping JSON to the `set` command.</span></span> <span data-ttu-id="d1261-169">Dans l’exemple suivant, le contenu du fichier *input.json* est acheminé à la `set` commande.</span><span class="sxs-lookup"><span data-stu-id="d1261-169">In the following example, the *input.json* file's contents are piped to the `set` command.</span></span>

# <a name="windows"></a>[<span data-ttu-id="d1261-170">Windows</span><span class="sxs-lookup"><span data-stu-id="d1261-170">Windows</span></span>](#tab/windows)

<span data-ttu-id="d1261-171">Ouvrez une coque de commande et exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="d1261-171">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[<span data-ttu-id="d1261-172">Linux / macOS</span><span class="sxs-lookup"><span data-stu-id="d1261-172">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="d1261-173">Ouvrez une coque de commande et exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="d1261-173">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a><span data-ttu-id="d1261-174">Accéder à un secret</span><span class="sxs-lookup"><span data-stu-id="d1261-174">Access a secret</span></span>

<span data-ttu-id="d1261-175">[L’API ASP.NET Core Configuration](xref:fundamentals/configuration/index) donne accès aux secrets de Secret Manager.</span><span class="sxs-lookup"><span data-stu-id="d1261-175">The [ASP.NET Core Configuration API](xref:fundamentals/configuration/index) provides access to Secret Manager secrets.</span></span>

<span data-ttu-id="d1261-176">La source de configuration des secrets d’utilisateur <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> est automatiquement ajoutée en mode de développement lorsque le projet appelle à l’initialisation d’une nouvelle instance de l’hôte avec des défauts préconfigurés.</span><span class="sxs-lookup"><span data-stu-id="d1261-176">The user secrets configuration source is automatically added in development mode when the project calls <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> to initialize a new instance of the host with preconfigured defaults.</span></span> <span data-ttu-id="d1261-177">`CreateDefaultBuilder`appels <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> lorsque <xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName> <xref:Microsoft.Extensions.Hosting.EnvironmentName.Development>le est :</span><span class="sxs-lookup"><span data-stu-id="d1261-177">`CreateDefaultBuilder` calls <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> when the <xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName> is <xref:Microsoft.Extensions.Hosting.EnvironmentName.Development>:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program.cs?name=snippet_CreateHostBuilder&highlight=2)]

<span data-ttu-id="d1261-178">Lorsqu’il `CreateDefaultBuilder` n’est pas appelé, ajoutez <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A>la source de configuration des secrets d’utilisateur explicitement en appelant .</span><span class="sxs-lookup"><span data-stu-id="d1261-178">When `CreateDefaultBuilder` isn't called, add the user secrets configuration source explicitly by calling <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A>.</span></span> <span data-ttu-id="d1261-179">N’appelez `AddUserSecrets` que lorsque l’application s’exécute dans l’environnement développement, comme le montre l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="d1261-179">Call `AddUserSecrets` only when the app runs in the Development environment, as shown in the following example:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program2.cs?name=snippet_Host&highlight=6-9)]

<span data-ttu-id="d1261-180">Les secrets d’utilisateur `Configuration` peuvent être récupérés via l’API :</span><span class="sxs-lookup"><span data-stu-id="d1261-180">User secrets can be retrieved via the `Configuration` API:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a><span data-ttu-id="d1261-181">Secrets de carte à un POCO</span><span class="sxs-lookup"><span data-stu-id="d1261-181">Map secrets to a POCO</span></span>

<span data-ttu-id="d1261-182">Cartographier un objet entier littéral à un POCO (une simple classe .NET avec propriétés) est utile pour agréger les propriétés connexes.</span><span class="sxs-lookup"><span data-stu-id="d1261-182">Mapping an entire object literal to a POCO (a simple .NET class with properties) is useful for aggregating related properties.</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="d1261-183">Pour cartographier les secrets précédents à `Configuration` un OCO, utilisez la fonction de liaison du [graphique](xref:fundamentals/configuration/index#bind-to-an-object-graph) objet de l’API.</span><span class="sxs-lookup"><span data-stu-id="d1261-183">To map the preceding secrets to a POCO, use the `Configuration` API's [object graph binding](xref:fundamentals/configuration/index#bind-to-an-object-graph) feature.</span></span> <span data-ttu-id="d1261-184">Le code suivant se `MovieSettings` lie à un `ServiceApiKey` OCO personnalisé et accède à la valeur de la propriété :</span><span class="sxs-lookup"><span data-stu-id="d1261-184">The following code binds to a custom `MovieSettings` POCO and accesses the `ServiceApiKey` property value:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

<span data-ttu-id="d1261-185">Les `Movies:ConnectionString` `Movies:ServiceApiKey` secrets et les secrets sont `MovieSettings`cartographiés sur les propriétés respectives dans :</span><span class="sxs-lookup"><span data-stu-id="d1261-185">The `Movies:ConnectionString` and `Movies:ServiceApiKey` secrets are mapped to the respective properties in `MovieSettings`:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a><span data-ttu-id="d1261-186">Remplacement de cordes avec des secrets</span><span class="sxs-lookup"><span data-stu-id="d1261-186">String replacement with secrets</span></span>

<span data-ttu-id="d1261-187">Stocker des mots de passe en texte clair n’est pas sûr.</span><span class="sxs-lookup"><span data-stu-id="d1261-187">Storing passwords in plain text is insecure.</span></span> <span data-ttu-id="d1261-188">Par exemple, une chaîne de connexion de base de données stockée dans *appsettings.json* peut inclure un mot de passe pour l’utilisateur spécifié :</span><span class="sxs-lookup"><span data-stu-id="d1261-188">For example, a database connection string stored in *appsettings.json* may include a password for the specified user:</span></span>

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

<span data-ttu-id="d1261-189">Une approche plus sûre consiste à stocker le mot de passe comme secret.</span><span class="sxs-lookup"><span data-stu-id="d1261-189">A more secure approach is to store the password as a secret.</span></span> <span data-ttu-id="d1261-190">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="d1261-190">For example:</span></span>

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

<span data-ttu-id="d1261-191">Retirez `Password` la paire de valeur de clé de la chaîne de connexion dans *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="d1261-191">Remove the `Password` key-value pair from the connection string in *appsettings.json*.</span></span> <span data-ttu-id="d1261-192">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="d1261-192">For example:</span></span>

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings.json?highlight=3)]

<span data-ttu-id="d1261-193">La valeur du secret peut <xref:System.Data.SqlClient.SqlConnectionStringBuilder> être fixée <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> sur la propriété d’un objet pour compléter la chaîne de connexion :</span><span class="sxs-lookup"><span data-stu-id="d1261-193">The secret's value can be set on a <xref:System.Data.SqlClient.SqlConnectionStringBuilder> object's <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> property to complete the connection string:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a><span data-ttu-id="d1261-194">Énumérer les secrets</span><span class="sxs-lookup"><span data-stu-id="d1261-194">List the secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="d1261-195">Exécutez la commande suivante de l’annuaire dans lequel le fichier *.csproj* existe :</span><span class="sxs-lookup"><span data-stu-id="d1261-195">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets list
```

<span data-ttu-id="d1261-196">Vous obtenez la sortie suivante :</span><span class="sxs-lookup"><span data-stu-id="d1261-196">The following output appears:</span></span>

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

<span data-ttu-id="d1261-197">Dans l’exemple précédent, un côlon dans les noms clés dénote la hiérarchie de l’objet dans *secrets.json*.</span><span class="sxs-lookup"><span data-stu-id="d1261-197">In the preceding example, a colon in the key names denotes the object hierarchy within *secrets.json*.</span></span>

## <a name="remove-a-single-secret"></a><span data-ttu-id="d1261-198">Supprimer un seul secret</span><span class="sxs-lookup"><span data-stu-id="d1261-198">Remove a single secret</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="d1261-199">Exécutez la commande suivante de l’annuaire dans lequel le fichier *.csproj* existe :</span><span class="sxs-lookup"><span data-stu-id="d1261-199">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

<span data-ttu-id="d1261-200">Le fichier *secrets.json* de l’application a été modifié `MoviesConnectionString` pour supprimer la paire de valeur clé associée à la clé :</span><span class="sxs-lookup"><span data-stu-id="d1261-200">The app's *secrets.json* file was modified to remove the key-value pair associated with the `MoviesConnectionString` key:</span></span>

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="d1261-201">`dotnet user-secrets list`affiche le message suivant :</span><span class="sxs-lookup"><span data-stu-id="d1261-201">`dotnet user-secrets list` displays the following message:</span></span>

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a><span data-ttu-id="d1261-202">Supprimer tous les secrets</span><span class="sxs-lookup"><span data-stu-id="d1261-202">Remove all secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="d1261-203">Exécutez la commande suivante de l’annuaire dans lequel le fichier *.csproj* existe :</span><span class="sxs-lookup"><span data-stu-id="d1261-203">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets clear
```

<span data-ttu-id="d1261-204">Tous les secrets d’utilisateur de l’application ont été supprimés du fichier *secrets.json:*</span><span class="sxs-lookup"><span data-stu-id="d1261-204">All user secrets for the app have been deleted from the *secrets.json* file:</span></span>

```json
{}
```

<span data-ttu-id="d1261-205">Exécution `dotnet user-secrets list` affiche le message suivant:</span><span class="sxs-lookup"><span data-stu-id="d1261-205">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a><span data-ttu-id="d1261-206">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="d1261-206">Additional resources</span></span>

* <span data-ttu-id="d1261-207">Voir [ce numéro](https://github.com/dotnet/AspNetCore.Docs/issues/16328) pour plus d’informations sur l’accès Secret Manager à partir de l’IIS.</span><span class="sxs-lookup"><span data-stu-id="d1261-207">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/16328) for information on accessing Secret Manager from IIS.</span></span>
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d1261-208">Par [Rick Anderson](https://twitter.com/RickAndMSFT), Daniel [Roth](https://github.com/danroth27), et [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="d1261-208">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="d1261-209">[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([comment télécharger](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d1261-209">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="d1261-210">Ce document explique les techniques de stockage et de récupération de données sensibles lors du développement d’une application ASP.NET Core sur une machine de développement.</span><span class="sxs-lookup"><span data-stu-id="d1261-210">This document explains techniques for storing and retrieving sensitive data during development of an ASP.NET Core app on a development machine.</span></span> <span data-ttu-id="d1261-211">Ne stockez jamais de mots de passe ou d’autres données sensibles dans le code source.</span><span class="sxs-lookup"><span data-stu-id="d1261-211">Never store passwords or other sensitive data in source code.</span></span> <span data-ttu-id="d1261-212">Les secrets de production ne doivent pas être utilisés pour le développement ou le test.</span><span class="sxs-lookup"><span data-stu-id="d1261-212">Production secrets shouldn't be used for development or test.</span></span> <span data-ttu-id="d1261-213">Les secrets ne doivent pas être déployés avec l’application.</span><span class="sxs-lookup"><span data-stu-id="d1261-213">Secrets shouldn't be deployed with the app.</span></span> <span data-ttu-id="d1261-214">Au lieu de cela, des secrets devraient être mis à disposition dans l’environnement de production par le biais d’un moyen contrôlé comme les variables de l’environnement, Azure Key Vault, etc. Vous pouvez stocker et protéger les secrets de test et de production Azure avec le [fournisseur de configuration Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="d1261-214">Instead, secrets should be made available in the production environment through a controlled means like environment variables, Azure Key Vault, etc. You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

## <a name="environment-variables"></a><span data-ttu-id="d1261-215">Variables d'environnement</span><span class="sxs-lookup"><span data-stu-id="d1261-215">Environment variables</span></span>

<span data-ttu-id="d1261-216">Les variables de l’environnement sont utilisées pour éviter le stockage des secrets d’application dans le code ou dans les fichiers de configuration locaux.</span><span class="sxs-lookup"><span data-stu-id="d1261-216">Environment variables are used to avoid storage of app secrets in code or in local configuration files.</span></span> <span data-ttu-id="d1261-217">Les variables de l’environnement remplacent les valeurs de configuration pour toutes les sources de configuration précédemment spécifiées.</span><span class="sxs-lookup"><span data-stu-id="d1261-217">Environment variables override configuration values for all previously specified configuration sources.</span></span>

<span data-ttu-id="d1261-218">Considérez une application web ASP.NET Core dans laquelle la sécurité **des comptes utilisateur individuels** est activée.</span><span class="sxs-lookup"><span data-stu-id="d1261-218">Consider an ASP.NET Core web app in which **Individual User Accounts** security is enabled.</span></span> <span data-ttu-id="d1261-219">Une chaîne de connexion de base de données par défaut est incluse `DefaultConnection`dans le fichier *appsettings.json* du projet avec la clé .</span><span class="sxs-lookup"><span data-stu-id="d1261-219">A default database connection string is included in the project's *appsettings.json* file with the key `DefaultConnection`.</span></span> <span data-ttu-id="d1261-220">La chaîne de connexion par défaut est pour LocalDB, qui s’exécute en mode utilisateur et ne nécessite pas de mot de passe.</span><span class="sxs-lookup"><span data-stu-id="d1261-220">The default connection string is for LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="d1261-221">Pendant le déploiement `DefaultConnection` de l’application, la valeur clé peut être remplacée par la valeur d’une variable d’environnement.</span><span class="sxs-lookup"><span data-stu-id="d1261-221">During app deployment, the `DefaultConnection` key value can be overridden with an environment variable's value.</span></span> <span data-ttu-id="d1261-222">La variable d’environnement peut stocker la chaîne de connexion complète avec des informations d’identification sensibles.</span><span class="sxs-lookup"><span data-stu-id="d1261-222">The environment variable may store the complete connection string with sensitive credentials.</span></span>

> [!WARNING]
> <span data-ttu-id="d1261-223">Les variables de l’environnement sont généralement stockées dans un texte simple et non chiffré.</span><span class="sxs-lookup"><span data-stu-id="d1261-223">Environment variables are generally stored in plain, unencrypted text.</span></span> <span data-ttu-id="d1261-224">Si la machine ou le processus est compromis, les variables de l’environnement peuvent être consultées par des parties non fiables.</span><span class="sxs-lookup"><span data-stu-id="d1261-224">If the machine or process is compromised, environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="d1261-225">Des mesures supplémentaires pour empêcher la divulgation de secrets d’utilisateur peuvent être nécessaires.</span><span class="sxs-lookup"><span data-stu-id="d1261-225">Additional measures to prevent disclosure of user secrets may be required.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a><span data-ttu-id="d1261-226">Gestionnaire secret</span><span class="sxs-lookup"><span data-stu-id="d1261-226">Secret Manager</span></span>

<span data-ttu-id="d1261-227">L’outil Secret Manager stocke des données sensibles lors du développement d’un projet ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d1261-227">The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.</span></span> <span data-ttu-id="d1261-228">Dans ce contexte, un élément de données sensibles est un secret d’application.</span><span class="sxs-lookup"><span data-stu-id="d1261-228">In this context, a piece of sensitive data is an app secret.</span></span> <span data-ttu-id="d1261-229">Les secrets de l’application sont stockés dans un endroit séparé de l’arbre du projet.</span><span class="sxs-lookup"><span data-stu-id="d1261-229">App secrets are stored in a separate location from the project tree.</span></span> <span data-ttu-id="d1261-230">Les secrets de l’application sont associés à un projet spécifique ou partagés sur plusieurs projets.</span><span class="sxs-lookup"><span data-stu-id="d1261-230">The app secrets are associated with a specific project or shared across several projects.</span></span> <span data-ttu-id="d1261-231">Les secrets de l’application ne sont pas vérifiés dans le contrôle source.</span><span class="sxs-lookup"><span data-stu-id="d1261-231">The app secrets aren't checked into source control.</span></span>

> [!WARNING]
> <span data-ttu-id="d1261-232">L’outil Secret Manager ne crypte pas les secrets stockés et ne doit pas être traité comme un magasin de confiance.</span><span class="sxs-lookup"><span data-stu-id="d1261-232">The Secret Manager tool doesn't encrypt the stored secrets and shouldn't be treated as a trusted store.</span></span> <span data-ttu-id="d1261-233">C’est uniquement à des fins de développement.</span><span class="sxs-lookup"><span data-stu-id="d1261-233">It's for development purposes only.</span></span> <span data-ttu-id="d1261-234">Les clés et les valeurs sont stockées dans un fichier de configuration JSON dans l’annuaire du profil utilisateur.</span><span class="sxs-lookup"><span data-stu-id="d1261-234">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="d1261-235">Fonctionnement de l’outil Secret Manager</span><span class="sxs-lookup"><span data-stu-id="d1261-235">How the Secret Manager tool works</span></span>

<span data-ttu-id="d1261-236">L’outil Secret Manager résume les détails de l’implémentation, tels que l’endroit et la façon dont les valeurs sont stockées.</span><span class="sxs-lookup"><span data-stu-id="d1261-236">The Secret Manager tool abstracts away the implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="d1261-237">Vous pouvez utiliser l’outil sans connaître ces détails d’implémentation.</span><span class="sxs-lookup"><span data-stu-id="d1261-237">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="d1261-238">Les valeurs sont stockées dans un fichier de configuration JSON dans un dossier de profil utilisateur protégé par le système sur la machine locale :</span><span class="sxs-lookup"><span data-stu-id="d1261-238">The values are stored in a JSON configuration file in a system-protected user profile folder on the local machine:</span></span>

# <a name="windows"></a>[<span data-ttu-id="d1261-239">Windows</span><span class="sxs-lookup"><span data-stu-id="d1261-239">Windows</span></span>](#tab/windows)

<span data-ttu-id="d1261-240">Chemin du système de fichiers :</span><span class="sxs-lookup"><span data-stu-id="d1261-240">File system path:</span></span>

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[<span data-ttu-id="d1261-241">Linux / macOS</span><span class="sxs-lookup"><span data-stu-id="d1261-241">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="d1261-242">Chemin du système de fichiers :</span><span class="sxs-lookup"><span data-stu-id="d1261-242">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

<span data-ttu-id="d1261-243">Dans les chemins de `<user_secrets_id>` fichier `UserSecretsId` précédents, remplacez par la valeur spécifiée dans le fichier *.csproj.*</span><span class="sxs-lookup"><span data-stu-id="d1261-243">In the preceding file paths, replace `<user_secrets_id>` with the `UserSecretsId` value specified in the *.csproj* file.</span></span>

<span data-ttu-id="d1261-244">N’écrivez pas de code qui dépend de l’emplacement ou du format des données enregistrées avec l’outil Secret Manager.</span><span class="sxs-lookup"><span data-stu-id="d1261-244">Don't write code that depends on the location or format of data saved with the Secret Manager tool.</span></span> <span data-ttu-id="d1261-245">Ces détails de mise en œuvre peuvent changer.</span><span class="sxs-lookup"><span data-stu-id="d1261-245">These implementation details may change.</span></span> <span data-ttu-id="d1261-246">Par exemple, les valeurs secrètes ne sont pas cryptées, mais pourraient l’être à l’avenir.</span><span class="sxs-lookup"><span data-stu-id="d1261-246">For example, the secret values aren't encrypted, but could be in the future.</span></span>

## <a name="enable-secret-storage"></a><span data-ttu-id="d1261-247">Activer le stockage secret</span><span class="sxs-lookup"><span data-stu-id="d1261-247">Enable secret storage</span></span>

<span data-ttu-id="d1261-248">L’outil Secret Manager fonctionne sur des paramètres de configuration spécifiques à un projet stockés dans votre profil d’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="d1261-248">The Secret Manager tool operates on project-specific configuration settings stored in your user profile.</span></span>

<span data-ttu-id="d1261-249">Pour utiliser les secrets `UserSecretsId` d’utilisateur, définissez un élément dans un `PropertyGroup` fichier *.csproj.*</span><span class="sxs-lookup"><span data-stu-id="d1261-249">To use user secrets, define a `UserSecretsId` element within a `PropertyGroup` of the *.csproj* file.</span></span> <span data-ttu-id="d1261-250">Le texte `UserSecretsId` intérieur est arbitraire, mais est unique au projet.</span><span class="sxs-lookup"><span data-stu-id="d1261-250">The inner text of `UserSecretsId` is arbitrary, but is unique to the project.</span></span> <span data-ttu-id="d1261-251">Les développeurs génèrent généralement `UserSecretsId`un GUID pour le .</span><span class="sxs-lookup"><span data-stu-id="d1261-251">Developers typically generate a GUID for the `UserSecretsId`.</span></span>

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

> [!TIP]
> <span data-ttu-id="d1261-252">Dans Visual Studio, cliquez à droite sur le projet dans Solution Explorer, et **sélectionnez Gérer les secrets d’utilisateur** dans le menu contextuelle.</span><span class="sxs-lookup"><span data-stu-id="d1261-252">In Visual Studio, right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="d1261-253">Ce geste `UserSecretsId` ajoute un élément, peuplé d’un GUID, au fichier *.csproj.*</span><span class="sxs-lookup"><span data-stu-id="d1261-253">This gesture adds a `UserSecretsId` element, populated with a GUID, to the *.csproj* file.</span></span>

## <a name="set-a-secret"></a><span data-ttu-id="d1261-254">Définir un secret</span><span class="sxs-lookup"><span data-stu-id="d1261-254">Set a secret</span></span>

<span data-ttu-id="d1261-255">Définissez un secret d’application composé d’une clé et de sa valeur.</span><span class="sxs-lookup"><span data-stu-id="d1261-255">Define an app secret consisting of a key and its value.</span></span> <span data-ttu-id="d1261-256">Le secret est associé à `UserSecretsId` la valeur du projet.</span><span class="sxs-lookup"><span data-stu-id="d1261-256">The secret is associated with the project's `UserSecretsId` value.</span></span> <span data-ttu-id="d1261-257">Par exemple, exécutez la commande suivante à partir de l’annuaire dans lequel le fichier *.csproj* existe :</span><span class="sxs-lookup"><span data-stu-id="d1261-257">For example, run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

<span data-ttu-id="d1261-258">Dans l’exemple précédent, le `Movies` côlon dénote qui `ServiceApiKey` est un objet littéral avec une propriété.</span><span class="sxs-lookup"><span data-stu-id="d1261-258">In the preceding example, the colon denotes that `Movies` is an object literal with a `ServiceApiKey` property.</span></span>

<span data-ttu-id="d1261-259">L’outil Secret Manager peut également être utilisé à partir d’autres répertoires.</span><span class="sxs-lookup"><span data-stu-id="d1261-259">The Secret Manager tool can be used from other directories too.</span></span> <span data-ttu-id="d1261-260">Utilisez `--project` l’option pour fournir la trajectoire du système de fichiers à laquelle le fichier *.csproj* existe.</span><span class="sxs-lookup"><span data-stu-id="d1261-260">Use the `--project` option to supply the file system path at which the *.csproj* file exists.</span></span> <span data-ttu-id="d1261-261">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="d1261-261">For example:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a><span data-ttu-id="d1261-262">La structure JSON aplatit dans Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d1261-262">JSON structure flattening in Visual Studio</span></span>

<span data-ttu-id="d1261-263">Visual Studio’s **Manage User Secrets** geste ouvre un fichier *secrets.json* dans l’éditeur de texte.</span><span class="sxs-lookup"><span data-stu-id="d1261-263">Visual Studio's **Manage User Secrets** gesture opens a *secrets.json* file in the text editor.</span></span> <span data-ttu-id="d1261-264">Remplacez le contenu de *secrets.json* par les paires de valeur clé à stocker.</span><span class="sxs-lookup"><span data-stu-id="d1261-264">Replace the contents of *secrets.json* with the key-value pairs to be stored.</span></span> <span data-ttu-id="d1261-265">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="d1261-265">For example:</span></span>

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="d1261-266">La structure JSON est aplatie `dotnet user-secrets remove` `dotnet user-secrets set`après modifications via ou .</span><span class="sxs-lookup"><span data-stu-id="d1261-266">The JSON structure is flattened after modifications via `dotnet user-secrets remove` or `dotnet user-secrets set`.</span></span> <span data-ttu-id="d1261-267">Par exemple, `dotnet user-secrets remove "Movies:ConnectionString"` la `Movies` course s’effondre l’objet littéral.</span><span class="sxs-lookup"><span data-stu-id="d1261-267">For example, running `dotnet user-secrets remove "Movies:ConnectionString"` collapses the `Movies` object literal.</span></span> <span data-ttu-id="d1261-268">Le fichier modifié ressemble à ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="d1261-268">The modified file resembles the following:</span></span>

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a><span data-ttu-id="d1261-269">Définir plusieurs secrets</span><span class="sxs-lookup"><span data-stu-id="d1261-269">Set multiple secrets</span></span>

<span data-ttu-id="d1261-270">Un lot de secrets peut être réglé `set` en tuyautant JSON à la commande.</span><span class="sxs-lookup"><span data-stu-id="d1261-270">A batch of secrets can be set by piping JSON to the `set` command.</span></span> <span data-ttu-id="d1261-271">Dans l’exemple suivant, le contenu du fichier *input.json* est acheminé à la `set` commande.</span><span class="sxs-lookup"><span data-stu-id="d1261-271">In the following example, the *input.json* file's contents are piped to the `set` command.</span></span>

# <a name="windows"></a>[<span data-ttu-id="d1261-272">Windows</span><span class="sxs-lookup"><span data-stu-id="d1261-272">Windows</span></span>](#tab/windows)

<span data-ttu-id="d1261-273">Ouvrez une coque de commande et exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="d1261-273">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[<span data-ttu-id="d1261-274">Linux / macOS</span><span class="sxs-lookup"><span data-stu-id="d1261-274">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="d1261-275">Ouvrez une coque de commande et exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="d1261-275">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a><span data-ttu-id="d1261-276">Accéder à un secret</span><span class="sxs-lookup"><span data-stu-id="d1261-276">Access a secret</span></span>

<span data-ttu-id="d1261-277">[L’API ASP.NET Core Configuration](xref:fundamentals/configuration/index) donne accès aux secrets de Secret Manager.</span><span class="sxs-lookup"><span data-stu-id="d1261-277">The [ASP.NET Core Configuration API](xref:fundamentals/configuration/index) provides access to Secret Manager secrets.</span></span>

<span data-ttu-id="d1261-278">Si votre projet cible .NET Framework, installez le package [Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet.</span><span class="sxs-lookup"><span data-stu-id="d1261-278">If your project targets .NET Framework, install the [Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet package.</span></span>

<span data-ttu-id="d1261-279">Dans ASP.NET Core 2.0 ou plus tard, la source de configuration des <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> secrets d’utilisateur est automatiquement ajoutée en mode de développement lorsque le projet appelle à parasimer une nouvelle instance de l’hôte avec des défauts préconfigurés.</span><span class="sxs-lookup"><span data-stu-id="d1261-279">In ASP.NET Core 2.0 or later, the user secrets configuration source is automatically added in development mode when the project calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> to initialize a new instance of the host with preconfigured defaults.</span></span> <span data-ttu-id="d1261-280">`CreateDefaultBuilder`appels <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> lorsque <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>le est :</span><span class="sxs-lookup"><span data-stu-id="d1261-280">`CreateDefaultBuilder` calls <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> when the <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> is <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Program.cs?name=snippet_CreateWebHostBuilder&highlight=2)]

<span data-ttu-id="d1261-281">Lorsqu’il `CreateDefaultBuilder` n’est pas appelé, ajoutez <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> explicitement `Startup` la source de configuration des secrets d’utilisateur en appelant le constructeur.</span><span class="sxs-lookup"><span data-stu-id="d1261-281">When `CreateDefaultBuilder` isn't called, add the user secrets configuration source explicitly by calling <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> in the `Startup` constructor.</span></span> <span data-ttu-id="d1261-282">N’appelez `AddUserSecrets` que lorsque l’application s’exécute dans l’environnement développement, comme le montre l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="d1261-282">Call `AddUserSecrets` only when the app runs in the Development environment, as shown in the following example:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_StartupConstructor&highlight=12)]

<span data-ttu-id="d1261-283">Les secrets d’utilisateur `Configuration` peuvent être récupérés via l’API :</span><span class="sxs-lookup"><span data-stu-id="d1261-283">User secrets can be retrieved via the `Configuration` API:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a><span data-ttu-id="d1261-284">Secrets de carte à un POCO</span><span class="sxs-lookup"><span data-stu-id="d1261-284">Map secrets to a POCO</span></span>

<span data-ttu-id="d1261-285">Cartographier un objet entier littéral à un POCO (une simple classe .NET avec propriétés) est utile pour agréger les propriétés connexes.</span><span class="sxs-lookup"><span data-stu-id="d1261-285">Mapping an entire object literal to a POCO (a simple .NET class with properties) is useful for aggregating related properties.</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="d1261-286">Pour cartographier les secrets précédents à `Configuration` un OCO, utilisez la fonction de liaison du [graphique](xref:fundamentals/configuration/index#bind-to-an-object-graph) objet de l’API.</span><span class="sxs-lookup"><span data-stu-id="d1261-286">To map the preceding secrets to a POCO, use the `Configuration` API's [object graph binding](xref:fundamentals/configuration/index#bind-to-an-object-graph) feature.</span></span> <span data-ttu-id="d1261-287">Le code suivant se `MovieSettings` lie à un `ServiceApiKey` OCO personnalisé et accède à la valeur de la propriété :</span><span class="sxs-lookup"><span data-stu-id="d1261-287">The following code binds to a custom `MovieSettings` POCO and accesses the `ServiceApiKey` property value:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

<span data-ttu-id="d1261-288">Les `Movies:ConnectionString` `Movies:ServiceApiKey` secrets et les secrets sont `MovieSettings`cartographiés sur les propriétés respectives dans :</span><span class="sxs-lookup"><span data-stu-id="d1261-288">The `Movies:ConnectionString` and `Movies:ServiceApiKey` secrets are mapped to the respective properties in `MovieSettings`:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a><span data-ttu-id="d1261-289">Remplacement de cordes avec des secrets</span><span class="sxs-lookup"><span data-stu-id="d1261-289">String replacement with secrets</span></span>

<span data-ttu-id="d1261-290">Stocker des mots de passe en texte clair n’est pas sûr.</span><span class="sxs-lookup"><span data-stu-id="d1261-290">Storing passwords in plain text is insecure.</span></span> <span data-ttu-id="d1261-291">Par exemple, une chaîne de connexion de base de données stockée dans *appsettings.json* peut inclure un mot de passe pour l’utilisateur spécifié :</span><span class="sxs-lookup"><span data-stu-id="d1261-291">For example, a database connection string stored in *appsettings.json* may include a password for the specified user:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

<span data-ttu-id="d1261-292">Une approche plus sûre consiste à stocker le mot de passe comme secret.</span><span class="sxs-lookup"><span data-stu-id="d1261-292">A more secure approach is to store the password as a secret.</span></span> <span data-ttu-id="d1261-293">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="d1261-293">For example:</span></span>

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

<span data-ttu-id="d1261-294">Retirez `Password` la paire de valeur de clé de la chaîne de connexion dans *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="d1261-294">Remove the `Password` key-value pair from the connection string in *appsettings.json*.</span></span> <span data-ttu-id="d1261-295">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="d1261-295">For example:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

<span data-ttu-id="d1261-296">La valeur du secret peut <xref:System.Data.SqlClient.SqlConnectionStringBuilder> être fixée <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> sur la propriété d’un objet pour compléter la chaîne de connexion :</span><span class="sxs-lookup"><span data-stu-id="d1261-296">The secret's value can be set on a <xref:System.Data.SqlClient.SqlConnectionStringBuilder> object's <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> property to complete the connection string:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a><span data-ttu-id="d1261-297">Énumérer les secrets</span><span class="sxs-lookup"><span data-stu-id="d1261-297">List the secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="d1261-298">Exécutez la commande suivante de l’annuaire dans lequel le fichier *.csproj* existe :</span><span class="sxs-lookup"><span data-stu-id="d1261-298">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets list
```

<span data-ttu-id="d1261-299">Vous obtenez la sortie suivante :</span><span class="sxs-lookup"><span data-stu-id="d1261-299">The following output appears:</span></span>

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

<span data-ttu-id="d1261-300">Dans l’exemple précédent, un côlon dans les noms clés dénote la hiérarchie de l’objet dans *secrets.json*.</span><span class="sxs-lookup"><span data-stu-id="d1261-300">In the preceding example, a colon in the key names denotes the object hierarchy within *secrets.json*.</span></span>

## <a name="remove-a-single-secret"></a><span data-ttu-id="d1261-301">Supprimer un seul secret</span><span class="sxs-lookup"><span data-stu-id="d1261-301">Remove a single secret</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="d1261-302">Exécutez la commande suivante de l’annuaire dans lequel le fichier *.csproj* existe :</span><span class="sxs-lookup"><span data-stu-id="d1261-302">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

<span data-ttu-id="d1261-303">Le fichier *secrets.json* de l’application a été modifié `MoviesConnectionString` pour supprimer la paire de valeur clé associée à la clé :</span><span class="sxs-lookup"><span data-stu-id="d1261-303">The app's *secrets.json* file was modified to remove the key-value pair associated with the `MoviesConnectionString` key:</span></span>

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="d1261-304">Exécution `dotnet user-secrets list` affiche le message suivant:</span><span class="sxs-lookup"><span data-stu-id="d1261-304">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a><span data-ttu-id="d1261-305">Supprimer tous les secrets</span><span class="sxs-lookup"><span data-stu-id="d1261-305">Remove all secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="d1261-306">Exécutez la commande suivante de l’annuaire dans lequel le fichier *.csproj* existe :</span><span class="sxs-lookup"><span data-stu-id="d1261-306">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets clear
```

<span data-ttu-id="d1261-307">Tous les secrets d’utilisateur de l’application ont été supprimés du fichier *secrets.json:*</span><span class="sxs-lookup"><span data-stu-id="d1261-307">All user secrets for the app have been deleted from the *secrets.json* file:</span></span>

```json
{}
```

<span data-ttu-id="d1261-308">Exécution `dotnet user-secrets list` affiche le message suivant:</span><span class="sxs-lookup"><span data-stu-id="d1261-308">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a><span data-ttu-id="d1261-309">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="d1261-309">Additional resources</span></span>

* <span data-ttu-id="d1261-310">Voir [ce numéro](https://github.com/dotnet/AspNetCore.Docs/issues/16328) pour plus d’informations sur l’accès Secret Manager à partir de l’IIS.</span><span class="sxs-lookup"><span data-stu-id="d1261-310">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/16328) for information on accessing Secret Manager from IIS.</span></span>
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end