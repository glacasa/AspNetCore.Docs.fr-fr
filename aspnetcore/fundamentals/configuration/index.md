---
title: Configuration dans ASP.NET Core
author: rick-anderson
description: Découvrez comment utiliser l’API de configuration pour configurer une application ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 3/29/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/configuration/index
ms.openlocfilehash: c2a7ef9c1523bc179524f328905f3a4b1460a1a5
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774494"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="825d6-103">Configuration dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="825d6-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="825d6-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT) et [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="825d6-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="825d6-105">La configuration dans ASP.NET Core est effectuée à l’aide d’un ou de plusieurs [fournisseurs de configuration](#cp).</span><span class="sxs-lookup"><span data-stu-id="825d6-105">Configuration in ASP.NET Core is performed using one or more [configuration providers](#cp).</span></span> <span data-ttu-id="825d6-106">Les fournisseurs de configuration lisent les données de configuration des paires clé-valeur à l’aide d’une variété de sources de configuration :</span><span class="sxs-lookup"><span data-stu-id="825d6-106">Configuration providers read configuration data from key-value pairs using a variety of configuration sources:</span></span>

* <span data-ttu-id="825d6-107">Fichiers de paramètres, tels que *appSettings. JSON*</span><span class="sxs-lookup"><span data-stu-id="825d6-107">Settings files, such as *appsettings.json*</span></span>
* <span data-ttu-id="825d6-108">Variables d'environnement</span><span class="sxs-lookup"><span data-stu-id="825d6-108">Environment variables</span></span>
* <span data-ttu-id="825d6-109">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="825d6-109">Azure Key Vault</span></span>
* <span data-ttu-id="825d6-110">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="825d6-110">Azure App Configuration</span></span>
* <span data-ttu-id="825d6-111">Arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="825d6-111">Command-line arguments</span></span>
* <span data-ttu-id="825d6-112">Fournisseurs personnalisés, installés ou créés</span><span class="sxs-lookup"><span data-stu-id="825d6-112">Custom providers, installed or created</span></span>
* <span data-ttu-id="825d6-113">Fichiers de répertoire</span><span class="sxs-lookup"><span data-stu-id="825d6-113">Directory files</span></span>
* <span data-ttu-id="825d6-114">Objets .NET en mémoire</span><span class="sxs-lookup"><span data-stu-id="825d6-114">In-memory .NET objects</span></span>

<span data-ttu-id="825d6-115">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="825d6-115">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="default"></a>

## <a name="default-configuration"></a><span data-ttu-id="825d6-116">Configuration par défaut</span><span class="sxs-lookup"><span data-stu-id="825d6-116">Default configuration</span></span>

<span data-ttu-id="825d6-117">ASP.NET Core les applications Web créées avec [dotnet New](/dotnet/core/tools/dotnet-new) ou Visual Studio génèrent le code suivant :</span><span class="sxs-lookup"><span data-stu-id="825d6-117">ASP.NET Core web apps created with [dotnet new](/dotnet/core/tools/dotnet-new) or Visual Studio generate the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet&highlight=9)]

 <span data-ttu-id="825d6-118"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> fournit la configuration par défaut de l’application dans l’ordre suivant :</span><span class="sxs-lookup"><span data-stu-id="825d6-118"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> provides default configuration for the app in the following order:</span></span>

