---
title: Configuration dans ASP.NET Core
author: rick-anderson
description: Découvrez comment utiliser l’API de configuration pour configurer une application ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 3/29/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/configuration/index
ms.openlocfilehash: c6b77f20792c32a697fff8d1d78ef3c2536c5f8f
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722895"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="6f99c-103">Configuration dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6f99c-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="6f99c-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT) et [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="6f99c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6f99c-105">La configuration dans ASP.NET Core est effectuée à l’aide d’un ou de plusieurs [fournisseurs de configuration](#cp).</span><span class="sxs-lookup"><span data-stu-id="6f99c-105">Configuration in ASP.NET Core is performed using one or more [configuration providers](#cp).</span></span> <span data-ttu-id="6f99c-106">Les fournisseurs de configuration lisent les données de configuration des paires clé-valeur à l’aide d’une variété de sources de configuration :</span><span class="sxs-lookup"><span data-stu-id="6f99c-106">Configuration providers read configuration data from key-value pairs using a variety of configuration sources:</span></span>

* <span data-ttu-id="6f99c-107">Fichiers de paramètres, tels que *appsettings.jssur*</span><span class="sxs-lookup"><span data-stu-id="6f99c-107">Settings files, such as *appsettings.json*</span></span>
* <span data-ttu-id="6f99c-108">Variables d'environnement</span><span class="sxs-lookup"><span data-stu-id="6f99c-108">Environment variables</span></span>
* <span data-ttu-id="6f99c-109">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="6f99c-109">Azure Key Vault</span></span>
* <span data-ttu-id="6f99c-110">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="6f99c-110">Azure App Configuration</span></span>
* <span data-ttu-id="6f99c-111">Arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="6f99c-111">Command-line arguments</span></span>
* <span data-ttu-id="6f99c-112">Fournisseurs personnalisés, installés ou créés</span><span class="sxs-lookup"><span data-stu-id="6f99c-112">Custom providers, installed or created</span></span>
* <span data-ttu-id="6f99c-113">Fichiers de répertoire</span><span class="sxs-lookup"><span data-stu-id="6f99c-113">Directory files</span></span>
* <span data-ttu-id="6f99c-114">Objets .NET en mémoire</span><span class="sxs-lookup"><span data-stu-id="6f99c-114">In-memory .NET objects</span></span>

<span data-ttu-id="6f99c-115">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6f99c-115">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="default"></a>

## <a name="default-configuration"></a><span data-ttu-id="6f99c-116">Configuration par défaut</span><span class="sxs-lookup"><span data-stu-id="6f99c-116">Default configuration</span></span>

<span data-ttu-id="6f99c-117">ASP.NET Core les applications Web créées avec [dotnet New](/dotnet/core/tools/dotnet-new) ou Visual Studio génèrent le code suivant :</span><span class="sxs-lookup"><span data-stu-id="6f99c-117">ASP.NET Core web apps created with [dotnet new](/dotnet/core/tools/dotnet-new) or Visual Studio generate the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet&highlight=9)]

 <span data-ttu-id="6f99c-118"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> fournit la configuration par défaut de l’application dans l’ordre suivant :</span><span class="sxs-lookup"><span data-stu-id="6f99c-118"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> provides default configuration for the app in the following order:</span></span>

