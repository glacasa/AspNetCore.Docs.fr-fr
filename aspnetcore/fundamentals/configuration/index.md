---
<span data-ttu-id="db9ac-101">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-102">'Blazor'</span></span>
- <span data-ttu-id="db9ac-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-103">'Identity'</span></span>
- <span data-ttu-id="db9ac-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-105">'Razor'</span></span>
- <span data-ttu-id="db9ac-106">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-106">'SignalR' uid:</span></span> 

---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="db9ac-107">Configuration dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="db9ac-107">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="db9ac-108">Par [Rick Anderson](https://twitter.com/RickAndMSFT) et [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="db9ac-108">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="db9ac-109">La configuration dans ASP.NET Core est effectuée à l’aide d’un ou de plusieurs [fournisseurs de configuration](#cp).</span><span class="sxs-lookup"><span data-stu-id="db9ac-109">Configuration in ASP.NET Core is performed using one or more [configuration providers](#cp).</span></span> <span data-ttu-id="db9ac-110">Les fournisseurs de configuration lisent les données de configuration des paires clé-valeur à l’aide d’une variété de sources de configuration :</span><span class="sxs-lookup"><span data-stu-id="db9ac-110">Configuration providers read configuration data from key-value pairs using a variety of configuration sources:</span></span>

* <span data-ttu-id="db9ac-111">Fichiers de paramètres, tels que *appSettings. JSON*</span><span class="sxs-lookup"><span data-stu-id="db9ac-111">Settings files, such as *appsettings.json*</span></span>
* <span data-ttu-id="db9ac-112">Variables d'environnement</span><span class="sxs-lookup"><span data-stu-id="db9ac-112">Environment variables</span></span>
* <span data-ttu-id="db9ac-113">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="db9ac-113">Azure Key Vault</span></span>
* <span data-ttu-id="db9ac-114">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="db9ac-114">Azure App Configuration</span></span>
* <span data-ttu-id="db9ac-115">Arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="db9ac-115">Command-line arguments</span></span>
* <span data-ttu-id="db9ac-116">Fournisseurs personnalisés, installés ou créés</span><span class="sxs-lookup"><span data-stu-id="db9ac-116">Custom providers, installed or created</span></span>
* <span data-ttu-id="db9ac-117">Fichiers de répertoire</span><span class="sxs-lookup"><span data-stu-id="db9ac-117">Directory files</span></span>
* <span data-ttu-id="db9ac-118">Objets .NET en mémoire</span><span class="sxs-lookup"><span data-stu-id="db9ac-118">In-memory .NET objects</span></span>

<span data-ttu-id="db9ac-119">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="db9ac-119">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="default"></a>

## <a name="default-configuration"></a><span data-ttu-id="db9ac-120">Configuration par défaut</span><span class="sxs-lookup"><span data-stu-id="db9ac-120">Default configuration</span></span>

<span data-ttu-id="db9ac-121">ASP.NET Core les applications Web créées avec [dotnet New](/dotnet/core/tools/dotnet-new) ou Visual Studio génèrent le code suivant :</span><span class="sxs-lookup"><span data-stu-id="db9ac-121">ASP.NET Core web apps created with [dotnet new](/dotnet/core/tools/dotnet-new) or Visual Studio generate the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet&highlight=9)]

 <span data-ttu-id="db9ac-122"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> fournit la configuration par défaut de l’application dans l’ordre suivant :</span><span class="sxs-lookup"><span data-stu-id="db9ac-122"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> provides default configuration for the app in the following order:</span></span>

