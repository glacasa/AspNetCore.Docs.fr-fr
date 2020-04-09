---
title: Configuration dans ASP.NET Core
author: rick-anderson
description: Découvrez comment utiliser l’API de configuration pour configurer une application ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 3/29/2020
uid: fundamentals/configuration/index
ms.openlocfilehash: d76ca78bc988f859b4e99752a0e88735e1df1d82
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80501329"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="2d045-103">Configuration dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2d045-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="2d045-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT) et Kirk [Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="2d045-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="2d045-105">La configuration dans ASP.NET Core est effectuée à l’aide d’un ou [plusieurs fournisseurs de configuration.](#cp)</span><span class="sxs-lookup"><span data-stu-id="2d045-105">Configuration in ASP.NET Core is performed using one or more [configuration providers](#cp).</span></span> <span data-ttu-id="2d045-106">Les fournisseurs de configuration lisent les données de configuration des paires de valeurs clés à l’aide d’une variété de sources de configuration :</span><span class="sxs-lookup"><span data-stu-id="2d045-106">Configuration providers read configuration data from key-value pairs using a variety of configuration sources:</span></span>

* <span data-ttu-id="2d045-107">Paramètres de fichiers, tels que *appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="2d045-107">Settings files, such as *appsettings.json*</span></span>
* <span data-ttu-id="2d045-108">Variables d'environnement</span><span class="sxs-lookup"><span data-stu-id="2d045-108">Environment variables</span></span>
* <span data-ttu-id="2d045-109">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="2d045-109">Azure Key Vault</span></span>
* <span data-ttu-id="2d045-110">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="2d045-110">Azure App Configuration</span></span>
* <span data-ttu-id="2d045-111">Arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="2d045-111">Command-line arguments</span></span>
* <span data-ttu-id="2d045-112">Fournisseurs personnalisés, installés ou créés</span><span class="sxs-lookup"><span data-stu-id="2d045-112">Custom providers, installed or created</span></span>
* <span data-ttu-id="2d045-113">Fichiers de répertoire</span><span class="sxs-lookup"><span data-stu-id="2d045-113">Directory files</span></span>
* <span data-ttu-id="2d045-114">Objets .NET en mémoire</span><span class="sxs-lookup"><span data-stu-id="2d045-114">In-memory .NET objects</span></span>

<span data-ttu-id="2d045-115">[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([comment télécharger](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2d045-115">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="default"></a>

## <a name="default-configuration"></a><span data-ttu-id="2d045-116">Configuration par défaut</span><span class="sxs-lookup"><span data-stu-id="2d045-116">Default configuration</span></span>

<span data-ttu-id="2d045-117">ASP.NET applications Web Core créées avec [dotnet nouveau](/dotnet/core/tools/dotnet-new) ou Visual Studio génèrent le code suivant :</span><span class="sxs-lookup"><span data-stu-id="2d045-117">ASP.NET Core web apps created with [dotnet new](/dotnet/core/tools/dotnet-new) or Visual Studio generate the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet&highlight=9)]

 <span data-ttu-id="2d045-118"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> fournit la configuration par défaut de l’application dans l’ordre suivant :</span><span class="sxs-lookup"><span data-stu-id="2d045-118"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> provides default configuration for the app in the following order:</span></span>

1. <span data-ttu-id="2d045-119">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) : Ajoute `IConfiguration` un existant comme source.</span><span class="sxs-lookup"><span data-stu-id="2d045-119">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) :  Adds an existing `IConfiguration` as a source.</span></span> <span data-ttu-id="2d045-120">Dans le cas de configuration par défaut, ajoute la configuration [de l’hôte](#hvac) et la définit comme la première source pour la configuration _de l’application._</span><span class="sxs-lookup"><span data-stu-id="2d045-120">In the default configuration case, adds the [host](#hvac) configuration and setting it as the first source for the _app_ configuration.</span></span>
1. <span data-ttu-id="2d045-121">[appsettings.json](#appsettingsjson) à l’aide du [fournisseur de configuration JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="2d045-121">[appsettings.json](#appsettingsjson) using the [JSON configuration provider](#file-configuration-provider).</span></span>
1. <span data-ttu-id="2d045-122">*applications.* `Environment` *.json* en utilisant le [fournisseur de configuration JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="2d045-122">*appsettings.*`Environment`*.json* using the [JSON configuration provider](#file-configuration-provider).</span></span> <span data-ttu-id="2d045-123">Par exemple, *appsettings*. ***Production***. *json* et *appsettings*. ***Développement***. *json*.</span><span class="sxs-lookup"><span data-stu-id="2d045-123">For example, *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json*.</span></span>
1. <span data-ttu-id="2d045-124">[Secrets d’application](xref:security/app-secrets) lorsque `Development` l’application s’exécute dans l’environnement.</span><span class="sxs-lookup"><span data-stu-id="2d045-124">[App secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
1. <span data-ttu-id="2d045-125">Variables de l’environnement à l’aide du [fournisseur de configuration Environment Variables](#evcp).</span><span class="sxs-lookup"><span data-stu-id="2d045-125">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="2d045-126">Arguments de ligne de commande utilisant le [fournisseur de configuration de ligne de commande.](#command-line)</span><span class="sxs-lookup"><span data-stu-id="2d045-126">Command-line arguments using the [Command-line configuration provider](#command-line).</span></span>

<span data-ttu-id="2d045-127">Les fournisseurs de configuration qui sont ajoutés plus tard remplacent les paramètres clés précédents.</span><span class="sxs-lookup"><span data-stu-id="2d045-127">Configuration providers that are added later override previous key settings.</span></span> <span data-ttu-id="2d045-128">Par exemple, `MyKey` si l’on trouve dans les deux *appsettings.json* et l’environnement, la valeur de l’environnement est utilisée.</span><span class="sxs-lookup"><span data-stu-id="2d045-128">For example, if `MyKey` is set in both *appsettings.json* and the environment, the environment value is used.</span></span> <span data-ttu-id="2d045-129">À l’aide des fournisseurs de configuration par défaut, le [fournisseur de configuration de ligne de commande](#command-line-configuration-provider) remplace tous les autres fournisseurs.</span><span class="sxs-lookup"><span data-stu-id="2d045-129">Using the default configuration providers, the  [Command-line configuration provider](#command-line-configuration-provider) overrides all other providers.</span></span>

<span data-ttu-id="2d045-130">Pour plus `CreateDefaultBuilder`d’informations sur , voir [Paramètres de constructeur par défaut](xref:fundamentals/host/generic-host#default-builder-settings).</span><span class="sxs-lookup"><span data-stu-id="2d045-130">For more information on `CreateDefaultBuilder`, see [Default builder settings](xref:fundamentals/host/generic-host#default-builder-settings).</span></span>

<span data-ttu-id="2d045-131">Le code suivant affiche les fournisseurs de configuration activés dans l’ordre qu’ils ont été ajoutés :</span><span class="sxs-lookup"><span data-stu-id="2d045-131">The following code displays the enabled configuration providers in the order they were added:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Index2.cshtml.cs?name=snippet)]

### <a name="appsettingsjson"></a><span data-ttu-id="2d045-132">appsettings.json</span><span class="sxs-lookup"><span data-stu-id="2d045-132">appsettings.json</span></span>

<span data-ttu-id="2d045-133">Considérez le fichier *appsettings.json* suivant :</span><span class="sxs-lookup"><span data-stu-id="2d045-133">Consider the following *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="2d045-134">Le code suivant du [téléchargement de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) affiche plusieurs des paramètres des configurations précédentes :</span><span class="sxs-lookup"><span data-stu-id="2d045-134">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="2d045-135">La <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> configuration des charges par défaut dans l’ordre suivant :</span><span class="sxs-lookup"><span data-stu-id="2d045-135">The default <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration in the following order:</span></span>

1. <span data-ttu-id="2d045-136">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="2d045-136">*appsettings.json*</span></span>
1. <span data-ttu-id="2d045-137">*applications.* `Environment` *.json* : Par exemple, les *appsettings*. ***Production***. *json* et *appsettings*. ***Développement***. *fichiers json.*</span><span class="sxs-lookup"><span data-stu-id="2d045-137">*appsettings.*`Environment`*.json* : For example, the *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json* files.</span></span> <span data-ttu-id="2d045-138">La version environnement du fichier est chargée en fonction de [l’IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="2d045-138">The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="2d045-139">Pour plus d’informations, consultez <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="2d045-139">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="2d045-140">*appsettings*. `Environment`. *json* valeurs remplacer les touches dans *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="2d045-140">*appsettings*.`Environment`.*json* values override keys in *appsettings.json*.</span></span> <span data-ttu-id="2d045-141">Par exemple, par défaut :</span><span class="sxs-lookup"><span data-stu-id="2d045-141">For example, by default:</span></span>

* <span data-ttu-id="2d045-142">En développement, *appsettings*. ***Développement***. *json* configuration surécrit les valeurs trouvées dans *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="2d045-142">In development, *appsettings*.***Development***.*json* configuration overwrites values found in *appsettings.json*.</span></span>
* <span data-ttu-id="2d045-143">En production, *appsettings*. ***Production***. *json* configuration surécrit les valeurs trouvées dans *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="2d045-143">In production, *appsettings*.***Production***.*json* configuration overwrites values found in *appsettings.json*.</span></span> <span data-ttu-id="2d045-144">Par exemple, lors du déploiement de l’application à Azure.</span><span class="sxs-lookup"><span data-stu-id="2d045-144">For example, when deploying the app to Azure.</span></span>

<a name="optpat"></a>

#### <a name="bind-hierarchical-configuration-data-using-the-options-pattern"></a><span data-ttu-id="2d045-145">Lier les données hiérarchiques de configuration à l’aide du modèle d’options</span><span class="sxs-lookup"><span data-stu-id="2d045-145">Bind hierarchical configuration data using the options pattern</span></span>

<span data-ttu-id="2d045-146">La façon préférée de lire les valeurs de configuration connexes est d’utiliser le [modèle d’options](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="2d045-146">The preferred way to read related configuration values is using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="2d045-147">Par exemple, pour lire les valeurs de configuration suivantes :</span><span class="sxs-lookup"><span data-stu-id="2d045-147">For example, to read the following configuration values:</span></span>

```json
  "Position": {
    "Title": "Editor",
    "Name": "Joe Smith"
  }
```

<span data-ttu-id="2d045-148">Créez la `PositionOptions` classe suivante :</span><span class="sxs-lookup"><span data-stu-id="2d045-148">Create the following `PositionOptions` class:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Options/PositionOptions.cs?name=snippet)]

<span data-ttu-id="2d045-149">Toutes les propriétés publiques de lecture-écriture du type sont liées.</span><span class="sxs-lookup"><span data-stu-id="2d045-149">All the public read-write properties of the type are bound.</span></span> <span data-ttu-id="2d045-150">Les champs ne sont ***pas*** liés.</span><span class="sxs-lookup"><span data-stu-id="2d045-150">Fields are ***not*** bound.</span></span>

<span data-ttu-id="2d045-151">Le code suivant :</span><span class="sxs-lookup"><span data-stu-id="2d045-151">The following code:</span></span>

* <span data-ttu-id="2d045-152">Appelle [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) pour `PositionOptions` lier `Position` la classe à la section.</span><span class="sxs-lookup"><span data-stu-id="2d045-152">Calls [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) to bind the `PositionOptions` class to the `Position` section.</span></span>
* <span data-ttu-id="2d045-153">Affiche `Position` les données de configuration.</span><span class="sxs-lookup"><span data-stu-id="2d045-153">Displays the `Position` configuration data.</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test22.cshtml.cs?name=snippet)]

<span data-ttu-id="2d045-154">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)lie et renvoie le type spécifié.</span><span class="sxs-lookup"><span data-stu-id="2d045-154">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="2d045-155">`ConfigurationBinder.Get<T>`peut être plus `ConfigurationBinder.Bind`pratique que d’utiliser .</span><span class="sxs-lookup"><span data-stu-id="2d045-155">`ConfigurationBinder.Get<T>` may be more convenient than using `ConfigurationBinder.Bind`.</span></span> <span data-ttu-id="2d045-156">Le code suivant montre `ConfigurationBinder.Get<T>` comment `PositionOptions` utiliser avec la classe :</span><span class="sxs-lookup"><span data-stu-id="2d045-156">The following code shows how to use `ConfigurationBinder.Get<T>` with the `PositionOptions` class:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test21.cshtml.cs?name=snippet)]

<span data-ttu-id="2d045-157">Une approche alternative lors de l’utilisation du ***modèle d’options*** est de lier la `Position` section et de l’ajouter au conteneur de service [d’injection de dépendance](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="2d045-157">An alternative approach when using the ***options pattern*** is to bind the `Position` section and add it to the [dependency injection service container](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="2d045-158">Dans le code `PositionOptions` suivant, est ajouté <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> au conteneur de service avec et lié à la configuration:</span><span class="sxs-lookup"><span data-stu-id="2d045-158">In the following code, `PositionOptions` is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Startup.cs?name=snippet)]

<span data-ttu-id="2d045-159">À l’aide du code précédent, le code suivant lit les options de position :</span><span class="sxs-lookup"><span data-stu-id="2d045-159">Using the preceding code, the following code reads the position options:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test2.cshtml.cs?name=snippet)]