1. <span data-ttu-id="825d6-119">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) : ajoute un existant `IConfiguration` en tant que source.</span><span class="sxs-lookup"><span data-stu-id="825d6-119">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) :  Adds an existing `IConfiguration` as a source.</span></span> <span data-ttu-id="825d6-120">Dans le cas de configuration par défaut, ajoute la configuration d' [hôte](#hvac) et la définit en tant que première source de la configuration de l' _application_ .</span><span class="sxs-lookup"><span data-stu-id="825d6-120">In the default configuration case, adds the [host](#hvac) configuration and setting it as the first source for the _app_ configuration.</span></span>
1. <span data-ttu-id="825d6-121">[appSettings. JSON](#appsettingsjson) à l’aide du [fournisseur de configuration JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="825d6-121">[appsettings.json](#appsettingsjson) using the [JSON configuration provider](#file-configuration-provider).</span></span>
1. <span data-ttu-id="825d6-122">*appSettings.* `Environment` *. JSON* à l’aide du [fournisseur de configuration JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="825d6-122">*appsettings.*`Environment`*.json* using the [JSON configuration provider](#file-configuration-provider).</span></span> <span data-ttu-id="825d6-123">Par exemple, *appSettings*. ***Production***. *JSON* et *appSettings*. ***Développement***. *JSON*.</span><span class="sxs-lookup"><span data-stu-id="825d6-123">For example, *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json*.</span></span>
1. <span data-ttu-id="825d6-124">[Secrets d’application](xref:security/app-secrets) lorsque l’application s’exécute `Development` dans l’environnement.</span><span class="sxs-lookup"><span data-stu-id="825d6-124">[App secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
1. <span data-ttu-id="825d6-125">Variables d’environnement à l’aide du [fournisseur de configuration des variables d’environnement](#evcp).</span><span class="sxs-lookup"><span data-stu-id="825d6-125">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="825d6-126">Arguments de ligne de commande à l’aide du [fournisseur de configuration de ligne de commande](#command-line).</span><span class="sxs-lookup"><span data-stu-id="825d6-126">Command-line arguments using the [Command-line configuration provider](#command-line).</span></span>

<span data-ttu-id="825d6-127">Les fournisseurs de configuration ajoutés ultérieurement remplacent les paramètres de clé précédents.</span><span class="sxs-lookup"><span data-stu-id="825d6-127">Configuration providers that are added later override previous key settings.</span></span> <span data-ttu-id="825d6-128">Par exemple, si `MyKey` est défini à la fois dans *appSettings. JSON* et dans l’environnement, la valeur d’environnement est utilisée.</span><span class="sxs-lookup"><span data-stu-id="825d6-128">For example, if `MyKey` is set in both *appsettings.json* and the environment, the environment value is used.</span></span> <span data-ttu-id="825d6-129">À l’aide des fournisseurs de configuration par défaut, le [fournisseur de configuration de ligne de commande](#command-line-configuration-provider) remplace tous les autres fournisseurs.</span><span class="sxs-lookup"><span data-stu-id="825d6-129">Using the default configuration providers, the  [Command-line configuration provider](#command-line-configuration-provider) overrides all other providers.</span></span>

<span data-ttu-id="825d6-130">Pour plus d’informations `CreateDefaultBuilder`sur, consultez [paramètres par défaut du générateur](xref:fundamentals/host/generic-host#default-builder-settings).</span><span class="sxs-lookup"><span data-stu-id="825d6-130">For more information on `CreateDefaultBuilder`, see [Default builder settings](xref:fundamentals/host/generic-host#default-builder-settings).</span></span>

<span data-ttu-id="825d6-131">Le code suivant affiche les fournisseurs de configuration activés dans l’ordre dans lequel ils ont été ajoutés :</span><span class="sxs-lookup"><span data-stu-id="825d6-131">The following code displays the enabled configuration providers in the order they were added:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Index2.cshtml.cs?name=snippet)]

### <a name="appsettingsjson"></a><span data-ttu-id="825d6-132">appsettings.json</span><span class="sxs-lookup"><span data-stu-id="825d6-132">appsettings.json</span></span>

<span data-ttu-id="825d6-133">Prenons le fichier *appSettings. JSON* suivant :</span><span class="sxs-lookup"><span data-stu-id="825d6-133">Consider the following *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="825d6-134">Le code suivant de l' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) affiche plusieurs des paramètres de configuration précédents :</span><span class="sxs-lookup"><span data-stu-id="825d6-134">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="825d6-135">La configuration <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> par défaut charge dans l’ordre suivant :</span><span class="sxs-lookup"><span data-stu-id="825d6-135">The default <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration in the following order:</span></span>

1. <span data-ttu-id="825d6-136">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="825d6-136">*appsettings.json*</span></span>
1. <span data-ttu-id="825d6-137">*appSettings.* `Environment` *. JSON* : par exemple, *appSettings*. ***Production***. *JSON* et *appSettings*. ***Développement***. fichiers *JSON* .</span><span class="sxs-lookup"><span data-stu-id="825d6-137">*appsettings.*`Environment`*.json* : For example, the *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json* files.</span></span> <span data-ttu-id="825d6-138">La version de l’environnement du fichier est chargée à partir de [IHostingEnvironment. EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="825d6-138">The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="825d6-139">Pour plus d’informations, consultez <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="825d6-139">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="825d6-140">*appSettings*. `Environment`. les valeurs *JSON* remplacent les clés dans *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="825d6-140">*appsettings*.`Environment`.*json* values override keys in *appsettings.json*.</span></span> <span data-ttu-id="825d6-141">Par exemple, par défaut :</span><span class="sxs-lookup"><span data-stu-id="825d6-141">For example, by default:</span></span>

* <span data-ttu-id="825d6-142">Dans le développement, *appSettings*. ***Développement***. la configuration *JSON* remplace les valeurs trouvées dans *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="825d6-142">In development, *appsettings*.***Development***.*json* configuration overwrites values found in *appsettings.json*.</span></span>
* <span data-ttu-id="825d6-143">En production, *appSettings*. ***Production***. la configuration *JSON* remplace les valeurs trouvées dans *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="825d6-143">In production, *appsettings*.***Production***.*json* configuration overwrites values found in *appsettings.json*.</span></span> <span data-ttu-id="825d6-144">Par exemple, lors du déploiement de l’application sur Azure.</span><span class="sxs-lookup"><span data-stu-id="825d6-144">For example, when deploying the app to Azure.</span></span>

<a name="optpat"></a>

#### <a name="bind-hierarchical-configuration-data-using-the-options-pattern"></a><span data-ttu-id="825d6-145">Lier des données de configuration hiérarchiques à l’aide du modèle options</span><span class="sxs-lookup"><span data-stu-id="825d6-145">Bind hierarchical configuration data using the options pattern</span></span>

<span data-ttu-id="825d6-146">La méthode recommandée pour lire les valeurs de configuration associées utilise le [modèle d’options](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="825d6-146">The preferred way to read related configuration values is using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="825d6-147">Par exemple, pour lire les valeurs de configuration suivantes :</span><span class="sxs-lookup"><span data-stu-id="825d6-147">For example, to read the following configuration values:</span></span>

```json
  "Position": {
    "Title": "Editor",
    "Name": "Joe Smith"
  }
```

<span data-ttu-id="825d6-148">Créez la classe `PositionOptions` suivante :</span><span class="sxs-lookup"><span data-stu-id="825d6-148">Create the following `PositionOptions` class:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Options/PositionOptions.cs?name=snippet)]

<span data-ttu-id="825d6-149">Toutes les propriétés publiques en lecture-écriture du type sont liées.</span><span class="sxs-lookup"><span data-stu-id="825d6-149">All the public read-write properties of the type are bound.</span></span> <span data-ttu-id="825d6-150">Les champs ne sont ***pas*** liés.</span><span class="sxs-lookup"><span data-stu-id="825d6-150">Fields are ***not*** bound.</span></span>

<span data-ttu-id="825d6-151">Le code suivant :</span><span class="sxs-lookup"><span data-stu-id="825d6-151">The following code:</span></span>

* <span data-ttu-id="825d6-152">Appelle [ConfigurationBinder. bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) pour lier la `PositionOptions` classe à la `Position` section.</span><span class="sxs-lookup"><span data-stu-id="825d6-152">Calls [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) to bind the `PositionOptions` class to the `Position` section.</span></span>
* <span data-ttu-id="825d6-153">Affiche les `Position` données de configuration.</span><span class="sxs-lookup"><span data-stu-id="825d6-153">Displays the `Position` configuration data.</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test22.cshtml.cs?name=snippet)]

<span data-ttu-id="825d6-154">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)lie et retourne le type spécifié.</span><span class="sxs-lookup"><span data-stu-id="825d6-154">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="825d6-155">`ConfigurationBinder.Get<T>`peut être plus pratique que l' `ConfigurationBinder.Bind`utilisation de.</span><span class="sxs-lookup"><span data-stu-id="825d6-155">`ConfigurationBinder.Get<T>` may be more convenient than using `ConfigurationBinder.Bind`.</span></span> <span data-ttu-id="825d6-156">Le code suivant montre comment utiliser `ConfigurationBinder.Get<T>` avec la `PositionOptions` classe :</span><span class="sxs-lookup"><span data-stu-id="825d6-156">The following code shows how to use `ConfigurationBinder.Get<T>` with the `PositionOptions` class:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test21.cshtml.cs?name=snippet)]

<span data-ttu-id="825d6-157">Une autre approche de l’utilisation du ***modèle options*** consiste à lier `Position` la section et à l’ajouter au [conteneur du service d’injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="825d6-157">An alternative approach when using the ***options pattern*** is to bind the `Position` section and add it to the [dependency injection service container](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="825d6-158">Dans le code suivant, `PositionOptions` est ajouté au conteneur de services avec <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> et lié à la configuration :</span><span class="sxs-lookup"><span data-stu-id="825d6-158">In the following code, `PositionOptions` is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Startup.cs?name=snippet)]

<span data-ttu-id="825d6-159">À l’aide du code précédent, le code suivant lit les options de position :</span><span class="sxs-lookup"><span data-stu-id="825d6-159">Using the preceding code, the following code reads the position options:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test2.cshtml.cs?name=snippet)]

<span data-ttu-id="825d6-160">À l’aide de la configuration [par défaut](#default) , *appSettings. JSON* et *appSettings.* `Environment`les fichiers *. JSON* sont activés avec [reloadOnChange : true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span><span class="sxs-lookup"><span data-stu-id="825d6-160">Using the [default](#default) configuration, the *appsettings.json* and *appsettings.*`Environment`*.json* files are enabled with [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span></span> <span data-ttu-id="825d6-161">Modifications apportées aux *appSettings. JSON* et *appSettings.* `Environment`le fichier *. JSON* ***après*** le démarrage de l’application est lu par le [fournisseur de configuration JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="825d6-161">Changes made to the *appsettings.json* and *appsettings.*`Environment`*.json* file ***after*** the app starts are read by the [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="825d6-162">Pour plus d’informations sur l’ajout de fichiers de configuration JSON supplémentaires, consultez [fournisseur de configuration JSON](#jcp) dans ce document.</span><span class="sxs-lookup"><span data-stu-id="825d6-162">See [JSON configuration provider](#jcp) in this document for information on adding additional JSON configuration files.</span></span>

<a name="security"></a>

## <a name="security-and-secret-manager"></a><span data-ttu-id="825d6-163">Responsable de la sécurité et du secret</span><span class="sxs-lookup"><span data-stu-id="825d6-163">Security and secret manager</span></span>

<span data-ttu-id="825d6-164">Instructions relatives aux données de configuration :</span><span class="sxs-lookup"><span data-stu-id="825d6-164">Configuration data guidelines:</span></span>

* <span data-ttu-id="825d6-165">Ne stockez jamais des mots de passe ou d’autres données sensibles dans le code du fournisseur de configuration ou dans les fichiers de configuration en texte clair.</span><span class="sxs-lookup"><span data-stu-id="825d6-165">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span> <span data-ttu-id="825d6-166">Le [Gestionnaire de secret](xref:security/app-secrets) peut être utilisé pour stocker les secrets en développement.</span><span class="sxs-lookup"><span data-stu-id="825d6-166">The [Secret manager](xref:security/app-secrets) can be used to store secrets in development.</span></span>
* <span data-ttu-id="825d6-167">N’utilisez aucun secret de production dans les environnements de développement ou de test.</span><span class="sxs-lookup"><span data-stu-id="825d6-167">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="825d6-168">Spécifiez les secrets en dehors du projet afin qu’ils ne puissent pas être validés par inadvertance dans un référentiel de code source.</span><span class="sxs-lookup"><span data-stu-id="825d6-168">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="825d6-169">Par [défaut](#default), le [Gestionnaire de secret](xref:security/app-secrets) lit les paramètres de configuration après *appSettings. JSON* et *appSettings.* `Environment` *. JSON*.</span><span class="sxs-lookup"><span data-stu-id="825d6-169">By [default](#default), [Secret manager](xref:security/app-secrets) reads configuration settings after *appsettings.json* and *appsettings.*`Environment`*.json*.</span></span>

<span data-ttu-id="825d6-170">Pour plus d’informations sur le stockage des mots de passe ou d’autres données sensibles :</span><span class="sxs-lookup"><span data-stu-id="825d6-170">For more information on storing passwords or other sensitive data:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="825d6-171"><xref:security/app-secrets>: Fournit des conseils sur l’utilisation de variables d’environnement pour stocker des données sensibles.</span><span class="sxs-lookup"><span data-stu-id="825d6-171"><xref:security/app-secrets>:  Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="825d6-172">Le gestionnaire de secret utilise le [fournisseur de configuration de fichiers](#fcp) pour stocker les secrets de l’utilisateur dans un fichier JSON sur le système local.</span><span class="sxs-lookup"><span data-stu-id="825d6-172">The Secret Manager uses the [File configuration provider](#fcp) to store user secrets in a JSON file on the local system.</span></span>

<span data-ttu-id="825d6-173">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) stocke en toute sécurité des secrets d’application pour les applications ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="825d6-173">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="825d6-174">Pour plus d’informations, consultez <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="825d6-174">For more information, see <xref:security/key-vault-configuration>.</span></span>

<a name="evcp"></a>

## <a name="environment-variables"></a><span data-ttu-id="825d6-175">Variables d'environnement</span><span class="sxs-lookup"><span data-stu-id="825d6-175">Environment variables</span></span>

<span data-ttu-id="825d6-176">À l’aide de la configuration <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> [par défaut](#default) , le charge la configuration à partir des paires clé-valeur de variable d’environnement après la lecture de *appSettings. JSON*, *appSettings.* `Environment` *. JSON*et le [Gestionnaire de secret](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="825d6-176">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs after reading *appsettings.json*, *appsettings.*`Environment`*.json*, and [Secret manager](xref:security/app-secrets).</span></span> <span data-ttu-id="825d6-177">Par conséquent, les valeurs de clés lues à partir de l’environnement remplacent les valeurs lues dans *appSettings. JSON*, *appSettings.* `Environment` *. JSON*et le gestionnaire de secret.</span><span class="sxs-lookup"><span data-stu-id="825d6-177">Therefore, key values read from the environment override values read from *appsettings.json*, *appsettings.*`Environment`*.json*, and Secret manager.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="825d6-178">Les commandes `set` suivantes :</span><span class="sxs-lookup"><span data-stu-id="825d6-178">The following `set` commands:</span></span>

* <span data-ttu-id="825d6-179">Définissez les clés et les valeurs d’environnement de l' [exemple précédent](#appsettingsjson) sur Windows.</span><span class="sxs-lookup"><span data-stu-id="825d6-179">Set the environment keys and values of the [preceding example](#appsettingsjson) on Windows.</span></span>
* <span data-ttu-id="825d6-180">Testez les paramètres lors de l’utilisation de l' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span><span class="sxs-lookup"><span data-stu-id="825d6-180">Test the settings when using the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span></span> <span data-ttu-id="825d6-181">La `dotnet run` commande doit être exécutée dans le répertoire du projet.</span><span class="sxs-lookup"><span data-stu-id="825d6-181">The `dotnet run` command must be run in the project directory.</span></span>

```dotnetcli
set MyKey="My key from Environment"
set Position__Title=Environment_Editor
set Position__Name=Environment_Rick
dotnet run
```

<span data-ttu-id="825d6-182">Paramètres d’environnement précédents :</span><span class="sxs-lookup"><span data-stu-id="825d6-182">The preceding environment settings:</span></span>

* <span data-ttu-id="825d6-183">Sont uniquement définies dans les processus lancés à partir de la fenêtre de commande dans laquelle ils ont été définis.</span><span class="sxs-lookup"><span data-stu-id="825d6-183">Are only set in processes launched from the command window they were set in.</span></span>
* <span data-ttu-id="825d6-184">Ne seront pas lues par les navigateurs lancés avec Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="825d6-184">Won't be read by browsers launched with Visual Studio.</span></span>

<span data-ttu-id="825d6-185">Les commandes [setx](/windows-server/administration/windows-commands/setx) suivantes peuvent être utilisées pour définir les clés et les valeurs d’environnement sur Windows.</span><span class="sxs-lookup"><span data-stu-id="825d6-185">The following [setx](/windows-server/administration/windows-commands/setx) commands can be used to set the environment keys and values on Windows.</span></span> <span data-ttu-id="825d6-186">Contrairement `set`à `setx` , les paramètres sont conservés.</span><span class="sxs-lookup"><span data-stu-id="825d6-186">Unlike `set`, `setx` settings are persisted.</span></span> <span data-ttu-id="825d6-187">`/M`définit la variable dans l’environnement système.</span><span class="sxs-lookup"><span data-stu-id="825d6-187">`/M` sets the variable in the system environment.</span></span> <span data-ttu-id="825d6-188">Si le `/M` commutateur n’est pas utilisé, une variable d’environnement utilisateur est définie.</span><span class="sxs-lookup"><span data-stu-id="825d6-188">If the `/M` switch isn't used, a user environment variable is set.</span></span>

```cmd
setx MyKey "My key from setx Environment" /M
setx Position__Title Setx_Environment_Editor /M
setx Position__Name Environment_Rick /M
```

<span data-ttu-id="825d6-189">Pour vérifier que les commandes précédentes remplacent *appSettings. JSON* et *appSettings.* `Environment` *. JSON*:</span><span class="sxs-lookup"><span data-stu-id="825d6-189">To test that the preceding commands override *appsettings.json* and *appsettings.*`Environment`*.json*:</span></span>

* <span data-ttu-id="825d6-190">Avec Visual Studio : quittez et redémarrez Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="825d6-190">With Visual Studio: Exit and restart Visual Studio.</span></span>
* <span data-ttu-id="825d6-191">Avec l’interface CLI : démarrez une nouvelle fenêtre de commande `dotnet run`et entrez.</span><span class="sxs-lookup"><span data-stu-id="825d6-191">With the CLI: Start a new command window and enter `dotnet run`.</span></span>

<span data-ttu-id="825d6-192">Appelez <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> avec une chaîne pour spécifier un préfixe pour les variables d’environnement :</span><span class="sxs-lookup"><span data-stu-id="825d6-192">Call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> with a string to specify a prefix for environment variables:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet4&highlight=12)]

<span data-ttu-id="825d6-193">Dans le code précédent :</span><span class="sxs-lookup"><span data-stu-id="825d6-193">In the preceding code:</span></span>

* <span data-ttu-id="825d6-194">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")`est ajouté après les [fournisseurs de configuration par défaut](#default).</span><span class="sxs-lookup"><span data-stu-id="825d6-194">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="825d6-195">Pour obtenir un exemple de classement des fournisseurs de configuration, consultez [fournisseur de configuration JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="825d6-195">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>
* <span data-ttu-id="825d6-196">Les variables d’environnement définies `MyCustomPrefix_` avec le préfixe remplacent les [fournisseurs de configuration par défaut](#default).</span><span class="sxs-lookup"><span data-stu-id="825d6-196">Environment variables set with the `MyCustomPrefix_` prefix override the [default configuration providers](#default).</span></span> <span data-ttu-id="825d6-197">Cela comprend les variables d’environnement sans le préfixe.</span><span class="sxs-lookup"><span data-stu-id="825d6-197">This includes environment variables without the prefix.</span></span>

<span data-ttu-id="825d6-198">Le préfixe est supprimé lorsque les paires clé-valeur de configuration sont lues.</span><span class="sxs-lookup"><span data-stu-id="825d6-198">The prefix is stripped off when the configuration key-value pairs are read.</span></span>

<span data-ttu-id="825d6-199">Les commandes suivantes testent le préfixe personnalisé :</span><span class="sxs-lookup"><span data-stu-id="825d6-199">The following commands test the custom prefix:</span></span>

```dotnetcli
set MyCustomPrefix_MyKey="My key with MyCustomPrefix_ Environment"
set MyCustomPrefix_Position__Title=Editor_with_customPrefix
set MyCustomPrefix_Position__Name=Environment_Rick_cp
dotnet run
```

<span data-ttu-id="825d6-200">La [configuration par défaut](#default) charge les variables d’environnement et les arguments de `DOTNET_` ligne `ASPNETCORE_`de commande préfixé avec et.</span><span class="sxs-lookup"><span data-stu-id="825d6-200">The [default configuration](#default) loads environment variables and command line arguments prefixed with `DOTNET_` and `ASPNETCORE_`.</span></span> <span data-ttu-id="825d6-201">Les `DOTNET_` préfixes et `ASPNETCORE_` sont utilisés par ASP.net Core pour la configuration de l' [hôte et](xref:fundamentals/host/generic-host#host-configuration)de l’application, mais pas pour la configuration de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="825d6-201">The `DOTNET_` and `ASPNETCORE_` prefixes are used by ASP.NET Core for [host and app configuration](xref:fundamentals/host/generic-host#host-configuration), but not for user configuration.</span></span> <span data-ttu-id="825d6-202">Pour plus d’informations sur la configuration de l’hôte et de l’application, consultez [hôte générique .net](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="825d6-202">For more information on host and app configuration, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

<span data-ttu-id="825d6-203">Dans [Azure App service](https://azure.microsoft.com/services/app-service/), sélectionnez **nouveau paramètre d’application** dans la page **paramètres > configuration** .</span><span class="sxs-lookup"><span data-stu-id="825d6-203">On [Azure App Service](https://azure.microsoft.com/services/app-service/), select **New application setting** on the **Settings > Configuration** page.</span></span> <span data-ttu-id="825d6-204">Azure App Service paramètres de l’application sont les suivants :</span><span class="sxs-lookup"><span data-stu-id="825d6-204">Azure App Service application settings are:</span></span>

* <span data-ttu-id="825d6-205">Chiffré au repos et transmis sur un canal chiffré.</span><span class="sxs-lookup"><span data-stu-id="825d6-205">Encrypted at rest and transmitted over an encrypted channel.</span></span>
* <span data-ttu-id="825d6-206">Exposés en tant que variables d’environnement.</span><span class="sxs-lookup"><span data-stu-id="825d6-206">Exposed as environment variables.</span></span>

<span data-ttu-id="825d6-207">Pour plus d’informations, consultez [Azure Apps : remplacer la configuration de l’application à l’aide du portail Azure](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="825d6-207">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="825d6-208">Consultez [préfixes de chaîne de connexion](#constr) pour plus d’informations sur les chaînes de connexion de base de données Azure.</span><span class="sxs-lookup"><span data-stu-id="825d6-208">See [Connection string prefixes](#constr) for information on Azure database connection strings.</span></span>

<a name="clcp"></a>

## <a name="command-line"></a><span data-ttu-id="825d6-209">Ligne de commande</span><span class="sxs-lookup"><span data-stu-id="825d6-209">Command-line</span></span>

<span data-ttu-id="825d6-210">À l’aide de la configuration <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> [par défaut](#default) , le charge la configuration à partir de paires clé-valeur d’argument de ligne de commande après les sources de configuration suivantes :</span><span class="sxs-lookup"><span data-stu-id="825d6-210">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs after the following configuration sources:</span></span>

* <span data-ttu-id="825d6-211">*appSettings. JSON* et *appSettings*. `Environment`. fichiers *JSON* .</span><span class="sxs-lookup"><span data-stu-id="825d6-211">*appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="825d6-212">[Secrets d’application (gestionnaire de secret)](xref:security/app-secrets) dans l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="825d6-212">[App secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="825d6-213">Variables d'environnement.</span><span class="sxs-lookup"><span data-stu-id="825d6-213">Environment variables.</span></span>

<span data-ttu-id="825d6-214">Par [défaut](#default), les valeurs de configuration définies sur la ligne de commande remplacent les valeurs de configuration définies avec tous les autres fournisseurs de configuration.</span><span class="sxs-lookup"><span data-stu-id="825d6-214">By [default](#default), configuration values set on the command-line override configuration values set with all the other configuration providers.</span></span>

### <a name="command-line-arguments"></a><span data-ttu-id="825d6-215">Arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="825d6-215">Command-line arguments</span></span>

<span data-ttu-id="825d6-216">La commande suivante définit des clés et des `=`valeurs à l’aide de :</span><span class="sxs-lookup"><span data-stu-id="825d6-216">The following command sets keys and values using `=`:</span></span>

```dotnetcli
dotnet run MyKey="My key from command line" Position:Title=Cmd Position:Name=Cmd_Rick
```

<span data-ttu-id="825d6-217">La commande suivante définit des clés et des `/`valeurs à l’aide de :</span><span class="sxs-lookup"><span data-stu-id="825d6-217">The following command sets keys and values using `/`:</span></span>

```dotnetcli
dotnet run /MyKey "Using /" /Position:Title=Cmd_ /Position:Name=Cmd_Rick
```

<span data-ttu-id="825d6-218">La commande suivante définit des clés et des `--`valeurs à l’aide de :</span><span class="sxs-lookup"><span data-stu-id="825d6-218">The following command sets keys and values using `--`:</span></span>

```dotnetcli
dotnet run --MyKey "Using --" --Position:Title=Cmd-- --Position:Name=Cmd--Rick
```

<span data-ttu-id="825d6-219">Valeur de la clé :</span><span class="sxs-lookup"><span data-stu-id="825d6-219">The key value:</span></span>

* <span data-ttu-id="825d6-220">Doit suivre `=`ou la clé doit avoir un préfixe `--` ou `/` lorsque la valeur suit un espace.</span><span class="sxs-lookup"><span data-stu-id="825d6-220">Must follow `=`, or the key must have a prefix of `--` or `/` when the value follows a space.</span></span>
* <span data-ttu-id="825d6-221">N’est pas `=` obligatoire si est utilisé.</span><span class="sxs-lookup"><span data-stu-id="825d6-221">Isn't required if `=` is used.</span></span> <span data-ttu-id="825d6-222">Par exemple : `MySetting=`.</span><span class="sxs-lookup"><span data-stu-id="825d6-222">For example, `MySetting=`.</span></span>

<span data-ttu-id="825d6-223">Dans la même commande, ne mélangez pas les paires clé-valeur d’argument de `=` ligne de commande qui utilisent des paires clé-valeur utilisant un espace.</span><span class="sxs-lookup"><span data-stu-id="825d6-223">Within the same command, don't mix command-line argument key-value pairs that use `=` with key-value pairs that use a space.</span></span>

### <a name="switch-mappings"></a><span data-ttu-id="825d6-224">Correspondances de commutateur</span><span class="sxs-lookup"><span data-stu-id="825d6-224">Switch mappings</span></span>

<span data-ttu-id="825d6-225">Les mappages de commutateur autorisent la logique de remplacement de nom de **clé** .</span><span class="sxs-lookup"><span data-stu-id="825d6-225">Switch mappings allow **key** name replacement logic.</span></span> <span data-ttu-id="825d6-226">Fournissez un dictionnaire de remplacements de commutateur dans <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> la méthode.</span><span class="sxs-lookup"><span data-stu-id="825d6-226">Provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="825d6-227">Quand le dictionnaire de correspondances de commutateur est utilisé, il est vérifié afin de déterminer s’il contient une clé correspondant à celle fournie par un argument de ligne de commande.</span><span class="sxs-lookup"><span data-stu-id="825d6-227">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="825d6-228">Si la clé de ligne de commande est trouvée dans le dictionnaire, la valeur du dictionnaire est retournée pour définir la paire clé-valeur dans la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="825d6-228">If the command-line key is found in the dictionary, the dictionary value is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="825d6-229">Une correspondance de commutateur est nécessaire pour chaque clé de ligne de commande préfixée avec un tiret unique (`-`).</span><span class="sxs-lookup"><span data-stu-id="825d6-229">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="825d6-230">Règles des clés du dictionnaire de correspondances de commutateur :</span><span class="sxs-lookup"><span data-stu-id="825d6-230">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="825d6-231">Les commutateurs doivent `-` commencer `--`par ou.</span><span class="sxs-lookup"><span data-stu-id="825d6-231">Switches must start with `-` or `--`.</span></span>
* <span data-ttu-id="825d6-232">Le dictionnaire de correspondances de commutateur ne doit pas contenir de clés en double.</span><span class="sxs-lookup"><span data-stu-id="825d6-232">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="825d6-233">Pour utiliser un dictionnaire de mappages de commutateur, transmettez-le `AddCommandLine`à l’appel à :</span><span class="sxs-lookup"><span data-stu-id="825d6-233">To use a switch mappings dictionary, pass it into the call to `AddCommandLine`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramSwitch.cs?name=snippet&highlight=10-18,23)]

<span data-ttu-id="825d6-234">Le code suivant montre les valeurs de clé pour les clés remplacées :</span><span class="sxs-lookup"><span data-stu-id="825d6-234">The following code shows the key values for the replaced keys:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test3.cshtml.cs?name=snippet)]

<span data-ttu-id="825d6-235">Exécutez la commande suivante pour tester le remplacement de la clé :</span><span class="sxs-lookup"><span data-stu-id="825d6-235">Run the following command to test the key replacement:</span></span>

```dotnetcli
dotnet run -k1=value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="825d6-236">Remarque : actuellement, `=` ne peut pas être utilisé pour définir des valeurs de remplacement de clé `-`avec un seul tiret.</span><span class="sxs-lookup"><span data-stu-id="825d6-236">Note: Currently, `=` cannot be used to set key-replacement values with a single dash `-`.</span></span> <span data-ttu-id="825d6-237">Consultez [ce problème GitHub](https://github.com/dotnet/extensions/issues/3059).</span><span class="sxs-lookup"><span data-stu-id="825d6-237">See [this GitHub issue](https://github.com/dotnet/extensions/issues/3059).</span></span>

<span data-ttu-id="825d6-238">La commande suivante fonctionne pour tester le remplacement de la clé :</span><span class="sxs-lookup"><span data-stu-id="825d6-238">The following command works to test key replacement:</span></span>

```dotnetcli
dotnet run -k1 value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="825d6-239">Pour les applications qui utilisent des mappages de commutateurs, l’appel à `CreateDefaultBuilder` ne doit pas passer d’arguments.</span><span class="sxs-lookup"><span data-stu-id="825d6-239">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="825d6-240">L' `CreateDefaultBuilder` appel de `AddCommandLine` la méthode n’inclut pas de commutateurs mappés, et il n’existe aucun moyen de passer le `CreateDefaultBuilder`dictionnaire de mappage de commutateur à.</span><span class="sxs-lookup"><span data-stu-id="825d6-240">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch-mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="825d6-241">La solution ne consiste pas à passer les `CreateDefaultBuilder` arguments à, mais à `ConfigurationBuilder` autoriser la `AddCommandLine` méthode de la méthode à traiter à la fois les arguments et le dictionnaire de mappage de commutateur.</span><span class="sxs-lookup"><span data-stu-id="825d6-241">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch-mapping dictionary.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="825d6-242">Données de configuration hiérarchiques</span><span class="sxs-lookup"><span data-stu-id="825d6-242">Hierarchical configuration data</span></span>

<span data-ttu-id="825d6-243">L’API de configuration lit les données de configuration hiérarchiques en aplatit les données hiérarchiques à l’aide d’un délimiteur dans les clés de configuration.</span><span class="sxs-lookup"><span data-stu-id="825d6-243">The Configuration API reads hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="825d6-244">L' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contient le fichier *appSettings. JSON* suivant :</span><span class="sxs-lookup"><span data-stu-id="825d6-244">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="825d6-245">Le code suivant de l' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) affiche plusieurs des paramètres de configuration :</span><span class="sxs-lookup"><span data-stu-id="825d6-245">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="825d6-246">La meilleure façon de lire des données de configuration hiérarchiques consiste à utiliser le modèle d’options.</span><span class="sxs-lookup"><span data-stu-id="825d6-246">The preferred way to read hierarchical configuration data is using the options pattern.</span></span> <span data-ttu-id="825d6-247">Pour plus d’informations, consultez [lier des données de configuration hiérarchiques](#optpat) dans ce document.</span><span class="sxs-lookup"><span data-stu-id="825d6-247">For more information, see [Bind hierarchical configuration data](#optpat) in this document.</span></span>

<span data-ttu-id="825d6-248">Les méthodes <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> et <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> sont disponibles pour isoler les sections et les enfants d’une section dans les données de configuration.</span><span class="sxs-lookup"><span data-stu-id="825d6-248"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="825d6-249">Ces méthodes sont décrites plus loin dans [GetSection, GetChildren et Exists](#getsection).</span><span class="sxs-lookup"><span data-stu-id="825d6-249">These methods are described later in [GetSection, GetChildren, and Exists](#getsection).</span></span>

<!--
[Azure Key Vault configuration provider](xref:security/key-vault-configuration) implement change detection.
-->

## <a name="configuration-keys-and-values"></a><span data-ttu-id="825d6-250">Clés et valeurs de configuration</span><span class="sxs-lookup"><span data-stu-id="825d6-250">Configuration keys and values</span></span>

<span data-ttu-id="825d6-251">Clés de configuration :</span><span class="sxs-lookup"><span data-stu-id="825d6-251">Configuration keys:</span></span>

* <span data-ttu-id="825d6-252">Ne respectent pas la casse.</span><span class="sxs-lookup"><span data-stu-id="825d6-252">Are case-insensitive.</span></span> <span data-ttu-id="825d6-253">Par exemple, `ConnectionString` et `connectionstring` sont traités en tant que clés équivalentes.</span><span class="sxs-lookup"><span data-stu-id="825d6-253">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="825d6-254">Si une clé et une valeur sont définies dans plusieurs fournisseurs de configuration, la valeur du dernier fournisseur ajouté est utilisée.</span><span class="sxs-lookup"><span data-stu-id="825d6-254">If a key and value is set in more than one configuration providers, the value from the last provider added is used.</span></span> <span data-ttu-id="825d6-255">Pour plus d’informations, consultez [configuration par défaut](#default).</span><span class="sxs-lookup"><span data-stu-id="825d6-255">For more information, see [Default configuration](#default).</span></span>
* <span data-ttu-id="825d6-256">Clés hiérarchiques</span><span class="sxs-lookup"><span data-stu-id="825d6-256">Hierarchical keys</span></span>
  * <span data-ttu-id="825d6-257">Dans l’API Configuration, un séparateur sous forme de signe deux-points (`:`) fonctionne sur toutes les plateformes.</span><span class="sxs-lookup"><span data-stu-id="825d6-257">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="825d6-258">Dans les variables d’environnement, un séparateur sous forme de signe deux-points peut ne pas fonctionner sur toutes les plateformes.</span><span class="sxs-lookup"><span data-stu-id="825d6-258">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="825d6-259">Un trait de soulignement double, `__`, est pris en charge par toutes les plateformes `:`et est automatiquement converti en deux-points.</span><span class="sxs-lookup"><span data-stu-id="825d6-259">A double underscore, `__`, is supported by all platforms and is automatically converted into a colon `:`.</span></span>
  * <span data-ttu-id="825d6-260">Dans Azure Key Vault, les clés hiérarchiques `--` utilisent comme séparateur.</span><span class="sxs-lookup"><span data-stu-id="825d6-260">In Azure Key Vault, hierarchical keys use `--` as a separator.</span></span> <span data-ttu-id="825d6-261">Le [fournisseur de configuration Azure Key Vault](xref:security/key-vault-configuration) remplace `--` automatiquement par `:` un lorsque les secrets sont chargés dans la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="825d6-261">The [Azure Key Vault configuration provider](xref:security/key-vault-configuration) automatically replaces `--` with a `:` when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="825d6-262"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> prend en charge la liaison de tableaux à des objets à l’aide d’index de tableau dans les clés de configuration.</span><span class="sxs-lookup"><span data-stu-id="825d6-262">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="825d6-263">La liaison de tableau est décrite dans la section [Lier un tableau à une classe](#boa).</span><span class="sxs-lookup"><span data-stu-id="825d6-263">Array binding is described in the [Bind an array to a class](#boa) section.</span></span>

<span data-ttu-id="825d6-264">Valeurs de configuration :</span><span class="sxs-lookup"><span data-stu-id="825d6-264">Configuration values:</span></span>

* <span data-ttu-id="825d6-265">Sont des chaînes.</span><span class="sxs-lookup"><span data-stu-id="825d6-265">Are strings.</span></span>
* <span data-ttu-id="825d6-266">Les valeurs NULL ne peuvent pas être stockées dans la configuration ou liées à des objets.</span><span class="sxs-lookup"><span data-stu-id="825d6-266">Null values can't be stored in configuration or bound to objects.</span></span>

<a name="cp"></a>

## <a name="configuration-providers"></a><span data-ttu-id="825d6-267">Fournisseurs de configuration</span><span class="sxs-lookup"><span data-stu-id="825d6-267">Configuration providers</span></span>

<span data-ttu-id="825d6-268">Le tableau suivant présente les fournisseurs de configuration disponibles pour les applications ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="825d6-268">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="825d6-269">Fournisseur</span><span class="sxs-lookup"><span data-stu-id="825d6-269">Provider</span></span> | <span data-ttu-id="825d6-270">Fournit la configuration à partir de</span><span class="sxs-lookup"><span data-stu-id="825d6-270">Provides configuration from</span></span> |
| -------- | ----------------------------------- |
| [<span data-ttu-id="825d6-271">Fournisseur de configuration Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="825d6-271">Azure Key Vault configuration provider</span></span>](xref:security/key-vault-configuration) | <span data-ttu-id="825d6-272">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="825d6-272">Azure Key Vault</span></span> |
| [<span data-ttu-id="825d6-273">Fournisseur de configuration Azure App</span><span class="sxs-lookup"><span data-stu-id="825d6-273">Azure App configuration provider</span></span>](/azure/azure-app-configuration/quickstart-aspnet-core-app) | <span data-ttu-id="825d6-274">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="825d6-274">Azure App Configuration</span></span> |
| [<span data-ttu-id="825d6-275">Fournisseur de configuration de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="825d6-275">Command-line configuration provider</span></span>](#clcp) | <span data-ttu-id="825d6-276">Paramètres de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="825d6-276">Command-line parameters</span></span> |
| [<span data-ttu-id="825d6-277">Fournisseur de configuration personnalisé</span><span class="sxs-lookup"><span data-stu-id="825d6-277">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="825d6-278">Source personnalisée</span><span class="sxs-lookup"><span data-stu-id="825d6-278">Custom source</span></span> |
| [<span data-ttu-id="825d6-279">Fournisseur de configuration des variables d’environnement</span><span class="sxs-lookup"><span data-stu-id="825d6-279">Environment Variables configuration provider</span></span>](#evcp) | <span data-ttu-id="825d6-280">Variables d'environnement</span><span class="sxs-lookup"><span data-stu-id="825d6-280">Environment variables</span></span> |
| [<span data-ttu-id="825d6-281">Fournisseur de configuration de fichier</span><span class="sxs-lookup"><span data-stu-id="825d6-281">File configuration provider</span></span>](#file-configuration-provider) | <span data-ttu-id="825d6-282">Fichiers INI, JSON et XML</span><span class="sxs-lookup"><span data-stu-id="825d6-282">INI, JSON, and XML files</span></span> |
| [<span data-ttu-id="825d6-283">Fournisseur de configuration de clé par fichier</span><span class="sxs-lookup"><span data-stu-id="825d6-283">Key-per-file configuration provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="825d6-284">Fichiers de répertoire</span><span class="sxs-lookup"><span data-stu-id="825d6-284">Directory files</span></span> |
| [<span data-ttu-id="825d6-285">Fournisseur de configuration de la mémoire</span><span class="sxs-lookup"><span data-stu-id="825d6-285">Memory configuration provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="825d6-286">Collections en mémoire</span><span class="sxs-lookup"><span data-stu-id="825d6-286">In-memory collections</span></span> |
| [<span data-ttu-id="825d6-287">Gestionnaire de secret</span><span class="sxs-lookup"><span data-stu-id="825d6-287">Secret Manager</span></span>](xref:security/app-secrets)  | <span data-ttu-id="825d6-288">Fichier dans le répertoire de profil utilisateur</span><span class="sxs-lookup"><span data-stu-id="825d6-288">File in the user profile directory</span></span> |

<span data-ttu-id="825d6-289">Les sources de configuration sont lues dans l’ordre dans lequel leurs fournisseurs de configuration sont spécifiés.</span><span class="sxs-lookup"><span data-stu-id="825d6-289">Configuration sources are read in the order that their configuration providers are specified.</span></span> <span data-ttu-id="825d6-290">Commandez des fournisseurs de configuration dans le code pour répondre aux priorités des sources de configuration sous-jacentes requises par l’application.</span><span class="sxs-lookup"><span data-stu-id="825d6-290">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="825d6-291">Une séquence type des fournisseurs de configuration est la suivante :</span><span class="sxs-lookup"><span data-stu-id="825d6-291">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="825d6-292">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="825d6-292">*appsettings.json*</span></span>
1. <span data-ttu-id="825d6-293">*appSettings*. `Environment`. *JSON*</span><span class="sxs-lookup"><span data-stu-id="825d6-293">*appsettings*.`Environment`.*json*</span></span>
1. [<span data-ttu-id="825d6-294">Gestionnaire de secret</span><span class="sxs-lookup"><span data-stu-id="825d6-294">Secret Manager</span></span>](xref:security/app-secrets)
1. <span data-ttu-id="825d6-295">Variables d’environnement à l’aide du [fournisseur de configuration des variables d’environnement](#evcp).</span><span class="sxs-lookup"><span data-stu-id="825d6-295">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="825d6-296">Arguments de ligne de commande à l’aide du [fournisseur de configuration de ligne de commande](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="825d6-296">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>

<span data-ttu-id="825d6-297">Une pratique courante consiste à ajouter le dernier fournisseur de configuration de ligne de commande dans une série de fournisseurs pour permettre aux arguments de ligne de commande de remplacer la configuration définie par les autres fournisseurs.</span><span class="sxs-lookup"><span data-stu-id="825d6-297">A common practice is to add the Command-line configuration provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="825d6-298">La séquence de fournisseurs précédente est utilisée dans la [configuration par défaut](#default).</span><span class="sxs-lookup"><span data-stu-id="825d6-298">The preceding sequence of providers is used in the [default configuration](#default).</span></span>

<a name="constr"></a>

### <a name="connection-string-prefixes"></a><span data-ttu-id="825d6-299">Préfixes des chaînes de connexion</span><span class="sxs-lookup"><span data-stu-id="825d6-299">Connection string prefixes</span></span>

<span data-ttu-id="825d6-300">L’API de configuration a des règles de traitement spéciales pour quatre variables d’environnement de chaîne de connexion.</span><span class="sxs-lookup"><span data-stu-id="825d6-300">The Configuration API has special processing rules for four connection string environment variables.</span></span> <span data-ttu-id="825d6-301">Ces chaînes de connexion sont impliquées dans la configuration des chaînes de connexion Azure pour l’environnement de l’application.</span><span class="sxs-lookup"><span data-stu-id="825d6-301">These connection strings are involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="825d6-302">Les variables d’environnement avec les préfixes indiqués dans le tableau sont chargées dans l’application avec la [configuration par défaut](#default) ou lorsqu' `AddEnvironmentVariables`aucun préfixe n’est fourni à.</span><span class="sxs-lookup"><span data-stu-id="825d6-302">Environment variables with the prefixes shown in the table are loaded into the app with the [default configuration](#default) or when no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="825d6-303">Préfixe de la chaîne de connexion</span><span class="sxs-lookup"><span data-stu-id="825d6-303">Connection string prefix</span></span> | <span data-ttu-id="825d6-304">Fournisseur</span><span class="sxs-lookup"><span data-stu-id="825d6-304">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="825d6-305">Fournisseur personnalisé</span><span class="sxs-lookup"><span data-stu-id="825d6-305">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="825d6-306">MySQL</span><span class="sxs-lookup"><span data-stu-id="825d6-306">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="825d6-307">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="825d6-307">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="825d6-308">SQL Server</span><span class="sxs-lookup"><span data-stu-id="825d6-308">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="825d6-309">Quand une variable d’environnement est découverte et chargée dans la configuration avec l’un des quatre préfixes indiqués dans le tableau :</span><span class="sxs-lookup"><span data-stu-id="825d6-309">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="825d6-310">La clé de configuration est créée en supprimant le préfixe de la variable d’environnement et en ajoutant une section de clé de configuration (`ConnectionStrings`).</span><span class="sxs-lookup"><span data-stu-id="825d6-310">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="825d6-311">Une nouvelle paire clé-valeur de configuration est créée qui représente le fournisseur de connexion de base de données (à l’exception de `CUSTOMCONNSTR_`, qui ne possède aucun fournisseur indiqué).</span><span class="sxs-lookup"><span data-stu-id="825d6-311">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="825d6-312">Clé de variable d’environnement</span><span class="sxs-lookup"><span data-stu-id="825d6-312">Environment variable key</span></span> | <span data-ttu-id="825d6-313">Clé de configuration convertie</span><span class="sxs-lookup"><span data-stu-id="825d6-313">Converted configuration key</span></span> | <span data-ttu-id="825d6-314">Entrée de configuration de fournisseur</span><span class="sxs-lookup"><span data-stu-id="825d6-314">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="825d6-315">Entrée de configuration non créée.</span><span class="sxs-lookup"><span data-stu-id="825d6-315">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="825d6-316">Clé : `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="825d6-316">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="825d6-317">Valeur: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="825d6-317">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="825d6-318">Clé : `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="825d6-318">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="825d6-319">Valeur: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="825d6-319">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="825d6-320">Clé : `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="825d6-320">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="825d6-321">Valeur: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="825d6-321">Value: `System.Data.SqlClient`</span></span>  |

<a name="jcp"></a>

### <a name="json-configuration-provider"></a><span data-ttu-id="825d6-322">Fournisseur de configuration JSON</span><span class="sxs-lookup"><span data-stu-id="825d6-322">JSON configuration provider</span></span>

<span data-ttu-id="825d6-323">Le <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> charge la configuration à partir de paires clé-valeur de fichier JSON.</span><span class="sxs-lookup"><span data-stu-id="825d6-323">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs.</span></span>

<span data-ttu-id="825d6-324">Les surcharges peuvent spécifier :</span><span class="sxs-lookup"><span data-stu-id="825d6-324">Overloads can specify:</span></span>

* <span data-ttu-id="825d6-325">Si le fichier est facultatif.</span><span class="sxs-lookup"><span data-stu-id="825d6-325">Whether the file is optional.</span></span>
* <span data-ttu-id="825d6-326">Si la configuration est rechargée quand le fichier est modifié.</span><span class="sxs-lookup"><span data-stu-id="825d6-326">Whether the configuration is reloaded if the file changes.</span></span>

<span data-ttu-id="825d6-327">Examinons le code ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="825d6-327">Consider the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON.cs?name=snippet&highlight=12-14)]

<span data-ttu-id="825d6-328">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="825d6-328">The preceding code:</span></span>

* <span data-ttu-id="825d6-329">Configure le fournisseur de configuration JSON pour charger le fichier *MyConfig. JSON* avec les options suivantes :</span><span class="sxs-lookup"><span data-stu-id="825d6-329">Configures the JSON configuration provider to load the *MyConfig.json* file with the following options:</span></span>
  * <span data-ttu-id="825d6-330">`optional: true`: Le fichier est facultatif.</span><span class="sxs-lookup"><span data-stu-id="825d6-330">`optional: true`: The file is optional.</span></span>
  * <span data-ttu-id="825d6-331">`reloadOnChange: true`: Le fichier est rechargé lorsque des modifications sont enregistrées.</span><span class="sxs-lookup"><span data-stu-id="825d6-331">`reloadOnChange: true` : The file is reloaded when changes are saved.</span></span>
* <span data-ttu-id="825d6-332">Lit les [fournisseurs de configuration par défaut](#default) avant le fichier *MyConfig. JSON* .</span><span class="sxs-lookup"><span data-stu-id="825d6-332">Reads the [default configuration providers](#default) before the *MyConfig.json* file.</span></span> <span data-ttu-id="825d6-333">Paramètres dans le paramètre de remplacement de fichier *MyConfig. JSON* des fournisseurs de configuration par défaut, y compris le [fournisseur de configuration des variables d’environnement](#evcp) et le fournisseur de configuration de ligne de [commande](#clcp).</span><span class="sxs-lookup"><span data-stu-id="825d6-333">Settings in the *MyConfig.json* file override setting in the default configuration providers, including the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="825d6-334">En général, vous ***ne souhaitez pas*** qu’une valeur de substitution de fichier JSON personnalisée soit définie dans le fournisseur de configuration des [variables d’environnement](#evcp) et dans le fournisseur de configuration de [ligne de commande](#clcp).</span><span class="sxs-lookup"><span data-stu-id="825d6-334">You typically ***don't*** want a custom JSON file overriding values set in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="825d6-335">Le code suivant efface tous les fournisseurs de configuration et ajoute plusieurs fournisseurs de configuration :</span><span class="sxs-lookup"><span data-stu-id="825d6-335">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON2.cs?name=snippet)]

<span data-ttu-id="825d6-336">Dans le code précédent, les paramètres dans *MyConfig. JSON* et *MyConfig*. `Environment`. fichiers *JSON* :</span><span class="sxs-lookup"><span data-stu-id="825d6-336">In the preceding code, settings in the *MyConfig.json* and  *MyConfig*.`Environment`.*json* files:</span></span>

* <span data-ttu-id="825d6-337">Substituez les paramètres dans *appSettings. JSON* et *appSettings*. `Environment`. fichiers *JSON* .</span><span class="sxs-lookup"><span data-stu-id="825d6-337">Override settings in the *appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="825d6-338">Sont remplacées par les paramètres dans le [fournisseur de configuration des variables d’environnement](#evcp) et le fournisseur de configuration de ligne de [commande](#clcp).</span><span class="sxs-lookup"><span data-stu-id="825d6-338">Are overridden by settings in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="825d6-339">L' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contient le fichier *MyConfig. JSON* suivant :</span><span class="sxs-lookup"><span data-stu-id="825d6-339">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *MyConfig.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyConfig.json)]

<span data-ttu-id="825d6-340">Le code suivant de l' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) affiche plusieurs des paramètres de configuration précédents :</span><span class="sxs-lookup"><span data-stu-id="825d6-340">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<a name="fcp"></a>

## <a name="file-configuration-provider"></a><span data-ttu-id="825d6-341">Fournisseur de configuration de fichier</span><span class="sxs-lookup"><span data-stu-id="825d6-341">File configuration provider</span></span>

<span data-ttu-id="825d6-342"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> est la classe de base pour charger la configuration à partir du système de fichiers.</span><span class="sxs-lookup"><span data-stu-id="825d6-342"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="825d6-343">Les fournisseurs de configuration suivants dérivent de `FileConfigurationProvider`:</span><span class="sxs-lookup"><span data-stu-id="825d6-343">The following configuration providers derive from `FileConfigurationProvider`:</span></span>

* [<span data-ttu-id="825d6-344">Fournisseur de configuration INI</span><span class="sxs-lookup"><span data-stu-id="825d6-344">INI configuration provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="825d6-345">Fournisseur de configuration JSON</span><span class="sxs-lookup"><span data-stu-id="825d6-345">JSON configuration provider</span></span>](#jcp)
* [<span data-ttu-id="825d6-346">Fournisseur de configuration XML</span><span class="sxs-lookup"><span data-stu-id="825d6-346">XML configuration provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="825d6-347">Fournisseur de configuration INI</span><span class="sxs-lookup"><span data-stu-id="825d6-347">INI configuration provider</span></span>

<span data-ttu-id="825d6-348"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> charge la configuration à partir des paires clé-valeur du fichier INI lors de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="825d6-348">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="825d6-349">Le code suivant efface tous les fournisseurs de configuration et ajoute plusieurs fournisseurs de configuration :</span><span class="sxs-lookup"><span data-stu-id="825d6-349">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramINI.cs?name=snippet&highlight=10-30)]

<span data-ttu-id="825d6-350">Dans le code précédent, les paramètres dans *MyIniConfig. ini* et *MyIniConfig*. `Environment`. les fichiers *ini* sont remplacés par les paramètres dans le :</span><span class="sxs-lookup"><span data-stu-id="825d6-350">In the preceding code, settings in the *MyIniConfig.ini* and  *MyIniConfig*.`Environment`.*ini* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="825d6-351">Fournisseur de configuration des variables d’environnement</span><span class="sxs-lookup"><span data-stu-id="825d6-351">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="825d6-352">[Fournisseur de configuration de ligne de commande](#clcp).</span><span class="sxs-lookup"><span data-stu-id="825d6-352">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="825d6-353">L' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contient le fichier *MyIniConfig. ini* suivant :</span><span class="sxs-lookup"><span data-stu-id="825d6-353">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyIniConfig.ini* file:</span></span>

[!code-ini[](index/samples/3.x/ConfigSample/MyIniConfig.ini)]

<span data-ttu-id="825d6-354">Le code suivant de l' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) affiche plusieurs des paramètres de configuration précédents :</span><span class="sxs-lookup"><span data-stu-id="825d6-354">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

### <a name="xml-configuration-provider"></a><span data-ttu-id="825d6-355">Fournisseur de configuration XML</span><span class="sxs-lookup"><span data-stu-id="825d6-355">XML configuration provider</span></span>

<span data-ttu-id="825d6-356"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> charge la configuration à partir des paires clé-valeur du fichier XML lors de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="825d6-356">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="825d6-357">Le code suivant efface tous les fournisseurs de configuration et ajoute plusieurs fournisseurs de configuration :</span><span class="sxs-lookup"><span data-stu-id="825d6-357">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramXML.cs?name=snippet)]

<span data-ttu-id="825d6-358">Dans le code précédent, les paramètres dans *MyXMLFile. xml* et *MyXMLFile*. `Environment`. les fichiers *XML* sont remplacés par les paramètres dans le :</span><span class="sxs-lookup"><span data-stu-id="825d6-358">In the preceding code, settings in the *MyXMLFile.xml* and  *MyXMLFile*.`Environment`.*xml* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="825d6-359">Fournisseur de configuration des variables d’environnement</span><span class="sxs-lookup"><span data-stu-id="825d6-359">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="825d6-360">[Fournisseur de configuration de ligne de commande](#clcp).</span><span class="sxs-lookup"><span data-stu-id="825d6-360">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="825d6-361">L' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contient le fichier *MyXMLFile. xml* suivant :</span><span class="sxs-lookup"><span data-stu-id="825d6-361">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyXMLFile.xml* file:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile.xml)]

<span data-ttu-id="825d6-362">Le code suivant de l' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) affiche plusieurs des paramètres de configuration précédents :</span><span class="sxs-lookup"><span data-stu-id="825d6-362">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="825d6-363">Les éléments répétitifs qui utilisent le même nom d’élément fonctionnent si l’attribut `name` est utilisé pour distinguer les éléments :</span><span class="sxs-lookup"><span data-stu-id="825d6-363">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile3.xml)]

<span data-ttu-id="825d6-364">Le code suivant lit le fichier de configuration précédent et affiche les clés et les valeurs :</span><span class="sxs-lookup"><span data-stu-id="825d6-364">The following code reads the previous configuration file and displays the keys and values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/XML/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="825d6-365">Les attributs peuvent être utilisés pour fournir des valeurs :</span><span class="sxs-lookup"><span data-stu-id="825d6-365">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="825d6-366">Le fichier de configuration précédent charge les clés suivantes avec `value` :</span><span class="sxs-lookup"><span data-stu-id="825d6-366">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="825d6-367">key:attribute</span><span class="sxs-lookup"><span data-stu-id="825d6-367">key:attribute</span></span>
* <span data-ttu-id="825d6-368">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="825d6-368">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="825d6-369">Fournisseur de configuration de clé par fichier</span><span class="sxs-lookup"><span data-stu-id="825d6-369">Key-per-file configuration provider</span></span>

<span data-ttu-id="825d6-370">Le <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> utilise les fichiers d’un répertoire en tant que paires clé-valeur de configuration.</span><span class="sxs-lookup"><span data-stu-id="825d6-370">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="825d6-371">La clé est le nom de fichier.</span><span class="sxs-lookup"><span data-stu-id="825d6-371">The key is the file name.</span></span> <span data-ttu-id="825d6-372">La valeur contient le contenu du fichier.</span><span class="sxs-lookup"><span data-stu-id="825d6-372">The value contains the file's contents.</span></span> <span data-ttu-id="825d6-373">Le fournisseur de configuration par fichier clé est utilisé dans les scénarios d’hébergement de l’ancrage.</span><span class="sxs-lookup"><span data-stu-id="825d6-373">The Key-per-file configuration provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="825d6-374">Pour activer la configuration clé par fichier, appelez la méthode d’extension <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> sur une instance de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="825d6-374">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="825d6-375">Le `directoryPath` aux fichiers doit être un chemin d’accès absolu.</span><span class="sxs-lookup"><span data-stu-id="825d6-375">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="825d6-376">Les surcharges permettent de spécifier :</span><span class="sxs-lookup"><span data-stu-id="825d6-376">Overloads permit specifying:</span></span>

* <span data-ttu-id="825d6-377">Un délégué `Action<KeyPerFileConfigurationSource>` qui configure la source.</span><span class="sxs-lookup"><span data-stu-id="825d6-377">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="825d6-378">Si le répertoire est facultatif et le chemin d’accès au répertoire.</span><span class="sxs-lookup"><span data-stu-id="825d6-378">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="825d6-379">Le double trait de soulignement (`__`) est utilisé comme un délimiteur de clé de configuration dans les noms de fichiers.</span><span class="sxs-lookup"><span data-stu-id="825d6-379">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="825d6-380">Par exemple, le nom de fichier `Logging__LogLevel__System` génère la clé de configuration `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="825d6-380">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="825d6-381">Appelez `ConfigureAppConfiguration` lors de la création de l’hôte pour spécifier la configuration de l’application :</span><span class="sxs-lookup"><span data-stu-id="825d6-381">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<a name="mcp"></a>

## <a name="memory-configuration-provider"></a><span data-ttu-id="825d6-382">Fournisseur de configuration de la mémoire</span><span class="sxs-lookup"><span data-stu-id="825d6-382">Memory configuration provider</span></span>

<span data-ttu-id="825d6-383">Le <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> utilise une collection en mémoire en tant que paires clé-valeur de configuration.</span><span class="sxs-lookup"><span data-stu-id="825d6-383">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="825d6-384">Le code suivant ajoute une collection de mémoire au système de configuration :</span><span class="sxs-lookup"><span data-stu-id="825d6-384">The following code adds a memory collection to the configuration system:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet6)]

<span data-ttu-id="825d6-385">Le code suivant de l' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) affiche les paramètres de configuration précédents :</span><span class="sxs-lookup"><span data-stu-id="825d6-385">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="825d6-386">Dans le code précédent, `config.AddInMemoryCollection(Dict)` est ajouté après les [fournisseurs de configuration par défaut](#default).</span><span class="sxs-lookup"><span data-stu-id="825d6-386">In the preceding code, `config.AddInMemoryCollection(Dict)` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="825d6-387">Pour obtenir un exemple de classement des fournisseurs de configuration, consultez [fournisseur de configuration JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="825d6-387">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="825d6-388">Pour un autre exemple, consultez [lier un tableau](#boa) à l’aide `MemoryConfigurationProvider`de.</span><span class="sxs-lookup"><span data-stu-id="825d6-388">See [Bind an array](#boa) for another example using `MemoryConfigurationProvider`.</span></span>

## <a name="getvalue"></a><span data-ttu-id="825d6-389">GetValue</span><span class="sxs-lookup"><span data-stu-id="825d6-389">GetValue</span></span>

<span data-ttu-id="825d6-390">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*)extrait une valeur unique de la configuration avec une clé spécifiée et la convertit en type spécifié :</span><span class="sxs-lookup"><span data-stu-id="825d6-390">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified type:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestNum.cshtml.cs?name=snippet)]

<span data-ttu-id="825d6-391">Dans le code précédent, si `NumberKey` est introuvable dans la configuration, la valeur `99` par défaut de est utilisée.</span><span class="sxs-lookup"><span data-stu-id="825d6-391">In the preceding code,  if `NumberKey` isn't found in the configuration, the default value of `99` is used.</span></span>

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="825d6-392">GetSection, GetChildren et Exists</span><span class="sxs-lookup"><span data-stu-id="825d6-392">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="825d6-393">Pour les exemples qui suivent, examinez le fichier *MySubsection. JSON* suivant :</span><span class="sxs-lookup"><span data-stu-id="825d6-393">For the examples that follow, consider the following *MySubsection.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MySubsection.json)]

<span data-ttu-id="825d6-394">Le code suivant ajoute *MySubsection. JSON* aux fournisseurs de configuration :</span><span class="sxs-lookup"><span data-stu-id="825d6-394">The following code adds *MySubsection.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONsection.cs?name=snippet)]

### <a name="getsection"></a><span data-ttu-id="825d6-395">GetSection</span><span class="sxs-lookup"><span data-stu-id="825d6-395">GetSection</span></span>

<span data-ttu-id="825d6-396">[IConfiguration. GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) retourne une sous-section de configuration avec la clé de sous-section spécifiée.</span><span class="sxs-lookup"><span data-stu-id="825d6-396">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) returns a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="825d6-397">Le code suivant retourne des valeurs `section1`pour :</span><span class="sxs-lookup"><span data-stu-id="825d6-397">The following code returns values for `section1`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection.cshtml.cs?name=snippet)]

<span data-ttu-id="825d6-398">Le code suivant retourne des valeurs `section2:subsection0`pour :</span><span class="sxs-lookup"><span data-stu-id="825d6-398">The following code returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection2.cshtml.cs?name=snippet)]

<span data-ttu-id="825d6-399">`GetSection` ne retourne jamais `null`.</span><span class="sxs-lookup"><span data-stu-id="825d6-399">`GetSection` never returns `null`.</span></span> <span data-ttu-id="825d6-400">Si aucune section correspondante n’est trouvée, une valeur `IConfigurationSection` vide est retournée.</span><span class="sxs-lookup"><span data-stu-id="825d6-400">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="825d6-401">Quand `GetSection` retourne une section correspondante, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> n’est pas rempli.</span><span class="sxs-lookup"><span data-stu-id="825d6-401">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="825d6-402"><xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> et <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> sont retournés quand la section existe.</span><span class="sxs-lookup"><span data-stu-id="825d6-402">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren-and-exists"></a><span data-ttu-id="825d6-403">GetChildren et EXISTS</span><span class="sxs-lookup"><span data-stu-id="825d6-403">GetChildren and Exists</span></span>

<span data-ttu-id="825d6-404">Le code suivant appelle [IConfiguration. GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) et retourne des valeurs `section2:subsection0`pour :</span><span class="sxs-lookup"><span data-stu-id="825d6-404">The following code calls [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) and returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection4.cshtml.cs?name=snippet)]

<span data-ttu-id="825d6-405">Le code précédent appelle [ConfigurationExtensions. Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) pour vérifier l’existence de la section :</span><span class="sxs-lookup"><span data-stu-id="825d6-405">The preceding code calls [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to verify the  section exists:</span></span>

 <a name="boa"></a>

## <a name="bind-an-array"></a><span data-ttu-id="825d6-406">Lier un tableau</span><span class="sxs-lookup"><span data-stu-id="825d6-406">Bind an array</span></span>

<span data-ttu-id="825d6-407">[ConfigurationBinder. bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) prend en charge les tableaux de liaison aux objets à l’aide d’index de tableau dans les clés de configuration.</span><span class="sxs-lookup"><span data-stu-id="825d6-407">The [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="825d6-408">Tout format de tableau qui expose un segment de clé numérique est capable d’effectuer une liaison de tableau à un tableau de classes [poco](https://wikipedia.org/wiki/Plain_Old_CLR_Object) .</span><span class="sxs-lookup"><span data-stu-id="825d6-408">Any array format that exposes a numeric key segment is capable of array binding to a [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) class array.</span></span>

<span data-ttu-id="825d6-409">Examinez *myArray. JSON* de l' [exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span><span class="sxs-lookup"><span data-stu-id="825d6-409">Consider *MyArray.json* from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyArray.json)]

<span data-ttu-id="825d6-410">Le code suivant ajoute *myArray. JSON* aux fournisseurs de configuration :</span><span class="sxs-lookup"><span data-stu-id="825d6-410">The following code adds *MyArray.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONarray.cs?name=snippet)]

<span data-ttu-id="825d6-411">Le code suivant lit la configuration et affiche les valeurs :</span><span class="sxs-lookup"><span data-stu-id="825d6-411">The following code reads the configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="825d6-412">Le code précédent retourne la sortie suivante :</span><span class="sxs-lookup"><span data-stu-id="825d6-412">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value00
Index: 1  Value: value10
Index: 2  Value: value20
Index: 3  Value: value40
Index: 4  Value: value50
```

<span data-ttu-id="825d6-413">Dans la sortie précédente, l’index 3 a `value40`la valeur, `"4": "value40",` qui correspond à dans *myArray. JSON*.</span><span class="sxs-lookup"><span data-stu-id="825d6-413">In the preceding output, Index 3 has value `value40`, corresponding to `"4": "value40",` in *MyArray.json*.</span></span> <span data-ttu-id="825d6-414">Les index de tableau liés sont continus et non liés à l’index de clé de configuration.</span><span class="sxs-lookup"><span data-stu-id="825d6-414">The bound array indices are continuous and not bound to the configuration key index.</span></span> <span data-ttu-id="825d6-415">Le Binder de configuration n’est pas en capacité à lier des valeurs null ou à créer des entrées NULL dans des objets liés</span><span class="sxs-lookup"><span data-stu-id="825d6-415">The configuration binder isn't capable of binding null values or creating null entries in bound objects</span></span>

<span data-ttu-id="825d6-416">Le code suivant charge la `array:entries` configuration avec la <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> méthode d’extension :</span><span class="sxs-lookup"><span data-stu-id="825d6-416">The  following code loads the `array:entries` configuration with the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet)]

<span data-ttu-id="825d6-417">Le code suivant lit la configuration dans `arrayDict` `Dictionary` et affiche les valeurs :</span><span class="sxs-lookup"><span data-stu-id="825d6-417">The following code reads the configuration in the `arrayDict` `Dictionary` and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="825d6-418">Le code précédent retourne la sortie suivante :</span><span class="sxs-lookup"><span data-stu-id="825d6-418">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value4
Index: 4  Value: value5
```

<span data-ttu-id="825d6-419">L’index &num;3 dans l’objet lié contient les données de configuration pour la clé de configuration `array:4` et sa valeur de `value4`.</span><span class="sxs-lookup"><span data-stu-id="825d6-419">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="825d6-420">Lorsque les données de configuration contenant un tableau sont liées, les index de tableau dans les clés de configuration sont utilisés pour itérer les données de configuration lors de la création de l’objet.</span><span class="sxs-lookup"><span data-stu-id="825d6-420">When configuration data containing an array is bound, the array indices in the configuration keys are used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="825d6-421">Une valeur null ne peut pas être conservée dans des données de configuration, et une entrée à valeur null n’est pas créée dans un objet lié quand un tableau dans des clés de configuration ignore un ou plusieurs index.</span><span class="sxs-lookup"><span data-stu-id="825d6-421">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="825d6-422">L’élément de configuration manquant pour &num;l’index 3 peut être fourni avant la `ArrayExample` liaison à l’instance par n’importe quel fournisseur &num;de configuration qui lit la paire clé/valeur de l’index 3.</span><span class="sxs-lookup"><span data-stu-id="825d6-422">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that reads the index &num;3 key/value pair.</span></span> <span data-ttu-id="825d6-423">Considérez le fichier *valeur3. JSON* suivant dans l’exemple de téléchargement :</span><span class="sxs-lookup"><span data-stu-id="825d6-423">Consider the following *Value3.json* file from the sample download:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/Value3.json)]

<span data-ttu-id="825d6-424">Le code suivant comprend la configuration de *valeur3. JSON* et `arrayDict` `Dictionary`:</span><span class="sxs-lookup"><span data-stu-id="825d6-424">The following code includes configuration for *Value3.json* and the `arrayDict` `Dictionary`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet2)]

<span data-ttu-id="825d6-425">Le code suivant lit la configuration précédente et affiche les valeurs :</span><span class="sxs-lookup"><span data-stu-id="825d6-425">The following code reads the preceding configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="825d6-426">Le code précédent retourne la sortie suivante :</span><span class="sxs-lookup"><span data-stu-id="825d6-426">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value3
Index: 4  Value: value4
Index: 5  Value: value5
```

<span data-ttu-id="825d6-427">Les fournisseurs de configuration personnalisés ne sont pas obligés d’implémenter la liaison de tableau.</span><span class="sxs-lookup"><span data-stu-id="825d6-427">Custom configuration providers aren't required to implement array binding.</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="825d6-428">Fournisseur de configuration personnalisé</span><span class="sxs-lookup"><span data-stu-id="825d6-428">Custom configuration provider</span></span>

<span data-ttu-id="825d6-429">L’exemple d’application montre comment créer un fournisseur de configuration de base qui lit les paires clé-valeur de configuration à partir d’une base de données à l’aide [d’Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="825d6-429">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="825d6-430">Le fournisseur présente les caractéristiques suivantes :</span><span class="sxs-lookup"><span data-stu-id="825d6-430">The provider has the following characteristics:</span></span>

* <span data-ttu-id="825d6-431">La base de données en mémoire EF est utilisée à des fins de démonstration.</span><span class="sxs-lookup"><span data-stu-id="825d6-431">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="825d6-432">Pour utiliser une base de données qui nécessite une chaîne de connexion, implémentez un autre `ConfigurationBuilder` pour fournir la chaîne de connexion à partir d’un autre fournisseur de configuration.</span><span class="sxs-lookup"><span data-stu-id="825d6-432">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="825d6-433">Le fournisseur lit une table de base de données dans la configuration au démarrage.</span><span class="sxs-lookup"><span data-stu-id="825d6-433">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="825d6-434">Le fournisseur n’interroge pas la base de données par clé.</span><span class="sxs-lookup"><span data-stu-id="825d6-434">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="825d6-435">Le rechargement en cas de changement n’est pas implémenté, par conséquent, la mise à jour la base de données après le démarrage de l’application n’a aucun effet sur la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="825d6-435">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="825d6-436">Définissez une entité `EFConfigurationValue` pour le stockage des valeurs de configuration dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="825d6-436">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="825d6-437">*Models/EFConfigurationValue.cs* :</span><span class="sxs-lookup"><span data-stu-id="825d6-437">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="825d6-438">Ajoutez un `EFConfigurationContext` pour stocker les valeurs configurées et y accéder.</span><span class="sxs-lookup"><span data-stu-id="825d6-438">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="825d6-439">*EFConfigurationProvider/EFConfigurationContext.cs* :</span><span class="sxs-lookup"><span data-stu-id="825d6-439">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="825d6-440">Créez une classe qui implémente <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="825d6-440">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="825d6-441">*EFConfigurationProvider/EFConfigurationSource.cs* :</span><span class="sxs-lookup"><span data-stu-id="825d6-441">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="825d6-442">Créez le fournisseur de configuration personnalisé en héritant de <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="825d6-442">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="825d6-443">Le fournisseur de configuration initialise la base de données quand elle est vide.</span><span class="sxs-lookup"><span data-stu-id="825d6-443">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="825d6-444">Étant donné que les [clés de configuration ne](#keys)respectent pas la casse, le dictionnaire utilisé pour initialiser la base de données est créé avec le comparateur ne respectant pas la casse ([StringComparer. OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span><span class="sxs-lookup"><span data-stu-id="825d6-444">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="825d6-445">*EFConfigurationProvider/EFConfigurationProvider.cs* :</span><span class="sxs-lookup"><span data-stu-id="825d6-445">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="825d6-446">Une méthode d’extension `AddEFConfiguration` permet d’ajouter la source de configuration à un `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="825d6-446">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="825d6-447">*Extensions/EntityFrameworkExtensions.cs* :</span><span class="sxs-lookup"><span data-stu-id="825d6-447">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="825d6-448">Le code suivant montre comment utiliser le `EFConfigurationProvider` personnalisé dans *Program.cs* :</span><span class="sxs-lookup"><span data-stu-id="825d6-448">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

<a name="acs"></a>

## <a name="access-configuration-in-startup"></a><span data-ttu-id="825d6-449">Configuration de l’accès au démarrage</span><span class="sxs-lookup"><span data-stu-id="825d6-449">Access configuration in Startup</span></span>

<span data-ttu-id="825d6-450">Le code suivant affiche les données de `Startup` configuration dans les méthodes :</span><span class="sxs-lookup"><span data-stu-id="825d6-450">The following code displays configuration data in `Startup` methods:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/StartupKey.cs?name=snippet&highlight=13,18)]

<span data-ttu-id="825d6-451">Pour obtenir un exemple d’accès à la configuration à l’aide des méthodes pratiques de démarrage, consultez [Démarrage de l’application : méthodes pratiques](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="825d6-451">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-razor-pages"></a><span data-ttu-id="825d6-452">Configuration de l’accès dans Razor Pages</span><span class="sxs-lookup"><span data-stu-id="825d6-452">Access configuration in Razor Pages</span></span>

<span data-ttu-id="825d6-453">Le code suivant affiche les données de configuration dans une page Razor :</span><span class="sxs-lookup"><span data-stu-id="825d6-453">The following code displays configuration data in a Razor Page:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Pages/Test5.cshtml)]

## <a name="access-configuration-in-a-mvc-view-file"></a><span data-ttu-id="825d6-454">Configuration de l’accès dans un fichier de vue MVC</span><span class="sxs-lookup"><span data-stu-id="825d6-454">Access configuration in a MVC view file</span></span>

<span data-ttu-id="825d6-455">Le code suivant affiche les données de configuration dans une vue MVC :</span><span class="sxs-lookup"><span data-stu-id="825d6-455">The following code displays configuration data in a MVC view:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Views/Home2/Index.cshtml)]

<a name="hvac"></a>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="825d6-456">Configuration de l’hôte ou configuration de l’application</span><span class="sxs-lookup"><span data-stu-id="825d6-456">Host versus app configuration</span></span>

<span data-ttu-id="825d6-457">Avant que l’application ne soit configurée et démarrée, un *hôte* est configuré et lancé.</span><span class="sxs-lookup"><span data-stu-id="825d6-457">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="825d6-458">L’hôte est responsable de la gestion du démarrage et de la durée de vie des applications.</span><span class="sxs-lookup"><span data-stu-id="825d6-458">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="825d6-459">L’application et l’hôte sont configurés à l’aide des fournisseurs de configuration décrits dans cette rubrique.</span><span class="sxs-lookup"><span data-stu-id="825d6-459">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="825d6-460">Les paires clé-valeur de la configuration de l’hôte sont également incluses dans la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="825d6-460">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="825d6-461">Pour plus d’informations sur l’utilisation des fournisseurs de configuration lors de la génération de l’hôte et l’impact des sources de configuration sur la configuration de l’hôte, consultez <xref:fundamentals/index#host>.</span><span class="sxs-lookup"><span data-stu-id="825d6-461">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

<a name="dhc"></a>

## <a name="default-host-configuration"></a><span data-ttu-id="825d6-462">Configuration de l’hôte par défaut</span><span class="sxs-lookup"><span data-stu-id="825d6-462">Default host configuration</span></span>

<span data-ttu-id="825d6-463">Pour plus de détails sur la configuration par défaut lors de l’utilisation de l’[hôte Web](xref:fundamentals/host/web-host), consultez la [version ASP.NET Core 2.2. de cette rubrique](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="825d6-463">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="825d6-464">La configuration de l’hôte est fournie à partir des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="825d6-464">Host configuration is provided from:</span></span>
  * <span data-ttu-id="825d6-465">Les variables d' `DOTNET_` environnement précédées du préfixe (par exemple, `DOTNET_ENVIRONMENT`) à l’aide du fournisseur de configuration des variables d' [environnement](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="825d6-465">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables configuration provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="825d6-466">Le préfixe (`DOTNET_`) est supprimé lorsque les paires clé-valeur de la configuration sont chargées.</span><span class="sxs-lookup"><span data-stu-id="825d6-466">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="825d6-467">Arguments de ligne de commande à l’aide du [fournisseur de configuration de ligne de commande](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="825d6-467">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="825d6-468">La configuration par défaut de l’hôte Web est établie (`ConfigureWebHostDefaults`) :</span><span class="sxs-lookup"><span data-stu-id="825d6-468">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="825d6-469">Kestrel est utilisé comme serveur web et configuré à l’aide des fournisseurs de configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="825d6-469">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="825d6-470">Ajoutez l’intergiciel de filtrage d’hôtes.</span><span class="sxs-lookup"><span data-stu-id="825d6-470">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="825d6-471">Ajoutez l’intergiciel d’en-têtes transférés si la variable d'environnement `ASPNETCORE_FORWARDEDHEADERS_ENABLED` est définie sur `true`.</span><span class="sxs-lookup"><span data-stu-id="825d6-471">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="825d6-472">Activez l’intégration d’IIS.</span><span class="sxs-lookup"><span data-stu-id="825d6-472">Enable IIS integration.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="825d6-473">Autre configuration</span><span class="sxs-lookup"><span data-stu-id="825d6-473">Other configuration</span></span>

<span data-ttu-id="825d6-474">Cette rubrique se rapporte uniquement à la configuration de l' *application*.</span><span class="sxs-lookup"><span data-stu-id="825d6-474">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="825d6-475">D’autres aspects de l’exécution et de l’hébergement des applications ASP.NET Core sont configurés à l’aide des fichiers de configuration non traités dans cette rubrique :</span><span class="sxs-lookup"><span data-stu-id="825d6-475">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="825d6-476">*Launch. JSON*/*launchSettings. JSON* sont des fichiers de configuration d’outils pour l’environnement de développement, décrits ci-après :</span><span class="sxs-lookup"><span data-stu-id="825d6-476">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="825d6-477">Dans <xref:fundamentals/environments#development>.</span><span class="sxs-lookup"><span data-stu-id="825d6-477">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="825d6-478">Dans l’ensemble de la documentation dans lequel les fichiers sont utilisés pour configurer des applications ASP.NET Core pour les scénarios de développement.</span><span class="sxs-lookup"><span data-stu-id="825d6-478">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="825d6-479">*Web. config* est un fichier de configuration de serveur, décrit dans les rubriques suivantes :</span><span class="sxs-lookup"><span data-stu-id="825d6-479">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="825d6-480">Pour plus d’informations sur la migration de la configuration d’application à partir <xref:migration/proper-to-2x/index#store-configurations>de versions antérieures de ASP.net, consultez.</span><span class="sxs-lookup"><span data-stu-id="825d6-480">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="825d6-481">Ajouter la configuration à partir d’un assembly externe</span><span class="sxs-lookup"><span data-stu-id="825d6-481">Add configuration from an external assembly</span></span>

<span data-ttu-id="825d6-482">Une implémentation de <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> permet d’ajouter des améliorations à une application au démarrage à partir d’un assembly externe, en dehors de la classe `Startup` de l’application.</span><span class="sxs-lookup"><span data-stu-id="825d6-482">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="825d6-483">Pour plus d’informations, consultez <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="825d6-483">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="825d6-484">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="825d6-484">Additional resources</span></span>

* [<span data-ttu-id="825d6-485">Code source de configuration</span><span class="sxs-lookup"><span data-stu-id="825d6-485">Configuration source code</span></span>](https://github.com/dotnet/extensions/tree/master/src/Configuration)
* <xref:fundamentals/configuration/options>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="825d6-486">La configuration d’application dans ASP.NET Core est basée sur des paires clé-valeur établies par les *fournisseurs de configuration*.</span><span class="sxs-lookup"><span data-stu-id="825d6-486">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="825d6-487">Les fournisseurs de configuration lisent les données de configuration dans les paires clé-valeur à partir de diverses sources de configuration :</span><span class="sxs-lookup"><span data-stu-id="825d6-487">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="825d6-488">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="825d6-488">Azure Key Vault</span></span>
* <span data-ttu-id="825d6-489">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="825d6-489">Azure App Configuration</span></span>
* <span data-ttu-id="825d6-490">Arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="825d6-490">Command-line arguments</span></span>
* <span data-ttu-id="825d6-491">Fournisseurs personnalisés (installés ou créés)</span><span class="sxs-lookup"><span data-stu-id="825d6-491">Custom providers (installed or created)</span></span>
* <span data-ttu-id="825d6-492">Fichiers de répertoire</span><span class="sxs-lookup"><span data-stu-id="825d6-492">Directory files</span></span>
* <span data-ttu-id="825d6-493">Variables d'environnement</span><span class="sxs-lookup"><span data-stu-id="825d6-493">Environment variables</span></span>
* <span data-ttu-id="825d6-494">Objets .NET en mémoire</span><span class="sxs-lookup"><span data-stu-id="825d6-494">In-memory .NET objects</span></span>
* <span data-ttu-id="825d6-495">Fichiers de paramètres</span><span class="sxs-lookup"><span data-stu-id="825d6-495">Settings files</span></span>

<span data-ttu-id="825d6-496">Les packages de configuration pour les scénarios de fournisseur de configuration courants ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) sont inclus implicitement dans le [métapaquet Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="825d6-496">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="825d6-497">Les exemples de code qui suivent et dans l’échantillon d’application utilisent l’espace de noms <xref:Microsoft.Extensions.Configuration> :</span><span class="sxs-lookup"><span data-stu-id="825d6-497">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="825d6-498">Le *modèle d’options* est une extension des concepts de configuration décrits dans cette rubrique.</span><span class="sxs-lookup"><span data-stu-id="825d6-498">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="825d6-499">Les options utilisent des classes pour représenter les groupes de paramètres associés.</span><span class="sxs-lookup"><span data-stu-id="825d6-499">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="825d6-500">Pour plus d’informations, consultez <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="825d6-500">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="825d6-501">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="825d6-501">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="825d6-502">Configuration de l’hôte ou configuration de l’application</span><span class="sxs-lookup"><span data-stu-id="825d6-502">Host versus app configuration</span></span>

<span data-ttu-id="825d6-503">Avant que l’application ne soit configurée et démarrée, un *hôte* est configuré et lancé.</span><span class="sxs-lookup"><span data-stu-id="825d6-503">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="825d6-504">L’hôte est responsable de la gestion du démarrage et de la durée de vie des applications.</span><span class="sxs-lookup"><span data-stu-id="825d6-504">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="825d6-505">L’application et l’hôte sont configurés à l’aide des fournisseurs de configuration décrits dans cette rubrique.</span><span class="sxs-lookup"><span data-stu-id="825d6-505">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="825d6-506">Les paires clé-valeur de la configuration de l’hôte sont également incluses dans la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="825d6-506">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="825d6-507">Pour plus d’informations sur l’utilisation des fournisseurs de configuration lors de la génération de l’hôte et l’impact des sources de configuration sur la configuration de l’hôte, consultez <xref:fundamentals/index#host>.</span><span class="sxs-lookup"><span data-stu-id="825d6-507">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="825d6-508">Autre configuration</span><span class="sxs-lookup"><span data-stu-id="825d6-508">Other configuration</span></span>

<span data-ttu-id="825d6-509">Cette rubrique se rapporte uniquement à la configuration de l' *application*.</span><span class="sxs-lookup"><span data-stu-id="825d6-509">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="825d6-510">D’autres aspects de l’exécution et de l’hébergement des applications ASP.NET Core sont configurés à l’aide des fichiers de configuration non traités dans cette rubrique :</span><span class="sxs-lookup"><span data-stu-id="825d6-510">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="825d6-511">*Launch. JSON*/*launchSettings. JSON* sont des fichiers de configuration d’outils pour l’environnement de développement, décrits ci-après :</span><span class="sxs-lookup"><span data-stu-id="825d6-511">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="825d6-512">Dans <xref:fundamentals/environments#development>.</span><span class="sxs-lookup"><span data-stu-id="825d6-512">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="825d6-513">Dans l’ensemble de la documentation dans lequel les fichiers sont utilisés pour configurer des applications ASP.NET Core pour les scénarios de développement.</span><span class="sxs-lookup"><span data-stu-id="825d6-513">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="825d6-514">*Web. config* est un fichier de configuration de serveur, décrit dans les rubriques suivantes :</span><span class="sxs-lookup"><span data-stu-id="825d6-514">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="825d6-515">Pour plus d’informations sur la migration de la configuration d’application à partir <xref:migration/proper-to-2x/index#store-configurations>de versions antérieures de ASP.net, consultez.</span><span class="sxs-lookup"><span data-stu-id="825d6-515">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="825d6-516">Configuration par défaut</span><span class="sxs-lookup"><span data-stu-id="825d6-516">Default configuration</span></span>

<span data-ttu-id="825d6-517">Les applications web basées sur les modèles ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) appellent <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> pendant la création d’un hôte.</span><span class="sxs-lookup"><span data-stu-id="825d6-517">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="825d6-518">`CreateDefaultBuilder` fournit la configuration par défaut de l’application dans l’ordre suivant :</span><span class="sxs-lookup"><span data-stu-id="825d6-518">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="825d6-519">Les éléments suivants s’appliquent aux applications qui utilisent l’[hôte web](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="825d6-519">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="825d6-520">Pour plus de détails sur la configuration par défaut lors de l’utilisation de l’[hôte générique](xref:fundamentals/host/generic-host), consultez la [dernière version de cette rubrique](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="825d6-520">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="825d6-521">La configuration de l’hôte est fournie à partir des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="825d6-521">Host configuration is provided from:</span></span>
  * <span data-ttu-id="825d6-522">Des variables d’environnement préfixées avec `ASPNETCORE_` (par exemple, `ASPNETCORE_ENVIRONMENT`) à l’aide du [Fournisseur de configuration des variables d’environnement](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="825d6-522">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="825d6-523">Le préfixe (`ASPNETCORE_`) est supprimé lorsque les paires clé-valeur de la configuration sont chargées.</span><span class="sxs-lookup"><span data-stu-id="825d6-523">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="825d6-524">Des arguments de ligne de commande à l’aide du [Fournisseur de configuration de ligne de commande](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="825d6-524">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="825d6-525">La configuration de l’application est fournie à partir des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="825d6-525">App configuration is provided from:</span></span>
  * <span data-ttu-id="825d6-526">*appSettings.JSON* à l’aide du [Fournisseur de configuration de fichier](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="825d6-526">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="825d6-527">*appsettings.{Environment}.json* à l’aide du [Fournisseur de configuration de fichier](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="825d6-527">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="825d6-528">L’outil [Secret Manager (Gestionnaire de secrets)](xref:security/app-secrets) quand l’application s’exécute dans l’environnement `Development` à l’aide de l’assembly d’entrée.</span><span class="sxs-lookup"><span data-stu-id="825d6-528">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="825d6-529">Des variables d’environnement à l’aide du [Fournisseur de configuration des variables d’environnement](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="825d6-529">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="825d6-530">Des arguments de ligne de commande à l’aide du [Fournisseur de configuration de ligne de commande](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="825d6-530">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="825d6-531">Sécurité</span><span class="sxs-lookup"><span data-stu-id="825d6-531">Security</span></span>

<span data-ttu-id="825d6-532">Adoptez les pratiques suivantes pour sécuriser les données de configuration sensibles :</span><span class="sxs-lookup"><span data-stu-id="825d6-532">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="825d6-533">Ne stockez jamais des mots de passe ou d’autres données sensibles dans le code du fournisseur de configuration ou dans les fichiers de configuration en texte clair.</span><span class="sxs-lookup"><span data-stu-id="825d6-533">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="825d6-534">N’utilisez aucun secret de production dans les environnements de développement ou de test.</span><span class="sxs-lookup"><span data-stu-id="825d6-534">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="825d6-535">Spécifiez les secrets en dehors du projet afin qu’ils ne puissent pas être validés par inadvertance dans un référentiel de code source.</span><span class="sxs-lookup"><span data-stu-id="825d6-535">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="825d6-536">Pour plus d'informations, voir les rubriques suivantes :</span><span class="sxs-lookup"><span data-stu-id="825d6-536">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="825d6-537"><xref:security/app-secrets>&ndash; Fournit des conseils sur l’utilisation de variables d’environnement pour stocker des données sensibles.</span><span class="sxs-lookup"><span data-stu-id="825d6-537"><xref:security/app-secrets> &ndash; Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="825d6-538">Secret Manager utilise le fournisseur de configuration de fichier pour stocker les secrets utilisateur dans un fichier JSON sur le système local.</span><span class="sxs-lookup"><span data-stu-id="825d6-538">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="825d6-539">Le fournisseur de configuration de fichier est décrit plus loin dans cette rubrique.</span><span class="sxs-lookup"><span data-stu-id="825d6-539">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="825d6-540">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) stocke en toute sécurité des secrets d’application pour les applications ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="825d6-540">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="825d6-541">Pour plus d’informations, consultez <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="825d6-541">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="825d6-542">Données de configuration hiérarchiques</span><span class="sxs-lookup"><span data-stu-id="825d6-542">Hierarchical configuration data</span></span>

<span data-ttu-id="825d6-543">L’API Configuration est capable de maintenir des données de configuration hiérarchiques en aplanissant les données hiérarchiques à l’aide d’un délimiteur dans les clés de configuration.</span><span class="sxs-lookup"><span data-stu-id="825d6-543">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="825d6-544">Dans le fichier JSON suivant, quatre clés existent dans une structure hiérarchique à deux sections :</span><span class="sxs-lookup"><span data-stu-id="825d6-544">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="825d6-545">Lorsque le fichier est lu dans la configuration, des clés uniques sont créées pour gérer la structure des données hiérarchiques d’origine de la source de configuration.</span><span class="sxs-lookup"><span data-stu-id="825d6-545">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="825d6-546">Les sections et les clés sont aplanies à l’aide d’un signe deux-points (`:`) pour conserver la structure d’origine :</span><span class="sxs-lookup"><span data-stu-id="825d6-546">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="825d6-547">section0:key0</span><span class="sxs-lookup"><span data-stu-id="825d6-547">section0:key0</span></span>
* <span data-ttu-id="825d6-548">section0:key1</span><span class="sxs-lookup"><span data-stu-id="825d6-548">section0:key1</span></span>
* <span data-ttu-id="825d6-549">section1:key0</span><span class="sxs-lookup"><span data-stu-id="825d6-549">section1:key0</span></span>
* <span data-ttu-id="825d6-550">section1:key1</span><span class="sxs-lookup"><span data-stu-id="825d6-550">section1:key1</span></span>

<span data-ttu-id="825d6-551">Les méthodes <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> et <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> sont disponibles pour isoler les sections et les enfants d’une section dans les données de configuration.</span><span class="sxs-lookup"><span data-stu-id="825d6-551"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="825d6-552">Ces méthodes sont décrites plus loin dans [GetSection, GetChildren et Exists](#getsection-getchildren-and-exists).</span><span class="sxs-lookup"><span data-stu-id="825d6-552">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="825d6-553">Conventions</span><span class="sxs-lookup"><span data-stu-id="825d6-553">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="825d6-554">Sources et fournisseurs</span><span class="sxs-lookup"><span data-stu-id="825d6-554">Sources and providers</span></span>

<span data-ttu-id="825d6-555">Au démarrage de l’application, les sources de configuration sont lues dans l’ordre où leurs fournisseurs de configuration sont spécifiés.</span><span class="sxs-lookup"><span data-stu-id="825d6-555">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="825d6-556">Les fournisseurs de configuration qui implémentent la détection des modifications peuvent recharger la configuration lorsqu’un paramètre sous-jacent est modifié.</span><span class="sxs-lookup"><span data-stu-id="825d6-556">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="825d6-557">Par exemple, le fournisseur de configuration de fichier (décrit plus loin dans cette rubrique) et le [fournisseur de configuration Azure Key Vault](xref:security/key-vault-configuration) implémentent la détection des modifications.</span><span class="sxs-lookup"><span data-stu-id="825d6-557">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="825d6-558"><xref:Microsoft.Extensions.Configuration.IConfiguration> est disponible dans le conteneur d’[injection de dépendances](xref:fundamentals/dependency-injection) de l’application.</span><span class="sxs-lookup"><span data-stu-id="825d6-558"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="825d6-559"><xref:Microsoft.Extensions.Configuration.IConfiguration>peut être injecté dans un Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> ou MVC <xref:Microsoft.AspNetCore.Mvc.Controller> pour obtenir la configuration de la classe.</span><span class="sxs-lookup"><span data-stu-id="825d6-559"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="825d6-560">Dans les exemples suivants, le `_config` champ est utilisé pour accéder aux valeurs de configuration :</span><span class="sxs-lookup"><span data-stu-id="825d6-560">In the following examples, the `_config` field is used to access configuration values:</span></span>

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

<span data-ttu-id="825d6-561">Les fournisseurs de configuration ne peuvent pas utiliser le DI, car celui-ci n’est pas disponible lorsque les fournisseurs sont configurés par l’hôte.</span><span class="sxs-lookup"><span data-stu-id="825d6-561">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="825d6-562">Keys</span><span class="sxs-lookup"><span data-stu-id="825d6-562">Keys</span></span>

<span data-ttu-id="825d6-563">Les clés de configuration adoptent les conventions suivantes :</span><span class="sxs-lookup"><span data-stu-id="825d6-563">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="825d6-564">Les clés ne respectent pas la casse.</span><span class="sxs-lookup"><span data-stu-id="825d6-564">Keys are case-insensitive.</span></span> <span data-ttu-id="825d6-565">Par exemple, `ConnectionString` et `connectionstring` sont traités en tant que clés équivalentes.</span><span class="sxs-lookup"><span data-stu-id="825d6-565">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="825d6-566">Si une valeur pour la même clé est définie par des fournisseurs de configuration identiques ou différents, la valeur utilisée est la dernière valeur définie sur la clé.</span><span class="sxs-lookup"><span data-stu-id="825d6-566">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span>
* <span data-ttu-id="825d6-567">Clés hiérarchiques</span><span class="sxs-lookup"><span data-stu-id="825d6-567">Hierarchical keys</span></span>
  * <span data-ttu-id="825d6-568">Dans l’API Configuration, un séparateur sous forme de signe deux-points (`:`) fonctionne sur toutes les plateformes.</span><span class="sxs-lookup"><span data-stu-id="825d6-568">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="825d6-569">Dans les variables d’environnement, un séparateur sous forme de signe deux-points peut ne pas fonctionner sur toutes les plateformes.</span><span class="sxs-lookup"><span data-stu-id="825d6-569">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="825d6-570">Un trait de soulignement double (`__`) est pris en charge par toutes les plateformes et automatiquement transformé en signe deux-points.</span><span class="sxs-lookup"><span data-stu-id="825d6-570">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="825d6-571">Dans Azure Key Vault, les clés hiérarchiques utilisent `--` (deux tirets) comme séparateur.</span><span class="sxs-lookup"><span data-stu-id="825d6-571">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="825d6-572">Écrivez du code pour remplacer les tirets par un signe deux-points lorsque les secrets sont chargés dans la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="825d6-572">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="825d6-573"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> prend en charge la liaison de tableaux à des objets à l’aide d’index de tableau dans les clés de configuration.</span><span class="sxs-lookup"><span data-stu-id="825d6-573">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="825d6-574">La liaison de tableau est décrite dans la section [Lier un tableau à une classe](#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="825d6-574">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="825d6-575">Valeurs</span><span class="sxs-lookup"><span data-stu-id="825d6-575">Values</span></span>

<span data-ttu-id="825d6-576">Les valeurs de configuration adoptent les conventions suivantes :</span><span class="sxs-lookup"><span data-stu-id="825d6-576">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="825d6-577">Les valeurs sont des chaînes.</span><span class="sxs-lookup"><span data-stu-id="825d6-577">Values are strings.</span></span>
* <span data-ttu-id="825d6-578">Les valeurs NULL ne peuvent pas être stockées dans la configuration ou liées à des objets.</span><span class="sxs-lookup"><span data-stu-id="825d6-578">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="825d6-579">Fournisseurs</span><span class="sxs-lookup"><span data-stu-id="825d6-579">Providers</span></span>

<span data-ttu-id="825d6-580">Le tableau suivant présente les fournisseurs de configuration disponibles pour les applications ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="825d6-580">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="825d6-581">Fournisseur</span><span class="sxs-lookup"><span data-stu-id="825d6-581">Provider</span></span> | <span data-ttu-id="825d6-582">Fournit la configuration à partir de&hellip;</span><span class="sxs-lookup"><span data-stu-id="825d6-582">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="825d6-583">[Fournisseur de configuration Azure Key Vault](xref:security/key-vault-configuration) (rubrique *Sécurité*)</span><span class="sxs-lookup"><span data-stu-id="825d6-583">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="825d6-584">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="825d6-584">Azure Key Vault</span></span> |
| <span data-ttu-id="825d6-585">[Fournisseur Azure App Configuration](/azure/azure-app-configuration/quickstart-aspnet-core-app) (documentation Azure)</span><span class="sxs-lookup"><span data-stu-id="825d6-585">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="825d6-586">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="825d6-586">Azure App Configuration</span></span> |
| [<span data-ttu-id="825d6-587">Fournisseur de configuration de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="825d6-587">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="825d6-588">Paramètres de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="825d6-588">Command-line parameters</span></span> |
| [<span data-ttu-id="825d6-589">Fournisseur de configuration personnalisé</span><span class="sxs-lookup"><span data-stu-id="825d6-589">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="825d6-590">Source personnalisée</span><span class="sxs-lookup"><span data-stu-id="825d6-590">Custom source</span></span> |
| [<span data-ttu-id="825d6-591">Fournisseur de configuration de variables d’environnement</span><span class="sxs-lookup"><span data-stu-id="825d6-591">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="825d6-592">Variables d'environnement</span><span class="sxs-lookup"><span data-stu-id="825d6-592">Environment variables</span></span> |
| [<span data-ttu-id="825d6-593">Fournisseur de configuration de fichier</span><span class="sxs-lookup"><span data-stu-id="825d6-593">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="825d6-594">Fichiers (INI, JSON, XML)</span><span class="sxs-lookup"><span data-stu-id="825d6-594">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="825d6-595">Fournisseur de configuration clé par fichier</span><span class="sxs-lookup"><span data-stu-id="825d6-595">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="825d6-596">Fichiers de répertoire</span><span class="sxs-lookup"><span data-stu-id="825d6-596">Directory files</span></span> |
| [<span data-ttu-id="825d6-597">Fournisseur de configuration de mémoire</span><span class="sxs-lookup"><span data-stu-id="825d6-597">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="825d6-598">Collections en mémoire</span><span class="sxs-lookup"><span data-stu-id="825d6-598">In-memory collections</span></span> |
| <span data-ttu-id="825d6-599">[Secrets utilisateur (Secret Manager)](xref:security/app-secrets) (rubrique *Sécurité*)</span><span class="sxs-lookup"><span data-stu-id="825d6-599">[User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="825d6-600">Fichier dans le répertoire de profil utilisateur</span><span class="sxs-lookup"><span data-stu-id="825d6-600">File in the user profile directory</span></span> |

<span data-ttu-id="825d6-601">Au démarrage, les sources de configuration sont lues dans l’ordre où leurs fournisseurs de configuration sont spécifiés.</span><span class="sxs-lookup"><span data-stu-id="825d6-601">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="825d6-602">Les fournisseurs de configuration décrits dans cette rubrique sont décrits par ordre alphabétique, et non pas dans l’ordre dans lequel le code les réorganise.</span><span class="sxs-lookup"><span data-stu-id="825d6-602">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="825d6-603">Commandez des fournisseurs de configuration dans le code pour répondre aux priorités des sources de configuration sous-jacentes requises par l’application.</span><span class="sxs-lookup"><span data-stu-id="825d6-603">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="825d6-604">Une séquence type des fournisseurs de configuration est la suivante :</span><span class="sxs-lookup"><span data-stu-id="825d6-604">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="825d6-605">Fichiers (*appsettings.json*, *appsettings.{Environment}.json*, où `{Environment}` est l'environnement d’hébergement actuel de l'application)</span><span class="sxs-lookup"><span data-stu-id="825d6-605">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="825d6-606">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="825d6-606">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="825d6-607">[Secrets utilisateur (Secret Manager)](xref:security/app-secrets) (dans l’environnement de développement uniquement)</span><span class="sxs-lookup"><span data-stu-id="825d6-607">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="825d6-608">Variables d'environnement</span><span class="sxs-lookup"><span data-stu-id="825d6-608">Environment variables</span></span>
1. <span data-ttu-id="825d6-609">Arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="825d6-609">Command-line arguments</span></span>

<span data-ttu-id="825d6-610">Une pratique courante consiste à placer le Fournisseur de configuration de ligne de commande en dernier dans une série de fournisseurs pour permettre aux arguments de ligne de commande de remplacer la configuration définie par les autres fournisseurs.</span><span class="sxs-lookup"><span data-stu-id="825d6-610">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="825d6-611">La séquence de fournisseurs précédente est utilisée lors de l’initialisation d’un nouveau générateur d' `CreateDefaultBuilder`hôte.</span><span class="sxs-lookup"><span data-stu-id="825d6-611">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="825d6-612">Pour plus d’informations, consultez la section [Configuration par défaut](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="825d6-612">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="825d6-613">Configurer le générateur d’ordinateur hôte avec UseConfiguration</span><span class="sxs-lookup"><span data-stu-id="825d6-613">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="825d6-614">Pour configurer le générateur d’ordinateur hôte, appelez <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> sur le générateur d’hôte avec la configuration.</span><span class="sxs-lookup"><span data-stu-id="825d6-614">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

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

## <a name="configureappconfiguration"></a><span data-ttu-id="825d6-615">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="825d6-615">ConfigureAppConfiguration</span></span>

<span data-ttu-id="825d6-616">Appelez `ConfigureAppConfiguration` quand vous créez l’hôte pour spécifier les fournisseurs de configuration de l’application en plus de ceux ajoutés automatiquement par `CreateDefaultBuilder` :</span><span class="sxs-lookup"><span data-stu-id="825d6-616">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="825d6-617">Remplacez la configuration précédente par des arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="825d6-617">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="825d6-618">Pour fournir une configuration d’application pouvant être remplacée par des arguments de ligne de commande, appelez `AddCommandLine` en dernier :</span><span class="sxs-lookup"><span data-stu-id="825d6-618">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="825d6-619">Supprimer les fournisseurs ajoutés par CreateDefaultBuilder</span><span class="sxs-lookup"><span data-stu-id="825d6-619">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="825d6-620">Pour supprimer les fournisseurs ajoutés par `CreateDefaultBuilder`, appelez d’abord [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) sur [IConfigurationBuilder. sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) :</span><span class="sxs-lookup"><span data-stu-id="825d6-620">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="825d6-621">Utiliser la configuration lors du démarrage de l’application</span><span class="sxs-lookup"><span data-stu-id="825d6-621">Consume configuration during app startup</span></span>

<span data-ttu-id="825d6-622">La configuration fournie à l’application dans `ConfigureAppConfiguration` est disponible lors du démarrage de l’application, notamment `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="825d6-622">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="825d6-623">Pour plus d’informations, consultez la section [Accéder à la configuration lors du démarrage](#access-configuration-during-startup).</span><span class="sxs-lookup"><span data-stu-id="825d6-623">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="825d6-624">Fournisseur de configuration de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="825d6-624">Command-line Configuration Provider</span></span>

<span data-ttu-id="825d6-625"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> charge la configuration à partir des paires clé-valeur de l’argument de ligne de commande lors de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="825d6-625">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="825d6-626">Pour activer la configuration en ligne de commande, la méthode d’extension <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> est appelée sur une instance de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="825d6-626">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="825d6-627">`AddCommandLine` est appelé automatiquement quand `CreateDefaultBuilder(string [])` est appelé.</span><span class="sxs-lookup"><span data-stu-id="825d6-627">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="825d6-628">Pour plus d’informations, consultez la section [Configuration par défaut](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="825d6-628">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="825d6-629">`CreateDefaultBuilder` charge également :</span><span class="sxs-lookup"><span data-stu-id="825d6-629">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="825d6-630">Configuration facultative à partir des fichiers *appsettings.json* et *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="825d6-630">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="825d6-631">[Secrets utilisateur (Secret Manager)](xref:security/app-secrets) dans l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="825d6-631">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="825d6-632">Variables d'environnement.</span><span class="sxs-lookup"><span data-stu-id="825d6-632">Environment variables.</span></span>

<span data-ttu-id="825d6-633">`CreateDefaultBuilder` ajoute en dernier le Fournisseur de configuration de ligne de commande.</span><span class="sxs-lookup"><span data-stu-id="825d6-633">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="825d6-634">Les arguments de ligne de commande passés lors de l’exécution remplacent la configuration définie par les autres fournisseurs.</span><span class="sxs-lookup"><span data-stu-id="825d6-634">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="825d6-635">`CreateDefaultBuilder` agit lors de la construction de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="825d6-635">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="825d6-636">Par conséquent, la configuration de ligne de commande activée par `CreateDefaultBuilder` peut affecter la façon dont l’hôte est configuré.</span><span class="sxs-lookup"><span data-stu-id="825d6-636">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="825d6-637">Pour les applications basées sur les modèles ASP.NET Core, `AddCommandLine` a déjà été appelé par `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="825d6-637">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="825d6-638">Pour ajouter des fournisseurs de configuration supplémentaires et conserver la capacité de remplacer leur configuration par des arguments de ligne de commande, appelez les fournisseurs supplémentaires de l’application dans `ConfigureAppConfiguration` et appelez `AddCommandLine` en dernier.</span><span class="sxs-lookup"><span data-stu-id="825d6-638">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="825d6-639">**Exemple**</span><span class="sxs-lookup"><span data-stu-id="825d6-639">**Example**</span></span>

<span data-ttu-id="825d6-640">L’exemple d’application tire parti de la méthode pratique statique `CreateDefaultBuilder` pour générer l’hôte, qui inclut un appel à <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="825d6-640">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="825d6-641">Ouvrez une invite de commandes dans le répertoire du projet.</span><span class="sxs-lookup"><span data-stu-id="825d6-641">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="825d6-642">Fournissez un argument de ligne de commande à la commande `dotnet run`, `dotnet run CommandLineKey=CommandLineValue`.</span><span class="sxs-lookup"><span data-stu-id="825d6-642">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="825d6-643">Une fois que l’application est en cours d’exécution, ouvrez un navigateur à l’application à l’adresse `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="825d6-643">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="825d6-644">Notez que la sortie contient la paire clé-valeur pour l’argument de ligne de commande de configuration fourni à `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="825d6-644">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="825d6-645">Arguments</span><span class="sxs-lookup"><span data-stu-id="825d6-645">Arguments</span></span>

<span data-ttu-id="825d6-646">La valeur doit suivre un signe égal (`=`) ou la clé doit avoir un préfixe (`--` ou `/`) lorsque la valeur suit un espace.</span><span class="sxs-lookup"><span data-stu-id="825d6-646">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="825d6-647">La valeur n’est pas requise si un signe égal est utilisé (par exemple, `CommandLineKey=`).</span><span class="sxs-lookup"><span data-stu-id="825d6-647">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="825d6-648">Préfixe de clé</span><span class="sxs-lookup"><span data-stu-id="825d6-648">Key prefix</span></span>               | <span data-ttu-id="825d6-649">Exemple</span><span class="sxs-lookup"><span data-stu-id="825d6-649">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="825d6-650">Aucun préfixe</span><span class="sxs-lookup"><span data-stu-id="825d6-650">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="825d6-651">Deux tirets (`--`)</span><span class="sxs-lookup"><span data-stu-id="825d6-651">Two dashes (`--`)</span></span>        | <span data-ttu-id="825d6-652">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="825d6-652">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="825d6-653">Barre oblique (`/`)</span><span class="sxs-lookup"><span data-stu-id="825d6-653">Forward slash (`/`)</span></span>      | <span data-ttu-id="825d6-654">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="825d6-654">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="825d6-655">Dans la même commande, ne mélangez pas des paires clé-valeur de l’argument de ligne de commande qui utilisent un signe égal avec des paires clé-valeur qui utilisent un espace.</span><span class="sxs-lookup"><span data-stu-id="825d6-655">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="825d6-656">Exemples de commandes :</span><span class="sxs-lookup"><span data-stu-id="825d6-656">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="825d6-657">Correspondances de commutateur</span><span class="sxs-lookup"><span data-stu-id="825d6-657">Switch mappings</span></span>

<span data-ttu-id="825d6-658">Les correspondances de commutateur permettent une logique de remplacement des noms de clés.</span><span class="sxs-lookup"><span data-stu-id="825d6-658">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="825d6-659">Lors de la génération manuelle d' <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>une configuration avec un, fournissez un dictionnaire de remplacements de commutateur à la <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> méthode.</span><span class="sxs-lookup"><span data-stu-id="825d6-659">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="825d6-660">Quand le dictionnaire de correspondances de commutateur est utilisé, il est vérifié afin de déterminer s’il contient une clé correspondant à celle fournie par un argument de ligne de commande.</span><span class="sxs-lookup"><span data-stu-id="825d6-660">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="825d6-661">Si la clé de ligne de commande est trouvée dans le dictionnaire, la valeur du dictionnaire (le remplacement de la clé) est repassée pour définir la paire clé-valeur dans la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="825d6-661">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="825d6-662">Une correspondance de commutateur est nécessaire pour chaque clé de ligne de commande préfixée avec un tiret unique (`-`).</span><span class="sxs-lookup"><span data-stu-id="825d6-662">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="825d6-663">Règles des clés du dictionnaire de correspondances de commutateur :</span><span class="sxs-lookup"><span data-stu-id="825d6-663">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="825d6-664">Les commutateurs doivent commencer par un tiret (`-`) ou un double tiret (`--`).</span><span class="sxs-lookup"><span data-stu-id="825d6-664">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="825d6-665">Le dictionnaire de correspondances de commutateur ne doit pas contenir de clés en double.</span><span class="sxs-lookup"><span data-stu-id="825d6-665">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="825d6-666">Créez un dictionnaire des mappages de commutateurs.</span><span class="sxs-lookup"><span data-stu-id="825d6-666">Create a switch mappings dictionary.</span></span> <span data-ttu-id="825d6-667">Dans l’exemple suivant, deux mappages de commutateurs sont créés :</span><span class="sxs-lookup"><span data-stu-id="825d6-667">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="825d6-668">Lorsque l’hôte est généré, appelez `AddCommandLine` avec le dictionnaire de mappages de commutateurs :</span><span class="sxs-lookup"><span data-stu-id="825d6-668">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="825d6-669">Pour les applications qui utilisent des mappages de commutateurs, l’appel à `CreateDefaultBuilder` ne doit pas passer d’arguments.</span><span class="sxs-lookup"><span data-stu-id="825d6-669">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="825d6-670">L’appel `AddCommandLine` de la méthode `CreateDefaultBuilder` n’inclut pas de commutateurs mappés et il n’existe aucun moyen de transmettre le dictionnaire de correspondance de commutateur à `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="825d6-670">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="825d6-671">La solution ne consiste pas à transmettre les arguments à `CreateDefaultBuilder`, mais plutôt à permettre à la méthode `AddCommandLine` de la méthode `ConfigurationBuilder` de traiter les arguments et le dictionnaire de mappage de commutateur.</span><span class="sxs-lookup"><span data-stu-id="825d6-671">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="825d6-672">Une fois le dictionnaire de correspondances de commutateur créé, il contient les données affichées dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="825d6-672">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="825d6-673">Clé</span><span class="sxs-lookup"><span data-stu-id="825d6-673">Key</span></span>       | <span data-ttu-id="825d6-674">Valeur</span><span class="sxs-lookup"><span data-stu-id="825d6-674">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="825d6-675">Si les clés mappées au commutateur sont utilisées lors du démarrage de l’application, la configuration reçoit la valeur de configuration sur la clé fournie par le dictionnaire :</span><span class="sxs-lookup"><span data-stu-id="825d6-675">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="825d6-676">Après avoir exécuté la commande précédente, la configuration contient les valeurs indiquées dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="825d6-676">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="825d6-677">Clé</span><span class="sxs-lookup"><span data-stu-id="825d6-677">Key</span></span>               | <span data-ttu-id="825d6-678">Valeur</span><span class="sxs-lookup"><span data-stu-id="825d6-678">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="825d6-679">Fournisseur de configuration de variables d’environnement</span><span class="sxs-lookup"><span data-stu-id="825d6-679">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="825d6-680"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> charge la configuration à partir des paires clé-valeur de la variable d’environnement lors de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="825d6-680">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="825d6-681">Pour activer la configuration des variables d’environnement, appelez la méthode d’extension <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> sur une instance de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="825d6-681">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="825d6-682">[Azure App service](https://azure.microsoft.com/services/app-service/) permet de définir des variables d’environnement dans le portail Azure qui peuvent remplacer la configuration de l’application à l’aide du fournisseur de configuration des variables d’environnement.</span><span class="sxs-lookup"><span data-stu-id="825d6-682">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="825d6-683">Pour plus d’informations, consultez [Azure Apps : remplacer la configuration de l’application à l’aide du portail Azure](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="825d6-683">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="825d6-684">`AddEnvironmentVariables` sert à charger les variables d’environnement préfixées avec `ASPNETCORE_` pour la [configuration hôte](#host-versus-app-configuration) lorsqu’un nouveau générateur d’hôte est initialisé avec l’[hôte web](xref:fundamentals/host/web-host) et que `CreateDefaultBuilder` est appelé.</span><span class="sxs-lookup"><span data-stu-id="825d6-684">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="825d6-685">Pour plus d’informations, consultez la section [Configuration par défaut](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="825d6-685">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="825d6-686">`CreateDefaultBuilder` charge également :</span><span class="sxs-lookup"><span data-stu-id="825d6-686">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="825d6-687">Configuration de l’application à partir de variables d’environnement sans préfixe en appelant `AddEnvironmentVariables` sans préfixe.</span><span class="sxs-lookup"><span data-stu-id="825d6-687">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="825d6-688">Configuration facultative à partir des fichiers *appsettings.json* et *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="825d6-688">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="825d6-689">[Secrets utilisateur (Secret Manager)](xref:security/app-secrets) dans l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="825d6-689">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="825d6-690">Arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="825d6-690">Command-line arguments.</span></span>

<span data-ttu-id="825d6-691">Le fournisseur de configuration de variables d’environnement est appelé une fois que la configuration est établie à partir des secrets utilisateur et des fichiers *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="825d6-691">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="825d6-692">Le fait d’appeler le fournisseur ainsi permet de lire les variables d’environnement pendant l’exécution pour substituer la configuration définie par les secrets utilisateur et les fichiers *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="825d6-692">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="825d6-693">Pour fournir la configuration d’application à partir de variables d’environnement supplémentaires, appelez les `ConfigureAppConfiguration` fournisseurs supplémentaires `AddEnvironmentVariables` de l’application dans et appelez avec le préfixe :</span><span class="sxs-lookup"><span data-stu-id="825d6-693">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="825d6-694">Appelez `AddEnvironmentVariables` Last pour autoriser les variables d’environnement avec le préfixe spécifié à substituer des valeurs d’autres fournisseurs.</span><span class="sxs-lookup"><span data-stu-id="825d6-694">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="825d6-695">**Exemple**</span><span class="sxs-lookup"><span data-stu-id="825d6-695">**Example**</span></span>

<span data-ttu-id="825d6-696">L’exemple d’application tire parti de la méthode pratique statique `CreateDefaultBuilder` pour générer l’hôte, qui inclut un appel à `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="825d6-696">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="825d6-697">Exécutez l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="825d6-697">Run the sample app.</span></span> <span data-ttu-id="825d6-698">Ouvrez un navigateur vers l’application avec l’adresse `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="825d6-698">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="825d6-699">Notez que la sortie contient la paire clé-valeur pour la variable d’environnement `ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="825d6-699">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="825d6-700">La valeur reflète l’environnement dans lequel l’application est en cours d’exécution, en général `Development` lors de l’exécution locale.</span><span class="sxs-lookup"><span data-stu-id="825d6-700">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="825d6-701">Pour que la liste des variables d’environnement restituée par l’application soit courte, l’application filtre les variables d’environnement.</span><span class="sxs-lookup"><span data-stu-id="825d6-701">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="825d6-702">Consultez le fichier *Pages/Index.cshtml.cs* de l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="825d6-702">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="825d6-703">Pour exposer toutes les variables d’environnement disponibles pour l’application, remplacez `FilteredConfiguration` dans *pages/index. cshtml. cs* par ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="825d6-703">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="825d6-704">Préfixes</span><span class="sxs-lookup"><span data-stu-id="825d6-704">Prefixes</span></span>

<span data-ttu-id="825d6-705">Les variables d’environnement chargées dans la configuration de l’application sont filtrées lors de la `AddEnvironmentVariables` spécification d’un préfixe à la méthode.</span><span class="sxs-lookup"><span data-stu-id="825d6-705">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="825d6-706">Par exemple, pour filtrer les variables d’environnement sur le préfixe `CUSTOM_`, fournissez le préfixe au fournisseur de configuration :</span><span class="sxs-lookup"><span data-stu-id="825d6-706">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="825d6-707">Le préfixe est supprimé lorsque les paires clé-valeur de la configuration sont créées.</span><span class="sxs-lookup"><span data-stu-id="825d6-707">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="825d6-708">Lorsque le générateur d’hôte est créé, la configuration de l’hôte est fournie par des variables d’environnement.</span><span class="sxs-lookup"><span data-stu-id="825d6-708">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="825d6-709">Pour plus d’informations sur le préfixe utilisé pour ces variables d’environnement, consultez la section [Configuration par défaut](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="825d6-709">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="825d6-710">**Préfixes des chaînes de connexion**</span><span class="sxs-lookup"><span data-stu-id="825d6-710">**Connection string prefixes**</span></span>

<span data-ttu-id="825d6-711">L’API Configuration possède des règles de traitement spéciales pour quatre variables d’environnement de chaîne de connexion impliquées dans la configuration des chaînes de connexion Azure pour l’environnement de l’application.</span><span class="sxs-lookup"><span data-stu-id="825d6-711">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="825d6-712">Les variables d’environnement avec les préfixes indiqués dans le tableau sont chargées dans l’application si aucun préfixe n’est fourni à `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="825d6-712">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="825d6-713">Préfixe de la chaîne de connexion</span><span class="sxs-lookup"><span data-stu-id="825d6-713">Connection string prefix</span></span> | <span data-ttu-id="825d6-714">Fournisseur</span><span class="sxs-lookup"><span data-stu-id="825d6-714">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="825d6-715">Fournisseur personnalisé</span><span class="sxs-lookup"><span data-stu-id="825d6-715">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="825d6-716">MySQL</span><span class="sxs-lookup"><span data-stu-id="825d6-716">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="825d6-717">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="825d6-717">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="825d6-718">SQL Server</span><span class="sxs-lookup"><span data-stu-id="825d6-718">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="825d6-719">Quand une variable d’environnement est découverte et chargée dans la configuration avec l’un des quatre préfixes indiqués dans le tableau :</span><span class="sxs-lookup"><span data-stu-id="825d6-719">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="825d6-720">La clé de configuration est créée en supprimant le préfixe de la variable d’environnement et en ajoutant une section de clé de configuration (`ConnectionStrings`).</span><span class="sxs-lookup"><span data-stu-id="825d6-720">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="825d6-721">Une nouvelle paire clé-valeur de configuration est créée qui représente le fournisseur de connexion de base de données (à l’exception de `CUSTOMCONNSTR_`, qui ne possède aucun fournisseur indiqué).</span><span class="sxs-lookup"><span data-stu-id="825d6-721">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="825d6-722">Clé de variable d’environnement</span><span class="sxs-lookup"><span data-stu-id="825d6-722">Environment variable key</span></span> | <span data-ttu-id="825d6-723">Clé de configuration convertie</span><span class="sxs-lookup"><span data-stu-id="825d6-723">Converted configuration key</span></span> | <span data-ttu-id="825d6-724">Entrée de configuration de fournisseur</span><span class="sxs-lookup"><span data-stu-id="825d6-724">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="825d6-725">Entrée de configuration non créée.</span><span class="sxs-lookup"><span data-stu-id="825d6-725">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="825d6-726">Clé : `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="825d6-726">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="825d6-727">Valeur: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="825d6-727">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="825d6-728">Clé : `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="825d6-728">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="825d6-729">Valeur: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="825d6-729">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="825d6-730">Clé : `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="825d6-730">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="825d6-731">Valeur: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="825d6-731">Value: `System.Data.SqlClient`</span></span>  |

<span data-ttu-id="825d6-732">**Exemple**</span><span class="sxs-lookup"><span data-stu-id="825d6-732">**Example**</span></span>

<span data-ttu-id="825d6-733">Une variable d’environnement de chaîne de connexion personnalisée est créée sur le serveur :</span><span class="sxs-lookup"><span data-stu-id="825d6-733">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="825d6-734">Nom &ndash;`CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="825d6-734">Name &ndash; `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="825d6-735">Valeur &ndash;`Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="825d6-735">Value &ndash; `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="825d6-736">Si `IConfiguration` est injecté et affecté à un champ nommé `_config`, lisez la valeur :</span><span class="sxs-lookup"><span data-stu-id="825d6-736">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="825d6-737">Fournisseur de configuration de fichier</span><span class="sxs-lookup"><span data-stu-id="825d6-737">File Configuration Provider</span></span>

<span data-ttu-id="825d6-738"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> est la classe de base pour charger la configuration à partir du système de fichiers.</span><span class="sxs-lookup"><span data-stu-id="825d6-738"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="825d6-739">Les fournisseurs de configuration suivants sont dédiés à des types de fichiers spécifiques :</span><span class="sxs-lookup"><span data-stu-id="825d6-739">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="825d6-740">Fournisseur de configuration INI</span><span class="sxs-lookup"><span data-stu-id="825d6-740">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="825d6-741">Fournisseur de configuration JSON</span><span class="sxs-lookup"><span data-stu-id="825d6-741">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="825d6-742">Fournisseur de configuration XML</span><span class="sxs-lookup"><span data-stu-id="825d6-742">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="825d6-743">Fournisseur de configuration INI</span><span class="sxs-lookup"><span data-stu-id="825d6-743">INI Configuration Provider</span></span>

<span data-ttu-id="825d6-744"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> charge la configuration à partir des paires clé-valeur du fichier INI lors de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="825d6-744">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="825d6-745">Pour activer la configuration du fichier INI, appelez la méthode d’extension <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> sur une instance de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="825d6-745">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="825d6-746">Le signe deux-points peut servir de délimiteur de section dans la configuration d’un fichier INI.</span><span class="sxs-lookup"><span data-stu-id="825d6-746">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="825d6-747">Les surcharges permettent de spécifier :</span><span class="sxs-lookup"><span data-stu-id="825d6-747">Overloads permit specifying:</span></span>

* <span data-ttu-id="825d6-748">Si le fichier est facultatif.</span><span class="sxs-lookup"><span data-stu-id="825d6-748">Whether the file is optional.</span></span>
* <span data-ttu-id="825d6-749">Si la configuration est rechargée quand le fichier est modifié.</span><span class="sxs-lookup"><span data-stu-id="825d6-749">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="825d6-750">Le <xref:Microsoft.Extensions.FileProviders.IFileProvider> utilisé pour accéder au fichier.</span><span class="sxs-lookup"><span data-stu-id="825d6-750">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="825d6-751">Appelez `ConfigureAppConfiguration` lors de la création de l’hôte pour spécifier la configuration de l’application :</span><span class="sxs-lookup"><span data-stu-id="825d6-751">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="825d6-752">Exemple générique d’un fichier de configuration INI :</span><span class="sxs-lookup"><span data-stu-id="825d6-752">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="825d6-753">Le fichier de configuration précédent charge les clés suivantes avec `value` :</span><span class="sxs-lookup"><span data-stu-id="825d6-753">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="825d6-754">section0:key0</span><span class="sxs-lookup"><span data-stu-id="825d6-754">section0:key0</span></span>
* <span data-ttu-id="825d6-755">section0:key1</span><span class="sxs-lookup"><span data-stu-id="825d6-755">section0:key1</span></span>
* <span data-ttu-id="825d6-756">section1:subsection:key</span><span class="sxs-lookup"><span data-stu-id="825d6-756">section1:subsection:key</span></span>
* <span data-ttu-id="825d6-757">section2:subsection0:key</span><span class="sxs-lookup"><span data-stu-id="825d6-757">section2:subsection0:key</span></span>
* <span data-ttu-id="825d6-758">section2:subsection1:key</span><span class="sxs-lookup"><span data-stu-id="825d6-758">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="825d6-759">Fournisseur de configuration JSON</span><span class="sxs-lookup"><span data-stu-id="825d6-759">JSON Configuration Provider</span></span>

<span data-ttu-id="825d6-760"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> charge la configuration à partir des paires clé-valeur du fichier JSON lors de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="825d6-760">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="825d6-761">Pour activer la configuration du fichier JSON, appelez la méthode d’extension <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> sur une instance de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="825d6-761">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="825d6-762">Les surcharges permettent de spécifier :</span><span class="sxs-lookup"><span data-stu-id="825d6-762">Overloads permit specifying:</span></span>

* <span data-ttu-id="825d6-763">Si le fichier est facultatif.</span><span class="sxs-lookup"><span data-stu-id="825d6-763">Whether the file is optional.</span></span>
* <span data-ttu-id="825d6-764">Si la configuration est rechargée quand le fichier est modifié.</span><span class="sxs-lookup"><span data-stu-id="825d6-764">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="825d6-765">Le <xref:Microsoft.Extensions.FileProviders.IFileProvider> utilisé pour accéder au fichier.</span><span class="sxs-lookup"><span data-stu-id="825d6-765">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="825d6-766">`AddJsonFile`est appelé automatiquement deux fois lors de l’initialisation d’un nouveau générateur `CreateDefaultBuilder`d’hôte.</span><span class="sxs-lookup"><span data-stu-id="825d6-766">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="825d6-767">La méthode est appelée pour charger la configuration à partir de :</span><span class="sxs-lookup"><span data-stu-id="825d6-767">The method is called to load configuration from:</span></span>

* <span data-ttu-id="825d6-768">*appSettings.JSON* &ndash; Ce fichier est lu en premier.</span><span class="sxs-lookup"><span data-stu-id="825d6-768">*appsettings.json* &ndash; This file is read first.</span></span> <span data-ttu-id="825d6-769">La version de l’environnement du fichier peut remplacer les valeurs fournies par le fichier *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="825d6-769">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="825d6-770">*appsettings.{Environment}.json* &ndash; La version de l’environnement du fichier est chargée à partir du fichier [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="825d6-770">*appsettings.{Environment}.json* &ndash; The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="825d6-771">Pour plus d’informations, consultez la section [Configuration par défaut](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="825d6-771">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="825d6-772">`CreateDefaultBuilder` charge également :</span><span class="sxs-lookup"><span data-stu-id="825d6-772">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="825d6-773">Variables d'environnement.</span><span class="sxs-lookup"><span data-stu-id="825d6-773">Environment variables.</span></span>
* <span data-ttu-id="825d6-774">[Secrets utilisateur (Secret Manager)](xref:security/app-secrets) dans l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="825d6-774">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="825d6-775">Arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="825d6-775">Command-line arguments.</span></span>

<span data-ttu-id="825d6-776">Le Fournisseur de configuration JSON est établi en premier.</span><span class="sxs-lookup"><span data-stu-id="825d6-776">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="825d6-777">Par conséquent, les secrets utilisateur, les variables d’environnement et les arguments de ligne de commande remplacent la configuration définie par les fichiers *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="825d6-777">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="825d6-778">Appelez `ConfigureAppConfiguration` lors de la création de l’hôte pour spécifier la configuration de l’application pour les fichiers autres qu’*appsettings.json* et *appsettings. {Environment} .json* :</span><span class="sxs-lookup"><span data-stu-id="825d6-778">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="825d6-779">**Exemple**</span><span class="sxs-lookup"><span data-stu-id="825d6-779">**Example**</span></span>

<span data-ttu-id="825d6-780">L’exemple d’application tire parti de la méthode `CreateDefaultBuilder` de commodité statique pour créer l’hôte, ce qui comprend `AddJsonFile`deux appels à :</span><span class="sxs-lookup"><span data-stu-id="825d6-780">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="825d6-781">Le premier appel à `AddJsonFile` charge la configuration à partir de *appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="825d6-781">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="825d6-782">Le deuxième appel à `AddJsonFile` charge la configuration à partir de *appSettings. { Environnement}. JSON*.</span><span class="sxs-lookup"><span data-stu-id="825d6-782">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="825d6-783">Pour *appSettings. Development. JSON* dans l’exemple d’application, le fichier suivant est chargé :</span><span class="sxs-lookup"><span data-stu-id="825d6-783">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="825d6-784">Exécutez l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="825d6-784">Run the sample app.</span></span> <span data-ttu-id="825d6-785">Ouvrez un navigateur vers l’application avec l’adresse `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="825d6-785">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="825d6-786">La sortie contient des paires clé-valeur pour la configuration en fonction de l’environnement de l’application.</span><span class="sxs-lookup"><span data-stu-id="825d6-786">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="825d6-787">Le niveau de journalisation de `Logging:LogLevel:Default` la `Debug` clé est lors de l’exécution de l’application dans l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="825d6-787">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="825d6-788">Exécutez à nouveau l’exemple d’application dans l’environnement de production :</span><span class="sxs-lookup"><span data-stu-id="825d6-788">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="825d6-789">Ouvrez le fichier *Properties/launchSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="825d6-789">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="825d6-790">Dans le `ConfigurationSample` profil, remplacez la valeur de la `ASPNETCORE_ENVIRONMENT` variable d’environnement `Production`par.</span><span class="sxs-lookup"><span data-stu-id="825d6-790">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="825d6-791">Enregistrez le fichier et exécutez l’application avec `dotnet run` dans un interpréteur de commandes.</span><span class="sxs-lookup"><span data-stu-id="825d6-791">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="825d6-792">Paramètres dans *appSettings. Development. JSON* ne remplace plus les paramètres dans *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="825d6-792">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="825d6-793">Le niveau de journalisation de `Logging:LogLevel:Default` la `Warning`clé est.</span><span class="sxs-lookup"><span data-stu-id="825d6-793">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="825d6-794">Fournisseur de configuration XML</span><span class="sxs-lookup"><span data-stu-id="825d6-794">XML Configuration Provider</span></span>

<span data-ttu-id="825d6-795"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> charge la configuration à partir des paires clé-valeur du fichier XML lors de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="825d6-795">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="825d6-796">Pour activer la configuration du fichier XML, appelez la méthode d’extension <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> sur une instance de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="825d6-796">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="825d6-797">Les surcharges permettent de spécifier :</span><span class="sxs-lookup"><span data-stu-id="825d6-797">Overloads permit specifying:</span></span>

* <span data-ttu-id="825d6-798">Si le fichier est facultatif.</span><span class="sxs-lookup"><span data-stu-id="825d6-798">Whether the file is optional.</span></span>
* <span data-ttu-id="825d6-799">Si la configuration est rechargée quand le fichier est modifié.</span><span class="sxs-lookup"><span data-stu-id="825d6-799">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="825d6-800">Le <xref:Microsoft.Extensions.FileProviders.IFileProvider> utilisé pour accéder au fichier.</span><span class="sxs-lookup"><span data-stu-id="825d6-800">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="825d6-801">Le nœud racine du fichier de configuration est ignoré lorsque les paires clé-valeur de la configuration sont créées.</span><span class="sxs-lookup"><span data-stu-id="825d6-801">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="825d6-802">Ne spécifiez pas une définition de type de document (DTD) ou un espace de noms dans le fichier.</span><span class="sxs-lookup"><span data-stu-id="825d6-802">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="825d6-803">Appelez `ConfigureAppConfiguration` lors de la création de l’hôte pour spécifier la configuration de l’application :</span><span class="sxs-lookup"><span data-stu-id="825d6-803">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="825d6-804">Les fichiers de configuration XML peuvent utiliser des noms d’éléments distincts pour les sections répétitives :</span><span class="sxs-lookup"><span data-stu-id="825d6-804">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="825d6-805">Le fichier de configuration précédent charge les clés suivantes avec `value` :</span><span class="sxs-lookup"><span data-stu-id="825d6-805">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="825d6-806">section0:key0</span><span class="sxs-lookup"><span data-stu-id="825d6-806">section0:key0</span></span>
* <span data-ttu-id="825d6-807">section0:key1</span><span class="sxs-lookup"><span data-stu-id="825d6-807">section0:key1</span></span>
* <span data-ttu-id="825d6-808">section1:key0</span><span class="sxs-lookup"><span data-stu-id="825d6-808">section1:key0</span></span>
* <span data-ttu-id="825d6-809">section1:key1</span><span class="sxs-lookup"><span data-stu-id="825d6-809">section1:key1</span></span>

<span data-ttu-id="825d6-810">Les éléments répétitifs qui utilisent le même nom d’élément fonctionnent si l’attribut `name` est utilisé pour distinguer les éléments :</span><span class="sxs-lookup"><span data-stu-id="825d6-810">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="825d6-811">Le fichier de configuration précédent charge les clés suivantes avec `value` :</span><span class="sxs-lookup"><span data-stu-id="825d6-811">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="825d6-812">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="825d6-812">section:section0:key:key0</span></span>
* <span data-ttu-id="825d6-813">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="825d6-813">section:section0:key:key1</span></span>
* <span data-ttu-id="825d6-814">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="825d6-814">section:section1:key:key0</span></span>
* <span data-ttu-id="825d6-815">section:section1:key:key1</span><span class="sxs-lookup"><span data-stu-id="825d6-815">section:section1:key:key1</span></span>

<span data-ttu-id="825d6-816">Les attributs peuvent être utilisés pour fournir des valeurs :</span><span class="sxs-lookup"><span data-stu-id="825d6-816">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="825d6-817">Le fichier de configuration précédent charge les clés suivantes avec `value` :</span><span class="sxs-lookup"><span data-stu-id="825d6-817">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="825d6-818">key:attribute</span><span class="sxs-lookup"><span data-stu-id="825d6-818">key:attribute</span></span>
* <span data-ttu-id="825d6-819">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="825d6-819">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="825d6-820">Fournisseur de configuration clé par fichier</span><span class="sxs-lookup"><span data-stu-id="825d6-820">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="825d6-821">Le <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> utilise les fichiers d’un répertoire en tant que paires clé-valeur de configuration.</span><span class="sxs-lookup"><span data-stu-id="825d6-821">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="825d6-822">La clé est le nom de fichier.</span><span class="sxs-lookup"><span data-stu-id="825d6-822">The key is the file name.</span></span> <span data-ttu-id="825d6-823">La valeur contient le contenu du fichier.</span><span class="sxs-lookup"><span data-stu-id="825d6-823">The value contains the file's contents.</span></span> <span data-ttu-id="825d6-824">Le Fournisseur de configuration Clé par fichier est utilisé dans les scénarios d’hébergement de Docker.</span><span class="sxs-lookup"><span data-stu-id="825d6-824">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="825d6-825">Pour activer la configuration clé par fichier, appelez la méthode d’extension <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> sur une instance de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="825d6-825">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="825d6-826">Le `directoryPath` aux fichiers doit être un chemin d’accès absolu.</span><span class="sxs-lookup"><span data-stu-id="825d6-826">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="825d6-827">Les surcharges permettent de spécifier :</span><span class="sxs-lookup"><span data-stu-id="825d6-827">Overloads permit specifying:</span></span>

* <span data-ttu-id="825d6-828">Un délégué `Action<KeyPerFileConfigurationSource>` qui configure la source.</span><span class="sxs-lookup"><span data-stu-id="825d6-828">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="825d6-829">Si le répertoire est facultatif et le chemin d’accès au répertoire.</span><span class="sxs-lookup"><span data-stu-id="825d6-829">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="825d6-830">Le double trait de soulignement (`__`) est utilisé comme un délimiteur de clé de configuration dans les noms de fichiers.</span><span class="sxs-lookup"><span data-stu-id="825d6-830">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="825d6-831">Par exemple, le nom de fichier `Logging__LogLevel__System` génère la clé de configuration `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="825d6-831">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="825d6-832">Appelez `ConfigureAppConfiguration` lors de la création de l’hôte pour spécifier la configuration de l’application :</span><span class="sxs-lookup"><span data-stu-id="825d6-832">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="825d6-833">Fournisseur de configuration de mémoire</span><span class="sxs-lookup"><span data-stu-id="825d6-833">Memory Configuration Provider</span></span>

<span data-ttu-id="825d6-834">Le <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> utilise une collection en mémoire en tant que paires clé-valeur de configuration.</span><span class="sxs-lookup"><span data-stu-id="825d6-834">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="825d6-835">Pour activer la configuration de la collection en mémoire, appelez la méthode d’extension <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> sur une instance de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="825d6-835">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="825d6-836">Le fournisseur de configuration peut être initialisé avec un `IEnumerable<KeyValuePair<String,String>>`.</span><span class="sxs-lookup"><span data-stu-id="825d6-836">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="825d6-837">Appelez `ConfigureAppConfiguration` lors de la création de l’hôte pour spécifier la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="825d6-837">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="825d6-838">Dans l’exemple suivant, un dictionnaire de configuration est créé :</span><span class="sxs-lookup"><span data-stu-id="825d6-838">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="825d6-839">Le dictionnaire est utilisé avec un appel à `AddInMemoryCollection` pour fournir la configuration :</span><span class="sxs-lookup"><span data-stu-id="825d6-839">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="825d6-840">GetValue</span><span class="sxs-lookup"><span data-stu-id="825d6-840">GetValue</span></span>

<span data-ttu-id="825d6-841">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*)extrait une valeur unique de la configuration avec une clé spécifiée et la convertit en type de non-collection spécifié.</span><span class="sxs-lookup"><span data-stu-id="825d6-841">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="825d6-842">Une surcharge accepte une valeur par défaut.</span><span class="sxs-lookup"><span data-stu-id="825d6-842">An overload accepts a default value.</span></span>

<span data-ttu-id="825d6-843">L’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="825d6-843">The following example:</span></span>

* <span data-ttu-id="825d6-844">Extrait la valeur de chaîne de la configuration avec la clé `NumberKey`.</span><span class="sxs-lookup"><span data-stu-id="825d6-844">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="825d6-845">Si `NumberKey` est introuvable dans les clés de configuration, la valeur par défaut de `99` est utilisée.</span><span class="sxs-lookup"><span data-stu-id="825d6-845">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="825d6-846">Tape la valeur comme `int`.</span><span class="sxs-lookup"><span data-stu-id="825d6-846">Types the value as an `int`.</span></span>
* <span data-ttu-id="825d6-847">Stocke la valeur dans la propriété `NumberConfig` pour une utilisation par la page.</span><span class="sxs-lookup"><span data-stu-id="825d6-847">Stores the value in the `NumberConfig` property for use by the page.</span></span>

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

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="825d6-848">GetSection, GetChildren et Exists</span><span class="sxs-lookup"><span data-stu-id="825d6-848">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="825d6-849">Pour les exemples qui suivent, utilisez le fichier JSON suivant.</span><span class="sxs-lookup"><span data-stu-id="825d6-849">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="825d6-850">Quatre clés se trouvent dans deux sections, dont l’une inclut deux sous-sections :</span><span class="sxs-lookup"><span data-stu-id="825d6-850">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="825d6-851">Lorsque le fichier est lu dans la configuration, les clés hiérarchiques uniques suivantes sont créées pour stocker les valeurs de la configuration :</span><span class="sxs-lookup"><span data-stu-id="825d6-851">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="825d6-852">section0:key0</span><span class="sxs-lookup"><span data-stu-id="825d6-852">section0:key0</span></span>
* <span data-ttu-id="825d6-853">section0:key1</span><span class="sxs-lookup"><span data-stu-id="825d6-853">section0:key1</span></span>
* <span data-ttu-id="825d6-854">section1:key0</span><span class="sxs-lookup"><span data-stu-id="825d6-854">section1:key0</span></span>
* <span data-ttu-id="825d6-855">section1:key1</span><span class="sxs-lookup"><span data-stu-id="825d6-855">section1:key1</span></span>
* <span data-ttu-id="825d6-856">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="825d6-856">section2:subsection0:key0</span></span>
* <span data-ttu-id="825d6-857">section2:subsection0:key1</span><span class="sxs-lookup"><span data-stu-id="825d6-857">section2:subsection0:key1</span></span>
* <span data-ttu-id="825d6-858">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="825d6-858">section2:subsection1:key0</span></span>
* <span data-ttu-id="825d6-859">section2:subsection1:key1</span><span class="sxs-lookup"><span data-stu-id="825d6-859">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="825d6-860">GetSection</span><span class="sxs-lookup"><span data-stu-id="825d6-860">GetSection</span></span>

<span data-ttu-id="825d6-861">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extrait une sous-section de la configuration avec la clé de sous-section spécifiée.</span><span class="sxs-lookup"><span data-stu-id="825d6-861">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="825d6-862">Pour retourner un <xref:Microsoft.Extensions.Configuration.IConfigurationSection> contenant uniquement les paires clé-valeur dans `section1`, appelez `GetSection` et fournissez le nom de section :</span><span class="sxs-lookup"><span data-stu-id="825d6-862">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="825d6-863">`configSection` n’a pas de valeur, seulement une clé et un chemin.</span><span class="sxs-lookup"><span data-stu-id="825d6-863">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="825d6-864">De même, pour obtenir les valeurs des clés dans `section2:subsection0`, appelez `GetSection` et fournissez le chemin d’accès de la section :</span><span class="sxs-lookup"><span data-stu-id="825d6-864">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="825d6-865">`GetSection` ne retourne jamais `null`.</span><span class="sxs-lookup"><span data-stu-id="825d6-865">`GetSection` never returns `null`.</span></span> <span data-ttu-id="825d6-866">Si aucune section correspondante n’est trouvée, une valeur `IConfigurationSection` vide est retournée.</span><span class="sxs-lookup"><span data-stu-id="825d6-866">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="825d6-867">Quand `GetSection` retourne une section correspondante, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> n’est pas rempli.</span><span class="sxs-lookup"><span data-stu-id="825d6-867">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="825d6-868"><xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> et <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> sont retournés quand la section existe.</span><span class="sxs-lookup"><span data-stu-id="825d6-868">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="825d6-869">GetChildren</span><span class="sxs-lookup"><span data-stu-id="825d6-869">GetChildren</span></span>

<span data-ttu-id="825d6-870">Un appel à [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) sur `section2` obtient un `IEnumerable<IConfigurationSection>` qui inclut :</span><span class="sxs-lookup"><span data-stu-id="825d6-870">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="825d6-871">Exists</span><span class="sxs-lookup"><span data-stu-id="825d6-871">Exists</span></span>

<span data-ttu-id="825d6-872">Utilisez [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) pour déterminer si une section de configuration existe :</span><span class="sxs-lookup"><span data-stu-id="825d6-872">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="825d6-873">Compte tenu des données d’exemple, `sectionExists` est `false`, car il n’y a pas de section `section2:subsection2` dans les données de configuration.</span><span class="sxs-lookup"><span data-stu-id="825d6-873">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="825d6-874">Établir une liaison à un graphe d’objets</span><span class="sxs-lookup"><span data-stu-id="825d6-874">Bind to an object graph</span></span>

<span data-ttu-id="825d6-875"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> est capable de lier l’intégralité d’un graphe d’objets POCO.</span><span class="sxs-lookup"><span data-stu-id="825d6-875"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="825d6-876">Comme pour la liaison d’un objet simple, seules les propriétés accessibles en lecture/écriture publiques sont liées.</span><span class="sxs-lookup"><span data-stu-id="825d6-876">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="825d6-877">L’exemple contient un modèle `TvShow` dont le graphe d’objets inclut les classes `Metadata` et `Actors` (*Models/TvShow.cs*) :</span><span class="sxs-lookup"><span data-stu-id="825d6-877">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="825d6-878">L’exemple d’application a un fichier *tvshow.xml* contenant les données de configuration :</span><span class="sxs-lookup"><span data-stu-id="825d6-878">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="825d6-879">La configuration est liée à l’intégralité du graphe d’objets `TvShow` avec la méthode `Bind`.</span><span class="sxs-lookup"><span data-stu-id="825d6-879">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="825d6-880">L’instance liée est affectée à une propriété pour le rendu :</span><span class="sxs-lookup"><span data-stu-id="825d6-880">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="825d6-881">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)lie et retourne le type spécifié.</span><span class="sxs-lookup"><span data-stu-id="825d6-881">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="825d6-882">Il est plus pratique d’utiliser `Get<T>` que `Bind`.</span><span class="sxs-lookup"><span data-stu-id="825d6-882">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="825d6-883">Le code suivant montre comment utiliser `Get<T>` avec l’exemple précédent :</span><span class="sxs-lookup"><span data-stu-id="825d6-883">The following code shows how to use `Get<T>` with the preceding example:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="825d6-884">Lier un tableau à une classe</span><span class="sxs-lookup"><span data-stu-id="825d6-884">Bind an array to a class</span></span>

<span data-ttu-id="825d6-885">*L’exemple d’application illustre les concepts abordés dans cette section.*</span><span class="sxs-lookup"><span data-stu-id="825d6-885">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="825d6-886"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> prend en charge la liaison de tableaux à des objets à l’aide d’index de tableau dans les clés de configuration.</span><span class="sxs-lookup"><span data-stu-id="825d6-886">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="825d6-887">Tout format de tableau qui expose un segment de clé`:0:`numérique `:1:`( &hellip; `:{n}:`,,) est capable d’effectuer une liaison de tableau à un tableau de classes POCO.</span><span class="sxs-lookup"><span data-stu-id="825d6-887">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="825d6-888">La liaison est fournie par convention.</span><span class="sxs-lookup"><span data-stu-id="825d6-888">Binding is provided by convention.</span></span> <span data-ttu-id="825d6-889">Les fournisseurs de configuration personnalisés ne sont pas obligés d’implémenter la liaison de tableau.</span><span class="sxs-lookup"><span data-stu-id="825d6-889">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="825d6-890">**Traitement de tableau en mémoire**</span><span class="sxs-lookup"><span data-stu-id="825d6-890">**In-memory array processing**</span></span>

<span data-ttu-id="825d6-891">Observez les valeurs et les clés de configuration indiquées dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="825d6-891">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="825d6-892">Clé</span><span class="sxs-lookup"><span data-stu-id="825d6-892">Key</span></span>             | <span data-ttu-id="825d6-893">Valeur</span><span class="sxs-lookup"><span data-stu-id="825d6-893">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="825d6-894">array:entries:0</span><span class="sxs-lookup"><span data-stu-id="825d6-894">array:entries:0</span></span> | <span data-ttu-id="825d6-895">value0</span><span class="sxs-lookup"><span data-stu-id="825d6-895">value0</span></span> |
| <span data-ttu-id="825d6-896">array:entries:1</span><span class="sxs-lookup"><span data-stu-id="825d6-896">array:entries:1</span></span> | <span data-ttu-id="825d6-897">valeur1</span><span class="sxs-lookup"><span data-stu-id="825d6-897">value1</span></span> |
| <span data-ttu-id="825d6-898">array:entries:2</span><span class="sxs-lookup"><span data-stu-id="825d6-898">array:entries:2</span></span> | <span data-ttu-id="825d6-899">valeur2</span><span class="sxs-lookup"><span data-stu-id="825d6-899">value2</span></span> |
| <span data-ttu-id="825d6-900">array:entries:4</span><span class="sxs-lookup"><span data-stu-id="825d6-900">array:entries:4</span></span> | <span data-ttu-id="825d6-901">value4</span><span class="sxs-lookup"><span data-stu-id="825d6-901">value4</span></span> |
| <span data-ttu-id="825d6-902">array:entries:5</span><span class="sxs-lookup"><span data-stu-id="825d6-902">array:entries:5</span></span> | <span data-ttu-id="825d6-903">value5</span><span class="sxs-lookup"><span data-stu-id="825d6-903">value5</span></span> |

<span data-ttu-id="825d6-904">Ces clés et valeurs sont chargées dans l’exemple d’application à l’aide du Fournisseur de configuration de mémoire :</span><span class="sxs-lookup"><span data-stu-id="825d6-904">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="825d6-905">Le tableau ignore une valeur pour l’index &num;3.</span><span class="sxs-lookup"><span data-stu-id="825d6-905">The array skips a value for index &num;3.</span></span> <span data-ttu-id="825d6-906">Le binder de configuration n’est pas capable de lier des valeurs null ou de créer des entrées null dans les objets liés, ce qui deviendra clair dans un moment lorsque le résultat de liaison de ce tableau à un objet est illustré.</span><span class="sxs-lookup"><span data-stu-id="825d6-906">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="825d6-907">Dans l’exemple d’application, une classe POCO est disponible pour contenir les données de configuration liées :</span><span class="sxs-lookup"><span data-stu-id="825d6-907">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="825d6-908">Les données de configuration sont liées à l’objet :</span><span class="sxs-lookup"><span data-stu-id="825d6-908">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="825d6-909">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)la syntaxe peut également être utilisée, ce qui se traduit par un code plus compact :</span><span class="sxs-lookup"><span data-stu-id="825d6-909">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="825d6-910">L’objet lié, une instance de `ArrayExample`, reçoit les données de tableau à partir de la configuration.</span><span class="sxs-lookup"><span data-stu-id="825d6-910">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="825d6-911">Index `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="825d6-911">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="825d6-912">`ArrayExample.Entries` Valeur</span><span class="sxs-lookup"><span data-stu-id="825d6-912">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="825d6-913">0</span><span class="sxs-lookup"><span data-stu-id="825d6-913">0</span></span>                            | <span data-ttu-id="825d6-914">value0</span><span class="sxs-lookup"><span data-stu-id="825d6-914">value0</span></span>                       |
| <span data-ttu-id="825d6-915">1</span><span class="sxs-lookup"><span data-stu-id="825d6-915">1</span></span>                            | <span data-ttu-id="825d6-916">valeur1</span><span class="sxs-lookup"><span data-stu-id="825d6-916">value1</span></span>                       |
| <span data-ttu-id="825d6-917">2</span><span class="sxs-lookup"><span data-stu-id="825d6-917">2</span></span>                            | <span data-ttu-id="825d6-918">valeur2</span><span class="sxs-lookup"><span data-stu-id="825d6-918">value2</span></span>                       |
| <span data-ttu-id="825d6-919">3</span><span class="sxs-lookup"><span data-stu-id="825d6-919">3</span></span>                            | <span data-ttu-id="825d6-920">value4</span><span class="sxs-lookup"><span data-stu-id="825d6-920">value4</span></span>                       |
| <span data-ttu-id="825d6-921">4</span><span class="sxs-lookup"><span data-stu-id="825d6-921">4</span></span>                            | <span data-ttu-id="825d6-922">value5</span><span class="sxs-lookup"><span data-stu-id="825d6-922">value5</span></span>                       |

<span data-ttu-id="825d6-923">L’index &num;3 dans l’objet lié contient les données de configuration pour la clé de configuration `array:4` et sa valeur de `value4`.</span><span class="sxs-lookup"><span data-stu-id="825d6-923">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="825d6-924">Lorsque des données de configuration contenant un tableau sont liées, les index de tableau dans les clés de configuration sont simplement utilisés pour itérer les données de configuration lors de la création de l’objet.</span><span class="sxs-lookup"><span data-stu-id="825d6-924">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="825d6-925">Une valeur null ne peut pas être conservée dans des données de configuration, et une entrée à valeur null n’est pas créée dans un objet lié quand un tableau dans des clés de configuration ignore un ou plusieurs index.</span><span class="sxs-lookup"><span data-stu-id="825d6-925">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="825d6-926">L’élément de configuration manquant pour l’index &num;3 peut être fourni avant la liaison à l’instance `ArrayExample` par n’importe quel fournisseur de configuration qui génère la paire clé-valeur correcte dans la configuration.</span><span class="sxs-lookup"><span data-stu-id="825d6-926">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="825d6-927">Si l’exemple inclut un Fournisseur de configuration JSON supplémentaire avec la paire clé-valeur manquante, `ArrayExample.Entries` correspond à l’intégralité du tableau de configuration :</span><span class="sxs-lookup"><span data-stu-id="825d6-927">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="825d6-928">*missing_value.json* :</span><span class="sxs-lookup"><span data-stu-id="825d6-928">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="825d6-929">Dans `ConfigureAppConfiguration` :</span><span class="sxs-lookup"><span data-stu-id="825d6-929">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="825d6-930">La paire clé-valeur indiquée dans le tableau est chargée dans la configuration.</span><span class="sxs-lookup"><span data-stu-id="825d6-930">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="825d6-931">Clé</span><span class="sxs-lookup"><span data-stu-id="825d6-931">Key</span></span>             | <span data-ttu-id="825d6-932">Valeur</span><span class="sxs-lookup"><span data-stu-id="825d6-932">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="825d6-933">array:entries:3</span><span class="sxs-lookup"><span data-stu-id="825d6-933">array:entries:3</span></span> | <span data-ttu-id="825d6-934">valeur3</span><span class="sxs-lookup"><span data-stu-id="825d6-934">value3</span></span> |

<span data-ttu-id="825d6-935">Si l’instance de classe `ArrayExample` est liée une fois que le Fournisseur de configuration JSON inclut l’entrée pour l’index &num;3, le tableau `ArrayExample.Entries` inclut la valeur.</span><span class="sxs-lookup"><span data-stu-id="825d6-935">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="825d6-936">Index `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="825d6-936">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="825d6-937">`ArrayExample.Entries` Valeur</span><span class="sxs-lookup"><span data-stu-id="825d6-937">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="825d6-938">0</span><span class="sxs-lookup"><span data-stu-id="825d6-938">0</span></span>                            | <span data-ttu-id="825d6-939">value0</span><span class="sxs-lookup"><span data-stu-id="825d6-939">value0</span></span>                       |
| <span data-ttu-id="825d6-940">1</span><span class="sxs-lookup"><span data-stu-id="825d6-940">1</span></span>                            | <span data-ttu-id="825d6-941">valeur1</span><span class="sxs-lookup"><span data-stu-id="825d6-941">value1</span></span>                       |
| <span data-ttu-id="825d6-942">2</span><span class="sxs-lookup"><span data-stu-id="825d6-942">2</span></span>                            | <span data-ttu-id="825d6-943">valeur2</span><span class="sxs-lookup"><span data-stu-id="825d6-943">value2</span></span>                       |
| <span data-ttu-id="825d6-944">3</span><span class="sxs-lookup"><span data-stu-id="825d6-944">3</span></span>                            | <span data-ttu-id="825d6-945">valeur3</span><span class="sxs-lookup"><span data-stu-id="825d6-945">value3</span></span>                       |
| <span data-ttu-id="825d6-946">4</span><span class="sxs-lookup"><span data-stu-id="825d6-946">4</span></span>                            | <span data-ttu-id="825d6-947">value4</span><span class="sxs-lookup"><span data-stu-id="825d6-947">value4</span></span>                       |
| <span data-ttu-id="825d6-948">5</span><span class="sxs-lookup"><span data-stu-id="825d6-948">5</span></span>                            | <span data-ttu-id="825d6-949">value5</span><span class="sxs-lookup"><span data-stu-id="825d6-949">value5</span></span>                       |

<span data-ttu-id="825d6-950">**Traitement de tableau JSON**</span><span class="sxs-lookup"><span data-stu-id="825d6-950">**JSON array processing**</span></span>

<span data-ttu-id="825d6-951">Si un fichier JSON contient un tableau, les clés de configuration sont créés pour les éléments du tableau avec un index de section basé sur zéro.</span><span class="sxs-lookup"><span data-stu-id="825d6-951">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="825d6-952">Dans le fichier de configuration suivant, `subsection` est un tableau :</span><span class="sxs-lookup"><span data-stu-id="825d6-952">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="825d6-953">Le Fournisseur de configuration JSON lit les données de configuration dans les paires clé-valeur suivantes :</span><span class="sxs-lookup"><span data-stu-id="825d6-953">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="825d6-954">Clé</span><span class="sxs-lookup"><span data-stu-id="825d6-954">Key</span></span>                     | <span data-ttu-id="825d6-955">Valeur</span><span class="sxs-lookup"><span data-stu-id="825d6-955">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="825d6-956">json_array:key</span><span class="sxs-lookup"><span data-stu-id="825d6-956">json_array:key</span></span>          | <span data-ttu-id="825d6-957">valueA</span><span class="sxs-lookup"><span data-stu-id="825d6-957">valueA</span></span> |
| <span data-ttu-id="825d6-958">json_array:subsection:0</span><span class="sxs-lookup"><span data-stu-id="825d6-958">json_array:subsection:0</span></span> | <span data-ttu-id="825d6-959">valueB</span><span class="sxs-lookup"><span data-stu-id="825d6-959">valueB</span></span> |
| <span data-ttu-id="825d6-960">json_array:subsection:1</span><span class="sxs-lookup"><span data-stu-id="825d6-960">json_array:subsection:1</span></span> | <span data-ttu-id="825d6-961">valueC</span><span class="sxs-lookup"><span data-stu-id="825d6-961">valueC</span></span> |
| <span data-ttu-id="825d6-962">json_array:subsection:2</span><span class="sxs-lookup"><span data-stu-id="825d6-962">json_array:subsection:2</span></span> | <span data-ttu-id="825d6-963">valueD</span><span class="sxs-lookup"><span data-stu-id="825d6-963">valueD</span></span> |

<span data-ttu-id="825d6-964">Dans l’exemple d’application, la classe POCO suivante est disponible pour lier les paires clé-valeur de configuration :</span><span class="sxs-lookup"><span data-stu-id="825d6-964">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="825d6-965">Après la liaison, `JsonArrayExample.Key` contient la valeur `valueA`.</span><span class="sxs-lookup"><span data-stu-id="825d6-965">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="825d6-966">Les valeurs de la sous-section sont stockées dans la propriété de tableau POCO, `Subsection`.</span><span class="sxs-lookup"><span data-stu-id="825d6-966">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="825d6-967">Index `JsonArrayExample.Subsection`</span><span class="sxs-lookup"><span data-stu-id="825d6-967">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="825d6-968">`JsonArrayExample.Subsection` Valeur</span><span class="sxs-lookup"><span data-stu-id="825d6-968">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="825d6-969">0</span><span class="sxs-lookup"><span data-stu-id="825d6-969">0</span></span>                                   | <span data-ttu-id="825d6-970">valueB</span><span class="sxs-lookup"><span data-stu-id="825d6-970">valueB</span></span>                              |
| <span data-ttu-id="825d6-971">1</span><span class="sxs-lookup"><span data-stu-id="825d6-971">1</span></span>                                   | <span data-ttu-id="825d6-972">valueC</span><span class="sxs-lookup"><span data-stu-id="825d6-972">valueC</span></span>                              |
| <span data-ttu-id="825d6-973">2</span><span class="sxs-lookup"><span data-stu-id="825d6-973">2</span></span>                                   | <span data-ttu-id="825d6-974">valueD</span><span class="sxs-lookup"><span data-stu-id="825d6-974">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="825d6-975">Fournisseur de configuration personnalisé</span><span class="sxs-lookup"><span data-stu-id="825d6-975">Custom configuration provider</span></span>

<span data-ttu-id="825d6-976">L’exemple d’application montre comment créer un fournisseur de configuration de base qui lit les paires clé-valeur de configuration à partir d’une base de données à l’aide [d’Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="825d6-976">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="825d6-977">Le fournisseur présente les caractéristiques suivantes :</span><span class="sxs-lookup"><span data-stu-id="825d6-977">The provider has the following characteristics:</span></span>

* <span data-ttu-id="825d6-978">La base de données en mémoire EF est utilisée à des fins de démonstration.</span><span class="sxs-lookup"><span data-stu-id="825d6-978">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="825d6-979">Pour utiliser une base de données qui nécessite une chaîne de connexion, implémentez un autre `ConfigurationBuilder` pour fournir la chaîne de connexion à partir d’un autre fournisseur de configuration.</span><span class="sxs-lookup"><span data-stu-id="825d6-979">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="825d6-980">Le fournisseur lit une table de base de données dans la configuration au démarrage.</span><span class="sxs-lookup"><span data-stu-id="825d6-980">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="825d6-981">Le fournisseur n’interroge pas la base de données par clé.</span><span class="sxs-lookup"><span data-stu-id="825d6-981">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="825d6-982">Le rechargement en cas de changement n’est pas implémenté, par conséquent, la mise à jour la base de données après le démarrage de l’application n’a aucun effet sur la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="825d6-982">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="825d6-983">Définissez une entité `EFConfigurationValue` pour le stockage des valeurs de configuration dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="825d6-983">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="825d6-984">*Models/EFConfigurationValue.cs* :</span><span class="sxs-lookup"><span data-stu-id="825d6-984">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="825d6-985">Ajoutez un `EFConfigurationContext` pour stocker les valeurs configurées et y accéder.</span><span class="sxs-lookup"><span data-stu-id="825d6-985">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="825d6-986">*EFConfigurationProvider/EFConfigurationContext.cs* :</span><span class="sxs-lookup"><span data-stu-id="825d6-986">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="825d6-987">Créez une classe qui implémente <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="825d6-987">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="825d6-988">*EFConfigurationProvider/EFConfigurationSource.cs* :</span><span class="sxs-lookup"><span data-stu-id="825d6-988">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="825d6-989">Créez le fournisseur de configuration personnalisé en héritant de <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="825d6-989">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="825d6-990">Le fournisseur de configuration initialise la base de données quand elle est vide.</span><span class="sxs-lookup"><span data-stu-id="825d6-990">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="825d6-991">*EFConfigurationProvider/EFConfigurationProvider.cs* :</span><span class="sxs-lookup"><span data-stu-id="825d6-991">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="825d6-992">Une méthode d’extension `AddEFConfiguration` permet d’ajouter la source de configuration à un `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="825d6-992">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="825d6-993">*Extensions/EntityFrameworkExtensions.cs* :</span><span class="sxs-lookup"><span data-stu-id="825d6-993">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="825d6-994">Le code suivant montre comment utiliser le `EFConfigurationProvider` personnalisé dans *Program.cs* :</span><span class="sxs-lookup"><span data-stu-id="825d6-994">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="825d6-995">Accéder à la configuration lors du démarrage</span><span class="sxs-lookup"><span data-stu-id="825d6-995">Access configuration during startup</span></span>

<span data-ttu-id="825d6-996">Injectez `IConfiguration` dans le constructeur `Startup` pour accéder aux valeurs de configuration dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="825d6-996">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="825d6-997">Pour accéder à la configuration dans `Startup.Configure`, injectez `IConfiguration` directement dans la méthode ou utilisez l’instance à partir du constructeur :</span><span class="sxs-lookup"><span data-stu-id="825d6-997">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="825d6-998">Pour obtenir un exemple d’accès à la configuration à l’aide des méthodes pratiques de démarrage, consultez [Démarrage de l’application : méthodes pratiques](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="825d6-998">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="825d6-999">Configuration de l’accès Razor dans une page pages ou une vue MVC</span><span class="sxs-lookup"><span data-stu-id="825d6-999">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="825d6-1000">Pour accéder aux paramètres de configuration Razor dans une page pages ou une vue MVC, ajoutez une [directive using](xref:mvc/views/razor#using) ([référence C# : directive using](/dotnet/csharp/language-reference/keywords/using-directive)) pour l' [espace de noms Microsoft. extensions. Configuration](xref:Microsoft.Extensions.Configuration) et injectez <xref:Microsoft.Extensions.Configuration.IConfiguration> dans la page ou la vue.</span><span class="sxs-lookup"><span data-stu-id="825d6-1000">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="825d6-1001">Dans une Razor page pages :</span><span class="sxs-lookup"><span data-stu-id="825d6-1001">In a Razor Pages page:</span></span>

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

<span data-ttu-id="825d6-1002">Dans une vue MVC :</span><span class="sxs-lookup"><span data-stu-id="825d6-1002">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="825d6-1003">Ajouter la configuration à partir d’un assembly externe</span><span class="sxs-lookup"><span data-stu-id="825d6-1003">Add configuration from an external assembly</span></span>

<span data-ttu-id="825d6-1004">Une implémentation de <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> permet d’ajouter des améliorations à une application au démarrage à partir d’un assembly externe, en dehors de la classe `Startup` de l’application.</span><span class="sxs-lookup"><span data-stu-id="825d6-1004">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="825d6-1005">Pour plus d’informations, consultez <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="825d6-1005">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="825d6-1006">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="825d6-1006">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end