1. <span data-ttu-id="6f99c-119">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) : ajoute un existant `IConfiguration` en tant que source.</span><span class="sxs-lookup"><span data-stu-id="6f99c-119">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) :  Adds an existing `IConfiguration` as a source.</span></span> <span data-ttu-id="6f99c-120">Dans le cas de configuration par défaut, ajoute la configuration d' [hôte](#hvac) et la définit en tant que première source de la configuration de l' _application_ .</span><span class="sxs-lookup"><span data-stu-id="6f99c-120">In the default configuration case, adds the [host](#hvac) configuration and setting it as the first source for the _app_ configuration.</span></span>
1. <span data-ttu-id="6f99c-121">[appsettings.jssur](#appsettingsjson) l’utilisation du [fournisseur de configuration JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="6f99c-121">[appsettings.json](#appsettingsjson) using the [JSON configuration provider](#file-configuration-provider).</span></span>
1. <span data-ttu-id="6f99c-122">*appSettings.* `Environment` *. JSON* à l’aide du [fournisseur de configuration JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="6f99c-122">*appsettings.*`Environment`*.json* using the [JSON configuration provider](#file-configuration-provider).</span></span> <span data-ttu-id="6f99c-123">Par exemple, *appSettings*. ***Production***. *JSON* et *appSettings*. ***Développement***. *JSON*.</span><span class="sxs-lookup"><span data-stu-id="6f99c-123">For example, *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json*.</span></span>
1. <span data-ttu-id="6f99c-124">[Secrets d’application](xref:security/app-secrets) lorsque l’application s’exécute dans l' `Development` environnement.</span><span class="sxs-lookup"><span data-stu-id="6f99c-124">[App secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
1. <span data-ttu-id="6f99c-125">Variables d’environnement à l’aide du [fournisseur de configuration des variables d’environnement](#evcp).</span><span class="sxs-lookup"><span data-stu-id="6f99c-125">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="6f99c-126">Arguments de ligne de commande à l’aide du [fournisseur de configuration de ligne de commande](#command-line).</span><span class="sxs-lookup"><span data-stu-id="6f99c-126">Command-line arguments using the [Command-line configuration provider](#command-line).</span></span>

<span data-ttu-id="6f99c-127">Les fournisseurs de configuration ajoutés ultérieurement remplacent les paramètres de clé précédents.</span><span class="sxs-lookup"><span data-stu-id="6f99c-127">Configuration providers that are added later override previous key settings.</span></span> <span data-ttu-id="6f99c-128">Par exemple, si `MyKey` est défini dans *appsettings.jssur* et dans l’environnement, la valeur d’environnement est utilisée.</span><span class="sxs-lookup"><span data-stu-id="6f99c-128">For example, if `MyKey` is set in both *appsettings.json* and the environment, the environment value is used.</span></span> <span data-ttu-id="6f99c-129">À l’aide des fournisseurs de configuration par défaut, le  [fournisseur de configuration de ligne de commande](#clcp) remplace tous les autres fournisseurs.</span><span class="sxs-lookup"><span data-stu-id="6f99c-129">Using the default configuration providers, the  [Command-line configuration provider](#clcp) overrides all other providers.</span></span>

<span data-ttu-id="6f99c-130">Pour plus d’informations sur `CreateDefaultBuilder` , consultez [paramètres par défaut du générateur](xref:fundamentals/host/generic-host#default-builder-settings).</span><span class="sxs-lookup"><span data-stu-id="6f99c-130">For more information on `CreateDefaultBuilder`, see [Default builder settings](xref:fundamentals/host/generic-host#default-builder-settings).</span></span>

<span data-ttu-id="6f99c-131">Le code suivant affiche les fournisseurs de configuration activés dans l’ordre dans lequel ils ont été ajoutés :</span><span class="sxs-lookup"><span data-stu-id="6f99c-131">The following code displays the enabled configuration providers in the order they were added:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Index2.cshtml.cs?name=snippet)]

### <a name="appsettingsjson"></a><span data-ttu-id="6f99c-132">appsettings.json</span><span class="sxs-lookup"><span data-stu-id="6f99c-132">appsettings.json</span></span>

<span data-ttu-id="6f99c-133">Prenez en compte les *appsettings.jssuivantes sur* le fichier :</span><span class="sxs-lookup"><span data-stu-id="6f99c-133">Consider the following *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="6f99c-134">Le code suivant de l' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) affiche plusieurs des paramètres de configuration précédents :</span><span class="sxs-lookup"><span data-stu-id="6f99c-134">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="6f99c-135">La configuration par défaut <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> charge dans l’ordre suivant :</span><span class="sxs-lookup"><span data-stu-id="6f99c-135">The default <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration in the following order:</span></span>

1. <span data-ttu-id="6f99c-136">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="6f99c-136">*appsettings.json*</span></span>
1. <span data-ttu-id="6f99c-137">*appSettings.* `Environment` *. JSON* : par exemple, *appSettings*. ***Production***. *JSON* et *appSettings*. ***Développement***. fichiers *JSON* .</span><span class="sxs-lookup"><span data-stu-id="6f99c-137">*appsettings.*`Environment`*.json* : For example, the *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json* files.</span></span> <span data-ttu-id="6f99c-138">La version de l’environnement du fichier est chargée à partir de [IHostingEnvironment. EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="6f99c-138">The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="6f99c-139">Pour plus d'informations, consultez <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="6f99c-139">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="6f99c-140">*appSettings*. `Environment` . les valeurs *JSON* remplacent les clés dans *appsettings.js*.</span><span class="sxs-lookup"><span data-stu-id="6f99c-140">*appsettings*.`Environment`.*json* values override keys in *appsettings.json*.</span></span> <span data-ttu-id="6f99c-141">Par exemple, par défaut :</span><span class="sxs-lookup"><span data-stu-id="6f99c-141">For example, by default:</span></span>

* <span data-ttu-id="6f99c-142">Dans le développement, *appSettings*. ***Développement***. la configuration *JSON* remplace les valeurs trouvées dans *appsettings.jssur*.</span><span class="sxs-lookup"><span data-stu-id="6f99c-142">In development, *appsettings*.***Development***.*json* configuration overwrites values found in *appsettings.json*.</span></span>
* <span data-ttu-id="6f99c-143">En production, *appSettings*. ***Production***. la configuration *JSON* remplace les valeurs trouvées dans *appsettings.jssur*.</span><span class="sxs-lookup"><span data-stu-id="6f99c-143">In production, *appsettings*.***Production***.*json* configuration overwrites values found in *appsettings.json*.</span></span> <span data-ttu-id="6f99c-144">Par exemple, lors du déploiement de l’application sur Azure.</span><span class="sxs-lookup"><span data-stu-id="6f99c-144">For example, when deploying the app to Azure.</span></span>

<a name="optpat"></a>

### <a name="bind-hierarchical-configuration-data-using-the-options-pattern"></a><span data-ttu-id="6f99c-145">Lier des données de configuration hiérarchiques à l’aide du modèle options</span><span class="sxs-lookup"><span data-stu-id="6f99c-145">Bind hierarchical configuration data using the options pattern</span></span>

[!INCLUDE[](~/includes/bind.md)]

<span data-ttu-id="6f99c-146">À l’aide de la configuration [par défaut](#default) , le *appsettings.jssur* et *appSettings.* `Environment` les fichiers *. JSON* sont activés avec [reloadOnChange : true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span><span class="sxs-lookup"><span data-stu-id="6f99c-146">Using the [default](#default) configuration, the *appsettings.json* and *appsettings.*`Environment`*.json* files are enabled with [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span></span> <span data-ttu-id="6f99c-147">Les modifications apportées à la *appsettings.jssur* et *appSettings.* `Environment` le fichier *. JSON* ***après*** le démarrage de l’application est lu par le [fournisseur de configuration JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="6f99c-147">Changes made to the *appsettings.json* and *appsettings.*`Environment`*.json* file ***after*** the app starts are read by the [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="6f99c-148">Pour plus d’informations sur l’ajout de fichiers de configuration JSON supplémentaires, consultez [fournisseur de configuration JSON](#jcp) dans ce document.</span><span class="sxs-lookup"><span data-stu-id="6f99c-148">See [JSON configuration provider](#jcp) in this document for information on adding additional JSON configuration files.</span></span>

## <a name="combining-service-collection"></a><span data-ttu-id="6f99c-149">Combinaison de la collection de services</span><span class="sxs-lookup"><span data-stu-id="6f99c-149">Combining service collection</span></span>

[!INCLUDE[](~/includes/combine-di.md)]

<a name="security"></a>

## <a name="security-and-secret-manager"></a><span data-ttu-id="6f99c-150">Responsable de la sécurité et du secret</span><span class="sxs-lookup"><span data-stu-id="6f99c-150">Security and secret manager</span></span>

<span data-ttu-id="6f99c-151">Instructions relatives aux données de configuration :</span><span class="sxs-lookup"><span data-stu-id="6f99c-151">Configuration data guidelines:</span></span>

* <span data-ttu-id="6f99c-152">Ne stockez jamais des mots de passe ou d’autres données sensibles dans le code du fournisseur de configuration ou dans les fichiers de configuration en texte clair.</span><span class="sxs-lookup"><span data-stu-id="6f99c-152">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span> <span data-ttu-id="6f99c-153">Le [Gestionnaire de secret](xref:security/app-secrets) peut être utilisé pour stocker les secrets en développement.</span><span class="sxs-lookup"><span data-stu-id="6f99c-153">The [Secret manager](xref:security/app-secrets) can be used to store secrets in development.</span></span>
* <span data-ttu-id="6f99c-154">N’utilisez aucun secret de production dans les environnements de développement ou de test.</span><span class="sxs-lookup"><span data-stu-id="6f99c-154">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="6f99c-155">Spécifiez les secrets en dehors du projet afin qu’ils ne puissent pas être validés par inadvertance dans un référentiel de code source.</span><span class="sxs-lookup"><span data-stu-id="6f99c-155">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="6f99c-156">Par [défaut](#default), le [Gestionnaire de secret](xref:security/app-secrets) lit les paramètres de configuration après *appsettings.jssur* et *appSettings.* `Environment` *. JSON*.</span><span class="sxs-lookup"><span data-stu-id="6f99c-156">By [default](#default), [Secret manager](xref:security/app-secrets) reads configuration settings after *appsettings.json* and *appsettings.*`Environment`*.json*.</span></span>

<span data-ttu-id="6f99c-157">Pour plus d’informations sur le stockage des mots de passe ou d’autres données sensibles :</span><span class="sxs-lookup"><span data-stu-id="6f99c-157">For more information on storing passwords or other sensitive data:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="6f99c-158"><xref:security/app-secrets>: Fournit des conseils sur l’utilisation de variables d’environnement pour stocker des données sensibles.</span><span class="sxs-lookup"><span data-stu-id="6f99c-158"><xref:security/app-secrets>:  Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="6f99c-159">Le gestionnaire de secret utilise le [fournisseur de configuration de fichiers](#fcp) pour stocker les secrets de l’utilisateur dans un fichier JSON sur le système local.</span><span class="sxs-lookup"><span data-stu-id="6f99c-159">The Secret Manager uses the [File configuration provider](#fcp) to store user secrets in a JSON file on the local system.</span></span>

<span data-ttu-id="6f99c-160">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) stocke en toute sécurité des secrets d’application pour les applications ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6f99c-160">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="6f99c-161">Pour plus d'informations, consultez <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="6f99c-161">For more information, see <xref:security/key-vault-configuration>.</span></span>

<a name="evcp"></a>

## <a name="environment-variables"></a><span data-ttu-id="6f99c-162">Variables d'environnement</span><span class="sxs-lookup"><span data-stu-id="6f99c-162">Environment variables</span></span>

<span data-ttu-id="6f99c-163">À l’aide de la configuration [par défaut](#default) , le <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> charge la configuration à partir des paires clé-valeur de variable d’environnement après la lecture *appsettings.jssur*, *appSettings.* `Environment` *. JSON*et le [Gestionnaire de secret](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="6f99c-163">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs after reading *appsettings.json*, *appsettings.*`Environment`*.json*, and [Secret manager](xref:security/app-secrets).</span></span> <span data-ttu-id="6f99c-164">Par conséquent, les valeurs de clés lues à partir de l’environnement remplacent les valeurs lues à partir de *appsettings.jssur*, *appSettings.* `Environment` *. JSON*et le gestionnaire de secret.</span><span class="sxs-lookup"><span data-stu-id="6f99c-164">Therefore, key values read from the environment override values read from *appsettings.json*, *appsettings.*`Environment`*.json*, and Secret manager.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="6f99c-165">Les `set` commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="6f99c-165">The following `set` commands:</span></span>

* <span data-ttu-id="6f99c-166">Définissez les clés et les valeurs d’environnement de l' [exemple précédent](#appsettingsjson) sur Windows.</span><span class="sxs-lookup"><span data-stu-id="6f99c-166">Set the environment keys and values of the [preceding example](#appsettingsjson) on Windows.</span></span>
* <span data-ttu-id="6f99c-167">Testez les paramètres lors de l’utilisation de l' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span><span class="sxs-lookup"><span data-stu-id="6f99c-167">Test the settings when using the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span></span> <span data-ttu-id="6f99c-168">La `dotnet run` commande doit être exécutée dans le répertoire du projet.</span><span class="sxs-lookup"><span data-stu-id="6f99c-168">The `dotnet run` command must be run in the project directory.</span></span>

```dotnetcli
set MyKey="My key from Environment"
set Position__Title=Environment_Editor
set Position__Name=Environment_Rick
dotnet run
```

<span data-ttu-id="6f99c-169">Paramètres d’environnement précédents :</span><span class="sxs-lookup"><span data-stu-id="6f99c-169">The preceding environment settings:</span></span>

* <span data-ttu-id="6f99c-170">Sont uniquement définies dans les processus lancés à partir de la fenêtre de commande dans laquelle ils ont été définis.</span><span class="sxs-lookup"><span data-stu-id="6f99c-170">Are only set in processes launched from the command window they were set in.</span></span>
* <span data-ttu-id="6f99c-171">Ne seront pas lues par les navigateurs lancés avec Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="6f99c-171">Won't be read by browsers launched with Visual Studio.</span></span>

<span data-ttu-id="6f99c-172">Les commandes [setx](/windows-server/administration/windows-commands/setx) suivantes peuvent être utilisées pour définir les clés et les valeurs d’environnement sur Windows.</span><span class="sxs-lookup"><span data-stu-id="6f99c-172">The following [setx](/windows-server/administration/windows-commands/setx) commands can be used to set the environment keys and values on Windows.</span></span> <span data-ttu-id="6f99c-173">Contrairement à `set` , `setx` les paramètres sont conservés.</span><span class="sxs-lookup"><span data-stu-id="6f99c-173">Unlike `set`, `setx` settings are persisted.</span></span> <span data-ttu-id="6f99c-174">`/M` définit la variable dans l’environnement système.</span><span class="sxs-lookup"><span data-stu-id="6f99c-174">`/M` sets the variable in the system environment.</span></span> <span data-ttu-id="6f99c-175">Si le `/M` commutateur n’est pas utilisé, une variable d’environnement utilisateur est définie.</span><span class="sxs-lookup"><span data-stu-id="6f99c-175">If the `/M` switch isn't used, a user environment variable is set.</span></span>

```cmd
setx MyKey "My key from setx Environment" /M
setx Position__Title Setx_Environment_Editor /M
setx Position__Name Environment_Rick /M
```

<span data-ttu-id="6f99c-176">Pour vérifier que les commandes précédentes remplacent *appsettings.jssur* et *appSettings.* `Environment` *. JSON*:</span><span class="sxs-lookup"><span data-stu-id="6f99c-176">To test that the preceding commands override *appsettings.json* and *appsettings.*`Environment`*.json*:</span></span>

* <span data-ttu-id="6f99c-177">Avec Visual Studio : quittez et redémarrez Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="6f99c-177">With Visual Studio: Exit and restart Visual Studio.</span></span>
* <span data-ttu-id="6f99c-178">Avec l’interface CLI : démarrez une nouvelle fenêtre de commande et entrez `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="6f99c-178">With the CLI: Start a new command window and enter `dotnet run`.</span></span>

<span data-ttu-id="6f99c-179">Appelez <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> avec une chaîne pour spécifier un préfixe pour les variables d’environnement :</span><span class="sxs-lookup"><span data-stu-id="6f99c-179">Call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> with a string to specify a prefix for environment variables:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Program.cs?name=snippet4&highlight=12)]

<span data-ttu-id="6f99c-180">Dans le code précédent :</span><span class="sxs-lookup"><span data-stu-id="6f99c-180">In the preceding code:</span></span>

* <span data-ttu-id="6f99c-181">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` est ajouté après les [fournisseurs de configuration par défaut](#default).</span><span class="sxs-lookup"><span data-stu-id="6f99c-181">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="6f99c-182">Pour obtenir un exemple de classement des fournisseurs de configuration, consultez [fournisseur de configuration JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="6f99c-182">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>
* <span data-ttu-id="6f99c-183">Les variables d’environnement définies avec le `MyCustomPrefix_` préfixe remplacent les [fournisseurs de configuration par défaut](#default).</span><span class="sxs-lookup"><span data-stu-id="6f99c-183">Environment variables set with the `MyCustomPrefix_` prefix override the [default configuration providers](#default).</span></span> <span data-ttu-id="6f99c-184">Cela comprend les variables d’environnement sans le préfixe.</span><span class="sxs-lookup"><span data-stu-id="6f99c-184">This includes environment variables without the prefix.</span></span>

<span data-ttu-id="6f99c-185">Le préfixe est supprimé lorsque les paires clé-valeur de configuration sont lues.</span><span class="sxs-lookup"><span data-stu-id="6f99c-185">The prefix is stripped off when the configuration key-value pairs are read.</span></span>

<span data-ttu-id="6f99c-186">Les commandes suivantes testent le préfixe personnalisé :</span><span class="sxs-lookup"><span data-stu-id="6f99c-186">The following commands test the custom prefix:</span></span>

```dotnetcli
set MyCustomPrefix_MyKey="My key with MyCustomPrefix_ Environment"
set MyCustomPrefix_Position__Title=Editor_with_customPrefix
set MyCustomPrefix_Position__Name=Environment_Rick_cp
dotnet run
```

<span data-ttu-id="6f99c-187">La [configuration par défaut](#default) charge les variables d’environnement et les arguments de ligne de commande préfixé avec `DOTNET_` et `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="6f99c-187">The [default configuration](#default) loads environment variables and command line arguments prefixed with `DOTNET_` and `ASPNETCORE_`.</span></span> <span data-ttu-id="6f99c-188">Les `DOTNET_` `ASPNETCORE_` préfixes et sont utilisés par ASP.net Core pour la configuration de l' [hôte et](xref:fundamentals/host/generic-host#host-configuration)de l’application, mais pas pour la configuration de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="6f99c-188">The `DOTNET_` and `ASPNETCORE_` prefixes are used by ASP.NET Core for [host and app configuration](xref:fundamentals/host/generic-host#host-configuration), but not for user configuration.</span></span> <span data-ttu-id="6f99c-189">Pour plus d’informations sur la configuration de l’hôte et de l’application, consultez [hôte générique .net](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="6f99c-189">For more information on host and app configuration, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

<span data-ttu-id="6f99c-190">Dans [Azure App service](https://azure.microsoft.com/services/app-service/), sélectionnez **nouveau paramètre d’application** dans la page **paramètres > configuration** .</span><span class="sxs-lookup"><span data-stu-id="6f99c-190">On [Azure App Service](https://azure.microsoft.com/services/app-service/), select **New application setting** on the **Settings > Configuration** page.</span></span> <span data-ttu-id="6f99c-191">Azure App Service paramètres de l’application sont les suivants :</span><span class="sxs-lookup"><span data-stu-id="6f99c-191">Azure App Service application settings are:</span></span>

* <span data-ttu-id="6f99c-192">Chiffré au repos et transmis sur un canal chiffré.</span><span class="sxs-lookup"><span data-stu-id="6f99c-192">Encrypted at rest and transmitted over an encrypted channel.</span></span>
* <span data-ttu-id="6f99c-193">Exposés en tant que variables d’environnement.</span><span class="sxs-lookup"><span data-stu-id="6f99c-193">Exposed as environment variables.</span></span>

<span data-ttu-id="6f99c-194">Pour plus d’informations, consultez [Azure Apps : remplacer la configuration de l’application à l’aide du portail Azure](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="6f99c-194">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="6f99c-195">Consultez [préfixes de chaîne de connexion](#constr) pour plus d’informations sur les chaînes de connexion de base de données Azure.</span><span class="sxs-lookup"><span data-stu-id="6f99c-195">See [Connection string prefixes](#constr) for information on Azure database connection strings.</span></span>

### <a name="environment-variables-set-in-launchsettingsjson"></a><span data-ttu-id="6f99c-196">Variables d’environnement définies dans launchSettings.jssur</span><span class="sxs-lookup"><span data-stu-id="6f99c-196">Environment variables set in launchSettings.json</span></span>

<span data-ttu-id="6f99c-197">Les variables d’environnement définies dans *launchSettings.jslors de* la substitution de celles définies dans l’environnement système.</span><span class="sxs-lookup"><span data-stu-id="6f99c-197">Environment variables set in *launchSettings.json* override those set in the system environment.</span></span>

<a name="clcp"></a>

## <a name="command-line"></a><span data-ttu-id="6f99c-198">Ligne de commande</span><span class="sxs-lookup"><span data-stu-id="6f99c-198">Command-line</span></span>

<span data-ttu-id="6f99c-199">À l’aide de la configuration [par défaut](#default) , le <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> charge la configuration à partir de paires clé-valeur d’argument de ligne de commande après les sources de configuration suivantes :</span><span class="sxs-lookup"><span data-stu-id="6f99c-199">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs after the following configuration sources:</span></span>

* <span data-ttu-id="6f99c-200">*appsettings.jssur* et *appSettings*. `Environment` fichiers *JSON* .</span><span class="sxs-lookup"><span data-stu-id="6f99c-200">*appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="6f99c-201">[Secrets d’application (gestionnaire de secret)](xref:security/app-secrets) dans l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="6f99c-201">[App secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="6f99c-202">Variables d'environnement.</span><span class="sxs-lookup"><span data-stu-id="6f99c-202">Environment variables.</span></span>

<span data-ttu-id="6f99c-203">Par [défaut](#default), les valeurs de configuration définies sur la ligne de commande remplacent les valeurs de configuration définies avec tous les autres fournisseurs de configuration.</span><span class="sxs-lookup"><span data-stu-id="6f99c-203">By [default](#default), configuration values set on the command-line override configuration values set with all the other configuration providers.</span></span>

### <a name="command-line-arguments"></a><span data-ttu-id="6f99c-204">Arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="6f99c-204">Command-line arguments</span></span>

<span data-ttu-id="6f99c-205">La commande suivante définit des clés et des valeurs à l’aide de `=` :</span><span class="sxs-lookup"><span data-stu-id="6f99c-205">The following command sets keys and values using `=`:</span></span>

```dotnetcli
dotnet run MyKey="My key from command line" Position:Title=Cmd Position:Name=Cmd_Rick
```

<span data-ttu-id="6f99c-206">La commande suivante définit des clés et des valeurs à l’aide de `/` :</span><span class="sxs-lookup"><span data-stu-id="6f99c-206">The following command sets keys and values using `/`:</span></span>

```dotnetcli
dotnet run /MyKey "Using /" /Position:Title=Cmd_ /Position:Name=Cmd_Rick
```

<span data-ttu-id="6f99c-207">La commande suivante définit des clés et des valeurs à l’aide de `--` :</span><span class="sxs-lookup"><span data-stu-id="6f99c-207">The following command sets keys and values using `--`:</span></span>

```dotnetcli
dotnet run --MyKey "Using --" --Position:Title=Cmd-- --Position:Name=Cmd--Rick
```

<span data-ttu-id="6f99c-208">Valeur de la clé :</span><span class="sxs-lookup"><span data-stu-id="6f99c-208">The key value:</span></span>

* <span data-ttu-id="6f99c-209">Doit suivre `=` ou la clé doit avoir un préfixe `--` ou `/` lorsque la valeur suit un espace.</span><span class="sxs-lookup"><span data-stu-id="6f99c-209">Must follow `=`, or the key must have a prefix of `--` or `/` when the value follows a space.</span></span>
* <span data-ttu-id="6f99c-210">N’est pas obligatoire si `=` est utilisé.</span><span class="sxs-lookup"><span data-stu-id="6f99c-210">Isn't required if `=` is used.</span></span> <span data-ttu-id="6f99c-211">Par exemple : `MySetting=`.</span><span class="sxs-lookup"><span data-stu-id="6f99c-211">For example, `MySetting=`.</span></span>

<span data-ttu-id="6f99c-212">Dans la même commande, ne mélangez pas les paires clé-valeur d’argument de ligne de commande qui utilisent `=` des paires clé-valeur utilisant un espace.</span><span class="sxs-lookup"><span data-stu-id="6f99c-212">Within the same command, don't mix command-line argument key-value pairs that use `=` with key-value pairs that use a space.</span></span>

### <a name="switch-mappings"></a><span data-ttu-id="6f99c-213">Correspondances de commutateur</span><span class="sxs-lookup"><span data-stu-id="6f99c-213">Switch mappings</span></span>

<span data-ttu-id="6f99c-214">Les mappages de commutateur autorisent la logique de remplacement de nom de **clé** .</span><span class="sxs-lookup"><span data-stu-id="6f99c-214">Switch mappings allow **key** name replacement logic.</span></span> <span data-ttu-id="6f99c-215">Fournissez un dictionnaire de remplacements de commutateur dans la <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> méthode.</span><span class="sxs-lookup"><span data-stu-id="6f99c-215">Provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="6f99c-216">Quand le dictionnaire de correspondances de commutateur est utilisé, il est vérifié afin de déterminer s’il contient une clé correspondant à celle fournie par un argument de ligne de commande.</span><span class="sxs-lookup"><span data-stu-id="6f99c-216">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="6f99c-217">Si la clé de ligne de commande est trouvée dans le dictionnaire, la valeur du dictionnaire est retournée pour définir la paire clé-valeur dans la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="6f99c-217">If the command-line key is found in the dictionary, the dictionary value is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="6f99c-218">Une correspondance de commutateur est nécessaire pour chaque clé de ligne de commande préfixée avec un tiret unique (`-`).</span><span class="sxs-lookup"><span data-stu-id="6f99c-218">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="6f99c-219">Règles des clés du dictionnaire de correspondances de commutateur :</span><span class="sxs-lookup"><span data-stu-id="6f99c-219">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="6f99c-220">Les commutateurs doivent commencer par `-` ou `--` .</span><span class="sxs-lookup"><span data-stu-id="6f99c-220">Switches must start with `-` or `--`.</span></span>
* <span data-ttu-id="6f99c-221">Le dictionnaire de correspondances de commutateur ne doit pas contenir de clés en double.</span><span class="sxs-lookup"><span data-stu-id="6f99c-221">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="6f99c-222">Pour utiliser un dictionnaire de mappages de commutateur, transmettez-le à l’appel à `AddCommandLine` :</span><span class="sxs-lookup"><span data-stu-id="6f99c-222">To use a switch mappings dictionary, pass it into the call to `AddCommandLine`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramSwitch.cs?name=snippet&highlight=10-18,23)]

<span data-ttu-id="6f99c-223">Le code suivant montre les valeurs de clé pour les clés remplacées :</span><span class="sxs-lookup"><span data-stu-id="6f99c-223">The following code shows the key values for the replaced keys:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test3.cshtml.cs?name=snippet)]

<span data-ttu-id="6f99c-224">La commande suivante fonctionne pour tester le remplacement de la clé :</span><span class="sxs-lookup"><span data-stu-id="6f99c-224">The following command works to test key replacement:</span></span>

```dotnetcli
dotnet run -k1 value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="6f99c-225">Pour les applications qui utilisent des mappages de commutateurs, l’appel à `CreateDefaultBuilder` ne doit pas passer d’arguments.</span><span class="sxs-lookup"><span data-stu-id="6f99c-225">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="6f99c-226">L' `CreateDefaultBuilder` appel de la méthode `AddCommandLine` n’inclut pas de commutateurs mappés, et il n’existe aucun moyen de passer le dictionnaire de mappage de commutateur à `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="6f99c-226">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch-mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="6f99c-227">La solution ne consiste pas à passer les arguments à `CreateDefaultBuilder` , mais à autoriser la méthode de la `ConfigurationBuilder` méthode `AddCommandLine` à traiter à la fois les arguments et le dictionnaire de mappage de commutateur.</span><span class="sxs-lookup"><span data-stu-id="6f99c-227">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch-mapping dictionary.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="6f99c-228">Données de configuration hiérarchiques</span><span class="sxs-lookup"><span data-stu-id="6f99c-228">Hierarchical configuration data</span></span>

<span data-ttu-id="6f99c-229">L’API de configuration lit les données de configuration hiérarchiques en aplatit les données hiérarchiques à l’aide d’un délimiteur dans les clés de configuration.</span><span class="sxs-lookup"><span data-stu-id="6f99c-229">The Configuration API reads hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="6f99c-230">L' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contient les  *appsettings.jssuivantes sur* le fichier :</span><span class="sxs-lookup"><span data-stu-id="6f99c-230">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="6f99c-231">Le code suivant de l' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) affiche plusieurs des paramètres de configuration :</span><span class="sxs-lookup"><span data-stu-id="6f99c-231">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="6f99c-232">La meilleure façon de lire des données de configuration hiérarchiques consiste à utiliser le modèle d’options.</span><span class="sxs-lookup"><span data-stu-id="6f99c-232">The preferred way to read hierarchical configuration data is using the options pattern.</span></span> <span data-ttu-id="6f99c-233">Pour plus d’informations, consultez [lier des données de configuration hiérarchiques](#optpat) dans ce document.</span><span class="sxs-lookup"><span data-stu-id="6f99c-233">For more information, see [Bind hierarchical configuration data](#optpat) in this document.</span></span>

<span data-ttu-id="6f99c-234">Les méthodes <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> et <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> sont disponibles pour isoler les sections et les enfants d’une section dans les données de configuration.</span><span class="sxs-lookup"><span data-stu-id="6f99c-234"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="6f99c-235">Ces méthodes sont décrites plus loin dans [GetSection, GetChildren et Exists](#getsection).</span><span class="sxs-lookup"><span data-stu-id="6f99c-235">These methods are described later in [GetSection, GetChildren, and Exists](#getsection).</span></span>

<!--
[Azure Key Vault configuration provider](xref:security/key-vault-configuration) implement change detection.
-->

## <a name="configuration-keys-and-values"></a><span data-ttu-id="6f99c-236">Clés et valeurs de configuration</span><span class="sxs-lookup"><span data-stu-id="6f99c-236">Configuration keys and values</span></span>

<span data-ttu-id="6f99c-237">Clés de configuration :</span><span class="sxs-lookup"><span data-stu-id="6f99c-237">Configuration keys:</span></span>

* <span data-ttu-id="6f99c-238">Ne respectent pas la casse.</span><span class="sxs-lookup"><span data-stu-id="6f99c-238">Are case-insensitive.</span></span> <span data-ttu-id="6f99c-239">Par exemple, `ConnectionString` et `connectionstring` sont traités en tant que clés équivalentes.</span><span class="sxs-lookup"><span data-stu-id="6f99c-239">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="6f99c-240">Si une clé et une valeur sont définies dans plusieurs fournisseurs de configuration, la valeur du dernier fournisseur ajouté est utilisée.</span><span class="sxs-lookup"><span data-stu-id="6f99c-240">If a key and value is set in more than one configuration providers, the value from the last provider added is used.</span></span> <span data-ttu-id="6f99c-241">Pour plus d’informations, consultez [configuration par défaut](#default).</span><span class="sxs-lookup"><span data-stu-id="6f99c-241">For more information, see [Default configuration](#default).</span></span>
* <span data-ttu-id="6f99c-242">Clés hiérarchiques</span><span class="sxs-lookup"><span data-stu-id="6f99c-242">Hierarchical keys</span></span>
  * <span data-ttu-id="6f99c-243">Dans l’API Configuration, un séparateur sous forme de signe deux-points (`:`) fonctionne sur toutes les plateformes.</span><span class="sxs-lookup"><span data-stu-id="6f99c-243">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="6f99c-244">Dans les variables d’environnement, un séparateur sous forme de signe deux-points peut ne pas fonctionner sur toutes les plateformes.</span><span class="sxs-lookup"><span data-stu-id="6f99c-244">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="6f99c-245">Un trait de soulignement double, `__` , est pris en charge par toutes les plateformes et est automatiquement converti en deux-points `:` .</span><span class="sxs-lookup"><span data-stu-id="6f99c-245">A double underscore, `__`, is supported by all platforms and is automatically converted into a colon `:`.</span></span>
  * <span data-ttu-id="6f99c-246">Dans Azure Key Vault, les clés hiérarchiques utilisent `--` comme séparateur.</span><span class="sxs-lookup"><span data-stu-id="6f99c-246">In Azure Key Vault, hierarchical keys use `--` as a separator.</span></span> <span data-ttu-id="6f99c-247">Le [fournisseur de configuration Azure Key Vault](xref:security/key-vault-configuration) remplace automatiquement `--` par un `:` lorsque les secrets sont chargés dans la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="6f99c-247">The [Azure Key Vault configuration provider](xref:security/key-vault-configuration) automatically replaces `--` with a `:` when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="6f99c-248"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> prend en charge la liaison de tableaux à des objets à l’aide d’index de tableau dans les clés de configuration.</span><span class="sxs-lookup"><span data-stu-id="6f99c-248">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="6f99c-249">La liaison de tableau est décrite dans la section [Lier un tableau à une classe](#boa).</span><span class="sxs-lookup"><span data-stu-id="6f99c-249">Array binding is described in the [Bind an array to a class](#boa) section.</span></span>

<span data-ttu-id="6f99c-250">Valeurs de configuration :</span><span class="sxs-lookup"><span data-stu-id="6f99c-250">Configuration values:</span></span>

* <span data-ttu-id="6f99c-251">Sont des chaînes.</span><span class="sxs-lookup"><span data-stu-id="6f99c-251">Are strings.</span></span>
* <span data-ttu-id="6f99c-252">Les valeurs NULL ne peuvent pas être stockées dans la configuration ou liées à des objets.</span><span class="sxs-lookup"><span data-stu-id="6f99c-252">Null values can't be stored in configuration or bound to objects.</span></span>

<a name="cp"></a>

## <a name="configuration-providers"></a><span data-ttu-id="6f99c-253">Fournisseurs de configuration</span><span class="sxs-lookup"><span data-stu-id="6f99c-253">Configuration providers</span></span>

<span data-ttu-id="6f99c-254">Le tableau suivant présente les fournisseurs de configuration disponibles pour les applications ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6f99c-254">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="6f99c-255">Fournisseur</span><span class="sxs-lookup"><span data-stu-id="6f99c-255">Provider</span></span> | <span data-ttu-id="6f99c-256">Fournit la configuration à partir de</span><span class="sxs-lookup"><span data-stu-id="6f99c-256">Provides configuration from</span></span> |
| -------- | ----------------------------------- |
| [<span data-ttu-id="6f99c-257">Fournisseur de configuration Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="6f99c-257">Azure Key Vault configuration provider</span></span>](xref:security/key-vault-configuration) | <span data-ttu-id="6f99c-258">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="6f99c-258">Azure Key Vault</span></span> |
| [<span data-ttu-id="6f99c-259">Fournisseur de configuration Azure App</span><span class="sxs-lookup"><span data-stu-id="6f99c-259">Azure App configuration provider</span></span>](/azure/azure-app-configuration/quickstart-aspnet-core-app) | <span data-ttu-id="6f99c-260">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="6f99c-260">Azure App Configuration</span></span> |
| [<span data-ttu-id="6f99c-261">Fournisseur de configuration de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="6f99c-261">Command-line configuration provider</span></span>](#clcp) | <span data-ttu-id="6f99c-262">Paramètres de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="6f99c-262">Command-line parameters</span></span> |
| [<span data-ttu-id="6f99c-263">Fournisseur de configuration personnalisé</span><span class="sxs-lookup"><span data-stu-id="6f99c-263">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="6f99c-264">Source personnalisée</span><span class="sxs-lookup"><span data-stu-id="6f99c-264">Custom source</span></span> |
| [<span data-ttu-id="6f99c-265">Fournisseur de configuration des variables d’environnement</span><span class="sxs-lookup"><span data-stu-id="6f99c-265">Environment Variables configuration provider</span></span>](#evcp) | <span data-ttu-id="6f99c-266">Variables d'environnement</span><span class="sxs-lookup"><span data-stu-id="6f99c-266">Environment variables</span></span> |
| [<span data-ttu-id="6f99c-267">Fournisseur de configuration de fichier</span><span class="sxs-lookup"><span data-stu-id="6f99c-267">File configuration provider</span></span>](#file-configuration-provider) | <span data-ttu-id="6f99c-268">Fichiers INI, JSON et XML</span><span class="sxs-lookup"><span data-stu-id="6f99c-268">INI, JSON, and XML files</span></span> |
| [<span data-ttu-id="6f99c-269">Fournisseur de configuration de clé par fichier</span><span class="sxs-lookup"><span data-stu-id="6f99c-269">Key-per-file configuration provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="6f99c-270">Fichiers de répertoire</span><span class="sxs-lookup"><span data-stu-id="6f99c-270">Directory files</span></span> |
| [<span data-ttu-id="6f99c-271">Fournisseur de configuration de la mémoire</span><span class="sxs-lookup"><span data-stu-id="6f99c-271">Memory configuration provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="6f99c-272">Collections en mémoire</span><span class="sxs-lookup"><span data-stu-id="6f99c-272">In-memory collections</span></span> |
| [<span data-ttu-id="6f99c-273">Gestionnaire de secret</span><span class="sxs-lookup"><span data-stu-id="6f99c-273">Secret Manager</span></span>](xref:security/app-secrets)  | <span data-ttu-id="6f99c-274">Fichier dans le répertoire de profil utilisateur</span><span class="sxs-lookup"><span data-stu-id="6f99c-274">File in the user profile directory</span></span> |

<span data-ttu-id="6f99c-275">Les sources de configuration sont lues dans l’ordre dans lequel leurs fournisseurs de configuration sont spécifiés.</span><span class="sxs-lookup"><span data-stu-id="6f99c-275">Configuration sources are read in the order that their configuration providers are specified.</span></span> <span data-ttu-id="6f99c-276">Commandez des fournisseurs de configuration dans le code pour répondre aux priorités des sources de configuration sous-jacentes requises par l’application.</span><span class="sxs-lookup"><span data-stu-id="6f99c-276">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="6f99c-277">Une séquence type des fournisseurs de configuration est la suivante :</span><span class="sxs-lookup"><span data-stu-id="6f99c-277">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="6f99c-278">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="6f99c-278">*appsettings.json*</span></span>
1. <span data-ttu-id="6f99c-279">*appSettings*. `Environment` . *JSON*</span><span class="sxs-lookup"><span data-stu-id="6f99c-279">*appsettings*.`Environment`.*json*</span></span>
1. [<span data-ttu-id="6f99c-280">Gestionnaire de secret</span><span class="sxs-lookup"><span data-stu-id="6f99c-280">Secret Manager</span></span>](xref:security/app-secrets)
1. <span data-ttu-id="6f99c-281">Variables d’environnement à l’aide du [fournisseur de configuration des variables d’environnement](#evcp).</span><span class="sxs-lookup"><span data-stu-id="6f99c-281">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="6f99c-282">Arguments de ligne de commande à l’aide du [fournisseur de configuration de ligne de commande](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="6f99c-282">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>

<span data-ttu-id="6f99c-283">Une pratique courante consiste à ajouter le dernier fournisseur de configuration de ligne de commande dans une série de fournisseurs pour permettre aux arguments de ligne de commande de remplacer la configuration définie par les autres fournisseurs.</span><span class="sxs-lookup"><span data-stu-id="6f99c-283">A common practice is to add the Command-line configuration provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="6f99c-284">La séquence de fournisseurs précédente est utilisée dans la [configuration par défaut](#default).</span><span class="sxs-lookup"><span data-stu-id="6f99c-284">The preceding sequence of providers is used in the [default configuration](#default).</span></span>

<a name="constr"></a>

### <a name="connection-string-prefixes"></a><span data-ttu-id="6f99c-285">Préfixes des chaînes de connexion</span><span class="sxs-lookup"><span data-stu-id="6f99c-285">Connection string prefixes</span></span>

<span data-ttu-id="6f99c-286">L’API de configuration a des règles de traitement spéciales pour quatre variables d’environnement de chaîne de connexion.</span><span class="sxs-lookup"><span data-stu-id="6f99c-286">The Configuration API has special processing rules for four connection string environment variables.</span></span> <span data-ttu-id="6f99c-287">Ces chaînes de connexion sont impliquées dans la configuration des chaînes de connexion Azure pour l’environnement de l’application.</span><span class="sxs-lookup"><span data-stu-id="6f99c-287">These connection strings are involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="6f99c-288">Les variables d’environnement avec les préfixes indiqués dans le tableau sont chargées dans l’application avec la [configuration par défaut](#default) ou lorsqu’aucun préfixe n’est fourni à `AddEnvironmentVariables` .</span><span class="sxs-lookup"><span data-stu-id="6f99c-288">Environment variables with the prefixes shown in the table are loaded into the app with the [default configuration](#default) or when no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="6f99c-289">Préfixe de la chaîne de connexion</span><span class="sxs-lookup"><span data-stu-id="6f99c-289">Connection string prefix</span></span> | <span data-ttu-id="6f99c-290">Fournisseur</span><span class="sxs-lookup"><span data-stu-id="6f99c-290">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="6f99c-291">Fournisseur personnalisé</span><span class="sxs-lookup"><span data-stu-id="6f99c-291">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="6f99c-292">MySQL</span><span class="sxs-lookup"><span data-stu-id="6f99c-292">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="6f99c-293">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="6f99c-293">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="6f99c-294">SQL Server</span><span class="sxs-lookup"><span data-stu-id="6f99c-294">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="6f99c-295">Quand une variable d’environnement est découverte et chargée dans la configuration avec l’un des quatre préfixes indiqués dans le tableau :</span><span class="sxs-lookup"><span data-stu-id="6f99c-295">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="6f99c-296">La clé de configuration est créée en supprimant le préfixe de la variable d’environnement et en ajoutant une section de clé de configuration (`ConnectionStrings`).</span><span class="sxs-lookup"><span data-stu-id="6f99c-296">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="6f99c-297">Une nouvelle paire clé-valeur de configuration est créée qui représente le fournisseur de connexion de base de données (à l’exception de `CUSTOMCONNSTR_`, qui ne possède aucun fournisseur indiqué).</span><span class="sxs-lookup"><span data-stu-id="6f99c-297">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="6f99c-298">Clé de variable d’environnement</span><span class="sxs-lookup"><span data-stu-id="6f99c-298">Environment variable key</span></span> | <span data-ttu-id="6f99c-299">Clé de configuration convertie</span><span class="sxs-lookup"><span data-stu-id="6f99c-299">Converted configuration key</span></span> | <span data-ttu-id="6f99c-300">Entrée de configuration de fournisseur</span><span class="sxs-lookup"><span data-stu-id="6f99c-300">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="6f99c-301">Entrée de configuration non créée.</span><span class="sxs-lookup"><span data-stu-id="6f99c-301">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="6f99c-302">Clé : `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="6f99c-302">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="6f99c-303">Valeur: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="6f99c-303">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="6f99c-304">Clé : `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="6f99c-304">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="6f99c-305">Valeur: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="6f99c-305">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="6f99c-306">Clé : `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="6f99c-306">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="6f99c-307">Valeur: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="6f99c-307">Value: `System.Data.SqlClient`</span></span>  |

<a name="fcp"></a>

## <a name="file-configuration-provider"></a><span data-ttu-id="6f99c-308">Fournisseur de configuration de fichier</span><span class="sxs-lookup"><span data-stu-id="6f99c-308">File configuration provider</span></span>

<span data-ttu-id="6f99c-309"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> est la classe de base pour charger la configuration à partir du système de fichiers.</span><span class="sxs-lookup"><span data-stu-id="6f99c-309"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="6f99c-310">Les fournisseurs de configuration suivants dérivent de `FileConfigurationProvider` :</span><span class="sxs-lookup"><span data-stu-id="6f99c-310">The following configuration providers derive from `FileConfigurationProvider`:</span></span>

* [<span data-ttu-id="6f99c-311">Fournisseur de configuration INI</span><span class="sxs-lookup"><span data-stu-id="6f99c-311">INI configuration provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="6f99c-312">Fournisseur de configuration JSON</span><span class="sxs-lookup"><span data-stu-id="6f99c-312">JSON configuration provider</span></span>](#jcp)
* [<span data-ttu-id="6f99c-313">Fournisseur de configuration XML</span><span class="sxs-lookup"><span data-stu-id="6f99c-313">XML configuration provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="6f99c-314">Fournisseur de configuration INI</span><span class="sxs-lookup"><span data-stu-id="6f99c-314">INI configuration provider</span></span>

<span data-ttu-id="6f99c-315"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> charge la configuration à partir des paires clé-valeur du fichier INI lors de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="6f99c-315">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="6f99c-316">Le code suivant efface tous les fournisseurs de configuration et ajoute plusieurs fournisseurs de configuration :</span><span class="sxs-lookup"><span data-stu-id="6f99c-316">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramINI.cs?name=snippet&highlight=10-30)]

<span data-ttu-id="6f99c-317">Dans le code précédent, les paramètres des *MyIniConfig.ini* et  *MyIniConfig*. `Environment` les fichiers *ini* sont remplacés par les paramètres dans le :</span><span class="sxs-lookup"><span data-stu-id="6f99c-317">In the preceding code, settings in the *MyIniConfig.ini* and  *MyIniConfig*.`Environment`.*ini* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="6f99c-318">Fournisseur de configuration des variables d’environnement</span><span class="sxs-lookup"><span data-stu-id="6f99c-318">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="6f99c-319">[Fournisseur de configuration de ligne de commande](#clcp).</span><span class="sxs-lookup"><span data-stu-id="6f99c-319">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="6f99c-320">L' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contient le fichier *MyIniConfig.ini* suivant :</span><span class="sxs-lookup"><span data-stu-id="6f99c-320">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyIniConfig.ini* file:</span></span>

[!code-ini[](index/samples/3.x/ConfigSample/MyIniConfig.ini)]

<span data-ttu-id="6f99c-321">Le code suivant de l' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) affiche plusieurs des paramètres de configuration précédents :</span><span class="sxs-lookup"><span data-stu-id="6f99c-321">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<a name="jcp"></a>

### <a name="json-configuration-provider"></a><span data-ttu-id="6f99c-322">Fournisseur de configuration JSON</span><span class="sxs-lookup"><span data-stu-id="6f99c-322">JSON configuration provider</span></span>

<span data-ttu-id="6f99c-323">Le <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> charge la configuration à partir de paires clé-valeur de fichier JSON.</span><span class="sxs-lookup"><span data-stu-id="6f99c-323">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs.</span></span>

<span data-ttu-id="6f99c-324">Les surcharges peuvent spécifier :</span><span class="sxs-lookup"><span data-stu-id="6f99c-324">Overloads can specify:</span></span>

* <span data-ttu-id="6f99c-325">Si le fichier est facultatif.</span><span class="sxs-lookup"><span data-stu-id="6f99c-325">Whether the file is optional.</span></span>
* <span data-ttu-id="6f99c-326">Si la configuration est rechargée quand le fichier est modifié.</span><span class="sxs-lookup"><span data-stu-id="6f99c-326">Whether the configuration is reloaded if the file changes.</span></span>

<span data-ttu-id="6f99c-327">Considérez le code suivant :</span><span class="sxs-lookup"><span data-stu-id="6f99c-327">Consider the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON.cs?name=snippet&highlight=12-14)]

<span data-ttu-id="6f99c-328">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="6f99c-328">The preceding code:</span></span>

* <span data-ttu-id="6f99c-329">Configure le fournisseur de configuration JSON pour charger le *MyConfig.jssur* le fichier avec les options suivantes :</span><span class="sxs-lookup"><span data-stu-id="6f99c-329">Configures the JSON configuration provider to load the *MyConfig.json* file with the following options:</span></span>
  * <span data-ttu-id="6f99c-330">`optional: true`: Le fichier est facultatif.</span><span class="sxs-lookup"><span data-stu-id="6f99c-330">`optional: true`: The file is optional.</span></span>
  * <span data-ttu-id="6f99c-331">`reloadOnChange: true` : Le fichier est rechargé lorsque des modifications sont enregistrées.</span><span class="sxs-lookup"><span data-stu-id="6f99c-331">`reloadOnChange: true` : The file is reloaded when changes are saved.</span></span>
* <span data-ttu-id="6f99c-332">Lit les [fournisseurs de configuration par défaut](#default) avant l' *MyConfig.jssur* le fichier.</span><span class="sxs-lookup"><span data-stu-id="6f99c-332">Reads the [default configuration providers](#default) before the *MyConfig.json* file.</span></span> <span data-ttu-id="6f99c-333">Les paramètres dans le *MyConfig.js* paramètre de remplacement de fichier dans les fournisseurs de configuration par défaut, y compris le [fournisseur de configuration des variables d’environnement](#evcp) et le fournisseur de configuration de ligne de [commande](#clcp).</span><span class="sxs-lookup"><span data-stu-id="6f99c-333">Settings in the *MyConfig.json* file override setting in the default configuration providers, including the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="6f99c-334">En général, vous ***ne souhaitez pas*** qu’une valeur de substitution de fichier JSON personnalisée soit définie dans le fournisseur de configuration des [variables d’environnement](#evcp) et dans le fournisseur de configuration de [ligne de commande](#clcp).</span><span class="sxs-lookup"><span data-stu-id="6f99c-334">You typically ***don't*** want a custom JSON file overriding values set in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="6f99c-335">Le code suivant efface tous les fournisseurs de configuration et ajoute plusieurs fournisseurs de configuration :</span><span class="sxs-lookup"><span data-stu-id="6f99c-335">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON2.cs?name=snippet)]

<span data-ttu-id="6f99c-336">Dans le code précédent, les paramètres de la *MyConfig.jssur* et  *MyConfig*. `Environment` . fichiers *JSON* :</span><span class="sxs-lookup"><span data-stu-id="6f99c-336">In the preceding code, settings in the *MyConfig.json* and  *MyConfig*.`Environment`.*json* files:</span></span>

* <span data-ttu-id="6f99c-337">Substituez les paramètres dans la *appsettings.jssur* et *appSettings* `Environment` . fichiers *JSON* .</span><span class="sxs-lookup"><span data-stu-id="6f99c-337">Override settings in the *appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="6f99c-338">Sont remplacées par les paramètres dans le [fournisseur de configuration des variables d’environnement](#evcp) et le fournisseur de configuration de ligne de [commande](#clcp).</span><span class="sxs-lookup"><span data-stu-id="6f99c-338">Are overridden by settings in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="6f99c-339">L' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contient les  *MyConfig.jssuivantes sur* le fichier :</span><span class="sxs-lookup"><span data-stu-id="6f99c-339">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *MyConfig.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyConfig.json)]

<span data-ttu-id="6f99c-340">Le code suivant de l' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) affiche plusieurs des paramètres de configuration précédents :</span><span class="sxs-lookup"><span data-stu-id="6f99c-340">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

### <a name="xml-configuration-provider"></a><span data-ttu-id="6f99c-341">Fournisseur de configuration XML</span><span class="sxs-lookup"><span data-stu-id="6f99c-341">XML configuration provider</span></span>

<span data-ttu-id="6f99c-342"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> charge la configuration à partir des paires clé-valeur du fichier XML lors de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="6f99c-342">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="6f99c-343">Le code suivant efface tous les fournisseurs de configuration et ajoute plusieurs fournisseurs de configuration :</span><span class="sxs-lookup"><span data-stu-id="6f99c-343">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramXML.cs?name=snippet)]

<span data-ttu-id="6f99c-344">Dans le code précédent, les paramètres des *MyXMLFile.xml* et  *MyXMLFile*. `Environment` les fichiers *XML* sont remplacés par les paramètres dans le :</span><span class="sxs-lookup"><span data-stu-id="6f99c-344">In the preceding code, settings in the *MyXMLFile.xml* and  *MyXMLFile*.`Environment`.*xml* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="6f99c-345">Fournisseur de configuration des variables d’environnement</span><span class="sxs-lookup"><span data-stu-id="6f99c-345">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="6f99c-346">[Fournisseur de configuration de ligne de commande](#clcp).</span><span class="sxs-lookup"><span data-stu-id="6f99c-346">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="6f99c-347">L' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contient le fichier *MyXMLFile.xml* suivant :</span><span class="sxs-lookup"><span data-stu-id="6f99c-347">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyXMLFile.xml* file:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile.xml)]

<span data-ttu-id="6f99c-348">Le code suivant de l' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) affiche plusieurs des paramètres de configuration précédents :</span><span class="sxs-lookup"><span data-stu-id="6f99c-348">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="6f99c-349">Les éléments répétitifs qui utilisent le même nom d’élément fonctionnent si l’attribut `name` est utilisé pour distinguer les éléments :</span><span class="sxs-lookup"><span data-stu-id="6f99c-349">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile3.xml)]

<span data-ttu-id="6f99c-350">Le code suivant lit le fichier de configuration précédent et affiche les clés et les valeurs :</span><span class="sxs-lookup"><span data-stu-id="6f99c-350">The following code reads the previous configuration file and displays the keys and values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/XML/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="6f99c-351">Les attributs peuvent être utilisés pour fournir des valeurs :</span><span class="sxs-lookup"><span data-stu-id="6f99c-351">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="6f99c-352">Le fichier de configuration précédent charge les clés suivantes avec `value` :</span><span class="sxs-lookup"><span data-stu-id="6f99c-352">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="6f99c-353">key:attribute</span><span class="sxs-lookup"><span data-stu-id="6f99c-353">key:attribute</span></span>
* <span data-ttu-id="6f99c-354">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="6f99c-354">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="6f99c-355">Fournisseur de configuration de clé par fichier</span><span class="sxs-lookup"><span data-stu-id="6f99c-355">Key-per-file configuration provider</span></span>

<span data-ttu-id="6f99c-356">Le <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> utilise les fichiers d’un répertoire en tant que paires clé-valeur de configuration.</span><span class="sxs-lookup"><span data-stu-id="6f99c-356">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="6f99c-357">La clé est le nom de fichier.</span><span class="sxs-lookup"><span data-stu-id="6f99c-357">The key is the file name.</span></span> <span data-ttu-id="6f99c-358">La valeur contient le contenu du fichier.</span><span class="sxs-lookup"><span data-stu-id="6f99c-358">The value contains the file's contents.</span></span> <span data-ttu-id="6f99c-359">Le fournisseur de configuration par fichier clé est utilisé dans les scénarios d’hébergement de l’ancrage.</span><span class="sxs-lookup"><span data-stu-id="6f99c-359">The Key-per-file configuration provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="6f99c-360">Pour activer la configuration clé par fichier, appelez la méthode d’extension <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> sur une instance de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="6f99c-360">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="6f99c-361">Le `directoryPath` aux fichiers doit être un chemin d’accès absolu.</span><span class="sxs-lookup"><span data-stu-id="6f99c-361">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="6f99c-362">Les surcharges permettent de spécifier :</span><span class="sxs-lookup"><span data-stu-id="6f99c-362">Overloads permit specifying:</span></span>

* <span data-ttu-id="6f99c-363">Un délégué `Action<KeyPerFileConfigurationSource>` qui configure la source.</span><span class="sxs-lookup"><span data-stu-id="6f99c-363">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="6f99c-364">Si le répertoire est facultatif et le chemin d’accès au répertoire.</span><span class="sxs-lookup"><span data-stu-id="6f99c-364">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="6f99c-365">Le double trait de soulignement (`__`) est utilisé comme un délimiteur de clé de configuration dans les noms de fichiers.</span><span class="sxs-lookup"><span data-stu-id="6f99c-365">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="6f99c-366">Par exemple, le nom de fichier `Logging__LogLevel__System` génère la clé de configuration `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="6f99c-366">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="6f99c-367">Appelez `ConfigureAppConfiguration` lors de la création de l’hôte pour spécifier la configuration de l’application :</span><span class="sxs-lookup"><span data-stu-id="6f99c-367">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<a name="mcp"></a>

## <a name="memory-configuration-provider"></a><span data-ttu-id="6f99c-368">Fournisseur de configuration de la mémoire</span><span class="sxs-lookup"><span data-stu-id="6f99c-368">Memory configuration provider</span></span>

<span data-ttu-id="6f99c-369">Le <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> utilise une collection en mémoire en tant que paires clé-valeur de configuration.</span><span class="sxs-lookup"><span data-stu-id="6f99c-369">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="6f99c-370">Le code suivant ajoute une collection de mémoire au système de configuration :</span><span class="sxs-lookup"><span data-stu-id="6f99c-370">The following code adds a memory collection to the configuration system:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet6)]

<span data-ttu-id="6f99c-371">Le code suivant de l' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) affiche les paramètres de configuration précédents :</span><span class="sxs-lookup"><span data-stu-id="6f99c-371">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="6f99c-372">Dans le code précédent, `config.AddInMemoryCollection(Dict)` est ajouté après les [fournisseurs de configuration par défaut](#default).</span><span class="sxs-lookup"><span data-stu-id="6f99c-372">In the preceding code, `config.AddInMemoryCollection(Dict)` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="6f99c-373">Pour obtenir un exemple de classement des fournisseurs de configuration, consultez [fournisseur de configuration JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="6f99c-373">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="6f99c-374">Pour un autre exemple, consultez [lier un tableau](#boa) à l’aide de `MemoryConfigurationProvider` .</span><span class="sxs-lookup"><span data-stu-id="6f99c-374">See [Bind an array](#boa) for another example using `MemoryConfigurationProvider`.</span></span>

## <a name="getvalue"></a><span data-ttu-id="6f99c-375">GetValue</span><span class="sxs-lookup"><span data-stu-id="6f99c-375">GetValue</span></span>

<span data-ttu-id="6f99c-376">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extrait une valeur unique de la configuration avec une clé spécifiée et la convertit en type spécifié :</span><span class="sxs-lookup"><span data-stu-id="6f99c-376">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified type:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestNum.cshtml.cs?name=snippet)]

<span data-ttu-id="6f99c-377">Dans le code précédent, si est `NumberKey` introuvable dans la configuration, la valeur par défaut de `99` est utilisée.</span><span class="sxs-lookup"><span data-stu-id="6f99c-377">In the preceding code,  if `NumberKey` isn't found in the configuration, the default value of `99` is used.</span></span>

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="6f99c-378">GetSection, GetChildren et Exists</span><span class="sxs-lookup"><span data-stu-id="6f99c-378">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="6f99c-379">Pour les exemples qui suivent, prenez en compte les *MySubsection.jssuivantes sur* le fichier :</span><span class="sxs-lookup"><span data-stu-id="6f99c-379">For the examples that follow, consider the following *MySubsection.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MySubsection.json)]

<span data-ttu-id="6f99c-380">Le code suivant ajoute *MySubsection.js* aux fournisseurs de configuration :</span><span class="sxs-lookup"><span data-stu-id="6f99c-380">The following code adds *MySubsection.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONsection.cs?name=snippet)]

### <a name="getsection"></a><span data-ttu-id="6f99c-381">GetSection</span><span class="sxs-lookup"><span data-stu-id="6f99c-381">GetSection</span></span>

<span data-ttu-id="6f99c-382">[IConfiguration. GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) retourne une sous-section de configuration avec la clé de sous-section spécifiée.</span><span class="sxs-lookup"><span data-stu-id="6f99c-382">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) returns a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="6f99c-383">Le code suivant retourne des valeurs pour `section1` :</span><span class="sxs-lookup"><span data-stu-id="6f99c-383">The following code returns values for `section1`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection.cshtml.cs?name=snippet)]

<span data-ttu-id="6f99c-384">Le code suivant retourne des valeurs pour `section2:subsection0` :</span><span class="sxs-lookup"><span data-stu-id="6f99c-384">The following code returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection2.cshtml.cs?name=snippet)]

<span data-ttu-id="6f99c-385">`GetSection` ne retourne jamais `null`.</span><span class="sxs-lookup"><span data-stu-id="6f99c-385">`GetSection` never returns `null`.</span></span> <span data-ttu-id="6f99c-386">Si aucune section correspondante n’est trouvée, une valeur `IConfigurationSection` vide est retournée.</span><span class="sxs-lookup"><span data-stu-id="6f99c-386">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="6f99c-387">Quand `GetSection` retourne une section correspondante, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> n’est pas rempli.</span><span class="sxs-lookup"><span data-stu-id="6f99c-387">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="6f99c-388"><xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> et <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> sont retournés quand la section existe.</span><span class="sxs-lookup"><span data-stu-id="6f99c-388">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren-and-exists"></a><span data-ttu-id="6f99c-389">GetChildren et EXISTS</span><span class="sxs-lookup"><span data-stu-id="6f99c-389">GetChildren and Exists</span></span>

<span data-ttu-id="6f99c-390">Le code suivant appelle [IConfiguration. GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) et retourne des valeurs pour `section2:subsection0` :</span><span class="sxs-lookup"><span data-stu-id="6f99c-390">The following code calls [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) and returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection4.cshtml.cs?name=snippet)]

<span data-ttu-id="6f99c-391">Le code précédent appelle [ConfigurationExtensions. Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) pour vérifier l’existence de la section :</span><span class="sxs-lookup"><span data-stu-id="6f99c-391">The preceding code calls [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to verify the  section exists:</span></span>

 <a name="boa"></a>

## <a name="bind-an-array"></a><span data-ttu-id="6f99c-392">Lier un tableau</span><span class="sxs-lookup"><span data-stu-id="6f99c-392">Bind an array</span></span>

<span data-ttu-id="6f99c-393">[ConfigurationBinder. bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) prend en charge les tableaux de liaison aux objets à l’aide d’index de tableau dans les clés de configuration.</span><span class="sxs-lookup"><span data-stu-id="6f99c-393">The [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="6f99c-394">Tout format de tableau qui expose un segment de clé numérique est capable d’effectuer une liaison de tableau à un tableau de classes [poco](https://wikipedia.org/wiki/Plain_Old_CLR_Object) .</span><span class="sxs-lookup"><span data-stu-id="6f99c-394">Any array format that exposes a numeric key segment is capable of array binding to a [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) class array.</span></span>

<span data-ttu-id="6f99c-395">Prenez *MyArray.js* à partir de l' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span><span class="sxs-lookup"><span data-stu-id="6f99c-395">Consider *MyArray.json* from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyArray.json)]

<span data-ttu-id="6f99c-396">Le code suivant ajoute *MyArray.js* aux fournisseurs de configuration :</span><span class="sxs-lookup"><span data-stu-id="6f99c-396">The following code adds *MyArray.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONarray.cs?name=snippet)]

<span data-ttu-id="6f99c-397">Le code suivant lit la configuration et affiche les valeurs :</span><span class="sxs-lookup"><span data-stu-id="6f99c-397">The following code reads the configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="6f99c-398">Le code précédent retourne la sortie suivante :</span><span class="sxs-lookup"><span data-stu-id="6f99c-398">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value00
Index: 1  Value: value10
Index: 2  Value: value20
Index: 3  Value: value40
Index: 4  Value: value50
```

<span data-ttu-id="6f99c-399">Dans la sortie précédente, l’index 3 a la valeur `value40` , ce qui correspond à `"4": "value40",` dans *MyArray.jssur*.</span><span class="sxs-lookup"><span data-stu-id="6f99c-399">In the preceding output, Index 3 has value `value40`, corresponding to `"4": "value40",` in *MyArray.json*.</span></span> <span data-ttu-id="6f99c-400">Les index de tableau liés sont continus et non liés à l’index de clé de configuration.</span><span class="sxs-lookup"><span data-stu-id="6f99c-400">The bound array indices are continuous and not bound to the configuration key index.</span></span> <span data-ttu-id="6f99c-401">Le Binder de configuration n’est pas en capacité à lier des valeurs null ou à créer des entrées NULL dans des objets liés</span><span class="sxs-lookup"><span data-stu-id="6f99c-401">The configuration binder isn't capable of binding null values or creating null entries in bound objects</span></span>

<span data-ttu-id="6f99c-402">Le code suivant charge la `array:entries` configuration avec la <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> méthode d’extension :</span><span class="sxs-lookup"><span data-stu-id="6f99c-402">The  following code loads the `array:entries` configuration with the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet)]

<span data-ttu-id="6f99c-403">Le code suivant lit la configuration dans `arrayDict` `Dictionary` et affiche les valeurs :</span><span class="sxs-lookup"><span data-stu-id="6f99c-403">The following code reads the configuration in the `arrayDict` `Dictionary` and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="6f99c-404">Le code précédent retourne la sortie suivante :</span><span class="sxs-lookup"><span data-stu-id="6f99c-404">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value4
Index: 4  Value: value5
```

<span data-ttu-id="6f99c-405">L’index &num;3 dans l’objet lié contient les données de configuration pour la clé de configuration `array:4` et sa valeur de `value4`.</span><span class="sxs-lookup"><span data-stu-id="6f99c-405">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="6f99c-406">Lorsque les données de configuration contenant un tableau sont liées, les index de tableau dans les clés de configuration sont utilisés pour itérer les données de configuration lors de la création de l’objet.</span><span class="sxs-lookup"><span data-stu-id="6f99c-406">When configuration data containing an array is bound, the array indices in the configuration keys are used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="6f99c-407">Une valeur null ne peut pas être conservée dans des données de configuration, et une entrée à valeur null n’est pas créée dans un objet lié quand un tableau dans des clés de configuration ignore un ou plusieurs index.</span><span class="sxs-lookup"><span data-stu-id="6f99c-407">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="6f99c-408">L’élément de configuration manquant pour l’index &num; 3 peut être fourni avant la liaison à l' `ArrayExample` instance par n’importe quel fournisseur de configuration qui lit la &num; paire clé/valeur de l’index 3.</span><span class="sxs-lookup"><span data-stu-id="6f99c-408">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that reads the index &num;3 key/value pair.</span></span> <span data-ttu-id="6f99c-409">Examinez le *Value3.jssuivant sur* le fichier à partir de l’exemple de téléchargement :</span><span class="sxs-lookup"><span data-stu-id="6f99c-409">Consider the following *Value3.json* file from the sample download:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/Value3.json)]

<span data-ttu-id="6f99c-410">Le code suivant comprend la configuration de *Value3.jssur* et `arrayDict` `Dictionary` :</span><span class="sxs-lookup"><span data-stu-id="6f99c-410">The following code includes configuration for *Value3.json* and the `arrayDict` `Dictionary`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet2)]

<span data-ttu-id="6f99c-411">Le code suivant lit la configuration précédente et affiche les valeurs :</span><span class="sxs-lookup"><span data-stu-id="6f99c-411">The following code reads the preceding configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="6f99c-412">Le code précédent retourne la sortie suivante :</span><span class="sxs-lookup"><span data-stu-id="6f99c-412">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value3
Index: 4  Value: value4
Index: 5  Value: value5
```

<span data-ttu-id="6f99c-413">Les fournisseurs de configuration personnalisés ne sont pas obligés d’implémenter la liaison de tableau.</span><span class="sxs-lookup"><span data-stu-id="6f99c-413">Custom configuration providers aren't required to implement array binding.</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="6f99c-414">Fournisseur de configuration personnalisé</span><span class="sxs-lookup"><span data-stu-id="6f99c-414">Custom configuration provider</span></span>

<span data-ttu-id="6f99c-415">L’exemple d’application montre comment créer un fournisseur de configuration de base qui lit les paires clé-valeur de configuration à partir d’une base de données à l’aide [d’Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="6f99c-415">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="6f99c-416">Le fournisseur présente les caractéristiques suivantes :</span><span class="sxs-lookup"><span data-stu-id="6f99c-416">The provider has the following characteristics:</span></span>

* <span data-ttu-id="6f99c-417">La base de données en mémoire EF est utilisée à des fins de démonstration.</span><span class="sxs-lookup"><span data-stu-id="6f99c-417">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="6f99c-418">Pour utiliser une base de données qui nécessite une chaîne de connexion, implémentez un autre `ConfigurationBuilder` pour fournir la chaîne de connexion à partir d’un autre fournisseur de configuration.</span><span class="sxs-lookup"><span data-stu-id="6f99c-418">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="6f99c-419">Le fournisseur lit une table de base de données dans la configuration au démarrage.</span><span class="sxs-lookup"><span data-stu-id="6f99c-419">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="6f99c-420">Le fournisseur n’interroge pas la base de données par clé.</span><span class="sxs-lookup"><span data-stu-id="6f99c-420">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="6f99c-421">Le rechargement en cas de changement n’est pas implémenté, par conséquent, la mise à jour la base de données après le démarrage de l’application n’a aucun effet sur la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="6f99c-421">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="6f99c-422">Définissez une entité `EFConfigurationValue` pour le stockage des valeurs de configuration dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="6f99c-422">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="6f99c-423">*Models/EFConfigurationValue.cs* :</span><span class="sxs-lookup"><span data-stu-id="6f99c-423">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="6f99c-424">Ajoutez un `EFConfigurationContext` pour stocker les valeurs configurées et y accéder.</span><span class="sxs-lookup"><span data-stu-id="6f99c-424">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="6f99c-425">*EFConfigurationProvider/EFConfigurationContext.cs* :</span><span class="sxs-lookup"><span data-stu-id="6f99c-425">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="6f99c-426">Créez une classe qui implémente <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="6f99c-426">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="6f99c-427">*EFConfigurationProvider/EFConfigurationSource.cs* :</span><span class="sxs-lookup"><span data-stu-id="6f99c-427">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="6f99c-428">Créez le fournisseur de configuration personnalisé en héritant de <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="6f99c-428">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="6f99c-429">Le fournisseur de configuration initialise la base de données quand elle est vide.</span><span class="sxs-lookup"><span data-stu-id="6f99c-429">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="6f99c-430">Étant donné que les [clés de configuration ne](#keys)respectent pas la casse, le dictionnaire utilisé pour initialiser la base de données est créé avec le comparateur ne respectant pas la casse ([StringComparer. OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span><span class="sxs-lookup"><span data-stu-id="6f99c-430">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="6f99c-431">*EFConfigurationProvider/EFConfigurationProvider.cs* :</span><span class="sxs-lookup"><span data-stu-id="6f99c-431">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="6f99c-432">Une méthode d’extension `AddEFConfiguration` permet d’ajouter la source de configuration à un `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="6f99c-432">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="6f99c-433">*Extensions/EntityFrameworkExtensions.cs* :</span><span class="sxs-lookup"><span data-stu-id="6f99c-433">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="6f99c-434">Le code suivant montre comment utiliser le `EFConfigurationProvider` personnalisé dans *Program.cs* :</span><span class="sxs-lookup"><span data-stu-id="6f99c-434">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples_snippets/3.x/ConfigurationSample/Program.cs?highlight=7-8)]

<a name="acs"></a>

## <a name="access-configuration-in-startup"></a><span data-ttu-id="6f99c-435">Configuration de l’accès au démarrage</span><span class="sxs-lookup"><span data-stu-id="6f99c-435">Access configuration in Startup</span></span>

<span data-ttu-id="6f99c-436">Le code suivant affiche les données de configuration dans les `Startup` méthodes :</span><span class="sxs-lookup"><span data-stu-id="6f99c-436">The following code displays configuration data in `Startup` methods:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/StartupKey.cs?name=snippet&highlight=13,18)]

<span data-ttu-id="6f99c-437">Pour obtenir un exemple d’accès à la configuration à l’aide des méthodes pratiques de démarrage, consultez [Démarrage de l’application : méthodes pratiques](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="6f99c-437">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-no-locrazor-pages"></a><span data-ttu-id="6f99c-438">Configuration de l’accès dans les Razor pages</span><span class="sxs-lookup"><span data-stu-id="6f99c-438">Access configuration in Razor Pages</span></span>

<span data-ttu-id="6f99c-439">Le code suivant affiche les données de configuration dans une Razor page :</span><span class="sxs-lookup"><span data-stu-id="6f99c-439">The following code displays configuration data in a Razor Page:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Pages/Test5.cshtml)]

<span data-ttu-id="6f99c-440">Dans le code suivant, `MyOptions` est ajouté au conteneur de services avec <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> et lié à la configuration :</span><span class="sxs-lookup"><span data-stu-id="6f99c-440">In the following code, `MyOptions` is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="6f99c-441">La balise suivante utilise la [`@inject`](xref:mvc/views/razor#inject) Razor directive pour résoudre et afficher les valeurs des options :</span><span class="sxs-lookup"><span data-stu-id="6f99c-441">The following markup uses the [`@inject`](xref:mvc/views/razor#inject) Razor directive to resolve and display the options values:</span></span>

[!code-cshtml[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Pages/Test3.cshtml)]

## <a name="access-configuration-in-a-mvc-view-file"></a><span data-ttu-id="6f99c-442">Configuration de l’accès dans un fichier de vue MVC</span><span class="sxs-lookup"><span data-stu-id="6f99c-442">Access configuration in a MVC view file</span></span>

<span data-ttu-id="6f99c-443">Le code suivant affiche les données de configuration dans une vue MVC :</span><span class="sxs-lookup"><span data-stu-id="6f99c-443">The following code displays configuration data in a MVC view:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Views/Home2/Index.cshtml)]

## <a name="configure-options-with-a-delegate"></a><span data-ttu-id="6f99c-444">Configurer des options avec un délégué</span><span class="sxs-lookup"><span data-stu-id="6f99c-444">Configure options with a delegate</span></span>

<span data-ttu-id="6f99c-445">Les options configurées dans un délégué remplacent les valeurs définies dans les fournisseurs de configuration.</span><span class="sxs-lookup"><span data-stu-id="6f99c-445">Options configured in a delegate override values set in the configuration providers.</span></span>

<span data-ttu-id="6f99c-446">La configuration d’options avec un délégué est illustrée dans l’exemple 2 de l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="6f99c-446">Configuring options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="6f99c-447">Dans le code suivant, un <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service est ajouté au conteneur de services.</span><span class="sxs-lookup"><span data-stu-id="6f99c-447">In the following code, an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="6f99c-448">Il utilise un délégué pour configurer des valeurs pour `MyOptions` :</span><span class="sxs-lookup"><span data-stu-id="6f99c-448">It uses a delegate to configure values for `MyOptions`:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup2.cs?name=snippet_Example2)]

<span data-ttu-id="6f99c-449">Le code suivant affiche les valeurs des options :</span><span class="sxs-lookup"><span data-stu-id="6f99c-449">The following code displays the options values:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Test2.cshtml.cs?name=snippet)]

<span data-ttu-id="6f99c-450">Dans l’exemple précédent, les valeurs de `Option1` et `Option2` sont spécifiées dans *appsettings.jssur* , puis remplacé par le délégué configuré.</span><span class="sxs-lookup"><span data-stu-id="6f99c-450">In the preceding example, the values of `Option1` and `Option2` are specified in *appsettings.json* and then overridden by the configured delegate.</span></span>

<a name="hvac"></a>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="6f99c-451">Configuration de l’hôte ou configuration de l’application</span><span class="sxs-lookup"><span data-stu-id="6f99c-451">Host versus app configuration</span></span>

<span data-ttu-id="6f99c-452">Avant que l’application ne soit configurée et démarrée, un *hôte* est configuré et lancé.</span><span class="sxs-lookup"><span data-stu-id="6f99c-452">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="6f99c-453">L’hôte est responsable de la gestion du démarrage et de la durée de vie des applications.</span><span class="sxs-lookup"><span data-stu-id="6f99c-453">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="6f99c-454">L’application et l’hôte sont configurés à l’aide des fournisseurs de configuration décrits dans cette rubrique.</span><span class="sxs-lookup"><span data-stu-id="6f99c-454">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="6f99c-455">Les paires clé-valeur de la configuration de l’hôte sont également incluses dans la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="6f99c-455">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="6f99c-456">Pour plus d’informations sur l’utilisation des fournisseurs de configuration lors de la génération de l’hôte et l’impact des sources de configuration sur la configuration de l’hôte, consultez <xref:fundamentals/index#host>.</span><span class="sxs-lookup"><span data-stu-id="6f99c-456">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

<a name="dhc"></a>

## <a name="default-host-configuration"></a><span data-ttu-id="6f99c-457">Configuration de l’hôte par défaut</span><span class="sxs-lookup"><span data-stu-id="6f99c-457">Default host configuration</span></span>

<span data-ttu-id="6f99c-458">Pour plus de détails sur la configuration par défaut lors de l’utilisation de l’[hôte Web](xref:fundamentals/host/web-host), consultez la [version ASP.NET Core 2.2. de cette rubrique](?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="6f99c-458">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="6f99c-459">La configuration de l’hôte est fournie à partir des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="6f99c-459">Host configuration is provided from:</span></span>
  * <span data-ttu-id="6f99c-460">Les variables d’environnement précédées du préfixe `DOTNET_` (par exemple, `DOTNET_ENVIRONMENT` ) à l’aide du [fournisseur de configuration des variables d’environnement](#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="6f99c-460">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables configuration provider](#environment-variables).</span></span> <span data-ttu-id="6f99c-461">Le préfixe (`DOTNET_`) est supprimé lorsque les paires clé-valeur de la configuration sont chargées.</span><span class="sxs-lookup"><span data-stu-id="6f99c-461">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="6f99c-462">Arguments de ligne de commande à l’aide du [fournisseur de configuration de ligne de commande](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="6f99c-462">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="6f99c-463">La configuration par défaut de l’hôte Web est établie (`ConfigureWebHostDefaults`) :</span><span class="sxs-lookup"><span data-stu-id="6f99c-463">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="6f99c-464">Kestrel est utilisé comme serveur web et configuré à l’aide des fournisseurs de configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="6f99c-464">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="6f99c-465">Ajoutez l’intergiciel de filtrage d’hôtes.</span><span class="sxs-lookup"><span data-stu-id="6f99c-465">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="6f99c-466">Ajoutez l’intergiciel d’en-têtes transférés si la variable d'environnement `ASPNETCORE_FORWARDEDHEADERS_ENABLED` est définie sur `true`.</span><span class="sxs-lookup"><span data-stu-id="6f99c-466">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="6f99c-467">Activez l’intégration d’IIS.</span><span class="sxs-lookup"><span data-stu-id="6f99c-467">Enable IIS integration.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="6f99c-468">Autre configuration</span><span class="sxs-lookup"><span data-stu-id="6f99c-468">Other configuration</span></span>

<span data-ttu-id="6f99c-469">Cette rubrique se rapporte uniquement à la configuration de l' *application*.</span><span class="sxs-lookup"><span data-stu-id="6f99c-469">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="6f99c-470">D’autres aspects de l’exécution et de l’hébergement des applications ASP.NET Core sont configurés à l’aide des fichiers de configuration non traités dans cette rubrique :</span><span class="sxs-lookup"><span data-stu-id="6f99c-470">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="6f99c-471">*launch.js* / *launchSettings.jssur* sont des fichiers de configuration d’outils pour l’environnement de développement, décrits ci-après :</span><span class="sxs-lookup"><span data-stu-id="6f99c-471">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="6f99c-472">Dans <xref:fundamentals/environments#development> .</span><span class="sxs-lookup"><span data-stu-id="6f99c-472">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="6f99c-473">Dans l’ensemble de la documentation dans lequel les fichiers sont utilisés pour configurer des applications ASP.NET Core pour les scénarios de développement.</span><span class="sxs-lookup"><span data-stu-id="6f99c-473">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="6f99c-474">*web.config* est un fichier de configuration de serveur, décrit dans les rubriques suivantes :</span><span class="sxs-lookup"><span data-stu-id="6f99c-474">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="6f99c-475">Les variables d’environnement définies dans *launchSettings.jslors de* la substitution de celles définies dans l’environnement système.</span><span class="sxs-lookup"><span data-stu-id="6f99c-475">Environment variables set in *launchSettings.json* override those set in the system environment.</span></span>

<span data-ttu-id="6f99c-476">Pour plus d’informations sur la migration de la configuration d’application à partir de versions antérieures de ASP.NET, consultez <xref:migration/proper-to-2x/index#store-configurations> .</span><span class="sxs-lookup"><span data-stu-id="6f99c-476">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="6f99c-477">Ajouter la configuration à partir d’un assembly externe</span><span class="sxs-lookup"><span data-stu-id="6f99c-477">Add configuration from an external assembly</span></span>

<span data-ttu-id="6f99c-478">Une implémentation de <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> permet d’ajouter des améliorations à une application au démarrage à partir d’un assembly externe, en dehors de la classe `Startup` de l’application.</span><span class="sxs-lookup"><span data-stu-id="6f99c-478">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="6f99c-479">Pour plus d'informations, consultez <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="6f99c-479">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6f99c-480">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="6f99c-480">Additional resources</span></span>

* [<span data-ttu-id="6f99c-481">Code source de configuration</span><span class="sxs-lookup"><span data-stu-id="6f99c-481">Configuration source code</span></span>](https://github.com/dotnet/extensions/tree/master/src/Configuration)
* <xref:fundamentals/configuration/options>
* <xref:blazor/fundamentals/configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="6f99c-482">La configuration d’application dans ASP.NET Core est basée sur des paires clé-valeur établies par les *fournisseurs de configuration*.</span><span class="sxs-lookup"><span data-stu-id="6f99c-482">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="6f99c-483">Les fournisseurs de configuration lisent les données de configuration dans les paires clé-valeur à partir de diverses sources de configuration :</span><span class="sxs-lookup"><span data-stu-id="6f99c-483">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="6f99c-484">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="6f99c-484">Azure Key Vault</span></span>
* <span data-ttu-id="6f99c-485">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="6f99c-485">Azure App Configuration</span></span>
* <span data-ttu-id="6f99c-486">Arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="6f99c-486">Command-line arguments</span></span>
* <span data-ttu-id="6f99c-487">Fournisseurs personnalisés (installés ou créés)</span><span class="sxs-lookup"><span data-stu-id="6f99c-487">Custom providers (installed or created)</span></span>
* <span data-ttu-id="6f99c-488">Fichiers de répertoire</span><span class="sxs-lookup"><span data-stu-id="6f99c-488">Directory files</span></span>
* <span data-ttu-id="6f99c-489">Variables d'environnement</span><span class="sxs-lookup"><span data-stu-id="6f99c-489">Environment variables</span></span>
* <span data-ttu-id="6f99c-490">Objets .NET en mémoire</span><span class="sxs-lookup"><span data-stu-id="6f99c-490">In-memory .NET objects</span></span>
* <span data-ttu-id="6f99c-491">Fichiers de paramètres</span><span class="sxs-lookup"><span data-stu-id="6f99c-491">Settings files</span></span>

<span data-ttu-id="6f99c-492">Les packages de configuration pour les scénarios de fournisseur de configuration courants ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) sont inclus implicitement dans le [métapaquet Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="6f99c-492">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="6f99c-493">Les exemples de code qui suivent et dans l’échantillon d’application utilisent l’espace de noms <xref:Microsoft.Extensions.Configuration> :</span><span class="sxs-lookup"><span data-stu-id="6f99c-493">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="6f99c-494">Le *modèle d’options* est une extension des concepts de configuration décrits dans cette rubrique.</span><span class="sxs-lookup"><span data-stu-id="6f99c-494">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="6f99c-495">Les options utilisent des classes pour représenter les groupes de paramètres associés.</span><span class="sxs-lookup"><span data-stu-id="6f99c-495">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="6f99c-496">Pour plus d'informations, consultez <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="6f99c-496">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="6f99c-497">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6f99c-497">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="6f99c-498">Configuration de l’hôte ou configuration de l’application</span><span class="sxs-lookup"><span data-stu-id="6f99c-498">Host versus app configuration</span></span>

<span data-ttu-id="6f99c-499">Avant que l’application ne soit configurée et démarrée, un *hôte* est configuré et lancé.</span><span class="sxs-lookup"><span data-stu-id="6f99c-499">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="6f99c-500">L’hôte est responsable de la gestion du démarrage et de la durée de vie des applications.</span><span class="sxs-lookup"><span data-stu-id="6f99c-500">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="6f99c-501">L’application et l’hôte sont configurés à l’aide des fournisseurs de configuration décrits dans cette rubrique.</span><span class="sxs-lookup"><span data-stu-id="6f99c-501">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="6f99c-502">Les paires clé-valeur de la configuration de l’hôte sont également incluses dans la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="6f99c-502">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="6f99c-503">Pour plus d’informations sur l’utilisation des fournisseurs de configuration lors de la génération de l’hôte et l’impact des sources de configuration sur la configuration de l’hôte, consultez <xref:fundamentals/index#host>.</span><span class="sxs-lookup"><span data-stu-id="6f99c-503">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="6f99c-504">Autre configuration</span><span class="sxs-lookup"><span data-stu-id="6f99c-504">Other configuration</span></span>

<span data-ttu-id="6f99c-505">Cette rubrique se rapporte uniquement à la configuration de l' *application*.</span><span class="sxs-lookup"><span data-stu-id="6f99c-505">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="6f99c-506">D’autres aspects de l’exécution et de l’hébergement des applications ASP.NET Core sont configurés à l’aide des fichiers de configuration non traités dans cette rubrique :</span><span class="sxs-lookup"><span data-stu-id="6f99c-506">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="6f99c-507">*launch.js* / *launchSettings.jssur* sont des fichiers de configuration d’outils pour l’environnement de développement, décrits ci-après :</span><span class="sxs-lookup"><span data-stu-id="6f99c-507">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="6f99c-508">Dans <xref:fundamentals/environments#development> .</span><span class="sxs-lookup"><span data-stu-id="6f99c-508">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="6f99c-509">Dans l’ensemble de la documentation dans lequel les fichiers sont utilisés pour configurer des applications ASP.NET Core pour les scénarios de développement.</span><span class="sxs-lookup"><span data-stu-id="6f99c-509">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="6f99c-510">*web.config* est un fichier de configuration de serveur, décrit dans les rubriques suivantes :</span><span class="sxs-lookup"><span data-stu-id="6f99c-510">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="6f99c-511">Pour plus d’informations sur la migration de la configuration d’application à partir de versions antérieures de ASP.NET, consultez <xref:migration/proper-to-2x/index#store-configurations> .</span><span class="sxs-lookup"><span data-stu-id="6f99c-511">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="6f99c-512">Configuration par défaut</span><span class="sxs-lookup"><span data-stu-id="6f99c-512">Default configuration</span></span>

<span data-ttu-id="6f99c-513">Les applications web basées sur les modèles ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) appellent <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> pendant la création d’un hôte.</span><span class="sxs-lookup"><span data-stu-id="6f99c-513">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="6f99c-514">`CreateDefaultBuilder` fournit la configuration par défaut de l’application dans l’ordre suivant :</span><span class="sxs-lookup"><span data-stu-id="6f99c-514">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="6f99c-515">Les éléments suivants s’appliquent aux applications qui utilisent l’[hôte web](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="6f99c-515">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="6f99c-516">Pour plus de détails sur la configuration par défaut lors de l’utilisation de l’[hôte générique](xref:fundamentals/host/generic-host), consultez la [dernière version de cette rubrique](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="6f99c-516">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="6f99c-517">La configuration de l’hôte est fournie à partir des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="6f99c-517">Host configuration is provided from:</span></span>
  * <span data-ttu-id="6f99c-518">Des variables d’environnement préfixées avec `ASPNETCORE_` (par exemple, `ASPNETCORE_ENVIRONMENT`) à l’aide du [Fournisseur de configuration des variables d’environnement](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="6f99c-518">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="6f99c-519">Le préfixe (`ASPNETCORE_`) est supprimé lorsque les paires clé-valeur de la configuration sont chargées.</span><span class="sxs-lookup"><span data-stu-id="6f99c-519">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="6f99c-520">Des arguments de ligne de commande à l’aide du [Fournisseur de configuration de ligne de commande](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="6f99c-520">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="6f99c-521">La configuration de l’application est fournie à partir des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="6f99c-521">App configuration is provided from:</span></span>
  * <span data-ttu-id="6f99c-522">*appSettings.JSON* à l’aide du [Fournisseur de configuration de fichier](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="6f99c-522">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="6f99c-523">*appsettings.{Environment}.json* à l’aide du [Fournisseur de configuration de fichier](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="6f99c-523">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="6f99c-524">L’outil [Secret Manager (Gestionnaire de secrets)](xref:security/app-secrets) quand l’application s’exécute dans l’environnement `Development` à l’aide de l’assembly d’entrée.</span><span class="sxs-lookup"><span data-stu-id="6f99c-524">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="6f99c-525">Des variables d’environnement à l’aide du [Fournisseur de configuration des variables d’environnement](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="6f99c-525">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="6f99c-526">Des arguments de ligne de commande à l’aide du [Fournisseur de configuration de ligne de commande](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="6f99c-526">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="6f99c-527">Sécurité</span><span class="sxs-lookup"><span data-stu-id="6f99c-527">Security</span></span>

<span data-ttu-id="6f99c-528">Adoptez les pratiques suivantes pour sécuriser les données de configuration sensibles :</span><span class="sxs-lookup"><span data-stu-id="6f99c-528">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="6f99c-529">Ne stockez jamais des mots de passe ou d’autres données sensibles dans le code du fournisseur de configuration ou dans les fichiers de configuration en texte clair.</span><span class="sxs-lookup"><span data-stu-id="6f99c-529">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="6f99c-530">N’utilisez aucun secret de production dans les environnements de développement ou de test.</span><span class="sxs-lookup"><span data-stu-id="6f99c-530">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="6f99c-531">Spécifiez les secrets en dehors du projet afin qu’ils ne puissent pas être validés par inadvertance dans un référentiel de code source.</span><span class="sxs-lookup"><span data-stu-id="6f99c-531">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="6f99c-532">Pour plus d'informations, voir les rubriques suivantes :</span><span class="sxs-lookup"><span data-stu-id="6f99c-532">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="6f99c-533"><xref:security/app-secrets>: Fournit des conseils sur l’utilisation de variables d’environnement pour stocker des données sensibles.</span><span class="sxs-lookup"><span data-stu-id="6f99c-533"><xref:security/app-secrets>: Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="6f99c-534">Secret Manager utilise le fournisseur de configuration de fichier pour stocker les secrets utilisateur dans un fichier JSON sur le système local.</span><span class="sxs-lookup"><span data-stu-id="6f99c-534">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="6f99c-535">Le fournisseur de configuration de fichier est décrit plus loin dans cette rubrique.</span><span class="sxs-lookup"><span data-stu-id="6f99c-535">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="6f99c-536">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) stocke en toute sécurité des secrets d’application pour les applications ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6f99c-536">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="6f99c-537">Pour plus d'informations, consultez <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="6f99c-537">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="6f99c-538">Données de configuration hiérarchiques</span><span class="sxs-lookup"><span data-stu-id="6f99c-538">Hierarchical configuration data</span></span>

<span data-ttu-id="6f99c-539">L’API Configuration est capable de maintenir des données de configuration hiérarchiques en aplanissant les données hiérarchiques à l’aide d’un délimiteur dans les clés de configuration.</span><span class="sxs-lookup"><span data-stu-id="6f99c-539">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="6f99c-540">Dans le fichier JSON suivant, quatre clés existent dans une structure hiérarchique à deux sections :</span><span class="sxs-lookup"><span data-stu-id="6f99c-540">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="6f99c-541">Lorsque le fichier est lu dans la configuration, des clés uniques sont créées pour gérer la structure des données hiérarchiques d’origine de la source de configuration.</span><span class="sxs-lookup"><span data-stu-id="6f99c-541">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="6f99c-542">Les sections et les clés sont aplanies à l’aide d’un signe deux-points (`:`) pour conserver la structure d’origine :</span><span class="sxs-lookup"><span data-stu-id="6f99c-542">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="6f99c-543">section0:key0</span><span class="sxs-lookup"><span data-stu-id="6f99c-543">section0:key0</span></span>
* <span data-ttu-id="6f99c-544">section0:key1</span><span class="sxs-lookup"><span data-stu-id="6f99c-544">section0:key1</span></span>
* <span data-ttu-id="6f99c-545">section1:key0</span><span class="sxs-lookup"><span data-stu-id="6f99c-545">section1:key0</span></span>
* <span data-ttu-id="6f99c-546">section1:key1</span><span class="sxs-lookup"><span data-stu-id="6f99c-546">section1:key1</span></span>

<span data-ttu-id="6f99c-547">Les méthodes <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> et <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> sont disponibles pour isoler les sections et les enfants d’une section dans les données de configuration.</span><span class="sxs-lookup"><span data-stu-id="6f99c-547"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="6f99c-548">Ces méthodes sont décrites plus loin dans [GetSection, GetChildren et Exists](#getsection-getchildren-and-exists).</span><span class="sxs-lookup"><span data-stu-id="6f99c-548">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="6f99c-549">Conventions</span><span class="sxs-lookup"><span data-stu-id="6f99c-549">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="6f99c-550">Sources et fournisseurs</span><span class="sxs-lookup"><span data-stu-id="6f99c-550">Sources and providers</span></span>

<span data-ttu-id="6f99c-551">Au démarrage de l’application, les sources de configuration sont lues dans l’ordre où leurs fournisseurs de configuration sont spécifiés.</span><span class="sxs-lookup"><span data-stu-id="6f99c-551">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="6f99c-552">Les fournisseurs de configuration qui implémentent la détection des modifications peuvent recharger la configuration lorsqu’un paramètre sous-jacent est modifié.</span><span class="sxs-lookup"><span data-stu-id="6f99c-552">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="6f99c-553">Par exemple, le fournisseur de configuration de fichier (décrit plus loin dans cette rubrique) et le [fournisseur de configuration Azure Key Vault](xref:security/key-vault-configuration) implémentent la détection des modifications.</span><span class="sxs-lookup"><span data-stu-id="6f99c-553">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="6f99c-554"><xref:Microsoft.Extensions.Configuration.IConfiguration> est disponible dans le conteneur d’[injection de dépendances](xref:fundamentals/dependency-injection) de l’application.</span><span class="sxs-lookup"><span data-stu-id="6f99c-554"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="6f99c-555"><xref:Microsoft.Extensions.Configuration.IConfiguration> peut être injecté dans une Razor page <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> ou un MVC <xref:Microsoft.AspNetCore.Mvc.Controller> pour obtenir la configuration de la classe.</span><span class="sxs-lookup"><span data-stu-id="6f99c-555"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="6f99c-556">Dans les exemples suivants, le `_config` champ est utilisé pour accéder aux valeurs de configuration :</span><span class="sxs-lookup"><span data-stu-id="6f99c-556">In the following examples, the `_config` field is used to access configuration values:</span></span>

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

<span data-ttu-id="6f99c-557">Les fournisseurs de configuration ne peuvent pas utiliser le DI, car celui-ci n’est pas disponible lorsque les fournisseurs sont configurés par l’hôte.</span><span class="sxs-lookup"><span data-stu-id="6f99c-557">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="6f99c-558">Touches</span><span class="sxs-lookup"><span data-stu-id="6f99c-558">Keys</span></span>

<span data-ttu-id="6f99c-559">Les clés de configuration adoptent les conventions suivantes :</span><span class="sxs-lookup"><span data-stu-id="6f99c-559">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="6f99c-560">Les clés ne respectent pas la casse.</span><span class="sxs-lookup"><span data-stu-id="6f99c-560">Keys are case-insensitive.</span></span> <span data-ttu-id="6f99c-561">Par exemple, `ConnectionString` et `connectionstring` sont traités en tant que clés équivalentes.</span><span class="sxs-lookup"><span data-stu-id="6f99c-561">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="6f99c-562">Si une valeur pour la même clé est définie par des fournisseurs de configuration identiques ou différents, la valeur utilisée est la dernière valeur définie sur la clé.</span><span class="sxs-lookup"><span data-stu-id="6f99c-562">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span> <span data-ttu-id="6f99c-563">Pour plus d’informations sur les clés JSON en double, consultez [ce problème GitHub](https://github.com/dotnet/extensions/issues/2381).</span><span class="sxs-lookup"><span data-stu-id="6f99c-563">For more information on duplicate JSON keys, see [this GitHub issue](https://github.com/dotnet/extensions/issues/2381).</span></span>
* <span data-ttu-id="6f99c-564">Clés hiérarchiques</span><span class="sxs-lookup"><span data-stu-id="6f99c-564">Hierarchical keys</span></span>
  * <span data-ttu-id="6f99c-565">Dans l’API Configuration, un séparateur sous forme de signe deux-points (`:`) fonctionne sur toutes les plateformes.</span><span class="sxs-lookup"><span data-stu-id="6f99c-565">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="6f99c-566">Dans les variables d’environnement, un séparateur sous forme de signe deux-points peut ne pas fonctionner sur toutes les plateformes.</span><span class="sxs-lookup"><span data-stu-id="6f99c-566">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="6f99c-567">Un trait de soulignement double (`__`) est pris en charge par toutes les plateformes et automatiquement transformé en signe deux-points.</span><span class="sxs-lookup"><span data-stu-id="6f99c-567">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="6f99c-568">Dans Azure Key Vault, les clés hiérarchiques utilisent `--` (deux tirets) comme séparateur.</span><span class="sxs-lookup"><span data-stu-id="6f99c-568">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="6f99c-569">Écrivez du code pour remplacer les tirets par un signe deux-points lorsque les secrets sont chargés dans la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="6f99c-569">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="6f99c-570"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> prend en charge la liaison de tableaux à des objets à l’aide d’index de tableau dans les clés de configuration.</span><span class="sxs-lookup"><span data-stu-id="6f99c-570">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="6f99c-571">La liaison de tableau est décrite dans la section [Lier un tableau à une classe](#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="6f99c-571">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="6f99c-572">Valeurs</span><span class="sxs-lookup"><span data-stu-id="6f99c-572">Values</span></span>

<span data-ttu-id="6f99c-573">Les valeurs de configuration adoptent les conventions suivantes :</span><span class="sxs-lookup"><span data-stu-id="6f99c-573">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="6f99c-574">Les valeurs sont des chaînes.</span><span class="sxs-lookup"><span data-stu-id="6f99c-574">Values are strings.</span></span>
* <span data-ttu-id="6f99c-575">Les valeurs NULL ne peuvent pas être stockées dans la configuration ou liées à des objets.</span><span class="sxs-lookup"><span data-stu-id="6f99c-575">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="6f99c-576">Fournisseurs</span><span class="sxs-lookup"><span data-stu-id="6f99c-576">Providers</span></span>

<span data-ttu-id="6f99c-577">Le tableau suivant présente les fournisseurs de configuration disponibles pour les applications ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6f99c-577">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="6f99c-578">Fournisseur</span><span class="sxs-lookup"><span data-stu-id="6f99c-578">Provider</span></span> | <span data-ttu-id="6f99c-579">Fournit la configuration à partir de&hellip;</span><span class="sxs-lookup"><span data-stu-id="6f99c-579">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="6f99c-580">[Fournisseur de configuration Azure Key Vault](xref:security/key-vault-configuration) (rubrique *Sécurité*)</span><span class="sxs-lookup"><span data-stu-id="6f99c-580">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="6f99c-581">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="6f99c-581">Azure Key Vault</span></span> |
| <span data-ttu-id="6f99c-582">[Fournisseur Azure App Configuration](/azure/azure-app-configuration/quickstart-aspnet-core-app) (documentation Azure)</span><span class="sxs-lookup"><span data-stu-id="6f99c-582">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="6f99c-583">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="6f99c-583">Azure App Configuration</span></span> |
| [<span data-ttu-id="6f99c-584">Fournisseur de configuration de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="6f99c-584">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="6f99c-585">Paramètres de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="6f99c-585">Command-line parameters</span></span> |
| [<span data-ttu-id="6f99c-586">Fournisseur de configuration personnalisé</span><span class="sxs-lookup"><span data-stu-id="6f99c-586">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="6f99c-587">Source personnalisée</span><span class="sxs-lookup"><span data-stu-id="6f99c-587">Custom source</span></span> |
| [<span data-ttu-id="6f99c-588">Fournisseur de configuration de variables d’environnement</span><span class="sxs-lookup"><span data-stu-id="6f99c-588">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="6f99c-589">Variables d'environnement</span><span class="sxs-lookup"><span data-stu-id="6f99c-589">Environment variables</span></span> |
| [<span data-ttu-id="6f99c-590">Fournisseur de configuration de fichier</span><span class="sxs-lookup"><span data-stu-id="6f99c-590">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="6f99c-591">Fichiers (INI, JSON, XML)</span><span class="sxs-lookup"><span data-stu-id="6f99c-591">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="6f99c-592">Fournisseur de configuration clé par fichier</span><span class="sxs-lookup"><span data-stu-id="6f99c-592">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="6f99c-593">Fichiers de répertoire</span><span class="sxs-lookup"><span data-stu-id="6f99c-593">Directory files</span></span> |
| [<span data-ttu-id="6f99c-594">Fournisseur de configuration de mémoire</span><span class="sxs-lookup"><span data-stu-id="6f99c-594">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="6f99c-595">Collections en mémoire</span><span class="sxs-lookup"><span data-stu-id="6f99c-595">In-memory collections</span></span> |
| <span data-ttu-id="6f99c-596">[Secrets utilisateur (Secret Manager)](xref:security/app-secrets) (rubrique *Sécurité*)</span><span class="sxs-lookup"><span data-stu-id="6f99c-596">[User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="6f99c-597">Fichier dans le répertoire de profil utilisateur</span><span class="sxs-lookup"><span data-stu-id="6f99c-597">File in the user profile directory</span></span> |

<span data-ttu-id="6f99c-598">Au démarrage, les sources de configuration sont lues dans l’ordre où leurs fournisseurs de configuration sont spécifiés.</span><span class="sxs-lookup"><span data-stu-id="6f99c-598">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="6f99c-599">Les fournisseurs de configuration décrits dans cette rubrique sont décrits par ordre alphabétique, et non pas dans l’ordre dans lequel le code les réorganise.</span><span class="sxs-lookup"><span data-stu-id="6f99c-599">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="6f99c-600">Commandez des fournisseurs de configuration dans le code pour répondre aux priorités des sources de configuration sous-jacentes requises par l’application.</span><span class="sxs-lookup"><span data-stu-id="6f99c-600">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="6f99c-601">Une séquence type des fournisseurs de configuration est la suivante :</span><span class="sxs-lookup"><span data-stu-id="6f99c-601">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="6f99c-602">Fichiers (*appsettings.json*, *appsettings.{Environment}.json*, où `{Environment}` est l'environnement d’hébergement actuel de l'application)</span><span class="sxs-lookup"><span data-stu-id="6f99c-602">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="6f99c-603">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="6f99c-603">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="6f99c-604">[Secrets utilisateur (Secret Manager)](xref:security/app-secrets) (dans l’environnement de développement uniquement)</span><span class="sxs-lookup"><span data-stu-id="6f99c-604">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="6f99c-605">Variables d'environnement</span><span class="sxs-lookup"><span data-stu-id="6f99c-605">Environment variables</span></span>
1. <span data-ttu-id="6f99c-606">Arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="6f99c-606">Command-line arguments</span></span>

<span data-ttu-id="6f99c-607">Une pratique courante consiste à placer le Fournisseur de configuration de ligne de commande en dernier dans une série de fournisseurs pour permettre aux arguments de ligne de commande de remplacer la configuration définie par les autres fournisseurs.</span><span class="sxs-lookup"><span data-stu-id="6f99c-607">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="6f99c-608">La séquence de fournisseurs précédente est utilisée lors de l’initialisation d’un nouveau générateur d’hôte `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="6f99c-608">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="6f99c-609">Pour plus d’informations, consultez la section [Configuration par défaut](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="6f99c-609">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="6f99c-610">Configurer le générateur d’ordinateur hôte avec UseConfiguration</span><span class="sxs-lookup"><span data-stu-id="6f99c-610">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="6f99c-611">Pour configurer le générateur d’ordinateur hôte, appelez <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> sur le générateur d’hôte avec la configuration.</span><span class="sxs-lookup"><span data-stu-id="6f99c-611">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

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

## <a name="configureappconfiguration"></a><span data-ttu-id="6f99c-612">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="6f99c-612">ConfigureAppConfiguration</span></span>

<span data-ttu-id="6f99c-613">Appelez `ConfigureAppConfiguration` quand vous créez l’hôte pour spécifier les fournisseurs de configuration de l’application en plus de ceux ajoutés automatiquement par `CreateDefaultBuilder` :</span><span class="sxs-lookup"><span data-stu-id="6f99c-613">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="6f99c-614">Remplacez la configuration précédente par des arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="6f99c-614">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="6f99c-615">Pour fournir une configuration d’application pouvant être remplacée par des arguments de ligne de commande, appelez `AddCommandLine` en dernier :</span><span class="sxs-lookup"><span data-stu-id="6f99c-615">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="6f99c-616">Supprimer les fournisseurs ajoutés par CreateDefaultBuilder</span><span class="sxs-lookup"><span data-stu-id="6f99c-616">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="6f99c-617">Pour supprimer les fournisseurs ajoutés par `CreateDefaultBuilder` , appelez d’abord [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) sur [IConfigurationBuilder. sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) :</span><span class="sxs-lookup"><span data-stu-id="6f99c-617">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="6f99c-618">Utiliser la configuration lors du démarrage de l’application</span><span class="sxs-lookup"><span data-stu-id="6f99c-618">Consume configuration during app startup</span></span>

<span data-ttu-id="6f99c-619">La configuration fournie à l’application dans `ConfigureAppConfiguration` est disponible lors du démarrage de l’application, notamment `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="6f99c-619">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="6f99c-620">Pour plus d’informations, consultez la section [Accéder à la configuration lors du démarrage](#access-configuration-during-startup).</span><span class="sxs-lookup"><span data-stu-id="6f99c-620">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="6f99c-621">Fournisseur de configuration de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="6f99c-621">Command-line Configuration Provider</span></span>

<span data-ttu-id="6f99c-622"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> charge la configuration à partir des paires clé-valeur de l’argument de ligne de commande lors de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="6f99c-622">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="6f99c-623">Pour activer la configuration en ligne de commande, la méthode d’extension <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> est appelée sur une instance de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="6f99c-623">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="6f99c-624">`AddCommandLine` est appelé automatiquement quand `CreateDefaultBuilder(string [])` est appelé.</span><span class="sxs-lookup"><span data-stu-id="6f99c-624">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="6f99c-625">Pour plus d’informations, consultez la section [Configuration par défaut](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="6f99c-625">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="6f99c-626">`CreateDefaultBuilder` charge également :</span><span class="sxs-lookup"><span data-stu-id="6f99c-626">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="6f99c-627">Configuration facultative à partir des fichiers *appsettings.json* et *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="6f99c-627">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="6f99c-628">[Secrets utilisateur (Secret Manager)](xref:security/app-secrets) dans l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="6f99c-628">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="6f99c-629">Variables d'environnement.</span><span class="sxs-lookup"><span data-stu-id="6f99c-629">Environment variables.</span></span>

<span data-ttu-id="6f99c-630">`CreateDefaultBuilder` ajoute en dernier le Fournisseur de configuration de ligne de commande.</span><span class="sxs-lookup"><span data-stu-id="6f99c-630">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="6f99c-631">Les arguments de ligne de commande passés lors de l’exécution remplacent la configuration définie par les autres fournisseurs.</span><span class="sxs-lookup"><span data-stu-id="6f99c-631">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="6f99c-632">`CreateDefaultBuilder` agit lors de la construction de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="6f99c-632">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="6f99c-633">Par conséquent, la configuration de ligne de commande activée par `CreateDefaultBuilder` peut affecter la façon dont l’hôte est configuré.</span><span class="sxs-lookup"><span data-stu-id="6f99c-633">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="6f99c-634">Pour les applications basées sur les modèles ASP.NET Core, `AddCommandLine` a déjà été appelé par `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="6f99c-634">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="6f99c-635">Pour ajouter des fournisseurs de configuration supplémentaires et conserver la capacité de remplacer leur configuration par des arguments de ligne de commande, appelez les fournisseurs supplémentaires de l’application dans `ConfigureAppConfiguration` et appelez `AddCommandLine` en dernier.</span><span class="sxs-lookup"><span data-stu-id="6f99c-635">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="6f99c-636">**Exemple**</span><span class="sxs-lookup"><span data-stu-id="6f99c-636">**Example**</span></span>

<span data-ttu-id="6f99c-637">L’exemple d’application tire parti de la méthode pratique statique `CreateDefaultBuilder` pour générer l’hôte, qui inclut un appel à <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="6f99c-637">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="6f99c-638">Ouvrez une invite de commandes dans le répertoire du projet.</span><span class="sxs-lookup"><span data-stu-id="6f99c-638">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="6f99c-639">Fournissez un argument de ligne de commande à la commande `dotnet run`, `dotnet run CommandLineKey=CommandLineValue`.</span><span class="sxs-lookup"><span data-stu-id="6f99c-639">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="6f99c-640">Une fois que l’application est en cours d’exécution, ouvrez un navigateur à l’application à l’adresse `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="6f99c-640">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="6f99c-641">Notez que la sortie contient la paire clé-valeur pour l’argument de ligne de commande de configuration fourni à `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="6f99c-641">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="6f99c-642">Arguments</span><span class="sxs-lookup"><span data-stu-id="6f99c-642">Arguments</span></span>

<span data-ttu-id="6f99c-643">La valeur doit suivre un signe égal (`=`) ou la clé doit avoir un préfixe (`--` ou `/`) lorsque la valeur suit un espace.</span><span class="sxs-lookup"><span data-stu-id="6f99c-643">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="6f99c-644">La valeur n’est pas requise si un signe égal est utilisé (par exemple, `CommandLineKey=`).</span><span class="sxs-lookup"><span data-stu-id="6f99c-644">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="6f99c-645">Préfixe de clé</span><span class="sxs-lookup"><span data-stu-id="6f99c-645">Key prefix</span></span>               | <span data-ttu-id="6f99c-646">Exemple</span><span class="sxs-lookup"><span data-stu-id="6f99c-646">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="6f99c-647">Aucun préfixe</span><span class="sxs-lookup"><span data-stu-id="6f99c-647">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="6f99c-648">Deux tirets (`--`)</span><span class="sxs-lookup"><span data-stu-id="6f99c-648">Two dashes (`--`)</span></span>        | <span data-ttu-id="6f99c-649">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="6f99c-649">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="6f99c-650">Barre oblique (`/`)</span><span class="sxs-lookup"><span data-stu-id="6f99c-650">Forward slash (`/`)</span></span>      | <span data-ttu-id="6f99c-651">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="6f99c-651">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="6f99c-652">Dans la même commande, ne mélangez pas des paires clé-valeur de l’argument de ligne de commande qui utilisent un signe égal avec des paires clé-valeur qui utilisent un espace.</span><span class="sxs-lookup"><span data-stu-id="6f99c-652">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="6f99c-653">Exemples de commandes :</span><span class="sxs-lookup"><span data-stu-id="6f99c-653">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="6f99c-654">Correspondances de commutateur</span><span class="sxs-lookup"><span data-stu-id="6f99c-654">Switch mappings</span></span>

<span data-ttu-id="6f99c-655">Les correspondances de commutateur permettent une logique de remplacement des noms de clés.</span><span class="sxs-lookup"><span data-stu-id="6f99c-655">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="6f99c-656">Lors de la génération manuelle d’une configuration avec un <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> , fournissez un dictionnaire de remplacements de commutateur à la <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> méthode.</span><span class="sxs-lookup"><span data-stu-id="6f99c-656">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="6f99c-657">Quand le dictionnaire de correspondances de commutateur est utilisé, il est vérifié afin de déterminer s’il contient une clé correspondant à celle fournie par un argument de ligne de commande.</span><span class="sxs-lookup"><span data-stu-id="6f99c-657">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="6f99c-658">Si la clé de ligne de commande est trouvée dans le dictionnaire, la valeur du dictionnaire (le remplacement de la clé) est repassée pour définir la paire clé-valeur dans la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="6f99c-658">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="6f99c-659">Une correspondance de commutateur est nécessaire pour chaque clé de ligne de commande préfixée avec un tiret unique (`-`).</span><span class="sxs-lookup"><span data-stu-id="6f99c-659">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="6f99c-660">Règles des clés du dictionnaire de correspondances de commutateur :</span><span class="sxs-lookup"><span data-stu-id="6f99c-660">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="6f99c-661">Les commutateurs doivent commencer par un tiret (`-`) ou un double tiret (`--`).</span><span class="sxs-lookup"><span data-stu-id="6f99c-661">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="6f99c-662">Le dictionnaire de correspondances de commutateur ne doit pas contenir de clés en double.</span><span class="sxs-lookup"><span data-stu-id="6f99c-662">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="6f99c-663">Créez un dictionnaire des mappages de commutateurs.</span><span class="sxs-lookup"><span data-stu-id="6f99c-663">Create a switch mappings dictionary.</span></span> <span data-ttu-id="6f99c-664">Dans l’exemple suivant, deux mappages de commutateurs sont créés :</span><span class="sxs-lookup"><span data-stu-id="6f99c-664">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="6f99c-665">Lorsque l’hôte est généré, appelez `AddCommandLine` avec le dictionnaire de mappages de commutateurs :</span><span class="sxs-lookup"><span data-stu-id="6f99c-665">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="6f99c-666">Pour les applications qui utilisent des mappages de commutateurs, l’appel à `CreateDefaultBuilder` ne doit pas passer d’arguments.</span><span class="sxs-lookup"><span data-stu-id="6f99c-666">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="6f99c-667">L’appel `AddCommandLine` de la méthode `CreateDefaultBuilder` n’inclut pas de commutateurs mappés et il n’existe aucun moyen de transmettre le dictionnaire de correspondance de commutateur à `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="6f99c-667">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="6f99c-668">La solution ne consiste pas à transmettre les arguments à `CreateDefaultBuilder`, mais plutôt à permettre à la méthode `AddCommandLine` de la méthode `ConfigurationBuilder` de traiter les arguments et le dictionnaire de mappage de commutateur.</span><span class="sxs-lookup"><span data-stu-id="6f99c-668">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="6f99c-669">Une fois le dictionnaire de correspondances de commutateur créé, il contient les données affichées dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="6f99c-669">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="6f99c-670">Clé</span><span class="sxs-lookup"><span data-stu-id="6f99c-670">Key</span></span>       | <span data-ttu-id="6f99c-671">Valeur</span><span class="sxs-lookup"><span data-stu-id="6f99c-671">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="6f99c-672">Si les clés mappées au commutateur sont utilisées lors du démarrage de l’application, la configuration reçoit la valeur de configuration sur la clé fournie par le dictionnaire :</span><span class="sxs-lookup"><span data-stu-id="6f99c-672">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="6f99c-673">Après avoir exécuté la commande précédente, la configuration contient les valeurs indiquées dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="6f99c-673">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="6f99c-674">Clé</span><span class="sxs-lookup"><span data-stu-id="6f99c-674">Key</span></span>               | <span data-ttu-id="6f99c-675">Valeur</span><span class="sxs-lookup"><span data-stu-id="6f99c-675">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="6f99c-676">Fournisseur de configuration de variables d’environnement</span><span class="sxs-lookup"><span data-stu-id="6f99c-676">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="6f99c-677"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> charge la configuration à partir des paires clé-valeur de la variable d’environnement lors de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="6f99c-677">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="6f99c-678">Pour activer la configuration des variables d’environnement, appelez la méthode d’extension <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> sur une instance de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="6f99c-678">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="6f99c-679">[Azure App service](https://azure.microsoft.com/services/app-service/) permet de définir des variables d’environnement dans le portail Azure qui peuvent remplacer la configuration de l’application à l’aide du fournisseur de configuration des variables d’environnement.</span><span class="sxs-lookup"><span data-stu-id="6f99c-679">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="6f99c-680">Pour plus d’informations, consultez [Azure Apps : remplacer la configuration de l’application à l’aide du portail Azure](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="6f99c-680">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="6f99c-681">`AddEnvironmentVariables` sert à charger les variables d’environnement préfixées avec `ASPNETCORE_` pour la [configuration hôte](#host-versus-app-configuration) lorsqu’un nouveau générateur d’hôte est initialisé avec l’[hôte web](xref:fundamentals/host/web-host) et que `CreateDefaultBuilder` est appelé.</span><span class="sxs-lookup"><span data-stu-id="6f99c-681">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="6f99c-682">Pour plus d’informations, consultez la section [Configuration par défaut](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="6f99c-682">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="6f99c-683">`CreateDefaultBuilder` charge également :</span><span class="sxs-lookup"><span data-stu-id="6f99c-683">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="6f99c-684">Configuration de l’application à partir de variables d’environnement sans préfixe en appelant `AddEnvironmentVariables` sans préfixe.</span><span class="sxs-lookup"><span data-stu-id="6f99c-684">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="6f99c-685">Configuration facultative à partir des fichiers *appsettings.json* et *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="6f99c-685">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="6f99c-686">[Secrets utilisateur (Secret Manager)](xref:security/app-secrets) dans l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="6f99c-686">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="6f99c-687">Arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="6f99c-687">Command-line arguments.</span></span>

<span data-ttu-id="6f99c-688">Le fournisseur de configuration de variables d’environnement est appelé une fois que la configuration est établie à partir des secrets utilisateur et des fichiers *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="6f99c-688">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="6f99c-689">Le fait d’appeler le fournisseur ainsi permet de lire les variables d’environnement pendant l’exécution pour substituer la configuration définie par les secrets utilisateur et les fichiers *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="6f99c-689">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="6f99c-690">Pour fournir la configuration d’application à partir de variables d’environnement supplémentaires, appelez les fournisseurs supplémentaires de l’application dans `ConfigureAppConfiguration` et appelez `AddEnvironmentVariables` avec le préfixe :</span><span class="sxs-lookup"><span data-stu-id="6f99c-690">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="6f99c-691">Appelez `AddEnvironmentVariables` Last pour autoriser les variables d’environnement avec le préfixe spécifié à substituer des valeurs d’autres fournisseurs.</span><span class="sxs-lookup"><span data-stu-id="6f99c-691">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="6f99c-692">**Exemple**</span><span class="sxs-lookup"><span data-stu-id="6f99c-692">**Example**</span></span>

<span data-ttu-id="6f99c-693">L’exemple d’application tire parti de la méthode pratique statique `CreateDefaultBuilder` pour générer l’hôte, qui inclut un appel à `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="6f99c-693">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="6f99c-694">Exécutez l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="6f99c-694">Run the sample app.</span></span> <span data-ttu-id="6f99c-695">Ouvrez un navigateur vers l’application avec l’adresse `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="6f99c-695">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="6f99c-696">Notez que la sortie contient la paire clé-valeur pour la variable d’environnement `ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="6f99c-696">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="6f99c-697">La valeur reflète l’environnement dans lequel l’application est en cours d’exécution, en général `Development` lors de l’exécution locale.</span><span class="sxs-lookup"><span data-stu-id="6f99c-697">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="6f99c-698">Pour que la liste des variables d’environnement restituée par l’application soit courte, l’application filtre les variables d’environnement.</span><span class="sxs-lookup"><span data-stu-id="6f99c-698">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="6f99c-699">Consultez le fichier *Pages/Index.cshtml.cs* de l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="6f99c-699">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="6f99c-700">Pour exposer toutes les variables d’environnement disponibles pour l’application, remplacez `FilteredConfiguration` dans *pages/index. cshtml. cs* par ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="6f99c-700">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="6f99c-701">Préfixes</span><span class="sxs-lookup"><span data-stu-id="6f99c-701">Prefixes</span></span>

<span data-ttu-id="6f99c-702">Les variables d’environnement chargées dans la configuration de l’application sont filtrées lors de la spécification d’un préfixe à la `AddEnvironmentVariables` méthode.</span><span class="sxs-lookup"><span data-stu-id="6f99c-702">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="6f99c-703">Par exemple, pour filtrer les variables d’environnement sur le préfixe `CUSTOM_`, fournissez le préfixe au fournisseur de configuration :</span><span class="sxs-lookup"><span data-stu-id="6f99c-703">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="6f99c-704">Le préfixe est supprimé lorsque les paires clé-valeur de la configuration sont créées.</span><span class="sxs-lookup"><span data-stu-id="6f99c-704">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="6f99c-705">Lorsque le générateur d’hôte est créé, la configuration de l’hôte est fournie par des variables d’environnement.</span><span class="sxs-lookup"><span data-stu-id="6f99c-705">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="6f99c-706">Pour plus d’informations sur le préfixe utilisé pour ces variables d’environnement, consultez la section [Configuration par défaut](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="6f99c-706">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="6f99c-707">**Préfixes des chaînes de connexion**</span><span class="sxs-lookup"><span data-stu-id="6f99c-707">**Connection string prefixes**</span></span>

<span data-ttu-id="6f99c-708">L’API Configuration possède des règles de traitement spéciales pour quatre variables d’environnement de chaîne de connexion impliquées dans la configuration des chaînes de connexion Azure pour l’environnement de l’application.</span><span class="sxs-lookup"><span data-stu-id="6f99c-708">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="6f99c-709">Les variables d’environnement avec les préfixes indiqués dans le tableau sont chargées dans l’application si aucun préfixe n’est fourni à `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="6f99c-709">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="6f99c-710">Préfixe de la chaîne de connexion</span><span class="sxs-lookup"><span data-stu-id="6f99c-710">Connection string prefix</span></span> | <span data-ttu-id="6f99c-711">Fournisseur</span><span class="sxs-lookup"><span data-stu-id="6f99c-711">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="6f99c-712">Fournisseur personnalisé</span><span class="sxs-lookup"><span data-stu-id="6f99c-712">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="6f99c-713">MySQL</span><span class="sxs-lookup"><span data-stu-id="6f99c-713">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="6f99c-714">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="6f99c-714">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="6f99c-715">SQL Server</span><span class="sxs-lookup"><span data-stu-id="6f99c-715">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="6f99c-716">Quand une variable d’environnement est découverte et chargée dans la configuration avec l’un des quatre préfixes indiqués dans le tableau :</span><span class="sxs-lookup"><span data-stu-id="6f99c-716">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="6f99c-717">La clé de configuration est créée en supprimant le préfixe de la variable d’environnement et en ajoutant une section de clé de configuration (`ConnectionStrings`).</span><span class="sxs-lookup"><span data-stu-id="6f99c-717">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="6f99c-718">Une nouvelle paire clé-valeur de configuration est créée qui représente le fournisseur de connexion de base de données (à l’exception de `CUSTOMCONNSTR_`, qui ne possède aucun fournisseur indiqué).</span><span class="sxs-lookup"><span data-stu-id="6f99c-718">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="6f99c-719">Clé de variable d’environnement</span><span class="sxs-lookup"><span data-stu-id="6f99c-719">Environment variable key</span></span> | <span data-ttu-id="6f99c-720">Clé de configuration convertie</span><span class="sxs-lookup"><span data-stu-id="6f99c-720">Converted configuration key</span></span> | <span data-ttu-id="6f99c-721">Entrée de configuration de fournisseur</span><span class="sxs-lookup"><span data-stu-id="6f99c-721">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="6f99c-722">Entrée de configuration non créée.</span><span class="sxs-lookup"><span data-stu-id="6f99c-722">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="6f99c-723">Clé : `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="6f99c-723">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="6f99c-724">Valeur: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="6f99c-724">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="6f99c-725">Clé : `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="6f99c-725">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="6f99c-726">Valeur: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="6f99c-726">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="6f99c-727">Clé : `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="6f99c-727">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="6f99c-728">Valeur: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="6f99c-728">Value: `System.Data.SqlClient`</span></span>  |

<span data-ttu-id="6f99c-729">**Exemple**</span><span class="sxs-lookup"><span data-stu-id="6f99c-729">**Example**</span></span>

<span data-ttu-id="6f99c-730">Une variable d’environnement de chaîne de connexion personnalisée est créée sur le serveur :</span><span class="sxs-lookup"><span data-stu-id="6f99c-730">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="6f99c-731">Nom : `CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="6f99c-731">Name: `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="6f99c-732">Valeur: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="6f99c-732">Value: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="6f99c-733">Si `IConfiguration` est injecté et affecté à un champ nommé `_config` , lisez la valeur :</span><span class="sxs-lookup"><span data-stu-id="6f99c-733">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="6f99c-734">Fournisseur de configuration de fichier</span><span class="sxs-lookup"><span data-stu-id="6f99c-734">File Configuration Provider</span></span>

<span data-ttu-id="6f99c-735"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> est la classe de base pour charger la configuration à partir du système de fichiers.</span><span class="sxs-lookup"><span data-stu-id="6f99c-735"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="6f99c-736">Les fournisseurs de configuration suivants sont dédiés à des types de fichiers spécifiques :</span><span class="sxs-lookup"><span data-stu-id="6f99c-736">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="6f99c-737">Fournisseur de configuration INI</span><span class="sxs-lookup"><span data-stu-id="6f99c-737">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="6f99c-738">Fournisseur de configuration JSON</span><span class="sxs-lookup"><span data-stu-id="6f99c-738">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="6f99c-739">Fournisseur de configuration XML</span><span class="sxs-lookup"><span data-stu-id="6f99c-739">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="6f99c-740">Fournisseur de configuration INI</span><span class="sxs-lookup"><span data-stu-id="6f99c-740">INI Configuration Provider</span></span>

<span data-ttu-id="6f99c-741"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> charge la configuration à partir des paires clé-valeur du fichier INI lors de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="6f99c-741">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="6f99c-742">Pour activer la configuration du fichier INI, appelez la méthode d’extension <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> sur une instance de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="6f99c-742">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="6f99c-743">Le signe deux-points peut servir de délimiteur de section dans la configuration d’un fichier INI.</span><span class="sxs-lookup"><span data-stu-id="6f99c-743">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="6f99c-744">Les surcharges permettent de spécifier :</span><span class="sxs-lookup"><span data-stu-id="6f99c-744">Overloads permit specifying:</span></span>

* <span data-ttu-id="6f99c-745">Si le fichier est facultatif.</span><span class="sxs-lookup"><span data-stu-id="6f99c-745">Whether the file is optional.</span></span>
* <span data-ttu-id="6f99c-746">Si la configuration est rechargée quand le fichier est modifié.</span><span class="sxs-lookup"><span data-stu-id="6f99c-746">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="6f99c-747">Le <xref:Microsoft.Extensions.FileProviders.IFileProvider> utilisé pour accéder au fichier.</span><span class="sxs-lookup"><span data-stu-id="6f99c-747">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="6f99c-748">Appelez `ConfigureAppConfiguration` lors de la création de l’hôte pour spécifier la configuration de l’application :</span><span class="sxs-lookup"><span data-stu-id="6f99c-748">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="6f99c-749">Exemple générique d’un fichier de configuration INI :</span><span class="sxs-lookup"><span data-stu-id="6f99c-749">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="6f99c-750">Le fichier de configuration précédent charge les clés suivantes avec `value` :</span><span class="sxs-lookup"><span data-stu-id="6f99c-750">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="6f99c-751">section0:key0</span><span class="sxs-lookup"><span data-stu-id="6f99c-751">section0:key0</span></span>
* <span data-ttu-id="6f99c-752">section0:key1</span><span class="sxs-lookup"><span data-stu-id="6f99c-752">section0:key1</span></span>
* <span data-ttu-id="6f99c-753">section1:subsection:key</span><span class="sxs-lookup"><span data-stu-id="6f99c-753">section1:subsection:key</span></span>
* <span data-ttu-id="6f99c-754">section2:subsection0:key</span><span class="sxs-lookup"><span data-stu-id="6f99c-754">section2:subsection0:key</span></span>
* <span data-ttu-id="6f99c-755">section2:subsection1:key</span><span class="sxs-lookup"><span data-stu-id="6f99c-755">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="6f99c-756">Fournisseur de configuration JSON</span><span class="sxs-lookup"><span data-stu-id="6f99c-756">JSON Configuration Provider</span></span>

<span data-ttu-id="6f99c-757"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> charge la configuration à partir des paires clé-valeur du fichier JSON lors de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="6f99c-757">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="6f99c-758">Pour activer la configuration du fichier JSON, appelez la méthode d’extension <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> sur une instance de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="6f99c-758">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="6f99c-759">Les surcharges permettent de spécifier :</span><span class="sxs-lookup"><span data-stu-id="6f99c-759">Overloads permit specifying:</span></span>

* <span data-ttu-id="6f99c-760">Si le fichier est facultatif.</span><span class="sxs-lookup"><span data-stu-id="6f99c-760">Whether the file is optional.</span></span>
* <span data-ttu-id="6f99c-761">Si la configuration est rechargée quand le fichier est modifié.</span><span class="sxs-lookup"><span data-stu-id="6f99c-761">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="6f99c-762">Le <xref:Microsoft.Extensions.FileProviders.IFileProvider> utilisé pour accéder au fichier.</span><span class="sxs-lookup"><span data-stu-id="6f99c-762">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="6f99c-763">`AddJsonFile` est appelé automatiquement deux fois lors de l’initialisation d’un nouveau générateur d’hôte `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="6f99c-763">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="6f99c-764">La méthode est appelée pour charger la configuration à partir de :</span><span class="sxs-lookup"><span data-stu-id="6f99c-764">The method is called to load configuration from:</span></span>

* <span data-ttu-id="6f99c-765">*appsettings.jsle*: ce fichier est lu en premier.</span><span class="sxs-lookup"><span data-stu-id="6f99c-765">*appsettings.json*: This file is read first.</span></span> <span data-ttu-id="6f99c-766">La version de l’environnement du fichier peut remplacer les valeurs fournies par le fichier *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="6f99c-766">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="6f99c-767">*appSettings. {Environment}. JSON*: la version de l’environnement du fichier est chargée à partir de [IHostingEnvironment. EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="6f99c-767">*appsettings.{Environment}.json*: The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="6f99c-768">Pour plus d’informations, consultez la section [Configuration par défaut](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="6f99c-768">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="6f99c-769">`CreateDefaultBuilder` charge également :</span><span class="sxs-lookup"><span data-stu-id="6f99c-769">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="6f99c-770">Variables d'environnement.</span><span class="sxs-lookup"><span data-stu-id="6f99c-770">Environment variables.</span></span>
* <span data-ttu-id="6f99c-771">[Secrets utilisateur (Secret Manager)](xref:security/app-secrets) dans l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="6f99c-771">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="6f99c-772">Arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="6f99c-772">Command-line arguments.</span></span>

<span data-ttu-id="6f99c-773">Le Fournisseur de configuration JSON est établi en premier.</span><span class="sxs-lookup"><span data-stu-id="6f99c-773">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="6f99c-774">Par conséquent, les secrets utilisateur, les variables d’environnement et les arguments de ligne de commande remplacent la configuration définie par les fichiers *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="6f99c-774">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="6f99c-775">Appelez `ConfigureAppConfiguration` lors de la création de l’hôte pour spécifier la configuration de l’application pour les fichiers autres qu’*appsettings.json* et *appsettings. {Environment} .json* :</span><span class="sxs-lookup"><span data-stu-id="6f99c-775">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="6f99c-776">**Exemple**</span><span class="sxs-lookup"><span data-stu-id="6f99c-776">**Example**</span></span>

<span data-ttu-id="6f99c-777">L’exemple d’application tire parti de la méthode de commodité statique `CreateDefaultBuilder` pour créer l’hôte, ce qui comprend deux appels à `AddJsonFile` :</span><span class="sxs-lookup"><span data-stu-id="6f99c-777">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="6f99c-778">Le premier appel à `AddJsonFile` charge la configuration à partir de *appsettings.jssur*:</span><span class="sxs-lookup"><span data-stu-id="6f99c-778">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="6f99c-779">Le deuxième appel à `AddJsonFile` charge la configuration à partir de *appSettings. { Environnement}. JSON*.</span><span class="sxs-lookup"><span data-stu-id="6f99c-779">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="6f99c-780">Pour *appsettings.Development.js* dans l’exemple d’application, le fichier suivant est chargé :</span><span class="sxs-lookup"><span data-stu-id="6f99c-780">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="6f99c-781">Exécutez l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="6f99c-781">Run the sample app.</span></span> <span data-ttu-id="6f99c-782">Ouvrez un navigateur vers l’application avec l’adresse `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="6f99c-782">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="6f99c-783">La sortie contient des paires clé-valeur pour la configuration en fonction de l’environnement de l’application.</span><span class="sxs-lookup"><span data-stu-id="6f99c-783">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="6f99c-784">Le niveau de journalisation de la clé `Logging:LogLevel:Default` est `Debug` lors de l’exécution de l’application dans l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="6f99c-784">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="6f99c-785">Exécutez à nouveau l’exemple d’application dans l’environnement de production :</span><span class="sxs-lookup"><span data-stu-id="6f99c-785">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="6f99c-786">Ouvrez le fichier *Properties/launchSettings.js* .</span><span class="sxs-lookup"><span data-stu-id="6f99c-786">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="6f99c-787">Dans le `ConfigurationSample` profil, remplacez la valeur de la `ASPNETCORE_ENVIRONMENT` variable d’environnement par `Production` .</span><span class="sxs-lookup"><span data-stu-id="6f99c-787">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="6f99c-788">Enregistrez le fichier et exécutez l’application avec `dotnet run` dans un interpréteur de commandes.</span><span class="sxs-lookup"><span data-stu-id="6f99c-788">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="6f99c-789">Les paramètres de la *appsettings.Development.js* qui ne se substituent plus aux paramètres de *appsettings.js*.</span><span class="sxs-lookup"><span data-stu-id="6f99c-789">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="6f99c-790">Le niveau de journalisation de la clé `Logging:LogLevel:Default` est `Warning` .</span><span class="sxs-lookup"><span data-stu-id="6f99c-790">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="6f99c-791">Fournisseur de configuration XML</span><span class="sxs-lookup"><span data-stu-id="6f99c-791">XML Configuration Provider</span></span>

<span data-ttu-id="6f99c-792"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> charge la configuration à partir des paires clé-valeur du fichier XML lors de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="6f99c-792">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="6f99c-793">Pour activer la configuration du fichier XML, appelez la méthode d’extension <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> sur une instance de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="6f99c-793">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="6f99c-794">Les surcharges permettent de spécifier :</span><span class="sxs-lookup"><span data-stu-id="6f99c-794">Overloads permit specifying:</span></span>

* <span data-ttu-id="6f99c-795">Si le fichier est facultatif.</span><span class="sxs-lookup"><span data-stu-id="6f99c-795">Whether the file is optional.</span></span>
* <span data-ttu-id="6f99c-796">Si la configuration est rechargée quand le fichier est modifié.</span><span class="sxs-lookup"><span data-stu-id="6f99c-796">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="6f99c-797">Le <xref:Microsoft.Extensions.FileProviders.IFileProvider> utilisé pour accéder au fichier.</span><span class="sxs-lookup"><span data-stu-id="6f99c-797">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="6f99c-798">Le nœud racine du fichier de configuration est ignoré lorsque les paires clé-valeur de la configuration sont créées.</span><span class="sxs-lookup"><span data-stu-id="6f99c-798">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="6f99c-799">Ne spécifiez pas une définition de type de document (DTD) ou un espace de noms dans le fichier.</span><span class="sxs-lookup"><span data-stu-id="6f99c-799">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="6f99c-800">Appelez `ConfigureAppConfiguration` lors de la création de l’hôte pour spécifier la configuration de l’application :</span><span class="sxs-lookup"><span data-stu-id="6f99c-800">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="6f99c-801">Les fichiers de configuration XML peuvent utiliser des noms d’éléments distincts pour les sections répétitives :</span><span class="sxs-lookup"><span data-stu-id="6f99c-801">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="6f99c-802">Le fichier de configuration précédent charge les clés suivantes avec `value` :</span><span class="sxs-lookup"><span data-stu-id="6f99c-802">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="6f99c-803">section0:key0</span><span class="sxs-lookup"><span data-stu-id="6f99c-803">section0:key0</span></span>
* <span data-ttu-id="6f99c-804">section0:key1</span><span class="sxs-lookup"><span data-stu-id="6f99c-804">section0:key1</span></span>
* <span data-ttu-id="6f99c-805">section1:key0</span><span class="sxs-lookup"><span data-stu-id="6f99c-805">section1:key0</span></span>
* <span data-ttu-id="6f99c-806">section1:key1</span><span class="sxs-lookup"><span data-stu-id="6f99c-806">section1:key1</span></span>

<span data-ttu-id="6f99c-807">Les éléments répétitifs qui utilisent le même nom d’élément fonctionnent si l’attribut `name` est utilisé pour distinguer les éléments :</span><span class="sxs-lookup"><span data-stu-id="6f99c-807">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="6f99c-808">Le fichier de configuration précédent charge les clés suivantes avec `value` :</span><span class="sxs-lookup"><span data-stu-id="6f99c-808">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="6f99c-809">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="6f99c-809">section:section0:key:key0</span></span>
* <span data-ttu-id="6f99c-810">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="6f99c-810">section:section0:key:key1</span></span>
* <span data-ttu-id="6f99c-811">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="6f99c-811">section:section1:key:key0</span></span>
* <span data-ttu-id="6f99c-812">section:section1:key:key1</span><span class="sxs-lookup"><span data-stu-id="6f99c-812">section:section1:key:key1</span></span>

<span data-ttu-id="6f99c-813">Les attributs peuvent être utilisés pour fournir des valeurs :</span><span class="sxs-lookup"><span data-stu-id="6f99c-813">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="6f99c-814">Le fichier de configuration précédent charge les clés suivantes avec `value` :</span><span class="sxs-lookup"><span data-stu-id="6f99c-814">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="6f99c-815">key:attribute</span><span class="sxs-lookup"><span data-stu-id="6f99c-815">key:attribute</span></span>
* <span data-ttu-id="6f99c-816">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="6f99c-816">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="6f99c-817">Fournisseur de configuration clé par fichier</span><span class="sxs-lookup"><span data-stu-id="6f99c-817">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="6f99c-818">Le <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> utilise les fichiers d’un répertoire en tant que paires clé-valeur de configuration.</span><span class="sxs-lookup"><span data-stu-id="6f99c-818">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="6f99c-819">La clé est le nom de fichier.</span><span class="sxs-lookup"><span data-stu-id="6f99c-819">The key is the file name.</span></span> <span data-ttu-id="6f99c-820">La valeur contient le contenu du fichier.</span><span class="sxs-lookup"><span data-stu-id="6f99c-820">The value contains the file's contents.</span></span> <span data-ttu-id="6f99c-821">Le Fournisseur de configuration Clé par fichier est utilisé dans les scénarios d’hébergement de Docker.</span><span class="sxs-lookup"><span data-stu-id="6f99c-821">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="6f99c-822">Pour activer la configuration clé par fichier, appelez la méthode d’extension <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> sur une instance de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="6f99c-822">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="6f99c-823">Le `directoryPath` aux fichiers doit être un chemin d’accès absolu.</span><span class="sxs-lookup"><span data-stu-id="6f99c-823">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="6f99c-824">Les surcharges permettent de spécifier :</span><span class="sxs-lookup"><span data-stu-id="6f99c-824">Overloads permit specifying:</span></span>

* <span data-ttu-id="6f99c-825">Un délégué `Action<KeyPerFileConfigurationSource>` qui configure la source.</span><span class="sxs-lookup"><span data-stu-id="6f99c-825">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="6f99c-826">Si le répertoire est facultatif et le chemin d’accès au répertoire.</span><span class="sxs-lookup"><span data-stu-id="6f99c-826">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="6f99c-827">Le double trait de soulignement (`__`) est utilisé comme un délimiteur de clé de configuration dans les noms de fichiers.</span><span class="sxs-lookup"><span data-stu-id="6f99c-827">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="6f99c-828">Par exemple, le nom de fichier `Logging__LogLevel__System` génère la clé de configuration `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="6f99c-828">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="6f99c-829">Appelez `ConfigureAppConfiguration` lors de la création de l’hôte pour spécifier la configuration de l’application :</span><span class="sxs-lookup"><span data-stu-id="6f99c-829">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="6f99c-830">Fournisseur de configuration de mémoire</span><span class="sxs-lookup"><span data-stu-id="6f99c-830">Memory Configuration Provider</span></span>

<span data-ttu-id="6f99c-831">Le <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> utilise une collection en mémoire en tant que paires clé-valeur de configuration.</span><span class="sxs-lookup"><span data-stu-id="6f99c-831">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="6f99c-832">Pour activer la configuration de la collection en mémoire, appelez la méthode d’extension <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> sur une instance de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="6f99c-832">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="6f99c-833">Le fournisseur de configuration peut être initialisé avec un `IEnumerable<KeyValuePair<String,String>>`.</span><span class="sxs-lookup"><span data-stu-id="6f99c-833">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="6f99c-834">Appelez `ConfigureAppConfiguration` lors de la création de l’hôte pour spécifier la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="6f99c-834">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="6f99c-835">Dans l’exemple suivant, un dictionnaire de configuration est créé :</span><span class="sxs-lookup"><span data-stu-id="6f99c-835">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="6f99c-836">Le dictionnaire est utilisé avec un appel à `AddInMemoryCollection` pour fournir la configuration :</span><span class="sxs-lookup"><span data-stu-id="6f99c-836">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="6f99c-837">GetValue</span><span class="sxs-lookup"><span data-stu-id="6f99c-837">GetValue</span></span>

<span data-ttu-id="6f99c-838">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extrait une valeur unique de la configuration avec une clé spécifiée et la convertit en type de non-collection spécifié.</span><span class="sxs-lookup"><span data-stu-id="6f99c-838">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="6f99c-839">Une surcharge accepte une valeur par défaut.</span><span class="sxs-lookup"><span data-stu-id="6f99c-839">An overload accepts a default value.</span></span>

<span data-ttu-id="6f99c-840">L’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="6f99c-840">The following example:</span></span>

* <span data-ttu-id="6f99c-841">Extrait la valeur de chaîne de la configuration avec la clé `NumberKey`.</span><span class="sxs-lookup"><span data-stu-id="6f99c-841">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="6f99c-842">Si `NumberKey` est introuvable dans les clés de configuration, la valeur par défaut de `99` est utilisée.</span><span class="sxs-lookup"><span data-stu-id="6f99c-842">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="6f99c-843">Tape la valeur comme `int`.</span><span class="sxs-lookup"><span data-stu-id="6f99c-843">Types the value as an `int`.</span></span>
* <span data-ttu-id="6f99c-844">Stocke la valeur dans la propriété `NumberConfig` pour une utilisation par la page.</span><span class="sxs-lookup"><span data-stu-id="6f99c-844">Stores the value in the `NumberConfig` property for use by the page.</span></span>

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

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="6f99c-845">GetSection, GetChildren et Exists</span><span class="sxs-lookup"><span data-stu-id="6f99c-845">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="6f99c-846">Pour les exemples qui suivent, utilisez le fichier JSON suivant.</span><span class="sxs-lookup"><span data-stu-id="6f99c-846">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="6f99c-847">Quatre clés se trouvent dans deux sections, dont l’une inclut deux sous-sections :</span><span class="sxs-lookup"><span data-stu-id="6f99c-847">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="6f99c-848">Lorsque le fichier est lu dans la configuration, les clés hiérarchiques uniques suivantes sont créées pour stocker les valeurs de la configuration :</span><span class="sxs-lookup"><span data-stu-id="6f99c-848">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="6f99c-849">section0:key0</span><span class="sxs-lookup"><span data-stu-id="6f99c-849">section0:key0</span></span>
* <span data-ttu-id="6f99c-850">section0:key1</span><span class="sxs-lookup"><span data-stu-id="6f99c-850">section0:key1</span></span>
* <span data-ttu-id="6f99c-851">section1:key0</span><span class="sxs-lookup"><span data-stu-id="6f99c-851">section1:key0</span></span>
* <span data-ttu-id="6f99c-852">section1:key1</span><span class="sxs-lookup"><span data-stu-id="6f99c-852">section1:key1</span></span>
* <span data-ttu-id="6f99c-853">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="6f99c-853">section2:subsection0:key0</span></span>
* <span data-ttu-id="6f99c-854">section2:subsection0:key1</span><span class="sxs-lookup"><span data-stu-id="6f99c-854">section2:subsection0:key1</span></span>
* <span data-ttu-id="6f99c-855">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="6f99c-855">section2:subsection1:key0</span></span>
* <span data-ttu-id="6f99c-856">section2:subsection1:key1</span><span class="sxs-lookup"><span data-stu-id="6f99c-856">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="6f99c-857">GetSection</span><span class="sxs-lookup"><span data-stu-id="6f99c-857">GetSection</span></span>

<span data-ttu-id="6f99c-858">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extrait une sous-section de la configuration avec la clé de sous-section spécifiée.</span><span class="sxs-lookup"><span data-stu-id="6f99c-858">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="6f99c-859">Pour retourner un <xref:Microsoft.Extensions.Configuration.IConfigurationSection> contenant uniquement les paires clé-valeur dans `section1`, appelez `GetSection` et fournissez le nom de section :</span><span class="sxs-lookup"><span data-stu-id="6f99c-859">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="6f99c-860">`configSection` n’a pas de valeur, seulement une clé et un chemin.</span><span class="sxs-lookup"><span data-stu-id="6f99c-860">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="6f99c-861">De même, pour obtenir les valeurs des clés dans `section2:subsection0`, appelez `GetSection` et fournissez le chemin d’accès de la section :</span><span class="sxs-lookup"><span data-stu-id="6f99c-861">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="6f99c-862">`GetSection` ne retourne jamais `null`.</span><span class="sxs-lookup"><span data-stu-id="6f99c-862">`GetSection` never returns `null`.</span></span> <span data-ttu-id="6f99c-863">Si aucune section correspondante n’est trouvée, une valeur `IConfigurationSection` vide est retournée.</span><span class="sxs-lookup"><span data-stu-id="6f99c-863">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="6f99c-864">Quand `GetSection` retourne une section correspondante, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> n’est pas rempli.</span><span class="sxs-lookup"><span data-stu-id="6f99c-864">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="6f99c-865"><xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> et <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> sont retournés quand la section existe.</span><span class="sxs-lookup"><span data-stu-id="6f99c-865">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="6f99c-866">GetChildren</span><span class="sxs-lookup"><span data-stu-id="6f99c-866">GetChildren</span></span>

<span data-ttu-id="6f99c-867">Un appel à [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) sur `section2` obtient un `IEnumerable<IConfigurationSection>` qui inclut :</span><span class="sxs-lookup"><span data-stu-id="6f99c-867">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="6f99c-868">Exists</span><span class="sxs-lookup"><span data-stu-id="6f99c-868">Exists</span></span>

<span data-ttu-id="6f99c-869">Utilisez [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) pour déterminer si une section de configuration existe :</span><span class="sxs-lookup"><span data-stu-id="6f99c-869">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="6f99c-870">Compte tenu des données d’exemple, `sectionExists` est `false`, car il n’y a pas de section `section2:subsection2` dans les données de configuration.</span><span class="sxs-lookup"><span data-stu-id="6f99c-870">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="6f99c-871">Établir une liaison à un graphe d’objets</span><span class="sxs-lookup"><span data-stu-id="6f99c-871">Bind to an object graph</span></span>

<span data-ttu-id="6f99c-872"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> est capable de lier l’intégralité d’un graphe d’objets POCO.</span><span class="sxs-lookup"><span data-stu-id="6f99c-872"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="6f99c-873">Comme pour la liaison d’un objet simple, seules les propriétés accessibles en lecture/écriture publiques sont liées.</span><span class="sxs-lookup"><span data-stu-id="6f99c-873">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="6f99c-874">L’exemple contient un modèle `TvShow` dont le graphe d’objets inclut les classes `Metadata` et `Actors` (*Models/TvShow.cs*) :</span><span class="sxs-lookup"><span data-stu-id="6f99c-874">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="6f99c-875">L’exemple d’application a un fichier *tvshow.xml* contenant les données de configuration :</span><span class="sxs-lookup"><span data-stu-id="6f99c-875">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="6f99c-876">La configuration est liée à l’intégralité du graphe d’objets `TvShow` avec la méthode `Bind`.</span><span class="sxs-lookup"><span data-stu-id="6f99c-876">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="6f99c-877">L’instance liée est affectée à une propriété pour le rendu :</span><span class="sxs-lookup"><span data-stu-id="6f99c-877">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="6f99c-878">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) lie et retourne le type spécifié.</span><span class="sxs-lookup"><span data-stu-id="6f99c-878">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="6f99c-879">Il est plus pratique d’utiliser `Get<T>` que `Bind`.</span><span class="sxs-lookup"><span data-stu-id="6f99c-879">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="6f99c-880">Le code suivant montre comment utiliser `Get<T>` avec l’exemple précédent :</span><span class="sxs-lookup"><span data-stu-id="6f99c-880">The following code shows how to use `Get<T>` with the preceding example:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="6f99c-881">Lier un tableau à une classe</span><span class="sxs-lookup"><span data-stu-id="6f99c-881">Bind an array to a class</span></span>

<span data-ttu-id="6f99c-882">*L’exemple d’application illustre les concepts abordés dans cette section.*</span><span class="sxs-lookup"><span data-stu-id="6f99c-882">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="6f99c-883"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> prend en charge la liaison de tableaux à des objets à l’aide d’index de tableau dans les clés de configuration.</span><span class="sxs-lookup"><span data-stu-id="6f99c-883">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="6f99c-884">Tout format de tableau qui expose un segment de clé numérique ( `:0:` , `:1:` , &hellip; `:{n}:` ) est capable d’effectuer une liaison de tableau à un tableau de classes POCO.</span><span class="sxs-lookup"><span data-stu-id="6f99c-884">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="6f99c-885">La liaison est fournie par convention.</span><span class="sxs-lookup"><span data-stu-id="6f99c-885">Binding is provided by convention.</span></span> <span data-ttu-id="6f99c-886">Les fournisseurs de configuration personnalisés ne sont pas obligés d’implémenter la liaison de tableau.</span><span class="sxs-lookup"><span data-stu-id="6f99c-886">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="6f99c-887">**Traitement de tableau en mémoire**</span><span class="sxs-lookup"><span data-stu-id="6f99c-887">**In-memory array processing**</span></span>

<span data-ttu-id="6f99c-888">Observez les valeurs et les clés de configuration indiquées dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="6f99c-888">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="6f99c-889">Clé</span><span class="sxs-lookup"><span data-stu-id="6f99c-889">Key</span></span>             | <span data-ttu-id="6f99c-890">Valeur</span><span class="sxs-lookup"><span data-stu-id="6f99c-890">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="6f99c-891">array:entries:0</span><span class="sxs-lookup"><span data-stu-id="6f99c-891">array:entries:0</span></span> | <span data-ttu-id="6f99c-892">value0</span><span class="sxs-lookup"><span data-stu-id="6f99c-892">value0</span></span> |
| <span data-ttu-id="6f99c-893">array:entries:1</span><span class="sxs-lookup"><span data-stu-id="6f99c-893">array:entries:1</span></span> | <span data-ttu-id="6f99c-894">valeur1</span><span class="sxs-lookup"><span data-stu-id="6f99c-894">value1</span></span> |
| <span data-ttu-id="6f99c-895">array:entries:2</span><span class="sxs-lookup"><span data-stu-id="6f99c-895">array:entries:2</span></span> | <span data-ttu-id="6f99c-896">valeur2</span><span class="sxs-lookup"><span data-stu-id="6f99c-896">value2</span></span> |
| <span data-ttu-id="6f99c-897">array:entries:4</span><span class="sxs-lookup"><span data-stu-id="6f99c-897">array:entries:4</span></span> | <span data-ttu-id="6f99c-898">value4</span><span class="sxs-lookup"><span data-stu-id="6f99c-898">value4</span></span> |
| <span data-ttu-id="6f99c-899">array:entries:5</span><span class="sxs-lookup"><span data-stu-id="6f99c-899">array:entries:5</span></span> | <span data-ttu-id="6f99c-900">value5</span><span class="sxs-lookup"><span data-stu-id="6f99c-900">value5</span></span> |

<span data-ttu-id="6f99c-901">Ces clés et valeurs sont chargées dans l’exemple d’application à l’aide du Fournisseur de configuration de mémoire :</span><span class="sxs-lookup"><span data-stu-id="6f99c-901">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="6f99c-902">Le tableau ignore une valeur pour l’index &num;3.</span><span class="sxs-lookup"><span data-stu-id="6f99c-902">The array skips a value for index &num;3.</span></span> <span data-ttu-id="6f99c-903">Le binder de configuration n’est pas capable de lier des valeurs null ou de créer des entrées null dans les objets liés, ce qui deviendra clair dans un moment lorsque le résultat de liaison de ce tableau à un objet est illustré.</span><span class="sxs-lookup"><span data-stu-id="6f99c-903">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="6f99c-904">Dans l’exemple d’application, une classe POCO est disponible pour contenir les données de configuration liées :</span><span class="sxs-lookup"><span data-stu-id="6f99c-904">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="6f99c-905">Les données de configuration sont liées à l’objet :</span><span class="sxs-lookup"><span data-stu-id="6f99c-905">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="6f99c-906">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) la syntaxe peut également être utilisée, ce qui se traduit par un code plus compact :</span><span class="sxs-lookup"><span data-stu-id="6f99c-906">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="6f99c-907">L’objet lié, une instance de `ArrayExample`, reçoit les données de tableau à partir de la configuration.</span><span class="sxs-lookup"><span data-stu-id="6f99c-907">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="6f99c-908">Index `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="6f99c-908">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="6f99c-909">`ArrayExample.Entries` Valeur</span><span class="sxs-lookup"><span data-stu-id="6f99c-909">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="6f99c-910">0</span><span class="sxs-lookup"><span data-stu-id="6f99c-910">0</span></span>                            | <span data-ttu-id="6f99c-911">value0</span><span class="sxs-lookup"><span data-stu-id="6f99c-911">value0</span></span>                       |
| <span data-ttu-id="6f99c-912">1</span><span class="sxs-lookup"><span data-stu-id="6f99c-912">1</span></span>                            | <span data-ttu-id="6f99c-913">valeur1</span><span class="sxs-lookup"><span data-stu-id="6f99c-913">value1</span></span>                       |
| <span data-ttu-id="6f99c-914">2</span><span class="sxs-lookup"><span data-stu-id="6f99c-914">2</span></span>                            | <span data-ttu-id="6f99c-915">valeur2</span><span class="sxs-lookup"><span data-stu-id="6f99c-915">value2</span></span>                       |
| <span data-ttu-id="6f99c-916">3</span><span class="sxs-lookup"><span data-stu-id="6f99c-916">3</span></span>                            | <span data-ttu-id="6f99c-917">value4</span><span class="sxs-lookup"><span data-stu-id="6f99c-917">value4</span></span>                       |
| <span data-ttu-id="6f99c-918">4</span><span class="sxs-lookup"><span data-stu-id="6f99c-918">4</span></span>                            | <span data-ttu-id="6f99c-919">value5</span><span class="sxs-lookup"><span data-stu-id="6f99c-919">value5</span></span>                       |

<span data-ttu-id="6f99c-920">L’index &num;3 dans l’objet lié contient les données de configuration pour la clé de configuration `array:4` et sa valeur de `value4`.</span><span class="sxs-lookup"><span data-stu-id="6f99c-920">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="6f99c-921">Lorsque des données de configuration contenant un tableau sont liées, les index de tableau dans les clés de configuration sont simplement utilisés pour itérer les données de configuration lors de la création de l’objet.</span><span class="sxs-lookup"><span data-stu-id="6f99c-921">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="6f99c-922">Une valeur null ne peut pas être conservée dans des données de configuration, et une entrée à valeur null n’est pas créée dans un objet lié quand un tableau dans des clés de configuration ignore un ou plusieurs index.</span><span class="sxs-lookup"><span data-stu-id="6f99c-922">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="6f99c-923">L’élément de configuration manquant pour l’index &num;3 peut être fourni avant la liaison à l’instance `ArrayExample` par n’importe quel fournisseur de configuration qui génère la paire clé-valeur correcte dans la configuration.</span><span class="sxs-lookup"><span data-stu-id="6f99c-923">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="6f99c-924">Si l’exemple inclut un Fournisseur de configuration JSON supplémentaire avec la paire clé-valeur manquante, `ArrayExample.Entries` correspond à l’intégralité du tableau de configuration :</span><span class="sxs-lookup"><span data-stu-id="6f99c-924">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="6f99c-925">*missing_value.json* :</span><span class="sxs-lookup"><span data-stu-id="6f99c-925">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="6f99c-926">Dans `ConfigureAppConfiguration` :</span><span class="sxs-lookup"><span data-stu-id="6f99c-926">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="6f99c-927">La paire clé-valeur indiquée dans le tableau est chargée dans la configuration.</span><span class="sxs-lookup"><span data-stu-id="6f99c-927">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="6f99c-928">Clé</span><span class="sxs-lookup"><span data-stu-id="6f99c-928">Key</span></span>             | <span data-ttu-id="6f99c-929">Valeur</span><span class="sxs-lookup"><span data-stu-id="6f99c-929">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="6f99c-930">array:entries:3</span><span class="sxs-lookup"><span data-stu-id="6f99c-930">array:entries:3</span></span> | <span data-ttu-id="6f99c-931">valeur3</span><span class="sxs-lookup"><span data-stu-id="6f99c-931">value3</span></span> |

<span data-ttu-id="6f99c-932">Si l’instance de classe `ArrayExample` est liée une fois que le Fournisseur de configuration JSON inclut l’entrée pour l’index &num;3, le tableau `ArrayExample.Entries` inclut la valeur.</span><span class="sxs-lookup"><span data-stu-id="6f99c-932">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="6f99c-933">Index `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="6f99c-933">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="6f99c-934">`ArrayExample.Entries` Valeur</span><span class="sxs-lookup"><span data-stu-id="6f99c-934">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="6f99c-935">0</span><span class="sxs-lookup"><span data-stu-id="6f99c-935">0</span></span>                            | <span data-ttu-id="6f99c-936">value0</span><span class="sxs-lookup"><span data-stu-id="6f99c-936">value0</span></span>                       |
| <span data-ttu-id="6f99c-937">1</span><span class="sxs-lookup"><span data-stu-id="6f99c-937">1</span></span>                            | <span data-ttu-id="6f99c-938">valeur1</span><span class="sxs-lookup"><span data-stu-id="6f99c-938">value1</span></span>                       |
| <span data-ttu-id="6f99c-939">2</span><span class="sxs-lookup"><span data-stu-id="6f99c-939">2</span></span>                            | <span data-ttu-id="6f99c-940">valeur2</span><span class="sxs-lookup"><span data-stu-id="6f99c-940">value2</span></span>                       |
| <span data-ttu-id="6f99c-941">3</span><span class="sxs-lookup"><span data-stu-id="6f99c-941">3</span></span>                            | <span data-ttu-id="6f99c-942">valeur3</span><span class="sxs-lookup"><span data-stu-id="6f99c-942">value3</span></span>                       |
| <span data-ttu-id="6f99c-943">4</span><span class="sxs-lookup"><span data-stu-id="6f99c-943">4</span></span>                            | <span data-ttu-id="6f99c-944">value4</span><span class="sxs-lookup"><span data-stu-id="6f99c-944">value4</span></span>                       |
| <span data-ttu-id="6f99c-945">5</span><span class="sxs-lookup"><span data-stu-id="6f99c-945">5</span></span>                            | <span data-ttu-id="6f99c-946">value5</span><span class="sxs-lookup"><span data-stu-id="6f99c-946">value5</span></span>                       |

<span data-ttu-id="6f99c-947">**Traitement de tableau JSON**</span><span class="sxs-lookup"><span data-stu-id="6f99c-947">**JSON array processing**</span></span>

<span data-ttu-id="6f99c-948">Si un fichier JSON contient un tableau, les clés de configuration sont créés pour les éléments du tableau avec un index de section basé sur zéro.</span><span class="sxs-lookup"><span data-stu-id="6f99c-948">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="6f99c-949">Dans le fichier de configuration suivant, `subsection` est un tableau :</span><span class="sxs-lookup"><span data-stu-id="6f99c-949">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="6f99c-950">Le Fournisseur de configuration JSON lit les données de configuration dans les paires clé-valeur suivantes :</span><span class="sxs-lookup"><span data-stu-id="6f99c-950">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="6f99c-951">Clé</span><span class="sxs-lookup"><span data-stu-id="6f99c-951">Key</span></span>                     | <span data-ttu-id="6f99c-952">Valeur</span><span class="sxs-lookup"><span data-stu-id="6f99c-952">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="6f99c-953">json_array:key</span><span class="sxs-lookup"><span data-stu-id="6f99c-953">json_array:key</span></span>          | <span data-ttu-id="6f99c-954">valueA</span><span class="sxs-lookup"><span data-stu-id="6f99c-954">valueA</span></span> |
| <span data-ttu-id="6f99c-955">json_array:subsection:0</span><span class="sxs-lookup"><span data-stu-id="6f99c-955">json_array:subsection:0</span></span> | <span data-ttu-id="6f99c-956">valueB</span><span class="sxs-lookup"><span data-stu-id="6f99c-956">valueB</span></span> |
| <span data-ttu-id="6f99c-957">json_array:subsection:1</span><span class="sxs-lookup"><span data-stu-id="6f99c-957">json_array:subsection:1</span></span> | <span data-ttu-id="6f99c-958">valueC</span><span class="sxs-lookup"><span data-stu-id="6f99c-958">valueC</span></span> |
| <span data-ttu-id="6f99c-959">json_array:subsection:2</span><span class="sxs-lookup"><span data-stu-id="6f99c-959">json_array:subsection:2</span></span> | <span data-ttu-id="6f99c-960">valueD</span><span class="sxs-lookup"><span data-stu-id="6f99c-960">valueD</span></span> |

<span data-ttu-id="6f99c-961">Dans l’exemple d’application, la classe POCO suivante est disponible pour lier les paires clé-valeur de configuration :</span><span class="sxs-lookup"><span data-stu-id="6f99c-961">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="6f99c-962">Après la liaison, `JsonArrayExample.Key` contient la valeur `valueA`.</span><span class="sxs-lookup"><span data-stu-id="6f99c-962">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="6f99c-963">Les valeurs de la sous-section sont stockées dans la propriété de tableau POCO, `Subsection`.</span><span class="sxs-lookup"><span data-stu-id="6f99c-963">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="6f99c-964">Index `JsonArrayExample.Subsection`</span><span class="sxs-lookup"><span data-stu-id="6f99c-964">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="6f99c-965">`JsonArrayExample.Subsection` Valeur</span><span class="sxs-lookup"><span data-stu-id="6f99c-965">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="6f99c-966">0</span><span class="sxs-lookup"><span data-stu-id="6f99c-966">0</span></span>                                   | <span data-ttu-id="6f99c-967">valueB</span><span class="sxs-lookup"><span data-stu-id="6f99c-967">valueB</span></span>                              |
| <span data-ttu-id="6f99c-968">1</span><span class="sxs-lookup"><span data-stu-id="6f99c-968">1</span></span>                                   | <span data-ttu-id="6f99c-969">valueC</span><span class="sxs-lookup"><span data-stu-id="6f99c-969">valueC</span></span>                              |
| <span data-ttu-id="6f99c-970">2</span><span class="sxs-lookup"><span data-stu-id="6f99c-970">2</span></span>                                   | <span data-ttu-id="6f99c-971">valueD</span><span class="sxs-lookup"><span data-stu-id="6f99c-971">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="6f99c-972">Fournisseur de configuration personnalisé</span><span class="sxs-lookup"><span data-stu-id="6f99c-972">Custom configuration provider</span></span>

<span data-ttu-id="6f99c-973">L’exemple d’application montre comment créer un fournisseur de configuration de base qui lit les paires clé-valeur de configuration à partir d’une base de données à l’aide [d’Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="6f99c-973">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="6f99c-974">Le fournisseur présente les caractéristiques suivantes :</span><span class="sxs-lookup"><span data-stu-id="6f99c-974">The provider has the following characteristics:</span></span>

* <span data-ttu-id="6f99c-975">La base de données en mémoire EF est utilisée à des fins de démonstration.</span><span class="sxs-lookup"><span data-stu-id="6f99c-975">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="6f99c-976">Pour utiliser une base de données qui nécessite une chaîne de connexion, implémentez un autre `ConfigurationBuilder` pour fournir la chaîne de connexion à partir d’un autre fournisseur de configuration.</span><span class="sxs-lookup"><span data-stu-id="6f99c-976">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="6f99c-977">Le fournisseur lit une table de base de données dans la configuration au démarrage.</span><span class="sxs-lookup"><span data-stu-id="6f99c-977">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="6f99c-978">Le fournisseur n’interroge pas la base de données par clé.</span><span class="sxs-lookup"><span data-stu-id="6f99c-978">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="6f99c-979">Le rechargement en cas de changement n’est pas implémenté, par conséquent, la mise à jour la base de données après le démarrage de l’application n’a aucun effet sur la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="6f99c-979">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="6f99c-980">Définissez une entité `EFConfigurationValue` pour le stockage des valeurs de configuration dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="6f99c-980">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="6f99c-981">*Models/EFConfigurationValue.cs* :</span><span class="sxs-lookup"><span data-stu-id="6f99c-981">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="6f99c-982">Ajoutez un `EFConfigurationContext` pour stocker les valeurs configurées et y accéder.</span><span class="sxs-lookup"><span data-stu-id="6f99c-982">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="6f99c-983">*EFConfigurationProvider/EFConfigurationContext.cs* :</span><span class="sxs-lookup"><span data-stu-id="6f99c-983">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="6f99c-984">Créez une classe qui implémente <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="6f99c-984">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="6f99c-985">*EFConfigurationProvider/EFConfigurationSource.cs* :</span><span class="sxs-lookup"><span data-stu-id="6f99c-985">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="6f99c-986">Créez le fournisseur de configuration personnalisé en héritant de <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="6f99c-986">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="6f99c-987">Le fournisseur de configuration initialise la base de données quand elle est vide.</span><span class="sxs-lookup"><span data-stu-id="6f99c-987">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="6f99c-988">*EFConfigurationProvider/EFConfigurationProvider.cs* :</span><span class="sxs-lookup"><span data-stu-id="6f99c-988">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="6f99c-989">Une méthode d’extension `AddEFConfiguration` permet d’ajouter la source de configuration à un `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="6f99c-989">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="6f99c-990">*Extensions/EntityFrameworkExtensions.cs* :</span><span class="sxs-lookup"><span data-stu-id="6f99c-990">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="6f99c-991">Le code suivant montre comment utiliser le `EFConfigurationProvider` personnalisé dans *Program.cs* :</span><span class="sxs-lookup"><span data-stu-id="6f99c-991">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="6f99c-992">Accéder à la configuration lors du démarrage</span><span class="sxs-lookup"><span data-stu-id="6f99c-992">Access configuration during startup</span></span>

<span data-ttu-id="6f99c-993">Injectez `IConfiguration` dans le constructeur `Startup` pour accéder aux valeurs de configuration dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="6f99c-993">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="6f99c-994">Pour accéder à la configuration dans `Startup.Configure`, injectez `IConfiguration` directement dans la méthode ou utilisez l’instance à partir du constructeur :</span><span class="sxs-lookup"><span data-stu-id="6f99c-994">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="6f99c-995">Pour obtenir un exemple d’accès à la configuration à l’aide des méthodes pratiques de démarrage, consultez [Démarrage de l’application : méthodes pratiques](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="6f99c-995">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-no-locrazor-pages-page-or-mvc-view"></a><span data-ttu-id="6f99c-996">Configuration de l’accès dans une Razor page pages ou une vue MVC</span><span class="sxs-lookup"><span data-stu-id="6f99c-996">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="6f99c-997">Pour accéder aux paramètres de configuration dans une Razor page pages ou une vue MVC, ajoutez une [directive using](xref:mvc/views/razor#using) ([référence C# : directive using](/dotnet/csharp/language-reference/keywords/using-directive)) pour l' [ espace de nomsMicrosoft.Extensions.Configfiguration](xref:Microsoft.Extensions.Configuration) et injectez <xref:Microsoft.Extensions.Configuration.IConfiguration> dans la page ou la vue.</span><span class="sxs-lookup"><span data-stu-id="6f99c-997">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="6f99c-998">Dans une Razor page pages :</span><span class="sxs-lookup"><span data-stu-id="6f99c-998">In a Razor Pages page:</span></span>

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

<span data-ttu-id="6f99c-999">Dans une vue MVC :</span><span class="sxs-lookup"><span data-stu-id="6f99c-999">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="6f99c-1000">Ajouter la configuration à partir d’un assembly externe</span><span class="sxs-lookup"><span data-stu-id="6f99c-1000">Add configuration from an external assembly</span></span>

<span data-ttu-id="6f99c-1001">Une implémentation de <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> permet d’ajouter des améliorations à une application au démarrage à partir d’un assembly externe, en dehors de la classe `Startup` de l’application.</span><span class="sxs-lookup"><span data-stu-id="6f99c-1001">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="6f99c-1002">Pour plus d'informations, consultez <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="6f99c-1002">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6f99c-1003">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="6f99c-1003">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end