<span data-ttu-id="2d045-160">En utilisant la configuration [par défaut,](#default) les *appsettings.json* et *appsettings.* `Environment` *.json* fichiers sont activés avec [rechargeOnChange: vrai](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span><span class="sxs-lookup"><span data-stu-id="2d045-160">Using the [default](#default) configuration, the *appsettings.json* and *appsettings.*`Environment`*.json* files are enabled with [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span></span> <span data-ttu-id="2d045-161">Modifications apportées aux *appsettings.json* et *aux appsettings.* `Environment` *.json* fichier ***après*** le démarrage de l’application sont lus par le [fournisseur de configuration JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="2d045-161">Changes made to the *appsettings.json* and *appsettings.*`Environment`*.json* file ***after*** the app starts are read by the [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="2d045-162">Consultez [le fournisseur de configuration JSON](#jcp) dans ce document pour obtenir des informations sur l’ajout de fichiers de configuration JSON supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="2d045-162">See [JSON configuration provider](#jcp) in this document for information on adding additional JSON configuration files.</span></span>

<a name="security"></a>

## <a name="security-and-secret-manager"></a><span data-ttu-id="2d045-163">Sécurité et gestionnaire secret</span><span class="sxs-lookup"><span data-stu-id="2d045-163">Security and secret manager</span></span>

<span data-ttu-id="2d045-164">Directives de données de configuration :</span><span class="sxs-lookup"><span data-stu-id="2d045-164">Configuration data guidelines:</span></span>

* <span data-ttu-id="2d045-165">Ne stockez jamais des mots de passe ou d’autres données sensibles dans le code du fournisseur de configuration ou dans les fichiers de configuration en texte clair.</span><span class="sxs-lookup"><span data-stu-id="2d045-165">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span> <span data-ttu-id="2d045-166">Le [gestionnaire secret](xref:security/app-secrets) peut être utilisé pour stocker des secrets dans le développement.</span><span class="sxs-lookup"><span data-stu-id="2d045-166">The [Secret manager](xref:security/app-secrets) can be used to store secrets in development.</span></span>
* <span data-ttu-id="2d045-167">N’utilisez aucun secret de production dans les environnements de développement ou de test.</span><span class="sxs-lookup"><span data-stu-id="2d045-167">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="2d045-168">Spécifiez les secrets en dehors du projet afin qu’ils ne puissent pas être validés par inadvertance dans un référentiel de code source.</span><span class="sxs-lookup"><span data-stu-id="2d045-168">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="2d045-169">Par [défaut,](#default) [Secret Manager](xref:security/app-secrets) lit les paramètres de configuration après *appsettings.json* et *appsettings.* `Environment` *.json*.</span><span class="sxs-lookup"><span data-stu-id="2d045-169">By [default](#default), [Secret manager](xref:security/app-secrets) reads configuration settings after *appsettings.json* and *appsettings.*`Environment`*.json*.</span></span>

<span data-ttu-id="2d045-170">Pour plus d’informations sur le stockage de mots de passe ou d’autres données sensibles :</span><span class="sxs-lookup"><span data-stu-id="2d045-170">For more information on storing passwords or other sensitive data:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="2d045-171"><xref:security/app-secrets>: Inclut des conseils sur l’utilisation des variables de l’environnement pour stocker des données sensibles.</span><span class="sxs-lookup"><span data-stu-id="2d045-171"><xref:security/app-secrets>:  Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="2d045-172">Le Gestionnaire Secret utilise le [fournisseur de configuration de fichiers](#fcp) pour stocker les secrets des utilisateurs dans un fichier JSON sur le système local.</span><span class="sxs-lookup"><span data-stu-id="2d045-172">The Secret Manager uses the [File configuration provider](#fcp) to store user secrets in a JSON file on the local system.</span></span>

<span data-ttu-id="2d045-173">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) stocke en toute sécurité des secrets d’application pour les applications ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2d045-173">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="2d045-174">Pour plus d’informations, consultez <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="2d045-174">For more information, see <xref:security/key-vault-configuration>.</span></span>

<a name="evcp"></a>

## <a name="environment-variables"></a><span data-ttu-id="2d045-175">Variables d'environnement</span><span class="sxs-lookup"><span data-stu-id="2d045-175">Environment variables</span></span>

<span data-ttu-id="2d045-176">En utilisant la <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> configuration [par défaut,](#default) la configuration des charges à partir de paires de clés variables de l’environnement après la lecture *appsettings.json*, *appsettings.* `Environment` *.json*, et [Secret manager](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="2d045-176">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs after reading *appsettings.json*, *appsettings.*`Environment`*.json*, and [Secret manager](xref:security/app-secrets).</span></span> <span data-ttu-id="2d045-177">Par conséquent, les valeurs clés lisent à partir des valeurs de remplacement de l’environnement lues à partir de *appsettings.json*, *appsettings.* `Environment` *.json*, et secret gestionnaire.</span><span class="sxs-lookup"><span data-stu-id="2d045-177">Therefore, key values read from the environment override values read from *appsettings.json*, *appsettings.*`Environment`*.json*, and Secret manager.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="2d045-178">Les `set` commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="2d045-178">The following `set` commands:</span></span>

* <span data-ttu-id="2d045-179">Définissez les clés et les valeurs de l’environnement de [l’exemple précédent](#appsettingsjson) sur Windows.</span><span class="sxs-lookup"><span data-stu-id="2d045-179">Set the environment keys and values of the [preceding example](#appsettingsjson) on Windows.</span></span>
* <span data-ttu-id="2d045-180">Testez les paramètres lors de l’utilisation de [l’échantillon de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span><span class="sxs-lookup"><span data-stu-id="2d045-180">Test the settings when using the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span></span> <span data-ttu-id="2d045-181">La `dotnet run` commande doit être exécutée dans l’annuaire du projet.</span><span class="sxs-lookup"><span data-stu-id="2d045-181">The `dotnet run` command must be run in the project directory.</span></span>

```dotnetcli
set MyKey="My key from Environment"
set Position__Title=Environment_Editor
set Position__Name=Environment_Rick
dotnet run
```

<span data-ttu-id="2d045-182">Les paramètres de l’environnement précédents :</span><span class="sxs-lookup"><span data-stu-id="2d045-182">The preceding environment settings:</span></span>

* <span data-ttu-id="2d045-183">Sont seulement mis en processus lancés à partir de la fenêtre de commande qu’ils ont été mis en.</span><span class="sxs-lookup"><span data-stu-id="2d045-183">Are only set in processes launched from the command window they were set in.</span></span>
* <span data-ttu-id="2d045-184">Ne sera pas lu par les navigateurs lancés avec Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="2d045-184">Won't be read by browsers launched with Visual Studio.</span></span>

<span data-ttu-id="2d045-185">Les commandes [setx](/windows-server/administration/windows-commands/setx) suivantes peuvent être utilisées pour définir les clés et les valeurs de l’environnement sur Windows.</span><span class="sxs-lookup"><span data-stu-id="2d045-185">The following [setx](/windows-server/administration/windows-commands/setx) commands can be used to set the environment keys and values on Windows.</span></span> <span data-ttu-id="2d045-186">Contrairement `set` `setx` à , les paramètres sont persistants.</span><span class="sxs-lookup"><span data-stu-id="2d045-186">Unlike `set`, `setx` settings are persisted.</span></span> <span data-ttu-id="2d045-187">`/M`définit la variable dans l’environnement du système.</span><span class="sxs-lookup"><span data-stu-id="2d045-187">`/M` sets the variable in the system environment.</span></span> <span data-ttu-id="2d045-188">Si `/M` le commutateur n’est pas utilisé, une variable d’environnement utilisateur est définie.</span><span class="sxs-lookup"><span data-stu-id="2d045-188">If the `/M` switch isn't used, a user environment variable is set.</span></span>

```cmd
setx MyKey "My key from setx Environment" /M
setx Position__Title Setx_Environment_Editor /M
setx Position__Name Environment_Rick /M
```

<span data-ttu-id="2d045-189">Pour tester que les commandes précédentes remplacent *appsettings.json* et *appsettings.* `Environment` *.json*:</span><span class="sxs-lookup"><span data-stu-id="2d045-189">To test that the preceding commands override *appsettings.json* and *appsettings.*`Environment`*.json*:</span></span>

* <span data-ttu-id="2d045-190">Avec Visual Studio: Exit and restart Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="2d045-190">With Visual Studio: Exit and restart Visual Studio.</span></span>
* <span data-ttu-id="2d045-191">Avec le CLI: Démarrer une `dotnet run`nouvelle fenêtre de commande et entrer .</span><span class="sxs-lookup"><span data-stu-id="2d045-191">With the CLI: Start a new command window and enter `dotnet run`.</span></span>

<span data-ttu-id="2d045-192">Appelez <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> avec une chaîne pour spécifier un préfixe pour les variables de l’environnement :</span><span class="sxs-lookup"><span data-stu-id="2d045-192">Call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> with a string to specify a prefix for environment variables:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet4&highlight=12)]

<span data-ttu-id="2d045-193">Dans le code précédent :</span><span class="sxs-lookup"><span data-stu-id="2d045-193">In the preceding code:</span></span>

* <span data-ttu-id="2d045-194">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")`est ajouté après les [fournisseurs de configuration par défaut](#default).</span><span class="sxs-lookup"><span data-stu-id="2d045-194">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="2d045-195">Pour un exemple de commande des fournisseurs de configuration, voir [le fournisseur de configuration JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="2d045-195">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>
* <span data-ttu-id="2d045-196">Les variables de `MyCustomPrefix_` l’environnement définies avec le préfixe remplacent les [fournisseurs de configuration par défaut](#default).</span><span class="sxs-lookup"><span data-stu-id="2d045-196">Environment variables set with the `MyCustomPrefix_` prefix override the [default configuration providers](#default).</span></span> <span data-ttu-id="2d045-197">Cela comprend les variables de l’environnement sans le préfixe.</span><span class="sxs-lookup"><span data-stu-id="2d045-197">This includes environment variables without the prefix.</span></span>

<span data-ttu-id="2d045-198">Le préfixe est dépouillé lorsque les paires de valeur clé de configuration sont lues.</span><span class="sxs-lookup"><span data-stu-id="2d045-198">The prefix is stripped off when the configuration key-value pairs are read.</span></span>

<span data-ttu-id="2d045-199">Les commandes suivantes testent le préfixe personnalisé :</span><span class="sxs-lookup"><span data-stu-id="2d045-199">The following commands test the custom prefix:</span></span>

```dotnetcli
set MyCustomPrefix_MyKey="My key with MyCustomPrefix_ Environment"
set MyCustomPrefix_Position__Title=Editor_with_customPrefix
set MyCustomPrefix_Position__Name=Environment_Rick_cp
dotnet run
```

<span data-ttu-id="2d045-200">La [configuration par défaut](#default) charge les variables de `DOTNET_` `ASPNETCORE_`l’environnement et les arguments de ligne de commande préfixés avec et .</span><span class="sxs-lookup"><span data-stu-id="2d045-200">The [default configuration](#default) loads environment variables and command line arguments prefixed with `DOTNET_` and `ASPNETCORE_`.</span></span> <span data-ttu-id="2d045-201">Les `DOTNET_` `ASPNETCORE_` préfixes et les préfixes sont utilisés par ASP.NET Core pour [la configuration de l’hôte et de l’application,](xref:fundamentals/host/generic-host#host-configuration)mais pas pour la configuration de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="2d045-201">The `DOTNET_` and `ASPNETCORE_` prefixes are used by ASP.NET Core for [host and app configuration](xref:fundamentals/host/generic-host#host-configuration), but not for user configuration.</span></span> <span data-ttu-id="2d045-202">Pour plus d’informations sur la configuration de l’hôte et de l’application, voir [.NET Generic Host](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="2d045-202">For more information on host and app configuration, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

<span data-ttu-id="2d045-203">Sur [Azure App Service](https://azure.microsoft.com/services/app-service/), sélectionnez Nouveau **paramètre d’application** sur la page **Paramètres > Configuration.**</span><span class="sxs-lookup"><span data-stu-id="2d045-203">On [Azure App Service](https://azure.microsoft.com/services/app-service/), select **New application setting** on the **Settings > Configuration** page.</span></span> <span data-ttu-id="2d045-204">Les paramètres de l’application Azure App Service sont les :</span><span class="sxs-lookup"><span data-stu-id="2d045-204">Azure App Service application settings are:</span></span>

* <span data-ttu-id="2d045-205">Crypté au repos et transmis sur une chaîne cryptée.</span><span class="sxs-lookup"><span data-stu-id="2d045-205">Encrypted at rest and transmitted over an encrypted channel.</span></span>
* <span data-ttu-id="2d045-206">Exposé comme variables de l’environnement.</span><span class="sxs-lookup"><span data-stu-id="2d045-206">Exposed as environment variables.</span></span>

<span data-ttu-id="2d045-207">Pour plus d’informations, consultez [Azure Apps : remplacer la configuration de l’application à l’aide du portail Azure](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="2d045-207">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="2d045-208">Consultez [les préfixes de chaîne Connection](#constr) pour plus d’informations sur les chaînes de connexion de base de données Azure.</span><span class="sxs-lookup"><span data-stu-id="2d045-208">See [Connection string prefixes](#constr) for information on Azure database connection strings.</span></span>

<a name="clcp"></a>

## <a name="command-line"></a><span data-ttu-id="2d045-209">Ligne de commande</span><span class="sxs-lookup"><span data-stu-id="2d045-209">Command-line</span></span>

<span data-ttu-id="2d045-210">En utilisant la <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> configuration [par défaut,](#default) la configuration des charges à partir de paires de valeur clé d’argument de commande après les sources de configuration suivantes :</span><span class="sxs-lookup"><span data-stu-id="2d045-210">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs after the following configuration sources:</span></span>

* <span data-ttu-id="2d045-211">*appsettings.json* et *appsettings*. `Environment`. *fichiers json.*</span><span class="sxs-lookup"><span data-stu-id="2d045-211">*appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="2d045-212">[Secrets d’application (Secret Manager)](xref:security/app-secrets) dans l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="2d045-212">[App secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="2d045-213">Variables d'environnement.</span><span class="sxs-lookup"><span data-stu-id="2d045-213">Environment variables.</span></span>

<span data-ttu-id="2d045-214">Par [défaut,](#default)les valeurs de configuration définies sur les valeurs de configuration de remplacement de la ligne de commande définies avec tous les autres fournisseurs de configuration.</span><span class="sxs-lookup"><span data-stu-id="2d045-214">By [default](#default), configuration values set on the command-line override configuration values set with all the other configuration providers.</span></span>

### <a name="command-line-arguments"></a><span data-ttu-id="2d045-215">Arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="2d045-215">Command-line arguments</span></span>

<span data-ttu-id="2d045-216">La commande suivante définit `=`les clés et les valeurs à l’aide de :</span><span class="sxs-lookup"><span data-stu-id="2d045-216">The following command sets keys and values using `=`:</span></span>

```dotnetcli
dotnet run MyKey="My key from command line" Position:Title=Cmd Position:Name=Cmd_Rick
```

<span data-ttu-id="2d045-217">La commande suivante définit `/`les clés et les valeurs à l’aide de :</span><span class="sxs-lookup"><span data-stu-id="2d045-217">The following command sets keys and values using `/`:</span></span>

```dotnetcli
dotnet run /MyKey "Using /" /Position:Title=Cmd_ /Position:Name=Cmd_Rick
```

<span data-ttu-id="2d045-218">La commande suivante définit `--`les clés et les valeurs à l’aide de :</span><span class="sxs-lookup"><span data-stu-id="2d045-218">The following command sets keys and values using `--`:</span></span>

```dotnetcli
dotnet run --MyKey "Using --" --Position:Title=Cmd-- --Position:Name=Cmd--Rick
```

<span data-ttu-id="2d045-219">La valeur clé :</span><span class="sxs-lookup"><span data-stu-id="2d045-219">The key value:</span></span>

* <span data-ttu-id="2d045-220">Doit `=`suivre, ou la clé doit `--` `/` avoir un préfixe de ou quand la valeur suit un espace.</span><span class="sxs-lookup"><span data-stu-id="2d045-220">Must follow `=`, or the key must have a prefix of `--` or `/` when the value follows a space.</span></span>
* <span data-ttu-id="2d045-221">N’est pas `=` nécessaire si elle est utilisée.</span><span class="sxs-lookup"><span data-stu-id="2d045-221">Isn't required if `=` is used.</span></span> <span data-ttu-id="2d045-222">Par exemple : `MySetting=`.</span><span class="sxs-lookup"><span data-stu-id="2d045-222">For example, `MySetting=`.</span></span>

<span data-ttu-id="2d045-223">Dans la même commande, ne mélangez pas les paires de valeur clé d’argument de commande-ligne qui utilisent `=` avec des paires de valeur de clé qui utilisent un espace.</span><span class="sxs-lookup"><span data-stu-id="2d045-223">Within the same command, don't mix command-line argument key-value pairs that use `=` with key-value pairs that use a space.</span></span>

### <a name="switch-mappings"></a><span data-ttu-id="2d045-224">Correspondances de commutateur</span><span class="sxs-lookup"><span data-stu-id="2d045-224">Switch mappings</span></span>

<span data-ttu-id="2d045-225">Les cartes de commutation permettent la logique de remplacement de nom **clé.**</span><span class="sxs-lookup"><span data-stu-id="2d045-225">Switch mappings allow **key** name replacement logic.</span></span> <span data-ttu-id="2d045-226">Fournir un dictionnaire de remplacements de commutateur à la <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> méthode.</span><span class="sxs-lookup"><span data-stu-id="2d045-226">Provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="2d045-227">Quand le dictionnaire de correspondances de commutateur est utilisé, il est vérifié afin de déterminer s’il contient une clé correspondant à celle fournie par un argument de ligne de commande.</span><span class="sxs-lookup"><span data-stu-id="2d045-227">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="2d045-228">Si la clé de commande se trouve dans le dictionnaire, la valeur du dictionnaire est transmise pour définir la paire de valeur clé dans la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="2d045-228">If the command-line key is found in the dictionary, the dictionary value is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="2d045-229">Une correspondance de commutateur est nécessaire pour chaque clé de ligne de commande préfixée avec un tiret unique (`-`).</span><span class="sxs-lookup"><span data-stu-id="2d045-229">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="2d045-230">Règles des clés du dictionnaire de correspondances de commutateur :</span><span class="sxs-lookup"><span data-stu-id="2d045-230">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="2d045-231">Les commutateurs `-` doivent `--`commencer par ou .</span><span class="sxs-lookup"><span data-stu-id="2d045-231">Switches must start with `-` or `--`.</span></span>
* <span data-ttu-id="2d045-232">Le dictionnaire de correspondances de commutateur ne doit pas contenir de clés en double.</span><span class="sxs-lookup"><span data-stu-id="2d045-232">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="2d045-233">Pour utiliser un dictionnaire de cartographie des commutateurs, passez-le dans l’appel à `AddCommandLine`:</span><span class="sxs-lookup"><span data-stu-id="2d045-233">To use a switch mappings dictionary, pass it into the call to `AddCommandLine`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramSwitch.cs?name=snippet&highlight=10-18,23)]

<span data-ttu-id="2d045-234">Le code suivant montre les valeurs clés pour les clés remplacées :</span><span class="sxs-lookup"><span data-stu-id="2d045-234">The following code shows the key values for the replaced keys:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test3.cshtml.cs?name=snippet)]

<span data-ttu-id="2d045-235">Exécutez la commande suivante pour tester le remplacement de clé :</span><span class="sxs-lookup"><span data-stu-id="2d045-235">Run the following command to test the key replacement:</span></span>

```dotnetcli
dotnet run -k1=value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="2d045-236">Remarque : `=` Actuellement, ne peut pas être utilisé `-`pour définir des valeurs de remplacement des clés avec un seul tableau de bord .</span><span class="sxs-lookup"><span data-stu-id="2d045-236">Note: Currently, `=` cannot be used to set key-replacement values with a single dash `-`.</span></span> <span data-ttu-id="2d045-237">Consultez [ce problème GitHub](https://github.com/dotnet/extensions/issues/3059).</span><span class="sxs-lookup"><span data-stu-id="2d045-237">See [this GitHub issue](https://github.com/dotnet/extensions/issues/3059).</span></span>

<span data-ttu-id="2d045-238">La commande suivante fonctionne pour tester le remplacement des clés :</span><span class="sxs-lookup"><span data-stu-id="2d045-238">The following command works to test key replacement:</span></span>

```dotnetcli
dotnet run -k1 value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="2d045-239">Pour les applications qui utilisent des mappages de commutateurs, l’appel à `CreateDefaultBuilder` ne doit pas passer d’arguments.</span><span class="sxs-lookup"><span data-stu-id="2d045-239">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="2d045-240">L’appel `CreateDefaultBuilder` `AddCommandLine` de la méthode n’inclut pas les commutateurs cartographiés, et il `CreateDefaultBuilder`n’y a aucun moyen de passer le dictionnaire de commutation-cartographie à .</span><span class="sxs-lookup"><span data-stu-id="2d045-240">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch-mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="2d045-241">La solution n’est pas de `CreateDefaultBuilder` passer les `ConfigurationBuilder` arguments à, mais plutôt de permettre à la méthode de la méthode de `AddCommandLine` traiter à la fois les arguments et le dictionnaire de commutation.</span><span class="sxs-lookup"><span data-stu-id="2d045-241">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch-mapping dictionary.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="2d045-242">Données de configuration hiérarchiques</span><span class="sxs-lookup"><span data-stu-id="2d045-242">Hierarchical configuration data</span></span>

<span data-ttu-id="2d045-243">L’API Configuration lit les données hiérarchiques de configuration en aplatissant les données hiérarchiques avec l’utilisation d’un délimitant dans les touches de configuration.</span><span class="sxs-lookup"><span data-stu-id="2d045-243">The Configuration API reads hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="2d045-244">Le [téléchargement de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contient le fichier *appsettings.json* suivant :</span><span class="sxs-lookup"><span data-stu-id="2d045-244">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="2d045-245">Le code suivant du [téléchargement de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) affiche plusieurs paramètres de configuration :</span><span class="sxs-lookup"><span data-stu-id="2d045-245">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="2d045-246">La meilleure façon de lire les données hiérarchiques de configuration est d’utiliser le modèle d’options.</span><span class="sxs-lookup"><span data-stu-id="2d045-246">The preferred way to read hierarchical configuration data is using the options pattern.</span></span> <span data-ttu-id="2d045-247">Pour plus d’informations, consultez [les données hiérarchiques de configuration bind](#optpat) dans ce document.</span><span class="sxs-lookup"><span data-stu-id="2d045-247">For more information, see [Bind hierarchical configuration data](#optpat) in this document.</span></span>

<span data-ttu-id="2d045-248">Les méthodes <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> et <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> sont disponibles pour isoler les sections et les enfants d’une section dans les données de configuration.</span><span class="sxs-lookup"><span data-stu-id="2d045-248"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="2d045-249">Ces méthodes sont décrites plus loin dans [GetSection, GetChildren et Exists](#getsection).</span><span class="sxs-lookup"><span data-stu-id="2d045-249">These methods are described later in [GetSection, GetChildren, and Exists](#getsection).</span></span>

<!--
[Azure Key Vault configuration provider](xref:security/key-vault-configuration) implement change detection.
-->

## <a name="configuration-keys-and-values"></a><span data-ttu-id="2d045-250">Touches et valeurs de configuration</span><span class="sxs-lookup"><span data-stu-id="2d045-250">Configuration keys and values</span></span>

<span data-ttu-id="2d045-251">Clés de configuration :</span><span class="sxs-lookup"><span data-stu-id="2d045-251">Configuration keys:</span></span>

* <span data-ttu-id="2d045-252">Sont insensibles aux cas.</span><span class="sxs-lookup"><span data-stu-id="2d045-252">Are case-insensitive.</span></span> <span data-ttu-id="2d045-253">Par exemple, `ConnectionString` et `connectionstring` sont traités en tant que clés équivalentes.</span><span class="sxs-lookup"><span data-stu-id="2d045-253">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="2d045-254">Si une clé et une valeur sont définies dans plus d’un fournisseur de configuration, la valeur du dernier fournisseur ajouté est utilisée.</span><span class="sxs-lookup"><span data-stu-id="2d045-254">If a key and value is set in more than one configuration providers, the value from the last provider added is used.</span></span> <span data-ttu-id="2d045-255">Pour plus d’informations, voir [Configuration par défaut](#default).</span><span class="sxs-lookup"><span data-stu-id="2d045-255">For more information, see [Default configuration](#default).</span></span>
* <span data-ttu-id="2d045-256">Clés hiérarchiques</span><span class="sxs-lookup"><span data-stu-id="2d045-256">Hierarchical keys</span></span>
  * <span data-ttu-id="2d045-257">Dans l’API Configuration, un séparateur sous forme de signe deux-points (`:`) fonctionne sur toutes les plateformes.</span><span class="sxs-lookup"><span data-stu-id="2d045-257">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="2d045-258">Dans les variables d’environnement, un séparateur sous forme de signe deux-points peut ne pas fonctionner sur toutes les plateformes.</span><span class="sxs-lookup"><span data-stu-id="2d045-258">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="2d045-259">Un double `__`soulignement, , est pris en charge `:`par toutes les plates-formes et est automatiquement converti en un côlon .</span><span class="sxs-lookup"><span data-stu-id="2d045-259">A double underscore, `__`, is supported by all platforms and is automatically converted into a colon `:`.</span></span>
  * <span data-ttu-id="2d045-260">Dans Azure Key Vault, les `--` touches hiérarchiques servent de séparateur.</span><span class="sxs-lookup"><span data-stu-id="2d045-260">In Azure Key Vault, hierarchical keys use `--` as a separator.</span></span> <span data-ttu-id="2d045-261">Écrivez du `--` code `:` pour remplacer le lorsque les secrets sont chargés dans la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="2d045-261">Write code to replace the `--` with a `:` when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="2d045-262"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> prend en charge la liaison de tableaux à des objets à l’aide d’index de tableau dans les clés de configuration.</span><span class="sxs-lookup"><span data-stu-id="2d045-262">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="2d045-263">La liaison de tableau est décrite dans la section [Lier un tableau à une classe](#boa).</span><span class="sxs-lookup"><span data-stu-id="2d045-263">Array binding is described in the [Bind an array to a class](#boa) section.</span></span>

<span data-ttu-id="2d045-264">Valeurs de configuration :</span><span class="sxs-lookup"><span data-stu-id="2d045-264">Configuration values:</span></span>

* <span data-ttu-id="2d045-265">Ce sont des cordes.</span><span class="sxs-lookup"><span data-stu-id="2d045-265">Are strings.</span></span>
* <span data-ttu-id="2d045-266">Les valeurs NULL ne peuvent pas être stockées dans la configuration ou liées à des objets.</span><span class="sxs-lookup"><span data-stu-id="2d045-266">Null values can't be stored in configuration or bound to objects.</span></span>

<a name="cp"></a>

## <a name="configuration-providers"></a><span data-ttu-id="2d045-267">Fournisseurs de configuration</span><span class="sxs-lookup"><span data-stu-id="2d045-267">Configuration providers</span></span>

<span data-ttu-id="2d045-268">Le tableau suivant présente les fournisseurs de configuration disponibles pour les applications ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2d045-268">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="2d045-269">Fournisseur</span><span class="sxs-lookup"><span data-stu-id="2d045-269">Provider</span></span> | <span data-ttu-id="2d045-270">Fournit la configuration à partir de</span><span class="sxs-lookup"><span data-stu-id="2d045-270">Provides configuration from</span></span> |
| -------- | ----------------------------------- |
| [<span data-ttu-id="2d045-271">Fournisseur de configuration Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="2d045-271">Azure Key Vault configuration provider</span></span>](xref:security/key-vault-configuration) | <span data-ttu-id="2d045-272">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="2d045-272">Azure Key Vault</span></span> |
| [<span data-ttu-id="2d045-273">Fournisseur de configuration d’App Azure</span><span class="sxs-lookup"><span data-stu-id="2d045-273">Azure App configuration provider</span></span>](/azure/azure-app-configuration/quickstart-aspnet-core-app) | <span data-ttu-id="2d045-274">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="2d045-274">Azure App Configuration</span></span> |
| [<span data-ttu-id="2d045-275">Fournisseur de configuration de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="2d045-275">Command-line configuration provider</span></span>](#clcp) | <span data-ttu-id="2d045-276">Paramètres de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="2d045-276">Command-line parameters</span></span> |
| [<span data-ttu-id="2d045-277">Fournisseur de configuration personnalisé</span><span class="sxs-lookup"><span data-stu-id="2d045-277">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="2d045-278">Source personnalisée</span><span class="sxs-lookup"><span data-stu-id="2d045-278">Custom source</span></span> |
| [<span data-ttu-id="2d045-279">Fournisseur de configuration de variables d’environnement</span><span class="sxs-lookup"><span data-stu-id="2d045-279">Environment Variables configuration provider</span></span>](#evcp) | <span data-ttu-id="2d045-280">Variables d'environnement</span><span class="sxs-lookup"><span data-stu-id="2d045-280">Environment variables</span></span> |
| [<span data-ttu-id="2d045-281">Fournisseur de configuration de fichiers</span><span class="sxs-lookup"><span data-stu-id="2d045-281">File configuration provider</span></span>](#file-configuration-provider) | <span data-ttu-id="2d045-282">Fichiers INI, JSON et XML</span><span class="sxs-lookup"><span data-stu-id="2d045-282">INI, JSON, and XML files</span></span> |
| [<span data-ttu-id="2d045-283">Fournisseur de configuration de clé par fichier</span><span class="sxs-lookup"><span data-stu-id="2d045-283">Key-per-file configuration provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="2d045-284">Fichiers de répertoire</span><span class="sxs-lookup"><span data-stu-id="2d045-284">Directory files</span></span> |
| [<span data-ttu-id="2d045-285">Fournisseur de configuration de mémoire</span><span class="sxs-lookup"><span data-stu-id="2d045-285">Memory configuration provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="2d045-286">Collections en mémoire</span><span class="sxs-lookup"><span data-stu-id="2d045-286">In-memory collections</span></span> |
| [<span data-ttu-id="2d045-287">Gestionnaire secret</span><span class="sxs-lookup"><span data-stu-id="2d045-287">Secret Manager</span></span>](xref:security/app-secrets)  | <span data-ttu-id="2d045-288">Fichier dans le répertoire de profil utilisateur</span><span class="sxs-lookup"><span data-stu-id="2d045-288">File in the user profile directory</span></span> |

<span data-ttu-id="2d045-289">Les sources de configuration sont lues dans l’ordre de spécifiés de leurs fournisseurs de configuration.</span><span class="sxs-lookup"><span data-stu-id="2d045-289">Configuration sources are read in the order that their configuration providers are specified.</span></span> <span data-ttu-id="2d045-290">Commandez les fournisseurs de configuration en code pour répondre aux priorités des sources de configuration sous-jacentes dont l’application a besoin.</span><span class="sxs-lookup"><span data-stu-id="2d045-290">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="2d045-291">Une séquence type des fournisseurs de configuration est la suivante :</span><span class="sxs-lookup"><span data-stu-id="2d045-291">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="2d045-292">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="2d045-292">*appsettings.json*</span></span>
1. <span data-ttu-id="2d045-293">*appsettings*. `Environment`. *json json*</span><span class="sxs-lookup"><span data-stu-id="2d045-293">*appsettings*.`Environment`.*json*</span></span>
1. [<span data-ttu-id="2d045-294">Gestionnaire secret</span><span class="sxs-lookup"><span data-stu-id="2d045-294">Secret Manager</span></span>](xref:security/app-secrets)
1. <span data-ttu-id="2d045-295">Variables de l’environnement à l’aide du [fournisseur de configuration Environment Variables](#evcp).</span><span class="sxs-lookup"><span data-stu-id="2d045-295">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="2d045-296">Arguments de ligne de commande utilisant le [fournisseur de configuration de ligne de commande.](#command-line-configuration-provider)</span><span class="sxs-lookup"><span data-stu-id="2d045-296">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>

<span data-ttu-id="2d045-297">Une pratique courante consiste à ajouter le fournisseur de configuration de ligne de commandement en dernier dans une série de fournisseurs pour permettre aux arguments de ligne de commande de remplacer la configuration définie par les autres fournisseurs.</span><span class="sxs-lookup"><span data-stu-id="2d045-297">A common practice is to add the Command-line configuration provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="2d045-298">La séquence précédente des fournisseurs est utilisée dans la [configuration par défaut](#default).</span><span class="sxs-lookup"><span data-stu-id="2d045-298">The preceding sequence of providers is used in the [default configuration](#default).</span></span>

<a name="constr"></a>

### <a name="connection-string-prefixes"></a><span data-ttu-id="2d045-299">Préfixes des chaînes de connexion</span><span class="sxs-lookup"><span data-stu-id="2d045-299">Connection string prefixes</span></span>

<span data-ttu-id="2d045-300">L’API Configuration a des règles de traitement spéciales pour quatre variables d’environnement de chaîne de connexion.</span><span class="sxs-lookup"><span data-stu-id="2d045-300">The Configuration API has special processing rules for four connection string environment variables.</span></span> <span data-ttu-id="2d045-301">Ces chaînes de connexion sont impliquées dans la configuration des chaînes de connexion Azure pour l’environnement de l’application.</span><span class="sxs-lookup"><span data-stu-id="2d045-301">These connection strings are involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="2d045-302">Les variables de l’environnement avec les préfixes indiqués dans le tableau sont `AddEnvironmentVariables`chargées dans l’application avec la configuration par [défaut](#default) ou quand aucun préfixe n’est fourni à .</span><span class="sxs-lookup"><span data-stu-id="2d045-302">Environment variables with the prefixes shown in the table are loaded into the app with the [default configuration](#default) or when no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="2d045-303">Préfixe de la chaîne de connexion</span><span class="sxs-lookup"><span data-stu-id="2d045-303">Connection string prefix</span></span> | <span data-ttu-id="2d045-304">Fournisseur</span><span class="sxs-lookup"><span data-stu-id="2d045-304">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="2d045-305">Fournisseur personnalisé</span><span class="sxs-lookup"><span data-stu-id="2d045-305">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="2d045-306">MySQL</span><span class="sxs-lookup"><span data-stu-id="2d045-306">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="2d045-307">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="2d045-307">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="2d045-308">SQL Server</span><span class="sxs-lookup"><span data-stu-id="2d045-308">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="2d045-309">Quand une variable d’environnement est découverte et chargée dans la configuration avec l’un des quatre préfixes indiqués dans le tableau :</span><span class="sxs-lookup"><span data-stu-id="2d045-309">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="2d045-310">La clé de configuration est créée en supprimant le préfixe de la variable d’environnement et en ajoutant une section de clé de configuration (`ConnectionStrings`).</span><span class="sxs-lookup"><span data-stu-id="2d045-310">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="2d045-311">Une nouvelle paire clé-valeur de configuration est créée qui représente le fournisseur de connexion de base de données (à l’exception de `CUSTOMCONNSTR_`, qui ne possède aucun fournisseur indiqué).</span><span class="sxs-lookup"><span data-stu-id="2d045-311">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="2d045-312">Clé de variable d’environnement</span><span class="sxs-lookup"><span data-stu-id="2d045-312">Environment variable key</span></span> | <span data-ttu-id="2d045-313">Clé de configuration convertie</span><span class="sxs-lookup"><span data-stu-id="2d045-313">Converted configuration key</span></span> | <span data-ttu-id="2d045-314">Entrée de configuration de fournisseur</span><span class="sxs-lookup"><span data-stu-id="2d045-314">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="2d045-315">Entrée de configuration non créée.</span><span class="sxs-lookup"><span data-stu-id="2d045-315">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="2d045-316">Clé : `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="2d045-316">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="2d045-317">Valeur: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="2d045-317">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="2d045-318">Clé : `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="2d045-318">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="2d045-319">Valeur: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="2d045-319">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="2d045-320">Clé : `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="2d045-320">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="2d045-321">Valeur: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="2d045-321">Value: `System.Data.SqlClient`</span></span>  |

<a name="jcp"></a>

### <a name="json-configuration-provider"></a><span data-ttu-id="2d045-322">Fournisseur de configuration JSON</span><span class="sxs-lookup"><span data-stu-id="2d045-322">JSON configuration provider</span></span>

<span data-ttu-id="2d045-323">La <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> configuration des charges de JSON fichier paires de valeur de clé.</span><span class="sxs-lookup"><span data-stu-id="2d045-323">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs.</span></span>

<span data-ttu-id="2d045-324">Les surcharges peuvent spécifier :</span><span class="sxs-lookup"><span data-stu-id="2d045-324">Overloads can specify:</span></span>

* <span data-ttu-id="2d045-325">Si le fichier est facultatif.</span><span class="sxs-lookup"><span data-stu-id="2d045-325">Whether the file is optional.</span></span>
* <span data-ttu-id="2d045-326">Si la configuration est rechargée quand le fichier est modifié.</span><span class="sxs-lookup"><span data-stu-id="2d045-326">Whether the configuration is reloaded if the file changes.</span></span>

<span data-ttu-id="2d045-327">Examinons le code ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="2d045-327">Consider the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON.cs?name=snippet&highlight=12-14)]

<span data-ttu-id="2d045-328">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="2d045-328">The preceding code:</span></span>

* <span data-ttu-id="2d045-329">Configure le fournisseur de configuration JSON pour charger le fichier *MyConfig.json* avec les options suivantes :</span><span class="sxs-lookup"><span data-stu-id="2d045-329">Configures the JSON configuration provider to load the *MyConfig.json* file with the following options:</span></span>
  * <span data-ttu-id="2d045-330">`optional: true`: Le fichier est facultatif.</span><span class="sxs-lookup"><span data-stu-id="2d045-330">`optional: true`: The file is optional.</span></span>
  * <span data-ttu-id="2d045-331">`reloadOnChange: true`: Le fichier est rechargé lorsque les modifications sont enregistrées.</span><span class="sxs-lookup"><span data-stu-id="2d045-331">`reloadOnChange: true` : The file is reloaded when changes are saved.</span></span>
* <span data-ttu-id="2d045-332">Lit les [fournisseurs de configuration par défaut](#default) avant le fichier *MyConfig.json.*</span><span class="sxs-lookup"><span data-stu-id="2d045-332">Reads the [default configuration providers](#default) before the *MyConfig.json* file.</span></span> <span data-ttu-id="2d045-333">Paramètres dans le fichier *MyConfig.json* remplacement de réglage dans les fournisseurs de configuration par défaut, y compris le [fournisseur de configuration des variables environnement](#evcp) et le fournisseur de configuration de ligne de [commande](#clcp).</span><span class="sxs-lookup"><span data-stu-id="2d045-333">Settings in the *MyConfig.json* file override setting in the default configuration providers, including the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="2d045-334">Vous ne voulez généralement ***pas d’un*** fichier JSON personnalisé des valeurs primordiales définies dans le [fournisseur de configuration des variables environnement](#evcp) et le fournisseur de configuration de ligne de [commande](#clcp).</span><span class="sxs-lookup"><span data-stu-id="2d045-334">You typically ***don't*** want a custom JSON file overriding values set in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="2d045-335">Le code suivant efface tous les fournisseurs de configuration et ajoute plusieurs fournisseurs de configuration :</span><span class="sxs-lookup"><span data-stu-id="2d045-335">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON2.cs?name=snippet)]

<span data-ttu-id="2d045-336">Dans le code précédent, les paramètres dans le *MyConfig.json* et *MyConfig*. `Environment`. *fichiers json:*</span><span class="sxs-lookup"><span data-stu-id="2d045-336">In the preceding code, settings in the *MyConfig.json* and  *MyConfig*.`Environment`.*json* files:</span></span>

* <span data-ttu-id="2d045-337">Remplacer les paramètres dans les *appsettings.json* et *appsettings*. `Environment`. *fichiers json.*</span><span class="sxs-lookup"><span data-stu-id="2d045-337">Override settings in the *appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="2d045-338">Sont remplacés par les paramètres du [fournisseur de configuration des variables environnement](#evcp) et du fournisseur de configuration de la ligne de [commande.](#clcp)</span><span class="sxs-lookup"><span data-stu-id="2d045-338">Are overridden by settings in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="2d045-339">Le [téléchargement de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contient le fichier *MyConfig.json* suivant :</span><span class="sxs-lookup"><span data-stu-id="2d045-339">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *MyConfig.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyConfig.json)]

<span data-ttu-id="2d045-340">Le code suivant du [téléchargement de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) affiche plusieurs des paramètres des configurations précédentes :</span><span class="sxs-lookup"><span data-stu-id="2d045-340">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<a name="fcp"></a>

## <a name="file-configuration-provider"></a><span data-ttu-id="2d045-341">Fournisseur de configuration de fichiers</span><span class="sxs-lookup"><span data-stu-id="2d045-341">File configuration provider</span></span>

<span data-ttu-id="2d045-342"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> est la classe de base pour charger la configuration à partir du système de fichiers.</span><span class="sxs-lookup"><span data-stu-id="2d045-342"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="2d045-343">Les fournisseurs de `FileConfigurationProvider`configuration suivants dérivent de :</span><span class="sxs-lookup"><span data-stu-id="2d045-343">The following configuration providers derive from `FileConfigurationProvider`:</span></span>

* [<span data-ttu-id="2d045-344">Fournisseur de configuration INI</span><span class="sxs-lookup"><span data-stu-id="2d045-344">INI configuration provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="2d045-345">Fournisseur de configuration JSON</span><span class="sxs-lookup"><span data-stu-id="2d045-345">JSON configuration provider</span></span>](#jcp)
* [<span data-ttu-id="2d045-346">Fournisseur de configuration XML</span><span class="sxs-lookup"><span data-stu-id="2d045-346">XML configuration provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="2d045-347">Fournisseur de configuration INI</span><span class="sxs-lookup"><span data-stu-id="2d045-347">INI configuration provider</span></span>

<span data-ttu-id="2d045-348"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> charge la configuration à partir des paires clé-valeur du fichier INI lors de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="2d045-348">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="2d045-349">Le code suivant efface tous les fournisseurs de configuration et ajoute plusieurs fournisseurs de configuration :</span><span class="sxs-lookup"><span data-stu-id="2d045-349">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramINI.cs?name=snippet&highlight=10-30)]

<span data-ttu-id="2d045-350">Dans le code précédent, les paramètres dans le *MyIniConfig.ini* et *MyIniConfig*. `Environment`. les fichiers *ini* sont remplacés par les paramètres de la :</span><span class="sxs-lookup"><span data-stu-id="2d045-350">In the preceding code, settings in the *MyIniConfig.ini* and  *MyIniConfig*.`Environment`.*ini* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="2d045-351">Fournisseur de configuration de variables environment</span><span class="sxs-lookup"><span data-stu-id="2d045-351">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="2d045-352">[Fournisseur de configuration de ligne de commande](#clcp).</span><span class="sxs-lookup"><span data-stu-id="2d045-352">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="2d045-353">Le [téléchargement de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contient le fichier *MyIniConfig.ini* suivant :</span><span class="sxs-lookup"><span data-stu-id="2d045-353">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyIniConfig.ini* file:</span></span>

[!code-ini[](index/samples/3.x/ConfigSample/MyIniConfig.ini)]

<span data-ttu-id="2d045-354">Le code suivant du [téléchargement de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) affiche plusieurs des paramètres des configurations précédentes :</span><span class="sxs-lookup"><span data-stu-id="2d045-354">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

### <a name="xml-configuration-provider"></a><span data-ttu-id="2d045-355">Fournisseur de configuration XML</span><span class="sxs-lookup"><span data-stu-id="2d045-355">XML configuration provider</span></span>

<span data-ttu-id="2d045-356"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> charge la configuration à partir des paires clé-valeur du fichier XML lors de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="2d045-356">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="2d045-357">Le code suivant efface tous les fournisseurs de configuration et ajoute plusieurs fournisseurs de configuration :</span><span class="sxs-lookup"><span data-stu-id="2d045-357">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramXML.cs?name=snippet)]

<span data-ttu-id="2d045-358">Dans le code précédent, les paramètres dans le *MyXMLFile.xml* et *MyXMLFile*. `Environment`. les fichiers *xml* sont remplacés par les paramètres de la :</span><span class="sxs-lookup"><span data-stu-id="2d045-358">In the preceding code, settings in the *MyXMLFile.xml* and  *MyXMLFile*.`Environment`.*xml* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="2d045-359">Fournisseur de configuration de variables environment</span><span class="sxs-lookup"><span data-stu-id="2d045-359">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="2d045-360">[Fournisseur de configuration de ligne de commande](#clcp).</span><span class="sxs-lookup"><span data-stu-id="2d045-360">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="2d045-361">Le [téléchargement de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contient le fichier *MyXMLFile.xml* suivant :</span><span class="sxs-lookup"><span data-stu-id="2d045-361">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyXMLFile.xml* file:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile.xml)]

<span data-ttu-id="2d045-362">Le code suivant du [téléchargement de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) affiche plusieurs des paramètres des configurations précédentes :</span><span class="sxs-lookup"><span data-stu-id="2d045-362">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="2d045-363">Les éléments répétitifs qui utilisent le même nom d’élément fonctionnent si l’attribut `name` est utilisé pour distinguer les éléments :</span><span class="sxs-lookup"><span data-stu-id="2d045-363">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile3.xml)]

<span data-ttu-id="2d045-364">Le code suivant lit le fichier de configuration précédent et affiche les clés et les valeurs :</span><span class="sxs-lookup"><span data-stu-id="2d045-364">The following code reads the previous configuration file and displays the keys and values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/XML/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="2d045-365">Les attributs peuvent être utilisés pour fournir des valeurs :</span><span class="sxs-lookup"><span data-stu-id="2d045-365">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="2d045-366">Le fichier de configuration précédent charge les clés suivantes avec `value` :</span><span class="sxs-lookup"><span data-stu-id="2d045-366">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="2d045-367">key:attribute</span><span class="sxs-lookup"><span data-stu-id="2d045-367">key:attribute</span></span>
* <span data-ttu-id="2d045-368">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="2d045-368">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="2d045-369">Fournisseur de configuration de clé par fichier</span><span class="sxs-lookup"><span data-stu-id="2d045-369">Key-per-file configuration provider</span></span>

<span data-ttu-id="2d045-370">Le <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> utilise les fichiers d’un répertoire en tant que paires clé-valeur de configuration.</span><span class="sxs-lookup"><span data-stu-id="2d045-370">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="2d045-371">La clé est le nom de fichier.</span><span class="sxs-lookup"><span data-stu-id="2d045-371">The key is the file name.</span></span> <span data-ttu-id="2d045-372">La valeur contient le contenu du fichier.</span><span class="sxs-lookup"><span data-stu-id="2d045-372">The value contains the file's contents.</span></span> <span data-ttu-id="2d045-373">Le fournisseur de configuration Key-per-file est utilisé dans les scénarios d’hébergement Docker.</span><span class="sxs-lookup"><span data-stu-id="2d045-373">The Key-per-file configuration provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="2d045-374">Pour activer la configuration clé par fichier, appelez la méthode d’extension <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> sur une instance de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="2d045-374">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="2d045-375">Le `directoryPath` aux fichiers doit être un chemin d’accès absolu.</span><span class="sxs-lookup"><span data-stu-id="2d045-375">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="2d045-376">Les surcharges permettent de spécifier :</span><span class="sxs-lookup"><span data-stu-id="2d045-376">Overloads permit specifying:</span></span>

* <span data-ttu-id="2d045-377">Un délégué `Action<KeyPerFileConfigurationSource>` qui configure la source.</span><span class="sxs-lookup"><span data-stu-id="2d045-377">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="2d045-378">Si le répertoire est facultatif et le chemin d’accès au répertoire.</span><span class="sxs-lookup"><span data-stu-id="2d045-378">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="2d045-379">Le double trait de soulignement (`__`) est utilisé comme un délimiteur de clé de configuration dans les noms de fichiers.</span><span class="sxs-lookup"><span data-stu-id="2d045-379">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="2d045-380">Par exemple, le nom de fichier `Logging__LogLevel__System` génère la clé de configuration `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="2d045-380">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="2d045-381">Appelez `ConfigureAppConfiguration` lors de la création de l’hôte pour spécifier la configuration de l’application :</span><span class="sxs-lookup"><span data-stu-id="2d045-381">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<a name="mcp"></a>

## <a name="memory-configuration-provider"></a><span data-ttu-id="2d045-382">Fournisseur de configuration de mémoire</span><span class="sxs-lookup"><span data-stu-id="2d045-382">Memory configuration provider</span></span>

<span data-ttu-id="2d045-383">Le <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> utilise une collection en mémoire en tant que paires clé-valeur de configuration.</span><span class="sxs-lookup"><span data-stu-id="2d045-383">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="2d045-384">Le code suivant ajoute une collection de mémoire au système de configuration :</span><span class="sxs-lookup"><span data-stu-id="2d045-384">The following code adds a memory collection to the configuration system:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet6)]

<span data-ttu-id="2d045-385">Le code suivant de [l’exemple de téléchargement](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) affiche les paramètres des configurations précédentes :</span><span class="sxs-lookup"><span data-stu-id="2d045-385">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="2d045-386">Dans le code `config.AddInMemoryCollection(Dict)` précédent, est ajouté après les [fournisseurs de configuration par défaut](#default).</span><span class="sxs-lookup"><span data-stu-id="2d045-386">In the preceding code, `config.AddInMemoryCollection(Dict)` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="2d045-387">Pour un exemple de commande des fournisseurs de configuration, voir [le fournisseur de configuration JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="2d045-387">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="2d045-388">Pour un exemple de commande des fournisseurs de configuration, voir [le fournisseur de configuration JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="2d045-388">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="2d045-389">Voir [Bind un tableau](#boa) `MemoryConfigurationProvider`pour un autre exemple en utilisant .</span><span class="sxs-lookup"><span data-stu-id="2d045-389">See [Bind an array](#boa) for another example using `MemoryConfigurationProvider`.</span></span>

## <a name="getvalue"></a><span data-ttu-id="2d045-390">GetValue</span><span class="sxs-lookup"><span data-stu-id="2d045-390">GetValue</span></span>

<span data-ttu-id="2d045-391">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*)extrait une seule valeur de la configuration avec une clé spécifiée et la convertit en type spécifié :</span><span class="sxs-lookup"><span data-stu-id="2d045-391">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified type:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestNum.cshtml.cs?name=snippet)]

<span data-ttu-id="2d045-392">Dans le code `NumberKey` précédent, si vous n’êtes pas `99` trouvé dans la configuration, la valeur par défaut de est utilisé.</span><span class="sxs-lookup"><span data-stu-id="2d045-392">In the preceding code,  if `NumberKey` isn't found in the configuration, the default value of `99` is used.</span></span>

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="2d045-393">GetSection, GetChildren et Exists</span><span class="sxs-lookup"><span data-stu-id="2d045-393">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="2d045-394">Pour les exemples qui suivent, considérez le fichier *MySubsection.json* suivant :</span><span class="sxs-lookup"><span data-stu-id="2d045-394">For the examples that follow, consider the following *MySubsection.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MySubsection.json)]

<span data-ttu-id="2d045-395">Le code suivant ajoute *MySubsection.json* aux fournisseurs de configuration :</span><span class="sxs-lookup"><span data-stu-id="2d045-395">The following code adds *MySubsection.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONsection.cs?name=snippet)]

### <a name="getsection"></a><span data-ttu-id="2d045-396">GetSection</span><span class="sxs-lookup"><span data-stu-id="2d045-396">GetSection</span></span>

<span data-ttu-id="2d045-397">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) renvoie une sous-section de configuration avec la clé de sous-section spécifiée.</span><span class="sxs-lookup"><span data-stu-id="2d045-397">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) returns a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="2d045-398">Le code suivant `section1`renvoie des valeurs pour :</span><span class="sxs-lookup"><span data-stu-id="2d045-398">The following code returns values for `section1`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection.cshtml.cs?name=snippet)]

<span data-ttu-id="2d045-399">Le code suivant `section2:subsection0`renvoie des valeurs pour :</span><span class="sxs-lookup"><span data-stu-id="2d045-399">The following code returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection2.cshtml.cs?name=snippet)]

<span data-ttu-id="2d045-400">`GetSection` ne retourne jamais `null`.</span><span class="sxs-lookup"><span data-stu-id="2d045-400">`GetSection` never returns `null`.</span></span> <span data-ttu-id="2d045-401">Si aucune section correspondante n’est trouvée, une valeur `IConfigurationSection` vide est retournée.</span><span class="sxs-lookup"><span data-stu-id="2d045-401">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="2d045-402">Quand `GetSection` retourne une section correspondante, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> n’est pas rempli.</span><span class="sxs-lookup"><span data-stu-id="2d045-402">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="2d045-403"><xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> et <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> sont retournés quand la section existe.</span><span class="sxs-lookup"><span data-stu-id="2d045-403">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren-and-exists"></a><span data-ttu-id="2d045-404">GetChildren et Existe</span><span class="sxs-lookup"><span data-stu-id="2d045-404">GetChildren and Exists</span></span>

<span data-ttu-id="2d045-405">Le code suivant appelle [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) et retourne des valeurs pour `section2:subsection0`:</span><span class="sxs-lookup"><span data-stu-id="2d045-405">The following code calls [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) and returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection4.cshtml.cs?name=snippet)]

<span data-ttu-id="2d045-406">Le code précédent appelle [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) pour vérifier l’existence de la section :</span><span class="sxs-lookup"><span data-stu-id="2d045-406">The preceding code calls [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to verify the  section exists:</span></span>

 <a name="boa"></a>

## <a name="bind-an-array"></a><span data-ttu-id="2d045-407">Lier un tableau</span><span class="sxs-lookup"><span data-stu-id="2d045-407">Bind an array</span></span>

<span data-ttu-id="2d045-408">[ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) prend en charge les tableaux de liaison aux objets utilisant des indices de tableau dans les touches de configuration.</span><span class="sxs-lookup"><span data-stu-id="2d045-408">The [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="2d045-409">Tout format de tableau qui expose un segment de clé numérique est capable de se lier de tableau à un tableau de classe [POCO.](https://wikipedia.org/wiki/Plain_Old_CLR_Object)</span><span class="sxs-lookup"><span data-stu-id="2d045-409">Any array format that exposes a numeric key segment is capable of array binding to a [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) class array.</span></span>

<span data-ttu-id="2d045-410">Considérez *MyArray.json* à partir du [téléchargement de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span><span class="sxs-lookup"><span data-stu-id="2d045-410">Consider *MyArray.json* from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyArray.json)]

<span data-ttu-id="2d045-411">Le code suivant ajoute *MyArray.json* aux fournisseurs de configuration :</span><span class="sxs-lookup"><span data-stu-id="2d045-411">The following code adds *MyArray.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONarray.cs?name=snippet)]

<span data-ttu-id="2d045-412">Le code suivant lit la configuration et affiche les valeurs :</span><span class="sxs-lookup"><span data-stu-id="2d045-412">The following code reads the configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="2d045-413">Le code précédent renvoie la sortie suivante :</span><span class="sxs-lookup"><span data-stu-id="2d045-413">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value00
Index: 1  Value: value10
Index: 2  Value: value20
Index: 3  Value: value40
Index: 4  Value: value50
```

<span data-ttu-id="2d045-414">Dans la sortie précédente, l’indice 3 a de la valeur `value40`, correspondant à `"4": "value40",` *MyArray.json*.</span><span class="sxs-lookup"><span data-stu-id="2d045-414">In the preceding output, Index 3 has value `value40`, corresponding to `"4": "value40",` in *MyArray.json*.</span></span> <span data-ttu-id="2d045-415">Les indices de tableau lié sont continus et ne sont pas liés à l’indice de clé de configuration.</span><span class="sxs-lookup"><span data-stu-id="2d045-415">The bound array indices are continuous and not bound to the configuration key index.</span></span> <span data-ttu-id="2d045-416">Le classeur de configuration n’est pas capable de lier des valeurs nulles ou de créer des entrées nulles dans des objets liés</span><span class="sxs-lookup"><span data-stu-id="2d045-416">The configuration binder isn't capable of binding null values or creating null entries in bound objects</span></span>

<span data-ttu-id="2d045-417">Le code suivant `array:entries` charge la <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> configuration avec la méthode d’extension :</span><span class="sxs-lookup"><span data-stu-id="2d045-417">The  following code loads the `array:entries` configuration with the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet)]

<span data-ttu-id="2d045-418">Le code suivant lit `arrayDict` `Dictionary` la configuration dans le et affiche les valeurs :</span><span class="sxs-lookup"><span data-stu-id="2d045-418">The following code reads the configuration in the `arrayDict` `Dictionary` and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="2d045-419">Le code précédent renvoie la sortie suivante :</span><span class="sxs-lookup"><span data-stu-id="2d045-419">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value4
Index: 4  Value: value5
```

<span data-ttu-id="2d045-420">L’index &num;3 dans l’objet lié contient les données de configuration pour la clé de configuration `array:4` et sa valeur de `value4`.</span><span class="sxs-lookup"><span data-stu-id="2d045-420">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="2d045-421">Lorsque les données de configuration contenant un tableau sont liées, les indices de tableau dans les touches de configuration sont utilisés pour itérer les données de configuration lors de la création de l’objet.</span><span class="sxs-lookup"><span data-stu-id="2d045-421">When configuration data containing an array is bound, the array indices in the configuration keys are used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="2d045-422">Une valeur null ne peut pas être conservée dans des données de configuration, et une entrée à valeur null n’est pas créée dans un objet lié quand un tableau dans des clés de configuration ignore un ou plusieurs index.</span><span class="sxs-lookup"><span data-stu-id="2d045-422">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="2d045-423">L’élément de &num;configuration manquant pour l’index `ArrayExample` 3 peut être fourni &num;avant de l’être par exemple par n’importe quel fournisseur de configuration qui lit la paire de clés/valeur de l’index 3.</span><span class="sxs-lookup"><span data-stu-id="2d045-423">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that reads the index &num;3 key/value pair.</span></span> <span data-ttu-id="2d045-424">Considérez le fichier *Value3.json* suivant à partir du téléchargement de l’échantillon :</span><span class="sxs-lookup"><span data-stu-id="2d045-424">Consider the following *Value3.json* file from the sample download:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/Value3.json)]

<span data-ttu-id="2d045-425">Le code suivant comprend la configuration de `arrayDict` `Dictionary` *Value3.json* et le :</span><span class="sxs-lookup"><span data-stu-id="2d045-425">The following code includes configuration for *Value3.json* and the `arrayDict` `Dictionary`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet2)]

<span data-ttu-id="2d045-426">Le code suivant lit la configuration précédente et affiche les valeurs :</span><span class="sxs-lookup"><span data-stu-id="2d045-426">The following code reads the preceding configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="2d045-427">Le code précédent renvoie la sortie suivante :</span><span class="sxs-lookup"><span data-stu-id="2d045-427">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value3
Index: 4  Value: value4
Index: 5  Value: value5
```

<span data-ttu-id="2d045-428">Les fournisseurs de configuration personnalisés ne sont pas obligés d’implémenter la liaison de tableau.</span><span class="sxs-lookup"><span data-stu-id="2d045-428">Custom configuration providers aren't required to implement array binding.</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="2d045-429">Fournisseur de configuration personnalisé</span><span class="sxs-lookup"><span data-stu-id="2d045-429">Custom configuration provider</span></span>

<span data-ttu-id="2d045-430">L’exemple d’application montre comment créer un fournisseur de configuration de base qui lit les paires clé-valeur de configuration à partir d’une base de données à l’aide [d’Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="2d045-430">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="2d045-431">Le fournisseur présente les caractéristiques suivantes :</span><span class="sxs-lookup"><span data-stu-id="2d045-431">The provider has the following characteristics:</span></span>

* <span data-ttu-id="2d045-432">La base de données en mémoire EF est utilisée à des fins de démonstration.</span><span class="sxs-lookup"><span data-stu-id="2d045-432">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="2d045-433">Pour utiliser une base de données qui nécessite une chaîne de connexion, implémentez un autre `ConfigurationBuilder` pour fournir la chaîne de connexion à partir d’un autre fournisseur de configuration.</span><span class="sxs-lookup"><span data-stu-id="2d045-433">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="2d045-434">Le fournisseur lit une table de base de données dans la configuration au démarrage.</span><span class="sxs-lookup"><span data-stu-id="2d045-434">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="2d045-435">Le fournisseur n’interroge pas la base de données par clé.</span><span class="sxs-lookup"><span data-stu-id="2d045-435">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="2d045-436">Le rechargement en cas de changement n’est pas implémenté, par conséquent, la mise à jour la base de données après le démarrage de l’application n’a aucun effet sur la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="2d045-436">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="2d045-437">Définissez une entité `EFConfigurationValue` pour le stockage des valeurs de configuration dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="2d045-437">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="2d045-438">*Models/EFConfigurationValue.cs* :</span><span class="sxs-lookup"><span data-stu-id="2d045-438">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="2d045-439">Ajoutez un `EFConfigurationContext` pour stocker les valeurs configurées et y accéder.</span><span class="sxs-lookup"><span data-stu-id="2d045-439">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="2d045-440">*EFConfigurationProvider/EFConfigurationContext.cs* :</span><span class="sxs-lookup"><span data-stu-id="2d045-440">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="2d045-441">Créez une classe qui implémente <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="2d045-441">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="2d045-442">*EFConfigurationProvider/EFConfigurationSource.cs* :</span><span class="sxs-lookup"><span data-stu-id="2d045-442">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="2d045-443">Créez le fournisseur de configuration personnalisé en héritant de <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="2d045-443">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="2d045-444">Le fournisseur de configuration initialise la base de données quand elle est vide.</span><span class="sxs-lookup"><span data-stu-id="2d045-444">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="2d045-445">Étant [donné que les touches de configuration sont insensibles aux cas,](#keys)le dictionnaire utilisé pour initialiser la base de données est créé avec le comparateur insensible au cas[(StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span><span class="sxs-lookup"><span data-stu-id="2d045-445">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="2d045-446">*EFConfigurationProvider/EFConfigurationProvider.cs* :</span><span class="sxs-lookup"><span data-stu-id="2d045-446">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="2d045-447">Une méthode d’extension `AddEFConfiguration` permet d’ajouter la source de configuration à un `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="2d045-447">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="2d045-448">*Extensions/EntityFrameworkExtensions.cs* :</span><span class="sxs-lookup"><span data-stu-id="2d045-448">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="2d045-449">Le code suivant montre comment utiliser le `EFConfigurationProvider` personnalisé dans *Program.cs* :</span><span class="sxs-lookup"><span data-stu-id="2d045-449">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

<a name="acs"></a>

## <a name="access-configuration-in-startup"></a><span data-ttu-id="2d045-450">Configuration d’accès dans Startup</span><span class="sxs-lookup"><span data-stu-id="2d045-450">Access configuration in Startup</span></span>

<span data-ttu-id="2d045-451">Le code suivant affiche `Startup` les données de configuration dans les méthodes :</span><span class="sxs-lookup"><span data-stu-id="2d045-451">The following code displays configuration data in `Startup` methods:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/StartupKey.cs?name=snippet&highlight=13,18)]

<span data-ttu-id="2d045-452">Pour obtenir un exemple d’accès à la configuration à l’aide des méthodes pratiques de démarrage, consultez [Démarrage de l’application : méthodes pratiques](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="2d045-452">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-razor-pages"></a><span data-ttu-id="2d045-453">Configuration d’accès dans Razor Pages</span><span class="sxs-lookup"><span data-stu-id="2d045-453">Access configuration in Razor Pages</span></span>

<span data-ttu-id="2d045-454">Le code suivant affiche les données de configuration dans une page Razor :</span><span class="sxs-lookup"><span data-stu-id="2d045-454">The following code displays configuration data in a Razor Page:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Pages/Test5.cshtml)]

## <a name="access-configuration-in-a-mvc-view-file"></a><span data-ttu-id="2d045-455">Configuration d’accès dans un fichier de vue MVC</span><span class="sxs-lookup"><span data-stu-id="2d045-455">Access configuration in a MVC view file</span></span>

<span data-ttu-id="2d045-456">Le code suivant affiche les données de configuration dans une vue MVC :</span><span class="sxs-lookup"><span data-stu-id="2d045-456">The following code displays configuration data in a MVC view:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Views/Home2/Index.cshtml)]

<a name="hvac"></a>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="2d045-457">Configuration de l’hôte ou configuration de l’application</span><span class="sxs-lookup"><span data-stu-id="2d045-457">Host versus app configuration</span></span>

<span data-ttu-id="2d045-458">Avant que l’application ne soit configurée et démarrée, un *hôte* est configuré et lancé.</span><span class="sxs-lookup"><span data-stu-id="2d045-458">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="2d045-459">L’hôte est responsable de la gestion du démarrage et de la durée de vie des applications.</span><span class="sxs-lookup"><span data-stu-id="2d045-459">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="2d045-460">L’application et l’hôte sont configurés à l’aide des fournisseurs de configuration décrits dans cette rubrique.</span><span class="sxs-lookup"><span data-stu-id="2d045-460">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="2d045-461">Les paires clé-valeur de la configuration de l’hôte sont également incluses dans la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="2d045-461">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="2d045-462">Pour plus d’informations sur l’utilisation des fournisseurs de configuration lors de la génération de l’hôte et l’impact des sources de configuration sur la configuration de l’hôte, consultez <xref:fundamentals/index#host>.</span><span class="sxs-lookup"><span data-stu-id="2d045-462">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

<a name="dhc"></a>

## <a name="default-host-configuration"></a><span data-ttu-id="2d045-463">Configuration d’hôte par défaut</span><span class="sxs-lookup"><span data-stu-id="2d045-463">Default host configuration</span></span>

<span data-ttu-id="2d045-464">Pour plus de détails sur la configuration par défaut lors de l’utilisation de l’[hôte Web](xref:fundamentals/host/web-host), consultez la [version ASP.NET Core 2.2. de cette rubrique](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="2d045-464">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="2d045-465">La configuration de l’hôte est fournie à partir des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="2d045-465">Host configuration is provided from:</span></span>
  * <span data-ttu-id="2d045-466">Variables de l’environnement préfixées avec `DOTNET_` (par exemple, `DOTNET_ENVIRONMENT`) en utilisant le fournisseur de configuration Environment [Variables](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="2d045-466">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables configuration provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="2d045-467">Le préfixe (`DOTNET_`) est supprimé lorsque les paires clé-valeur de la configuration sont chargées.</span><span class="sxs-lookup"><span data-stu-id="2d045-467">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="2d045-468">Arguments de ligne de commande utilisant le [fournisseur de configuration de ligne de commande.](#command-line-configuration-provider)</span><span class="sxs-lookup"><span data-stu-id="2d045-468">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="2d045-469">La configuration par défaut de l’hôte Web est établie (`ConfigureWebHostDefaults`) :</span><span class="sxs-lookup"><span data-stu-id="2d045-469">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="2d045-470">Kestrel est utilisé comme serveur web et configuré à l’aide des fournisseurs de configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="2d045-470">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="2d045-471">Ajoutez l’intergiciel de filtrage d’hôtes.</span><span class="sxs-lookup"><span data-stu-id="2d045-471">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="2d045-472">Ajoutez l’intergiciel d’en-têtes transférés si la variable d'environnement `ASPNETCORE_FORWARDEDHEADERS_ENABLED` est définie sur `true`.</span><span class="sxs-lookup"><span data-stu-id="2d045-472">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="2d045-473">Activez l’intégration d’IIS.</span><span class="sxs-lookup"><span data-stu-id="2d045-473">Enable IIS integration.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="2d045-474">Autre configuration</span><span class="sxs-lookup"><span data-stu-id="2d045-474">Other configuration</span></span>

<span data-ttu-id="2d045-475">Ce sujet ne concerne que la *configuration de l’application*.</span><span class="sxs-lookup"><span data-stu-id="2d045-475">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="2d045-476">D’autres aspects de l’exécution et de l’hébergement des applications ASP.NET Core sont configurés à l’aide de fichiers de configuration non couverts dans ce sujet :</span><span class="sxs-lookup"><span data-stu-id="2d045-476">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="2d045-477">*launch.json*/*launchSettings.json* sont des fichiers de configuration d’outillage pour l’environnement de développement, décrits:</span><span class="sxs-lookup"><span data-stu-id="2d045-477">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="2d045-478">Dans <xref:fundamentals/environments#development>.</span><span class="sxs-lookup"><span data-stu-id="2d045-478">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="2d045-479">Dans l’ensemble de documentation où les fichiers sont utilisés pour configurer ASP.NET applications de base pour les scénarios de développement.</span><span class="sxs-lookup"><span data-stu-id="2d045-479">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="2d045-480">*web.config* est un fichier de configuration serveur, décrit dans les sujets suivants :</span><span class="sxs-lookup"><span data-stu-id="2d045-480">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="2d045-481">Pour plus d’informations sur la configuration de l’application <xref:migration/proper-to-2x/index#store-configurations>de migration à partir de versions antérieures de ASP.NET, voir .</span><span class="sxs-lookup"><span data-stu-id="2d045-481">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="2d045-482">Ajouter la configuration à partir d’un assembly externe</span><span class="sxs-lookup"><span data-stu-id="2d045-482">Add configuration from an external assembly</span></span>

<span data-ttu-id="2d045-483">Une implémentation de <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> permet d’ajouter des améliorations à une application au démarrage à partir d’un assembly externe, en dehors de la classe `Startup` de l’application.</span><span class="sxs-lookup"><span data-stu-id="2d045-483">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="2d045-484">Pour plus d’informations, consultez <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="2d045-484">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2d045-485">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="2d045-485">Additional resources</span></span>

* [<span data-ttu-id="2d045-486">Code source de configuration</span><span class="sxs-lookup"><span data-stu-id="2d045-486">Configuration source code</span></span>](https://github.com/dotnet/extensions/tree/master/src/Configuration)
* <xref:fundamentals/configuration/options>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2d045-487">La configuration d’application dans ASP.NET Core est basée sur des paires clé-valeur établies par les *fournisseurs de configuration*.</span><span class="sxs-lookup"><span data-stu-id="2d045-487">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="2d045-488">Les fournisseurs de configuration lisent les données de configuration dans les paires clé-valeur à partir de diverses sources de configuration :</span><span class="sxs-lookup"><span data-stu-id="2d045-488">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="2d045-489">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="2d045-489">Azure Key Vault</span></span>
* <span data-ttu-id="2d045-490">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="2d045-490">Azure App Configuration</span></span>
* <span data-ttu-id="2d045-491">Arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="2d045-491">Command-line arguments</span></span>
* <span data-ttu-id="2d045-492">Fournisseurs personnalisés (installés ou créés)</span><span class="sxs-lookup"><span data-stu-id="2d045-492">Custom providers (installed or created)</span></span>
* <span data-ttu-id="2d045-493">Fichiers de répertoire</span><span class="sxs-lookup"><span data-stu-id="2d045-493">Directory files</span></span>
* <span data-ttu-id="2d045-494">Variables d'environnement</span><span class="sxs-lookup"><span data-stu-id="2d045-494">Environment variables</span></span>
* <span data-ttu-id="2d045-495">Objets .NET en mémoire</span><span class="sxs-lookup"><span data-stu-id="2d045-495">In-memory .NET objects</span></span>
* <span data-ttu-id="2d045-496">Fichiers de paramètres</span><span class="sxs-lookup"><span data-stu-id="2d045-496">Settings files</span></span>

<span data-ttu-id="2d045-497">Les packages de configuration pour les scénarios de fournisseur de configuration courants ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) sont inclus implicitement dans le [métapaquet Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="2d045-497">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="2d045-498">Les exemples de code qui suivent et dans l’échantillon d’application utilisent l’espace de noms <xref:Microsoft.Extensions.Configuration> :</span><span class="sxs-lookup"><span data-stu-id="2d045-498">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="2d045-499">Le *modèle d’options* est une extension des concepts de configuration décrits dans cette rubrique.</span><span class="sxs-lookup"><span data-stu-id="2d045-499">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="2d045-500">Les options utilisent des classes pour représenter les groupes de paramètres associés.</span><span class="sxs-lookup"><span data-stu-id="2d045-500">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="2d045-501">Pour plus d’informations, consultez <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="2d045-501">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="2d045-502">[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([comment télécharger](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2d045-502">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="2d045-503">Configuration de l’hôte ou configuration de l’application</span><span class="sxs-lookup"><span data-stu-id="2d045-503">Host versus app configuration</span></span>

<span data-ttu-id="2d045-504">Avant que l’application ne soit configurée et démarrée, un *hôte* est configuré et lancé.</span><span class="sxs-lookup"><span data-stu-id="2d045-504">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="2d045-505">L’hôte est responsable de la gestion du démarrage et de la durée de vie des applications.</span><span class="sxs-lookup"><span data-stu-id="2d045-505">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="2d045-506">L’application et l’hôte sont configurés à l’aide des fournisseurs de configuration décrits dans cette rubrique.</span><span class="sxs-lookup"><span data-stu-id="2d045-506">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="2d045-507">Les paires clé-valeur de la configuration de l’hôte sont également incluses dans la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="2d045-507">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="2d045-508">Pour plus d’informations sur l’utilisation des fournisseurs de configuration lors de la génération de l’hôte et l’impact des sources de configuration sur la configuration de l’hôte, consultez <xref:fundamentals/index#host>.</span><span class="sxs-lookup"><span data-stu-id="2d045-508">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="2d045-509">Autre configuration</span><span class="sxs-lookup"><span data-stu-id="2d045-509">Other configuration</span></span>

<span data-ttu-id="2d045-510">Ce sujet ne concerne que la *configuration de l’application*.</span><span class="sxs-lookup"><span data-stu-id="2d045-510">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="2d045-511">D’autres aspects de l’exécution et de l’hébergement des applications ASP.NET Core sont configurés à l’aide de fichiers de configuration non couverts dans ce sujet :</span><span class="sxs-lookup"><span data-stu-id="2d045-511">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="2d045-512">*launch.json*/*launchSettings.json* sont des fichiers de configuration d’outillage pour l’environnement de développement, décrits:</span><span class="sxs-lookup"><span data-stu-id="2d045-512">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="2d045-513">Dans <xref:fundamentals/environments#development>.</span><span class="sxs-lookup"><span data-stu-id="2d045-513">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="2d045-514">Dans l’ensemble de documentation où les fichiers sont utilisés pour configurer ASP.NET applications de base pour les scénarios de développement.</span><span class="sxs-lookup"><span data-stu-id="2d045-514">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="2d045-515">*web.config* est un fichier de configuration serveur, décrit dans les sujets suivants :</span><span class="sxs-lookup"><span data-stu-id="2d045-515">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="2d045-516">Pour plus d’informations sur la configuration de l’application <xref:migration/proper-to-2x/index#store-configurations>de migration à partir de versions antérieures de ASP.NET, voir .</span><span class="sxs-lookup"><span data-stu-id="2d045-516">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="2d045-517">Configuration par défaut</span><span class="sxs-lookup"><span data-stu-id="2d045-517">Default configuration</span></span>

<span data-ttu-id="2d045-518">Les applications web basées sur les modèles ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) appellent <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> pendant la création d’un hôte.</span><span class="sxs-lookup"><span data-stu-id="2d045-518">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="2d045-519">`CreateDefaultBuilder` fournit la configuration par défaut de l’application dans l’ordre suivant :</span><span class="sxs-lookup"><span data-stu-id="2d045-519">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="2d045-520">Les éléments suivants s’appliquent aux applications qui utilisent l’[hôte web](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="2d045-520">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="2d045-521">Pour plus de détails sur la configuration par défaut lors de l’utilisation de l’[hôte générique](xref:fundamentals/host/generic-host), consultez la [dernière version de cette rubrique](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="2d045-521">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="2d045-522">La configuration de l’hôte est fournie à partir des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="2d045-522">Host configuration is provided from:</span></span>
  * <span data-ttu-id="2d045-523">Des variables d’environnement préfixées avec `ASPNETCORE_` (par exemple, `ASPNETCORE_ENVIRONMENT`) à l’aide du [Fournisseur de configuration des variables d’environnement](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="2d045-523">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="2d045-524">Le préfixe (`ASPNETCORE_`) est supprimé lorsque les paires clé-valeur de la configuration sont chargées.</span><span class="sxs-lookup"><span data-stu-id="2d045-524">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="2d045-525">Des arguments de ligne de commande à l’aide du [Fournisseur de configuration de ligne de commande](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="2d045-525">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="2d045-526">La configuration de l’application est fournie à partir des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="2d045-526">App configuration is provided from:</span></span>
  * <span data-ttu-id="2d045-527">*appSettings.JSON* à l’aide du [Fournisseur de configuration de fichier](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="2d045-527">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="2d045-528">*appsettings.{Environment}.json* à l’aide du [Fournisseur de configuration de fichier](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="2d045-528">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="2d045-529">L’outil [Secret Manager (Gestionnaire de secrets)](xref:security/app-secrets) quand l’application s’exécute dans l’environnement `Development` à l’aide de l’assembly d’entrée.</span><span class="sxs-lookup"><span data-stu-id="2d045-529">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="2d045-530">Des variables d’environnement à l’aide du [Fournisseur de configuration des variables d’environnement](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="2d045-530">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="2d045-531">Des arguments de ligne de commande à l’aide du [Fournisseur de configuration de ligne de commande](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="2d045-531">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="2d045-532">Sécurité</span><span class="sxs-lookup"><span data-stu-id="2d045-532">Security</span></span>

<span data-ttu-id="2d045-533">Adoptez les pratiques suivantes pour sécuriser les données de configuration sensibles :</span><span class="sxs-lookup"><span data-stu-id="2d045-533">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="2d045-534">Ne stockez jamais des mots de passe ou d’autres données sensibles dans le code du fournisseur de configuration ou dans les fichiers de configuration en texte clair.</span><span class="sxs-lookup"><span data-stu-id="2d045-534">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="2d045-535">N’utilisez aucun secret de production dans les environnements de développement ou de test.</span><span class="sxs-lookup"><span data-stu-id="2d045-535">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="2d045-536">Spécifiez les secrets en dehors du projet afin qu’ils ne puissent pas être validés par inadvertance dans un référentiel de code source.</span><span class="sxs-lookup"><span data-stu-id="2d045-536">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="2d045-537">Pour plus d'informations, voir les rubriques suivantes :</span><span class="sxs-lookup"><span data-stu-id="2d045-537">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="2d045-538"><xref:security/app-secrets>&ndash; Inclut des conseils sur l’utilisation des variables de l’environnement pour stocker des données sensibles.</span><span class="sxs-lookup"><span data-stu-id="2d045-538"><xref:security/app-secrets> &ndash; Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="2d045-539">Secret Manager utilise le fournisseur de configuration de fichier pour stocker les secrets utilisateur dans un fichier JSON sur le système local.</span><span class="sxs-lookup"><span data-stu-id="2d045-539">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="2d045-540">Le fournisseur de configuration de fichier est décrit plus loin dans cette rubrique.</span><span class="sxs-lookup"><span data-stu-id="2d045-540">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="2d045-541">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) stocke en toute sécurité des secrets d’application pour les applications ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2d045-541">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="2d045-542">Pour plus d’informations, consultez <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="2d045-542">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="2d045-543">Données de configuration hiérarchiques</span><span class="sxs-lookup"><span data-stu-id="2d045-543">Hierarchical configuration data</span></span>

<span data-ttu-id="2d045-544">L’API Configuration est capable de maintenir des données de configuration hiérarchiques en aplanissant les données hiérarchiques à l’aide d’un délimiteur dans les clés de configuration.</span><span class="sxs-lookup"><span data-stu-id="2d045-544">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="2d045-545">Dans le fichier JSON suivant, quatre clés existent dans une structure hiérarchique à deux sections :</span><span class="sxs-lookup"><span data-stu-id="2d045-545">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="2d045-546">Lorsque le fichier est lu dans la configuration, des clés uniques sont créées pour gérer la structure des données hiérarchiques d’origine de la source de configuration.</span><span class="sxs-lookup"><span data-stu-id="2d045-546">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="2d045-547">Les sections et les clés sont aplanies à l’aide d’un signe deux-points (`:`) pour conserver la structure d’origine :</span><span class="sxs-lookup"><span data-stu-id="2d045-547">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="2d045-548">section0:key0</span><span class="sxs-lookup"><span data-stu-id="2d045-548">section0:key0</span></span>
* <span data-ttu-id="2d045-549">section0:key1</span><span class="sxs-lookup"><span data-stu-id="2d045-549">section0:key1</span></span>
* <span data-ttu-id="2d045-550">section1:key0</span><span class="sxs-lookup"><span data-stu-id="2d045-550">section1:key0</span></span>
* <span data-ttu-id="2d045-551">section1:key1</span><span class="sxs-lookup"><span data-stu-id="2d045-551">section1:key1</span></span>

<span data-ttu-id="2d045-552">Les méthodes <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> et <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> sont disponibles pour isoler les sections et les enfants d’une section dans les données de configuration.</span><span class="sxs-lookup"><span data-stu-id="2d045-552"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="2d045-553">Ces méthodes sont décrites plus loin dans [GetSection, GetChildren et Exists](#getsection-getchildren-and-exists).</span><span class="sxs-lookup"><span data-stu-id="2d045-553">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="2d045-554">Conventions</span><span class="sxs-lookup"><span data-stu-id="2d045-554">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="2d045-555">Sources et fournisseurs</span><span class="sxs-lookup"><span data-stu-id="2d045-555">Sources and providers</span></span>

<span data-ttu-id="2d045-556">Au démarrage de l’application, les sources de configuration sont lues dans l’ordre où leurs fournisseurs de configuration sont spécifiés.</span><span class="sxs-lookup"><span data-stu-id="2d045-556">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="2d045-557">Les fournisseurs de configuration qui implémentent la détection des modifications peuvent recharger la configuration lorsqu’un paramètre sous-jacent est modifié.</span><span class="sxs-lookup"><span data-stu-id="2d045-557">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="2d045-558">Par exemple, le fournisseur de configuration de fichier (décrit plus loin dans cette rubrique) et le [fournisseur de configuration Azure Key Vault](xref:security/key-vault-configuration) implémentent la détection des modifications.</span><span class="sxs-lookup"><span data-stu-id="2d045-558">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="2d045-559"><xref:Microsoft.Extensions.Configuration.IConfiguration> est disponible dans le conteneur d’[injection de dépendances](xref:fundamentals/dependency-injection) de l’application.</span><span class="sxs-lookup"><span data-stu-id="2d045-559"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="2d045-560"><xref:Microsoft.Extensions.Configuration.IConfiguration>peut être injecté dans <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> une <xref:Microsoft.AspNetCore.Mvc.Controller> page Razor ou un MVC pour obtenir la configuration de la classe.</span><span class="sxs-lookup"><span data-stu-id="2d045-560"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="2d045-561">Dans les exemples `_config` suivants, le champ est utilisé pour accéder aux valeurs de configuration :</span><span class="sxs-lookup"><span data-stu-id="2d045-561">In the following examples, the `_config` field is used to access configuration values:</span></span>

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

<span data-ttu-id="2d045-562">Les fournisseurs de configuration ne peuvent pas utiliser le DI, car celui-ci n’est pas disponible lorsque les fournisseurs sont configurés par l’hôte.</span><span class="sxs-lookup"><span data-stu-id="2d045-562">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="2d045-563">Keys</span><span class="sxs-lookup"><span data-stu-id="2d045-563">Keys</span></span>

<span data-ttu-id="2d045-564">Les clés de configuration adoptent les conventions suivantes :</span><span class="sxs-lookup"><span data-stu-id="2d045-564">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="2d045-565">Les clés ne respectent pas la casse.</span><span class="sxs-lookup"><span data-stu-id="2d045-565">Keys are case-insensitive.</span></span> <span data-ttu-id="2d045-566">Par exemple, `ConnectionString` et `connectionstring` sont traités en tant que clés équivalentes.</span><span class="sxs-lookup"><span data-stu-id="2d045-566">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="2d045-567">Si une valeur pour la même clé est définie par des fournisseurs de configuration identiques ou différents, la valeur utilisée est la dernière valeur définie sur la clé.</span><span class="sxs-lookup"><span data-stu-id="2d045-567">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span>
* <span data-ttu-id="2d045-568">Clés hiérarchiques</span><span class="sxs-lookup"><span data-stu-id="2d045-568">Hierarchical keys</span></span>
  * <span data-ttu-id="2d045-569">Dans l’API Configuration, un séparateur sous forme de signe deux-points (`:`) fonctionne sur toutes les plateformes.</span><span class="sxs-lookup"><span data-stu-id="2d045-569">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="2d045-570">Dans les variables d’environnement, un séparateur sous forme de signe deux-points peut ne pas fonctionner sur toutes les plateformes.</span><span class="sxs-lookup"><span data-stu-id="2d045-570">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="2d045-571">Un trait de soulignement double (`__`) est pris en charge par toutes les plateformes et automatiquement transformé en signe deux-points.</span><span class="sxs-lookup"><span data-stu-id="2d045-571">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="2d045-572">Dans Azure Key Vault, les clés hiérarchiques utilisent `--` (deux tirets) comme séparateur.</span><span class="sxs-lookup"><span data-stu-id="2d045-572">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="2d045-573">Écrivez du code pour remplacer les tirets par un côlon lorsque les secrets sont chargés dans la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="2d045-573">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="2d045-574"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> prend en charge la liaison de tableaux à des objets à l’aide d’index de tableau dans les clés de configuration.</span><span class="sxs-lookup"><span data-stu-id="2d045-574">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="2d045-575">La liaison de tableau est décrite dans la section [Lier un tableau à une classe](#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="2d045-575">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="2d045-576">Valeurs</span><span class="sxs-lookup"><span data-stu-id="2d045-576">Values</span></span>

<span data-ttu-id="2d045-577">Les valeurs de configuration adoptent les conventions suivantes :</span><span class="sxs-lookup"><span data-stu-id="2d045-577">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="2d045-578">Les valeurs sont des chaînes.</span><span class="sxs-lookup"><span data-stu-id="2d045-578">Values are strings.</span></span>
* <span data-ttu-id="2d045-579">Les valeurs NULL ne peuvent pas être stockées dans la configuration ou liées à des objets.</span><span class="sxs-lookup"><span data-stu-id="2d045-579">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="2d045-580">Fournisseurs</span><span class="sxs-lookup"><span data-stu-id="2d045-580">Providers</span></span>

<span data-ttu-id="2d045-581">Le tableau suivant présente les fournisseurs de configuration disponibles pour les applications ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2d045-581">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="2d045-582">Fournisseur</span><span class="sxs-lookup"><span data-stu-id="2d045-582">Provider</span></span> | <span data-ttu-id="2d045-583">Fournit la configuration à partir de&hellip;</span><span class="sxs-lookup"><span data-stu-id="2d045-583">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="2d045-584">[Fournisseur de configuration Azure Key Vault](xref:security/key-vault-configuration) (rubrique *Sécurité*)</span><span class="sxs-lookup"><span data-stu-id="2d045-584">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="2d045-585">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="2d045-585">Azure Key Vault</span></span> |
| <span data-ttu-id="2d045-586">[Fournisseur Azure App Configuration](/azure/azure-app-configuration/quickstart-aspnet-core-app) (documentation Azure)</span><span class="sxs-lookup"><span data-stu-id="2d045-586">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="2d045-587">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="2d045-587">Azure App Configuration</span></span> |
| [<span data-ttu-id="2d045-588">Fournisseur de configuration de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="2d045-588">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="2d045-589">Paramètres de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="2d045-589">Command-line parameters</span></span> |
| [<span data-ttu-id="2d045-590">Fournisseur de configuration personnalisé</span><span class="sxs-lookup"><span data-stu-id="2d045-590">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="2d045-591">Source personnalisée</span><span class="sxs-lookup"><span data-stu-id="2d045-591">Custom source</span></span> |
| [<span data-ttu-id="2d045-592">Fournisseur de configuration de variables d’environnement</span><span class="sxs-lookup"><span data-stu-id="2d045-592">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="2d045-593">Variables d'environnement</span><span class="sxs-lookup"><span data-stu-id="2d045-593">Environment variables</span></span> |
| [<span data-ttu-id="2d045-594">Fournisseur de configuration de fichiers</span><span class="sxs-lookup"><span data-stu-id="2d045-594">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="2d045-595">Fichiers (INI, JSON, XML)</span><span class="sxs-lookup"><span data-stu-id="2d045-595">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="2d045-596">Fournisseur de configuration clé par fichier</span><span class="sxs-lookup"><span data-stu-id="2d045-596">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="2d045-597">Fichiers de répertoire</span><span class="sxs-lookup"><span data-stu-id="2d045-597">Directory files</span></span> |
| [<span data-ttu-id="2d045-598">Fournisseur de configuration de mémoire</span><span class="sxs-lookup"><span data-stu-id="2d045-598">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="2d045-599">Collections en mémoire</span><span class="sxs-lookup"><span data-stu-id="2d045-599">In-memory collections</span></span> |
| <span data-ttu-id="2d045-600">[Secrets utilisateur (Secret Manager)](xref:security/app-secrets) (rubrique *Sécurité*)</span><span class="sxs-lookup"><span data-stu-id="2d045-600">[User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="2d045-601">Fichier dans le répertoire de profil utilisateur</span><span class="sxs-lookup"><span data-stu-id="2d045-601">File in the user profile directory</span></span> |

<span data-ttu-id="2d045-602">Au démarrage, les sources de configuration sont lues dans l’ordre où leurs fournisseurs de configuration sont spécifiés.</span><span class="sxs-lookup"><span data-stu-id="2d045-602">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="2d045-603">Les fournisseurs de configuration décrits dans ce sujet sont décrits par ordre alphabétique, et non dans l’ordre que le code les arrange.</span><span class="sxs-lookup"><span data-stu-id="2d045-603">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="2d045-604">Commandez les fournisseurs de configuration en code pour répondre aux priorités des sources de configuration sous-jacentes dont l’application a besoin.</span><span class="sxs-lookup"><span data-stu-id="2d045-604">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="2d045-605">Une séquence type des fournisseurs de configuration est la suivante :</span><span class="sxs-lookup"><span data-stu-id="2d045-605">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="2d045-606">Fichiers (*appsettings.json*, *appsettings.{Environment}.json*, où `{Environment}` est l'environnement d’hébergement actuel de l'application)</span><span class="sxs-lookup"><span data-stu-id="2d045-606">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="2d045-607">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="2d045-607">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="2d045-608">[Secrets utilisateur (Secret Manager)](xref:security/app-secrets) (dans l’environnement de développement uniquement)</span><span class="sxs-lookup"><span data-stu-id="2d045-608">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="2d045-609">Variables d'environnement</span><span class="sxs-lookup"><span data-stu-id="2d045-609">Environment variables</span></span>
1. <span data-ttu-id="2d045-610">Arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="2d045-610">Command-line arguments</span></span>

<span data-ttu-id="2d045-611">Une pratique courante consiste à placer le Fournisseur de configuration de ligne de commande en dernier dans une série de fournisseurs pour permettre aux arguments de ligne de commande de remplacer la configuration définie par les autres fournisseurs.</span><span class="sxs-lookup"><span data-stu-id="2d045-611">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="2d045-612">La séquence précédente des fournisseurs est utilisée lorsqu’un nouveau constructeur hôte est parasécé avec `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="2d045-612">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="2d045-613">Pour plus d’informations, consultez la section [Configuration par défaut](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="2d045-613">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="2d045-614">Configurer le générateur d’ordinateur hôte avec UseConfiguration</span><span class="sxs-lookup"><span data-stu-id="2d045-614">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="2d045-615">Pour configurer le générateur d’ordinateur hôte, appelez <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> sur le générateur d’hôte avec la configuration.</span><span class="sxs-lookup"><span data-stu-id="2d045-615">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

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

## <a name="configureappconfiguration"></a><span data-ttu-id="2d045-616">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="2d045-616">ConfigureAppConfiguration</span></span>

<span data-ttu-id="2d045-617">Appelez `ConfigureAppConfiguration` quand vous créez l’hôte pour spécifier les fournisseurs de configuration de l’application en plus de ceux ajoutés automatiquement par `CreateDefaultBuilder` :</span><span class="sxs-lookup"><span data-stu-id="2d045-617">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="2d045-618">Remplacez la configuration précédente par des arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="2d045-618">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="2d045-619">Pour fournir une configuration d’application pouvant être remplacée par des arguments de ligne de commande, appelez `AddCommandLine` en dernier :</span><span class="sxs-lookup"><span data-stu-id="2d045-619">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="2d045-620">Supprimer les fournisseurs ajoutés par CreateDefaultBuilder</span><span class="sxs-lookup"><span data-stu-id="2d045-620">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="2d045-621">Pour supprimer les `CreateDefaultBuilder`fournisseurs ajoutés par , appelez [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) sur [l’IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) d’abord:</span><span class="sxs-lookup"><span data-stu-id="2d045-621">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="2d045-622">Utiliser la configuration lors du démarrage de l’application</span><span class="sxs-lookup"><span data-stu-id="2d045-622">Consume configuration during app startup</span></span>

<span data-ttu-id="2d045-623">La configuration fournie à l’application dans `ConfigureAppConfiguration` est disponible lors du démarrage de l’application, notamment `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="2d045-623">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="2d045-624">Pour plus d’informations, consultez la section [Accéder à la configuration lors du démarrage](#access-configuration-during-startup).</span><span class="sxs-lookup"><span data-stu-id="2d045-624">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="2d045-625">Fournisseur de configuration de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="2d045-625">Command-line Configuration Provider</span></span>

<span data-ttu-id="2d045-626"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> charge la configuration à partir des paires clé-valeur de l’argument de ligne de commande lors de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="2d045-626">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="2d045-627">Pour activer la configuration en ligne de commande, la méthode d’extension <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> est appelée sur une instance de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="2d045-627">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="2d045-628">`AddCommandLine` est appelé automatiquement quand `CreateDefaultBuilder(string [])` est appelé.</span><span class="sxs-lookup"><span data-stu-id="2d045-628">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="2d045-629">Pour plus d’informations, consultez la section [Configuration par défaut](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="2d045-629">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="2d045-630">`CreateDefaultBuilder` charge également :</span><span class="sxs-lookup"><span data-stu-id="2d045-630">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="2d045-631">Configuration facultative à partir des fichiers *appsettings.json* et *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="2d045-631">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="2d045-632">[Secrets utilisateur (Secret Manager)](xref:security/app-secrets) dans l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="2d045-632">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="2d045-633">Variables d'environnement.</span><span class="sxs-lookup"><span data-stu-id="2d045-633">Environment variables.</span></span>

<span data-ttu-id="2d045-634">`CreateDefaultBuilder` ajoute en dernier le Fournisseur de configuration de ligne de commande.</span><span class="sxs-lookup"><span data-stu-id="2d045-634">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="2d045-635">Les arguments de ligne de commande passés lors de l’exécution remplacent la configuration définie par les autres fournisseurs.</span><span class="sxs-lookup"><span data-stu-id="2d045-635">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="2d045-636">`CreateDefaultBuilder` agit lors de la construction de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="2d045-636">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="2d045-637">Par conséquent, la configuration de ligne de commande activée par `CreateDefaultBuilder` peut affecter la façon dont l’hôte est configuré.</span><span class="sxs-lookup"><span data-stu-id="2d045-637">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="2d045-638">Pour les applications basées sur les modèles ASP.NET Core, `AddCommandLine` a déjà été appelé par `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="2d045-638">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="2d045-639">Pour ajouter des fournisseurs de configuration supplémentaires et conserver la capacité de remplacer leur configuration par des arguments de ligne de commande, appelez les fournisseurs supplémentaires de l’application dans `ConfigureAppConfiguration` et appelez `AddCommandLine` en dernier.</span><span class="sxs-lookup"><span data-stu-id="2d045-639">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="2d045-640">**Exemple**</span><span class="sxs-lookup"><span data-stu-id="2d045-640">**Example**</span></span>

<span data-ttu-id="2d045-641">L’exemple d’application tire parti de la méthode pratique statique `CreateDefaultBuilder` pour générer l’hôte, qui inclut un appel à <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="2d045-641">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="2d045-642">Ouvrez une invite de commandes dans le répertoire du projet.</span><span class="sxs-lookup"><span data-stu-id="2d045-642">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="2d045-643">Fournissez un argument de ligne de commande à la commande `dotnet run`, `dotnet run CommandLineKey=CommandLineValue`.</span><span class="sxs-lookup"><span data-stu-id="2d045-643">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="2d045-644">Une fois que l’application est en cours d’exécution, ouvrez un navigateur à l’application à l’adresse `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="2d045-644">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="2d045-645">Notez que la sortie contient la paire clé-valeur pour l’argument de ligne de commande de configuration fourni à `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="2d045-645">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="2d045-646">Arguments</span><span class="sxs-lookup"><span data-stu-id="2d045-646">Arguments</span></span>

<span data-ttu-id="2d045-647">La valeur doit suivre un signe égal (`=`) ou la clé doit avoir un préfixe (`--` ou `/`) lorsque la valeur suit un espace.</span><span class="sxs-lookup"><span data-stu-id="2d045-647">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="2d045-648">La valeur n’est pas requise si un signe égal est utilisé (par exemple, `CommandLineKey=`).</span><span class="sxs-lookup"><span data-stu-id="2d045-648">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="2d045-649">Préfixe de clé</span><span class="sxs-lookup"><span data-stu-id="2d045-649">Key prefix</span></span>               | <span data-ttu-id="2d045-650">Exemple</span><span class="sxs-lookup"><span data-stu-id="2d045-650">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="2d045-651">Aucun préfixe</span><span class="sxs-lookup"><span data-stu-id="2d045-651">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="2d045-652">Deux tirets (`--`)</span><span class="sxs-lookup"><span data-stu-id="2d045-652">Two dashes (`--`)</span></span>        | <span data-ttu-id="2d045-653">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="2d045-653">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="2d045-654">Barre oblique (`/`)</span><span class="sxs-lookup"><span data-stu-id="2d045-654">Forward slash (`/`)</span></span>      | <span data-ttu-id="2d045-655">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="2d045-655">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="2d045-656">Dans la même commande, ne mélangez pas des paires clé-valeur de l’argument de ligne de commande qui utilisent un signe égal avec des paires clé-valeur qui utilisent un espace.</span><span class="sxs-lookup"><span data-stu-id="2d045-656">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="2d045-657">Exemples de commandes :</span><span class="sxs-lookup"><span data-stu-id="2d045-657">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="2d045-658">Correspondances de commutateur</span><span class="sxs-lookup"><span data-stu-id="2d045-658">Switch mappings</span></span>

<span data-ttu-id="2d045-659">Les correspondances de commutateur permettent une logique de remplacement des noms de clés.</span><span class="sxs-lookup"><span data-stu-id="2d045-659">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="2d045-660">Lorsque la configuration de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>construction manuelle avec un , <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> fournir un dictionnaire de remplacements de commutateur à la méthode.</span><span class="sxs-lookup"><span data-stu-id="2d045-660">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="2d045-661">Quand le dictionnaire de correspondances de commutateur est utilisé, il est vérifié afin de déterminer s’il contient une clé correspondant à celle fournie par un argument de ligne de commande.</span><span class="sxs-lookup"><span data-stu-id="2d045-661">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="2d045-662">Si la clé de ligne de commande est trouvée dans le dictionnaire, la valeur du dictionnaire (le remplacement de la clé) est repassée pour définir la paire clé-valeur dans la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="2d045-662">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="2d045-663">Une correspondance de commutateur est nécessaire pour chaque clé de ligne de commande préfixée avec un tiret unique (`-`).</span><span class="sxs-lookup"><span data-stu-id="2d045-663">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="2d045-664">Règles des clés du dictionnaire de correspondances de commutateur :</span><span class="sxs-lookup"><span data-stu-id="2d045-664">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="2d045-665">Les commutateurs doivent commencer par un tiret (`-`) ou un double tiret (`--`).</span><span class="sxs-lookup"><span data-stu-id="2d045-665">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="2d045-666">Le dictionnaire de correspondances de commutateur ne doit pas contenir de clés en double.</span><span class="sxs-lookup"><span data-stu-id="2d045-666">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="2d045-667">Créez un dictionnaire des mappages de commutateurs.</span><span class="sxs-lookup"><span data-stu-id="2d045-667">Create a switch mappings dictionary.</span></span> <span data-ttu-id="2d045-668">Dans l’exemple suivant, deux mappages de commutateurs sont créés :</span><span class="sxs-lookup"><span data-stu-id="2d045-668">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="2d045-669">Lorsque l’hôte est généré, appelez `AddCommandLine` avec le dictionnaire de mappages de commutateurs :</span><span class="sxs-lookup"><span data-stu-id="2d045-669">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="2d045-670">Pour les applications qui utilisent des mappages de commutateurs, l’appel à `CreateDefaultBuilder` ne doit pas passer d’arguments.</span><span class="sxs-lookup"><span data-stu-id="2d045-670">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="2d045-671">L’appel `AddCommandLine` de la méthode `CreateDefaultBuilder` n’inclut pas de commutateurs mappés et il n’existe aucun moyen de transmettre le dictionnaire de correspondance de commutateur à `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="2d045-671">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="2d045-672">La solution ne consiste pas à transmettre les arguments à `CreateDefaultBuilder`, mais plutôt à permettre à la méthode `AddCommandLine` de la méthode `ConfigurationBuilder` de traiter les arguments et le dictionnaire de mappage de commutateur.</span><span class="sxs-lookup"><span data-stu-id="2d045-672">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="2d045-673">Une fois le dictionnaire de correspondances de commutateur créé, il contient les données affichées dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="2d045-673">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="2d045-674">Clé</span><span class="sxs-lookup"><span data-stu-id="2d045-674">Key</span></span>       | <span data-ttu-id="2d045-675">Valeur</span><span class="sxs-lookup"><span data-stu-id="2d045-675">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="2d045-676">Si les clés mappées au commutateur sont utilisées lors du démarrage de l’application, la configuration reçoit la valeur de configuration sur la clé fournie par le dictionnaire :</span><span class="sxs-lookup"><span data-stu-id="2d045-676">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="2d045-677">Après avoir exécuté la commande précédente, la configuration contient les valeurs indiquées dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="2d045-677">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="2d045-678">Clé</span><span class="sxs-lookup"><span data-stu-id="2d045-678">Key</span></span>               | <span data-ttu-id="2d045-679">Valeur</span><span class="sxs-lookup"><span data-stu-id="2d045-679">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="2d045-680">Fournisseur de configuration de variables d’environnement</span><span class="sxs-lookup"><span data-stu-id="2d045-680">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="2d045-681"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> charge la configuration à partir des paires clé-valeur de la variable d’environnement lors de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="2d045-681">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="2d045-682">Pour activer la configuration des variables d’environnement, appelez la méthode d’extension <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> sur une instance de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="2d045-682">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="2d045-683">[Azure App Service](https://azure.microsoft.com/services/app-service/) permet de définir des variables de l’environnement dans le portail Azure qui peuvent remplacer la configuration de l’application à l’aide du fournisseur de configuration des variables d’environnement.</span><span class="sxs-lookup"><span data-stu-id="2d045-683">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="2d045-684">Pour plus d’informations, consultez [Azure Apps : remplacer la configuration de l’application à l’aide du portail Azure](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="2d045-684">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="2d045-685">`AddEnvironmentVariables` sert à charger les variables d’environnement préfixées avec `ASPNETCORE_` pour la [configuration hôte](#host-versus-app-configuration) lorsqu’un nouveau générateur d’hôte est initialisé avec l’[hôte web](xref:fundamentals/host/web-host) et que `CreateDefaultBuilder` est appelé.</span><span class="sxs-lookup"><span data-stu-id="2d045-685">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="2d045-686">Pour plus d’informations, consultez la section [Configuration par défaut](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="2d045-686">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="2d045-687">`CreateDefaultBuilder` charge également :</span><span class="sxs-lookup"><span data-stu-id="2d045-687">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="2d045-688">Configuration de l’application à partir de variables d’environnement sans préfixe en appelant `AddEnvironmentVariables` sans préfixe.</span><span class="sxs-lookup"><span data-stu-id="2d045-688">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="2d045-689">Configuration facultative à partir des fichiers *appsettings.json* et *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="2d045-689">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="2d045-690">[Secrets utilisateur (Secret Manager)](xref:security/app-secrets) dans l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="2d045-690">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="2d045-691">Arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="2d045-691">Command-line arguments.</span></span>

<span data-ttu-id="2d045-692">Le fournisseur de configuration de variables d’environnement est appelé une fois que la configuration est établie à partir des secrets utilisateur et des fichiers *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="2d045-692">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="2d045-693">Le fait d’appeler le fournisseur ainsi permet de lire les variables d’environnement pendant l’exécution pour substituer la configuration définie par les secrets utilisateur et les fichiers *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="2d045-693">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="2d045-694">Pour fournir la configuration de l’application à partir `ConfigureAppConfiguration` de `AddEnvironmentVariables` variables d’environnement supplémentaires, appelez les fournisseurs supplémentaires de l’application et appelez avec le préfixe :</span><span class="sxs-lookup"><span data-stu-id="2d045-694">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="2d045-695">Appelez `AddEnvironmentVariables` en dernier pour permettre des variables de l’environnement avec le préfixe donné pour remplacer les valeurs d’autres fournisseurs.</span><span class="sxs-lookup"><span data-stu-id="2d045-695">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="2d045-696">**Exemple**</span><span class="sxs-lookup"><span data-stu-id="2d045-696">**Example**</span></span>

<span data-ttu-id="2d045-697">L’exemple d’application tire parti de la méthode pratique statique `CreateDefaultBuilder` pour générer l’hôte, qui inclut un appel à `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="2d045-697">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="2d045-698">Exécutez l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="2d045-698">Run the sample app.</span></span> <span data-ttu-id="2d045-699">Ouvrez un navigateur vers l’application avec l’adresse `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="2d045-699">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="2d045-700">Notez que la sortie contient la paire clé-valeur pour la variable d’environnement `ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="2d045-700">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="2d045-701">La valeur reflète l’environnement dans lequel l’application est en cours d’exécution, en général `Development` lors de l’exécution locale.</span><span class="sxs-lookup"><span data-stu-id="2d045-701">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="2d045-702">Pour que la liste des variables d’environnement restituée par l’application soit courte, l’application filtre les variables d’environnement.</span><span class="sxs-lookup"><span data-stu-id="2d045-702">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="2d045-703">Consultez le fichier *Pages/Index.cshtml.cs* de l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="2d045-703">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="2d045-704">Pour exposer toutes les variables de l’environnement disponibles à l’application, modifiez les `FilteredConfiguration` *Pages/Index.cshtml.cs* à ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="2d045-704">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="2d045-705">Préfixes</span><span class="sxs-lookup"><span data-stu-id="2d045-705">Prefixes</span></span>

<span data-ttu-id="2d045-706">Les variables d’environnement chargées dans la configuration de l’application sont filtrées lors de la fourniture d’un préfixe à la `AddEnvironmentVariables` méthode.</span><span class="sxs-lookup"><span data-stu-id="2d045-706">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="2d045-707">Par exemple, pour filtrer les variables d’environnement sur le préfixe `CUSTOM_`, fournissez le préfixe au fournisseur de configuration :</span><span class="sxs-lookup"><span data-stu-id="2d045-707">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="2d045-708">Le préfixe est supprimé lorsque les paires clé-valeur de la configuration sont créées.</span><span class="sxs-lookup"><span data-stu-id="2d045-708">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="2d045-709">Lorsque le générateur d’hôte est créé, la configuration de l’hôte est fournie par des variables d’environnement.</span><span class="sxs-lookup"><span data-stu-id="2d045-709">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="2d045-710">Pour plus d’informations sur le préfixe utilisé pour ces variables d’environnement, consultez la section [Configuration par défaut](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="2d045-710">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="2d045-711">**Préfixes des chaînes de connexion**</span><span class="sxs-lookup"><span data-stu-id="2d045-711">**Connection string prefixes**</span></span>

<span data-ttu-id="2d045-712">L’API Configuration possède des règles de traitement spéciales pour quatre variables d’environnement de chaîne de connexion impliquées dans la configuration des chaînes de connexion Azure pour l’environnement de l’application.</span><span class="sxs-lookup"><span data-stu-id="2d045-712">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="2d045-713">Les variables d’environnement avec les préfixes indiqués dans le tableau sont chargées dans l’application si aucun préfixe n’est fourni à `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="2d045-713">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="2d045-714">Préfixe de la chaîne de connexion</span><span class="sxs-lookup"><span data-stu-id="2d045-714">Connection string prefix</span></span> | <span data-ttu-id="2d045-715">Fournisseur</span><span class="sxs-lookup"><span data-stu-id="2d045-715">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="2d045-716">Fournisseur personnalisé</span><span class="sxs-lookup"><span data-stu-id="2d045-716">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="2d045-717">MySQL</span><span class="sxs-lookup"><span data-stu-id="2d045-717">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="2d045-718">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="2d045-718">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="2d045-719">SQL Server</span><span class="sxs-lookup"><span data-stu-id="2d045-719">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="2d045-720">Quand une variable d’environnement est découverte et chargée dans la configuration avec l’un des quatre préfixes indiqués dans le tableau :</span><span class="sxs-lookup"><span data-stu-id="2d045-720">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="2d045-721">La clé de configuration est créée en supprimant le préfixe de la variable d’environnement et en ajoutant une section de clé de configuration (`ConnectionStrings`).</span><span class="sxs-lookup"><span data-stu-id="2d045-721">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="2d045-722">Une nouvelle paire clé-valeur de configuration est créée qui représente le fournisseur de connexion de base de données (à l’exception de `CUSTOMCONNSTR_`, qui ne possède aucun fournisseur indiqué).</span><span class="sxs-lookup"><span data-stu-id="2d045-722">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="2d045-723">Clé de variable d’environnement</span><span class="sxs-lookup"><span data-stu-id="2d045-723">Environment variable key</span></span> | <span data-ttu-id="2d045-724">Clé de configuration convertie</span><span class="sxs-lookup"><span data-stu-id="2d045-724">Converted configuration key</span></span> | <span data-ttu-id="2d045-725">Entrée de configuration de fournisseur</span><span class="sxs-lookup"><span data-stu-id="2d045-725">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="2d045-726">Entrée de configuration non créée.</span><span class="sxs-lookup"><span data-stu-id="2d045-726">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="2d045-727">Clé : `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="2d045-727">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="2d045-728">Valeur: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="2d045-728">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="2d045-729">Clé : `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="2d045-729">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="2d045-730">Valeur: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="2d045-730">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="2d045-731">Clé : `ConnectionStrings:{KEY}_ProviderName` :</span><span class="sxs-lookup"><span data-stu-id="2d045-731">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="2d045-732">Valeur: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="2d045-732">Value: `System.Data.SqlClient`</span></span>  |

<span data-ttu-id="2d045-733">**Exemple**</span><span class="sxs-lookup"><span data-stu-id="2d045-733">**Example**</span></span>

<span data-ttu-id="2d045-734">Une variable personnalisée d’environnement de chaîne de connexion est créée sur le serveur :</span><span class="sxs-lookup"><span data-stu-id="2d045-734">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="2d045-735">Nom &ndash;`CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="2d045-735">Name &ndash; `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="2d045-736">Valeur &ndash;`Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="2d045-736">Value &ndash; `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="2d045-737">Si `IConfiguration` elle est injectée `_config`et assignée à un champ nommé, lisez la valeur :</span><span class="sxs-lookup"><span data-stu-id="2d045-737">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="2d045-738">Fournisseur de configuration de fichier</span><span class="sxs-lookup"><span data-stu-id="2d045-738">File Configuration Provider</span></span>

<span data-ttu-id="2d045-739"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> est la classe de base pour charger la configuration à partir du système de fichiers.</span><span class="sxs-lookup"><span data-stu-id="2d045-739"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="2d045-740">Les fournisseurs de configuration suivants sont dédiés à des types de fichiers spécifiques :</span><span class="sxs-lookup"><span data-stu-id="2d045-740">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="2d045-741">Fournisseur de configuration INI</span><span class="sxs-lookup"><span data-stu-id="2d045-741">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="2d045-742">Fournisseur de configuration JSON</span><span class="sxs-lookup"><span data-stu-id="2d045-742">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="2d045-743">Fournisseur de configuration XML</span><span class="sxs-lookup"><span data-stu-id="2d045-743">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="2d045-744">Fournisseur de configuration INI</span><span class="sxs-lookup"><span data-stu-id="2d045-744">INI Configuration Provider</span></span>

<span data-ttu-id="2d045-745"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> charge la configuration à partir des paires clé-valeur du fichier INI lors de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="2d045-745">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="2d045-746">Pour activer la configuration du fichier INI, appelez la méthode d’extension <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> sur une instance de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="2d045-746">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="2d045-747">Le signe deux-points peut servir de délimiteur de section dans la configuration d’un fichier INI.</span><span class="sxs-lookup"><span data-stu-id="2d045-747">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="2d045-748">Les surcharges permettent de spécifier :</span><span class="sxs-lookup"><span data-stu-id="2d045-748">Overloads permit specifying:</span></span>

* <span data-ttu-id="2d045-749">Si le fichier est facultatif.</span><span class="sxs-lookup"><span data-stu-id="2d045-749">Whether the file is optional.</span></span>
* <span data-ttu-id="2d045-750">Si la configuration est rechargée quand le fichier est modifié.</span><span class="sxs-lookup"><span data-stu-id="2d045-750">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="2d045-751">Le <xref:Microsoft.Extensions.FileProviders.IFileProvider> utilisé pour accéder au fichier.</span><span class="sxs-lookup"><span data-stu-id="2d045-751">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="2d045-752">Appelez `ConfigureAppConfiguration` lors de la création de l’hôte pour spécifier la configuration de l’application :</span><span class="sxs-lookup"><span data-stu-id="2d045-752">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="2d045-753">Exemple générique d’un fichier de configuration INI :</span><span class="sxs-lookup"><span data-stu-id="2d045-753">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="2d045-754">Le fichier de configuration précédent charge les clés suivantes avec `value` :</span><span class="sxs-lookup"><span data-stu-id="2d045-754">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="2d045-755">section0:key0</span><span class="sxs-lookup"><span data-stu-id="2d045-755">section0:key0</span></span>
* <span data-ttu-id="2d045-756">section0:key1</span><span class="sxs-lookup"><span data-stu-id="2d045-756">section0:key1</span></span>
* <span data-ttu-id="2d045-757">section1:subsection:key</span><span class="sxs-lookup"><span data-stu-id="2d045-757">section1:subsection:key</span></span>
* <span data-ttu-id="2d045-758">section2:subsection0:key</span><span class="sxs-lookup"><span data-stu-id="2d045-758">section2:subsection0:key</span></span>
* <span data-ttu-id="2d045-759">section2:subsection1:key</span><span class="sxs-lookup"><span data-stu-id="2d045-759">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="2d045-760">Fournisseur de configuration JSON</span><span class="sxs-lookup"><span data-stu-id="2d045-760">JSON Configuration Provider</span></span>

<span data-ttu-id="2d045-761"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> charge la configuration à partir des paires clé-valeur du fichier JSON lors de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="2d045-761">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="2d045-762">Pour activer la configuration du fichier JSON, appelez la méthode d’extension <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> sur une instance de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="2d045-762">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="2d045-763">Les surcharges permettent de spécifier :</span><span class="sxs-lookup"><span data-stu-id="2d045-763">Overloads permit specifying:</span></span>

* <span data-ttu-id="2d045-764">Si le fichier est facultatif.</span><span class="sxs-lookup"><span data-stu-id="2d045-764">Whether the file is optional.</span></span>
* <span data-ttu-id="2d045-765">Si la configuration est rechargée quand le fichier est modifié.</span><span class="sxs-lookup"><span data-stu-id="2d045-765">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="2d045-766">Le <xref:Microsoft.Extensions.FileProviders.IFileProvider> utilisé pour accéder au fichier.</span><span class="sxs-lookup"><span data-stu-id="2d045-766">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="2d045-767">`AddJsonFile`est automatiquement appelé deux fois quand un `CreateDefaultBuilder`nouveau constructeur hôte est initialisé avec .</span><span class="sxs-lookup"><span data-stu-id="2d045-767">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="2d045-768">La méthode est appelée pour charger la configuration à partir de :</span><span class="sxs-lookup"><span data-stu-id="2d045-768">The method is called to load configuration from:</span></span>

* <span data-ttu-id="2d045-769">*appSettings.JSON* &ndash; Ce fichier est lu en premier.</span><span class="sxs-lookup"><span data-stu-id="2d045-769">*appsettings.json* &ndash; This file is read first.</span></span> <span data-ttu-id="2d045-770">La version de l’environnement du fichier peut remplacer les valeurs fournies par le fichier *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="2d045-770">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="2d045-771">*appsettings.{Environment}.json* &ndash; La version de l’environnement du fichier est chargée à partir du fichier [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="2d045-771">*appsettings.{Environment}.json* &ndash; The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="2d045-772">Pour plus d’informations, consultez la section [Configuration par défaut](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="2d045-772">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="2d045-773">`CreateDefaultBuilder` charge également :</span><span class="sxs-lookup"><span data-stu-id="2d045-773">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="2d045-774">Variables d'environnement.</span><span class="sxs-lookup"><span data-stu-id="2d045-774">Environment variables.</span></span>
* <span data-ttu-id="2d045-775">[Secrets utilisateur (Secret Manager)](xref:security/app-secrets) dans l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="2d045-775">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="2d045-776">Arguments de ligne de commande</span><span class="sxs-lookup"><span data-stu-id="2d045-776">Command-line arguments.</span></span>

<span data-ttu-id="2d045-777">Le Fournisseur de configuration JSON est établi en premier.</span><span class="sxs-lookup"><span data-stu-id="2d045-777">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="2d045-778">Par conséquent, les secrets utilisateur, les variables d’environnement et les arguments de ligne de commande remplacent la configuration définie par les fichiers *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="2d045-778">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="2d045-779">Appelez `ConfigureAppConfiguration` lors de la création de l’hôte pour spécifier la configuration de l’application pour les fichiers autres qu’*appsettings.json* et *appsettings. {Environment} .json* :</span><span class="sxs-lookup"><span data-stu-id="2d045-779">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="2d045-780">**Exemple**</span><span class="sxs-lookup"><span data-stu-id="2d045-780">**Example**</span></span>

<span data-ttu-id="2d045-781">L’application d’échantillon tire `CreateDefaultBuilder` parti de la méthode de `AddJsonFile`commodité statique pour construire l’hôte, qui comprend deux appels à :</span><span class="sxs-lookup"><span data-stu-id="2d045-781">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="2d045-782">Le premier `AddJsonFile` appel pour charger la configuration de *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="2d045-782">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="2d045-783">Le deuxième `AddJsonFile` appel pour charger la configuration des *applications. Environnement.json*.</span><span class="sxs-lookup"><span data-stu-id="2d045-783">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="2d045-784">Pour *les applications. Development.json* dans l’application de l’échantillon, le fichier suivant est chargé:</span><span class="sxs-lookup"><span data-stu-id="2d045-784">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="2d045-785">Exécutez l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="2d045-785">Run the sample app.</span></span> <span data-ttu-id="2d045-786">Ouvrez un navigateur vers l’application avec l’adresse `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="2d045-786">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="2d045-787">La sortie contient des paires de valeur clé pour la configuration en fonction de l’environnement de l’application.</span><span class="sxs-lookup"><span data-stu-id="2d045-787">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="2d045-788">Le niveau de `Logging:LogLevel:Default` journal `Debug` pour la clé est lors de l’exécution de l’application dans l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="2d045-788">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="2d045-789">Exécutez à nouveau l’application d’échantillon dans l’environnement de production :</span><span class="sxs-lookup"><span data-stu-id="2d045-789">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="2d045-790">Ouvrez le fichier *Properties/launchSettings.json.*</span><span class="sxs-lookup"><span data-stu-id="2d045-790">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="2d045-791">Dans `ConfigurationSample` le profil, changer `ASPNETCORE_ENVIRONMENT` la valeur `Production`de la variable de l’environnement à .</span><span class="sxs-lookup"><span data-stu-id="2d045-791">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="2d045-792">Enregistrer le fichier et `dotnet run` exécuter l’application avec dans une coque de commande.</span><span class="sxs-lookup"><span data-stu-id="2d045-792">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="2d045-793">Les paramètres dans les *applications. Development.json* ne remplace plus les paramètres de *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="2d045-793">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="2d045-794">Le niveau de `Logging:LogLevel:Default` journal `Warning`pour la clé est .</span><span class="sxs-lookup"><span data-stu-id="2d045-794">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="2d045-795">Fournisseur de configuration XML</span><span class="sxs-lookup"><span data-stu-id="2d045-795">XML Configuration Provider</span></span>

<span data-ttu-id="2d045-796"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> charge la configuration à partir des paires clé-valeur du fichier XML lors de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="2d045-796">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="2d045-797">Pour activer la configuration du fichier XML, appelez la méthode d’extension <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> sur une instance de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="2d045-797">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="2d045-798">Les surcharges permettent de spécifier :</span><span class="sxs-lookup"><span data-stu-id="2d045-798">Overloads permit specifying:</span></span>

* <span data-ttu-id="2d045-799">Si le fichier est facultatif.</span><span class="sxs-lookup"><span data-stu-id="2d045-799">Whether the file is optional.</span></span>
* <span data-ttu-id="2d045-800">Si la configuration est rechargée quand le fichier est modifié.</span><span class="sxs-lookup"><span data-stu-id="2d045-800">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="2d045-801">Le <xref:Microsoft.Extensions.FileProviders.IFileProvider> utilisé pour accéder au fichier.</span><span class="sxs-lookup"><span data-stu-id="2d045-801">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="2d045-802">Le nœud racine du fichier de configuration est ignoré lorsque les paires clé-valeur de la configuration sont créées.</span><span class="sxs-lookup"><span data-stu-id="2d045-802">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="2d045-803">Ne spécifiez pas une définition de type de document (DTD) ou un espace de noms dans le fichier.</span><span class="sxs-lookup"><span data-stu-id="2d045-803">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="2d045-804">Appelez `ConfigureAppConfiguration` lors de la création de l’hôte pour spécifier la configuration de l’application :</span><span class="sxs-lookup"><span data-stu-id="2d045-804">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="2d045-805">Les fichiers de configuration XML peuvent utiliser des noms d’éléments distincts pour les sections répétitives :</span><span class="sxs-lookup"><span data-stu-id="2d045-805">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="2d045-806">Le fichier de configuration précédent charge les clés suivantes avec `value` :</span><span class="sxs-lookup"><span data-stu-id="2d045-806">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="2d045-807">section0:key0</span><span class="sxs-lookup"><span data-stu-id="2d045-807">section0:key0</span></span>
* <span data-ttu-id="2d045-808">section0:key1</span><span class="sxs-lookup"><span data-stu-id="2d045-808">section0:key1</span></span>
* <span data-ttu-id="2d045-809">section1:key0</span><span class="sxs-lookup"><span data-stu-id="2d045-809">section1:key0</span></span>
* <span data-ttu-id="2d045-810">section1:key1</span><span class="sxs-lookup"><span data-stu-id="2d045-810">section1:key1</span></span>

<span data-ttu-id="2d045-811">Les éléments répétitifs qui utilisent le même nom d’élément fonctionnent si l’attribut `name` est utilisé pour distinguer les éléments :</span><span class="sxs-lookup"><span data-stu-id="2d045-811">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="2d045-812">Le fichier de configuration précédent charge les clés suivantes avec `value` :</span><span class="sxs-lookup"><span data-stu-id="2d045-812">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="2d045-813">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="2d045-813">section:section0:key:key0</span></span>
* <span data-ttu-id="2d045-814">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="2d045-814">section:section0:key:key1</span></span>
* <span data-ttu-id="2d045-815">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="2d045-815">section:section1:key:key0</span></span>
* <span data-ttu-id="2d045-816">section:section1:key:key1</span><span class="sxs-lookup"><span data-stu-id="2d045-816">section:section1:key:key1</span></span>

<span data-ttu-id="2d045-817">Les attributs peuvent être utilisés pour fournir des valeurs :</span><span class="sxs-lookup"><span data-stu-id="2d045-817">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="2d045-818">Le fichier de configuration précédent charge les clés suivantes avec `value` :</span><span class="sxs-lookup"><span data-stu-id="2d045-818">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="2d045-819">key:attribute</span><span class="sxs-lookup"><span data-stu-id="2d045-819">key:attribute</span></span>
* <span data-ttu-id="2d045-820">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="2d045-820">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="2d045-821">Fournisseur de configuration clé par fichier</span><span class="sxs-lookup"><span data-stu-id="2d045-821">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="2d045-822">Le <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> utilise les fichiers d’un répertoire en tant que paires clé-valeur de configuration.</span><span class="sxs-lookup"><span data-stu-id="2d045-822">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="2d045-823">La clé est le nom de fichier.</span><span class="sxs-lookup"><span data-stu-id="2d045-823">The key is the file name.</span></span> <span data-ttu-id="2d045-824">La valeur contient le contenu du fichier.</span><span class="sxs-lookup"><span data-stu-id="2d045-824">The value contains the file's contents.</span></span> <span data-ttu-id="2d045-825">Le Fournisseur de configuration Clé par fichier est utilisé dans les scénarios d’hébergement de Docker.</span><span class="sxs-lookup"><span data-stu-id="2d045-825">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="2d045-826">Pour activer la configuration clé par fichier, appelez la méthode d’extension <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> sur une instance de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="2d045-826">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="2d045-827">Le `directoryPath` aux fichiers doit être un chemin d’accès absolu.</span><span class="sxs-lookup"><span data-stu-id="2d045-827">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="2d045-828">Les surcharges permettent de spécifier :</span><span class="sxs-lookup"><span data-stu-id="2d045-828">Overloads permit specifying:</span></span>

* <span data-ttu-id="2d045-829">Un délégué `Action<KeyPerFileConfigurationSource>` qui configure la source.</span><span class="sxs-lookup"><span data-stu-id="2d045-829">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="2d045-830">Si le répertoire est facultatif et le chemin d’accès au répertoire.</span><span class="sxs-lookup"><span data-stu-id="2d045-830">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="2d045-831">Le double trait de soulignement (`__`) est utilisé comme un délimiteur de clé de configuration dans les noms de fichiers.</span><span class="sxs-lookup"><span data-stu-id="2d045-831">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="2d045-832">Par exemple, le nom de fichier `Logging__LogLevel__System` génère la clé de configuration `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="2d045-832">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="2d045-833">Appelez `ConfigureAppConfiguration` lors de la création de l’hôte pour spécifier la configuration de l’application :</span><span class="sxs-lookup"><span data-stu-id="2d045-833">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="2d045-834">Fournisseur de configuration de mémoire</span><span class="sxs-lookup"><span data-stu-id="2d045-834">Memory Configuration Provider</span></span>

<span data-ttu-id="2d045-835">Le <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> utilise une collection en mémoire en tant que paires clé-valeur de configuration.</span><span class="sxs-lookup"><span data-stu-id="2d045-835">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="2d045-836">Pour activer la configuration de la collection en mémoire, appelez la méthode d’extension <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> sur une instance de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="2d045-836">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="2d045-837">Le fournisseur de configuration peut être initialisé avec un `IEnumerable<KeyValuePair<String,String>>`.</span><span class="sxs-lookup"><span data-stu-id="2d045-837">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="2d045-838">Appelez `ConfigureAppConfiguration` lors de la création de l’hôte pour spécifier la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="2d045-838">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="2d045-839">Dans l’exemple suivant, un dictionnaire de configuration est créé :</span><span class="sxs-lookup"><span data-stu-id="2d045-839">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="2d045-840">Le dictionnaire est utilisé avec un appel à `AddInMemoryCollection` pour fournir la configuration :</span><span class="sxs-lookup"><span data-stu-id="2d045-840">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="2d045-841">GetValue</span><span class="sxs-lookup"><span data-stu-id="2d045-841">GetValue</span></span>

<span data-ttu-id="2d045-842">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*)extrait une seule valeur de la configuration avec une clé spécifiée et la convertit en type de noncollection spécifié.</span><span class="sxs-lookup"><span data-stu-id="2d045-842">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="2d045-843">Une surcharge accepte une valeur par défaut.</span><span class="sxs-lookup"><span data-stu-id="2d045-843">An overload accepts a default value.</span></span>

<span data-ttu-id="2d045-844">L’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="2d045-844">The following example:</span></span>

* <span data-ttu-id="2d045-845">Extrait la valeur de chaîne de la configuration avec la clé `NumberKey`.</span><span class="sxs-lookup"><span data-stu-id="2d045-845">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="2d045-846">Si `NumberKey` est introuvable dans les clés de configuration, la valeur par défaut de `99` est utilisée.</span><span class="sxs-lookup"><span data-stu-id="2d045-846">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="2d045-847">Tape la valeur comme `int`.</span><span class="sxs-lookup"><span data-stu-id="2d045-847">Types the value as an `int`.</span></span>
* <span data-ttu-id="2d045-848">Stocke la valeur dans la propriété `NumberConfig` pour une utilisation par la page.</span><span class="sxs-lookup"><span data-stu-id="2d045-848">Stores the value in the `NumberConfig` property for use by the page.</span></span>

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

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="2d045-849">GetSection, GetChildren et Exists</span><span class="sxs-lookup"><span data-stu-id="2d045-849">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="2d045-850">Pour les exemples qui suivent, utilisez le fichier JSON suivant.</span><span class="sxs-lookup"><span data-stu-id="2d045-850">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="2d045-851">Quatre clés se trouvent dans deux sections, dont l’une inclut deux sous-sections :</span><span class="sxs-lookup"><span data-stu-id="2d045-851">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="2d045-852">Lorsque le fichier est lu dans la configuration, les clés hiérarchiques uniques suivantes sont créées pour stocker les valeurs de la configuration :</span><span class="sxs-lookup"><span data-stu-id="2d045-852">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="2d045-853">section0:key0</span><span class="sxs-lookup"><span data-stu-id="2d045-853">section0:key0</span></span>
* <span data-ttu-id="2d045-854">section0:key1</span><span class="sxs-lookup"><span data-stu-id="2d045-854">section0:key1</span></span>
* <span data-ttu-id="2d045-855">section1:key0</span><span class="sxs-lookup"><span data-stu-id="2d045-855">section1:key0</span></span>
* <span data-ttu-id="2d045-856">section1:key1</span><span class="sxs-lookup"><span data-stu-id="2d045-856">section1:key1</span></span>
* <span data-ttu-id="2d045-857">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="2d045-857">section2:subsection0:key0</span></span>
* <span data-ttu-id="2d045-858">section2:subsection0:key1</span><span class="sxs-lookup"><span data-stu-id="2d045-858">section2:subsection0:key1</span></span>
* <span data-ttu-id="2d045-859">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="2d045-859">section2:subsection1:key0</span></span>
* <span data-ttu-id="2d045-860">section2:subsection1:key1</span><span class="sxs-lookup"><span data-stu-id="2d045-860">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="2d045-861">GetSection</span><span class="sxs-lookup"><span data-stu-id="2d045-861">GetSection</span></span>

<span data-ttu-id="2d045-862">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extrait une sous-section de la configuration avec la clé de sous-section spécifiée.</span><span class="sxs-lookup"><span data-stu-id="2d045-862">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="2d045-863">Pour retourner un <xref:Microsoft.Extensions.Configuration.IConfigurationSection> contenant uniquement les paires clé-valeur dans `section1`, appelez `GetSection` et fournissez le nom de section :</span><span class="sxs-lookup"><span data-stu-id="2d045-863">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="2d045-864">`configSection` n’a pas de valeur, seulement une clé et un chemin.</span><span class="sxs-lookup"><span data-stu-id="2d045-864">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="2d045-865">De même, pour obtenir les valeurs des clés dans `section2:subsection0`, appelez `GetSection` et fournissez le chemin d’accès de la section :</span><span class="sxs-lookup"><span data-stu-id="2d045-865">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="2d045-866">`GetSection` ne retourne jamais `null`.</span><span class="sxs-lookup"><span data-stu-id="2d045-866">`GetSection` never returns `null`.</span></span> <span data-ttu-id="2d045-867">Si aucune section correspondante n’est trouvée, une valeur `IConfigurationSection` vide est retournée.</span><span class="sxs-lookup"><span data-stu-id="2d045-867">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="2d045-868">Quand `GetSection` retourne une section correspondante, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> n’est pas rempli.</span><span class="sxs-lookup"><span data-stu-id="2d045-868">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="2d045-869"><xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> et <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> sont retournés quand la section existe.</span><span class="sxs-lookup"><span data-stu-id="2d045-869">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="2d045-870">GetChildren</span><span class="sxs-lookup"><span data-stu-id="2d045-870">GetChildren</span></span>

<span data-ttu-id="2d045-871">Un appel à [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) sur `section2` obtient un `IEnumerable<IConfigurationSection>` qui inclut :</span><span class="sxs-lookup"><span data-stu-id="2d045-871">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="2d045-872">Exists</span><span class="sxs-lookup"><span data-stu-id="2d045-872">Exists</span></span>

<span data-ttu-id="2d045-873">Utilisez [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) pour déterminer si une section de configuration existe :</span><span class="sxs-lookup"><span data-stu-id="2d045-873">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="2d045-874">Compte tenu des données d’exemple, `sectionExists` est `false`, car il n’y a pas de section `section2:subsection2` dans les données de configuration.</span><span class="sxs-lookup"><span data-stu-id="2d045-874">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="2d045-875">Établir une liaison à un graphe d’objets</span><span class="sxs-lookup"><span data-stu-id="2d045-875">Bind to an object graph</span></span>

<span data-ttu-id="2d045-876"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> est capable de lier l’intégralité d’un graphe d’objets POCO.</span><span class="sxs-lookup"><span data-stu-id="2d045-876"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="2d045-877">Comme pour la liaison d’un objet simple, seules les propriétés publiques de lecture/écriture sont liées.</span><span class="sxs-lookup"><span data-stu-id="2d045-877">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="2d045-878">L’exemple contient un modèle `TvShow` dont le graphe d’objets inclut les classes `Metadata` et `Actors` (*Models/TvShow.cs*) :</span><span class="sxs-lookup"><span data-stu-id="2d045-878">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="2d045-879">L’exemple d’application a un fichier *tvshow.xml* contenant les données de configuration :</span><span class="sxs-lookup"><span data-stu-id="2d045-879">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="2d045-880">La configuration est liée à l’intégralité du graphe d’objets `TvShow` avec la méthode `Bind`.</span><span class="sxs-lookup"><span data-stu-id="2d045-880">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="2d045-881">L’instance liée est affectée à une propriété pour le rendu :</span><span class="sxs-lookup"><span data-stu-id="2d045-881">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="2d045-882">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)lie et renvoie le type spécifié.</span><span class="sxs-lookup"><span data-stu-id="2d045-882">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="2d045-883">Il est plus pratique d’utiliser `Get<T>` que `Bind`.</span><span class="sxs-lookup"><span data-stu-id="2d045-883">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="2d045-884">Le code suivant montre `Get<T>` comment utiliser avec l’exemple précédent :</span><span class="sxs-lookup"><span data-stu-id="2d045-884">The following code shows how to use `Get<T>` with the preceding example:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="2d045-885">Lier un tableau à une classe</span><span class="sxs-lookup"><span data-stu-id="2d045-885">Bind an array to a class</span></span>

<span data-ttu-id="2d045-886">*L’exemple d’application illustre les concepts abordés dans cette section.*</span><span class="sxs-lookup"><span data-stu-id="2d045-886">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="2d045-887"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> prend en charge la liaison de tableaux à des objets à l’aide d’index de tableau dans les clés de configuration.</span><span class="sxs-lookup"><span data-stu-id="2d045-887">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="2d045-888">Tout format de tableau qui expose`:0:` `:1:`un &hellip; `:{n}:`segment de clé numérique ( , , ) est capable de la liaison de tableau à un tableau de classe POCO.</span><span class="sxs-lookup"><span data-stu-id="2d045-888">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="2d045-889">La liaison est fournie par convention.</span><span class="sxs-lookup"><span data-stu-id="2d045-889">Binding is provided by convention.</span></span> <span data-ttu-id="2d045-890">Les fournisseurs de configuration personnalisés ne sont pas obligés d’implémenter la liaison de tableau.</span><span class="sxs-lookup"><span data-stu-id="2d045-890">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="2d045-891">**Traitement de tableau en mémoire**</span><span class="sxs-lookup"><span data-stu-id="2d045-891">**In-memory array processing**</span></span>

<span data-ttu-id="2d045-892">Observez les valeurs et les clés de configuration indiquées dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="2d045-892">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="2d045-893">Clé</span><span class="sxs-lookup"><span data-stu-id="2d045-893">Key</span></span>             | <span data-ttu-id="2d045-894">Valeur</span><span class="sxs-lookup"><span data-stu-id="2d045-894">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="2d045-895">array:entries:0</span><span class="sxs-lookup"><span data-stu-id="2d045-895">array:entries:0</span></span> | <span data-ttu-id="2d045-896">value0</span><span class="sxs-lookup"><span data-stu-id="2d045-896">value0</span></span> |
| <span data-ttu-id="2d045-897">array:entries:1</span><span class="sxs-lookup"><span data-stu-id="2d045-897">array:entries:1</span></span> | <span data-ttu-id="2d045-898">valeur1</span><span class="sxs-lookup"><span data-stu-id="2d045-898">value1</span></span> |
| <span data-ttu-id="2d045-899">array:entries:2</span><span class="sxs-lookup"><span data-stu-id="2d045-899">array:entries:2</span></span> | <span data-ttu-id="2d045-900">valeur2</span><span class="sxs-lookup"><span data-stu-id="2d045-900">value2</span></span> |
| <span data-ttu-id="2d045-901">array:entries:4</span><span class="sxs-lookup"><span data-stu-id="2d045-901">array:entries:4</span></span> | <span data-ttu-id="2d045-902">value4</span><span class="sxs-lookup"><span data-stu-id="2d045-902">value4</span></span> |
| <span data-ttu-id="2d045-903">array:entries:5</span><span class="sxs-lookup"><span data-stu-id="2d045-903">array:entries:5</span></span> | <span data-ttu-id="2d045-904">value5</span><span class="sxs-lookup"><span data-stu-id="2d045-904">value5</span></span> |

<span data-ttu-id="2d045-905">Ces clés et valeurs sont chargées dans l’exemple d’application à l’aide du Fournisseur de configuration de mémoire :</span><span class="sxs-lookup"><span data-stu-id="2d045-905">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="2d045-906">Le tableau ignore une valeur pour l’index &num;3.</span><span class="sxs-lookup"><span data-stu-id="2d045-906">The array skips a value for index &num;3.</span></span> <span data-ttu-id="2d045-907">Le binder de configuration n’est pas capable de lier des valeurs null ou de créer des entrées null dans les objets liés, ce qui deviendra clair dans un moment lorsque le résultat de liaison de ce tableau à un objet est illustré.</span><span class="sxs-lookup"><span data-stu-id="2d045-907">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="2d045-908">Dans l’exemple d’application, une classe POCO est disponible pour contenir les données de configuration liées :</span><span class="sxs-lookup"><span data-stu-id="2d045-908">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="2d045-909">Les données de configuration sont liées à l’objet :</span><span class="sxs-lookup"><span data-stu-id="2d045-909">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="2d045-910">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)la syntaxe peut également être utilisée, ce qui se traduit par un code plus compact :</span><span class="sxs-lookup"><span data-stu-id="2d045-910">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="2d045-911">L’objet lié, une instance de `ArrayExample`, reçoit les données de tableau à partir de la configuration.</span><span class="sxs-lookup"><span data-stu-id="2d045-911">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="2d045-912">Index `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="2d045-912">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="2d045-913">`ArrayExample.Entries` Valeur</span><span class="sxs-lookup"><span data-stu-id="2d045-913">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="2d045-914">0</span><span class="sxs-lookup"><span data-stu-id="2d045-914">0</span></span>                            | <span data-ttu-id="2d045-915">value0</span><span class="sxs-lookup"><span data-stu-id="2d045-915">value0</span></span>                       |
| <span data-ttu-id="2d045-916">1</span><span class="sxs-lookup"><span data-stu-id="2d045-916">1</span></span>                            | <span data-ttu-id="2d045-917">valeur1</span><span class="sxs-lookup"><span data-stu-id="2d045-917">value1</span></span>                       |
| <span data-ttu-id="2d045-918">2</span><span class="sxs-lookup"><span data-stu-id="2d045-918">2</span></span>                            | <span data-ttu-id="2d045-919">valeur2</span><span class="sxs-lookup"><span data-stu-id="2d045-919">value2</span></span>                       |
| <span data-ttu-id="2d045-920">3</span><span class="sxs-lookup"><span data-stu-id="2d045-920">3</span></span>                            | <span data-ttu-id="2d045-921">value4</span><span class="sxs-lookup"><span data-stu-id="2d045-921">value4</span></span>                       |
| <span data-ttu-id="2d045-922">4</span><span class="sxs-lookup"><span data-stu-id="2d045-922">4</span></span>                            | <span data-ttu-id="2d045-923">value5</span><span class="sxs-lookup"><span data-stu-id="2d045-923">value5</span></span>                       |

<span data-ttu-id="2d045-924">L’index &num;3 dans l’objet lié contient les données de configuration pour la clé de configuration `array:4` et sa valeur de `value4`.</span><span class="sxs-lookup"><span data-stu-id="2d045-924">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="2d045-925">Lorsque des données de configuration contenant un tableau sont liées, les index de tableau dans les clés de configuration sont simplement utilisés pour itérer les données de configuration lors de la création de l’objet.</span><span class="sxs-lookup"><span data-stu-id="2d045-925">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="2d045-926">Une valeur null ne peut pas être conservée dans des données de configuration, et une entrée à valeur null n’est pas créée dans un objet lié quand un tableau dans des clés de configuration ignore un ou plusieurs index.</span><span class="sxs-lookup"><span data-stu-id="2d045-926">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="2d045-927">L’élément de configuration manquant pour l’index &num;3 peut être fourni avant la liaison à l’instance `ArrayExample` par n’importe quel fournisseur de configuration qui génère la paire clé-valeur correcte dans la configuration.</span><span class="sxs-lookup"><span data-stu-id="2d045-927">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="2d045-928">Si l’exemple inclut un Fournisseur de configuration JSON supplémentaire avec la paire clé-valeur manquante, `ArrayExample.Entries` correspond à l’intégralité du tableau de configuration :</span><span class="sxs-lookup"><span data-stu-id="2d045-928">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="2d045-929">*missing_value.json* :</span><span class="sxs-lookup"><span data-stu-id="2d045-929">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="2d045-930">Dans `ConfigureAppConfiguration` :</span><span class="sxs-lookup"><span data-stu-id="2d045-930">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="2d045-931">La paire clé-valeur indiquée dans le tableau est chargée dans la configuration.</span><span class="sxs-lookup"><span data-stu-id="2d045-931">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="2d045-932">Clé</span><span class="sxs-lookup"><span data-stu-id="2d045-932">Key</span></span>             | <span data-ttu-id="2d045-933">Valeur</span><span class="sxs-lookup"><span data-stu-id="2d045-933">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="2d045-934">array:entries:3</span><span class="sxs-lookup"><span data-stu-id="2d045-934">array:entries:3</span></span> | <span data-ttu-id="2d045-935">valeur3</span><span class="sxs-lookup"><span data-stu-id="2d045-935">value3</span></span> |

<span data-ttu-id="2d045-936">Si l’instance de classe `ArrayExample` est liée une fois que le Fournisseur de configuration JSON inclut l’entrée pour l’index &num;3, le tableau `ArrayExample.Entries` inclut la valeur.</span><span class="sxs-lookup"><span data-stu-id="2d045-936">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="2d045-937">Index `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="2d045-937">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="2d045-938">`ArrayExample.Entries` Valeur</span><span class="sxs-lookup"><span data-stu-id="2d045-938">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="2d045-939">0</span><span class="sxs-lookup"><span data-stu-id="2d045-939">0</span></span>                            | <span data-ttu-id="2d045-940">value0</span><span class="sxs-lookup"><span data-stu-id="2d045-940">value0</span></span>                       |
| <span data-ttu-id="2d045-941">1</span><span class="sxs-lookup"><span data-stu-id="2d045-941">1</span></span>                            | <span data-ttu-id="2d045-942">valeur1</span><span class="sxs-lookup"><span data-stu-id="2d045-942">value1</span></span>                       |
| <span data-ttu-id="2d045-943">2</span><span class="sxs-lookup"><span data-stu-id="2d045-943">2</span></span>                            | <span data-ttu-id="2d045-944">valeur2</span><span class="sxs-lookup"><span data-stu-id="2d045-944">value2</span></span>                       |
| <span data-ttu-id="2d045-945">3</span><span class="sxs-lookup"><span data-stu-id="2d045-945">3</span></span>                            | <span data-ttu-id="2d045-946">valeur3</span><span class="sxs-lookup"><span data-stu-id="2d045-946">value3</span></span>                       |
| <span data-ttu-id="2d045-947">4</span><span class="sxs-lookup"><span data-stu-id="2d045-947">4</span></span>                            | <span data-ttu-id="2d045-948">value4</span><span class="sxs-lookup"><span data-stu-id="2d045-948">value4</span></span>                       |
| <span data-ttu-id="2d045-949">5</span><span class="sxs-lookup"><span data-stu-id="2d045-949">5</span></span>                            | <span data-ttu-id="2d045-950">value5</span><span class="sxs-lookup"><span data-stu-id="2d045-950">value5</span></span>                       |

<span data-ttu-id="2d045-951">**Traitement de tableau JSON**</span><span class="sxs-lookup"><span data-stu-id="2d045-951">**JSON array processing**</span></span>

<span data-ttu-id="2d045-952">Si un fichier JSON contient un tableau, les clés de configuration sont créés pour les éléments du tableau avec un index de section basé sur zéro.</span><span class="sxs-lookup"><span data-stu-id="2d045-952">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="2d045-953">Dans le fichier de configuration suivant, `subsection` est un tableau :</span><span class="sxs-lookup"><span data-stu-id="2d045-953">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="2d045-954">Le Fournisseur de configuration JSON lit les données de configuration dans les paires clé-valeur suivantes :</span><span class="sxs-lookup"><span data-stu-id="2d045-954">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="2d045-955">Clé</span><span class="sxs-lookup"><span data-stu-id="2d045-955">Key</span></span>                     | <span data-ttu-id="2d045-956">Valeur</span><span class="sxs-lookup"><span data-stu-id="2d045-956">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="2d045-957">json_array:key</span><span class="sxs-lookup"><span data-stu-id="2d045-957">json_array:key</span></span>          | <span data-ttu-id="2d045-958">valueA</span><span class="sxs-lookup"><span data-stu-id="2d045-958">valueA</span></span> |
| <span data-ttu-id="2d045-959">json_array:subsection:0</span><span class="sxs-lookup"><span data-stu-id="2d045-959">json_array:subsection:0</span></span> | <span data-ttu-id="2d045-960">valueB</span><span class="sxs-lookup"><span data-stu-id="2d045-960">valueB</span></span> |
| <span data-ttu-id="2d045-961">json_array:subsection:1</span><span class="sxs-lookup"><span data-stu-id="2d045-961">json_array:subsection:1</span></span> | <span data-ttu-id="2d045-962">valueC</span><span class="sxs-lookup"><span data-stu-id="2d045-962">valueC</span></span> |
| <span data-ttu-id="2d045-963">json_array:subsection:2</span><span class="sxs-lookup"><span data-stu-id="2d045-963">json_array:subsection:2</span></span> | <span data-ttu-id="2d045-964">valueD</span><span class="sxs-lookup"><span data-stu-id="2d045-964">valueD</span></span> |

<span data-ttu-id="2d045-965">Dans l’exemple d’application, la classe POCO suivante est disponible pour lier les paires clé-valeur de configuration :</span><span class="sxs-lookup"><span data-stu-id="2d045-965">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="2d045-966">Après la liaison, `JsonArrayExample.Key` contient la valeur `valueA`.</span><span class="sxs-lookup"><span data-stu-id="2d045-966">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="2d045-967">Les valeurs de la sous-section sont stockées dans la propriété de tableau POCO, `Subsection`.</span><span class="sxs-lookup"><span data-stu-id="2d045-967">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="2d045-968">Index `JsonArrayExample.Subsection`</span><span class="sxs-lookup"><span data-stu-id="2d045-968">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="2d045-969">`JsonArrayExample.Subsection` Valeur</span><span class="sxs-lookup"><span data-stu-id="2d045-969">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="2d045-970">0</span><span class="sxs-lookup"><span data-stu-id="2d045-970">0</span></span>                                   | <span data-ttu-id="2d045-971">valueB</span><span class="sxs-lookup"><span data-stu-id="2d045-971">valueB</span></span>                              |
| <span data-ttu-id="2d045-972">1</span><span class="sxs-lookup"><span data-stu-id="2d045-972">1</span></span>                                   | <span data-ttu-id="2d045-973">valueC</span><span class="sxs-lookup"><span data-stu-id="2d045-973">valueC</span></span>                              |
| <span data-ttu-id="2d045-974">2</span><span class="sxs-lookup"><span data-stu-id="2d045-974">2</span></span>                                   | <span data-ttu-id="2d045-975">valueD</span><span class="sxs-lookup"><span data-stu-id="2d045-975">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="2d045-976">Fournisseur de configuration personnalisé</span><span class="sxs-lookup"><span data-stu-id="2d045-976">Custom configuration provider</span></span>

<span data-ttu-id="2d045-977">L’exemple d’application montre comment créer un fournisseur de configuration de base qui lit les paires clé-valeur de configuration à partir d’une base de données à l’aide [d’Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="2d045-977">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="2d045-978">Le fournisseur présente les caractéristiques suivantes :</span><span class="sxs-lookup"><span data-stu-id="2d045-978">The provider has the following characteristics:</span></span>

* <span data-ttu-id="2d045-979">La base de données en mémoire EF est utilisée à des fins de démonstration.</span><span class="sxs-lookup"><span data-stu-id="2d045-979">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="2d045-980">Pour utiliser une base de données qui nécessite une chaîne de connexion, implémentez un autre `ConfigurationBuilder` pour fournir la chaîne de connexion à partir d’un autre fournisseur de configuration.</span><span class="sxs-lookup"><span data-stu-id="2d045-980">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="2d045-981">Le fournisseur lit une table de base de données dans la configuration au démarrage.</span><span class="sxs-lookup"><span data-stu-id="2d045-981">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="2d045-982">Le fournisseur n’interroge pas la base de données par clé.</span><span class="sxs-lookup"><span data-stu-id="2d045-982">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="2d045-983">Le rechargement en cas de changement n’est pas implémenté, par conséquent, la mise à jour la base de données après le démarrage de l’application n’a aucun effet sur la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="2d045-983">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="2d045-984">Définissez une entité `EFConfigurationValue` pour le stockage des valeurs de configuration dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="2d045-984">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="2d045-985">*Models/EFConfigurationValue.cs* :</span><span class="sxs-lookup"><span data-stu-id="2d045-985">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="2d045-986">Ajoutez un `EFConfigurationContext` pour stocker les valeurs configurées et y accéder.</span><span class="sxs-lookup"><span data-stu-id="2d045-986">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="2d045-987">*EFConfigurationProvider/EFConfigurationContext.cs* :</span><span class="sxs-lookup"><span data-stu-id="2d045-987">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="2d045-988">Créez une classe qui implémente <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="2d045-988">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="2d045-989">*EFConfigurationProvider/EFConfigurationSource.cs* :</span><span class="sxs-lookup"><span data-stu-id="2d045-989">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="2d045-990">Créez le fournisseur de configuration personnalisé en héritant de <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="2d045-990">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="2d045-991">Le fournisseur de configuration initialise la base de données quand elle est vide.</span><span class="sxs-lookup"><span data-stu-id="2d045-991">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="2d045-992">*EFConfigurationProvider/EFConfigurationProvider.cs* :</span><span class="sxs-lookup"><span data-stu-id="2d045-992">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="2d045-993">Une méthode d’extension `AddEFConfiguration` permet d’ajouter la source de configuration à un `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="2d045-993">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="2d045-994">*Extensions/EntityFrameworkExtensions.cs* :</span><span class="sxs-lookup"><span data-stu-id="2d045-994">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="2d045-995">Le code suivant montre comment utiliser le `EFConfigurationProvider` personnalisé dans *Program.cs* :</span><span class="sxs-lookup"><span data-stu-id="2d045-995">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="2d045-996">Accéder à la configuration lors du démarrage</span><span class="sxs-lookup"><span data-stu-id="2d045-996">Access configuration during startup</span></span>

<span data-ttu-id="2d045-997">Injectez `IConfiguration` dans le constructeur `Startup` pour accéder aux valeurs de configuration dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="2d045-997">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="2d045-998">Pour accéder à la configuration dans `Startup.Configure`, injectez `IConfiguration` directement dans la méthode ou utilisez l’instance à partir du constructeur :</span><span class="sxs-lookup"><span data-stu-id="2d045-998">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="2d045-999">Pour obtenir un exemple d’accès à la configuration à l’aide des méthodes pratiques de démarrage, consultez [Démarrage de l’application : méthodes pratiques](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="2d045-999">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="2d045-1000">Accéder à la configuration dans une page Razor Pages ou une vue MVC</span><span class="sxs-lookup"><span data-stu-id="2d045-1000">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="2d045-1001">Pour accéder aux paramètres de configuration dans une page Pages Razor ou une vue MVC, ajoutez une [directive using](xref:mvc/views/razor#using) ([Informations de référence sur C# : directive using](/dotnet/csharp/language-reference/keywords/using-directive)) pour [l’espace de noms Microsoft.Extensions.Configuration](xref:Microsoft.Extensions.Configuration) et injectez <xref:Microsoft.Extensions.Configuration.IConfiguration> dans la page ou la vue.</span><span class="sxs-lookup"><span data-stu-id="2d045-1001">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="2d045-1002">Dans une page Pages Razor :</span><span class="sxs-lookup"><span data-stu-id="2d045-1002">In a Razor Pages page:</span></span>

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

<span data-ttu-id="2d045-1003">Dans une vue MVC :</span><span class="sxs-lookup"><span data-stu-id="2d045-1003">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="2d045-1004">Ajouter la configuration à partir d’un assembly externe</span><span class="sxs-lookup"><span data-stu-id="2d045-1004">Add configuration from an external assembly</span></span>

<span data-ttu-id="2d045-1005">Une implémentation de <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> permet d’ajouter des améliorations à une application au démarrage à partir d’un assembly externe, en dehors de la classe `Startup` de l’application.</span><span class="sxs-lookup"><span data-stu-id="2d045-1005">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="2d045-1006">Pour plus d’informations, consultez <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="2d045-1006">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2d045-1007">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="2d045-1007">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end