1. <span data-ttu-id="db9ac-123">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) : ajoute un existant `IConfiguration` en tant que source.</span><span class="sxs-lookup"><span data-stu-id="db9ac-123">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) :  Adds an existing `IConfiguration` as a source.</span></span> <span data-ttu-id="db9ac-124">Dans le cas de configuration par défaut, ajoute la configuration d' [hôte](#hvac) et la définit en tant que première source de la configuration de l' _application_ .</span><span class="sxs-lookup"><span data-stu-id="db9ac-124">In the default configuration case, adds the [host](#hvac) configuration and setting it as the first source for the _app_ configuration.</span></span>
1. <span data-ttu-id="db9ac-125">[appSettings. JSON](#appsettingsjson) à l’aide du [fournisseur de configuration JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="db9ac-125">[appsettings.json](#appsettingsjson) using the [JSON configuration provider](#file-configuration-provider).</span></span>
1. <span data-ttu-id="db9ac-126">*appSettings.* `Environment` *. JSON* à l’aide du [fournisseur de configuration JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="db9ac-126">*appsettings.*`Environment`*.json* using the [JSON configuration provider](#file-configuration-provider).</span></span> <span data-ttu-id="db9ac-127">Par exemple, *appSettings*. ***Production***. *JSON* et *appSettings*. ***Développement***. *JSON*.</span><span class="sxs-lookup"><span data-stu-id="db9ac-127">For example, *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json*.</span></span>
1. <span data-ttu-id="db9ac-128">[Secrets d’application](xref:security/app-secrets) lorsque l’application s’exécute dans l' `Development` environnement.</span><span class="sxs-lookup"><span data-stu-id="db9ac-128">[App secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
1. <span data-ttu-id="db9ac-129">Variables d’environnement à l’aide du [fournisseur de configuration des variables d’environnement](#evcp).</span><span class="sxs-lookup"><span data-stu-id="db9ac-129">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="db9ac-130">Arguments de ligne de commande à l’aide du [fournisseur de configuration de ligne de commande](#command-line).</span><span class="sxs-lookup"><span data-stu-id="db9ac-130">Command-line arguments using the [Command-line configuration provider](#command-line).</span></span>

<span data-ttu-id="db9ac-131">Les fournisseurs de configuration ajoutés ultérieurement remplacent les paramètres de clé précédents.</span><span class="sxs-lookup"><span data-stu-id="db9ac-131">Configuration providers that are added later override previous key settings.</span></span> <span data-ttu-id="db9ac-132">Par exemple, si `MyKey` est défini à la fois dans *appSettings. JSON* et dans l’environnement, la valeur d’environnement est utilisée.</span><span class="sxs-lookup"><span data-stu-id="db9ac-132">For example, if `MyKey` is set in both *appsettings.json* and the environment, the environment value is used.</span></span> <span data-ttu-id="db9ac-133">À l’aide des fournisseurs de configuration par défaut, le [fournisseur de configuration de ligne de commande](#command-line-configuration-provider) remplace tous les autres fournisseurs.</span><span class="sxs-lookup"><span data-stu-id="db9ac-133">Using the default configuration providers, the  [Command-line configuration provider](#command-line-configuration-provider) overrides all other providers.</span></span>

<span data-ttu-id="db9ac-134">Pour plus d’informations sur `CreateDefaultBuilder` , consultez [paramètres par défaut du générateur](xref:fundamentals/host/generic-host#default-builder-settings).</span><span class="sxs-lookup"><span data-stu-id="db9ac-134">For more information on `CreateDefaultBuilder`, see [Default builder settings](xref:fundamentals/host/generic-host#default-builder-settings).</span></span>

<span data-ttu-id="db9ac-135">Le code suivant affiche les fournisseurs de configuration activés dans l’ordre dans lequel ils ont été ajoutés :</span><span class="sxs-lookup"><span data-stu-id="db9ac-135">The following code displays the enabled configuration providers in the order they were added:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Index2.cshtml.cs?name=snippet)]

### <a name="appsettingsjson"></a><span data-ttu-id="db9ac-136">appsettings.json</span><span class="sxs-lookup"><span data-stu-id="db9ac-136">appsettings.json</span></span>

<span data-ttu-id="db9ac-137">Prenons le fichier *appSettings. JSON* suivant :</span><span class="sxs-lookup"><span data-stu-id="db9ac-137">Consider the following *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="db9ac-138">Le code suivant de l' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) affiche plusieurs des paramètres de configuration précédents :</span><span class="sxs-lookup"><span data-stu-id="db9ac-138">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="db9ac-139">La configuration par défaut <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> charge dans l’ordre suivant :</span><span class="sxs-lookup"><span data-stu-id="db9ac-139">The default <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration in the following order:</span></span>

1. <span data-ttu-id="db9ac-140">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="db9ac-140">*appsettings.json*</span></span>
1. <span data-ttu-id="db9ac-141">*appSettings.* `Environment` *. JSON* : par exemple, *appSettings*. ***Production***. *JSON* et *appSettings*. ***Développement***. fichiers *JSON* .</span><span class="sxs-lookup"><span data-stu-id="db9ac-141">*appsettings.*`Environment`*.json* : For example, the *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json* files.</span></span> <span data-ttu-id="db9ac-142">La version de l’environnement du fichier est chargée à partir de [IHostingEnvironment. EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="db9ac-142">The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="db9ac-143">Pour plus d'informations, consultez <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="db9ac-143">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="db9ac-144">*appSettings*. `Environment` . les valeurs *JSON* remplacent les clés dans *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="db9ac-144">*appsettings*.`Environment`.*json* values override keys in *appsettings.json*.</span></span> <span data-ttu-id="db9ac-145">Par exemple, par défaut :</span><span class="sxs-lookup"><span data-stu-id="db9ac-145">For example, by default:</span></span>

* <span data-ttu-id="db9ac-146">Dans le développement, *appSettings*. ***Développement***. la configuration *JSON* remplace les valeurs trouvées dans *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="db9ac-146">In development, *appsettings*.***Development***.*json* configuration overwrites values found in *appsettings.json*.</span></span>
* <span data-ttu-id="db9ac-147">En production, *appSettings*. ***Production***. la configuration *JSON* remplace les valeurs trouvées dans *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="db9ac-147">In production, *appsettings*.***Production***.*json* configuration overwrites values found in *appsettings.json*.</span></span> <span data-ttu-id="db9ac-148">Par exemple, lors du déploiement de l’application sur Azure.</span><span class="sxs-lookup"><span data-stu-id="db9ac-148">For example, when deploying the app to Azure.</span></span>

<a name="optpat"></a>

### <a name="bind-hierarchical-configuration-data-using-the-options-pattern"></a><span data-ttu-id="db9ac-149">Lier des données de configuration hiérarchiques à l’aide du modèle options</span><span class="sxs-lookup"><span data-stu-id="db9ac-149">Bind hierarchical configuration data using the options pattern</span></span>

[!INCLUDE[](~/includes/bind.md)]

<span data-ttu-id="db9ac-150">À l’aide de la configuration [par défaut](#default) , *appSettings. JSON* et *appSettings.* `Environment` les fichiers *. JSON* sont activés avec [reloadOnChange : true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span><span class="sxs-lookup"><span data-stu-id="db9ac-150">Using the [default](#default) configuration, the *appsettings.json* and *appsettings.*`Environment`*.json* files are enabled with [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span></span> <span data-ttu-id="db9ac-151">Modifications apportées aux *appSettings. JSON* et *appSettings.* `Environment` le fichier *. JSON* ***après*** le démarrage de l’application est lu par le [fournisseur de configuration JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="db9ac-151">Changes made to the *appsettings.json* and *appsettings.*`Environment`*.json* file ***after*** the app starts are read by the [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="db9ac-152">Pour plus d’informations sur l’ajout de fichiers de configuration JSON supplémentaires, consultez [fournisseur de configuration JSON](#jcp) dans ce document.</span><span class="sxs-lookup"><span data-stu-id="db9ac-152">See [JSON configuration provider](#jcp) in this document for information on adding additional JSON configuration files.</span></span>

<a name="security"></a>

## <a name="security-and-secret-manager"></a><span data-ttu-id="db9ac-153">Responsable de la sécurité et du secret</span><span class="sxs-lookup"><span data-stu-id="db9ac-153">Security and secret manager</span></span>

<span data-ttu-id="db9ac-154">Instructions relatives aux données de configuration :</span><span class="sxs-lookup"><span data-stu-id="db9ac-154">Configuration data guidelines:</span></span>

* <span data-ttu-id="db9ac-155">Ne stockez jamais des mots de passe ou d’autres données sensibles dans le code du fournisseur de configuration ou dans les fichiers de configuration en texte clair.</span><span class="sxs-lookup"><span data-stu-id="db9ac-155">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span> <span data-ttu-id="db9ac-156">Le [Gestionnaire de secret](xref:security/app-secrets) peut être utilisé pour stocker les secrets en développement.</span><span class="sxs-lookup"><span data-stu-id="db9ac-156">The [Secret manager](xref:security/app-secrets) can be used to store secrets in development.</span></span>
* <span data-ttu-id="db9ac-157">N’utilisez aucun secret de production dans les environnements de développement ou de test.</span><span class="sxs-lookup"><span data-stu-id="db9ac-157">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="db9ac-158">Spécifiez les secrets en dehors du projet afin qu’ils ne puissent pas être validés par inadvertance dans un référentiel de code source.</span><span class="sxs-lookup"><span data-stu-id="db9ac-158">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="db9ac-159">Par [défaut](#default), le [Gestionnaire de secret](xref:security/app-secrets) lit les paramètres de configuration après *appSettings. JSON* et *appSettings.* `Environment` *. JSON*.</span><span class="sxs-lookup"><span data-stu-id="db9ac-159">By [default](#default), [Secret manager](xref:security/app-secrets) reads configuration settings after *appsettings.json* and *appsettings.*`Environment`*.json*.</span></span>

<span data-ttu-id="db9ac-160">Pour plus d’informations sur le stockage des mots de passe ou d’autres données sensibles :</span><span class="sxs-lookup"><span data-stu-id="db9ac-160">For more information on storing passwords or other sensitive data:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="db9ac-161"><xref:security/app-secrets>: Fournit des conseils sur l’utilisation de variables d’environnement pour stocker des données sensibles.</span><span class="sxs-lookup"><span data-stu-id="db9ac-161"><xref:security/app-secrets>:  Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="db9ac-162">Le gestionnaire de secret utilise le [fournisseur de configuration de fichiers](#fcp) pour stocker les secrets de l’utilisateur dans un fichier JSON sur le système local.</span><span class="sxs-lookup"><span data-stu-id="db9ac-162">The Secret Manager uses the [File configuration provider](#fcp) to store user secrets in a JSON file on the local system.</span></span>

<span data-ttu-id="db9ac-163">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) stocke en toute sécurité des secrets d’application pour les applications ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="db9ac-163">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="db9ac-164">Pour plus d'informations, consultez <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="db9ac-164">For more information, see <xref:security/key-vault-configuration>.</span></span>

<a name="evcp"></a>

## <a name="environment-variables"></a><span data-ttu-id="db9ac-165">Variables d'environnement</span><span class="sxs-lookup"><span data-stu-id="db9ac-165">Environment variables</span></span>

<span data-ttu-id="db9ac-166">À l’aide de la configuration [par défaut](#default) , le <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> charge la configuration à partir des paires clé-valeur de variable d’environnement après la lecture de *appSettings. JSON*, *appSettings.* `Environment` *. JSON*et le [Gestionnaire de secret](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="db9ac-166">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs after reading *appsettings.json*, *appsettings.*`Environment`*.json*, and [Secret manager](xref:security/app-secrets).</span></span> <span data-ttu-id="db9ac-167">Par conséquent, les valeurs de clés lues à partir de l’environnement remplacent les valeurs lues dans *appSettings. JSON*, *appSettings.* `Environment` *. JSON*et le gestionnaire de secret.</span><span class="sxs-lookup"><span data-stu-id="db9ac-167">Therefore, key values read from the environment override values read from *appsettings.json*, *appsettings.*`Environment`*.json*, and Secret manager.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="db9ac-168">Les `set` commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="db9ac-168">The following `set` commands:</span></span>

* <span data-ttu-id="db9ac-169">Définissez les clés et les valeurs d’environnement de l' [exemple précédent](#appsettingsjson) sur Windows.</span><span class="sxs-lookup"><span data-stu-id="db9ac-169">Set the environment keys and values of the [preceding example](#appsettingsjson) on Windows.</span></span>
* <span data-ttu-id="db9ac-170">Testez les paramètres lors de l’utilisation de l' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span><span class="sxs-lookup"><span data-stu-id="db9ac-170">Test the settings when using the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span></span> <span data-ttu-id="db9ac-171">La `dotnet run` commande doit être exécutée dans le répertoire du projet.</span><span class="sxs-lookup"><span data-stu-id="db9ac-171">The `dotnet run` command must be run in the project directory.</span></span>

```dotnetcli
set MyKey="My key from Environment"
set Position__Title=Environment_Editor
set Position__Name=Environment_Rick
dotnet run
```

<span data-ttu-id="db9ac-172">Paramètres d’environnement précédents :</span><span class="sxs-lookup"><span data-stu-id="db9ac-172">The preceding environment settings:</span></span>

* <span data-ttu-id="db9ac-173">Sont uniquement définies dans les processus lancés à partir de la fenêtre de commande dans laquelle ils ont été définis.</span><span class="sxs-lookup"><span data-stu-id="db9ac-173">Are only set in processes launched from the command window they were set in.</span></span>
* <span data-ttu-id="db9ac-174">Ne seront pas lues par les navigateurs lancés avec Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="db9ac-174">Won't be read by browsers launched with Visual Studio.</span></span>

<span data-ttu-id="db9ac-175">Les commandes [setx](/windows-server/administration/windows-commands/setx) suivantes peuvent être utilisées pour définir les clés et les valeurs d’environnement sur Windows.</span><span class="sxs-lookup"><span data-stu-id="db9ac-175">The following [setx](/windows-server/administration/windows-commands/setx) commands can be used to set the environment keys and values on Windows.</span></span> <span data-ttu-id="db9ac-176">Contrairement à `set` , `setx` les paramètres sont conservés.</span><span class="sxs-lookup"><span data-stu-id="db9ac-176">Unlike `set`, `setx` settings are persisted.</span></span> <span data-ttu-id="db9ac-177">`/M`définit la variable dans l’environnement système.</span><span class="sxs-lookup"><span data-stu-id="db9ac-177">`/M` sets the variable in the system environment.</span></span> <span data-ttu-id="db9ac-178">Si le `/M` commutateur n’est pas utilisé, une variable d’environnement utilisateur est définie.</span><span class="sxs-lookup"><span data-stu-id="db9ac-178">If the `/M` switch isn't used, a user environment variable is set.</span></span>

```cmd
setx MyKey "My key from setx Environment" /M
setx Position__Title Setx_Environment_Editor /M
setx Position__Name Environment_Rick /M
```

<span data-ttu-id="db9ac-179">Pour vérifier que les commandes précédentes remplacent *appSettings. JSON* et *appSettings.* `Environment` *. JSON*:</span><span class="sxs-lookup"><span data-stu-id="db9ac-179">To test that the preceding commands override *appsettings.json* and *appsettings.*`Environment`*.json*:</span></span>

* <span data-ttu-id="db9ac-180">Avec Visual Studio : quittez et redémarrez Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="db9ac-180">With Visual Studio: Exit and restart Visual Studio.</span></span>
* <span data-ttu-id="db9ac-181">Avec l’interface CLI : démarrez une nouvelle fenêtre de commande et entrez `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="db9ac-181">With the CLI: Start a new command window and enter `dotnet run`.</span></span>

<span data-ttu-id="db9ac-182">Appelez <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> avec une chaîne pour spécifier un préfixe pour les variables d’environnement :</span><span class="sxs-lookup"><span data-stu-id="db9ac-182">Call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> with a string to specify a prefix for environment variables:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Program.cs?name=snippet4&highlight=12)]

<span data-ttu-id="db9ac-183">Dans le code précédent :</span><span class="sxs-lookup"><span data-stu-id="db9ac-183">In the preceding code:</span></span>

* <span data-ttu-id="db9ac-184">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")`est ajouté après les [fournisseurs de configuration par défaut](#default).</span><span class="sxs-lookup"><span data-stu-id="db9ac-184">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="db9ac-185">Pour obtenir un exemple de classement des fournisseurs de configuration, consultez [fournisseur de configuration JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="db9ac-185">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>
* <span data-ttu-id="db9ac-186">Les variables d’environnement définies avec le `MyCustomPrefix_` préfixe remplacent les [fournisseurs de configuration par défaut](#default).</span><span class="sxs-lookup"><span data-stu-id="db9ac-186">Environment variables set with the `MyCustomPrefix_` prefix override the [default configuration providers](#default).</span></span> <span data-ttu-id="db9ac-187">Cela comprend les variables d’environnement sans le préfixe.</span><span class="sxs-lookup"><span data-stu-id="db9ac-187">This includes environment variables without the prefix.</span></span>

<span data-ttu-id="db9ac-188">Le préfixe est supprimé lorsque les paires clé-valeur de configuration sont lues.</span><span class="sxs-lookup"><span data-stu-id="db9ac-188">The prefix is stripped off when the configuration key-value pairs are read.</span></span>

<span data-ttu-id="db9ac-189">Les commandes suivantes testent le préfixe personnalisé :</span><span class="sxs-lookup"><span data-stu-id="db9ac-189">The following commands test the custom prefix:</span></span>

```dotnetcli
set MyCustomPrefix_MyKey="My key with MyCustomPrefix_ Environment"
set MyCustomPrefix_Position__Title=Editor_with_customPrefix
set MyCustomPrefix_Position__Name=Environment_Rick_cp
dotnet run
```

<span data-ttu-id="db9ac-190">La [configuration par défaut](#default) charge les variables d’environnement et les arguments de ligne de commande préfixé avec `DOTNET_` et `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="db9ac-190">The [default configuration](#default) loads environment variables and command line arguments prefixed with `DOTNET_` and `ASPNETCORE_`.</span></span> <span data-ttu-id="db9ac-191">Les `DOTNET_` `ASPNETCORE_` préfixes et sont utilisés par ASP.net Core pour la configuration de l' [hôte et](xref:fundamentals/host/generic-host#host-configuration)de l’application, mais pas pour la configuration de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="db9ac-191">The `DOTNET_` and `ASPNETCORE_` prefixes are used by ASP.NET Core for [host and app configuration](xref:fundamentals/host/generic-host#host-configuration), but not for user configuration.</span></span> <span data-ttu-id="db9ac-192">Pour plus d’informations sur la configuration de l’hôte et de l’application, consultez [hôte générique .net](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="db9ac-192">For more information on host and app configuration, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

<span data-ttu-id="db9ac-193">Dans [Azure App service](https://azure.microsoft.com/services/app-service/), sélectionnez **nouveau paramètre d’application** dans la page **paramètres > configuration** .</span><span class="sxs-lookup"><span data-stu-id="db9ac-193">On [Azure App Service](https://azure.microsoft.com/services/app-service/), select **New application setting** on the **Settings > Configuration** page.</span></span> <span data-ttu-id="db9ac-194">Azure App Service paramètres de l’application sont les suivants :</span><span class="sxs-lookup"><span data-stu-id="db9ac-194">Azure App Service application settings are:</span></span>

* <span data-ttu-id="db9ac-195">Chiffré au repos et transmis sur un canal chiffré.</span><span class="sxs-lookup"><span data-stu-id="db9ac-195">Encrypted at rest and transmitted over an encrypted channel.</span></span>
* <span data-ttu-id="db9ac-196">Exposés en tant que variables d’environnement.</span><span class="sxs-lookup"><span data-stu-id="db9ac-196">Exposed as environment variables.</span></span>

<span data-ttu-id="db9ac-197">Pour plus d’informations, consultez [Azure Apps : remplacer la configuration de l’application à l’aide du portail Azure](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="db9ac-197">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="db9ac-198">Consultez [préfixes de chaîne de connexion](#constr) pour plus d’informations sur les chaînes de connexion de base de données Azure.</span><span class="sxs-lookup"><span data-stu-id="db9ac-198">See [Connection string prefixes](#constr) for information on Azure database connection strings.</span></span>

<a name="clcp"></a>

## <a name="command-line"></a><span data-ttu-id="db9ac-199">Ligne de commande</span><span class="sxs-lookup"><span data-stu-id="db9ac-199">Command-line</span></span>

<span data-ttu-id="db9ac-200">À l’aide de la configuration [par défaut](#default) , le <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> charge la configuration à partir de paires clé-valeur d’argument de ligne de commande après les sources de configuration suivantes :</span><span class="sxs-lookup"><span data-stu-id="db9ac-200">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs after the following configuration sources:</span></span>

* <span data-ttu-id="db9ac-201">*appSettings. JSON* et *appSettings*. `Environment` . fichiers *JSON* .</span><span class="sxs-lookup"><span data-stu-id="db9ac-201">*appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="db9ac-202">[Secrets d’application (gestionnaire de secret)](xref:security/app-secrets) dans l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="db9ac-202">[App secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="db9ac-203">Variables d'environnement.</span><span class="sxs-lookup"><span data-stu-id="db9ac-203">Environment variables.</span></span>

<span data-ttu-id="db9ac-204">Par [défaut](#default), les valeurs de configuration définies sur la ligne de commande remplacent les valeurs de configuration définies avec tous les autres fournisseurs de configuration.</span><span class="sxs-lookup"><span data-stu-id="db9ac-204">By [default](#default), configuration values set on the command-line override configuration values set with all the other configuration providers.</span></span>

### <a name="command-line-arguments"></a><span data-ttu-id="db9ac-205">Arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="db9ac-205">Command-line arguments</span></span>

<span data-ttu-id="db9ac-206">La commande suivante définit des clés et des valeurs à l’aide de `=` :</span><span class="sxs-lookup"><span data-stu-id="db9ac-206">The following command sets keys and values using `=`:</span></span>

```dotnetcli
dotnet run MyKey="My key from command line" Position:Title=Cmd Position:Name=Cmd_Rick
```

<span data-ttu-id="db9ac-207">La commande suivante définit des clés et des valeurs à l’aide de `/` :</span><span class="sxs-lookup"><span data-stu-id="db9ac-207">The following command sets keys and values using `/`:</span></span>

```dotnetcli
dotnet run /MyKey "Using /" /Position:Title=Cmd_ /Position:Name=Cmd_Rick
```

<span data-ttu-id="db9ac-208">La commande suivante définit des clés et des valeurs à l’aide de `--` :</span><span class="sxs-lookup"><span data-stu-id="db9ac-208">The following command sets keys and values using `--`:</span></span>

```dotnetcli
dotnet run --MyKey "Using --" --Position:Title=Cmd-- --Position:Name=Cmd--Rick
```

<span data-ttu-id="db9ac-209">Valeur de la clé :</span><span class="sxs-lookup"><span data-stu-id="db9ac-209">The key value:</span></span>

* <span data-ttu-id="db9ac-210">Doit suivre `=` ou la clé doit avoir un préfixe `--` ou `/` lorsque la valeur suit un espace.</span><span class="sxs-lookup"><span data-stu-id="db9ac-210">Must follow `=`, or the key must have a prefix of `--` or `/` when the value follows a space.</span></span>
* <span data-ttu-id="db9ac-211">N’est pas obligatoire si `=` est utilisé.</span><span class="sxs-lookup"><span data-stu-id="db9ac-211">Isn't required if `=` is used.</span></span> <span data-ttu-id="db9ac-212">Par exemple : `MySetting=`.</span><span class="sxs-lookup"><span data-stu-id="db9ac-212">For example, `MySetting=`.</span></span>

<span data-ttu-id="db9ac-213">Dans la même commande, ne mélangez pas les paires clé-valeur d’argument de ligne de commande qui utilisent `=` des paires clé-valeur utilisant un espace.</span><span class="sxs-lookup"><span data-stu-id="db9ac-213">Within the same command, don't mix command-line argument key-value pairs that use `=` with key-value pairs that use a space.</span></span>

### <a name="switch-mappings"></a><span data-ttu-id="db9ac-214">Correspondances de commutateur</span><span class="sxs-lookup"><span data-stu-id="db9ac-214">Switch mappings</span></span>

<span data-ttu-id="db9ac-215">Les mappages de commutateur autorisent la logique de remplacement de nom de **clé** .</span><span class="sxs-lookup"><span data-stu-id="db9ac-215">Switch mappings allow **key** name replacement logic.</span></span> <span data-ttu-id="db9ac-216">Fournissez un dictionnaire de remplacements de commutateur dans la <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> méthode.</span><span class="sxs-lookup"><span data-stu-id="db9ac-216">Provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="db9ac-217">Quand le dictionnaire de correspondances de commutateur est utilisé, il est vérifié afin de déterminer s’il contient une clé correspondant à celle fournie par un argument de ligne de commande.</span><span class="sxs-lookup"><span data-stu-id="db9ac-217">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="db9ac-218">Si la clé de ligne de commande est trouvée dans le dictionnaire, la valeur du dictionnaire est retournée pour définir la paire clé-valeur dans la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="db9ac-218">If the command-line key is found in the dictionary, the dictionary value is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="db9ac-219">Une correspondance de commutateur est nécessaire pour chaque clé de ligne de commande préfixée avec un tiret unique (`-`).</span><span class="sxs-lookup"><span data-stu-id="db9ac-219">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="db9ac-220">Règles des clés du dictionnaire de correspondances de commutateur :</span><span class="sxs-lookup"><span data-stu-id="db9ac-220">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="db9ac-221">Les commutateurs doivent commencer par `-` ou `--` .</span><span class="sxs-lookup"><span data-stu-id="db9ac-221">Switches must start with `-` or `--`.</span></span>
* <span data-ttu-id="db9ac-222">Le dictionnaire de correspondances de commutateur ne doit pas contenir de clés en double.</span><span class="sxs-lookup"><span data-stu-id="db9ac-222">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="db9ac-223">Pour utiliser un dictionnaire de mappages de commutateur, transmettez-le à l’appel à `AddCommandLine` :</span><span class="sxs-lookup"><span data-stu-id="db9ac-223">To use a switch mappings dictionary, pass it into the call to `AddCommandLine`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramSwitch.cs?name=snippet&highlight=10-18,23)]

<span data-ttu-id="db9ac-224">Le code suivant montre les valeurs de clé pour les clés remplacées :</span><span class="sxs-lookup"><span data-stu-id="db9ac-224">The following code shows the key values for the replaced keys:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test3.cshtml.cs?name=snippet)]

<span data-ttu-id="db9ac-225">Exécutez la commande suivante pour tester le remplacement de la clé :</span><span class="sxs-lookup"><span data-stu-id="db9ac-225">Run the following command to test the key replacement:</span></span>

```dotnetcli
dotnet run -k1=value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="db9ac-226">Remarque : actuellement, `=` ne peut pas être utilisé pour définir des valeurs de remplacement de clé avec un seul tiret `-` .</span><span class="sxs-lookup"><span data-stu-id="db9ac-226">Note: Currently, `=` cannot be used to set key-replacement values with a single dash `-`.</span></span> <span data-ttu-id="db9ac-227">Consultez [ce problème GitHub](https://github.com/dotnet/extensions/issues/3059).</span><span class="sxs-lookup"><span data-stu-id="db9ac-227">See [this GitHub issue](https://github.com/dotnet/extensions/issues/3059).</span></span>

<span data-ttu-id="db9ac-228">La commande suivante fonctionne pour tester le remplacement de la clé :</span><span class="sxs-lookup"><span data-stu-id="db9ac-228">The following command works to test key replacement:</span></span>

```dotnetcli
dotnet run -k1 value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="db9ac-229">Pour les applications qui utilisent des mappages de commutateurs, l’appel à `CreateDefaultBuilder` ne doit pas passer d’arguments.</span><span class="sxs-lookup"><span data-stu-id="db9ac-229">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="db9ac-230">L' `CreateDefaultBuilder` appel de la méthode `AddCommandLine` n’inclut pas de commutateurs mappés, et il n’existe aucun moyen de passer le dictionnaire de mappage de commutateur à `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="db9ac-230">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch-mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="db9ac-231">La solution ne consiste pas à passer les arguments à `CreateDefaultBuilder` , mais à autoriser la méthode de la `ConfigurationBuilder` méthode `AddCommandLine` à traiter à la fois les arguments et le dictionnaire de mappage de commutateur.</span><span class="sxs-lookup"><span data-stu-id="db9ac-231">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch-mapping dictionary.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="db9ac-232">Données de configuration hiérarchiques</span><span class="sxs-lookup"><span data-stu-id="db9ac-232">Hierarchical configuration data</span></span>

<span data-ttu-id="db9ac-233">L’API de configuration lit les données de configuration hiérarchiques en aplatit les données hiérarchiques à l’aide d’un délimiteur dans les clés de configuration.</span><span class="sxs-lookup"><span data-stu-id="db9ac-233">The Configuration API reads hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="db9ac-234">L' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contient le fichier *appSettings. JSON* suivant :</span><span class="sxs-lookup"><span data-stu-id="db9ac-234">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="db9ac-235">Le code suivant de l' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) affiche plusieurs des paramètres de configuration :</span><span class="sxs-lookup"><span data-stu-id="db9ac-235">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="db9ac-236">La meilleure façon de lire des données de configuration hiérarchiques consiste à utiliser le modèle d’options.</span><span class="sxs-lookup"><span data-stu-id="db9ac-236">The preferred way to read hierarchical configuration data is using the options pattern.</span></span> <span data-ttu-id="db9ac-237">Pour plus d’informations, consultez [lier des données de configuration hiérarchiques](#optpat) dans ce document.</span><span class="sxs-lookup"><span data-stu-id="db9ac-237">For more information, see [Bind hierarchical configuration data](#optpat) in this document.</span></span>

<span data-ttu-id="db9ac-238">Les méthodes <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> et <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> sont disponibles pour isoler les sections et les enfants d’une section dans les données de configuration.</span><span class="sxs-lookup"><span data-stu-id="db9ac-238"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="db9ac-239">Ces méthodes sont décrites plus loin dans [GetSection, GetChildren et Exists](#getsection).</span><span class="sxs-lookup"><span data-stu-id="db9ac-239">These methods are described later in [GetSection, GetChildren, and Exists](#getsection).</span></span>

<!--
[Azure Key Vault configuration provider](xref:security/key-vault-configuration) implement change detection.
-->

## <a name="configuration-keys-and-values"></a><span data-ttu-id="db9ac-240">Clés et valeurs de configuration</span><span class="sxs-lookup"><span data-stu-id="db9ac-240">Configuration keys and values</span></span>

<span data-ttu-id="db9ac-241">Clés de configuration :</span><span class="sxs-lookup"><span data-stu-id="db9ac-241">Configuration keys:</span></span>

* <span data-ttu-id="db9ac-242">Ne respectent pas la casse.</span><span class="sxs-lookup"><span data-stu-id="db9ac-242">Are case-insensitive.</span></span> <span data-ttu-id="db9ac-243">Par exemple, `ConnectionString` et `connectionstring` sont traités en tant que clés équivalentes.</span><span class="sxs-lookup"><span data-stu-id="db9ac-243">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="db9ac-244">Si une clé et une valeur sont définies dans plusieurs fournisseurs de configuration, la valeur du dernier fournisseur ajouté est utilisée.</span><span class="sxs-lookup"><span data-stu-id="db9ac-244">If a key and value is set in more than one configuration providers, the value from the last provider added is used.</span></span> <span data-ttu-id="db9ac-245">Pour plus d’informations, consultez [configuration par défaut](#default).</span><span class="sxs-lookup"><span data-stu-id="db9ac-245">For more information, see [Default configuration](#default).</span></span>
* <span data-ttu-id="db9ac-246">Clés hiérarchiques</span><span class="sxs-lookup"><span data-stu-id="db9ac-246">Hierarchical keys</span></span>
  * <span data-ttu-id="db9ac-247">Dans l’API Configuration, un séparateur sous forme de signe deux-points (`:`) fonctionne sur toutes les plateformes.</span><span class="sxs-lookup"><span data-stu-id="db9ac-247">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="db9ac-248">Dans les variables d’environnement, un séparateur sous forme de signe deux-points peut ne pas fonctionner sur toutes les plateformes.</span><span class="sxs-lookup"><span data-stu-id="db9ac-248">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="db9ac-249">Un trait de soulignement double, `__` , est pris en charge par toutes les plateformes et est automatiquement converti en deux-points `:` .</span><span class="sxs-lookup"><span data-stu-id="db9ac-249">A double underscore, `__`, is supported by all platforms and is automatically converted into a colon `:`.</span></span>
  * <span data-ttu-id="db9ac-250">Dans Azure Key Vault, les clés hiérarchiques utilisent `--` comme séparateur.</span><span class="sxs-lookup"><span data-stu-id="db9ac-250">In Azure Key Vault, hierarchical keys use `--` as a separator.</span></span> <span data-ttu-id="db9ac-251">Le [fournisseur de configuration Azure Key Vault](xref:security/key-vault-configuration) remplace automatiquement `--` par un `:` lorsque les secrets sont chargés dans la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="db9ac-251">The [Azure Key Vault configuration provider](xref:security/key-vault-configuration) automatically replaces `--` with a `:` when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="db9ac-252"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> prend en charge la liaison de tableaux à des objets à l’aide d’index de tableau dans les clés de configuration.</span><span class="sxs-lookup"><span data-stu-id="db9ac-252">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="db9ac-253">La liaison de tableau est décrite dans la section [Lier un tableau à une classe](#boa).</span><span class="sxs-lookup"><span data-stu-id="db9ac-253">Array binding is described in the [Bind an array to a class](#boa) section.</span></span>

<span data-ttu-id="db9ac-254">Valeurs de configuration :</span><span class="sxs-lookup"><span data-stu-id="db9ac-254">Configuration values:</span></span>

* <span data-ttu-id="db9ac-255">Sont des chaînes.</span><span class="sxs-lookup"><span data-stu-id="db9ac-255">Are strings.</span></span>
* <span data-ttu-id="db9ac-256">Les valeurs NULL ne peuvent pas être stockées dans la configuration ou liées à des objets.</span><span class="sxs-lookup"><span data-stu-id="db9ac-256">Null values can't be stored in configuration or bound to objects.</span></span>

<a name="cp"></a>

## <a name="configuration-providers"></a><span data-ttu-id="db9ac-257">Fournisseurs de configuration</span><span class="sxs-lookup"><span data-stu-id="db9ac-257">Configuration providers</span></span>

<span data-ttu-id="db9ac-258">Le tableau suivant présente les fournisseurs de configuration disponibles pour les applications ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="db9ac-258">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="db9ac-259">Fournisseur</span><span class="sxs-lookup"><span data-stu-id="db9ac-259">Provider</span></span> | <span data-ttu-id="db9ac-260">Fournit la configuration à partir de</span><span class="sxs-lookup"><span data-stu-id="db9ac-260">Provides configuration from</span></span> |
| ---
<span data-ttu-id="db9ac-261">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-261">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-262">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-262">'Blazor'</span></span>
- <span data-ttu-id="db9ac-263">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-263">'Identity'</span></span>
- <span data-ttu-id="db9ac-264">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-264">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-265">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-265">'Razor'</span></span>
- <span data-ttu-id="db9ac-266">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-266">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-267">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-267">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-268">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-268">'Blazor'</span></span>
- <span data-ttu-id="db9ac-269">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-269">'Identity'</span></span>
- <span data-ttu-id="db9ac-270">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-270">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-271">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-271">'Razor'</span></span>
- <span data-ttu-id="db9ac-272">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-272">'SignalR' uid:</span></span> 

<span data-ttu-id="db9ac-273">---- | titre de--- : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-273">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-274">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-274">'Blazor'</span></span>
- <span data-ttu-id="db9ac-275">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-275">'Identity'</span></span>
- <span data-ttu-id="db9ac-276">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-276">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-277">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-277">'Razor'</span></span>
- <span data-ttu-id="db9ac-278">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-278">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-279">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-279">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-280">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-280">'Blazor'</span></span>
- <span data-ttu-id="db9ac-281">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-281">'Identity'</span></span>
- <span data-ttu-id="db9ac-282">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-282">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-283">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-283">'Razor'</span></span>
- <span data-ttu-id="db9ac-284">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-284">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-285">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-285">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-286">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-286">'Blazor'</span></span>
- <span data-ttu-id="db9ac-287">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-287">'Identity'</span></span>
- <span data-ttu-id="db9ac-288">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-288">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-289">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-289">'Razor'</span></span>
- <span data-ttu-id="db9ac-290">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-290">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-291">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-291">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-292">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-292">'Blazor'</span></span>
- <span data-ttu-id="db9ac-293">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-293">'Identity'</span></span>
- <span data-ttu-id="db9ac-294">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-294">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-295">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-295">'Razor'</span></span>
- <span data-ttu-id="db9ac-296">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-296">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-297">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-297">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-298">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-298">'Blazor'</span></span>
- <span data-ttu-id="db9ac-299">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-299">'Identity'</span></span>
- <span data-ttu-id="db9ac-300">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-300">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-301">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-301">'Razor'</span></span>
- <span data-ttu-id="db9ac-302">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-302">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-303">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-303">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-304">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-304">'Blazor'</span></span>
- <span data-ttu-id="db9ac-305">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-305">'Identity'</span></span>
- <span data-ttu-id="db9ac-306">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-306">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-307">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-307">'Razor'</span></span>
- <span data-ttu-id="db9ac-308">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-308">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-309">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-309">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-310">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-310">'Blazor'</span></span>
- <span data-ttu-id="db9ac-311">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-311">'Identity'</span></span>
- <span data-ttu-id="db9ac-312">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-312">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-313">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-313">'Razor'</span></span>
- <span data-ttu-id="db9ac-314">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-314">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-315">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-315">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-316">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-316">'Blazor'</span></span>
- <span data-ttu-id="db9ac-317">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-317">'Identity'</span></span>
- <span data-ttu-id="db9ac-318">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-318">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-319">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-319">'Razor'</span></span>
- <span data-ttu-id="db9ac-320">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-320">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-321">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-321">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-322">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-322">'Blazor'</span></span>
- <span data-ttu-id="db9ac-323">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-323">'Identity'</span></span>
- <span data-ttu-id="db9ac-324">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-324">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-325">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-325">'Razor'</span></span>
- <span data-ttu-id="db9ac-326">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-326">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-327">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-327">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-328">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-328">'Blazor'</span></span>
- <span data-ttu-id="db9ac-329">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-329">'Identity'</span></span>
- <span data-ttu-id="db9ac-330">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-330">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-331">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-331">'Razor'</span></span>
- <span data-ttu-id="db9ac-332">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-332">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-333">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-334">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-334">'Blazor'</span></span>
- <span data-ttu-id="db9ac-335">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-335">'Identity'</span></span>
- <span data-ttu-id="db9ac-336">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-336">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-337">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-337">'Razor'</span></span>
- <span data-ttu-id="db9ac-338">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-338">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-339">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-340">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-340">'Blazor'</span></span>
- <span data-ttu-id="db9ac-341">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-341">'Identity'</span></span>
- <span data-ttu-id="db9ac-342">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-342">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-343">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-343">'Razor'</span></span>
- <span data-ttu-id="db9ac-344">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-344">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-345">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-346">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-346">'Blazor'</span></span>
- <span data-ttu-id="db9ac-347">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-347">'Identity'</span></span>
- <span data-ttu-id="db9ac-348">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-348">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-349">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-349">'Razor'</span></span>
- <span data-ttu-id="db9ac-350">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-350">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-351">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-352">'Blazor'</span></span>
- <span data-ttu-id="db9ac-353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-353">'Identity'</span></span>
- <span data-ttu-id="db9ac-354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-354">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-355">'Razor'</span></span>
- <span data-ttu-id="db9ac-356">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-356">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-357">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-358">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-358">'Blazor'</span></span>
- <span data-ttu-id="db9ac-359">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-359">'Identity'</span></span>
- <span data-ttu-id="db9ac-360">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-360">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-361">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-361">'Razor'</span></span>
- <span data-ttu-id="db9ac-362">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-362">'SignalR' uid:</span></span> 

<span data-ttu-id="db9ac-363">------------------ | | [Fournisseur de configuration Azure Key Vault](xref:security/key-vault-configuration) | Azure Key Vault | | [Fournisseur de configuration Azure App](/azure/azure-app-configuration/quickstart-aspnet-core-app) | Configuration de Azure App | | [Fournisseur de configuration de ligne de commande](#clcp) | Paramètres de ligne de commande | | [Fournisseur de configuration personnalisée](#custom-configuration-provider) | Source personnalisée | | [Fournisseur de configuration des variables d’environnement](#evcp) | Variables d’environnement | | [Fournisseur de configuration de fichier](#file-configuration-provider) | Fichiers INI, JSON et XML | | [Fournisseur de configuration de clé par fichier](#key-per-file-configuration-provider) | Fichiers de répertoire | | [Fournisseur de configuration](#memory-configuration-provider) de la mémoire | Collections en mémoire | | [Gestionnaire de secret](xref:security/app-secrets) | Fichier dans le répertoire du profil utilisateur |</span><span class="sxs-lookup"><span data-stu-id="db9ac-363">------------------ | | [Azure Key Vault configuration provider](xref:security/key-vault-configuration) | Azure Key Vault | | [Azure App configuration provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) | Azure App Configuration | | [Command-line configuration provider](#clcp) | Command-line parameters | | [Custom configuration provider](#custom-configuration-provider) | Custom source | | [Environment Variables configuration provider](#evcp) | Environment variables | | [File configuration provider](#file-configuration-provider) | INI, JSON, and XML files | | [Key-per-file configuration provider](#key-per-file-configuration-provider) | Directory files | | [Memory configuration provider](#memory-configuration-provider) | In-memory collections | | [Secret Manager](xref:security/app-secrets)  | File in the user profile directory |</span></span>

<span data-ttu-id="db9ac-364">Les sources de configuration sont lues dans l’ordre dans lequel leurs fournisseurs de configuration sont spécifiés.</span><span class="sxs-lookup"><span data-stu-id="db9ac-364">Configuration sources are read in the order that their configuration providers are specified.</span></span> <span data-ttu-id="db9ac-365">Commandez des fournisseurs de configuration dans le code pour répondre aux priorités des sources de configuration sous-jacentes requises par l’application.</span><span class="sxs-lookup"><span data-stu-id="db9ac-365">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="db9ac-366">Une séquence type des fournisseurs de configuration est la suivante :</span><span class="sxs-lookup"><span data-stu-id="db9ac-366">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="db9ac-367">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="db9ac-367">*appsettings.json*</span></span>
1. <span data-ttu-id="db9ac-368">*appSettings*. `Environment` . *JSON*</span><span class="sxs-lookup"><span data-stu-id="db9ac-368">*appsettings*.`Environment`.*json*</span></span>
1. [<span data-ttu-id="db9ac-369">Gestionnaire de secret</span><span class="sxs-lookup"><span data-stu-id="db9ac-369">Secret Manager</span></span>](xref:security/app-secrets)
1. <span data-ttu-id="db9ac-370">Variables d’environnement à l’aide du [fournisseur de configuration des variables d’environnement](#evcp).</span><span class="sxs-lookup"><span data-stu-id="db9ac-370">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="db9ac-371">Arguments de ligne de commande à l’aide du [fournisseur de configuration de ligne de commande](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="db9ac-371">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>

<span data-ttu-id="db9ac-372">Une pratique courante consiste à ajouter le dernier fournisseur de configuration de ligne de commande dans une série de fournisseurs pour permettre aux arguments de ligne de commande de remplacer la configuration définie par les autres fournisseurs.</span><span class="sxs-lookup"><span data-stu-id="db9ac-372">A common practice is to add the Command-line configuration provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="db9ac-373">La séquence de fournisseurs précédente est utilisée dans la [configuration par défaut](#default).</span><span class="sxs-lookup"><span data-stu-id="db9ac-373">The preceding sequence of providers is used in the [default configuration](#default).</span></span>

<a name="constr"></a>

### <a name="connection-string-prefixes"></a><span data-ttu-id="db9ac-374">Préfixes des chaînes de connexion</span><span class="sxs-lookup"><span data-stu-id="db9ac-374">Connection string prefixes</span></span>

<span data-ttu-id="db9ac-375">L’API de configuration a des règles de traitement spéciales pour quatre variables d’environnement de chaîne de connexion.</span><span class="sxs-lookup"><span data-stu-id="db9ac-375">The Configuration API has special processing rules for four connection string environment variables.</span></span> <span data-ttu-id="db9ac-376">Ces chaînes de connexion sont impliquées dans la configuration des chaînes de connexion Azure pour l’environnement de l’application.</span><span class="sxs-lookup"><span data-stu-id="db9ac-376">These connection strings are involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="db9ac-377">Les variables d’environnement avec les préfixes indiqués dans le tableau sont chargées dans l’application avec la [configuration par défaut](#default) ou lorsqu’aucun préfixe n’est fourni à `AddEnvironmentVariables` .</span><span class="sxs-lookup"><span data-stu-id="db9ac-377">Environment variables with the prefixes shown in the table are loaded into the app with the [default configuration](#default) or when no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="db9ac-378">Préfixe de la chaîne de connexion</span><span class="sxs-lookup"><span data-stu-id="db9ac-378">Connection string prefix</span></span> | <span data-ttu-id="db9ac-379">Fournisseur</span><span class="sxs-lookup"><span data-stu-id="db9ac-379">Provider</span></span> |
| ---
<span data-ttu-id="db9ac-380">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-380">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-381">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-381">'Blazor'</span></span>
- <span data-ttu-id="db9ac-382">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-382">'Identity'</span></span>
- <span data-ttu-id="db9ac-383">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-383">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-384">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-384">'Razor'</span></span>
- <span data-ttu-id="db9ac-385">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-385">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-386">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-386">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-387">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-387">'Blazor'</span></span>
- <span data-ttu-id="db9ac-388">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-388">'Identity'</span></span>
- <span data-ttu-id="db9ac-389">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-389">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-390">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-390">'Razor'</span></span>
- <span data-ttu-id="db9ac-391">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-391">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-392">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-392">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-393">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-393">'Blazor'</span></span>
- <span data-ttu-id="db9ac-394">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-394">'Identity'</span></span>
- <span data-ttu-id="db9ac-395">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-395">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-396">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-396">'Razor'</span></span>
- <span data-ttu-id="db9ac-397">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-397">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-398">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-398">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-399">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-399">'Blazor'</span></span>
- <span data-ttu-id="db9ac-400">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-400">'Identity'</span></span>
- <span data-ttu-id="db9ac-401">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-401">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-402">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-402">'Razor'</span></span>
- <span data-ttu-id="db9ac-403">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-403">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-404">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-404">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-405">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-405">'Blazor'</span></span>
- <span data-ttu-id="db9ac-406">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-406">'Identity'</span></span>
- <span data-ttu-id="db9ac-407">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-407">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-408">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-408">'Razor'</span></span>
- <span data-ttu-id="db9ac-409">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-409">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-410">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-410">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-411">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-411">'Blazor'</span></span>
- <span data-ttu-id="db9ac-412">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-412">'Identity'</span></span>
- <span data-ttu-id="db9ac-413">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-413">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-414">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-414">'Razor'</span></span>
- <span data-ttu-id="db9ac-415">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-415">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-416">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-416">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-417">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-417">'Blazor'</span></span>
- <span data-ttu-id="db9ac-418">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-418">'Identity'</span></span>
- <span data-ttu-id="db9ac-419">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-419">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-420">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-420">'Razor'</span></span>
- <span data-ttu-id="db9ac-421">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-421">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-422">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-422">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-423">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-423">'Blazor'</span></span>
- <span data-ttu-id="db9ac-424">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-424">'Identity'</span></span>
- <span data-ttu-id="db9ac-425">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-425">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-426">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-426">'Razor'</span></span>
- <span data-ttu-id="db9ac-427">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-427">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-428">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-428">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-429">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-429">'Blazor'</span></span>
- <span data-ttu-id="db9ac-430">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-430">'Identity'</span></span>
- <span data-ttu-id="db9ac-431">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-431">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-432">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-432">'Razor'</span></span>
- <span data-ttu-id="db9ac-433">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-433">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-434">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-434">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-435">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-435">'Blazor'</span></span>
- <span data-ttu-id="db9ac-436">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-436">'Identity'</span></span>
- <span data-ttu-id="db9ac-437">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-437">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-438">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-438">'Razor'</span></span>
- <span data-ttu-id="db9ac-439">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-439">'SignalR' uid:</span></span> 

<span data-ttu-id="db9ac-440">------------ | titre de--- : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-440">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-441">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-441">'Blazor'</span></span>
- <span data-ttu-id="db9ac-442">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-442">'Identity'</span></span>
- <span data-ttu-id="db9ac-443">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-443">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-444">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-444">'Razor'</span></span>
- <span data-ttu-id="db9ac-445">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-445">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-446">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-446">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-447">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-447">'Blazor'</span></span>
- <span data-ttu-id="db9ac-448">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-448">'Identity'</span></span>
- <span data-ttu-id="db9ac-449">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-449">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-450">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-450">'Razor'</span></span>
- <span data-ttu-id="db9ac-451">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-451">'SignalR' uid:</span></span> 

<span data-ttu-id="db9ac-452">---- | | `CUSTOMCONNSTR_` | Fournisseur personnalisé | | `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
 MySQL | `SQLAZURECONNSTR_` | [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) |
 | `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/)|</span><span class="sxs-lookup"><span data-stu-id="db9ac-452">---- | | `CUSTOMCONNSTR_` | Custom provider | | `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/) |</span></span>

<span data-ttu-id="db9ac-453">Quand une variable d’environnement est découverte et chargée dans la configuration avec l’un des quatre préfixes indiqués dans le tableau :</span><span class="sxs-lookup"><span data-stu-id="db9ac-453">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="db9ac-454">La clé de configuration est créée en supprimant le préfixe de la variable d’environnement et en ajoutant une section de clé de configuration (`ConnectionStrings`).</span><span class="sxs-lookup"><span data-stu-id="db9ac-454">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="db9ac-455">Une nouvelle paire clé-valeur de configuration est créée qui représente le fournisseur de connexion de base de données (à l’exception de `CUSTOMCONNSTR_`, qui ne possède aucun fournisseur indiqué).</span><span class="sxs-lookup"><span data-stu-id="db9ac-455">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="db9ac-456">Clé de variable d’environnement</span><span class="sxs-lookup"><span data-stu-id="db9ac-456">Environment variable key</span></span> | <span data-ttu-id="db9ac-457">Clé de configuration convertie</span><span class="sxs-lookup"><span data-stu-id="db9ac-457">Converted configuration key</span></span> | <span data-ttu-id="db9ac-458">Entrée de configuration de fournisseur</span><span class="sxs-lookup"><span data-stu-id="db9ac-458">Provider configuration entry</span></span>                                                    |
| ---
<span data-ttu-id="db9ac-459">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-459">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-460">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-460">'Blazor'</span></span>
- <span data-ttu-id="db9ac-461">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-461">'Identity'</span></span>
- <span data-ttu-id="db9ac-462">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-462">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-463">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-463">'Razor'</span></span>
- <span data-ttu-id="db9ac-464">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-464">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-465">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-466">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-466">'Blazor'</span></span>
- <span data-ttu-id="db9ac-467">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-467">'Identity'</span></span>
- <span data-ttu-id="db9ac-468">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-468">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-469">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-469">'Razor'</span></span>
- <span data-ttu-id="db9ac-470">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-470">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-471">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-472">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-472">'Blazor'</span></span>
- <span data-ttu-id="db9ac-473">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-473">'Identity'</span></span>
- <span data-ttu-id="db9ac-474">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-474">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-475">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-475">'Razor'</span></span>
- <span data-ttu-id="db9ac-476">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-476">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-477">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-477">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-478">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-478">'Blazor'</span></span>
- <span data-ttu-id="db9ac-479">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-479">'Identity'</span></span>
- <span data-ttu-id="db9ac-480">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-480">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-481">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-481">'Razor'</span></span>
- <span data-ttu-id="db9ac-482">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-482">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-483">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-484">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-484">'Blazor'</span></span>
- <span data-ttu-id="db9ac-485">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-485">'Identity'</span></span>
- <span data-ttu-id="db9ac-486">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-486">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-487">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-487">'Razor'</span></span>
- <span data-ttu-id="db9ac-488">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-488">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-489">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-490">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-490">'Blazor'</span></span>
- <span data-ttu-id="db9ac-491">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-491">'Identity'</span></span>
- <span data-ttu-id="db9ac-492">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-492">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-493">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-493">'Razor'</span></span>
- <span data-ttu-id="db9ac-494">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-494">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-495">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-495">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-496">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-496">'Blazor'</span></span>
- <span data-ttu-id="db9ac-497">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-497">'Identity'</span></span>
- <span data-ttu-id="db9ac-498">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-498">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-499">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-499">'Razor'</span></span>
- <span data-ttu-id="db9ac-500">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-500">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-501">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-502">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-502">'Blazor'</span></span>
- <span data-ttu-id="db9ac-503">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-503">'Identity'</span></span>
- <span data-ttu-id="db9ac-504">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-504">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-505">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-505">'Razor'</span></span>
- <span data-ttu-id="db9ac-506">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-506">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-507">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-507">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-508">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-508">'Blazor'</span></span>
- <span data-ttu-id="db9ac-509">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-509">'Identity'</span></span>
- <span data-ttu-id="db9ac-510">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-510">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-511">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-511">'Razor'</span></span>
- <span data-ttu-id="db9ac-512">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-512">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-513">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-513">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-514">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-514">'Blazor'</span></span>
- <span data-ttu-id="db9ac-515">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-515">'Identity'</span></span>
- <span data-ttu-id="db9ac-516">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-516">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-517">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-517">'Razor'</span></span>
- <span data-ttu-id="db9ac-518">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-518">'SignalR' uid:</span></span> 

<span data-ttu-id="db9ac-519">------------ | titre de--- : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-519">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-520">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-520">'Blazor'</span></span>
- <span data-ttu-id="db9ac-521">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-521">'Identity'</span></span>
- <span data-ttu-id="db9ac-522">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-522">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-523">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-523">'Razor'</span></span>
- <span data-ttu-id="db9ac-524">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-524">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-525">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-525">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-526">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-526">'Blazor'</span></span>
- <span data-ttu-id="db9ac-527">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-527">'Identity'</span></span>
- <span data-ttu-id="db9ac-528">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-528">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-529">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-529">'Razor'</span></span>
- <span data-ttu-id="db9ac-530">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-530">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-531">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-531">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-532">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-532">'Blazor'</span></span>
- <span data-ttu-id="db9ac-533">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-533">'Identity'</span></span>
- <span data-ttu-id="db9ac-534">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-534">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-535">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-535">'Razor'</span></span>
- <span data-ttu-id="db9ac-536">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-536">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-537">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-537">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-538">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-538">'Blazor'</span></span>
- <span data-ttu-id="db9ac-539">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-539">'Identity'</span></span>
- <span data-ttu-id="db9ac-540">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-540">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-541">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-541">'Razor'</span></span>
- <span data-ttu-id="db9ac-542">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-542">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-543">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-543">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-544">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-544">'Blazor'</span></span>
- <span data-ttu-id="db9ac-545">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-545">'Identity'</span></span>
- <span data-ttu-id="db9ac-546">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-546">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-547">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-547">'Razor'</span></span>
- <span data-ttu-id="db9ac-548">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-548">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-549">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-549">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-550">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-550">'Blazor'</span></span>
- <span data-ttu-id="db9ac-551">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-551">'Identity'</span></span>
- <span data-ttu-id="db9ac-552">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-552">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-553">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-553">'Razor'</span></span>
- <span data-ttu-id="db9ac-554">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-554">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-555">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-555">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-556">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-556">'Blazor'</span></span>
- <span data-ttu-id="db9ac-557">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-557">'Identity'</span></span>
- <span data-ttu-id="db9ac-558">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-558">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-559">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-559">'Razor'</span></span>
- <span data-ttu-id="db9ac-560">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-560">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-561">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-561">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-562">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-562">'Blazor'</span></span>
- <span data-ttu-id="db9ac-563">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-563">'Identity'</span></span>
- <span data-ttu-id="db9ac-564">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-564">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-565">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-565">'Razor'</span></span>
- <span data-ttu-id="db9ac-566">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-566">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-567">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-567">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-568">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-568">'Blazor'</span></span>
- <span data-ttu-id="db9ac-569">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-569">'Identity'</span></span>
- <span data-ttu-id="db9ac-570">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-570">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-571">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-571">'Razor'</span></span>
- <span data-ttu-id="db9ac-572">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-572">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-573">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-573">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-574">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-574">'Blazor'</span></span>
- <span data-ttu-id="db9ac-575">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-575">'Identity'</span></span>
- <span data-ttu-id="db9ac-576">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-576">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-577">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-577">'Razor'</span></span>
- <span data-ttu-id="db9ac-578">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-578">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-579">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-579">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-580">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-580">'Blazor'</span></span>
- <span data-ttu-id="db9ac-581">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-581">'Identity'</span></span>
- <span data-ttu-id="db9ac-582">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-582">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-583">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-583">'Razor'</span></span>
- <span data-ttu-id="db9ac-584">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-584">'SignalR' uid:</span></span> 

<span data-ttu-id="db9ac-585">-------------- | titre de--- : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-585">-------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-586">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-586">'Blazor'</span></span>
- <span data-ttu-id="db9ac-587">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-587">'Identity'</span></span>
- <span data-ttu-id="db9ac-588">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-588">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-589">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-589">'Razor'</span></span>
- <span data-ttu-id="db9ac-590">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-590">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-591">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-591">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-592">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-592">'Blazor'</span></span>
- <span data-ttu-id="db9ac-593">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-593">'Identity'</span></span>
- <span data-ttu-id="db9ac-594">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-594">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-595">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-595">'Razor'</span></span>
- <span data-ttu-id="db9ac-596">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-596">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-597">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-597">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-598">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-598">'Blazor'</span></span>
- <span data-ttu-id="db9ac-599">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-599">'Identity'</span></span>
- <span data-ttu-id="db9ac-600">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-600">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-601">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-601">'Razor'</span></span>
- <span data-ttu-id="db9ac-602">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-602">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-603">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-604">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-604">'Blazor'</span></span>
- <span data-ttu-id="db9ac-605">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-605">'Identity'</span></span>
- <span data-ttu-id="db9ac-606">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-606">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-607">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-607">'Razor'</span></span>
- <span data-ttu-id="db9ac-608">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-608">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-609">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-609">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-610">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-610">'Blazor'</span></span>
- <span data-ttu-id="db9ac-611">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-611">'Identity'</span></span>
- <span data-ttu-id="db9ac-612">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-612">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-613">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-613">'Razor'</span></span>
- <span data-ttu-id="db9ac-614">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-614">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-615">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-615">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-616">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-616">'Blazor'</span></span>
- <span data-ttu-id="db9ac-617">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-617">'Identity'</span></span>
- <span data-ttu-id="db9ac-618">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-618">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-619">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-619">'Razor'</span></span>
- <span data-ttu-id="db9ac-620">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-620">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-621">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-622">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-622">'Blazor'</span></span>
- <span data-ttu-id="db9ac-623">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-623">'Identity'</span></span>
- <span data-ttu-id="db9ac-624">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-624">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-625">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-625">'Razor'</span></span>
- <span data-ttu-id="db9ac-626">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-626">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-627">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-627">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-628">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-628">'Blazor'</span></span>
- <span data-ttu-id="db9ac-629">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-629">'Identity'</span></span>
- <span data-ttu-id="db9ac-630">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-630">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-631">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-631">'Razor'</span></span>
- <span data-ttu-id="db9ac-632">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-632">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-633">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-633">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-634">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-634">'Blazor'</span></span>
- <span data-ttu-id="db9ac-635">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-635">'Identity'</span></span>
- <span data-ttu-id="db9ac-636">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-636">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-637">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-637">'Razor'</span></span>
- <span data-ttu-id="db9ac-638">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-638">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-639">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-639">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-640">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-640">'Blazor'</span></span>
- <span data-ttu-id="db9ac-641">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-641">'Identity'</span></span>
- <span data-ttu-id="db9ac-642">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-642">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-643">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-643">'Razor'</span></span>
- <span data-ttu-id="db9ac-644">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-644">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-645">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-645">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-646">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-646">'Blazor'</span></span>
- <span data-ttu-id="db9ac-647">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-647">'Identity'</span></span>
- <span data-ttu-id="db9ac-648">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-648">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-649">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-649">'Razor'</span></span>
- <span data-ttu-id="db9ac-650">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-650">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-651">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-651">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-652">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-652">'Blazor'</span></span>
- <span data-ttu-id="db9ac-653">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-653">'Identity'</span></span>
- <span data-ttu-id="db9ac-654">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-654">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-655">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-655">'Razor'</span></span>
- <span data-ttu-id="db9ac-656">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-656">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-657">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-657">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-658">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-658">'Blazor'</span></span>
- <span data-ttu-id="db9ac-659">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-659">'Identity'</span></span>
- <span data-ttu-id="db9ac-660">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-660">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-661">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-661">'Razor'</span></span>
- <span data-ttu-id="db9ac-662">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-662">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-663">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-663">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-664">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-664">'Blazor'</span></span>
- <span data-ttu-id="db9ac-665">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-665">'Identity'</span></span>
- <span data-ttu-id="db9ac-666">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-666">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-667">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-667">'Razor'</span></span>
- <span data-ttu-id="db9ac-668">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-668">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-669">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-669">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-670">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-670">'Blazor'</span></span>
- <span data-ttu-id="db9ac-671">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-671">'Identity'</span></span>
- <span data-ttu-id="db9ac-672">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-672">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-673">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-673">'Razor'</span></span>
- <span data-ttu-id="db9ac-674">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-674">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-675">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-675">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-676">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-676">'Blazor'</span></span>
- <span data-ttu-id="db9ac-677">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-677">'Identity'</span></span>
- <span data-ttu-id="db9ac-678">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-678">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-679">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-679">'Razor'</span></span>
- <span data-ttu-id="db9ac-680">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-680">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-681">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-681">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-682">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-682">'Blazor'</span></span>
- <span data-ttu-id="db9ac-683">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-683">'Identity'</span></span>
- <span data-ttu-id="db9ac-684">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-684">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-685">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-685">'Razor'</span></span>
- <span data-ttu-id="db9ac-686">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-686">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-687">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-687">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-688">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-688">'Blazor'</span></span>
- <span data-ttu-id="db9ac-689">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-689">'Identity'</span></span>
- <span data-ttu-id="db9ac-690">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-690">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-691">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-691">'Razor'</span></span>
- <span data-ttu-id="db9ac-692">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-692">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-693">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-693">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-694">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-694">'Blazor'</span></span>
- <span data-ttu-id="db9ac-695">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-695">'Identity'</span></span>
- <span data-ttu-id="db9ac-696">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-696">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-697">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-697">'Razor'</span></span>
- <span data-ttu-id="db9ac-698">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-698">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-699">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-699">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-700">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-700">'Blazor'</span></span>
- <span data-ttu-id="db9ac-701">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-701">'Identity'</span></span>
- <span data-ttu-id="db9ac-702">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-702">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-703">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-703">'Razor'</span></span>
- <span data-ttu-id="db9ac-704">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-704">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-705">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-705">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-706">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-706">'Blazor'</span></span>
- <span data-ttu-id="db9ac-707">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-707">'Identity'</span></span>
- <span data-ttu-id="db9ac-708">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-708">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-709">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-709">'Razor'</span></span>
- <span data-ttu-id="db9ac-710">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-710">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-711">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-711">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-712">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-712">'Blazor'</span></span>
- <span data-ttu-id="db9ac-713">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-713">'Identity'</span></span>
- <span data-ttu-id="db9ac-714">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-714">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-715">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-715">'Razor'</span></span>
- <span data-ttu-id="db9ac-716">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-716">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-717">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-717">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-718">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-718">'Blazor'</span></span>
- <span data-ttu-id="db9ac-719">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-719">'Identity'</span></span>
- <span data-ttu-id="db9ac-720">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-720">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-721">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-721">'Razor'</span></span>
- <span data-ttu-id="db9ac-722">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-722">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-723">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-723">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-724">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-724">'Blazor'</span></span>
- <span data-ttu-id="db9ac-725">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-725">'Identity'</span></span>
- <span data-ttu-id="db9ac-726">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-726">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-727">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-727">'Razor'</span></span>
- <span data-ttu-id="db9ac-728">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-728">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-729">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-729">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-730">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-730">'Blazor'</span></span>
- <span data-ttu-id="db9ac-731">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-731">'Identity'</span></span>
- <span data-ttu-id="db9ac-732">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-732">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-733">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-733">'Razor'</span></span>
- <span data-ttu-id="db9ac-734">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-734">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-735">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-735">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-736">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-736">'Blazor'</span></span>
- <span data-ttu-id="db9ac-737">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-737">'Identity'</span></span>
- <span data-ttu-id="db9ac-738">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-738">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-739">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-739">'Razor'</span></span>
- <span data-ttu-id="db9ac-740">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-740">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-741">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-741">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-742">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-742">'Blazor'</span></span>
- <span data-ttu-id="db9ac-743">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-743">'Identity'</span></span>
- <span data-ttu-id="db9ac-744">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-744">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-745">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-745">'Razor'</span></span>
- <span data-ttu-id="db9ac-746">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-746">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-747">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-747">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-748">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-748">'Blazor'</span></span>
- <span data-ttu-id="db9ac-749">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-749">'Identity'</span></span>
- <span data-ttu-id="db9ac-750">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-750">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-751">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-751">'Razor'</span></span>
- <span data-ttu-id="db9ac-752">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-752">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-753">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-753">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-754">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-754">'Blazor'</span></span>
- <span data-ttu-id="db9ac-755">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-755">'Identity'</span></span>
- <span data-ttu-id="db9ac-756">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-756">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-757">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-757">'Razor'</span></span>
- <span data-ttu-id="db9ac-758">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-758">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-759">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-759">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-760">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-760">'Blazor'</span></span>
- <span data-ttu-id="db9ac-761">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-761">'Identity'</span></span>
- <span data-ttu-id="db9ac-762">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-762">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-763">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-763">'Razor'</span></span>
- <span data-ttu-id="db9ac-764">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-764">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-765">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-765">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-766">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-766">'Blazor'</span></span>
- <span data-ttu-id="db9ac-767">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-767">'Identity'</span></span>
- <span data-ttu-id="db9ac-768">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-768">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-769">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-769">'Razor'</span></span>
- <span data-ttu-id="db9ac-770">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-770">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-771">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-771">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-772">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-772">'Blazor'</span></span>
- <span data-ttu-id="db9ac-773">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-773">'Identity'</span></span>
- <span data-ttu-id="db9ac-774">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-774">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-775">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-775">'Razor'</span></span>
- <span data-ttu-id="db9ac-776">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-776">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-777">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-777">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-778">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-778">'Blazor'</span></span>
- <span data-ttu-id="db9ac-779">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-779">'Identity'</span></span>
- <span data-ttu-id="db9ac-780">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-780">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-781">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-781">'Razor'</span></span>
- <span data-ttu-id="db9ac-782">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-782">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-783">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-783">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-784">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-784">'Blazor'</span></span>
- <span data-ttu-id="db9ac-785">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-785">'Identity'</span></span>
- <span data-ttu-id="db9ac-786">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-786">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-787">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-787">'Razor'</span></span>
- <span data-ttu-id="db9ac-788">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-788">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-789">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-789">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-790">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-790">'Blazor'</span></span>
- <span data-ttu-id="db9ac-791">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-791">'Identity'</span></span>
- <span data-ttu-id="db9ac-792">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-792">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-793">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-793">'Razor'</span></span>
- <span data-ttu-id="db9ac-794">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-794">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-795">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-795">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-796">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-796">'Blazor'</span></span>
- <span data-ttu-id="db9ac-797">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-797">'Identity'</span></span>
- <span data-ttu-id="db9ac-798">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-798">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-799">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-799">'Razor'</span></span>
- <span data-ttu-id="db9ac-800">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-800">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-801">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-801">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-802">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-802">'Blazor'</span></span>
- <span data-ttu-id="db9ac-803">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-803">'Identity'</span></span>
- <span data-ttu-id="db9ac-804">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-804">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-805">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-805">'Razor'</span></span>
- <span data-ttu-id="db9ac-806">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-806">'SignalR' uid:</span></span> 

<span data-ttu-id="db9ac-807">---------------------------------------- | | `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | Entrée de configuration non créée.</span><span class="sxs-lookup"><span data-stu-id="db9ac-807">---------------------------------------- | | `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | Configuration entry not created.</span></span>                                                <span data-ttu-id="db9ac-808">| | `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | Clé : `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="db9ac-808">| | `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="db9ac-809">Valeur : `MySql.Data.MySqlClient` | | `SQLAZURECONNSTR_{KEY}`   |  `ConnectionStrings:{KEY}`   | Clé : `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="db9ac-809">Value: `MySql.Data.MySqlClient` | | `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="db9ac-810">Valeur : `System.Data.SqlClient` | | `SQLCONNSTR_{KEY}`        |  `ConnectionStrings:{KEY}`   | Clé : `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="db9ac-810">Value: `System.Data.SqlClient`  | | `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="db9ac-811">Ajoutée`System.Data.SqlClient`  |</span><span class="sxs-lookup"><span data-stu-id="db9ac-811">Value: `System.Data.SqlClient`  |</span></span>

<a name="jcp"></a>

### <a name="json-configuration-provider"></a><span data-ttu-id="db9ac-812">Fournisseur de configuration JSON</span><span class="sxs-lookup"><span data-stu-id="db9ac-812">JSON configuration provider</span></span>

<span data-ttu-id="db9ac-813">Le <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> charge la configuration à partir de paires clé-valeur de fichier JSON.</span><span class="sxs-lookup"><span data-stu-id="db9ac-813">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs.</span></span>

<span data-ttu-id="db9ac-814">Les surcharges peuvent spécifier :</span><span class="sxs-lookup"><span data-stu-id="db9ac-814">Overloads can specify:</span></span>

* <span data-ttu-id="db9ac-815">Si le fichier est facultatif.</span><span class="sxs-lookup"><span data-stu-id="db9ac-815">Whether the file is optional.</span></span>
* <span data-ttu-id="db9ac-816">Si la configuration est rechargée quand le fichier est modifié.</span><span class="sxs-lookup"><span data-stu-id="db9ac-816">Whether the configuration is reloaded if the file changes.</span></span>

<span data-ttu-id="db9ac-817">Examinons le code ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="db9ac-817">Consider the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON.cs?name=snippet&highlight=12-14)]

<span data-ttu-id="db9ac-818">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="db9ac-818">The preceding code:</span></span>

* <span data-ttu-id="db9ac-819">Configure le fournisseur de configuration JSON pour charger le fichier *MyConfig. JSON* avec les options suivantes :</span><span class="sxs-lookup"><span data-stu-id="db9ac-819">Configures the JSON configuration provider to load the *MyConfig.json* file with the following options:</span></span>
  * <span data-ttu-id="db9ac-820">`optional: true`: Le fichier est facultatif.</span><span class="sxs-lookup"><span data-stu-id="db9ac-820">`optional: true`: The file is optional.</span></span>
  * <span data-ttu-id="db9ac-821">`reloadOnChange: true`: Le fichier est rechargé lorsque des modifications sont enregistrées.</span><span class="sxs-lookup"><span data-stu-id="db9ac-821">`reloadOnChange: true` : The file is reloaded when changes are saved.</span></span>
* <span data-ttu-id="db9ac-822">Lit les [fournisseurs de configuration par défaut](#default) avant le fichier *MyConfig. JSON* .</span><span class="sxs-lookup"><span data-stu-id="db9ac-822">Reads the [default configuration providers](#default) before the *MyConfig.json* file.</span></span> <span data-ttu-id="db9ac-823">Paramètres dans le paramètre de remplacement de fichier *MyConfig. JSON* des fournisseurs de configuration par défaut, y compris le [fournisseur de configuration des variables d’environnement](#evcp) et le fournisseur de configuration de ligne de [commande](#clcp).</span><span class="sxs-lookup"><span data-stu-id="db9ac-823">Settings in the *MyConfig.json* file override setting in the default configuration providers, including the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="db9ac-824">En général, vous ***ne souhaitez pas*** qu’une valeur de substitution de fichier JSON personnalisée soit définie dans le fournisseur de configuration des [variables d’environnement](#evcp) et dans le fournisseur de configuration de [ligne de commande](#clcp).</span><span class="sxs-lookup"><span data-stu-id="db9ac-824">You typically ***don't*** want a custom JSON file overriding values set in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="db9ac-825">Le code suivant efface tous les fournisseurs de configuration et ajoute plusieurs fournisseurs de configuration :</span><span class="sxs-lookup"><span data-stu-id="db9ac-825">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON2.cs?name=snippet)]

<span data-ttu-id="db9ac-826">Dans le code précédent, les paramètres des *MyConfig. JSON* et *MyConfig*. `Environment` . fichiers *JSON* :</span><span class="sxs-lookup"><span data-stu-id="db9ac-826">In the preceding code, settings in the *MyConfig.json* and  *MyConfig*.`Environment`.*json* files:</span></span>

* <span data-ttu-id="db9ac-827">Substituez les paramètres dans *appSettings. JSON* et *appSettings*. `Environment` fichiers *JSON* .</span><span class="sxs-lookup"><span data-stu-id="db9ac-827">Override settings in the *appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="db9ac-828">Sont remplacées par les paramètres dans le [fournisseur de configuration des variables d’environnement](#evcp) et le fournisseur de configuration de ligne de [commande](#clcp).</span><span class="sxs-lookup"><span data-stu-id="db9ac-828">Are overridden by settings in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="db9ac-829">L' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contient le fichier *MyConfig. JSON* suivant :</span><span class="sxs-lookup"><span data-stu-id="db9ac-829">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *MyConfig.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyConfig.json)]

<span data-ttu-id="db9ac-830">Le code suivant de l' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) affiche plusieurs des paramètres de configuration précédents :</span><span class="sxs-lookup"><span data-stu-id="db9ac-830">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<a name="fcp"></a>

## <a name="file-configuration-provider"></a><span data-ttu-id="db9ac-831">Fournisseur de configuration de fichier</span><span class="sxs-lookup"><span data-stu-id="db9ac-831">File configuration provider</span></span>

<span data-ttu-id="db9ac-832"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> est la classe de base pour charger la configuration à partir du système de fichiers.</span><span class="sxs-lookup"><span data-stu-id="db9ac-832"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="db9ac-833">Les fournisseurs de configuration suivants dérivent de `FileConfigurationProvider` :</span><span class="sxs-lookup"><span data-stu-id="db9ac-833">The following configuration providers derive from `FileConfigurationProvider`:</span></span>

* [<span data-ttu-id="db9ac-834">Fournisseur de configuration INI</span><span class="sxs-lookup"><span data-stu-id="db9ac-834">INI configuration provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="db9ac-835">Fournisseur de configuration JSON</span><span class="sxs-lookup"><span data-stu-id="db9ac-835">JSON configuration provider</span></span>](#jcp)
* [<span data-ttu-id="db9ac-836">Fournisseur de configuration XML</span><span class="sxs-lookup"><span data-stu-id="db9ac-836">XML configuration provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="db9ac-837">Fournisseur de configuration INI</span><span class="sxs-lookup"><span data-stu-id="db9ac-837">INI configuration provider</span></span>

<span data-ttu-id="db9ac-838"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> charge la configuration à partir des paires clé-valeur du fichier INI lors de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="db9ac-838">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="db9ac-839">Le code suivant efface tous les fournisseurs de configuration et ajoute plusieurs fournisseurs de configuration :</span><span class="sxs-lookup"><span data-stu-id="db9ac-839">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramINI.cs?name=snippet&highlight=10-30)]

<span data-ttu-id="db9ac-840">Dans le code précédent, les paramètres des *MyIniConfig. ini* et *MyIniConfig*. `Environment` . les fichiers *ini* sont remplacés par les paramètres dans le :</span><span class="sxs-lookup"><span data-stu-id="db9ac-840">In the preceding code, settings in the *MyIniConfig.ini* and  *MyIniConfig*.`Environment`.*ini* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="db9ac-841">Fournisseur de configuration des variables d’environnement</span><span class="sxs-lookup"><span data-stu-id="db9ac-841">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="db9ac-842">[Fournisseur de configuration de ligne de commande](#clcp).</span><span class="sxs-lookup"><span data-stu-id="db9ac-842">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="db9ac-843">L' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contient le fichier *MyIniConfig. ini* suivant :</span><span class="sxs-lookup"><span data-stu-id="db9ac-843">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyIniConfig.ini* file:</span></span>

[!code-ini[](index/samples/3.x/ConfigSample/MyIniConfig.ini)]

<span data-ttu-id="db9ac-844">Le code suivant de l' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) affiche plusieurs des paramètres de configuration précédents :</span><span class="sxs-lookup"><span data-stu-id="db9ac-844">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

### <a name="xml-configuration-provider"></a><span data-ttu-id="db9ac-845">Fournisseur de configuration XML</span><span class="sxs-lookup"><span data-stu-id="db9ac-845">XML configuration provider</span></span>

<span data-ttu-id="db9ac-846"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> charge la configuration à partir des paires clé-valeur du fichier XML lors de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="db9ac-846">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="db9ac-847">Le code suivant efface tous les fournisseurs de configuration et ajoute plusieurs fournisseurs de configuration :</span><span class="sxs-lookup"><span data-stu-id="db9ac-847">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramXML.cs?name=snippet)]

<span data-ttu-id="db9ac-848">Dans le code précédent, les paramètres des *MyXMLFile. xml* et *MyXMLFile*. `Environment` . les fichiers *XML* sont remplacés par les paramètres dans le :</span><span class="sxs-lookup"><span data-stu-id="db9ac-848">In the preceding code, settings in the *MyXMLFile.xml* and  *MyXMLFile*.`Environment`.*xml* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="db9ac-849">Fournisseur de configuration des variables d’environnement</span><span class="sxs-lookup"><span data-stu-id="db9ac-849">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="db9ac-850">[Fournisseur de configuration de ligne de commande](#clcp).</span><span class="sxs-lookup"><span data-stu-id="db9ac-850">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="db9ac-851">L' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contient le fichier *MyXMLFile. xml* suivant :</span><span class="sxs-lookup"><span data-stu-id="db9ac-851">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyXMLFile.xml* file:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile.xml)]

<span data-ttu-id="db9ac-852">Le code suivant de l' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) affiche plusieurs des paramètres de configuration précédents :</span><span class="sxs-lookup"><span data-stu-id="db9ac-852">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="db9ac-853">Les éléments répétitifs qui utilisent le même nom d’élément fonctionnent si l’attribut `name` est utilisé pour distinguer les éléments :</span><span class="sxs-lookup"><span data-stu-id="db9ac-853">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile3.xml)]

<span data-ttu-id="db9ac-854">Le code suivant lit le fichier de configuration précédent et affiche les clés et les valeurs :</span><span class="sxs-lookup"><span data-stu-id="db9ac-854">The following code reads the previous configuration file and displays the keys and values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/XML/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="db9ac-855">Les attributs peuvent être utilisés pour fournir des valeurs :</span><span class="sxs-lookup"><span data-stu-id="db9ac-855">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="db9ac-856">Le fichier de configuration précédent charge les clés suivantes avec `value` :</span><span class="sxs-lookup"><span data-stu-id="db9ac-856">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="db9ac-857">key:attribute</span><span class="sxs-lookup"><span data-stu-id="db9ac-857">key:attribute</span></span>
* <span data-ttu-id="db9ac-858">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="db9ac-858">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="db9ac-859">Fournisseur de configuration de clé par fichier</span><span class="sxs-lookup"><span data-stu-id="db9ac-859">Key-per-file configuration provider</span></span>

<span data-ttu-id="db9ac-860">Le <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> utilise les fichiers d’un répertoire en tant que paires clé-valeur de configuration.</span><span class="sxs-lookup"><span data-stu-id="db9ac-860">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="db9ac-861">La clé est le nom de fichier.</span><span class="sxs-lookup"><span data-stu-id="db9ac-861">The key is the file name.</span></span> <span data-ttu-id="db9ac-862">La valeur contient le contenu du fichier.</span><span class="sxs-lookup"><span data-stu-id="db9ac-862">The value contains the file's contents.</span></span> <span data-ttu-id="db9ac-863">Le fournisseur de configuration par fichier clé est utilisé dans les scénarios d’hébergement de l’ancrage.</span><span class="sxs-lookup"><span data-stu-id="db9ac-863">The Key-per-file configuration provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="db9ac-864">Pour activer la configuration clé par fichier, appelez la méthode d’extension <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> sur une instance de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="db9ac-864">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="db9ac-865">Le `directoryPath` aux fichiers doit être un chemin d’accès absolu.</span><span class="sxs-lookup"><span data-stu-id="db9ac-865">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="db9ac-866">Les surcharges permettent de spécifier :</span><span class="sxs-lookup"><span data-stu-id="db9ac-866">Overloads permit specifying:</span></span>

* <span data-ttu-id="db9ac-867">Un délégué `Action<KeyPerFileConfigurationSource>` qui configure la source.</span><span class="sxs-lookup"><span data-stu-id="db9ac-867">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="db9ac-868">Si le répertoire est facultatif et le chemin d’accès au répertoire.</span><span class="sxs-lookup"><span data-stu-id="db9ac-868">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="db9ac-869">Le double trait de soulignement (`__`) est utilisé comme un délimiteur de clé de configuration dans les noms de fichiers.</span><span class="sxs-lookup"><span data-stu-id="db9ac-869">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="db9ac-870">Par exemple, le nom de fichier `Logging__LogLevel__System` génère la clé de configuration `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="db9ac-870">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="db9ac-871">Appelez `ConfigureAppConfiguration` lors de la création de l’hôte pour spécifier la configuration de l’application :</span><span class="sxs-lookup"><span data-stu-id="db9ac-871">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<a name="mcp"></a>

## <a name="memory-configuration-provider"></a><span data-ttu-id="db9ac-872">Fournisseur de configuration de la mémoire</span><span class="sxs-lookup"><span data-stu-id="db9ac-872">Memory configuration provider</span></span>

<span data-ttu-id="db9ac-873">Le <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> utilise une collection en mémoire en tant que paires clé-valeur de configuration.</span><span class="sxs-lookup"><span data-stu-id="db9ac-873">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="db9ac-874">Le code suivant ajoute une collection de mémoire au système de configuration :</span><span class="sxs-lookup"><span data-stu-id="db9ac-874">The following code adds a memory collection to the configuration system:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet6)]

<span data-ttu-id="db9ac-875">Le code suivant de l' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) affiche les paramètres de configuration précédents :</span><span class="sxs-lookup"><span data-stu-id="db9ac-875">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="db9ac-876">Dans le code précédent, `config.AddInMemoryCollection(Dict)` est ajouté après les [fournisseurs de configuration par défaut](#default).</span><span class="sxs-lookup"><span data-stu-id="db9ac-876">In the preceding code, `config.AddInMemoryCollection(Dict)` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="db9ac-877">Pour obtenir un exemple de classement des fournisseurs de configuration, consultez [fournisseur de configuration JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="db9ac-877">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="db9ac-878">Pour un autre exemple, consultez [lier un tableau](#boa) à l’aide de `MemoryConfigurationProvider` .</span><span class="sxs-lookup"><span data-stu-id="db9ac-878">See [Bind an array](#boa) for another example using `MemoryConfigurationProvider`.</span></span>

## <a name="getvalue"></a><span data-ttu-id="db9ac-879">GetValue</span><span class="sxs-lookup"><span data-stu-id="db9ac-879">GetValue</span></span>

<span data-ttu-id="db9ac-880">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*)extrait une valeur unique de la configuration avec une clé spécifiée et la convertit en type spécifié :</span><span class="sxs-lookup"><span data-stu-id="db9ac-880">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified type:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestNum.cshtml.cs?name=snippet)]

<span data-ttu-id="db9ac-881">Dans le code précédent, si est `NumberKey` introuvable dans la configuration, la valeur par défaut de `99` est utilisée.</span><span class="sxs-lookup"><span data-stu-id="db9ac-881">In the preceding code,  if `NumberKey` isn't found in the configuration, the default value of `99` is used.</span></span>

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="db9ac-882">GetSection, GetChildren et Exists</span><span class="sxs-lookup"><span data-stu-id="db9ac-882">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="db9ac-883">Pour les exemples qui suivent, examinez le fichier *MySubsection. JSON* suivant :</span><span class="sxs-lookup"><span data-stu-id="db9ac-883">For the examples that follow, consider the following *MySubsection.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MySubsection.json)]

<span data-ttu-id="db9ac-884">Le code suivant ajoute *MySubsection. JSON* aux fournisseurs de configuration :</span><span class="sxs-lookup"><span data-stu-id="db9ac-884">The following code adds *MySubsection.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONsection.cs?name=snippet)]

### <a name="getsection"></a><span data-ttu-id="db9ac-885">GetSection</span><span class="sxs-lookup"><span data-stu-id="db9ac-885">GetSection</span></span>

<span data-ttu-id="db9ac-886">[IConfiguration. GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) retourne une sous-section de configuration avec la clé de sous-section spécifiée.</span><span class="sxs-lookup"><span data-stu-id="db9ac-886">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) returns a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="db9ac-887">Le code suivant retourne des valeurs pour `section1` :</span><span class="sxs-lookup"><span data-stu-id="db9ac-887">The following code returns values for `section1`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection.cshtml.cs?name=snippet)]

<span data-ttu-id="db9ac-888">Le code suivant retourne des valeurs pour `section2:subsection0` :</span><span class="sxs-lookup"><span data-stu-id="db9ac-888">The following code returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection2.cshtml.cs?name=snippet)]

<span data-ttu-id="db9ac-889">`GetSection` ne retourne jamais `null`.</span><span class="sxs-lookup"><span data-stu-id="db9ac-889">`GetSection` never returns `null`.</span></span> <span data-ttu-id="db9ac-890">Si aucune section correspondante n’est trouvée, une valeur `IConfigurationSection` vide est retournée.</span><span class="sxs-lookup"><span data-stu-id="db9ac-890">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="db9ac-891">Quand `GetSection` retourne une section correspondante, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> n’est pas rempli.</span><span class="sxs-lookup"><span data-stu-id="db9ac-891">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="db9ac-892"><xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> et <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> sont retournés quand la section existe.</span><span class="sxs-lookup"><span data-stu-id="db9ac-892">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren-and-exists"></a><span data-ttu-id="db9ac-893">GetChildren et EXISTS</span><span class="sxs-lookup"><span data-stu-id="db9ac-893">GetChildren and Exists</span></span>

<span data-ttu-id="db9ac-894">Le code suivant appelle [IConfiguration. GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) et retourne des valeurs pour `section2:subsection0` :</span><span class="sxs-lookup"><span data-stu-id="db9ac-894">The following code calls [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) and returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection4.cshtml.cs?name=snippet)]

<span data-ttu-id="db9ac-895">Le code précédent appelle [ConfigurationExtensions. Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) pour vérifier l’existence de la section :</span><span class="sxs-lookup"><span data-stu-id="db9ac-895">The preceding code calls [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to verify the  section exists:</span></span>

 <a name="boa"></a>

## <a name="bind-an-array"></a><span data-ttu-id="db9ac-896">Lier un tableau</span><span class="sxs-lookup"><span data-stu-id="db9ac-896">Bind an array</span></span>

<span data-ttu-id="db9ac-897">[ConfigurationBinder. bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) prend en charge les tableaux de liaison aux objets à l’aide d’index de tableau dans les clés de configuration.</span><span class="sxs-lookup"><span data-stu-id="db9ac-897">The [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="db9ac-898">Tout format de tableau qui expose un segment de clé numérique est capable d’effectuer une liaison de tableau à un tableau de classes [poco](https://wikipedia.org/wiki/Plain_Old_CLR_Object) .</span><span class="sxs-lookup"><span data-stu-id="db9ac-898">Any array format that exposes a numeric key segment is capable of array binding to a [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) class array.</span></span>

<span data-ttu-id="db9ac-899">Examinez *myArray. JSON* de l' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span><span class="sxs-lookup"><span data-stu-id="db9ac-899">Consider *MyArray.json* from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyArray.json)]

<span data-ttu-id="db9ac-900">Le code suivant ajoute *myArray. JSON* aux fournisseurs de configuration :</span><span class="sxs-lookup"><span data-stu-id="db9ac-900">The following code adds *MyArray.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONarray.cs?name=snippet)]

<span data-ttu-id="db9ac-901">Le code suivant lit la configuration et affiche les valeurs :</span><span class="sxs-lookup"><span data-stu-id="db9ac-901">The following code reads the configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="db9ac-902">Le code précédent retourne la sortie suivante :</span><span class="sxs-lookup"><span data-stu-id="db9ac-902">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value00
Index: 1  Value: value10
Index: 2  Value: value20
Index: 3  Value: value40
Index: 4  Value: value50
```

<span data-ttu-id="db9ac-903">Dans la sortie précédente, l’index 3 a la valeur `value40` , qui correspond à `"4": "value40",` dans *myArray. JSON*.</span><span class="sxs-lookup"><span data-stu-id="db9ac-903">In the preceding output, Index 3 has value `value40`, corresponding to `"4": "value40",` in *MyArray.json*.</span></span> <span data-ttu-id="db9ac-904">Les index de tableau liés sont continus et non liés à l’index de clé de configuration.</span><span class="sxs-lookup"><span data-stu-id="db9ac-904">The bound array indices are continuous and not bound to the configuration key index.</span></span> <span data-ttu-id="db9ac-905">Le Binder de configuration n’est pas en capacité à lier des valeurs null ou à créer des entrées NULL dans des objets liés</span><span class="sxs-lookup"><span data-stu-id="db9ac-905">The configuration binder isn't capable of binding null values or creating null entries in bound objects</span></span>

<span data-ttu-id="db9ac-906">Le code suivant charge la `array:entries` configuration avec la <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> méthode d’extension :</span><span class="sxs-lookup"><span data-stu-id="db9ac-906">The  following code loads the `array:entries` configuration with the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet)]

<span data-ttu-id="db9ac-907">Le code suivant lit la configuration dans `arrayDict` `Dictionary` et affiche les valeurs :</span><span class="sxs-lookup"><span data-stu-id="db9ac-907">The following code reads the configuration in the `arrayDict` `Dictionary` and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="db9ac-908">Le code précédent retourne la sortie suivante :</span><span class="sxs-lookup"><span data-stu-id="db9ac-908">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value4
Index: 4  Value: value5
```

<span data-ttu-id="db9ac-909">L’index &num;3 dans l’objet lié contient les données de configuration pour la clé de configuration `array:4` et sa valeur de `value4`.</span><span class="sxs-lookup"><span data-stu-id="db9ac-909">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="db9ac-910">Lorsque les données de configuration contenant un tableau sont liées, les index de tableau dans les clés de configuration sont utilisés pour itérer les données de configuration lors de la création de l’objet.</span><span class="sxs-lookup"><span data-stu-id="db9ac-910">When configuration data containing an array is bound, the array indices in the configuration keys are used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="db9ac-911">Une valeur null ne peut pas être conservée dans des données de configuration, et une entrée à valeur null n’est pas créée dans un objet lié quand un tableau dans des clés de configuration ignore un ou plusieurs index.</span><span class="sxs-lookup"><span data-stu-id="db9ac-911">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="db9ac-912">L’élément de configuration manquant pour l’index &num; 3 peut être fourni avant la liaison à l' `ArrayExample` instance par n’importe quel fournisseur de configuration qui lit la &num; paire clé/valeur de l’index 3.</span><span class="sxs-lookup"><span data-stu-id="db9ac-912">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that reads the index &num;3 key/value pair.</span></span> <span data-ttu-id="db9ac-913">Considérez le fichier *valeur3. JSON* suivant dans l’exemple de téléchargement :</span><span class="sxs-lookup"><span data-stu-id="db9ac-913">Consider the following *Value3.json* file from the sample download:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/Value3.json)]

<span data-ttu-id="db9ac-914">Le code suivant comprend la configuration de *valeur3. JSON* et `arrayDict` `Dictionary` :</span><span class="sxs-lookup"><span data-stu-id="db9ac-914">The following code includes configuration for *Value3.json* and the `arrayDict` `Dictionary`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet2)]

<span data-ttu-id="db9ac-915">Le code suivant lit la configuration précédente et affiche les valeurs :</span><span class="sxs-lookup"><span data-stu-id="db9ac-915">The following code reads the preceding configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="db9ac-916">Le code précédent retourne la sortie suivante :</span><span class="sxs-lookup"><span data-stu-id="db9ac-916">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value3
Index: 4  Value: value4
Index: 5  Value: value5
```

<span data-ttu-id="db9ac-917">Les fournisseurs de configuration personnalisés ne sont pas obligés d’implémenter la liaison de tableau.</span><span class="sxs-lookup"><span data-stu-id="db9ac-917">Custom configuration providers aren't required to implement array binding.</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="db9ac-918">Fournisseur de configuration personnalisé</span><span class="sxs-lookup"><span data-stu-id="db9ac-918">Custom configuration provider</span></span>

<span data-ttu-id="db9ac-919">L’exemple d’application montre comment créer un fournisseur de configuration de base qui lit les paires clé-valeur de configuration à partir d’une base de données à l’aide [d’Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="db9ac-919">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="db9ac-920">Le fournisseur présente les caractéristiques suivantes :</span><span class="sxs-lookup"><span data-stu-id="db9ac-920">The provider has the following characteristics:</span></span>

* <span data-ttu-id="db9ac-921">La base de données en mémoire EF est utilisée à des fins de démonstration.</span><span class="sxs-lookup"><span data-stu-id="db9ac-921">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="db9ac-922">Pour utiliser une base de données qui nécessite une chaîne de connexion, implémentez un autre `ConfigurationBuilder` pour fournir la chaîne de connexion à partir d’un autre fournisseur de configuration.</span><span class="sxs-lookup"><span data-stu-id="db9ac-922">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="db9ac-923">Le fournisseur lit une table de base de données dans la configuration au démarrage.</span><span class="sxs-lookup"><span data-stu-id="db9ac-923">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="db9ac-924">Le fournisseur n’interroge pas la base de données par clé.</span><span class="sxs-lookup"><span data-stu-id="db9ac-924">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="db9ac-925">Le rechargement en cas de changement n’est pas implémenté, par conséquent, la mise à jour la base de données après le démarrage de l’application n’a aucun effet sur la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="db9ac-925">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="db9ac-926">Définissez une entité `EFConfigurationValue` pour le stockage des valeurs de configuration dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="db9ac-926">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="db9ac-927">*Models/EFConfigurationValue.cs* :</span><span class="sxs-lookup"><span data-stu-id="db9ac-927">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="db9ac-928">Ajoutez un `EFConfigurationContext` pour stocker les valeurs configurées et y accéder.</span><span class="sxs-lookup"><span data-stu-id="db9ac-928">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="db9ac-929">*EFConfigurationProvider/EFConfigurationContext.cs* :</span><span class="sxs-lookup"><span data-stu-id="db9ac-929">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="db9ac-930">Créez une classe qui implémente <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="db9ac-930">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="db9ac-931">*EFConfigurationProvider/EFConfigurationSource.cs* :</span><span class="sxs-lookup"><span data-stu-id="db9ac-931">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="db9ac-932">Créez le fournisseur de configuration personnalisé en héritant de <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="db9ac-932">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="db9ac-933">Le fournisseur de configuration initialise la base de données quand elle est vide.</span><span class="sxs-lookup"><span data-stu-id="db9ac-933">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="db9ac-934">Étant donné que les [clés de configuration ne](#keys)respectent pas la casse, le dictionnaire utilisé pour initialiser la base de données est créé avec le comparateur ne respectant pas la casse ([StringComparer. OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span><span class="sxs-lookup"><span data-stu-id="db9ac-934">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="db9ac-935">*EFConfigurationProvider/EFConfigurationProvider.cs* :</span><span class="sxs-lookup"><span data-stu-id="db9ac-935">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="db9ac-936">Une méthode d’extension `AddEFConfiguration` permet d’ajouter la source de configuration à un `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="db9ac-936">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="db9ac-937">*Extensions/EntityFrameworkExtensions.cs* :</span><span class="sxs-lookup"><span data-stu-id="db9ac-937">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="db9ac-938">Le code suivant montre comment utiliser le `EFConfigurationProvider` personnalisé dans *Program.cs* :</span><span class="sxs-lookup"><span data-stu-id="db9ac-938">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

<a name="acs"></a>

## <a name="access-configuration-in-startup"></a><span data-ttu-id="db9ac-939">Configuration de l’accès au démarrage</span><span class="sxs-lookup"><span data-stu-id="db9ac-939">Access configuration in Startup</span></span>

<span data-ttu-id="db9ac-940">Le code suivant affiche les données de configuration dans les `Startup` méthodes :</span><span class="sxs-lookup"><span data-stu-id="db9ac-940">The following code displays configuration data in `Startup` methods:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/StartupKey.cs?name=snippet&highlight=13,18)]

<span data-ttu-id="db9ac-941">Pour obtenir un exemple d’accès à la configuration à l’aide des méthodes pratiques de démarrage, consultez [Démarrage de l’application : méthodes pratiques](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="db9ac-941">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-razor-pages"></a><span data-ttu-id="db9ac-942">Configuration de l’accès dans les Razor pages</span><span class="sxs-lookup"><span data-stu-id="db9ac-942">Access configuration in Razor Pages</span></span>

<span data-ttu-id="db9ac-943">Le code suivant affiche les données de configuration dans une Razor page :</span><span class="sxs-lookup"><span data-stu-id="db9ac-943">The following code displays configuration data in a Razor Page:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Pages/Test5.cshtml)]

<span data-ttu-id="db9ac-944">Dans le code suivant, `MyOptions` est ajouté au conteneur de services avec <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> et lié à la configuration :</span><span class="sxs-lookup"><span data-stu-id="db9ac-944">In the following code, `MyOptions` is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="db9ac-945">La balise suivante utilise la [`@inject`](xref:mvc/views/razor#inject) Razor directive pour résoudre et afficher les valeurs des options :</span><span class="sxs-lookup"><span data-stu-id="db9ac-945">The following markup uses the [`@inject`](xref:mvc/views/razor#inject) Razor directive to resolve and display the options values:</span></span>

[!code-cshtml[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Pages/Test3.cshtml)]

## <a name="access-configuration-in-a-mvc-view-file"></a><span data-ttu-id="db9ac-946">Configuration de l’accès dans un fichier de vue MVC</span><span class="sxs-lookup"><span data-stu-id="db9ac-946">Access configuration in a MVC view file</span></span>

<span data-ttu-id="db9ac-947">Le code suivant affiche les données de configuration dans une vue MVC :</span><span class="sxs-lookup"><span data-stu-id="db9ac-947">The following code displays configuration data in a MVC view:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Views/Home2/Index.cshtml)]

## <a name="configure-options-with-a-delegate"></a><span data-ttu-id="db9ac-948">Configurer des options avec un délégué</span><span class="sxs-lookup"><span data-stu-id="db9ac-948">Configure options with a delegate</span></span>

<span data-ttu-id="db9ac-949">Les options configurées dans un délégué remplacent les valeurs définies dans les fournisseurs de configuration.</span><span class="sxs-lookup"><span data-stu-id="db9ac-949">Options configured in a delegate override values set in the configuration providers.</span></span>

<span data-ttu-id="db9ac-950">La configuration d’options avec un délégué est illustrée dans l’exemple 2 de l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="db9ac-950">Configuring options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="db9ac-951">Dans le code suivant, un <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service est ajouté au conteneur de services.</span><span class="sxs-lookup"><span data-stu-id="db9ac-951">In the following code, an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="db9ac-952">Il utilise un délégué pour configurer des valeurs pour `MyOptions` :</span><span class="sxs-lookup"><span data-stu-id="db9ac-952">It uses a delegate to configure values for `MyOptions`:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup2.cs?name=snippet_Example2)]

<span data-ttu-id="db9ac-953">Le code suivant affiche les valeurs des options :</span><span class="sxs-lookup"><span data-stu-id="db9ac-953">The following code displays the options values:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Test2.cshtml.cs?name=snippet)]

<span data-ttu-id="db9ac-954">Dans l’exemple précédent, les valeurs de `Option1` et `Option2` sont spécifiées dans *appSettings. JSON* , puis remplacées par le délégué configuré.</span><span class="sxs-lookup"><span data-stu-id="db9ac-954">In the preceding example, the values of `Option1` and `Option2` are specified in *appsettings.json* and then overridden by the configured delegate.</span></span>

<a name="hvac"></a>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="db9ac-955">Configuration de l’hôte ou configuration de l’application</span><span class="sxs-lookup"><span data-stu-id="db9ac-955">Host versus app configuration</span></span>

<span data-ttu-id="db9ac-956">Avant que l’application ne soit configurée et démarrée, un *hôte* est configuré et lancé.</span><span class="sxs-lookup"><span data-stu-id="db9ac-956">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="db9ac-957">L’hôte est responsable de la gestion du démarrage et de la durée de vie des applications.</span><span class="sxs-lookup"><span data-stu-id="db9ac-957">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="db9ac-958">L’application et l’hôte sont configurés à l’aide des fournisseurs de configuration décrits dans cette rubrique.</span><span class="sxs-lookup"><span data-stu-id="db9ac-958">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="db9ac-959">Les paires clé-valeur de la configuration de l’hôte sont également incluses dans la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="db9ac-959">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="db9ac-960">Pour plus d’informations sur l’utilisation des fournisseurs de configuration lors de la génération de l’hôte et l’impact des sources de configuration sur la configuration de l’hôte, consultez <xref:fundamentals/index#host>.</span><span class="sxs-lookup"><span data-stu-id="db9ac-960">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

<a name="dhc"></a>

## <a name="default-host-configuration"></a><span data-ttu-id="db9ac-961">Configuration de l’hôte par défaut</span><span class="sxs-lookup"><span data-stu-id="db9ac-961">Default host configuration</span></span>

<span data-ttu-id="db9ac-962">Pour plus de détails sur la configuration par défaut lors de l’utilisation de l’[hôte Web](xref:fundamentals/host/web-host), consultez la [version ASP.NET Core 2.2. de cette rubrique](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="db9ac-962">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="db9ac-963">La configuration de l’hôte est fournie à partir des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="db9ac-963">Host configuration is provided from:</span></span>
  * <span data-ttu-id="db9ac-964">Les variables d’environnement précédées du préfixe `DOTNET_` (par exemple, `DOTNET_ENVIRONMENT` ) à l’aide du [fournisseur de configuration des variables d’environnement](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="db9ac-964">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables configuration provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="db9ac-965">Le préfixe (`DOTNET_`) est supprimé lorsque les paires clé-valeur de la configuration sont chargées.</span><span class="sxs-lookup"><span data-stu-id="db9ac-965">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="db9ac-966">Arguments de ligne de commande à l’aide du [fournisseur de configuration de ligne de commande](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="db9ac-966">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="db9ac-967">La configuration par défaut de l’hôte Web est établie (`ConfigureWebHostDefaults`) :</span><span class="sxs-lookup"><span data-stu-id="db9ac-967">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="db9ac-968">Kestrel est utilisé comme serveur web et configuré à l’aide des fournisseurs de configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="db9ac-968">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="db9ac-969">Ajoutez l’intergiciel de filtrage d’hôtes.</span><span class="sxs-lookup"><span data-stu-id="db9ac-969">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="db9ac-970">Ajoutez l’intergiciel d’en-têtes transférés si la variable d'environnement `ASPNETCORE_FORWARDEDHEADERS_ENABLED` est définie sur `true`.</span><span class="sxs-lookup"><span data-stu-id="db9ac-970">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="db9ac-971">Activez l’intégration d’IIS.</span><span class="sxs-lookup"><span data-stu-id="db9ac-971">Enable IIS integration.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="db9ac-972">Autre configuration</span><span class="sxs-lookup"><span data-stu-id="db9ac-972">Other configuration</span></span>

<span data-ttu-id="db9ac-973">Cette rubrique se rapporte uniquement à la configuration de l' *application*.</span><span class="sxs-lookup"><span data-stu-id="db9ac-973">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="db9ac-974">D’autres aspects de l’exécution et de l’hébergement des applications ASP.NET Core sont configurés à l’aide des fichiers de configuration non traités dans cette rubrique :</span><span class="sxs-lookup"><span data-stu-id="db9ac-974">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="db9ac-975">*Launch. JSON* / *launchSettings. JSON* sont des fichiers de configuration d’outils pour l’environnement de développement, décrits ci-après :</span><span class="sxs-lookup"><span data-stu-id="db9ac-975">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="db9ac-976">Dans <xref:fundamentals/environments#development> .</span><span class="sxs-lookup"><span data-stu-id="db9ac-976">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="db9ac-977">Dans l’ensemble de la documentation dans lequel les fichiers sont utilisés pour configurer des applications ASP.NET Core pour les scénarios de développement.</span><span class="sxs-lookup"><span data-stu-id="db9ac-977">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="db9ac-978">*Web. config* est un fichier de configuration de serveur, décrit dans les rubriques suivantes :</span><span class="sxs-lookup"><span data-stu-id="db9ac-978">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="db9ac-979">Pour plus d’informations sur la migration de la configuration d’application à partir de versions antérieures de ASP.NET, consultez <xref:migration/proper-to-2x/index#store-configurations> .</span><span class="sxs-lookup"><span data-stu-id="db9ac-979">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="db9ac-980">Ajouter la configuration à partir d’un assembly externe</span><span class="sxs-lookup"><span data-stu-id="db9ac-980">Add configuration from an external assembly</span></span>

<span data-ttu-id="db9ac-981">Une implémentation de <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> permet d’ajouter des améliorations à une application au démarrage à partir d’un assembly externe, en dehors de la classe `Startup` de l’application.</span><span class="sxs-lookup"><span data-stu-id="db9ac-981">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="db9ac-982">Pour plus d'informations, consultez <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="db9ac-982">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="db9ac-983">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="db9ac-983">Additional resources</span></span>

* [<span data-ttu-id="db9ac-984">Code source de configuration</span><span class="sxs-lookup"><span data-stu-id="db9ac-984">Configuration source code</span></span>](https://github.com/dotnet/extensions/tree/master/src/Configuration)
* <xref:fundamentals/configuration/options>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="db9ac-985">La configuration d’application dans ASP.NET Core est basée sur des paires clé-valeur établies par les *fournisseurs de configuration*.</span><span class="sxs-lookup"><span data-stu-id="db9ac-985">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="db9ac-986">Les fournisseurs de configuration lisent les données de configuration dans les paires clé-valeur à partir de diverses sources de configuration :</span><span class="sxs-lookup"><span data-stu-id="db9ac-986">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="db9ac-987">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="db9ac-987">Azure Key Vault</span></span>
* <span data-ttu-id="db9ac-988">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="db9ac-988">Azure App Configuration</span></span>
* <span data-ttu-id="db9ac-989">Arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="db9ac-989">Command-line arguments</span></span>
* <span data-ttu-id="db9ac-990">Fournisseurs personnalisés (installés ou créés)</span><span class="sxs-lookup"><span data-stu-id="db9ac-990">Custom providers (installed or created)</span></span>
* <span data-ttu-id="db9ac-991">Fichiers de répertoire</span><span class="sxs-lookup"><span data-stu-id="db9ac-991">Directory files</span></span>
* <span data-ttu-id="db9ac-992">Variables d'environnement</span><span class="sxs-lookup"><span data-stu-id="db9ac-992">Environment variables</span></span>
* <span data-ttu-id="db9ac-993">Objets .NET en mémoire</span><span class="sxs-lookup"><span data-stu-id="db9ac-993">In-memory .NET objects</span></span>
* <span data-ttu-id="db9ac-994">Fichiers de paramètres</span><span class="sxs-lookup"><span data-stu-id="db9ac-994">Settings files</span></span>

<span data-ttu-id="db9ac-995">Les packages de configuration pour les scénarios de fournisseur de configuration courants ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) sont inclus implicitement dans le [métapaquet Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="db9ac-995">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="db9ac-996">Les exemples de code qui suivent et dans l’échantillon d’application utilisent l’espace de noms <xref:Microsoft.Extensions.Configuration> :</span><span class="sxs-lookup"><span data-stu-id="db9ac-996">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="db9ac-997">Le *modèle d’options* est une extension des concepts de configuration décrits dans cette rubrique.</span><span class="sxs-lookup"><span data-stu-id="db9ac-997">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="db9ac-998">Les options utilisent des classes pour représenter les groupes de paramètres associés.</span><span class="sxs-lookup"><span data-stu-id="db9ac-998">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="db9ac-999">Pour plus d'informations, consultez <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="db9ac-999">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="db9ac-1000">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="db9ac-1000">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="db9ac-1001">Configuration de l’hôte ou configuration de l’application</span><span class="sxs-lookup"><span data-stu-id="db9ac-1001">Host versus app configuration</span></span>

<span data-ttu-id="db9ac-1002">Avant que l’application ne soit configurée et démarrée, un *hôte* est configuré et lancé.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1002">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="db9ac-1003">L’hôte est responsable de la gestion du démarrage et de la durée de vie des applications.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1003">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="db9ac-1004">L’application et l’hôte sont configurés à l’aide des fournisseurs de configuration décrits dans cette rubrique.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1004">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="db9ac-1005">Les paires clé-valeur de la configuration de l’hôte sont également incluses dans la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1005">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="db9ac-1006">Pour plus d’informations sur l’utilisation des fournisseurs de configuration lors de la génération de l’hôte et l’impact des sources de configuration sur la configuration de l’hôte, consultez <xref:fundamentals/index#host>.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1006">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="db9ac-1007">Autre configuration</span><span class="sxs-lookup"><span data-stu-id="db9ac-1007">Other configuration</span></span>

<span data-ttu-id="db9ac-1008">Cette rubrique se rapporte uniquement à la configuration de l' *application*.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1008">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="db9ac-1009">D’autres aspects de l’exécution et de l’hébergement des applications ASP.NET Core sont configurés à l’aide des fichiers de configuration non traités dans cette rubrique :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1009">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="db9ac-1010">*Launch. JSON* / *launchSettings. JSON* sont des fichiers de configuration d’outils pour l’environnement de développement, décrits ci-après :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1010">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="db9ac-1011">Dans <xref:fundamentals/environments#development> .</span><span class="sxs-lookup"><span data-stu-id="db9ac-1011">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="db9ac-1012">Dans l’ensemble de la documentation dans lequel les fichiers sont utilisés pour configurer des applications ASP.NET Core pour les scénarios de développement.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1012">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="db9ac-1013">*Web. config* est un fichier de configuration de serveur, décrit dans les rubriques suivantes :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1013">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="db9ac-1014">Pour plus d’informations sur la migration de la configuration d’application à partir de versions antérieures de ASP.NET, consultez <xref:migration/proper-to-2x/index#store-configurations> .</span><span class="sxs-lookup"><span data-stu-id="db9ac-1014">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="db9ac-1015">Configuration par défaut</span><span class="sxs-lookup"><span data-stu-id="db9ac-1015">Default configuration</span></span>

<span data-ttu-id="db9ac-1016">Les applications web basées sur les modèles ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) appellent <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> pendant la création d’un hôte.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1016">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="db9ac-1017">`CreateDefaultBuilder` fournit la configuration par défaut de l’application dans l’ordre suivant :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1017">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="db9ac-1018">Les éléments suivants s’appliquent aux applications qui utilisent l’[hôte web](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="db9ac-1018">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="db9ac-1019">Pour plus de détails sur la configuration par défaut lors de l’utilisation de l’[hôte générique](xref:fundamentals/host/generic-host), consultez la [dernière version de cette rubrique](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="db9ac-1019">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="db9ac-1020">La configuration de l’hôte est fournie à partir des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1020">Host configuration is provided from:</span></span>
  * <span data-ttu-id="db9ac-1021">Des variables d’environnement préfixées avec `ASPNETCORE_` (par exemple, `ASPNETCORE_ENVIRONMENT`) à l’aide du [Fournisseur de configuration des variables d’environnement](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="db9ac-1021">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="db9ac-1022">Le préfixe (`ASPNETCORE_`) est supprimé lorsque les paires clé-valeur de la configuration sont chargées.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1022">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="db9ac-1023">Des arguments de ligne de commande à l’aide du [Fournisseur de configuration de ligne de commande](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="db9ac-1023">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="db9ac-1024">La configuration de l’application est fournie à partir des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1024">App configuration is provided from:</span></span>
  * <span data-ttu-id="db9ac-1025">*appSettings.JSON* à l’aide du [Fournisseur de configuration de fichier](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="db9ac-1025">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="db9ac-1026">*appsettings.{Environment}.json* à l’aide du [Fournisseur de configuration de fichier](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="db9ac-1026">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="db9ac-1027">L’outil [Secret Manager (Gestionnaire de secrets)](xref:security/app-secrets) quand l’application s’exécute dans l’environnement `Development` à l’aide de l’assembly d’entrée.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1027">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="db9ac-1028">Des variables d’environnement à l’aide du [Fournisseur de configuration des variables d’environnement](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="db9ac-1028">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="db9ac-1029">Des arguments de ligne de commande à l’aide du [Fournisseur de configuration de ligne de commande](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="db9ac-1029">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="db9ac-1030">Sécurité</span><span class="sxs-lookup"><span data-stu-id="db9ac-1030">Security</span></span>

<span data-ttu-id="db9ac-1031">Adoptez les pratiques suivantes pour sécuriser les données de configuration sensibles :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1031">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="db9ac-1032">Ne stockez jamais des mots de passe ou d’autres données sensibles dans le code du fournisseur de configuration ou dans les fichiers de configuration en texte clair.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1032">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="db9ac-1033">N’utilisez aucun secret de production dans les environnements de développement ou de test.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1033">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="db9ac-1034">Spécifiez les secrets en dehors du projet afin qu’ils ne puissent pas être validés par inadvertance dans un référentiel de code source.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1034">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="db9ac-1035">Pour plus d'informations, voir les rubriques suivantes :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1035">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="db9ac-1036"><xref:security/app-secrets>&ndash;Fournit des conseils sur l’utilisation de variables d’environnement pour stocker des données sensibles.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1036"><xref:security/app-secrets> &ndash; Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="db9ac-1037">Secret Manager utilise le fournisseur de configuration de fichier pour stocker les secrets utilisateur dans un fichier JSON sur le système local.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1037">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="db9ac-1038">Le fournisseur de configuration de fichier est décrit plus loin dans cette rubrique.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1038">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="db9ac-1039">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) stocke en toute sécurité des secrets d’application pour les applications ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1039">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="db9ac-1040">Pour plus d'informations, consultez <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1040">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="db9ac-1041">Données de configuration hiérarchiques</span><span class="sxs-lookup"><span data-stu-id="db9ac-1041">Hierarchical configuration data</span></span>

<span data-ttu-id="db9ac-1042">L’API Configuration est capable de maintenir des données de configuration hiérarchiques en aplanissant les données hiérarchiques à l’aide d’un délimiteur dans les clés de configuration.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1042">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="db9ac-1043">Dans le fichier JSON suivant, quatre clés existent dans une structure hiérarchique à deux sections :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1043">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

```json
{
  "section0": {
    "key0": "value",
    "key1": "value"
  },
  "section1": {
    "key0": "value",
    "key1": "value"
  }
}
```

<span data-ttu-id="db9ac-1044">Lorsque le fichier est lu dans la configuration, des clés uniques sont créées pour gérer la structure des données hiérarchiques d’origine de la source de configuration.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1044">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="db9ac-1045">Les sections et les clés sont aplanies à l’aide d’un signe deux-points (`:`) pour conserver la structure d’origine :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1045">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="db9ac-1046">section0:key0</span><span class="sxs-lookup"><span data-stu-id="db9ac-1046">section0:key0</span></span>
* <span data-ttu-id="db9ac-1047">section0:key1</span><span class="sxs-lookup"><span data-stu-id="db9ac-1047">section0:key1</span></span>
* <span data-ttu-id="db9ac-1048">section1:key0</span><span class="sxs-lookup"><span data-stu-id="db9ac-1048">section1:key0</span></span>
* <span data-ttu-id="db9ac-1049">section1:key1</span><span class="sxs-lookup"><span data-stu-id="db9ac-1049">section1:key1</span></span>

<span data-ttu-id="db9ac-1050">Les méthodes <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> et <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> sont disponibles pour isoler les sections et les enfants d’une section dans les données de configuration.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1050"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="db9ac-1051">Ces méthodes sont décrites plus loin dans [GetSection, GetChildren et Exists](#getsection-getchildren-and-exists).</span><span class="sxs-lookup"><span data-stu-id="db9ac-1051">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="db9ac-1052">Conventions</span><span class="sxs-lookup"><span data-stu-id="db9ac-1052">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="db9ac-1053">Sources et fournisseurs</span><span class="sxs-lookup"><span data-stu-id="db9ac-1053">Sources and providers</span></span>

<span data-ttu-id="db9ac-1054">Au démarrage de l’application, les sources de configuration sont lues dans l’ordre où leurs fournisseurs de configuration sont spécifiés.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1054">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="db9ac-1055">Les fournisseurs de configuration qui implémentent la détection des modifications peuvent recharger la configuration lorsqu’un paramètre sous-jacent est modifié.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1055">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="db9ac-1056">Par exemple, le fournisseur de configuration de fichier (décrit plus loin dans cette rubrique) et le [fournisseur de configuration Azure Key Vault](xref:security/key-vault-configuration) implémentent la détection des modifications.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1056">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="db9ac-1057"><xref:Microsoft.Extensions.Configuration.IConfiguration> est disponible dans le conteneur d’[injection de dépendances](xref:fundamentals/dependency-injection) de l’application.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1057"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="db9ac-1058"><xref:Microsoft.Extensions.Configuration.IConfiguration>peut être injecté dans une Razor page <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> ou un MVC <xref:Microsoft.AspNetCore.Mvc.Controller> pour obtenir la configuration de la classe.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1058"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="db9ac-1059">Dans les exemples suivants, le `_config` champ est utilisé pour accéder aux valeurs de configuration :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1059">In the following examples, the `_config` field is used to access configuration values:</span></span>

```csharp
public class IndexModel : PageModel
{
    private readonly IConfiguration _config;

    public IndexModel(IConfiguration config)
    {
        _config = config;
    }
}
```

```csharp
public class HomeController : Controller
{
    private readonly IConfiguration _config;

    public HomeController(IConfiguration config)
    {
        _config = config;
    }
}
```

<span data-ttu-id="db9ac-1060">Les fournisseurs de configuration ne peuvent pas utiliser le DI, car celui-ci n’est pas disponible lorsque les fournisseurs sont configurés par l’hôte.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1060">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="db9ac-1061">Keys</span><span class="sxs-lookup"><span data-stu-id="db9ac-1061">Keys</span></span>

<span data-ttu-id="db9ac-1062">Les clés de configuration adoptent les conventions suivantes :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1062">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="db9ac-1063">Les clés ne respectent pas la casse.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1063">Keys are case-insensitive.</span></span> <span data-ttu-id="db9ac-1064">Par exemple, `ConnectionString` et `connectionstring` sont traités en tant que clés équivalentes.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1064">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="db9ac-1065">Si une valeur pour la même clé est définie par des fournisseurs de configuration identiques ou différents, la valeur utilisée est la dernière valeur définie sur la clé.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1065">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span>
* <span data-ttu-id="db9ac-1066">Clés hiérarchiques</span><span class="sxs-lookup"><span data-stu-id="db9ac-1066">Hierarchical keys</span></span>
  * <span data-ttu-id="db9ac-1067">Dans l’API Configuration, un séparateur sous forme de signe deux-points (`:`) fonctionne sur toutes les plateformes.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1067">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="db9ac-1068">Dans les variables d’environnement, un séparateur sous forme de signe deux-points peut ne pas fonctionner sur toutes les plateformes.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1068">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="db9ac-1069">Un trait de soulignement double (`__`) est pris en charge par toutes les plateformes et automatiquement transformé en signe deux-points.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1069">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="db9ac-1070">Dans Azure Key Vault, les clés hiérarchiques utilisent `--` (deux tirets) comme séparateur.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1070">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="db9ac-1071">Écrivez du code pour remplacer les tirets par un signe deux-points lorsque les secrets sont chargés dans la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1071">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="db9ac-1072"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> prend en charge la liaison de tableaux à des objets à l’aide d’index de tableau dans les clés de configuration.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1072">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="db9ac-1073">La liaison de tableau est décrite dans la section [Lier un tableau à une classe](#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="db9ac-1073">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="db9ac-1074">Valeurs</span><span class="sxs-lookup"><span data-stu-id="db9ac-1074">Values</span></span>

<span data-ttu-id="db9ac-1075">Les valeurs de configuration adoptent les conventions suivantes :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1075">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="db9ac-1076">Les valeurs sont des chaînes.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1076">Values are strings.</span></span>
* <span data-ttu-id="db9ac-1077">Les valeurs NULL ne peuvent pas être stockées dans la configuration ou liées à des objets.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1077">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="db9ac-1078">Fournisseurs</span><span class="sxs-lookup"><span data-stu-id="db9ac-1078">Providers</span></span>

<span data-ttu-id="db9ac-1079">Le tableau suivant présente les fournisseurs de configuration disponibles pour les applications ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1079">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="db9ac-1080">Fournisseur</span><span class="sxs-lookup"><span data-stu-id="db9ac-1080">Provider</span></span> | <span data-ttu-id="db9ac-1081">Fournit la configuration à partir de&hellip;</span><span class="sxs-lookup"><span data-stu-id="db9ac-1081">Provides configuration from&hellip;</span></span> |
| ---
<span data-ttu-id="db9ac-1082">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1082">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1083">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1083">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1084">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1084">'Identity'</span></span>
- <span data-ttu-id="db9ac-1085">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1085">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1086">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1086">'Razor'</span></span>
- <span data-ttu-id="db9ac-1087">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1087">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1088">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1088">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1089">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1089">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1090">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1090">'Identity'</span></span>
- <span data-ttu-id="db9ac-1091">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1091">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1092">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1092">'Razor'</span></span>
- <span data-ttu-id="db9ac-1093">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1093">'SignalR' uid:</span></span> 

<span data-ttu-id="db9ac-1094">---- | titre de--- : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1094">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1095">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1095">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1096">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1096">'Identity'</span></span>
- <span data-ttu-id="db9ac-1097">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1097">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1098">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1098">'Razor'</span></span>
- <span data-ttu-id="db9ac-1099">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1099">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1100">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1100">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1101">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1101">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1102">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1102">'Identity'</span></span>
- <span data-ttu-id="db9ac-1103">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1103">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1104">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1104">'Razor'</span></span>
- <span data-ttu-id="db9ac-1105">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1105">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1106">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1106">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1107">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1107">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1108">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1108">'Identity'</span></span>
- <span data-ttu-id="db9ac-1109">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1109">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1110">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1110">'Razor'</span></span>
- <span data-ttu-id="db9ac-1111">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1111">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1112">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1112">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1113">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1113">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1114">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1114">'Identity'</span></span>
- <span data-ttu-id="db9ac-1115">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1115">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1116">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1116">'Razor'</span></span>
- <span data-ttu-id="db9ac-1117">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1117">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1118">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1118">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1119">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1119">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1120">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1120">'Identity'</span></span>
- <span data-ttu-id="db9ac-1121">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1121">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1122">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1122">'Razor'</span></span>
- <span data-ttu-id="db9ac-1123">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1123">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1124">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1124">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1125">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1125">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1126">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1126">'Identity'</span></span>
- <span data-ttu-id="db9ac-1127">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1127">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1128">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1128">'Razor'</span></span>
- <span data-ttu-id="db9ac-1129">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1129">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1130">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1130">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1131">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1131">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1132">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1132">'Identity'</span></span>
- <span data-ttu-id="db9ac-1133">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1133">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1134">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1134">'Razor'</span></span>
- <span data-ttu-id="db9ac-1135">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1135">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1136">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1136">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1137">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1137">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1138">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1138">'Identity'</span></span>
- <span data-ttu-id="db9ac-1139">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1139">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1140">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1140">'Razor'</span></span>
- <span data-ttu-id="db9ac-1141">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1141">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1142">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1142">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1143">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1143">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1144">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1144">'Identity'</span></span>
- <span data-ttu-id="db9ac-1145">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1145">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1146">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1146">'Razor'</span></span>
- <span data-ttu-id="db9ac-1147">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1147">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1148">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1148">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1149">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1149">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1150">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1150">'Identity'</span></span>
- <span data-ttu-id="db9ac-1151">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1151">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1152">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1152">'Razor'</span></span>
- <span data-ttu-id="db9ac-1153">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1153">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1154">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1154">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1155">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1155">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1156">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1156">'Identity'</span></span>
- <span data-ttu-id="db9ac-1157">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1157">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1158">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1158">'Razor'</span></span>
- <span data-ttu-id="db9ac-1159">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1159">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1160">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1160">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1161">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1161">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1162">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1162">'Identity'</span></span>
- <span data-ttu-id="db9ac-1163">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1163">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1164">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1164">'Razor'</span></span>
- <span data-ttu-id="db9ac-1165">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1165">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1166">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1166">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1167">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1167">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1168">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1168">'Identity'</span></span>
- <span data-ttu-id="db9ac-1169">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1169">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1170">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1170">'Razor'</span></span>
- <span data-ttu-id="db9ac-1171">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1171">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1172">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1172">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1173">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1173">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1174">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1174">'Identity'</span></span>
- <span data-ttu-id="db9ac-1175">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1175">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1176">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1176">'Razor'</span></span>
- <span data-ttu-id="db9ac-1177">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1177">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1178">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1178">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1179">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1179">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1180">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1180">'Identity'</span></span>
- <span data-ttu-id="db9ac-1181">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1181">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1182">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1182">'Razor'</span></span>
- <span data-ttu-id="db9ac-1183">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1183">'SignalR' uid:</span></span> 

<span data-ttu-id="db9ac-1184">------------------ | | [Fournisseur de configuration Azure Key Vault](xref:security/key-vault-configuration) (rubriques de*sécurité* ) | Azure Key Vault | | [Fournisseur de configuration Azure App](/azure/azure-app-configuration/quickstart-aspnet-core-app) (documentation Azure) | Configuration de Azure App | | [Fournisseur de configuration de ligne de commande](#command-line-configuration-provider) | Paramètres de ligne de commande | | [Fournisseur de configuration personnalisée](#custom-configuration-provider) | Source personnalisée | | [Fournisseur de configuration des variables d’environnement](#environment-variables-configuration-provider) | Variables d’environnement | | [Fournisseur de configuration de fichier](#file-configuration-provider) | Fichiers (INI, JSON, XML) | | [Fournisseur de configuration de clé par fichier](#key-per-file-configuration-provider) | Fichiers de répertoire | | [Fournisseur de configuration](#memory-configuration-provider) de la mémoire | Collections en mémoire | Secrets de l' | [utilisateur (gestionnaire de secret)](xref:security/app-secrets) (rubriques de*sécurité* ) | Fichier dans le répertoire du profil utilisateur |</span><span class="sxs-lookup"><span data-stu-id="db9ac-1184">------------------ | | [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics) | Azure Key Vault | | [Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation) | Azure App Configuration | | [Command-line Configuration Provider](#command-line-configuration-provider) | Command-line parameters | | [Custom configuration provider](#custom-configuration-provider) | Custom source | | [Environment Variables Configuration Provider](#environment-variables-configuration-provider) | Environment variables | | [File Configuration Provider](#file-configuration-provider) | Files (INI, JSON, XML) | | [Key-per-file Configuration Provider](#key-per-file-configuration-provider) | Directory files | | [Memory Configuration Provider](#memory-configuration-provider) | In-memory collections | | [User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics) | File in the user profile directory |</span></span>

<span data-ttu-id="db9ac-1185">Au démarrage, les sources de configuration sont lues dans l’ordre où leurs fournisseurs de configuration sont spécifiés.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1185">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="db9ac-1186">Les fournisseurs de configuration décrits dans cette rubrique sont décrits par ordre alphabétique, et non pas dans l’ordre dans lequel le code les réorganise.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1186">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="db9ac-1187">Commandez des fournisseurs de configuration dans le code pour répondre aux priorités des sources de configuration sous-jacentes requises par l’application.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1187">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="db9ac-1188">Une séquence type des fournisseurs de configuration est la suivante :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1188">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="db9ac-1189">Fichiers (*appsettings.json*, *appsettings.{Environment}.json*, où `{Environment}` est l'environnement d’hébergement actuel de l'application)</span><span class="sxs-lookup"><span data-stu-id="db9ac-1189">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="db9ac-1190">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="db9ac-1190">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="db9ac-1191">[Secrets utilisateur (Secret Manager)](xref:security/app-secrets) (dans l’environnement de développement uniquement)</span><span class="sxs-lookup"><span data-stu-id="db9ac-1191">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="db9ac-1192">Variables d'environnement</span><span class="sxs-lookup"><span data-stu-id="db9ac-1192">Environment variables</span></span>
1. <span data-ttu-id="db9ac-1193">Arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="db9ac-1193">Command-line arguments</span></span>

<span data-ttu-id="db9ac-1194">Une pratique courante consiste à placer le Fournisseur de configuration de ligne de commande en dernier dans une série de fournisseurs pour permettre aux arguments de ligne de commande de remplacer la configuration définie par les autres fournisseurs.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1194">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="db9ac-1195">La séquence de fournisseurs précédente est utilisée lors de l’initialisation d’un nouveau générateur d’hôte `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="db9ac-1195">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="db9ac-1196">Pour plus d’informations, consultez la section [Configuration par défaut](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="db9ac-1196">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="db9ac-1197">Configurer le générateur d’ordinateur hôte avec UseConfiguration</span><span class="sxs-lookup"><span data-stu-id="db9ac-1197">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="db9ac-1198">Pour configurer le générateur d’ordinateur hôte, appelez <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> sur le générateur d’hôte avec la configuration.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1198">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args)
{
    var dict = new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };

    var config = new ConfigurationBuilder()
        .AddInMemoryCollection(dict)
        .Build();

    return WebHost.CreateDefaultBuilder(args)
        .UseConfiguration(config)
        .UseStartup<Startup>();
}
```

## <a name="configureappconfiguration"></a><span data-ttu-id="db9ac-1199">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="db9ac-1199">ConfigureAppConfiguration</span></span>

<span data-ttu-id="db9ac-1200">Appelez `ConfigureAppConfiguration` quand vous créez l’hôte pour spécifier les fournisseurs de configuration de l’application en plus de ceux ajoutés automatiquement par `CreateDefaultBuilder` :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1200">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="db9ac-1201">Remplacez la configuration précédente par des arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="db9ac-1201">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="db9ac-1202">Pour fournir une configuration d’application pouvant être remplacée par des arguments de ligne de commande, appelez `AddCommandLine` en dernier :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1202">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="db9ac-1203">Supprimer les fournisseurs ajoutés par CreateDefaultBuilder</span><span class="sxs-lookup"><span data-stu-id="db9ac-1203">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="db9ac-1204">Pour supprimer les fournisseurs ajoutés par `CreateDefaultBuilder` , appelez d’abord [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) sur [IConfigurationBuilder. sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1204">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="db9ac-1205">Utiliser la configuration lors du démarrage de l’application</span><span class="sxs-lookup"><span data-stu-id="db9ac-1205">Consume configuration during app startup</span></span>

<span data-ttu-id="db9ac-1206">La configuration fournie à l’application dans `ConfigureAppConfiguration` est disponible lors du démarrage de l’application, notamment `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1206">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="db9ac-1207">Pour plus d’informations, consultez la section [Accéder à la configuration lors du démarrage](#access-configuration-during-startup).</span><span class="sxs-lookup"><span data-stu-id="db9ac-1207">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="db9ac-1208">Fournisseur de configuration de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="db9ac-1208">Command-line Configuration Provider</span></span>

<span data-ttu-id="db9ac-1209"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> charge la configuration à partir des paires clé-valeur de l’argument de ligne de commande lors de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1209">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="db9ac-1210">Pour activer la configuration en ligne de commande, la méthode d’extension <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> est appelée sur une instance de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1210">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="db9ac-1211">`AddCommandLine` est appelé automatiquement quand `CreateDefaultBuilder(string [])` est appelé.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1211">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="db9ac-1212">Pour plus d’informations, consultez la section [Configuration par défaut](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="db9ac-1212">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="db9ac-1213">`CreateDefaultBuilder` charge également :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1213">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="db9ac-1214">Configuration facultative à partir des fichiers *appsettings.json* et *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1214">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="db9ac-1215">[Secrets utilisateur (Secret Manager)](xref:security/app-secrets) dans l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1215">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="db9ac-1216">Variables d'environnement.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1216">Environment variables.</span></span>

<span data-ttu-id="db9ac-1217">`CreateDefaultBuilder` ajoute en dernier le Fournisseur de configuration de ligne de commande.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1217">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="db9ac-1218">Les arguments de ligne de commande passés lors de l’exécution remplacent la configuration définie par les autres fournisseurs.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1218">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="db9ac-1219">`CreateDefaultBuilder` agit lors de la construction de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1219">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="db9ac-1220">Par conséquent, la configuration de ligne de commande activée par `CreateDefaultBuilder` peut affecter la façon dont l’hôte est configuré.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1220">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="db9ac-1221">Pour les applications basées sur les modèles ASP.NET Core, `AddCommandLine` a déjà été appelé par `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1221">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="db9ac-1222">Pour ajouter des fournisseurs de configuration supplémentaires et conserver la capacité de remplacer leur configuration par des arguments de ligne de commande, appelez les fournisseurs supplémentaires de l’application dans `ConfigureAppConfiguration` et appelez `AddCommandLine` en dernier.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1222">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="db9ac-1223">**Exemple**</span><span class="sxs-lookup"><span data-stu-id="db9ac-1223">**Example**</span></span>

<span data-ttu-id="db9ac-1224">L’exemple d’application tire parti de la méthode pratique statique `CreateDefaultBuilder` pour générer l’hôte, qui inclut un appel à <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1224">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="db9ac-1225">Ouvrez une invite de commandes dans le répertoire du projet.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1225">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="db9ac-1226">Fournissez un argument de ligne de commande à la commande `dotnet run`, `dotnet run CommandLineKey=CommandLineValue`.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1226">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="db9ac-1227">Une fois que l’application est en cours d’exécution, ouvrez un navigateur à l’application à l’adresse `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1227">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="db9ac-1228">Notez que la sortie contient la paire clé-valeur pour l’argument de ligne de commande de configuration fourni à `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1228">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="db9ac-1229">Arguments</span><span class="sxs-lookup"><span data-stu-id="db9ac-1229">Arguments</span></span>

<span data-ttu-id="db9ac-1230">La valeur doit suivre un signe égal (`=`) ou la clé doit avoir un préfixe (`--` ou `/`) lorsque la valeur suit un espace.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1230">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="db9ac-1231">La valeur n’est pas requise si un signe égal est utilisé (par exemple, `CommandLineKey=`).</span><span class="sxs-lookup"><span data-stu-id="db9ac-1231">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="db9ac-1232">Préfixe de clé</span><span class="sxs-lookup"><span data-stu-id="db9ac-1232">Key prefix</span></span>               | <span data-ttu-id="db9ac-1233">Exemple</span><span class="sxs-lookup"><span data-stu-id="db9ac-1233">Example</span></span>                                                |
| ---
<span data-ttu-id="db9ac-1234">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1234">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1235">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1235">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1236">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1236">'Identity'</span></span>
- <span data-ttu-id="db9ac-1237">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1237">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1238">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1238">'Razor'</span></span>
- <span data-ttu-id="db9ac-1239">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1239">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1240">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1240">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1241">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1241">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1242">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1242">'Identity'</span></span>
- <span data-ttu-id="db9ac-1243">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1243">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1244">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1244">'Razor'</span></span>
- <span data-ttu-id="db9ac-1245">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1245">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1246">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1246">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1247">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1247">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1248">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1248">'Identity'</span></span>
- <span data-ttu-id="db9ac-1249">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1249">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1250">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1250">'Razor'</span></span>
- <span data-ttu-id="db9ac-1251">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1251">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1252">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1252">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1253">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1253">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1254">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1254">'Identity'</span></span>
- <span data-ttu-id="db9ac-1255">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1255">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1256">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1256">'Razor'</span></span>
- <span data-ttu-id="db9ac-1257">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1257">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1258">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1258">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1259">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1259">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1260">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1260">'Identity'</span></span>
- <span data-ttu-id="db9ac-1261">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1261">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1262">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1262">'Razor'</span></span>
- <span data-ttu-id="db9ac-1263">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1263">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1264">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1264">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1265">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1265">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1266">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1266">'Identity'</span></span>
- <span data-ttu-id="db9ac-1267">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1267">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1268">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1268">'Razor'</span></span>
- <span data-ttu-id="db9ac-1269">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1269">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1270">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1270">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1271">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1271">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1272">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1272">'Identity'</span></span>
- <span data-ttu-id="db9ac-1273">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1273">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1274">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1274">'Razor'</span></span>
- <span data-ttu-id="db9ac-1275">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1275">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1276">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1276">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1277">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1277">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1278">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1278">'Identity'</span></span>
- <span data-ttu-id="db9ac-1279">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1279">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1280">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1280">'Razor'</span></span>
- <span data-ttu-id="db9ac-1281">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1281">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1282">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1282">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1283">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1283">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1284">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1284">'Identity'</span></span>
- <span data-ttu-id="db9ac-1285">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1285">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1286">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1286">'Razor'</span></span>
- <span data-ttu-id="db9ac-1287">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1287">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1288">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1288">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1289">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1289">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1290">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1290">'Identity'</span></span>
- <span data-ttu-id="db9ac-1291">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1291">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1292">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1292">'Razor'</span></span>
- <span data-ttu-id="db9ac-1293">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1293">'SignalR' uid:</span></span> 

<span data-ttu-id="db9ac-1294">------------ | titre de--- : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1294">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1295">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1295">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1296">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1296">'Identity'</span></span>
- <span data-ttu-id="db9ac-1297">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1297">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1298">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1298">'Razor'</span></span>
- <span data-ttu-id="db9ac-1299">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1299">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1300">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1300">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1301">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1301">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1302">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1302">'Identity'</span></span>
- <span data-ttu-id="db9ac-1303">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1303">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1304">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1304">'Razor'</span></span>
- <span data-ttu-id="db9ac-1305">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1305">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1306">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1306">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1307">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1307">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1308">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1308">'Identity'</span></span>
- <span data-ttu-id="db9ac-1309">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1309">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1310">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1310">'Razor'</span></span>
- <span data-ttu-id="db9ac-1311">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1311">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1312">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1312">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1313">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1313">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1314">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1314">'Identity'</span></span>
- <span data-ttu-id="db9ac-1315">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1315">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1316">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1316">'Razor'</span></span>
- <span data-ttu-id="db9ac-1317">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1317">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1318">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1318">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1319">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1319">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1320">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1320">'Identity'</span></span>
- <span data-ttu-id="db9ac-1321">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1321">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1322">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1322">'Razor'</span></span>
- <span data-ttu-id="db9ac-1323">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1323">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1324">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1324">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1325">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1325">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1326">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1326">'Identity'</span></span>
- <span data-ttu-id="db9ac-1327">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1327">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1328">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1328">'Razor'</span></span>
- <span data-ttu-id="db9ac-1329">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1329">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1330">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1330">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1331">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1331">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1332">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1332">'Identity'</span></span>
- <span data-ttu-id="db9ac-1333">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1333">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1334">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1334">'Razor'</span></span>
- <span data-ttu-id="db9ac-1335">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1335">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1336">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1336">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1337">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1337">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1338">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1338">'Identity'</span></span>
- <span data-ttu-id="db9ac-1339">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1339">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1340">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1340">'Razor'</span></span>
- <span data-ttu-id="db9ac-1341">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1341">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1342">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1342">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1343">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1343">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1344">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1344">'Identity'</span></span>
- <span data-ttu-id="db9ac-1345">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1345">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1346">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1346">'Razor'</span></span>
- <span data-ttu-id="db9ac-1347">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1347">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1348">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1348">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1349">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1349">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1350">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1350">'Identity'</span></span>
- <span data-ttu-id="db9ac-1351">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1351">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1352">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1352">'Razor'</span></span>
- <span data-ttu-id="db9ac-1353">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1353">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1354">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1354">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1355">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1355">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1356">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1356">'Identity'</span></span>
- <span data-ttu-id="db9ac-1357">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1357">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1358">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1358">'Razor'</span></span>
- <span data-ttu-id="db9ac-1359">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1359">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1360">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1360">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1361">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1361">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1362">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1362">'Identity'</span></span>
- <span data-ttu-id="db9ac-1363">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1363">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1364">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1364">'Razor'</span></span>
- <span data-ttu-id="db9ac-1365">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1365">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1366">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1366">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1367">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1367">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1368">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1368">'Identity'</span></span>
- <span data-ttu-id="db9ac-1369">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1369">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1370">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1370">'Razor'</span></span>
- <span data-ttu-id="db9ac-1371">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1371">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1372">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1372">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1373">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1373">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1374">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1374">'Identity'</span></span>
- <span data-ttu-id="db9ac-1375">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1375">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1376">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1376">'Razor'</span></span>
- <span data-ttu-id="db9ac-1377">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1377">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1378">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1378">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1379">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1379">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1380">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1380">'Identity'</span></span>
- <span data-ttu-id="db9ac-1381">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1381">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1382">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1382">'Razor'</span></span>
- <span data-ttu-id="db9ac-1383">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1383">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1384">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1384">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1385">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1385">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1386">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1386">'Identity'</span></span>
- <span data-ttu-id="db9ac-1387">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1387">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1388">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1388">'Razor'</span></span>
- <span data-ttu-id="db9ac-1389">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1389">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1390">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1390">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1391">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1391">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1392">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1392">'Identity'</span></span>
- <span data-ttu-id="db9ac-1393">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1393">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1394">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1394">'Razor'</span></span>
- <span data-ttu-id="db9ac-1395">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1395">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1396">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1396">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1397">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1397">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1398">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1398">'Identity'</span></span>
- <span data-ttu-id="db9ac-1399">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1399">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1400">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1400">'Razor'</span></span>
- <span data-ttu-id="db9ac-1401">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1401">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1402">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1402">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1403">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1403">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1404">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1404">'Identity'</span></span>
- <span data-ttu-id="db9ac-1405">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1405">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1406">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1406">'Razor'</span></span>
- <span data-ttu-id="db9ac-1407">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1407">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1408">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1408">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1409">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1409">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1410">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1410">'Identity'</span></span>
- <span data-ttu-id="db9ac-1411">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1411">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1412">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1412">'Razor'</span></span>
- <span data-ttu-id="db9ac-1413">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1413">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1414">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1414">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1415">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1415">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1416">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1416">'Identity'</span></span>
- <span data-ttu-id="db9ac-1417">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1417">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1418">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1418">'Razor'</span></span>
- <span data-ttu-id="db9ac-1419">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1419">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1420">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1420">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1421">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1421">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1422">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1422">'Identity'</span></span>
- <span data-ttu-id="db9ac-1423">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1423">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1424">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1424">'Razor'</span></span>
- <span data-ttu-id="db9ac-1425">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1425">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1426">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1426">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1427">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1427">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1428">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1428">'Identity'</span></span>
- <span data-ttu-id="db9ac-1429">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1429">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1430">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1430">'Razor'</span></span>
- <span data-ttu-id="db9ac-1431">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1431">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1432">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1432">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1433">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1433">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1434">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1434">'Identity'</span></span>
- <span data-ttu-id="db9ac-1435">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1435">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1436">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1436">'Razor'</span></span>
- <span data-ttu-id="db9ac-1437">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1437">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1438">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1438">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1439">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1439">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1440">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1440">'Identity'</span></span>
- <span data-ttu-id="db9ac-1441">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1441">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1442">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1442">'Razor'</span></span>
- <span data-ttu-id="db9ac-1443">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1443">'SignalR' uid:</span></span> 

<span data-ttu-id="db9ac-1444">--------------------------- | | Aucun préfixe | `CommandLineKey1=value1`                               |
| Deux tirets ( `--` ) | `--CommandLineKey2=value2` , `--CommandLineKey2 value2` |
 | Barre oblique ( `/` ) | `/CommandLineKey3=value3` ,`/CommandLineKey3 value3`   |</span><span class="sxs-lookup"><span data-stu-id="db9ac-1444">--------------------------- | | No prefix                | `CommandLineKey1=value1`                               |
| Two dashes (`--`)        | `--CommandLineKey2=value2`, `--CommandLineKey2 value2` |
| Forward slash (`/`)      | `/CommandLineKey3=value3`, `/CommandLineKey3 value3`   |</span></span>

<span data-ttu-id="db9ac-1445">Dans la même commande, ne mélangez pas des paires clé-valeur de l’argument de ligne de commande qui utilisent un signe égal avec des paires clé-valeur qui utilisent un espace.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1445">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="db9ac-1446">Exemples de commandes :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1446">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="db9ac-1447">Correspondances de commutateur</span><span class="sxs-lookup"><span data-stu-id="db9ac-1447">Switch mappings</span></span>

<span data-ttu-id="db9ac-1448">Les correspondances de commutateur permettent une logique de remplacement des noms de clés.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1448">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="db9ac-1449">Lors de la génération manuelle d’une configuration avec un <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> , fournissez un dictionnaire de remplacements de commutateur à la <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> méthode.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1449">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="db9ac-1450">Quand le dictionnaire de correspondances de commutateur est utilisé, il est vérifié afin de déterminer s’il contient une clé correspondant à celle fournie par un argument de ligne de commande.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1450">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="db9ac-1451">Si la clé de ligne de commande est trouvée dans le dictionnaire, la valeur du dictionnaire (le remplacement de la clé) est repassée pour définir la paire clé-valeur dans la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1451">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="db9ac-1452">Une correspondance de commutateur est nécessaire pour chaque clé de ligne de commande préfixée avec un tiret unique (`-`).</span><span class="sxs-lookup"><span data-stu-id="db9ac-1452">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="db9ac-1453">Règles des clés du dictionnaire de correspondances de commutateur :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1453">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="db9ac-1454">Les commutateurs doivent commencer par un tiret (`-`) ou un double tiret (`--`).</span><span class="sxs-lookup"><span data-stu-id="db9ac-1454">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="db9ac-1455">Le dictionnaire de correspondances de commutateur ne doit pas contenir de clés en double.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1455">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="db9ac-1456">Créez un dictionnaire des mappages de commutateurs.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1456">Create a switch mappings dictionary.</span></span> <span data-ttu-id="db9ac-1457">Dans l’exemple suivant, deux mappages de commutateurs sont créés :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1457">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="db9ac-1458">Lorsque l’hôte est généré, appelez `AddCommandLine` avec le dictionnaire de mappages de commutateurs :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1458">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="db9ac-1459">Pour les applications qui utilisent des mappages de commutateurs, l’appel à `CreateDefaultBuilder` ne doit pas passer d’arguments.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1459">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="db9ac-1460">L’appel `AddCommandLine` de la méthode `CreateDefaultBuilder` n’inclut pas de commutateurs mappés et il n’existe aucun moyen de transmettre le dictionnaire de correspondance de commutateur à `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1460">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="db9ac-1461">La solution ne consiste pas à transmettre les arguments à `CreateDefaultBuilder`, mais plutôt à permettre à la méthode `AddCommandLine` de la méthode `ConfigurationBuilder` de traiter les arguments et le dictionnaire de mappage de commutateur.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1461">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="db9ac-1462">Une fois le dictionnaire de correspondances de commutateur créé, il contient les données affichées dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1462">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="db9ac-1463">Clé</span><span class="sxs-lookup"><span data-stu-id="db9ac-1463">Key</span></span>       | <span data-ttu-id="db9ac-1464">Valeur</span><span class="sxs-lookup"><span data-stu-id="db9ac-1464">Value</span></span>             |
| ---
<span data-ttu-id="db9ac-1465">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1466">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1466">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1467">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1467">'Identity'</span></span>
- <span data-ttu-id="db9ac-1468">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1468">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1469">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1469">'Razor'</span></span>
- <span data-ttu-id="db9ac-1470">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1470">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1471">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1472">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1472">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1473">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1473">'Identity'</span></span>
- <span data-ttu-id="db9ac-1474">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1474">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1475">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1475">'Razor'</span></span>
- <span data-ttu-id="db9ac-1476">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1476">'SignalR' uid:</span></span> 

<span data-ttu-id="db9ac-1477">----- | titre de--- : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1477">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1478">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1478">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1479">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1479">'Identity'</span></span>
- <span data-ttu-id="db9ac-1480">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1480">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1481">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1481">'Razor'</span></span>
- <span data-ttu-id="db9ac-1482">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1482">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1483">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1484">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1484">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1485">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1485">'Identity'</span></span>
- <span data-ttu-id="db9ac-1486">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1486">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1487">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1487">'Razor'</span></span>
- <span data-ttu-id="db9ac-1488">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1488">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1489">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1490">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1490">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1491">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1491">'Identity'</span></span>
- <span data-ttu-id="db9ac-1492">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1492">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1493">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1493">'Razor'</span></span>
- <span data-ttu-id="db9ac-1494">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1494">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1495">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1495">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1496">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1496">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1497">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1497">'Identity'</span></span>
- <span data-ttu-id="db9ac-1498">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1498">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1499">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1499">'Razor'</span></span>
- <span data-ttu-id="db9ac-1500">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1500">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1501">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1502">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1502">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1503">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1503">'Identity'</span></span>
- <span data-ttu-id="db9ac-1504">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1504">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1505">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1505">'Razor'</span></span>
- <span data-ttu-id="db9ac-1506">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1506">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1507">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1507">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1508">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1508">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1509">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1509">'Identity'</span></span>
- <span data-ttu-id="db9ac-1510">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1510">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1511">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1511">'Razor'</span></span>
- <span data-ttu-id="db9ac-1512">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1512">'SignalR' uid:</span></span> 

<span data-ttu-id="db9ac-1513">--------- | | `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |</span><span class="sxs-lookup"><span data-stu-id="db9ac-1513">--------- | | `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |</span></span>

<span data-ttu-id="db9ac-1514">Si les clés mappées au commutateur sont utilisées lors du démarrage de l’application, la configuration reçoit la valeur de configuration sur la clé fournie par le dictionnaire :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1514">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="db9ac-1515">Après avoir exécuté la commande précédente, la configuration contient les valeurs indiquées dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1515">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="db9ac-1516">Clé</span><span class="sxs-lookup"><span data-stu-id="db9ac-1516">Key</span></span>               | <span data-ttu-id="db9ac-1517">Valeur</span><span class="sxs-lookup"><span data-stu-id="db9ac-1517">Value</span></span>    |
| ---
<span data-ttu-id="db9ac-1518">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1518">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1519">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1519">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1520">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1520">'Identity'</span></span>
- <span data-ttu-id="db9ac-1521">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1521">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1522">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1522">'Razor'</span></span>
- <span data-ttu-id="db9ac-1523">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1523">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1524">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1524">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1525">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1525">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1526">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1526">'Identity'</span></span>
- <span data-ttu-id="db9ac-1527">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1527">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1528">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1528">'Razor'</span></span>
- <span data-ttu-id="db9ac-1529">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1529">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1530">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1530">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1531">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1531">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1532">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1532">'Identity'</span></span>
- <span data-ttu-id="db9ac-1533">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1533">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1534">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1534">'Razor'</span></span>
- <span data-ttu-id="db9ac-1535">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1535">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1536">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1536">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1537">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1537">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1538">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1538">'Identity'</span></span>
- <span data-ttu-id="db9ac-1539">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1539">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1540">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1540">'Razor'</span></span>
- <span data-ttu-id="db9ac-1541">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1541">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1542">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1542">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1543">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1543">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1544">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1544">'Identity'</span></span>
- <span data-ttu-id="db9ac-1545">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1545">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1546">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1546">'Razor'</span></span>
- <span data-ttu-id="db9ac-1547">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1547">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1548">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1548">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1549">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1549">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1550">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1550">'Identity'</span></span>
- <span data-ttu-id="db9ac-1551">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1551">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1552">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1552">'Razor'</span></span>
- <span data-ttu-id="db9ac-1553">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1553">'SignalR' uid:</span></span> 

<span data-ttu-id="db9ac-1554">--------- | titre de--- : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1554">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1555">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1555">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1556">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1556">'Identity'</span></span>
- <span data-ttu-id="db9ac-1557">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1557">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1558">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1558">'Razor'</span></span>
- <span data-ttu-id="db9ac-1559">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1559">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1560">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1560">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1561">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1561">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1562">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1562">'Identity'</span></span>
- <span data-ttu-id="db9ac-1563">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1563">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1564">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1564">'Razor'</span></span>
- <span data-ttu-id="db9ac-1565">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1565">'SignalR' uid:</span></span> 

<span data-ttu-id="db9ac-1566">---- | | `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |</span><span class="sxs-lookup"><span data-stu-id="db9ac-1566">---- | | `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |</span></span>

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="db9ac-1567">Fournisseur de configuration de variables d’environnement</span><span class="sxs-lookup"><span data-stu-id="db9ac-1567">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="db9ac-1568"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> charge la configuration à partir des paires clé-valeur de la variable d’environnement lors de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1568">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="db9ac-1569">Pour activer la configuration des variables d’environnement, appelez la méthode d’extension <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> sur une instance de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1569">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="db9ac-1570">[Azure App service](https://azure.microsoft.com/services/app-service/) permet de définir des variables d’environnement dans le portail Azure qui peuvent remplacer la configuration de l’application à l’aide du fournisseur de configuration des variables d’environnement.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1570">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="db9ac-1571">Pour plus d’informations, consultez [Azure Apps : remplacer la configuration de l’application à l’aide du portail Azure](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="db9ac-1571">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="db9ac-1572">`AddEnvironmentVariables` sert à charger les variables d’environnement préfixées avec `ASPNETCORE_` pour la [configuration hôte](#host-versus-app-configuration) lorsqu’un nouveau générateur d’hôte est initialisé avec l’[hôte web](xref:fundamentals/host/web-host) et que `CreateDefaultBuilder` est appelé.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1572">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="db9ac-1573">Pour plus d’informations, consultez la section [Configuration par défaut](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="db9ac-1573">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="db9ac-1574">`CreateDefaultBuilder` charge également :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1574">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="db9ac-1575">Configuration de l’application à partir de variables d’environnement sans préfixe en appelant `AddEnvironmentVariables` sans préfixe.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1575">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="db9ac-1576">Configuration facultative à partir des fichiers *appsettings.json* et *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1576">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="db9ac-1577">[Secrets utilisateur (Secret Manager)](xref:security/app-secrets) dans l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1577">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="db9ac-1578">Arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="db9ac-1578">Command-line arguments.</span></span>

<span data-ttu-id="db9ac-1579">Le fournisseur de configuration de variables d’environnement est appelé une fois que la configuration est établie à partir des secrets utilisateur et des fichiers *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1579">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="db9ac-1580">Le fait d’appeler le fournisseur ainsi permet de lire les variables d’environnement pendant l’exécution pour substituer la configuration définie par les secrets utilisateur et les fichiers *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1580">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="db9ac-1581">Pour fournir la configuration d’application à partir de variables d’environnement supplémentaires, appelez les fournisseurs supplémentaires de l’application dans `ConfigureAppConfiguration` et appelez `AddEnvironmentVariables` avec le préfixe :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1581">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="db9ac-1582">Appelez `AddEnvironmentVariables` Last pour autoriser les variables d’environnement avec le préfixe spécifié à substituer des valeurs d’autres fournisseurs.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1582">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="db9ac-1583">**Exemple**</span><span class="sxs-lookup"><span data-stu-id="db9ac-1583">**Example**</span></span>

<span data-ttu-id="db9ac-1584">L’exemple d’application tire parti de la méthode pratique statique `CreateDefaultBuilder` pour générer l’hôte, qui inclut un appel à `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1584">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="db9ac-1585">Exécutez l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1585">Run the sample app.</span></span> <span data-ttu-id="db9ac-1586">Ouvrez un navigateur vers l’application avec l’adresse `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1586">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="db9ac-1587">Notez que la sortie contient la paire clé-valeur pour la variable d’environnement `ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1587">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="db9ac-1588">La valeur reflète l’environnement dans lequel l’application est en cours d’exécution, en général `Development` lors de l’exécution locale.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1588">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="db9ac-1589">Pour que la liste des variables d’environnement restituée par l’application soit courte, l’application filtre les variables d’environnement.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1589">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="db9ac-1590">Consultez le fichier *Pages/Index.cshtml.cs* de l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1590">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="db9ac-1591">Pour exposer toutes les variables d’environnement disponibles pour l’application, remplacez `FilteredConfiguration` dans *pages/index. cshtml. cs* par ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1591">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="db9ac-1592">Préfixes</span><span class="sxs-lookup"><span data-stu-id="db9ac-1592">Prefixes</span></span>

<span data-ttu-id="db9ac-1593">Les variables d’environnement chargées dans la configuration de l’application sont filtrées lors de la spécification d’un préfixe à la `AddEnvironmentVariables` méthode.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1593">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="db9ac-1594">Par exemple, pour filtrer les variables d’environnement sur le préfixe `CUSTOM_`, fournissez le préfixe au fournisseur de configuration :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1594">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="db9ac-1595">Le préfixe est supprimé lorsque les paires clé-valeur de la configuration sont créées.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1595">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="db9ac-1596">Lorsque le générateur d’hôte est créé, la configuration de l’hôte est fournie par des variables d’environnement.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1596">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="db9ac-1597">Pour plus d’informations sur le préfixe utilisé pour ces variables d’environnement, consultez la section [Configuration par défaut](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="db9ac-1597">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="db9ac-1598">**Préfixes des chaînes de connexion**</span><span class="sxs-lookup"><span data-stu-id="db9ac-1598">**Connection string prefixes**</span></span>

<span data-ttu-id="db9ac-1599">L’API Configuration possède des règles de traitement spéciales pour quatre variables d’environnement de chaîne de connexion impliquées dans la configuration des chaînes de connexion Azure pour l’environnement de l’application.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1599">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="db9ac-1600">Les variables d’environnement avec les préfixes indiqués dans le tableau sont chargées dans l’application si aucun préfixe n’est fourni à `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="db9ac-1600">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="db9ac-1601">Préfixe de la chaîne de connexion</span><span class="sxs-lookup"><span data-stu-id="db9ac-1601">Connection string prefix</span></span> | <span data-ttu-id="db9ac-1602">Fournisseur</span><span class="sxs-lookup"><span data-stu-id="db9ac-1602">Provider</span></span> |
| ---
<span data-ttu-id="db9ac-1603">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1604">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1604">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1605">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1605">'Identity'</span></span>
- <span data-ttu-id="db9ac-1606">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1606">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1607">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1607">'Razor'</span></span>
- <span data-ttu-id="db9ac-1608">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1608">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1609">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1609">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1610">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1610">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1611">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1611">'Identity'</span></span>
- <span data-ttu-id="db9ac-1612">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1612">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1613">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1613">'Razor'</span></span>
- <span data-ttu-id="db9ac-1614">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1614">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1615">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1615">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1616">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1616">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1617">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1617">'Identity'</span></span>
- <span data-ttu-id="db9ac-1618">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1618">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1619">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1619">'Razor'</span></span>
- <span data-ttu-id="db9ac-1620">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1620">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1621">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1622">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1622">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1623">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1623">'Identity'</span></span>
- <span data-ttu-id="db9ac-1624">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1624">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1625">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1625">'Razor'</span></span>
- <span data-ttu-id="db9ac-1626">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1626">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1627">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1627">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1628">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1628">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1629">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1629">'Identity'</span></span>
- <span data-ttu-id="db9ac-1630">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1630">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1631">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1631">'Razor'</span></span>
- <span data-ttu-id="db9ac-1632">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1632">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1633">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1633">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1634">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1634">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1635">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1635">'Identity'</span></span>
- <span data-ttu-id="db9ac-1636">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1636">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1637">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1637">'Razor'</span></span>
- <span data-ttu-id="db9ac-1638">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1638">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1639">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1639">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1640">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1640">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1641">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1641">'Identity'</span></span>
- <span data-ttu-id="db9ac-1642">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1642">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1643">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1643">'Razor'</span></span>
- <span data-ttu-id="db9ac-1644">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1644">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1645">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1645">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1646">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1646">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1647">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1647">'Identity'</span></span>
- <span data-ttu-id="db9ac-1648">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1648">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1649">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1649">'Razor'</span></span>
- <span data-ttu-id="db9ac-1650">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1650">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1651">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1651">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1652">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1652">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1653">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1653">'Identity'</span></span>
- <span data-ttu-id="db9ac-1654">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1654">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1655">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1655">'Razor'</span></span>
- <span data-ttu-id="db9ac-1656">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1656">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1657">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1657">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1658">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1658">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1659">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1659">'Identity'</span></span>
- <span data-ttu-id="db9ac-1660">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1660">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1661">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1661">'Razor'</span></span>
- <span data-ttu-id="db9ac-1662">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1662">'SignalR' uid:</span></span> 

<span data-ttu-id="db9ac-1663">------------ | titre de--- : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1663">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1664">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1664">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1665">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1665">'Identity'</span></span>
- <span data-ttu-id="db9ac-1666">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1666">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1667">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1667">'Razor'</span></span>
- <span data-ttu-id="db9ac-1668">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1668">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1669">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1669">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1670">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1670">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1671">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1671">'Identity'</span></span>
- <span data-ttu-id="db9ac-1672">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1672">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1673">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1673">'Razor'</span></span>
- <span data-ttu-id="db9ac-1674">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1674">'SignalR' uid:</span></span> 

<span data-ttu-id="db9ac-1675">---- | | `CUSTOMCONNSTR_` | Fournisseur personnalisé | | `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
 MySQL | `SQLAZURECONNSTR_` | [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) |
 | `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/)|</span><span class="sxs-lookup"><span data-stu-id="db9ac-1675">---- | | `CUSTOMCONNSTR_` | Custom provider | | `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/) |</span></span>

<span data-ttu-id="db9ac-1676">Quand une variable d’environnement est découverte et chargée dans la configuration avec l’un des quatre préfixes indiqués dans le tableau :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1676">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="db9ac-1677">La clé de configuration est créée en supprimant le préfixe de la variable d’environnement et en ajoutant une section de clé de configuration (`ConnectionStrings`).</span><span class="sxs-lookup"><span data-stu-id="db9ac-1677">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="db9ac-1678">Une nouvelle paire clé-valeur de configuration est créée qui représente le fournisseur de connexion de base de données (à l’exception de `CUSTOMCONNSTR_`, qui ne possède aucun fournisseur indiqué).</span><span class="sxs-lookup"><span data-stu-id="db9ac-1678">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="db9ac-1679">Clé de variable d’environnement</span><span class="sxs-lookup"><span data-stu-id="db9ac-1679">Environment variable key</span></span> | <span data-ttu-id="db9ac-1680">Clé de configuration convertie</span><span class="sxs-lookup"><span data-stu-id="db9ac-1680">Converted configuration key</span></span> | <span data-ttu-id="db9ac-1681">Entrée de configuration de fournisseur</span><span class="sxs-lookup"><span data-stu-id="db9ac-1681">Provider configuration entry</span></span>                                                    |
| ---
<span data-ttu-id="db9ac-1682">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1682">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1683">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1683">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1684">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1684">'Identity'</span></span>
- <span data-ttu-id="db9ac-1685">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1685">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1686">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1686">'Razor'</span></span>
- <span data-ttu-id="db9ac-1687">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1687">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1688">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1688">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1689">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1689">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1690">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1690">'Identity'</span></span>
- <span data-ttu-id="db9ac-1691">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1691">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1692">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1692">'Razor'</span></span>
- <span data-ttu-id="db9ac-1693">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1693">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1694">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1694">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1695">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1695">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1696">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1696">'Identity'</span></span>
- <span data-ttu-id="db9ac-1697">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1697">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1698">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1698">'Razor'</span></span>
- <span data-ttu-id="db9ac-1699">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1699">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1700">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1700">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1701">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1701">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1702">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1702">'Identity'</span></span>
- <span data-ttu-id="db9ac-1703">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1703">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1704">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1704">'Razor'</span></span>
- <span data-ttu-id="db9ac-1705">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1705">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1706">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1706">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1707">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1707">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1708">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1708">'Identity'</span></span>
- <span data-ttu-id="db9ac-1709">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1709">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1710">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1710">'Razor'</span></span>
- <span data-ttu-id="db9ac-1711">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1711">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1712">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1712">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1713">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1713">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1714">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1714">'Identity'</span></span>
- <span data-ttu-id="db9ac-1715">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1715">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1716">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1716">'Razor'</span></span>
- <span data-ttu-id="db9ac-1717">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1717">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1718">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1718">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1719">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1719">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1720">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1720">'Identity'</span></span>
- <span data-ttu-id="db9ac-1721">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1721">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1722">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1722">'Razor'</span></span>
- <span data-ttu-id="db9ac-1723">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1723">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1724">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1724">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1725">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1725">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1726">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1726">'Identity'</span></span>
- <span data-ttu-id="db9ac-1727">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1727">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1728">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1728">'Razor'</span></span>
- <span data-ttu-id="db9ac-1729">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1729">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1730">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1730">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1731">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1731">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1732">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1732">'Identity'</span></span>
- <span data-ttu-id="db9ac-1733">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1733">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1734">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1734">'Razor'</span></span>
- <span data-ttu-id="db9ac-1735">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1735">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1736">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1736">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1737">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1737">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1738">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1738">'Identity'</span></span>
- <span data-ttu-id="db9ac-1739">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1739">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1740">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1740">'Razor'</span></span>
- <span data-ttu-id="db9ac-1741">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1741">'SignalR' uid:</span></span> 

<span data-ttu-id="db9ac-1742">------------ | titre de--- : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1742">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1743">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1743">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1744">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1744">'Identity'</span></span>
- <span data-ttu-id="db9ac-1745">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1745">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1746">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1746">'Razor'</span></span>
- <span data-ttu-id="db9ac-1747">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1747">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1748">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1748">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1749">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1749">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1750">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1750">'Identity'</span></span>
- <span data-ttu-id="db9ac-1751">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1751">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1752">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1752">'Razor'</span></span>
- <span data-ttu-id="db9ac-1753">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1753">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1754">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1754">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1755">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1755">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1756">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1756">'Identity'</span></span>
- <span data-ttu-id="db9ac-1757">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1757">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1758">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1758">'Razor'</span></span>
- <span data-ttu-id="db9ac-1759">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1759">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1760">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1760">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1761">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1761">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1762">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1762">'Identity'</span></span>
- <span data-ttu-id="db9ac-1763">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1763">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1764">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1764">'Razor'</span></span>
- <span data-ttu-id="db9ac-1765">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1765">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1766">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1766">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1767">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1767">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1768">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1768">'Identity'</span></span>
- <span data-ttu-id="db9ac-1769">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1769">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1770">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1770">'Razor'</span></span>
- <span data-ttu-id="db9ac-1771">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1771">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1772">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1772">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1773">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1773">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1774">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1774">'Identity'</span></span>
- <span data-ttu-id="db9ac-1775">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1775">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1776">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1776">'Razor'</span></span>
- <span data-ttu-id="db9ac-1777">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1777">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1778">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1778">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1779">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1779">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1780">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1780">'Identity'</span></span>
- <span data-ttu-id="db9ac-1781">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1781">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1782">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1782">'Razor'</span></span>
- <span data-ttu-id="db9ac-1783">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1783">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1784">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1784">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1785">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1785">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1786">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1786">'Identity'</span></span>
- <span data-ttu-id="db9ac-1787">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1787">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1788">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1788">'Razor'</span></span>
- <span data-ttu-id="db9ac-1789">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1789">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1790">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1790">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1791">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1791">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1792">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1792">'Identity'</span></span>
- <span data-ttu-id="db9ac-1793">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1793">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1794">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1794">'Razor'</span></span>
- <span data-ttu-id="db9ac-1795">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1795">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1796">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1796">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1797">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1797">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1798">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1798">'Identity'</span></span>
- <span data-ttu-id="db9ac-1799">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1799">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1800">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1800">'Razor'</span></span>
- <span data-ttu-id="db9ac-1801">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1801">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1802">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1802">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1803">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1803">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1804">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1804">'Identity'</span></span>
- <span data-ttu-id="db9ac-1805">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1805">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1806">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1806">'Razor'</span></span>
- <span data-ttu-id="db9ac-1807">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1807">'SignalR' uid:</span></span> 

<span data-ttu-id="db9ac-1808">-------------- | titre de--- : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1808">-------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1809">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1809">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1810">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1810">'Identity'</span></span>
- <span data-ttu-id="db9ac-1811">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1811">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1812">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1812">'Razor'</span></span>
- <span data-ttu-id="db9ac-1813">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1813">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1814">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1814">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1815">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1815">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1816">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1816">'Identity'</span></span>
- <span data-ttu-id="db9ac-1817">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1817">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1818">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1818">'Razor'</span></span>
- <span data-ttu-id="db9ac-1819">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1819">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1820">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1820">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1821">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1821">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1822">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1822">'Identity'</span></span>
- <span data-ttu-id="db9ac-1823">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1823">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1824">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1824">'Razor'</span></span>
- <span data-ttu-id="db9ac-1825">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1825">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1826">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1826">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1827">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1827">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1828">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1828">'Identity'</span></span>
- <span data-ttu-id="db9ac-1829">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1829">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1830">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1830">'Razor'</span></span>
- <span data-ttu-id="db9ac-1831">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1831">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1832">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1832">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1833">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1833">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1834">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1834">'Identity'</span></span>
- <span data-ttu-id="db9ac-1835">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1835">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1836">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1836">'Razor'</span></span>
- <span data-ttu-id="db9ac-1837">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1837">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1838">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1838">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1839">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1839">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1840">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1840">'Identity'</span></span>
- <span data-ttu-id="db9ac-1841">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1841">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1842">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1842">'Razor'</span></span>
- <span data-ttu-id="db9ac-1843">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1843">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1844">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1844">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1845">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1845">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1846">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1846">'Identity'</span></span>
- <span data-ttu-id="db9ac-1847">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1847">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1848">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1848">'Razor'</span></span>
- <span data-ttu-id="db9ac-1849">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1849">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1850">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1850">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1851">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1851">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1852">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1852">'Identity'</span></span>
- <span data-ttu-id="db9ac-1853">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1853">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1854">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1854">'Razor'</span></span>
- <span data-ttu-id="db9ac-1855">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1855">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1856">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1856">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1857">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1857">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1858">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1858">'Identity'</span></span>
- <span data-ttu-id="db9ac-1859">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1859">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1860">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1860">'Razor'</span></span>
- <span data-ttu-id="db9ac-1861">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1861">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1862">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1862">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1863">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1863">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1864">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1864">'Identity'</span></span>
- <span data-ttu-id="db9ac-1865">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1865">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1866">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1866">'Razor'</span></span>
- <span data-ttu-id="db9ac-1867">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1867">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1868">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1868">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1869">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1869">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1870">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1870">'Identity'</span></span>
- <span data-ttu-id="db9ac-1871">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1871">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1872">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1872">'Razor'</span></span>
- <span data-ttu-id="db9ac-1873">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1873">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1874">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1874">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1875">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1875">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1876">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1876">'Identity'</span></span>
- <span data-ttu-id="db9ac-1877">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1877">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1878">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1878">'Razor'</span></span>
- <span data-ttu-id="db9ac-1879">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1879">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1880">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1880">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1881">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1881">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1882">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1882">'Identity'</span></span>
- <span data-ttu-id="db9ac-1883">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1883">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1884">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1884">'Razor'</span></span>
- <span data-ttu-id="db9ac-1885">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1885">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1886">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1886">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1887">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1887">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1888">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1888">'Identity'</span></span>
- <span data-ttu-id="db9ac-1889">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1889">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1890">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1890">'Razor'</span></span>
- <span data-ttu-id="db9ac-1891">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1891">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1892">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1892">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1893">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1893">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1894">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1894">'Identity'</span></span>
- <span data-ttu-id="db9ac-1895">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1895">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1896">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1896">'Razor'</span></span>
- <span data-ttu-id="db9ac-1897">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1897">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1898">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1898">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1899">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1899">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1900">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1900">'Identity'</span></span>
- <span data-ttu-id="db9ac-1901">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1901">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1902">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1902">'Razor'</span></span>
- <span data-ttu-id="db9ac-1903">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1903">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1904">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1904">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1905">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1905">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1906">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1906">'Identity'</span></span>
- <span data-ttu-id="db9ac-1907">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1907">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1908">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1908">'Razor'</span></span>
- <span data-ttu-id="db9ac-1909">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1909">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1910">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1910">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1911">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1911">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1912">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1912">'Identity'</span></span>
- <span data-ttu-id="db9ac-1913">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1913">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1914">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1914">'Razor'</span></span>
- <span data-ttu-id="db9ac-1915">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1915">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1916">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1916">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1917">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1917">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1918">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1918">'Identity'</span></span>
- <span data-ttu-id="db9ac-1919">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1919">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1920">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1920">'Razor'</span></span>
- <span data-ttu-id="db9ac-1921">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1921">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1922">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1922">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1923">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1923">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1924">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1924">'Identity'</span></span>
- <span data-ttu-id="db9ac-1925">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1925">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1926">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1926">'Razor'</span></span>
- <span data-ttu-id="db9ac-1927">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1927">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1928">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1928">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1929">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1929">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1930">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1930">'Identity'</span></span>
- <span data-ttu-id="db9ac-1931">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1931">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1932">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1932">'Razor'</span></span>
- <span data-ttu-id="db9ac-1933">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1933">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1934">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1934">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1935">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1935">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1936">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1936">'Identity'</span></span>
- <span data-ttu-id="db9ac-1937">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1937">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1938">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1938">'Razor'</span></span>
- <span data-ttu-id="db9ac-1939">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1939">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1940">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1940">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1941">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1941">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1942">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1942">'Identity'</span></span>
- <span data-ttu-id="db9ac-1943">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1943">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1944">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1944">'Razor'</span></span>
- <span data-ttu-id="db9ac-1945">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1945">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1946">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1946">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1947">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1947">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1948">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1948">'Identity'</span></span>
- <span data-ttu-id="db9ac-1949">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1949">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1950">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1950">'Razor'</span></span>
- <span data-ttu-id="db9ac-1951">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1951">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1952">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1952">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1953">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1953">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1954">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1954">'Identity'</span></span>
- <span data-ttu-id="db9ac-1955">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1955">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1956">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1956">'Razor'</span></span>
- <span data-ttu-id="db9ac-1957">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1957">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1958">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1958">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1959">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1959">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1960">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1960">'Identity'</span></span>
- <span data-ttu-id="db9ac-1961">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1961">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1962">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1962">'Razor'</span></span>
- <span data-ttu-id="db9ac-1963">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1963">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1964">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1964">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1965">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1965">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1966">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1966">'Identity'</span></span>
- <span data-ttu-id="db9ac-1967">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1967">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1968">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1968">'Razor'</span></span>
- <span data-ttu-id="db9ac-1969">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1969">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1970">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1970">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1971">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1971">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1972">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1972">'Identity'</span></span>
- <span data-ttu-id="db9ac-1973">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1973">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1974">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1974">'Razor'</span></span>
- <span data-ttu-id="db9ac-1975">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1975">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1976">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1976">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1977">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1977">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1978">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1978">'Identity'</span></span>
- <span data-ttu-id="db9ac-1979">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1979">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1980">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1980">'Razor'</span></span>
- <span data-ttu-id="db9ac-1981">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1981">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1982">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1982">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1983">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1983">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1984">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1984">'Identity'</span></span>
- <span data-ttu-id="db9ac-1985">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1985">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1986">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1986">'Razor'</span></span>
- <span data-ttu-id="db9ac-1987">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1987">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1988">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1988">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1989">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1989">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1990">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1990">'Identity'</span></span>
- <span data-ttu-id="db9ac-1991">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1991">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1992">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1992">'Razor'</span></span>
- <span data-ttu-id="db9ac-1993">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1993">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-1994">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1994">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-1995">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1995">'Blazor'</span></span>
- <span data-ttu-id="db9ac-1996">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1996">'Identity'</span></span>
- <span data-ttu-id="db9ac-1997">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1997">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-1998">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-1998">'Razor'</span></span>
- <span data-ttu-id="db9ac-1999">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-1999">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-2000">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2000">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2001">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2001">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2002">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2002">'Identity'</span></span>
- <span data-ttu-id="db9ac-2003">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2003">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2004">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2004">'Razor'</span></span>
- <span data-ttu-id="db9ac-2005">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2005">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-2006">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2006">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2007">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2007">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2008">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2008">'Identity'</span></span>
- <span data-ttu-id="db9ac-2009">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2009">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2010">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2010">'Razor'</span></span>
- <span data-ttu-id="db9ac-2011">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2011">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-2012">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2012">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2013">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2013">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2014">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2014">'Identity'</span></span>
- <span data-ttu-id="db9ac-2015">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2015">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2016">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2016">'Razor'</span></span>
- <span data-ttu-id="db9ac-2017">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2017">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-2018">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2018">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2019">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2019">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2020">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2020">'Identity'</span></span>
- <span data-ttu-id="db9ac-2021">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2021">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2022">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2022">'Razor'</span></span>
- <span data-ttu-id="db9ac-2023">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2023">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-2024">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2024">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2025">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2025">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2026">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2026">'Identity'</span></span>
- <span data-ttu-id="db9ac-2027">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2027">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2028">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2028">'Razor'</span></span>
- <span data-ttu-id="db9ac-2029">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2029">'SignalR' uid:</span></span> 

<span data-ttu-id="db9ac-2030">---------------------------------------- | | `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | Entrée de configuration non créée.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2030">---------------------------------------- | | `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | Configuration entry not created.</span></span>                                                <span data-ttu-id="db9ac-2031">| | `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | Clé : `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2031">| | `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="db9ac-2032">Valeur : `MySql.Data.MySqlClient` | | `SQLAZURECONNSTR_{KEY}`   |  `ConnectionStrings:{KEY}`   | Clé : `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2032">Value: `MySql.Data.MySqlClient` | | `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="db9ac-2033">Valeur : `System.Data.SqlClient` | | `SQLCONNSTR_{KEY}`        |  `ConnectionStrings:{KEY}`   | Clé : `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2033">Value: `System.Data.SqlClient`  | | `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="db9ac-2034">Ajoutée`System.Data.SqlClient`  |</span><span class="sxs-lookup"><span data-stu-id="db9ac-2034">Value: `System.Data.SqlClient`  |</span></span>

<span data-ttu-id="db9ac-2035">**Exemple**</span><span class="sxs-lookup"><span data-stu-id="db9ac-2035">**Example**</span></span>

<span data-ttu-id="db9ac-2036">Une variable d’environnement de chaîne de connexion personnalisée est créée sur le serveur :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2036">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="db9ac-2037">Nom &ndash;`CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="db9ac-2037">Name &ndash; `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="db9ac-2038">Valeur &ndash;`Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="db9ac-2038">Value &ndash; `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="db9ac-2039">Si `IConfiguration` est injecté et affecté à un champ nommé `_config` , lisez la valeur :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2039">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="db9ac-2040">Fournisseur de configuration de fichier</span><span class="sxs-lookup"><span data-stu-id="db9ac-2040">File Configuration Provider</span></span>

<span data-ttu-id="db9ac-2041"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> est la classe de base pour charger la configuration à partir du système de fichiers.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2041"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="db9ac-2042">Les fournisseurs de configuration suivants sont dédiés à des types de fichiers spécifiques :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2042">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="db9ac-2043">Fournisseur de configuration INI</span><span class="sxs-lookup"><span data-stu-id="db9ac-2043">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="db9ac-2044">Fournisseur de configuration JSON</span><span class="sxs-lookup"><span data-stu-id="db9ac-2044">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="db9ac-2045">Fournisseur de configuration XML</span><span class="sxs-lookup"><span data-stu-id="db9ac-2045">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="db9ac-2046">Fournisseur de configuration INI</span><span class="sxs-lookup"><span data-stu-id="db9ac-2046">INI Configuration Provider</span></span>

<span data-ttu-id="db9ac-2047"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> charge la configuration à partir des paires clé-valeur du fichier INI lors de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2047">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="db9ac-2048">Pour activer la configuration du fichier INI, appelez la méthode d’extension <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> sur une instance de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2048">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="db9ac-2049">Le signe deux-points peut servir de délimiteur de section dans la configuration d’un fichier INI.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2049">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="db9ac-2050">Les surcharges permettent de spécifier :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2050">Overloads permit specifying:</span></span>

* <span data-ttu-id="db9ac-2051">Si le fichier est facultatif.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2051">Whether the file is optional.</span></span>
* <span data-ttu-id="db9ac-2052">Si la configuration est rechargée quand le fichier est modifié.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2052">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="db9ac-2053">Le <xref:Microsoft.Extensions.FileProviders.IFileProvider> utilisé pour accéder au fichier.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2053">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="db9ac-2054">Appelez `ConfigureAppConfiguration` lors de la création de l’hôte pour spécifier la configuration de l’application :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2054">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="db9ac-2055">Exemple générique d’un fichier de configuration INI :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2055">A generic example of an INI configuration file:</span></span>

```ini
[section0]
key0=value
key1=value

[section1]
subsection:key=value

[section2:subsection0]
key=value

[section2:subsection1]
key=value
```

<span data-ttu-id="db9ac-2056">Le fichier de configuration précédent charge les clés suivantes avec `value` :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2056">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="db9ac-2057">section0:key0</span><span class="sxs-lookup"><span data-stu-id="db9ac-2057">section0:key0</span></span>
* <span data-ttu-id="db9ac-2058">section0:key1</span><span class="sxs-lookup"><span data-stu-id="db9ac-2058">section0:key1</span></span>
* <span data-ttu-id="db9ac-2059">section1:subsection:key</span><span class="sxs-lookup"><span data-stu-id="db9ac-2059">section1:subsection:key</span></span>
* <span data-ttu-id="db9ac-2060">section2:subsection0:key</span><span class="sxs-lookup"><span data-stu-id="db9ac-2060">section2:subsection0:key</span></span>
* <span data-ttu-id="db9ac-2061">section2:subsection1:key</span><span class="sxs-lookup"><span data-stu-id="db9ac-2061">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="db9ac-2062">Fournisseur de configuration JSON</span><span class="sxs-lookup"><span data-stu-id="db9ac-2062">JSON Configuration Provider</span></span>

<span data-ttu-id="db9ac-2063"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> charge la configuration à partir des paires clé-valeur du fichier JSON lors de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2063">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="db9ac-2064">Pour activer la configuration du fichier JSON, appelez la méthode d’extension <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> sur une instance de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2064">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="db9ac-2065">Les surcharges permettent de spécifier :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2065">Overloads permit specifying:</span></span>

* <span data-ttu-id="db9ac-2066">Si le fichier est facultatif.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2066">Whether the file is optional.</span></span>
* <span data-ttu-id="db9ac-2067">Si la configuration est rechargée quand le fichier est modifié.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2067">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="db9ac-2068">Le <xref:Microsoft.Extensions.FileProviders.IFileProvider> utilisé pour accéder au fichier.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2068">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="db9ac-2069">`AddJsonFile`est appelé automatiquement deux fois lors de l’initialisation d’un nouveau générateur d’hôte `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="db9ac-2069">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="db9ac-2070">La méthode est appelée pour charger la configuration à partir de :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2070">The method is called to load configuration from:</span></span>

* <span data-ttu-id="db9ac-2071">*appSettings.JSON* &ndash; Ce fichier est lu en premier.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2071">*appsettings.json* &ndash; This file is read first.</span></span> <span data-ttu-id="db9ac-2072">La version de l’environnement du fichier peut remplacer les valeurs fournies par le fichier *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2072">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="db9ac-2073">*appsettings.{Environment}.json* &ndash; La version de l’environnement du fichier est chargée à partir du fichier [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="db9ac-2073">*appsettings.{Environment}.json* &ndash; The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="db9ac-2074">Pour plus d’informations, consultez la section [Configuration par défaut](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="db9ac-2074">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="db9ac-2075">`CreateDefaultBuilder` charge également :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2075">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="db9ac-2076">Variables d'environnement.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2076">Environment variables.</span></span>
* <span data-ttu-id="db9ac-2077">[Secrets utilisateur (Secret Manager)](xref:security/app-secrets) dans l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2077">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="db9ac-2078">Arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="db9ac-2078">Command-line arguments.</span></span>

<span data-ttu-id="db9ac-2079">Le Fournisseur de configuration JSON est établi en premier.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2079">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="db9ac-2080">Par conséquent, les secrets utilisateur, les variables d’environnement et les arguments de ligne de commande remplacent la configuration définie par les fichiers *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2080">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="db9ac-2081">Appelez `ConfigureAppConfiguration` lors de la création de l’hôte pour spécifier la configuration de l’application pour les fichiers autres qu’*appsettings.json* et *appsettings. {Environment} .json* :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2081">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="db9ac-2082">**Exemple**</span><span class="sxs-lookup"><span data-stu-id="db9ac-2082">**Example**</span></span>

<span data-ttu-id="db9ac-2083">L’exemple d’application tire parti de la méthode de commodité statique `CreateDefaultBuilder` pour créer l’hôte, ce qui comprend deux appels à `AddJsonFile` :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2083">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="db9ac-2084">Le premier appel à `AddJsonFile` charge la configuration à partir de *appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="db9ac-2084">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="db9ac-2085">Le deuxième appel à `AddJsonFile` charge la configuration à partir de *appSettings. { Environnement}. JSON*.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2085">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="db9ac-2086">Pour *appSettings. Development. JSON* dans l’exemple d’application, le fichier suivant est chargé :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2086">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="db9ac-2087">Exécutez l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2087">Run the sample app.</span></span> <span data-ttu-id="db9ac-2088">Ouvrez un navigateur vers l’application avec l’adresse `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2088">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="db9ac-2089">La sortie contient des paires clé-valeur pour la configuration en fonction de l’environnement de l’application.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2089">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="db9ac-2090">Le niveau de journalisation de la clé `Logging:LogLevel:Default` est `Debug` lors de l’exécution de l’application dans l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2090">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="db9ac-2091">Exécutez à nouveau l’exemple d’application dans l’environnement de production :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2091">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="db9ac-2092">Ouvrez le fichier *Properties/launchSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="db9ac-2092">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="db9ac-2093">Dans le `ConfigurationSample` profil, remplacez la valeur de la `ASPNETCORE_ENVIRONMENT` variable d’environnement par `Production` .</span><span class="sxs-lookup"><span data-stu-id="db9ac-2093">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="db9ac-2094">Enregistrez le fichier et exécutez l’application avec `dotnet run` dans un interpréteur de commandes.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2094">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="db9ac-2095">Paramètres dans *appSettings. Development. JSON* ne remplace plus les paramètres dans *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2095">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="db9ac-2096">Le niveau de journalisation de la clé `Logging:LogLevel:Default` est `Warning` .</span><span class="sxs-lookup"><span data-stu-id="db9ac-2096">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="db9ac-2097">Fournisseur de configuration XML</span><span class="sxs-lookup"><span data-stu-id="db9ac-2097">XML Configuration Provider</span></span>

<span data-ttu-id="db9ac-2098"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> charge la configuration à partir des paires clé-valeur du fichier XML lors de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2098">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="db9ac-2099">Pour activer la configuration du fichier XML, appelez la méthode d’extension <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> sur une instance de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2099">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="db9ac-2100">Les surcharges permettent de spécifier :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2100">Overloads permit specifying:</span></span>

* <span data-ttu-id="db9ac-2101">Si le fichier est facultatif.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2101">Whether the file is optional.</span></span>
* <span data-ttu-id="db9ac-2102">Si la configuration est rechargée quand le fichier est modifié.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2102">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="db9ac-2103">Le <xref:Microsoft.Extensions.FileProviders.IFileProvider> utilisé pour accéder au fichier.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2103">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="db9ac-2104">Le nœud racine du fichier de configuration est ignoré lorsque les paires clé-valeur de la configuration sont créées.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2104">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="db9ac-2105">Ne spécifiez pas une définition de type de document (DTD) ou un espace de noms dans le fichier.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2105">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="db9ac-2106">Appelez `ConfigureAppConfiguration` lors de la création de l’hôte pour spécifier la configuration de l’application :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2106">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="db9ac-2107">Les fichiers de configuration XML peuvent utiliser des noms d’éléments distincts pour les sections répétitives :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2107">XML configuration files can use distinct element names for repeating sections:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <section0>
    <key0>value</key0>
    <key1>value</key1>
  </section0>
  <section1>
    <key0>value</key0>
    <key1>value</key1>
  </section1>
</configuration>
```

<span data-ttu-id="db9ac-2108">Le fichier de configuration précédent charge les clés suivantes avec `value` :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2108">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="db9ac-2109">section0:key0</span><span class="sxs-lookup"><span data-stu-id="db9ac-2109">section0:key0</span></span>
* <span data-ttu-id="db9ac-2110">section0:key1</span><span class="sxs-lookup"><span data-stu-id="db9ac-2110">section0:key1</span></span>
* <span data-ttu-id="db9ac-2111">section1:key0</span><span class="sxs-lookup"><span data-stu-id="db9ac-2111">section1:key0</span></span>
* <span data-ttu-id="db9ac-2112">section1:key1</span><span class="sxs-lookup"><span data-stu-id="db9ac-2112">section1:key1</span></span>

<span data-ttu-id="db9ac-2113">Les éléments répétitifs qui utilisent le même nom d’élément fonctionnent si l’attribut `name` est utilisé pour distinguer les éléments :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2113">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <section name="section0">
    <key name="key0">value</key>
    <key name="key1">value</key>
  </section>
  <section name="section1">
    <key name="key0">value</key>
    <key name="key1">value</key>
  </section>
</configuration>
```

<span data-ttu-id="db9ac-2114">Le fichier de configuration précédent charge les clés suivantes avec `value` :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2114">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="db9ac-2115">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="db9ac-2115">section:section0:key:key0</span></span>
* <span data-ttu-id="db9ac-2116">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="db9ac-2116">section:section0:key:key1</span></span>
* <span data-ttu-id="db9ac-2117">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="db9ac-2117">section:section1:key:key0</span></span>
* <span data-ttu-id="db9ac-2118">section:section1:key:key1</span><span class="sxs-lookup"><span data-stu-id="db9ac-2118">section:section1:key:key1</span></span>

<span data-ttu-id="db9ac-2119">Les attributs peuvent être utilisés pour fournir des valeurs :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2119">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="db9ac-2120">Le fichier de configuration précédent charge les clés suivantes avec `value` :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2120">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="db9ac-2121">key:attribute</span><span class="sxs-lookup"><span data-stu-id="db9ac-2121">key:attribute</span></span>
* <span data-ttu-id="db9ac-2122">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="db9ac-2122">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="db9ac-2123">Fournisseur de configuration clé par fichier</span><span class="sxs-lookup"><span data-stu-id="db9ac-2123">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="db9ac-2124">Le <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> utilise les fichiers d’un répertoire en tant que paires clé-valeur de configuration.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2124">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="db9ac-2125">La clé est le nom de fichier.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2125">The key is the file name.</span></span> <span data-ttu-id="db9ac-2126">La valeur contient le contenu du fichier.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2126">The value contains the file's contents.</span></span> <span data-ttu-id="db9ac-2127">Le Fournisseur de configuration Clé par fichier est utilisé dans les scénarios d’hébergement de Docker.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2127">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="db9ac-2128">Pour activer la configuration clé par fichier, appelez la méthode d’extension <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> sur une instance de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2128">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="db9ac-2129">Le `directoryPath` aux fichiers doit être un chemin d’accès absolu.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2129">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="db9ac-2130">Les surcharges permettent de spécifier :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2130">Overloads permit specifying:</span></span>

* <span data-ttu-id="db9ac-2131">Un délégué `Action<KeyPerFileConfigurationSource>` qui configure la source.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2131">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="db9ac-2132">Si le répertoire est facultatif et le chemin d’accès au répertoire.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2132">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="db9ac-2133">Le double trait de soulignement (`__`) est utilisé comme un délimiteur de clé de configuration dans les noms de fichiers.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2133">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="db9ac-2134">Par exemple, le nom de fichier `Logging__LogLevel__System` génère la clé de configuration `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2134">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="db9ac-2135">Appelez `ConfigureAppConfiguration` lors de la création de l’hôte pour spécifier la configuration de l’application :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2135">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="db9ac-2136">Fournisseur de configuration de mémoire</span><span class="sxs-lookup"><span data-stu-id="db9ac-2136">Memory Configuration Provider</span></span>

<span data-ttu-id="db9ac-2137">Le <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> utilise une collection en mémoire en tant que paires clé-valeur de configuration.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2137">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="db9ac-2138">Pour activer la configuration de la collection en mémoire, appelez la méthode d’extension <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> sur une instance de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2138">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="db9ac-2139">Le fournisseur de configuration peut être initialisé avec un `IEnumerable<KeyValuePair<String,String>>`.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2139">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="db9ac-2140">Appelez `ConfigureAppConfiguration` lors de la création de l’hôte pour spécifier la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2140">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="db9ac-2141">Dans l’exemple suivant, un dictionnaire de configuration est créé :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2141">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="db9ac-2142">Le dictionnaire est utilisé avec un appel à `AddInMemoryCollection` pour fournir la configuration :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2142">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="db9ac-2143">GetValue</span><span class="sxs-lookup"><span data-stu-id="db9ac-2143">GetValue</span></span>

<span data-ttu-id="db9ac-2144">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*)extrait une valeur unique de la configuration avec une clé spécifiée et la convertit en type de non-collection spécifié.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2144">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="db9ac-2145">Une surcharge accepte une valeur par défaut.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2145">An overload accepts a default value.</span></span>

<span data-ttu-id="db9ac-2146">L’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2146">The following example:</span></span>

* <span data-ttu-id="db9ac-2147">Extrait la valeur de chaîne de la configuration avec la clé `NumberKey`.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2147">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="db9ac-2148">Si `NumberKey` est introuvable dans les clés de configuration, la valeur par défaut de `99` est utilisée.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2148">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="db9ac-2149">Tape la valeur comme `int`.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2149">Types the value as an `int`.</span></span>
* <span data-ttu-id="db9ac-2150">Stocke la valeur dans la propriété `NumberConfig` pour une utilisation par la page.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2150">Stores the value in the `NumberConfig` property for use by the page.</span></span>

```csharp
public class IndexModel : PageModel
{
    public IndexModel(IConfiguration config)
    {
        _config = config;
    }

    public int NumberConfig { get; private set; }

    public void OnGet()
    {
        NumberConfig = _config.GetValue<int>("NumberKey", 99);
    }
}
```

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="db9ac-2151">GetSection, GetChildren et Exists</span><span class="sxs-lookup"><span data-stu-id="db9ac-2151">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="db9ac-2152">Pour les exemples qui suivent, utilisez le fichier JSON suivant.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2152">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="db9ac-2153">Quatre clés se trouvent dans deux sections, dont l’une inclut deux sous-sections :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2153">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

```json
{
  "section0": {
    "key0": "value",
    "key1": "value"
  },
  "section1": {
    "key0": "value",
    "key1": "value"
  },
  "section2": {
    "subsection0" : {
      "key0": "value",
      "key1": "value"
    },
    "subsection1" : {
      "key0": "value",
      "key1": "value"
    }
  }
}
```

<span data-ttu-id="db9ac-2154">Lorsque le fichier est lu dans la configuration, les clés hiérarchiques uniques suivantes sont créées pour stocker les valeurs de la configuration :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2154">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="db9ac-2155">section0:key0</span><span class="sxs-lookup"><span data-stu-id="db9ac-2155">section0:key0</span></span>
* <span data-ttu-id="db9ac-2156">section0:key1</span><span class="sxs-lookup"><span data-stu-id="db9ac-2156">section0:key1</span></span>
* <span data-ttu-id="db9ac-2157">section1:key0</span><span class="sxs-lookup"><span data-stu-id="db9ac-2157">section1:key0</span></span>
* <span data-ttu-id="db9ac-2158">section1:key1</span><span class="sxs-lookup"><span data-stu-id="db9ac-2158">section1:key1</span></span>
* <span data-ttu-id="db9ac-2159">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="db9ac-2159">section2:subsection0:key0</span></span>
* <span data-ttu-id="db9ac-2160">section2:subsection0:key1</span><span class="sxs-lookup"><span data-stu-id="db9ac-2160">section2:subsection0:key1</span></span>
* <span data-ttu-id="db9ac-2161">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="db9ac-2161">section2:subsection1:key0</span></span>
* <span data-ttu-id="db9ac-2162">section2:subsection1:key1</span><span class="sxs-lookup"><span data-stu-id="db9ac-2162">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="db9ac-2163">GetSection</span><span class="sxs-lookup"><span data-stu-id="db9ac-2163">GetSection</span></span>

<span data-ttu-id="db9ac-2164">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extrait une sous-section de la configuration avec la clé de sous-section spécifiée.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2164">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="db9ac-2165">Pour retourner un <xref:Microsoft.Extensions.Configuration.IConfigurationSection> contenant uniquement les paires clé-valeur dans `section1`, appelez `GetSection` et fournissez le nom de section :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2165">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="db9ac-2166">`configSection` n’a pas de valeur, seulement une clé et un chemin.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2166">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="db9ac-2167">De même, pour obtenir les valeurs des clés dans `section2:subsection0`, appelez `GetSection` et fournissez le chemin d’accès de la section :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2167">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="db9ac-2168">`GetSection` ne retourne jamais `null`.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2168">`GetSection` never returns `null`.</span></span> <span data-ttu-id="db9ac-2169">Si aucune section correspondante n’est trouvée, une valeur `IConfigurationSection` vide est retournée.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2169">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="db9ac-2170">Quand `GetSection` retourne une section correspondante, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> n’est pas rempli.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2170">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="db9ac-2171"><xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> et <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> sont retournés quand la section existe.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2171">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="db9ac-2172">GetChildren</span><span class="sxs-lookup"><span data-stu-id="db9ac-2172">GetChildren</span></span>

<span data-ttu-id="db9ac-2173">Un appel à [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) sur `section2` obtient un `IEnumerable<IConfigurationSection>` qui inclut :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2173">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="db9ac-2174">Exists</span><span class="sxs-lookup"><span data-stu-id="db9ac-2174">Exists</span></span>

<span data-ttu-id="db9ac-2175">Utilisez [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) pour déterminer si une section de configuration existe :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2175">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="db9ac-2176">Compte tenu des données d’exemple, `sectionExists` est `false`, car il n’y a pas de section `section2:subsection2` dans les données de configuration.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2176">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="db9ac-2177">Établir une liaison à un graphe d’objets</span><span class="sxs-lookup"><span data-stu-id="db9ac-2177">Bind to an object graph</span></span>

<span data-ttu-id="db9ac-2178"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> est capable de lier l’intégralité d’un graphe d’objets POCO.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2178"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="db9ac-2179">Comme pour la liaison d’un objet simple, seules les propriétés accessibles en lecture/écriture publiques sont liées.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2179">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="db9ac-2180">L’exemple contient un modèle `TvShow` dont le graphe d’objets inclut les classes `Metadata` et `Actors` (*Models/TvShow.cs*) :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2180">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="db9ac-2181">L’exemple d’application a un fichier *tvshow.xml* contenant les données de configuration :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2181">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="db9ac-2182">La configuration est liée à l’intégralité du graphe d’objets `TvShow` avec la méthode `Bind`.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2182">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="db9ac-2183">L’instance liée est affectée à une propriété pour le rendu :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2183">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="db9ac-2184">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)lie et retourne le type spécifié.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2184">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="db9ac-2185">Il est plus pratique d’utiliser `Get<T>` que `Bind`.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2185">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="db9ac-2186">Le code suivant montre comment utiliser `Get<T>` avec l’exemple précédent :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2186">The following code shows how to use `Get<T>` with the preceding example:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="db9ac-2187">Lier un tableau à une classe</span><span class="sxs-lookup"><span data-stu-id="db9ac-2187">Bind an array to a class</span></span>

<span data-ttu-id="db9ac-2188">*L’exemple d’application illustre les concepts abordés dans cette section.*</span><span class="sxs-lookup"><span data-stu-id="db9ac-2188">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="db9ac-2189"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> prend en charge la liaison de tableaux à des objets à l’aide d’index de tableau dans les clés de configuration.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2189">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="db9ac-2190">Tout format de tableau qui expose un segment de clé numérique ( `:0:` , `:1:` , &hellip; `:{n}:` ) est capable d’effectuer une liaison de tableau à un tableau de classes POCO.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2190">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="db9ac-2191">La liaison est fournie par convention.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2191">Binding is provided by convention.</span></span> <span data-ttu-id="db9ac-2192">Les fournisseurs de configuration personnalisés ne sont pas obligés d’implémenter la liaison de tableau.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2192">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="db9ac-2193">**Traitement de tableau en mémoire**</span><span class="sxs-lookup"><span data-stu-id="db9ac-2193">**In-memory array processing**</span></span>

<span data-ttu-id="db9ac-2194">Observez les valeurs et les clés de configuration indiquées dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2194">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="db9ac-2195">Clé</span><span class="sxs-lookup"><span data-stu-id="db9ac-2195">Key</span></span>             | <span data-ttu-id="db9ac-2196">Valeur</span><span class="sxs-lookup"><span data-stu-id="db9ac-2196">Value</span></span>  |
| :---
<span data-ttu-id="db9ac-2197">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2197">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2198">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2198">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2199">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2199">'Identity'</span></span>
- <span data-ttu-id="db9ac-2200">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2200">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2201">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2201">'Razor'</span></span>
- <span data-ttu-id="db9ac-2202">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2202">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-2203">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2203">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2204">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2204">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2205">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2205">'Identity'</span></span>
- <span data-ttu-id="db9ac-2206">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2206">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2207">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2207">'Razor'</span></span>
- <span data-ttu-id="db9ac-2208">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2208">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-2209">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2209">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2210">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2210">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2211">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2211">'Identity'</span></span>
- <span data-ttu-id="db9ac-2212">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2212">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2213">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2213">'Razor'</span></span>
- <span data-ttu-id="db9ac-2214">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2214">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-2215">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2215">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2216">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2216">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2217">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2217">'Identity'</span></span>
- <span data-ttu-id="db9ac-2218">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2218">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2219">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2219">'Razor'</span></span>
- <span data-ttu-id="db9ac-2220">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2220">'SignalR' uid:</span></span> 

<span data-ttu-id="db9ac-2221">-------: | :----: | | Tableau : entrées : 0 | value0 | | Tableau : entrées : 1 | valeur1 | | Tableau : entrées : 2 | valeur2 | | Tableau : entrées : 4 | Value4 | | Tableau : entrées : 5 | value5 |</span><span class="sxs-lookup"><span data-stu-id="db9ac-2221">-------: | :----: | | array:entries:0 | value0 | | array:entries:1 | value1 | | array:entries:2 | value2 | | array:entries:4 | value4 | | array:entries:5 | value5 |</span></span>

<span data-ttu-id="db9ac-2222">Ces clés et valeurs sont chargées dans l’exemple d’application à l’aide du Fournisseur de configuration de mémoire :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2222">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="db9ac-2223">Le tableau ignore une valeur pour l’index &num;3.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2223">The array skips a value for index &num;3.</span></span> <span data-ttu-id="db9ac-2224">Le binder de configuration n’est pas capable de lier des valeurs null ou de créer des entrées null dans les objets liés, ce qui deviendra clair dans un moment lorsque le résultat de liaison de ce tableau à un objet est illustré.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2224">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="db9ac-2225">Dans l’exemple d’application, une classe POCO est disponible pour contenir les données de configuration liées :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2225">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="db9ac-2226">Les données de configuration sont liées à l’objet :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2226">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="db9ac-2227">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)la syntaxe peut également être utilisée, ce qui se traduit par un code plus compact :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2227">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="db9ac-2228">L’objet lié, une instance de `ArrayExample`, reçoit les données de tableau à partir de la configuration.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2228">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="db9ac-2229">Index `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="db9ac-2229">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="db9ac-2230">`ArrayExample.Entries` Valeur</span><span class="sxs-lookup"><span data-stu-id="db9ac-2230">`ArrayExample.Entries` Value</span></span> |
| :---
<span data-ttu-id="db9ac-2231">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2231">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2232">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2232">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2233">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2233">'Identity'</span></span>
- <span data-ttu-id="db9ac-2234">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2234">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2235">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2235">'Razor'</span></span>
- <span data-ttu-id="db9ac-2236">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2236">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-2237">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2237">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2238">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2238">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2239">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2239">'Identity'</span></span>
- <span data-ttu-id="db9ac-2240">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2240">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2241">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2241">'Razor'</span></span>
- <span data-ttu-id="db9ac-2242">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2242">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-2243">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2243">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2244">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2244">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2245">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2245">'Identity'</span></span>
- <span data-ttu-id="db9ac-2246">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2246">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2247">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2247">'Razor'</span></span>
- <span data-ttu-id="db9ac-2248">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2248">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-2249">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2249">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2250">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2250">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2251">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2251">'Identity'</span></span>
- <span data-ttu-id="db9ac-2252">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2252">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2253">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2253">'Razor'</span></span>
- <span data-ttu-id="db9ac-2254">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2254">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-2255">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2255">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2256">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2256">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2257">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2257">'Identity'</span></span>
- <span data-ttu-id="db9ac-2258">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2258">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2259">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2259">'Razor'</span></span>
- <span data-ttu-id="db9ac-2260">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2260">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-2261">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2261">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2262">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2262">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2263">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2263">'Identity'</span></span>
- <span data-ttu-id="db9ac-2264">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2264">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2265">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2265">'Razor'</span></span>
- <span data-ttu-id="db9ac-2266">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2266">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-2267">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2267">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2268">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2268">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2269">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2269">'Identity'</span></span>
- <span data-ttu-id="db9ac-2270">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2270">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2271">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2271">'Razor'</span></span>
- <span data-ttu-id="db9ac-2272">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2272">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-2273">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2273">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2274">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2274">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2275">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2275">'Identity'</span></span>
- <span data-ttu-id="db9ac-2276">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2276">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2277">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2277">'Razor'</span></span>
- <span data-ttu-id="db9ac-2278">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2278">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-2279">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2279">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2280">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2280">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2281">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2281">'Identity'</span></span>
- <span data-ttu-id="db9ac-2282">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2282">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2283">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2283">'Razor'</span></span>
- <span data-ttu-id="db9ac-2284">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2284">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-2285">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2285">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2286">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2286">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2287">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2287">'Identity'</span></span>
- <span data-ttu-id="db9ac-2288">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2288">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2289">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2289">'Razor'</span></span>
- <span data-ttu-id="db9ac-2290">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2290">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-2291">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2291">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2292">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2292">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2293">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2293">'Identity'</span></span>
- <span data-ttu-id="db9ac-2294">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2294">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2295">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2295">'Razor'</span></span>
- <span data-ttu-id="db9ac-2296">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2296">'SignalR' uid:</span></span> 

<span data-ttu-id="db9ac-2297">-------------: | :---Title : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2297">-------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2298">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2298">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2299">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2299">'Identity'</span></span>
- <span data-ttu-id="db9ac-2300">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2300">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2301">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2301">'Razor'</span></span>
- <span data-ttu-id="db9ac-2302">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2302">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-2303">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2303">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2304">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2304">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2305">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2305">'Identity'</span></span>
- <span data-ttu-id="db9ac-2306">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2306">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2307">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2307">'Razor'</span></span>
- <span data-ttu-id="db9ac-2308">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2308">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-2309">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2309">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2310">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2310">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2311">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2311">'Identity'</span></span>
- <span data-ttu-id="db9ac-2312">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2312">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2313">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2313">'Razor'</span></span>
- <span data-ttu-id="db9ac-2314">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2314">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-2315">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2315">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2316">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2316">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2317">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2317">'Identity'</span></span>
- <span data-ttu-id="db9ac-2318">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2318">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2319">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2319">'Razor'</span></span>
- <span data-ttu-id="db9ac-2320">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2320">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-2321">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2321">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2322">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2322">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2323">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2323">'Identity'</span></span>
- <span data-ttu-id="db9ac-2324">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2324">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2325">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2325">'Razor'</span></span>
- <span data-ttu-id="db9ac-2326">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2326">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-2327">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2327">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2328">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2328">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2329">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2329">'Identity'</span></span>
- <span data-ttu-id="db9ac-2330">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2330">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2331">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2331">'Razor'</span></span>
- <span data-ttu-id="db9ac-2332">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2332">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-2333">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2334">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2334">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2335">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2335">'Identity'</span></span>
- <span data-ttu-id="db9ac-2336">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2336">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2337">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2337">'Razor'</span></span>
- <span data-ttu-id="db9ac-2338">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2338">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-2339">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2340">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2340">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2341">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2341">'Identity'</span></span>
- <span data-ttu-id="db9ac-2342">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2342">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2343">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2343">'Razor'</span></span>
- <span data-ttu-id="db9ac-2344">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2344">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-2345">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2346">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2346">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2347">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2347">'Identity'</span></span>
- <span data-ttu-id="db9ac-2348">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2348">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2349">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2349">'Razor'</span></span>
- <span data-ttu-id="db9ac-2350">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2350">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-2351">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2352">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2353">'Identity'</span></span>
- <span data-ttu-id="db9ac-2354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2354">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2355">'Razor'</span></span>
- <span data-ttu-id="db9ac-2356">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2356">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-2357">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2358">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2358">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2359">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2359">'Identity'</span></span>
- <span data-ttu-id="db9ac-2360">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2360">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2361">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2361">'Razor'</span></span>
- <span data-ttu-id="db9ac-2362">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2362">'SignalR' uid:</span></span> 

<span data-ttu-id="db9ac-2363">-------------: | | 0 | value0 | | 1 | valeur1 | | 2 | valeur2 | | 3 | Value4 | | 4 | value5 |</span><span class="sxs-lookup"><span data-stu-id="db9ac-2363">-------------: | | 0                            | value0                       | | 1                            | value1                       | | 2                            | value2                       | | 3                            | value4                       | | 4                            | value5                       |</span></span>

<span data-ttu-id="db9ac-2364">L’index &num;3 dans l’objet lié contient les données de configuration pour la clé de configuration `array:4` et sa valeur de `value4`.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2364">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="db9ac-2365">Lorsque des données de configuration contenant un tableau sont liées, les index de tableau dans les clés de configuration sont simplement utilisés pour itérer les données de configuration lors de la création de l’objet.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2365">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="db9ac-2366">Une valeur null ne peut pas être conservée dans des données de configuration, et une entrée à valeur null n’est pas créée dans un objet lié quand un tableau dans des clés de configuration ignore un ou plusieurs index.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2366">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="db9ac-2367">L’élément de configuration manquant pour l’index &num;3 peut être fourni avant la liaison à l’instance `ArrayExample` par n’importe quel fournisseur de configuration qui génère la paire clé-valeur correcte dans la configuration.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2367">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="db9ac-2368">Si l’exemple inclut un Fournisseur de configuration JSON supplémentaire avec la paire clé-valeur manquante, `ArrayExample.Entries` correspond à l’intégralité du tableau de configuration :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2368">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="db9ac-2369">*missing_value.json* :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2369">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="db9ac-2370">Dans `ConfigureAppConfiguration` :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2370">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="db9ac-2371">La paire clé-valeur indiquée dans le tableau est chargée dans la configuration.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2371">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="db9ac-2372">Clé</span><span class="sxs-lookup"><span data-stu-id="db9ac-2372">Key</span></span>             | <span data-ttu-id="db9ac-2373">Valeur</span><span class="sxs-lookup"><span data-stu-id="db9ac-2373">Value</span></span>  |
| :---
<span data-ttu-id="db9ac-2374">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2374">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2375">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2375">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2376">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2376">'Identity'</span></span>
- <span data-ttu-id="db9ac-2377">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2377">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2378">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2378">'Razor'</span></span>
- <span data-ttu-id="db9ac-2379">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2379">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-2380">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2380">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2381">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2381">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2382">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2382">'Identity'</span></span>
- <span data-ttu-id="db9ac-2383">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2383">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2384">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2384">'Razor'</span></span>
- <span data-ttu-id="db9ac-2385">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2385">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-2386">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2386">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2387">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2387">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2388">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2388">'Identity'</span></span>
- <span data-ttu-id="db9ac-2389">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2389">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2390">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2390">'Razor'</span></span>
- <span data-ttu-id="db9ac-2391">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2391">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-2392">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2392">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2393">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2393">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2394">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2394">'Identity'</span></span>
- <span data-ttu-id="db9ac-2395">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2395">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2396">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2396">'Razor'</span></span>
- <span data-ttu-id="db9ac-2397">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2397">'SignalR' uid:</span></span> 

<span data-ttu-id="db9ac-2398">-------: | :----: | | Tableau : entrées : 3 | valeur3 |</span><span class="sxs-lookup"><span data-stu-id="db9ac-2398">-------: | :----: | | array:entries:3 | value3 |</span></span>

<span data-ttu-id="db9ac-2399">Si l’instance de classe `ArrayExample` est liée une fois que le Fournisseur de configuration JSON inclut l’entrée pour l’index &num;3, le tableau `ArrayExample.Entries` inclut la valeur.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2399">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="db9ac-2400">Index `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="db9ac-2400">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="db9ac-2401">`ArrayExample.Entries` Valeur</span><span class="sxs-lookup"><span data-stu-id="db9ac-2401">`ArrayExample.Entries` Value</span></span> |
| :---
<span data-ttu-id="db9ac-2402">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2402">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2403">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2403">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2404">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2404">'Identity'</span></span>
- <span data-ttu-id="db9ac-2405">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2405">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2406">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2406">'Razor'</span></span>
- <span data-ttu-id="db9ac-2407">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2407">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-2408">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2408">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2409">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2409">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2410">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2410">'Identity'</span></span>
- <span data-ttu-id="db9ac-2411">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2411">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2412">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2412">'Razor'</span></span>
- <span data-ttu-id="db9ac-2413">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2413">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-2414">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2414">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2415">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2415">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2416">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2416">'Identity'</span></span>
- <span data-ttu-id="db9ac-2417">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2417">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2418">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2418">'Razor'</span></span>
- <span data-ttu-id="db9ac-2419">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2419">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-2420">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2420">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2421">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2421">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2422">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2422">'Identity'</span></span>
- <span data-ttu-id="db9ac-2423">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2423">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2424">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2424">'Razor'</span></span>
- <span data-ttu-id="db9ac-2425">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2425">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-2426">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2426">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2427">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2427">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2428">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2428">'Identity'</span></span>
- <span data-ttu-id="db9ac-2429">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2429">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2430">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2430">'Razor'</span></span>
- <span data-ttu-id="db9ac-2431">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2431">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-2432">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2432">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2433">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2433">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2434">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2434">'Identity'</span></span>
- <span data-ttu-id="db9ac-2435">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2435">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2436">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2436">'Razor'</span></span>
- <span data-ttu-id="db9ac-2437">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2437">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-2438">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2438">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2439">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2439">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2440">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2440">'Identity'</span></span>
- <span data-ttu-id="db9ac-2441">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2441">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2442">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2442">'Razor'</span></span>
- <span data-ttu-id="db9ac-2443">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2443">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-2444">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2444">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2445">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2445">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2446">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2446">'Identity'</span></span>
- <span data-ttu-id="db9ac-2447">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2447">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2448">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2448">'Razor'</span></span>
- <span data-ttu-id="db9ac-2449">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2449">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-2450">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2450">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2451">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2451">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2452">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2452">'Identity'</span></span>
- <span data-ttu-id="db9ac-2453">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2453">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2454">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2454">'Razor'</span></span>
- <span data-ttu-id="db9ac-2455">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2455">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-2456">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2456">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2457">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2457">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2458">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2458">'Identity'</span></span>
- <span data-ttu-id="db9ac-2459">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2459">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2460">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2460">'Razor'</span></span>
- <span data-ttu-id="db9ac-2461">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2461">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-2462">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2462">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2463">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2463">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2464">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2464">'Identity'</span></span>
- <span data-ttu-id="db9ac-2465">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2465">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2466">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2466">'Razor'</span></span>
- <span data-ttu-id="db9ac-2467">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2467">'SignalR' uid:</span></span> 

<span data-ttu-id="db9ac-2468">-------------: | :---Title : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2468">-------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2469">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2469">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2470">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2470">'Identity'</span></span>
- <span data-ttu-id="db9ac-2471">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2471">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2472">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2472">'Razor'</span></span>
- <span data-ttu-id="db9ac-2473">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2473">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-2474">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2474">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2475">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2475">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2476">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2476">'Identity'</span></span>
- <span data-ttu-id="db9ac-2477">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2477">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2478">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2478">'Razor'</span></span>
- <span data-ttu-id="db9ac-2479">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2479">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-2480">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2480">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2481">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2481">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2482">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2482">'Identity'</span></span>
- <span data-ttu-id="db9ac-2483">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2483">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2484">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2484">'Razor'</span></span>
- <span data-ttu-id="db9ac-2485">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2485">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-2486">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2486">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2487">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2487">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2488">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2488">'Identity'</span></span>
- <span data-ttu-id="db9ac-2489">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2489">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2490">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2490">'Razor'</span></span>
- <span data-ttu-id="db9ac-2491">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2491">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-2492">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2492">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2493">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2493">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2494">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2494">'Identity'</span></span>
- <span data-ttu-id="db9ac-2495">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2495">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2496">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2496">'Razor'</span></span>
- <span data-ttu-id="db9ac-2497">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2497">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-2498">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2498">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2499">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2499">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2500">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2500">'Identity'</span></span>
- <span data-ttu-id="db9ac-2501">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2501">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2502">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2502">'Razor'</span></span>
- <span data-ttu-id="db9ac-2503">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2503">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-2504">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2504">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2505">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2505">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2506">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2506">'Identity'</span></span>
- <span data-ttu-id="db9ac-2507">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2507">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2508">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2508">'Razor'</span></span>
- <span data-ttu-id="db9ac-2509">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2509">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-2510">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2510">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2511">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2511">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2512">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2512">'Identity'</span></span>
- <span data-ttu-id="db9ac-2513">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2513">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2514">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2514">'Razor'</span></span>
- <span data-ttu-id="db9ac-2515">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2515">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-2516">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2516">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2517">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2517">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2518">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2518">'Identity'</span></span>
- <span data-ttu-id="db9ac-2519">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2519">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2520">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2520">'Razor'</span></span>
- <span data-ttu-id="db9ac-2521">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2521">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-2522">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2522">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2523">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2523">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2524">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2524">'Identity'</span></span>
- <span data-ttu-id="db9ac-2525">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2525">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2526">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2526">'Razor'</span></span>
- <span data-ttu-id="db9ac-2527">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2527">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-2528">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2528">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2529">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2529">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2530">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2530">'Identity'</span></span>
- <span data-ttu-id="db9ac-2531">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2531">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2532">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2532">'Razor'</span></span>
- <span data-ttu-id="db9ac-2533">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2533">'SignalR' uid:</span></span> 

<span data-ttu-id="db9ac-2534">-------------: | | 0 | value0 | | 1 | valeur1 | | 2 | valeur2 | | 3 | valeur3 | | 4 | Value4 | | 5 | value5 |</span><span class="sxs-lookup"><span data-stu-id="db9ac-2534">-------------: | | 0                            | value0                       | | 1                            | value1                       | | 2                            | value2                       | | 3                            | value3                       | | 4                            | value4                       | | 5                            | value5                       |</span></span>

<span data-ttu-id="db9ac-2535">**Traitement de tableau JSON**</span><span class="sxs-lookup"><span data-stu-id="db9ac-2535">**JSON array processing**</span></span>

<span data-ttu-id="db9ac-2536">Si un fichier JSON contient un tableau, les clés de configuration sont créés pour les éléments du tableau avec un index de section basé sur zéro.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2536">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="db9ac-2537">Dans le fichier de configuration suivant, `subsection` est un tableau :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2537">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="db9ac-2538">Le Fournisseur de configuration JSON lit les données de configuration dans les paires clé-valeur suivantes :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2538">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="db9ac-2539">Clé</span><span class="sxs-lookup"><span data-stu-id="db9ac-2539">Key</span></span>                     | <span data-ttu-id="db9ac-2540">Valeur</span><span class="sxs-lookup"><span data-stu-id="db9ac-2540">Value</span></span>  |
| ---
<span data-ttu-id="db9ac-2541">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2541">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2542">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2542">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2543">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2543">'Identity'</span></span>
- <span data-ttu-id="db9ac-2544">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2544">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2545">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2545">'Razor'</span></span>
- <span data-ttu-id="db9ac-2546">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2546">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-2547">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2547">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2548">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2548">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2549">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2549">'Identity'</span></span>
- <span data-ttu-id="db9ac-2550">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2550">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2551">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2551">'Razor'</span></span>
- <span data-ttu-id="db9ac-2552">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2552">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-2553">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2553">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2554">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2554">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2555">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2555">'Identity'</span></span>
- <span data-ttu-id="db9ac-2556">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2556">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2557">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2557">'Razor'</span></span>
- <span data-ttu-id="db9ac-2558">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2558">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-2559">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2559">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2560">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2560">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2561">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2561">'Identity'</span></span>
- <span data-ttu-id="db9ac-2562">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2562">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2563">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2563">'Razor'</span></span>
- <span data-ttu-id="db9ac-2564">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2564">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-2565">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2565">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2566">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2566">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2567">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2567">'Identity'</span></span>
- <span data-ttu-id="db9ac-2568">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2568">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2569">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2569">'Razor'</span></span>
- <span data-ttu-id="db9ac-2570">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2570">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-2571">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2571">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2572">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2572">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2573">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2573">'Identity'</span></span>
- <span data-ttu-id="db9ac-2574">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2574">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2575">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2575">'Razor'</span></span>
- <span data-ttu-id="db9ac-2576">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2576">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-2577">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2577">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2578">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2578">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2579">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2579">'Identity'</span></span>
- <span data-ttu-id="db9ac-2580">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2580">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2581">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2581">'Razor'</span></span>
- <span data-ttu-id="db9ac-2582">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2582">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-2583">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2583">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2584">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2584">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2585">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2585">'Identity'</span></span>
- <span data-ttu-id="db9ac-2586">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2586">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2587">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2587">'Razor'</span></span>
- <span data-ttu-id="db9ac-2588">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2588">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-2589">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2589">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2590">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2590">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2591">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2591">'Identity'</span></span>
- <span data-ttu-id="db9ac-2592">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2592">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2593">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2593">'Razor'</span></span>
- <span data-ttu-id="db9ac-2594">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2594">'SignalR' uid:</span></span> 

<span data-ttu-id="db9ac-2595">------------ | :----: | | json_array : clé | valeura | | json_array : sous-section : 0 | valueB | | json_array : sous-section : 1 | valueC | | json_array : sous-section : 2 | Valeur |</span><span class="sxs-lookup"><span data-stu-id="db9ac-2595">------------ | :----: | | json_array:key          | valueA | | json_array:subsection:0 | valueB | | json_array:subsection:1 | valueC | | json_array:subsection:2 | valueD |</span></span>

<span data-ttu-id="db9ac-2596">Dans l’exemple d’application, la classe POCO suivante est disponible pour lier les paires clé-valeur de configuration :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2596">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="db9ac-2597">Après la liaison, `JsonArrayExample.Key` contient la valeur `valueA`.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2597">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="db9ac-2598">Les valeurs de la sous-section sont stockées dans la propriété de tableau POCO, `Subsection`.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2598">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="db9ac-2599">Index `JsonArrayExample.Subsection`</span><span class="sxs-lookup"><span data-stu-id="db9ac-2599">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="db9ac-2600">`JsonArrayExample.Subsection` Valeur</span><span class="sxs-lookup"><span data-stu-id="db9ac-2600">`JsonArrayExample.Subsection` Value</span></span> |
| :---
<span data-ttu-id="db9ac-2601">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2601">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2602">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2602">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2603">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2603">'Identity'</span></span>
- <span data-ttu-id="db9ac-2604">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2604">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2605">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2605">'Razor'</span></span>
- <span data-ttu-id="db9ac-2606">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2606">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-2607">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2607">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2608">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2608">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2609">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2609">'Identity'</span></span>
- <span data-ttu-id="db9ac-2610">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2610">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2611">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2611">'Razor'</span></span>
- <span data-ttu-id="db9ac-2612">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2612">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-2613">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2613">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2614">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2614">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2615">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2615">'Identity'</span></span>
- <span data-ttu-id="db9ac-2616">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2616">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2617">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2617">'Razor'</span></span>
- <span data-ttu-id="db9ac-2618">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2618">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-2619">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2619">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2620">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2620">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2621">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2621">'Identity'</span></span>
- <span data-ttu-id="db9ac-2622">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2622">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2623">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2623">'Razor'</span></span>
- <span data-ttu-id="db9ac-2624">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2624">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-2625">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2625">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2626">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2626">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2627">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2627">'Identity'</span></span>
- <span data-ttu-id="db9ac-2628">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2628">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2629">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2629">'Razor'</span></span>
- <span data-ttu-id="db9ac-2630">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2630">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-2631">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2631">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2632">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2632">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2633">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2633">'Identity'</span></span>
- <span data-ttu-id="db9ac-2634">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2634">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2635">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2635">'Razor'</span></span>
- <span data-ttu-id="db9ac-2636">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2636">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-2637">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2637">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2638">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2638">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2639">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2639">'Identity'</span></span>
- <span data-ttu-id="db9ac-2640">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2640">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2641">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2641">'Razor'</span></span>
- <span data-ttu-id="db9ac-2642">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2642">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-2643">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2643">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2644">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2644">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2645">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2645">'Identity'</span></span>
- <span data-ttu-id="db9ac-2646">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2646">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2647">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2647">'Razor'</span></span>
- <span data-ttu-id="db9ac-2648">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2648">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-2649">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2649">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2650">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2650">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2651">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2651">'Identity'</span></span>
- <span data-ttu-id="db9ac-2652">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2652">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2653">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2653">'Razor'</span></span>
- <span data-ttu-id="db9ac-2654">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2654">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-2655">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2655">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2656">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2656">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2657">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2657">'Identity'</span></span>
- <span data-ttu-id="db9ac-2658">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2658">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2659">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2659">'Razor'</span></span>
- <span data-ttu-id="db9ac-2660">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2660">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-2661">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2661">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2662">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2662">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2663">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2663">'Identity'</span></span>
- <span data-ttu-id="db9ac-2664">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2664">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2665">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2665">'Razor'</span></span>
- <span data-ttu-id="db9ac-2666">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2666">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-2667">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2667">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2668">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2668">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2669">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2669">'Identity'</span></span>
- <span data-ttu-id="db9ac-2670">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2670">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2671">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2671">'Razor'</span></span>
- <span data-ttu-id="db9ac-2672">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2672">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-2673">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2673">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2674">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2674">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2675">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2675">'Identity'</span></span>
- <span data-ttu-id="db9ac-2676">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2676">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2677">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2677">'Razor'</span></span>
- <span data-ttu-id="db9ac-2678">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2678">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-2679">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2679">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2680">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2680">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2681">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2681">'Identity'</span></span>
- <span data-ttu-id="db9ac-2682">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2682">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2683">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2683">'Razor'</span></span>
- <span data-ttu-id="db9ac-2684">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2684">'SignalR' uid:</span></span> 

<span data-ttu-id="db9ac-2685">-----------------: | :---Title : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2685">-----------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2686">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2686">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2687">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2687">'Identity'</span></span>
- <span data-ttu-id="db9ac-2688">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2688">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2689">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2689">'Razor'</span></span>
- <span data-ttu-id="db9ac-2690">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2690">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-2691">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2691">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2692">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2692">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2693">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2693">'Identity'</span></span>
- <span data-ttu-id="db9ac-2694">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2694">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2695">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2695">'Razor'</span></span>
- <span data-ttu-id="db9ac-2696">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2696">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-2697">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2697">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2698">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2698">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2699">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2699">'Identity'</span></span>
- <span data-ttu-id="db9ac-2700">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2700">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2701">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2701">'Razor'</span></span>
- <span data-ttu-id="db9ac-2702">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2702">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-2703">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2703">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2704">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2704">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2705">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2705">'Identity'</span></span>
- <span data-ttu-id="db9ac-2706">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2706">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2707">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2707">'Razor'</span></span>
- <span data-ttu-id="db9ac-2708">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2708">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-2709">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2709">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2710">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2710">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2711">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2711">'Identity'</span></span>
- <span data-ttu-id="db9ac-2712">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2712">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2713">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2713">'Razor'</span></span>
- <span data-ttu-id="db9ac-2714">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2714">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-2715">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2715">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2716">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2716">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2717">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2717">'Identity'</span></span>
- <span data-ttu-id="db9ac-2718">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2718">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2719">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2719">'Razor'</span></span>
- <span data-ttu-id="db9ac-2720">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2720">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-2721">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2721">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2722">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2722">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2723">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2723">'Identity'</span></span>
- <span data-ttu-id="db9ac-2724">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2724">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2725">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2725">'Razor'</span></span>
- <span data-ttu-id="db9ac-2726">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2726">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-2727">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2727">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2728">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2728">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2729">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2729">'Identity'</span></span>
- <span data-ttu-id="db9ac-2730">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2730">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2731">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2731">'Razor'</span></span>
- <span data-ttu-id="db9ac-2732">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2732">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-2733">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2733">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2734">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2734">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2735">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2735">'Identity'</span></span>
- <span data-ttu-id="db9ac-2736">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2736">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2737">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2737">'Razor'</span></span>
- <span data-ttu-id="db9ac-2738">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2738">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-2739">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2739">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2740">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2740">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2741">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2741">'Identity'</span></span>
- <span data-ttu-id="db9ac-2742">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2742">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2743">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2743">'Razor'</span></span>
- <span data-ttu-id="db9ac-2744">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2744">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-2745">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2745">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2746">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2746">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2747">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2747">'Identity'</span></span>
- <span data-ttu-id="db9ac-2748">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2748">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2749">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2749">'Razor'</span></span>
- <span data-ttu-id="db9ac-2750">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2750">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-2751">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2751">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2752">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2752">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2753">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2753">'Identity'</span></span>
- <span data-ttu-id="db9ac-2754">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2754">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2755">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2755">'Razor'</span></span>
- <span data-ttu-id="db9ac-2756">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2756">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-2757">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2757">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2758">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2758">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2759">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2759">'Identity'</span></span>
- <span data-ttu-id="db9ac-2760">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2760">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2761">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2761">'Razor'</span></span>
- <span data-ttu-id="db9ac-2762">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2762">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db9ac-2763">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2763">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db9ac-2764">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2764">'Blazor'</span></span>
- <span data-ttu-id="db9ac-2765">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2765">'Identity'</span></span>
- <span data-ttu-id="db9ac-2766">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2766">'Let's Encrypt'</span></span>
- <span data-ttu-id="db9ac-2767">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db9ac-2767">'Razor'</span></span>
- <span data-ttu-id="db9ac-2768">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2768">'SignalR' uid:</span></span> 

<span data-ttu-id="db9ac-2769">-----------------: | | 0 | valueB | | 1 | valueC | | 2 | Valeur |</span><span class="sxs-lookup"><span data-stu-id="db9ac-2769">-----------------: | | 0                                   | valueB                              | | 1                                   | valueC                              | | 2                                   | valueD                              |</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="db9ac-2770">Fournisseur de configuration personnalisé</span><span class="sxs-lookup"><span data-stu-id="db9ac-2770">Custom configuration provider</span></span>

<span data-ttu-id="db9ac-2771">L’exemple d’application montre comment créer un fournisseur de configuration de base qui lit les paires clé-valeur de configuration à partir d’une base de données à l’aide [d’Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="db9ac-2771">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="db9ac-2772">Le fournisseur présente les caractéristiques suivantes :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2772">The provider has the following characteristics:</span></span>

* <span data-ttu-id="db9ac-2773">La base de données en mémoire EF est utilisée à des fins de démonstration.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2773">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="db9ac-2774">Pour utiliser une base de données qui nécessite une chaîne de connexion, implémentez un autre `ConfigurationBuilder` pour fournir la chaîne de connexion à partir d’un autre fournisseur de configuration.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2774">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="db9ac-2775">Le fournisseur lit une table de base de données dans la configuration au démarrage.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2775">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="db9ac-2776">Le fournisseur n’interroge pas la base de données par clé.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2776">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="db9ac-2777">Le rechargement en cas de changement n’est pas implémenté, par conséquent, la mise à jour la base de données après le démarrage de l’application n’a aucun effet sur la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2777">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="db9ac-2778">Définissez une entité `EFConfigurationValue` pour le stockage des valeurs de configuration dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2778">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="db9ac-2779">*Models/EFConfigurationValue.cs* :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2779">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="db9ac-2780">Ajoutez un `EFConfigurationContext` pour stocker les valeurs configurées et y accéder.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2780">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="db9ac-2781">*EFConfigurationProvider/EFConfigurationContext.cs* :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2781">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="db9ac-2782">Créez une classe qui implémente <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2782">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="db9ac-2783">*EFConfigurationProvider/EFConfigurationSource.cs* :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2783">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="db9ac-2784">Créez le fournisseur de configuration personnalisé en héritant de <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2784">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="db9ac-2785">Le fournisseur de configuration initialise la base de données quand elle est vide.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2785">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="db9ac-2786">*EFConfigurationProvider/EFConfigurationProvider.cs* :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2786">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="db9ac-2787">Une méthode d’extension `AddEFConfiguration` permet d’ajouter la source de configuration à un `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2787">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="db9ac-2788">*Extensions/EntityFrameworkExtensions.cs* :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2788">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="db9ac-2789">Le code suivant montre comment utiliser le `EFConfigurationProvider` personnalisé dans *Program.cs* :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2789">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="db9ac-2790">Accéder à la configuration lors du démarrage</span><span class="sxs-lookup"><span data-stu-id="db9ac-2790">Access configuration during startup</span></span>

<span data-ttu-id="db9ac-2791">Injectez `IConfiguration` dans le constructeur `Startup` pour accéder aux valeurs de configuration dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2791">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="db9ac-2792">Pour accéder à la configuration dans `Startup.Configure`, injectez `IConfiguration` directement dans la méthode ou utilisez l’instance à partir du constructeur :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2792">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

```csharp
public class Startup
{
    private readonly IConfiguration _config;

    public Startup(IConfiguration config)
    {
        _config = config;
    }

    public void ConfigureServices(IServiceCollection services)
    {
        var value = _config["key"];
    }

    public void Configure(IApplicationBuilder app, IConfiguration config)
    {
        var value = config["key"];
    }
}
```

<span data-ttu-id="db9ac-2793">Pour obtenir un exemple d’accès à la configuration à l’aide des méthodes pratiques de démarrage, consultez [Démarrage de l’application : méthodes pratiques](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="db9ac-2793">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="db9ac-2794">Configuration de l’accès dans une Razor page pages ou une vue MVC</span><span class="sxs-lookup"><span data-stu-id="db9ac-2794">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="db9ac-2795">Pour accéder aux paramètres de configuration dans une Razor page pages ou une vue MVC, ajoutez une [directive using](xref:mvc/views/razor#using) ([référence C# : directive using](/dotnet/csharp/language-reference/keywords/using-directive)) pour l' [espace de noms Microsoft. extensions. Configuration](xref:Microsoft.Extensions.Configuration) et injectez <xref:Microsoft.Extensions.Configuration.IConfiguration> dans la page ou la vue.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2795">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="db9ac-2796">Dans une Razor page pages :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2796">In a Razor Pages page:</span></span>

```cshtml
@page
@model IndexModel
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Index Page</title>
</head>
<body>
    <h1>Access configuration in a Razor Pages page</h1>
    <p>Configuration value for 'key': @Configuration["key"]</p>
</body>
</html>
```

<span data-ttu-id="db9ac-2797">Dans une vue MVC :</span><span class="sxs-lookup"><span data-stu-id="db9ac-2797">In an MVC view:</span></span>

```cshtml
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Index View</title>
</head>
<body>
    <h1>Access configuration in an MVC view</h1>
    <p>Configuration value for 'key': @Configuration["key"]</p>
</body>
</html>
```

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="db9ac-2798">Ajouter la configuration à partir d’un assembly externe</span><span class="sxs-lookup"><span data-stu-id="db9ac-2798">Add configuration from an external assembly</span></span>

<span data-ttu-id="db9ac-2799">Une implémentation de <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> permet d’ajouter des améliorations à une application au démarrage à partir d’un assembly externe, en dehors de la classe `Startup` de l’application.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2799">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="db9ac-2800">Pour plus d'informations, consultez <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="db9ac-2800">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="db9ac-2801">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="db9ac-2801">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end
