---
<span data-ttu-id="c6e74-101">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="c6e74-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6e74-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6e74-102">'Blazor'</span></span>
- <span data-ttu-id="c6e74-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6e74-103">'Identity'</span></span>
- <span data-ttu-id="c6e74-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6e74-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6e74-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6e74-105">'Razor'</span></span>
- <span data-ttu-id="c6e74-106">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="c6e74-106">'SignalR' uid:</span></span> 

---
# <a name="visual-studio-publish-profiles-pubxml-for-aspnet-core-app-deployment"></a><span data-ttu-id="c6e74-107">Profils de publication Visual Studio (. pubxml) pour le déploiement d’applications ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c6e74-107">Visual Studio publish profiles (.pubxml) for ASP.NET Core app deployment</span></span>

<span data-ttu-id="c6e74-108">De [Sayed Ibrahim Hashimi](https://github.com/sayedihashimi) et [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="c6e74-108">By [Sayed Ibrahim Hashimi](https://github.com/sayedihashimi) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="c6e74-109">Ce document traite de l’utilisation de Visual Studio 2019 ou supérieur pour créer et utiliser des profils de publication.</span><span class="sxs-lookup"><span data-stu-id="c6e74-109">This document focuses on using Visual Studio 2019 or later to create and use publish profiles.</span></span> <span data-ttu-id="c6e74-110">Les profils de publication créés avec Visual Studio peuvent être utilisés avec MSBuild et Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="c6e74-110">The publish profiles created with Visual Studio can be used with MSBuild and Visual Studio.</span></span> <span data-ttu-id="c6e74-111">Pour obtenir des instructions sur la publication sur Azure, consultez <xref:tutorials/publish-to-azure-webapp-using-vs>.</span><span class="sxs-lookup"><span data-stu-id="c6e74-111">For instructions on publishing to Azure, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

<span data-ttu-id="c6e74-112">La `dotnet new mvc` commande génère un fichier projet contenant l' [ \<Project> élément](/visualstudio/msbuild/project-element-msbuild)de niveau racine suivant :</span><span class="sxs-lookup"><span data-stu-id="c6e74-112">The `dotnet new mvc` command produces a project file containing the following root-level [\<Project> element](/visualstudio/msbuild/project-element-msbuild):</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
</Project>
```

<span data-ttu-id="c6e74-113">L’attribut `Sdk` de l’élément `<Project>` précédent importe les [propriétés](/visualstudio/msbuild/msbuild-properties) et [cibles](/visualstudio/msbuild/msbuild-targets) de MSBuild à partir de *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.props* et *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.targets*, respectivement.</span><span class="sxs-lookup"><span data-stu-id="c6e74-113">The preceding `<Project>` element's `Sdk` attribute imports the MSBuild [properties](/visualstudio/msbuild/msbuild-properties) and [targets](/visualstudio/msbuild/msbuild-targets) from *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.props* and *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.targets*, respectively.</span></span> <span data-ttu-id="c6e74-114">L’emplacement par défaut de `$(MSBuildSDKsPath)` (avec Visual Studio 2019 Enterprise) est le dossier *%programfiles(x86)%\Microsoft Visual Studio\2019\Enterprise\MSBuild\Sdks*.</span><span class="sxs-lookup"><span data-stu-id="c6e74-114">The default location for `$(MSBuildSDKsPath)` (with Visual Studio 2019 Enterprise) is the *%programfiles(x86)%\Microsoft Visual Studio\2019\Enterprise\MSBuild\Sdks* folder.</span></span>

<span data-ttu-id="c6e74-115">`Microsoft.NET.Sdk.Web`([Kit de développement logiciel (SDK) Web](xref:razor-pages/web-sdk)) dépend d’autres kits de développement logiciel (SDK), y compris `Microsoft.NET.Sdk` ([Kit SDK .net Core](/dotnet/core/project-sdk/msbuild-props)) et `Microsoft.NET.Sdk.Razor` ([ Razor SDK](xref:razor-pages/sdk)).</span><span class="sxs-lookup"><span data-stu-id="c6e74-115">`Microsoft.NET.Sdk.Web` ([Web SDK](xref:razor-pages/web-sdk)) depends on other SDKs, including `Microsoft.NET.Sdk` ([.NET Core SDK](/dotnet/core/project-sdk/msbuild-props)) and `Microsoft.NET.Sdk.Razor` ([Razor SDK](xref:razor-pages/sdk)).</span></span> <span data-ttu-id="c6e74-116">Les propriétés et cibles MSBuild associées à chaque kit de développement logiciel sont importées.</span><span class="sxs-lookup"><span data-stu-id="c6e74-116">The MSBuild properties and targets associated with each dependent SDK are imported.</span></span> <span data-ttu-id="c6e74-117">Les cibles de publication importent le bon ensemble de cibles en fonction de la méthode de publication utilisée.</span><span class="sxs-lookup"><span data-stu-id="c6e74-117">Publish targets import the appropriate set of targets based on the publish method used.</span></span>

<span data-ttu-id="c6e74-118">Quand MSBuild ou Visual Studio charge un projet, les actions principales suivantes se produisent :</span><span class="sxs-lookup"><span data-stu-id="c6e74-118">When MSBuild or Visual Studio loads a project, the following high-level actions occur:</span></span>

* <span data-ttu-id="c6e74-119">Génération du projet</span><span class="sxs-lookup"><span data-stu-id="c6e74-119">Build project</span></span>
* <span data-ttu-id="c6e74-120">Calcul des fichiers à publier</span><span class="sxs-lookup"><span data-stu-id="c6e74-120">Compute files to publish</span></span>
* <span data-ttu-id="c6e74-121">Publication des fichiers sur la destination</span><span class="sxs-lookup"><span data-stu-id="c6e74-121">Publish files to destination</span></span>

## <a name="compute-project-items"></a><span data-ttu-id="c6e74-122">Calcul des éléments du projet</span><span class="sxs-lookup"><span data-stu-id="c6e74-122">Compute project items</span></span>

<span data-ttu-id="c6e74-123">Quand le projet est chargé, les [éléments du projet MSBuild](/visualstudio/msbuild/common-msbuild-project-items) (fichiers) sont calculés.</span><span class="sxs-lookup"><span data-stu-id="c6e74-123">When the project is loaded, the [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) (files) are computed.</span></span> <span data-ttu-id="c6e74-124">Le type d’élément détermine la façon dont le fichier est traité.</span><span class="sxs-lookup"><span data-stu-id="c6e74-124">The item type determines how the file is processed.</span></span> <span data-ttu-id="c6e74-125">Par défaut, les fichiers *.cs* sont inclus dans la liste d’éléments `Compile`.</span><span class="sxs-lookup"><span data-stu-id="c6e74-125">By default, *.cs* files are included in the `Compile` item list.</span></span> <span data-ttu-id="c6e74-126">Les fichiers dans la liste d’éléments `Compile` sont compilés.</span><span class="sxs-lookup"><span data-stu-id="c6e74-126">Files in the `Compile` item list are compiled.</span></span>

<span data-ttu-id="c6e74-127">La liste d’éléments `Content` contient des fichiers qui sont publiés en plus des sorties de génération.</span><span class="sxs-lookup"><span data-stu-id="c6e74-127">The `Content` item list contains files that are published in addition to the build outputs.</span></span> <span data-ttu-id="c6e74-128">Par défaut, les fichiers qui correspondent aux modèles `wwwroot\**`, `**\*.config` et `**\*.json` sont inclus dans l’élément `Content`.</span><span class="sxs-lookup"><span data-stu-id="c6e74-128">By default, files matching the patterns `wwwroot\**`, `**\*.config`, and `**\*.json` are included in the `Content` item list.</span></span> <span data-ttu-id="c6e74-129">Par exemple, le  [modèle d’utilisation des caractères génériques](https://gruntjs.com/configuring-tasks#globbing-patterns)`wwwroot\**` correspond à tous les fichiers dans le dossier *wwwroot* et ses sous-dossiers.</span><span class="sxs-lookup"><span data-stu-id="c6e74-129">For example, the `wwwroot\**` [globbing pattern](https://gruntjs.com/configuring-tasks#globbing-patterns) matches all files in the *wwwroot* folder and its subfolders.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c6e74-130">Le [Kit de développement logiciel (SDK) Web](xref:razor-pages/web-sdk) importe le [ Razor SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="c6e74-130">The [Web SDK](xref:razor-pages/web-sdk) imports the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="c6e74-131">Par conséquent, les fichiers correspondant aux modèles `**\*.cshtml` et `**\*.razor` sont également inclus dans la liste d’éléments `Content`.</span><span class="sxs-lookup"><span data-stu-id="c6e74-131">As a result, files matching the patterns `**\*.cshtml` and `**\*.razor` are also included in the `Content` item list.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

<span data-ttu-id="c6e74-132">Le [Kit de développement logiciel (SDK) Web](xref:razor-pages/web-sdk) importe le [ Razor SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="c6e74-132">The [Web SDK](xref:razor-pages/web-sdk) imports the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="c6e74-133">Par conséquent, les fichiers correspondant au modèle `**\*.cshtml` sont également inclus dans la liste d’éléments `Content`.</span><span class="sxs-lookup"><span data-stu-id="c6e74-133">As a result, files matching the `**\*.cshtml` pattern are also included in the `Content` item list.</span></span>

::: moniker-end

<span data-ttu-id="c6e74-134">Pour ajouter explicitement un fichier à la liste de publication, ajoutez-le directement au fichier *.csproj* comme indiqué dans la section [Inclure des fichiers](#include-files).</span><span class="sxs-lookup"><span data-stu-id="c6e74-134">To explicitly add a file to the publish list, add the file directly in the *.csproj* file as shown in the [Include Files](#include-files) section.</span></span>

<span data-ttu-id="c6e74-135">Quand vous sélectionnez le bouton **Publier** dans Visual Studio ou quand vous publiez à partir de la ligne de commande :</span><span class="sxs-lookup"><span data-stu-id="c6e74-135">When selecting the **Publish** button in Visual Studio or when publishing from the command line:</span></span>

* <span data-ttu-id="c6e74-136">Les éléments/propriétés sont calculés (il s’agit des fichiers nécessaires à la génération).</span><span class="sxs-lookup"><span data-stu-id="c6e74-136">The properties/items are computed (the files that are needed to build).</span></span>
* <span data-ttu-id="c6e74-137">**Visual Studio uniquement**: les packages NuGet sont restaurés.</span><span class="sxs-lookup"><span data-stu-id="c6e74-137">**Visual Studio only**: NuGet packages are restored.</span></span> <span data-ttu-id="c6e74-138">(La restauration doit être explicite par l’utilisateur sur l’interface CLI.)</span><span class="sxs-lookup"><span data-stu-id="c6e74-138">(Restore needs to be explicit by the user on the CLI.)</span></span>
* <span data-ttu-id="c6e74-139">Le projet est généré.</span><span class="sxs-lookup"><span data-stu-id="c6e74-139">The project builds.</span></span>
* <span data-ttu-id="c6e74-140">Les éléments de publication sont calculés (il s’agit des fichiers nécessaires à la publication).</span><span class="sxs-lookup"><span data-stu-id="c6e74-140">The publish items are computed (the files that are needed to publish).</span></span>
* <span data-ttu-id="c6e74-141">Le projet est publié (les fichiers calculés sont copiés sur la destination de publication).</span><span class="sxs-lookup"><span data-stu-id="c6e74-141">The project is published (the computed files are copied to the publish destination).</span></span>

<span data-ttu-id="c6e74-142">Quand un projet ASP.NET Core référence `Microsoft.NET.Sdk.Web` dans le fichier projet, un fichier *app_offline.htm* est placé à la racine du répertoire des applications web.</span><span class="sxs-lookup"><span data-stu-id="c6e74-142">When an ASP.NET Core project references `Microsoft.NET.Sdk.Web` in the project file, an *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="c6e74-143">Quand le fichier est présent, le module ASP.NET Core ferme l’application normalement et sert le fichier *app_offline.htm* pendant le déploiement.</span><span class="sxs-lookup"><span data-stu-id="c6e74-143">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="c6e74-144">Pour plus d’informations, consultez les [Informations de référence sur la configuration du module ASP.NET Core](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span><span class="sxs-lookup"><span data-stu-id="c6e74-144">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>

## <a name="basic-command-line-publishing"></a><span data-ttu-id="c6e74-145">Publication de base à partir d’une ligne de commande</span><span class="sxs-lookup"><span data-stu-id="c6e74-145">Basic command-line publishing</span></span>

<span data-ttu-id="c6e74-146">La publication à partir d’une ligne de commande fonctionne sur toutes les plateformes .NET Core prises en charge et ne nécessite pas Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="c6e74-146">Command-line publishing works on all .NET Core-supported platforms and doesn't require Visual Studio.</span></span> <span data-ttu-id="c6e74-147">Dans les exemples suivants, la commande [dotnet publish](/dotnet/core/tools/dotnet-publish) de l’interface CLI .NET Core est exécutée à partir du répertoire de projet (qui contient le fichier *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="c6e74-147">In the following examples, the .NET Core CLI's [dotnet publish](/dotnet/core/tools/dotnet-publish) command is run from the project directory (which contains the *.csproj* file).</span></span> <span data-ttu-id="c6e74-148">Si le dossier du projet n’est pas le répertoire de travail actuel, passez explicitement le chemin du fichier projet.</span><span class="sxs-lookup"><span data-stu-id="c6e74-148">If the project folder isn't the current working directory, explicitly pass in the project file path.</span></span> <span data-ttu-id="c6e74-149">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="c6e74-149">For example:</span></span>

```dotnetcli
dotnet publish C:\Webs\Web1
```

<span data-ttu-id="c6e74-150">Exécutez les commandes suivantes pour créer et publier une application web :</span><span class="sxs-lookup"><span data-stu-id="c6e74-150">Run the following commands to create and publish a web app:</span></span>

```dotnetcli
dotnet new mvc
dotnet publish
```

<span data-ttu-id="c6e74-151">La commande `dotnet publish` produit une variante de la sortie suivante :</span><span class="sxs-lookup"><span data-stu-id="c6e74-151">The `dotnet publish` command produces a variation of the following output:</span></span>

```console
C:\Webs\Web1>dotnet publish
Microsoft (R) Build Engine version {VERSION} for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.

  Restore completed in 36.81 ms for C:\Webs\Web1\Web1.csproj.
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\Web1.dll
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\Web1.Views.dll
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\publish\
```

<span data-ttu-id="c6e74-152">Le format par défaut du dossier de publication est *bin\Debug\\{MONIKER DU FRAMEWORK CIBLE}\publish\\*.</span><span class="sxs-lookup"><span data-stu-id="c6e74-152">The default publish folder format is *bin\Debug\\{TARGET FRAMEWORK MONIKER}\publish\\*.</span></span> <span data-ttu-id="c6e74-153">Par exemple, *bin\Debug\netcoreapp2.2\publish\\*.</span><span class="sxs-lookup"><span data-stu-id="c6e74-153">For example, *bin\Debug\netcoreapp2.2\publish\\*.</span></span>

<span data-ttu-id="c6e74-154">La commande suivante spécifie une build `Release` et le répertoire de publication :</span><span class="sxs-lookup"><span data-stu-id="c6e74-154">The following command specifies a `Release` build and the publishing directory:</span></span>

```dotnetcli
dotnet publish -c Release -o C:\MyWebs\test
```

<span data-ttu-id="c6e74-155">La commande `dotnet publish` appelle MSBuild, qui appelle la cible de `Publish`.</span><span class="sxs-lookup"><span data-stu-id="c6e74-155">The `dotnet publish` command calls MSBuild, which invokes the `Publish` target.</span></span> <span data-ttu-id="c6e74-156">Les paramètres passés à `dotnet publish` sont passés à MSBuild.</span><span class="sxs-lookup"><span data-stu-id="c6e74-156">Any parameters passed to `dotnet publish` are passed to MSBuild.</span></span> <span data-ttu-id="c6e74-157">Les paramètres `-c` et `-o` correspondent aux propriétés `Configuration` et `OutputPath` de MSBuild, respectivement.</span><span class="sxs-lookup"><span data-stu-id="c6e74-157">The `-c` and `-o` parameters map to MSBuild's `Configuration` and `OutputPath` properties, respectively.</span></span>

<span data-ttu-id="c6e74-158">Les propriétés MSBuild peuvent être passées à l’aide de l’un des formats suivants :</span><span class="sxs-lookup"><span data-stu-id="c6e74-158">MSBuild properties can be passed using either of the following formats:</span></span>

* `p:<NAME>=<VALUE>`
* `/p:<NAME>=<VALUE>`

<span data-ttu-id="c6e74-159">Par exemple, la commande suivante publie une build `Release` sur un partage réseau.</span><span class="sxs-lookup"><span data-stu-id="c6e74-159">For example, the following command publishes a `Release` build to a network share.</span></span> <span data-ttu-id="c6e74-160">Le partage réseau est spécifié avec des barres obliques (*//r8/*) et fonctionne sur toutes les plateformes .NET Core prises en charge.</span><span class="sxs-lookup"><span data-stu-id="c6e74-160">The network share is specified with forward slashes (*//r8/*) and works on all .NET Core supported platforms.</span></span>

```dotnetcli
dotnet publish -c Release /p:PublishDir=//r8/release/AdminWeb
```

<span data-ttu-id="c6e74-161">Vérifiez que l’application publiée pour le déploiement n’est pas en cours d’exécution.</span><span class="sxs-lookup"><span data-stu-id="c6e74-161">Confirm that the published app for deployment isn't running.</span></span> <span data-ttu-id="c6e74-162">Les fichiers dans le dossier *publish* sont verrouillés quand l’application est en cours d’exécution.</span><span class="sxs-lookup"><span data-stu-id="c6e74-162">Files in the *publish* folder are locked when the app is running.</span></span> <span data-ttu-id="c6e74-163">Le déploiement ne peut pas se produire car les fichiers verrouillés ne peuvent pas être copiés.</span><span class="sxs-lookup"><span data-stu-id="c6e74-163">Deployment can't occur because locked files can't be copied.</span></span>

## <a name="publish-profiles"></a><span data-ttu-id="c6e74-164">Profils de publication</span><span class="sxs-lookup"><span data-stu-id="c6e74-164">Publish profiles</span></span>

<span data-ttu-id="c6e74-165">Cette section utilise Visual Studio 2019 ou supérieur pour créer un profil de publication.</span><span class="sxs-lookup"><span data-stu-id="c6e74-165">This section uses Visual Studio 2019 or later to create a publishing profile.</span></span> <span data-ttu-id="c6e74-166">Une fois le profil créé, la publication à partir de Visual Studio ou de la ligne de commande est disponible.</span><span class="sxs-lookup"><span data-stu-id="c6e74-166">Once the profile is created, publishing from Visual Studio or the command line is available.</span></span> <span data-ttu-id="c6e74-167">Les profils de publication peuvent simplifier le processus de publication, et n’importe quel nombre de profils peut exister.</span><span class="sxs-lookup"><span data-stu-id="c6e74-167">Publish profiles can simplify the publishing process, and any number of profiles can exist.</span></span>

<span data-ttu-id="c6e74-168">Créez un profil de publication dans Visual Studio en choisissant l’une des méthodes suivantes :</span><span class="sxs-lookup"><span data-stu-id="c6e74-168">Create a publish profile in Visual Studio by choosing one of the following paths:</span></span>

* <span data-ttu-id="c6e74-169">Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur le projet, puis sélectionnez **Publier**.</span><span class="sxs-lookup"><span data-stu-id="c6e74-169">Right-click the project in **Solution Explorer** and select **Publish**.</span></span>
* <span data-ttu-id="c6e74-170">Sélectionner **Publier {NOM DU PROJET}** dans le menu **Générer**.</span><span class="sxs-lookup"><span data-stu-id="c6e74-170">Select **Publish {PROJECT NAME}** from the **Build** menu.</span></span>

<span data-ttu-id="c6e74-171">L’onglet **Publier** de la page de fonctionnalités de l’application s’affiche.</span><span class="sxs-lookup"><span data-stu-id="c6e74-171">The **Publish** tab of the app capabilities page is displayed.</span></span> <span data-ttu-id="c6e74-172">Si le projet n’a pas de profil de publication, la page **Choisir une cible de publication** s’affiche.</span><span class="sxs-lookup"><span data-stu-id="c6e74-172">If the project lacks a publish profile, the **Pick a publish target** page is displayed.</span></span> <span data-ttu-id="c6e74-173">Vous êtes invité à sélectionner une des cibles de publication suivantes :</span><span class="sxs-lookup"><span data-stu-id="c6e74-173">You're asked to select one of the following publish targets:</span></span>

* <span data-ttu-id="c6e74-174">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="c6e74-174">Azure App Service</span></span>
* <span data-ttu-id="c6e74-175">Azure App Service sur Linux</span><span class="sxs-lookup"><span data-stu-id="c6e74-175">Azure App Service on Linux</span></span>
* <span data-ttu-id="c6e74-176">Machines virtuelles Azure</span><span class="sxs-lookup"><span data-stu-id="c6e74-176">Azure Virtual Machines</span></span>
* <span data-ttu-id="c6e74-177">Dossier</span><span class="sxs-lookup"><span data-stu-id="c6e74-177">Folder</span></span>
* <span data-ttu-id="c6e74-178">IIS, FTP, Web Deploy (pour n’importe quel serveur web)</span><span class="sxs-lookup"><span data-stu-id="c6e74-178">IIS, FTP, Web Deploy (for any web server)</span></span>
* <span data-ttu-id="c6e74-179">Profil d’importation</span><span class="sxs-lookup"><span data-stu-id="c6e74-179">Import Profile</span></span>

<span data-ttu-id="c6e74-180">Pour déterminer la cible de publication la plus appropriée, consultez [Quelles sont les meilleures options de publication pour moi](/visualstudio/ide/not-in-toc/web-publish-options).</span><span class="sxs-lookup"><span data-stu-id="c6e74-180">To determine the most appropriate publish target, see [What publishing options are right for me](/visualstudio/ide/not-in-toc/web-publish-options).</span></span>

<span data-ttu-id="c6e74-181">Quand la cible de publication **Dossier** est sélectionné, spécifiez un chemin de dossier pour stocker les ressources publiées.</span><span class="sxs-lookup"><span data-stu-id="c6e74-181">When the **Folder** publish target is selected, specify a folder path to store the published assets.</span></span> <span data-ttu-id="c6e74-182">Le chemin du dossier par défaut est *bin\\{CONFIGURATION DU PROJET}\\{MONIKER DU FRAMEWORK CIBLE}\publish\\*.</span><span class="sxs-lookup"><span data-stu-id="c6e74-182">The default folder path is *bin\\{PROJECT CONFIGURATION}\\{TARGET FRAMEWORK MONIKER}\publish\\*.</span></span> <span data-ttu-id="c6e74-183">Par exemple, *bin\Release\netcoreapp2.2\publish\\*.</span><span class="sxs-lookup"><span data-stu-id="c6e74-183">For example, *bin\Release\netcoreapp2.2\publish\\*.</span></span> <span data-ttu-id="c6e74-184">Sélectionnez le bouton **Créer un profil** pour terminer.</span><span class="sxs-lookup"><span data-stu-id="c6e74-184">Select the **Create Profile** button to finish.</span></span>

<span data-ttu-id="c6e74-185">Une fois créé le profil de publication, le contenu de l’onglet **Publier** change.</span><span class="sxs-lookup"><span data-stu-id="c6e74-185">Once a publish profile is created, the **Publish** tab's content changes.</span></span> <span data-ttu-id="c6e74-186">Le profil créé apparaît dans une liste déroulante.</span><span class="sxs-lookup"><span data-stu-id="c6e74-186">The newly created profile appears in a drop-down list.</span></span> <span data-ttu-id="c6e74-187">Dans la liste déroulante, sélectionnez **Créer un profil** pour créer un autre profil.</span><span class="sxs-lookup"><span data-stu-id="c6e74-187">Below the drop-down list, select **Create new profile** to create another new profile.</span></span>

<span data-ttu-id="c6e74-188">L’outil de publication de Visual Studio produit un fichier MSBuild *Properties/PublishProfiles/{NOM DU PROFIL}.pubxml* décrivant le profil de publication.</span><span class="sxs-lookup"><span data-stu-id="c6e74-188">Visual Studio's publish tool produces a *Properties/PublishProfiles/{PROFILE NAME}.pubxml* MSBuild file describing the publish profile.</span></span> <span data-ttu-id="c6e74-189">Le fichier *.pubxml* :</span><span class="sxs-lookup"><span data-stu-id="c6e74-189">The *.pubxml* file:</span></span>

* <span data-ttu-id="c6e74-190">Contient les paramètres de configuration de publication et est utilisé par le processus de publication.</span><span class="sxs-lookup"><span data-stu-id="c6e74-190">Contains publish configuration settings and is consumed by the publishing process.</span></span>
* <span data-ttu-id="c6e74-191">Peut être modifié pour personnaliser le processus de génération et de publication.</span><span class="sxs-lookup"><span data-stu-id="c6e74-191">Can be modified to customize the build and publish process.</span></span>

<span data-ttu-id="c6e74-192">Dans le cas d’une publication sur une cible Azure, le fichier *.pubxml* contient l’identificateur de votre abonnement Azure.</span><span class="sxs-lookup"><span data-stu-id="c6e74-192">When publishing to an Azure target, the *.pubxml* file contains your Azure subscription identifier.</span></span> <span data-ttu-id="c6e74-193">Avec ce type de cible, nous vous déconseillons d’ajouter ce fichier au contrôle de code source.</span><span class="sxs-lookup"><span data-stu-id="c6e74-193">With that target type, adding this file to source control is discouraged.</span></span> <span data-ttu-id="c6e74-194">Dans le cas d’une publication sur une cible non-Azure, nous vous recommandons d’archiver le fichier *.pubxml*.</span><span class="sxs-lookup"><span data-stu-id="c6e74-194">When publishing to a non-Azure target, it's safe to check in the *.pubxml* file.</span></span>

<span data-ttu-id="c6e74-195">Les informations sensibles (telles que le mot de passe de publication) sont chiffrées par utilisateur/machine.</span><span class="sxs-lookup"><span data-stu-id="c6e74-195">Sensitive information (like the publish password) is encrypted on a per user/machine level.</span></span> <span data-ttu-id="c6e74-196">Elles sont stockées dans le fichier *Properties/PublishProfiles/{NOM DU PROFIL}.pubxml.user*.</span><span class="sxs-lookup"><span data-stu-id="c6e74-196">It's stored in the *Properties/PublishProfiles/{PROFILE NAME}.pubxml.user* file.</span></span> <span data-ttu-id="c6e74-197">Ce fichier pouvant stocker des informations sensibles, il ne doit pas être archivé dans le contrôle de code source.</span><span class="sxs-lookup"><span data-stu-id="c6e74-197">Because this file can store sensitive information, it shouldn't be checked into source control.</span></span>

<span data-ttu-id="c6e74-198">Pour obtenir une vue d’ensemble expliquant comment publier une application web ASP.NET Core, consultez <xref:host-and-deploy/index>.</span><span class="sxs-lookup"><span data-stu-id="c6e74-198">For an overview of how to publish an ASP.NET Core web app, see <xref:host-and-deploy/index>.</span></span> <span data-ttu-id="c6e74-199">Les tâches et les cibles MSBuild nécessaires pour publier une ASP.NET Core application Web sont Open source dans le [référentiel ASPNET/WebSDK](https://github.com/aspnet/websdk).</span><span class="sxs-lookup"><span data-stu-id="c6e74-199">The MSBuild tasks and targets necessary to publish an ASP.NET Core web app are open-source in the [aspnet/websdk repository](https://github.com/aspnet/websdk).</span></span>

<span data-ttu-id="c6e74-200">Les commandes suivantes peuvent utiliser les profils de publication de dossier, MSDeploy et [Kudu](https://github.com/projectkudu/kudu/wiki) .</span><span class="sxs-lookup"><span data-stu-id="c6e74-200">The following commands can use folder, MSDeploy, and [Kudu](https://github.com/projectkudu/kudu/wiki) publish profiles.</span></span> <span data-ttu-id="c6e74-201">Comme MSDeploy ne prend pas en charge plusieurs plateformes, les options MSDeploy suivantes sont prises en charge uniquement sur Windows.</span><span class="sxs-lookup"><span data-stu-id="c6e74-201">Because MSDeploy lacks cross-platform support, the following MSDeploy options are supported only on Windows.</span></span>

<span data-ttu-id="c6e74-202">**Dossier (fonctionne sur plusieurs plateformes) :**</span><span class="sxs-lookup"><span data-stu-id="c6e74-202">**Folder (works cross-platform):**</span></span>

<!--

NOTE: Add back the following 'dotnet publish' folder publish example after https://github.com/aspnet/websdk/issues/888 is resolved.

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<FolderProfileName>
```

-->

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<FolderProfileName>
```

<span data-ttu-id="c6e74-203">**MSDeploy :**</span><span class="sxs-lookup"><span data-stu-id="c6e74-203">**MSDeploy:**</span></span>

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployProfileName> /p:Password=<DeploymentPassword>
```

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<MsDeployProfileName> /p:Password=<DeploymentPassword>
```

<span data-ttu-id="c6e74-204">**Package MSDeploy :**</span><span class="sxs-lookup"><span data-stu-id="c6e74-204">**MSDeploy package:**</span></span>

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployPackageProfileName>
```

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<MsDeployPackageProfileName>
```

<span data-ttu-id="c6e74-205">Dans les exemples précédents :</span><span class="sxs-lookup"><span data-stu-id="c6e74-205">In the preceding examples:</span></span>

* <span data-ttu-id="c6e74-206">`dotnet publish`et `dotnet build` prennent en charge les API Kudu pour publier sur Azure à partir de n’importe quelle plateforme.</span><span class="sxs-lookup"><span data-stu-id="c6e74-206">`dotnet publish` and `dotnet build` support Kudu APIs to publish to Azure from any platform.</span></span> <span data-ttu-id="c6e74-207">La publication Visual Studio prend en charge les API Kudu, mais avec la prise en charge par WebSDK pour la publication multiplateforme sur Azure.</span><span class="sxs-lookup"><span data-stu-id="c6e74-207">Visual Studio publish supports the Kudu APIs, but it's supported by WebSDK for cross-platform publish to Azure.</span></span>
* <span data-ttu-id="c6e74-208">Ne pas passer `DeployOnBuild` à la `dotnet publish` commande.</span><span class="sxs-lookup"><span data-stu-id="c6e74-208">Don't pass `DeployOnBuild` to the `dotnet publish` command.</span></span>

<span data-ttu-id="c6e74-209">Pour plus d’informations, consultez [Microsoft.NET.Sdk.Publish](https://github.com/aspnet/websdk#microsoftnetsdkpublish).</span><span class="sxs-lookup"><span data-stu-id="c6e74-209">For more information, see [Microsoft.NET.Sdk.Publish](https://github.com/aspnet/websdk#microsoftnetsdkpublish).</span></span>

<span data-ttu-id="c6e74-210">Ajoutez un profil de publication au dossier *Properties/PublishProfiles* avec le contenu suivant :</span><span class="sxs-lookup"><span data-stu-id="c6e74-210">Add a publish profile to the project's *Properties/PublishProfiles* folder with the following content:</span></span>

```xml
<Project>
  <PropertyGroup>
    <PublishProtocol>Kudu</PublishProtocol>
    <PublishSiteName>nodewebapp</PublishSiteName>
    <UserName>username</UserName>
    <Password>password</Password>
  </PropertyGroup>
</Project>
```

## <a name="folder-publish-example"></a><span data-ttu-id="c6e74-211">Exemple de publication de dossier</span><span class="sxs-lookup"><span data-stu-id="c6e74-211">Folder publish example</span></span>

<span data-ttu-id="c6e74-212">Lors de la publication avec un profil nommé *FolderProfile*, utilisez l’une des commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="c6e74-212">When publishing with a profile named *FolderProfile*, use either of the following commands:</span></span>

<!--

NOTE: Temporarily removed until https://github.com/aspnet/websdk/issues/888 is resolved.

* `dotnet publish /p:Configuration=Release /p:PublishProfile=FolderProfile`

-->

* `dotnet build /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`
* `msbuild /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`

<span data-ttu-id="c6e74-213">La commande [dotnet build](/dotnet/core/tools/dotnet-build) de l’interface CLI .NET Core appelle `msbuild` pour exécuter les processus de génération et de publication.</span><span class="sxs-lookup"><span data-stu-id="c6e74-213">The .NET Core CLI's [dotnet build](/dotnet/core/tools/dotnet-build) command calls `msbuild` to run the build and publish process.</span></span> <span data-ttu-id="c6e74-214">Les commandes `dotnet build` et `msbuild` sont équivalentes quand vous passez un profil de dossier.</span><span class="sxs-lookup"><span data-stu-id="c6e74-214">The `dotnet build` and `msbuild` commands are equivalent when passing in a folder profile.</span></span> <span data-ttu-id="c6e74-215">Quand vous appelez `msbuild` directement sur Windows, la version MSBuild du .NET Framework est utilisée.</span><span class="sxs-lookup"><span data-stu-id="c6e74-215">When calling `msbuild` directly on Windows, the .NET Framework version of MSBuild is used.</span></span> <span data-ttu-id="c6e74-216">L’appel de `dotnet build` sur un profil autre qu’un dossier :</span><span class="sxs-lookup"><span data-stu-id="c6e74-216">Calling `dotnet build` on a non-folder profile:</span></span>

* <span data-ttu-id="c6e74-217">Appelle `msbuild`, qui utilise MSDeploy.</span><span class="sxs-lookup"><span data-stu-id="c6e74-217">Invokes `msbuild`, which uses MSDeploy.</span></span>
* <span data-ttu-id="c6e74-218">Entraîne un échec (même en cas d’exécution sur Windows).</span><span class="sxs-lookup"><span data-stu-id="c6e74-218">Results in a failure (even when running on Windows).</span></span> <span data-ttu-id="c6e74-219">Pour publier avec un profil autre qu’un dossier, appelez `msbuild` directement.</span><span class="sxs-lookup"><span data-stu-id="c6e74-219">To publish with a non-folder profile, call `msbuild` directly.</span></span>

<span data-ttu-id="c6e74-220">Le profil de publication de dossier suivant a été créé avec Visual Studio et publie sur un partage réseau :</span><span class="sxs-lookup"><span data-stu-id="c6e74-220">The following folder publish profile was created with Visual Studio and publishes to a network share:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<!--
This file is used by the publish/package process of your Web project.
You can customize the behavior of this process by editing this 
MSBuild file.
-->
<Project ToolsVersion="4.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
  <PropertyGroup>
    <WebPublishMethod>FileSystem</WebPublishMethod>
    <PublishProvider>FileSystem</PublishProvider>
    <LastUsedBuildConfiguration>Release</LastUsedBuildConfiguration>
    <LastUsedPlatform>Any CPU</LastUsedPlatform>
    <SiteUrlToLaunchAfterPublish />
    <LaunchSiteAfterPublish>True</LaunchSiteAfterPublish>
    <ExcludeApp_Data>False</ExcludeApp_Data>
    <PublishFramework>netcoreapp1.1</PublishFramework>
    <ProjectGuid>c30c453c-312e-40c4-aec9-394a145dee0b</ProjectGuid>
    <publishUrl>\\r8\Release\AdminWeb</publishUrl>
    <DeleteExistingFiles>False</DeleteExistingFiles>
  </PropertyGroup>
</Project>
```

<span data-ttu-id="c6e74-221">Dans l'exemple précédent :</span><span class="sxs-lookup"><span data-stu-id="c6e74-221">In the preceding example:</span></span>

* <span data-ttu-id="c6e74-222">La propriété `<ExcludeApp_Data>` est présente simplement pour satisfaire une exigence de schéma XML.</span><span class="sxs-lookup"><span data-stu-id="c6e74-222">The `<ExcludeApp_Data>` property is present merely to satisfy an XML schema requirement.</span></span> <span data-ttu-id="c6e74-223">La propriété `<ExcludeApp_Data>` n’a aucun effet sur le processus de publication, même s’il y a un dossier *App_Data* à la racine du projet.</span><span class="sxs-lookup"><span data-stu-id="c6e74-223">The `<ExcludeApp_Data>` property has no effect on the publish process, even if there's an *App_Data* folder in the project root.</span></span> <span data-ttu-id="c6e74-224">Le dossier *App_Data* ne reçoit pas de traitement spécial comme c’est le cas dans les projets ASP.NET 4.x.</span><span class="sxs-lookup"><span data-stu-id="c6e74-224">The *App_Data* folder doesn't receive special treatment as it does in ASP.NET 4.x projects.</span></span>

<!--

NOTE: Temporarily removed from 'Using the .NET Core CLI' below until https://github.com/aspnet/websdk/issues/888 is resolved.

    ```dotnetcli
    dotnet publish /p:Configuration=Release /p:PublishProfile=FolderProfile
    ```

-->

* <span data-ttu-id="c6e74-225">La propriété `<LastUsedBuildConfiguration>` a la valeur `Release`.</span><span class="sxs-lookup"><span data-stu-id="c6e74-225">The `<LastUsedBuildConfiguration>` property is set to `Release`.</span></span> <span data-ttu-id="c6e74-226">En cas de publication à partir de Visual Studio, la valeur de `<LastUsedBuildConfiguration>` est définie sur la valeur existante au démarrage du processus de publication.</span><span class="sxs-lookup"><span data-stu-id="c6e74-226">When publishing from Visual Studio, the value of `<LastUsedBuildConfiguration>` is set using the value when the publish process is started.</span></span> <span data-ttu-id="c6e74-227">La propriété `<LastUsedBuildConfiguration>` est spéciale et ne doit pas être remplacée dans un fichier MSBuild importé.</span><span class="sxs-lookup"><span data-stu-id="c6e74-227">`<LastUsedBuildConfiguration>` is special and shouldn't be overridden in an imported MSBuild file.</span></span> <span data-ttu-id="c6e74-228">Cette propriété peut, toutefois, être remplacée à partir de la ligne de commande avec l’une des approches suivantes.</span><span class="sxs-lookup"><span data-stu-id="c6e74-228">This property can, however, be overridden from the command line using one of the following approaches.</span></span>
  * <span data-ttu-id="c6e74-229">À l’aide de CLI .NET Core :</span><span class="sxs-lookup"><span data-stu-id="c6e74-229">Using the .NET Core CLI:</span></span>

    ```dotnetcli
    dotnet build -c Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
    ```

  * <span data-ttu-id="c6e74-230">À l’aide de MSBuild :</span><span class="sxs-lookup"><span data-stu-id="c6e74-230">Using MSBuild:</span></span>

    ```console
    msbuild /p:Configuration=Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
    ```

  <span data-ttu-id="c6e74-231">Pour plus d’informations, consultez [MSBuild: how to set the configuration property](http://sedodream.com/2012/10/27/MSBuildHowToSetTheConfigurationProperty.aspx) (Comment définir la propriété de configuration).</span><span class="sxs-lookup"><span data-stu-id="c6e74-231">For more information, see [MSBuild: how to set the configuration property](http://sedodream.com/2012/10/27/MSBuildHowToSetTheConfigurationProperty.aspx).</span></span>

## <a name="publish-to-an-msdeploy-endpoint-from-the-command-line"></a><span data-ttu-id="c6e74-232">Publier sur un point de terminaison MSDeploy à partir de la ligne de commande</span><span class="sxs-lookup"><span data-stu-id="c6e74-232">Publish to an MSDeploy endpoint from the command line</span></span>

<span data-ttu-id="c6e74-233">L’exemple suivant utilise une application web ASP.NET Core créée par Visual Studio et nommée *AzureWebApp*.</span><span class="sxs-lookup"><span data-stu-id="c6e74-233">The following example uses an ASP.NET Core web app created by Visual Studio named *AzureWebApp*.</span></span> <span data-ttu-id="c6e74-234">Un profil de publication Azure Apps est ajouté avec Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="c6e74-234">An Azure Apps publish profile is added with Visual Studio.</span></span> <span data-ttu-id="c6e74-235">Pour plus d’informations sur la création d’un profil, consultez la section [profils de publication](#publish-profiles).</span><span class="sxs-lookup"><span data-stu-id="c6e74-235">For more information on how to create a profile, see the [Publish profiles](#publish-profiles) section.</span></span>

<span data-ttu-id="c6e74-236">Pour déployer l’application à l’aide d’un profil de publication, exécutez la `msbuild` commande à partir d’une **invite de commandes développeur** Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="c6e74-236">To deploy the app using a publish profile, execute the `msbuild` command from a Visual Studio **Developer Command Prompt**.</span></span> <span data-ttu-id="c6e74-237">L’invite de commandes est disponible dans le dossier *Visual Studio* du menu **Démarrer** sur la barre des tâches Windows.</span><span class="sxs-lookup"><span data-stu-id="c6e74-237">The command prompt is available in the *Visual Studio* folder of the **Start** menu on the Windows taskbar.</span></span> <span data-ttu-id="c6e74-238">Pour en faciliter accès, vous pouvez ajouter à l’invite de commandes au menu **Outils** dans Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="c6e74-238">For easier access, you can add the command prompt to the **Tools** menu in Visual Studio.</span></span> <span data-ttu-id="c6e74-239">Pour plus d’informations, consultez [invite de commandes développeur pour Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs#run-the-command-prompt-from-inside-visual-studio).</span><span class="sxs-lookup"><span data-stu-id="c6e74-239">For more information, see [Developer Command Prompt for Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs#run-the-command-prompt-from-inside-visual-studio).</span></span>

<span data-ttu-id="c6e74-240">MSBuild utilise la syntaxe de commande suivante :</span><span class="sxs-lookup"><span data-stu-id="c6e74-240">MSBuild uses the following command syntax:</span></span>

```console
msbuild {PATH} 
    /p:DeployOnBuild=true 
    /p:PublishProfile={PROFILE} 
    /p:Username={USERNAME} 
    /p:Password={PASSWORD}
```

* <span data-ttu-id="c6e74-241">{PATH} : chemin d’accès au fichier projet de l’application.</span><span class="sxs-lookup"><span data-stu-id="c6e74-241">{PATH}: Path to the app's project file.</span></span>
* <span data-ttu-id="c6e74-242">{PROFILe} : nom du profil de publication.</span><span class="sxs-lookup"><span data-stu-id="c6e74-242">{PROFILE}: Name of the publish profile.</span></span>
* <span data-ttu-id="c6e74-243">{USERNAME} : nom d’utilisateur MSDeploy.</span><span class="sxs-lookup"><span data-stu-id="c6e74-243">{USERNAME}: MSDeploy username.</span></span> <span data-ttu-id="c6e74-244">{USERNAME} figure dans le profil de publication.</span><span class="sxs-lookup"><span data-stu-id="c6e74-244">The {USERNAME} can be found in the publish profile.</span></span>
* <span data-ttu-id="c6e74-245">{PASSWORD} : mot de passe MSDeploy.</span><span class="sxs-lookup"><span data-stu-id="c6e74-245">{PASSWORD}: MSDeploy password.</span></span> <span data-ttu-id="c6e74-246">Obtenez le {PASSWORD} à partir du fichier *{PROFILE}. PublishSettings*.</span><span class="sxs-lookup"><span data-stu-id="c6e74-246">Obtain the {PASSWORD} from the *{PROFILE}.PublishSettings* file.</span></span> <span data-ttu-id="c6e74-247">Téléchargez le fichier *.PublishSettings* à partir d’un des emplacements suivants :</span><span class="sxs-lookup"><span data-stu-id="c6e74-247">Download the *.PublishSettings* file from either:</span></span>
  * <span data-ttu-id="c6e74-248">**Explorateur de solutions**: sélectionnez **Afficher**les  >  **Cloud Explorer**.</span><span class="sxs-lookup"><span data-stu-id="c6e74-248">**Solution Explorer**: Select **View** > **Cloud Explorer**.</span></span> <span data-ttu-id="c6e74-249">Connectez-vous avec votre abonnement Azure.</span><span class="sxs-lookup"><span data-stu-id="c6e74-249">Connect with your Azure subscription.</span></span> <span data-ttu-id="c6e74-250">Ouvrez **App Services**.</span><span class="sxs-lookup"><span data-stu-id="c6e74-250">Open **App Services**.</span></span> <span data-ttu-id="c6e74-251">Faites un clic droit sur l’application.</span><span class="sxs-lookup"><span data-stu-id="c6e74-251">Right-click the app.</span></span> <span data-ttu-id="c6e74-252">Sélectionnez **Télecharger un profil de publication**.</span><span class="sxs-lookup"><span data-stu-id="c6e74-252">Select **Download Publish Profile**.</span></span>
  * <span data-ttu-id="c6e74-253">Portail Azure : sélectionnez **accéder au profil de publication** dans le panneau **vue d’ensemble** de l’application Web.</span><span class="sxs-lookup"><span data-stu-id="c6e74-253">Azure portal: Select **Get publish profile** in the web app's **Overview** panel.</span></span>

<span data-ttu-id="c6e74-254">L’exemple suivant utilise un profil de publication nommé *AzureWebApp - Web Deploy*:</span><span class="sxs-lookup"><span data-stu-id="c6e74-254">The following example uses a publish profile named *AzureWebApp - Web Deploy*:</span></span>

```console
msbuild "AzureWebApp.csproj" 
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

<span data-ttu-id="c6e74-255">Un profil de publication peut également être utilisé avec la commande [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) de l’interface CLI .NET Core à partir d’un interpréteur de commandes Windows :</span><span class="sxs-lookup"><span data-stu-id="c6e74-255">A publish profile can also be used with the .NET Core CLI's [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) command from a Windows command shell:</span></span>

```dotnetcli
dotnet msbuild "AzureWebApp.csproj"
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

> [!IMPORTANT]
> <span data-ttu-id="c6e74-256">La commande `dotnet msbuild` est une commande multiplateforme pouvant compiler des applications ASP.NET Core sur macOS et Linux.</span><span class="sxs-lookup"><span data-stu-id="c6e74-256">The `dotnet msbuild` command is a cross-platform command and can compile ASP.NET Core apps on macOS and Linux.</span></span> <span data-ttu-id="c6e74-257">Toutefois, MSBuild sur macOS et Linux n’est pas capable de déployer une application sur Azure ou d’autres points de terminaison MSDeploy.</span><span class="sxs-lookup"><span data-stu-id="c6e74-257">However, MSBuild on macOS and Linux isn't capable of deploying an app to Azure or other MSDeploy endpoints.</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="c6e74-258">Définir l’environnement</span><span class="sxs-lookup"><span data-stu-id="c6e74-258">Set the environment</span></span>

<span data-ttu-id="c6e74-259">Incluez la propriété `<EnvironmentName>` dans le profil de facturation (*.pubxml*) ou le fichier projet pour définir [l’environnement](xref:fundamentals/environments) de l’application :</span><span class="sxs-lookup"><span data-stu-id="c6e74-259">Include the `<EnvironmentName>` property in the publish profile (*.pubxml*) or project file to set the app's [environment](xref:fundamentals/environments):</span></span>

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

<span data-ttu-id="c6e74-260">Si vous avez besoin de transformations de *web.config* (par exemple, définir les variables d’environnement basées sur la configuration, le profil ou l’environnement), consultez <xref:host-and-deploy/iis/transform-webconfig>.</span><span class="sxs-lookup"><span data-stu-id="c6e74-260">If you require *web.config* transformations (for example, setting environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="exclude-files"></a><span data-ttu-id="c6e74-261">Exclure des fichiers</span><span class="sxs-lookup"><span data-stu-id="c6e74-261">Exclude files</span></span>

<span data-ttu-id="c6e74-262">Lors de la publication d’applications web ASP.NET Core, les ressources suivantes sont incluses :</span><span class="sxs-lookup"><span data-stu-id="c6e74-262">When publishing ASP.NET Core web apps, the following assets are included:</span></span>

* <span data-ttu-id="c6e74-263">Artefacts de build</span><span class="sxs-lookup"><span data-stu-id="c6e74-263">Build artifacts</span></span>
* <span data-ttu-id="c6e74-264">Les dossiers et fichiers correspondant aux modèles d’utilisation des caractères génériques suivants :</span><span class="sxs-lookup"><span data-stu-id="c6e74-264">Folders and files matching the following globbing patterns:</span></span>
  * <span data-ttu-id="c6e74-265">`**\*.config` (par exemple *web.config*)</span><span class="sxs-lookup"><span data-stu-id="c6e74-265">`**\*.config` (for example, *web.config*)</span></span>
  * <span data-ttu-id="c6e74-266">`**\*.json` (par exemple *appsettings.json*)</span><span class="sxs-lookup"><span data-stu-id="c6e74-266">`**\*.json` (for example, *appsettings.json*)</span></span>
  * `wwwroot\**`

<span data-ttu-id="c6e74-267">MSBuild prend en charge les [modèles d’utilisation des caractères génériques](https://gruntjs.com/configuring-tasks#globbing-patterns).</span><span class="sxs-lookup"><span data-stu-id="c6e74-267">MSBuild supports [globbing patterns](https://gruntjs.com/configuring-tasks#globbing-patterns).</span></span> <span data-ttu-id="c6e74-268">Par exemple, l’élément `<Content>` suivant supprime la copie des fichiers texte (*.txt*) dans le dossier *wwwroot/content* et ses sous-dossiers :</span><span class="sxs-lookup"><span data-stu-id="c6e74-268">For example, the following `<Content>` element suppresses the copying of text (*.txt*) files in the *wwwroot\content* folder and its subfolders:</span></span>

```xml
<ItemGroup>
  <Content Update="wwwroot/content/**/*.txt" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="c6e74-269">Vous pouvez ajouter le balisage précédent à un profil de publication ou au fichier *.csproj*.</span><span class="sxs-lookup"><span data-stu-id="c6e74-269">The preceding markup can be added to a publish profile or the *.csproj* file.</span></span> <span data-ttu-id="c6e74-270">En cas d’ajout au fichier *.csproj*, la règle est ajoutée à tous les profils de publication dans le projet.</span><span class="sxs-lookup"><span data-stu-id="c6e74-270">When added to the *.csproj* file, the rule is added to all publish profiles in the project.</span></span>

<span data-ttu-id="c6e74-271">L' `<MsDeploySkipRules>` élément suivant exclut tous les fichiers du dossier *wwwroot\content* :</span><span class="sxs-lookup"><span data-stu-id="c6e74-271">The following `<MsDeploySkipRules>` element excludes all files from the *wwwroot\content* folder:</span></span>

```xml
<ItemGroup>
  <MsDeploySkipRules Include="CustomSkipFolder">
    <ObjectName>dirPath</ObjectName>
    <AbsolutePath>wwwroot\\content</AbsolutePath>
  </MsDeploySkipRules>
</ItemGroup>
```

<span data-ttu-id="c6e74-272">`<MsDeploySkipRules>` ne supprime pas les cibles *skip* du site de déploiement.</span><span class="sxs-lookup"><span data-stu-id="c6e74-272">`<MsDeploySkipRules>` won't delete the *skip* targets from the deployment site.</span></span> <span data-ttu-id="c6e74-273">Les dossiers et fichiers ciblés par `<Content>` sont supprimés du site de déploiement.</span><span class="sxs-lookup"><span data-stu-id="c6e74-273">`<Content>` targeted files and folders are deleted from the deployment site.</span></span> <span data-ttu-id="c6e74-274">Par exemple, supposez qu’une application web déployée avait les fichiers suivants :</span><span class="sxs-lookup"><span data-stu-id="c6e74-274">For example, suppose a deployed web app had the following files:</span></span>

* <span data-ttu-id="c6e74-275">*Views/Home/About1.cshtml*</span><span class="sxs-lookup"><span data-stu-id="c6e74-275">*Views/Home/About1.cshtml*</span></span>
* <span data-ttu-id="c6e74-276">*Views/Home/About2.cshtml*</span><span class="sxs-lookup"><span data-stu-id="c6e74-276">*Views/Home/About2.cshtml*</span></span>
* <span data-ttu-id="c6e74-277">*Views/Home/About3.cshtml*</span><span class="sxs-lookup"><span data-stu-id="c6e74-277">*Views/Home/About3.cshtml*</span></span>

<span data-ttu-id="c6e74-278">Si les éléments `<MsDeploySkipRules>` suivants sont ajoutés, ces fichiers ne sont pas supprimés sur le site de déploiement.</span><span class="sxs-lookup"><span data-stu-id="c6e74-278">If the following `<MsDeploySkipRules>` elements are added, those files wouldn't be deleted on the deployment site.</span></span>

```xml
<ItemGroup>
  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About1.cshtml</AbsolutePath>
  </MsDeploySkipRules>

  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About2.cshtml</AbsolutePath>
  </MsDeploySkipRules>

  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About3.cshtml</AbsolutePath>
  </MsDeploySkipRules>
</ItemGroup>
```

<span data-ttu-id="c6e74-279">Les éléments `<MsDeploySkipRules>` précédents empêchent le déploiement des fichiers *skipped*.</span><span class="sxs-lookup"><span data-stu-id="c6e74-279">The preceding `<MsDeploySkipRules>` elements prevent the *skipped* files from being deployed.</span></span> <span data-ttu-id="c6e74-280">Ces fichiers ne sont pas supprimés une fois déployés.</span><span class="sxs-lookup"><span data-stu-id="c6e74-280">It won't delete those files once they're deployed.</span></span>

<span data-ttu-id="c6e74-281">L’élément `<Content>` suivant supprime les fichiers ciblés sur le site de déploiement :</span><span class="sxs-lookup"><span data-stu-id="c6e74-281">The following `<Content>` element deletes the targeted files at the deployment site:</span></span>

```xml
<ItemGroup>
  <Content Update="Views/Home/About?.cshtml" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="c6e74-282">L’utilisation du déploiement à partir de la ligne de commande avec l’élément `<Content>` précédent génère une variante de la sortie suivante :</span><span class="sxs-lookup"><span data-stu-id="c6e74-282">Using command-line deployment with the preceding `<Content>` element yields a variation of the following output:</span></span>

```console
MSDeployPublish:
  Starting Web deployment task from source: manifest(C:\Webs\Web1\obj\Release\{TARGET FRAMEWORK MONIKER}\PubTmp\Web1.SourceManifest.
  xml) to Destination: auto().
  Deleting file (Web11112\Views\Home\About1.cshtml).
  Deleting file (Web11112\Views\Home\About2.cshtml).
  Deleting file (Web11112\Views\Home\About3.cshtml).
  Updating file (Web11112\web.config).
  Updating file (Web11112\Web1.deps.json).
  Updating file (Web11112\Web1.dll).
  Updating file (Web11112\Web1.pdb).
  Updating file (Web11112\Web1.runtimeconfig.json).
  Successfully executed Web deployment task.
  Publish Succeeded.
Done Building Project "C:\Webs\Web1\Web1.csproj" (default targets).
```

## <a name="include-files"></a><span data-ttu-id="c6e74-283">Fichiers Include</span><span class="sxs-lookup"><span data-stu-id="c6e74-283">Include files</span></span>

<span data-ttu-id="c6e74-284">Les sections suivantes décrivent différentes approches pour l’inclusion de fichier au moment de la publication.</span><span class="sxs-lookup"><span data-stu-id="c6e74-284">The following sections outline different approaches for file inclusion at publish time.</span></span> <span data-ttu-id="c6e74-285">La section [général d’inclusion de fichier](#general-file-inclusion) utilise l' `DotNetPublishFiles` élément, qui est fourni par un fichier de cibles de publication dans le [Kit de développement logiciel (SDK) Web](xref:razor-pages/web-sdk).</span><span class="sxs-lookup"><span data-stu-id="c6e74-285">The [General file inclusion](#general-file-inclusion) section uses the `DotNetPublishFiles` item, which is provided by a publish targets file in the [Web SDK](xref:razor-pages/web-sdk).</span></span> <span data-ttu-id="c6e74-286">La section d' [inclusion sélective des fichiers](#selective-file-inclusion) utilise l' `ResolvedFileToPublish` élément, qui est fourni par un fichier de cibles de publication dans le [Kit SDK .net Core](/dotnet/core/project-sdk/msbuild-props).</span><span class="sxs-lookup"><span data-stu-id="c6e74-286">The [Selective file inclusion](#selective-file-inclusion) section uses the `ResolvedFileToPublish` item, which is provided by a publish targets file in the [.NET Core SDK](/dotnet/core/project-sdk/msbuild-props).</span></span> <span data-ttu-id="c6e74-287">Comme le SDK Web dépend du SDK .NET Core, l’élément peut être utilisé dans un projet ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c6e74-287">Because the Web SDK depends on the .NET Core SDK, either item can be used in an ASP.NET Core project.</span></span>

### <a name="general-file-inclusion"></a><span data-ttu-id="c6e74-288">Inclusion de fichier générale</span><span class="sxs-lookup"><span data-stu-id="c6e74-288">General file inclusion</span></span>

<span data-ttu-id="c6e74-289">L’élément `<ItemGroup>` de l’exemple suivant illustre la copie d’un dossier situé en dehors du répertoire de projet dans un dossier du site publié.</span><span class="sxs-lookup"><span data-stu-id="c6e74-289">The following example's `<ItemGroup>` element demonstrates copying a folder located outside of the project directory to a folder of the published site.</span></span> <span data-ttu-id="c6e74-290">Les fichiers ajoutés à l’élément `<ItemGroup>` du balisage suivant sont inclus par défaut.</span><span class="sxs-lookup"><span data-stu-id="c6e74-290">Any files added to the following markup's `<ItemGroup>` are included by default.</span></span>

```xml
<ItemGroup>
  <_CustomFiles Include="$(MSBuildProjectDirectory)/../images/**/*" />
  <DotNetPublishFiles Include="@(_CustomFiles)">
    <DestinationRelativePath>wwwroot/images/%(RecursiveDir)%(Filename)%(Extension)</DestinationRelativePath>
  </DotNetPublishFiles>
</ItemGroup>
```

<span data-ttu-id="c6e74-291">Le balisage précédent :</span><span class="sxs-lookup"><span data-stu-id="c6e74-291">The preceding markup:</span></span>

* <span data-ttu-id="c6e74-292">Vous pouvez l’ajouter au fichier *.csproj* ou au profil de publication.</span><span class="sxs-lookup"><span data-stu-id="c6e74-292">Can be added to the *.csproj* file or the publish profile.</span></span> <span data-ttu-id="c6e74-293">Si vous l’ajoutez au fichier *csproj*, il est inclus dans chaque profil de publication du projet.</span><span class="sxs-lookup"><span data-stu-id="c6e74-293">If it's added to the *.csproj* file, it's included in each publish profile in the project.</span></span>
* <span data-ttu-id="c6e74-294">Déclare un élément `_CustomFiles` pour stocker les fichiers qui correspondent au modèle d’utilisation des caractères génériques `Include` de l’attribut.</span><span class="sxs-lookup"><span data-stu-id="c6e74-294">Declares a `_CustomFiles` item to store files matching the `Include` attribute's globbing pattern.</span></span> <span data-ttu-id="c6e74-295">Le dossier d’*images* référencé dans le modèle se trouve en dehors du répertoire de projet.</span><span class="sxs-lookup"><span data-stu-id="c6e74-295">The *images* folder referenced in the pattern is located outside of the project directory.</span></span> <span data-ttu-id="c6e74-296">Une [propriété réservée](/visualstudio/msbuild/msbuild-reserved-and-well-known-properties), nommée `$(MSBuildProjectDirectory)`, se traduit par le chemin absolu du fichier projet.</span><span class="sxs-lookup"><span data-stu-id="c6e74-296">A [reserved property](/visualstudio/msbuild/msbuild-reserved-and-well-known-properties), named `$(MSBuildProjectDirectory)`, resolves to the project file's absolute path.</span></span>
* <span data-ttu-id="c6e74-297">Fournit une liste de fichiers à l’élément `DotNetPublishFiles`.</span><span class="sxs-lookup"><span data-stu-id="c6e74-297">Provides a list of files to the `DotNetPublishFiles` item.</span></span> <span data-ttu-id="c6e74-298">Par défaut, l’élément `<DestinationRelativePath>` de l’élément est vide.</span><span class="sxs-lookup"><span data-stu-id="c6e74-298">By default, the item's `<DestinationRelativePath>` element is empty.</span></span> <span data-ttu-id="c6e74-299">La valeur par défaut est remplacée dans le balisage et utilise des [métadonnées d’éléments connus](/visualstudio/msbuild/msbuild-well-known-item-metadata) comme `%(RecursiveDir)`.</span><span class="sxs-lookup"><span data-stu-id="c6e74-299">The default value is overridden in the markup and uses [well-known item metadata](/visualstudio/msbuild/msbuild-well-known-item-metadata) such as `%(RecursiveDir)`.</span></span> <span data-ttu-id="c6e74-300">Le texte interne représente le dossier *wwwroot/images* du site publié.</span><span class="sxs-lookup"><span data-stu-id="c6e74-300">The inner text represents the *wwwroot/images* folder of the published site.</span></span>

### <a name="selective-file-inclusion"></a><span data-ttu-id="c6e74-301">Inclusion de fichier sélective</span><span class="sxs-lookup"><span data-stu-id="c6e74-301">Selective file inclusion</span></span>

<span data-ttu-id="c6e74-302">Dans l’exemple suivant, le balisage en surbrillance illustre ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="c6e74-302">The highlighted markup in the following example demonstrates:</span></span>

* <span data-ttu-id="c6e74-303">La copie d’un fichier situé en dehors du projet dans le dossier *wwwroot* du site publié.</span><span class="sxs-lookup"><span data-stu-id="c6e74-303">Copying a file located outside of the project into the published site's *wwwroot* folder.</span></span> <span data-ttu-id="c6e74-304">Le nom de fichier *ReadMe2.md* est conservé.</span><span class="sxs-lookup"><span data-stu-id="c6e74-304">The file name of *ReadMe2.md* is maintained.</span></span>
* <span data-ttu-id="c6e74-305">L’exclusion du dossier *wwwroot\Content*.</span><span class="sxs-lookup"><span data-stu-id="c6e74-305">Excluding the *wwwroot\Content* folder.</span></span>
* <span data-ttu-id="c6e74-306">L’exclusion de *Views\Home\About2.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="c6e74-306">Excluding *Views\Home\About2.cshtml*.</span></span>

[!code-xml[](visual-studio-publish-profiles/samples/Web1.pubxml?highlight=18-23)]

<span data-ttu-id="c6e74-307">L’exemple précédent utilise l’élément `ResolvedFileToPublish`, dont le comportement par défaut consiste à toujours copier les fichiers fournis dans l’attribut `Include` sur le site publié.</span><span class="sxs-lookup"><span data-stu-id="c6e74-307">The preceding example uses the `ResolvedFileToPublish` item, whose default behavior is to always copy the files provided in the `Include` attribute to the published site.</span></span> <span data-ttu-id="c6e74-308">Remplacez le comportement par défaut en incluant un élément enfant `<CopyToPublishDirectory>` avec le texte interne `Never` ou `PreserveNewest`.</span><span class="sxs-lookup"><span data-stu-id="c6e74-308">Override the default behavior by including a `<CopyToPublishDirectory>` child element with inner text of either `Never` or `PreserveNewest`.</span></span> <span data-ttu-id="c6e74-309">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="c6e74-309">For example:</span></span>

```xml
<ResolvedFileToPublish Include="..\ReadMe2.md">
  <RelativePath>wwwroot\ReadMe2.md</RelativePath>
  <CopyToPublishDirectory>PreserveNewest</CopyToPublishDirectory>
</ResolvedFileToPublish>
```

<span data-ttu-id="c6e74-310">Pour voir d’autres exemples de déploiement, consultez le [Fichier Lisez-moi du dépôt du SDK Web](https://github.com/aspnet/websdk).</span><span class="sxs-lookup"><span data-stu-id="c6e74-310">For more deployment samples, see the [Web SDK repository Readme](https://github.com/aspnet/websdk).</span></span>

## <a name="run-a-target-before-or-after-publishing"></a><span data-ttu-id="c6e74-311">Exécuter une cible avant ou après la publication</span><span class="sxs-lookup"><span data-stu-id="c6e74-311">Run a target before or after publishing</span></span>

<span data-ttu-id="c6e74-312">Les cibles intégrées `BeforePublish` et `AfterPublish` exécutent une cible avant ou après la cible de publication.</span><span class="sxs-lookup"><span data-stu-id="c6e74-312">The built-in `BeforePublish` and `AfterPublish` targets execute a target before or after the publish target.</span></span> <span data-ttu-id="c6e74-313">Ajoutez les éléments suivants au profil de publication pour journaliser les messages de console avant et après la publication :</span><span class="sxs-lookup"><span data-stu-id="c6e74-313">Add the following elements to the publish profile to log console messages both before and after publishing:</span></span>

```xml
<Target Name="CustomActionsBeforePublish" BeforeTargets="BeforePublish">
    <Message Text="Inside BeforePublish" Importance="high" />
  </Target>
  <Target Name="CustomActionsAfterPublish" AfterTargets="AfterPublish">
    <Message Text="Inside AfterPublish" Importance="high" />
</Target>
```

## <a name="publish-to-a-server-using-an-untrusted-certificate"></a><span data-ttu-id="c6e74-314">Publier sur un serveur à l’aide d’un certificat non approuvé</span><span class="sxs-lookup"><span data-stu-id="c6e74-314">Publish to a server using an untrusted certificate</span></span>

<span data-ttu-id="c6e74-315">Ajoutez la propriété `<AllowUntrustedCertificate>` avec la valeur `True` au profil de publication :</span><span class="sxs-lookup"><span data-stu-id="c6e74-315">Add the `<AllowUntrustedCertificate>` property with a value of `True` to the publish profile:</span></span>

```xml
<PropertyGroup>
  <AllowUntrustedCertificate>True</AllowUntrustedCertificate>
</PropertyGroup>
```

## <a name="the-kudu-service"></a><span data-ttu-id="c6e74-316">Le service Kudu</span><span class="sxs-lookup"><span data-stu-id="c6e74-316">The Kudu service</span></span>

<span data-ttu-id="c6e74-317">Pour afficher les fichiers dans un déploiement d’application web Azure App Service, utilisez le [service Kudu](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service).</span><span class="sxs-lookup"><span data-stu-id="c6e74-317">To view the files in an Azure App Service web app deployment, use the [Kudu service](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service).</span></span> <span data-ttu-id="c6e74-318">Ajoutez le jeton `scm` au nom de l’application web.</span><span class="sxs-lookup"><span data-stu-id="c6e74-318">Append the `scm` token to the web app name.</span></span> <span data-ttu-id="c6e74-319">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="c6e74-319">For example:</span></span>

| <span data-ttu-id="c6e74-320">URL</span><span class="sxs-lookup"><span data-stu-id="c6e74-320">URL</span></span>                                    | <span data-ttu-id="c6e74-321">Résultat</span><span class="sxs-lookup"><span data-stu-id="c6e74-321">Result</span></span>       |
| ---
<span data-ttu-id="c6e74-322">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="c6e74-322">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6e74-323">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6e74-323">'Blazor'</span></span>
- <span data-ttu-id="c6e74-324">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6e74-324">'Identity'</span></span>
- <span data-ttu-id="c6e74-325">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6e74-325">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6e74-326">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6e74-326">'Razor'</span></span>
- <span data-ttu-id="c6e74-327">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="c6e74-327">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c6e74-328">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="c6e74-328">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6e74-329">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6e74-329">'Blazor'</span></span>
- <span data-ttu-id="c6e74-330">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6e74-330">'Identity'</span></span>
- <span data-ttu-id="c6e74-331">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6e74-331">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6e74-332">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6e74-332">'Razor'</span></span>
- <span data-ttu-id="c6e74-333">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="c6e74-333">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c6e74-334">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="c6e74-334">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6e74-335">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6e74-335">'Blazor'</span></span>
- <span data-ttu-id="c6e74-336">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6e74-336">'Identity'</span></span>
- <span data-ttu-id="c6e74-337">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6e74-337">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6e74-338">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6e74-338">'Razor'</span></span>
- <span data-ttu-id="c6e74-339">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="c6e74-339">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c6e74-340">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="c6e74-340">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6e74-341">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6e74-341">'Blazor'</span></span>
- <span data-ttu-id="c6e74-342">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6e74-342">'Identity'</span></span>
- <span data-ttu-id="c6e74-343">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6e74-343">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6e74-344">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6e74-344">'Razor'</span></span>
- <span data-ttu-id="c6e74-345">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="c6e74-345">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c6e74-346">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="c6e74-346">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6e74-347">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6e74-347">'Blazor'</span></span>
- <span data-ttu-id="c6e74-348">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6e74-348">'Identity'</span></span>
- <span data-ttu-id="c6e74-349">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6e74-349">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6e74-350">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6e74-350">'Razor'</span></span>
- <span data-ttu-id="c6e74-351">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="c6e74-351">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c6e74-352">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="c6e74-352">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6e74-353">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6e74-353">'Blazor'</span></span>
- <span data-ttu-id="c6e74-354">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6e74-354">'Identity'</span></span>
- <span data-ttu-id="c6e74-355">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6e74-355">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6e74-356">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6e74-356">'Razor'</span></span>
- <span data-ttu-id="c6e74-357">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="c6e74-357">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c6e74-358">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="c6e74-358">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6e74-359">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6e74-359">'Blazor'</span></span>
- <span data-ttu-id="c6e74-360">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6e74-360">'Identity'</span></span>
- <span data-ttu-id="c6e74-361">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6e74-361">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6e74-362">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6e74-362">'Razor'</span></span>
- <span data-ttu-id="c6e74-363">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="c6e74-363">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c6e74-364">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="c6e74-364">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6e74-365">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6e74-365">'Blazor'</span></span>
- <span data-ttu-id="c6e74-366">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6e74-366">'Identity'</span></span>
- <span data-ttu-id="c6e74-367">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6e74-367">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6e74-368">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6e74-368">'Razor'</span></span>
- <span data-ttu-id="c6e74-369">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="c6e74-369">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c6e74-370">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="c6e74-370">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6e74-371">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6e74-371">'Blazor'</span></span>
- <span data-ttu-id="c6e74-372">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6e74-372">'Identity'</span></span>
- <span data-ttu-id="c6e74-373">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6e74-373">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6e74-374">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6e74-374">'Razor'</span></span>
- <span data-ttu-id="c6e74-375">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="c6e74-375">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c6e74-376">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="c6e74-376">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6e74-377">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6e74-377">'Blazor'</span></span>
- <span data-ttu-id="c6e74-378">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6e74-378">'Identity'</span></span>
- <span data-ttu-id="c6e74-379">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6e74-379">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6e74-380">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6e74-380">'Razor'</span></span>
- <span data-ttu-id="c6e74-381">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="c6e74-381">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c6e74-382">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="c6e74-382">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6e74-383">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6e74-383">'Blazor'</span></span>
- <span data-ttu-id="c6e74-384">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6e74-384">'Identity'</span></span>
- <span data-ttu-id="c6e74-385">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6e74-385">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6e74-386">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6e74-386">'Razor'</span></span>
- <span data-ttu-id="c6e74-387">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="c6e74-387">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c6e74-388">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="c6e74-388">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6e74-389">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6e74-389">'Blazor'</span></span>
- <span data-ttu-id="c6e74-390">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6e74-390">'Identity'</span></span>
- <span data-ttu-id="c6e74-391">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6e74-391">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6e74-392">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6e74-392">'Razor'</span></span>
- <span data-ttu-id="c6e74-393">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="c6e74-393">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c6e74-394">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="c6e74-394">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6e74-395">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6e74-395">'Blazor'</span></span>
- <span data-ttu-id="c6e74-396">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6e74-396">'Identity'</span></span>
- <span data-ttu-id="c6e74-397">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6e74-397">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6e74-398">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6e74-398">'Razor'</span></span>
- <span data-ttu-id="c6e74-399">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="c6e74-399">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c6e74-400">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="c6e74-400">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6e74-401">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6e74-401">'Blazor'</span></span>
- <span data-ttu-id="c6e74-402">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6e74-402">'Identity'</span></span>
- <span data-ttu-id="c6e74-403">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6e74-403">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6e74-404">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6e74-404">'Razor'</span></span>
- <span data-ttu-id="c6e74-405">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="c6e74-405">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c6e74-406">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="c6e74-406">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6e74-407">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6e74-407">'Blazor'</span></span>
- <span data-ttu-id="c6e74-408">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6e74-408">'Identity'</span></span>
- <span data-ttu-id="c6e74-409">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6e74-409">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6e74-410">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6e74-410">'Razor'</span></span>
- <span data-ttu-id="c6e74-411">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="c6e74-411">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c6e74-412">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="c6e74-412">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6e74-413">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6e74-413">'Blazor'</span></span>
- <span data-ttu-id="c6e74-414">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6e74-414">'Identity'</span></span>
- <span data-ttu-id="c6e74-415">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6e74-415">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6e74-416">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6e74-416">'Razor'</span></span>
- <span data-ttu-id="c6e74-417">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="c6e74-417">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c6e74-418">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="c6e74-418">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6e74-419">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6e74-419">'Blazor'</span></span>
- <span data-ttu-id="c6e74-420">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6e74-420">'Identity'</span></span>
- <span data-ttu-id="c6e74-421">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6e74-421">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6e74-422">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6e74-422">'Razor'</span></span>
- <span data-ttu-id="c6e74-423">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="c6e74-423">'SignalR' uid:</span></span> 

<span data-ttu-id="c6e74-424">------------------- | titre de--- : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="c6e74-424">------------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6e74-425">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6e74-425">'Blazor'</span></span>
- <span data-ttu-id="c6e74-426">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6e74-426">'Identity'</span></span>
- <span data-ttu-id="c6e74-427">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6e74-427">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6e74-428">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6e74-428">'Razor'</span></span>
- <span data-ttu-id="c6e74-429">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="c6e74-429">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c6e74-430">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="c6e74-430">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6e74-431">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6e74-431">'Blazor'</span></span>
- <span data-ttu-id="c6e74-432">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6e74-432">'Identity'</span></span>
- <span data-ttu-id="c6e74-433">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6e74-433">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6e74-434">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6e74-434">'Razor'</span></span>
- <span data-ttu-id="c6e74-435">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="c6e74-435">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c6e74-436">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="c6e74-436">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6e74-437">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6e74-437">'Blazor'</span></span>
- <span data-ttu-id="c6e74-438">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6e74-438">'Identity'</span></span>
- <span data-ttu-id="c6e74-439">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6e74-439">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6e74-440">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6e74-440">'Razor'</span></span>
- <span data-ttu-id="c6e74-441">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="c6e74-441">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c6e74-442">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="c6e74-442">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6e74-443">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6e74-443">'Blazor'</span></span>
- <span data-ttu-id="c6e74-444">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6e74-444">'Identity'</span></span>
- <span data-ttu-id="c6e74-445">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6e74-445">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6e74-446">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6e74-446">'Razor'</span></span>
- <span data-ttu-id="c6e74-447">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="c6e74-447">'SignalR' uid:</span></span> 

<span data-ttu-id="c6e74-448">------ | | `http://mysite.azurewebsites.net/`     | Application Web | | `http://mysite.scm.azurewebsites.net/` | Service Kudu |</span><span class="sxs-lookup"><span data-stu-id="c6e74-448">------ | | `http://mysite.azurewebsites.net/`     | Web App      | | `http://mysite.scm.azurewebsites.net/` | Kudu service |</span></span>

<span data-ttu-id="c6e74-449">Sélectionnez l’élément de menu [Console de débogage](https://github.com/projectkudu/kudu/wiki/Kudu-console) pour afficher, modifier, supprimer ou ajouter des fichiers.</span><span class="sxs-lookup"><span data-stu-id="c6e74-449">Select the [Debug Console](https://github.com/projectkudu/kudu/wiki/Kudu-console) menu item to view, edit, delete, or add files.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c6e74-450">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="c6e74-450">Additional resources</span></span>

* <span data-ttu-id="c6e74-451">[Web Deploy](https://www.iis.net/downloads/microsoft/web-deploy) (MSDeploy) simplifie le déploiement des applications web et des sites web sur les serveurs IIS.</span><span class="sxs-lookup"><span data-stu-id="c6e74-451">[Web Deploy](https://www.iis.net/downloads/microsoft/web-deploy) (MSDeploy) simplifies deployment of web apps and websites to IIS servers.</span></span>
* <span data-ttu-id="c6e74-452">[Référentiel github du kit de développement logiciel (SDK) Web](https://github.com/aspnet/websdk/issues): problèmes de fichier et demande de déploiement.</span><span class="sxs-lookup"><span data-stu-id="c6e74-452">[Web SDK GitHub repository](https://github.com/aspnet/websdk/issues): File issues and request features for deployment.</span></span>
* [<span data-ttu-id="c6e74-453">Publier une application web ASP.NET sur une machine virtuelle Azure à partir de Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c6e74-453">Publish an ASP.NET Web App to an Azure VM from Visual Studio</span></span>](/azure/virtual-machines/windows/publish-web-app-from-visual-studio)
* <xref:host-and-deploy/iis/transform-webconfig>
