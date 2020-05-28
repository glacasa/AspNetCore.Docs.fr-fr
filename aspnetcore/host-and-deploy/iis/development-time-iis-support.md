---
<span data-ttu-id="3f201-101">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="3f201-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3f201-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3f201-102">'Blazor'</span></span>
- <span data-ttu-id="3f201-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3f201-103">'Identity'</span></span>
- <span data-ttu-id="3f201-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3f201-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="3f201-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3f201-105">'Razor'</span></span>
- <span data-ttu-id="3f201-106">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="3f201-106">'SignalR' uid:</span></span> 

---
# <a name="development-time-iis-support-in-visual-studio-for-aspnet-core"></a><span data-ttu-id="3f201-107">Prise en charge d’IIS pendant le développement dans Visual Studio pour ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3f201-107">Development-time IIS support in Visual Studio for ASP.NET Core</span></span>

<span data-ttu-id="3f201-108">De [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span><span class="sxs-lookup"><span data-stu-id="3f201-108">By [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3f201-109">Cet article décrit la prise en charge de [Visual Studio](https://visualstudio.microsoft.com) pour le débogage des applications ASP.NET Core s’exécutant avec IIS sur Windows Server.</span><span class="sxs-lookup"><span data-stu-id="3f201-109">This article describes [Visual Studio](https://visualstudio.microsoft.com) support for debugging ASP.NET Core apps running with IIS on Windows Server.</span></span> <span data-ttu-id="3f201-110">Cette rubrique présente ce scénario et la configuration d’un projet.</span><span class="sxs-lookup"><span data-stu-id="3f201-110">This topic walks through enabling this scenario and setting up a project.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3f201-111">Prérequis</span><span class="sxs-lookup"><span data-stu-id="3f201-111">Prerequisites</span></span>

* [<span data-ttu-id="3f201-112">Visual Studio pour Windows</span><span class="sxs-lookup"><span data-stu-id="3f201-112">Visual Studio for Windows</span></span>](https://visualstudio.microsoft.com/downloads/)
* <span data-ttu-id="3f201-113">Charge de travail **Développement web et ASP.NET**</span><span class="sxs-lookup"><span data-stu-id="3f201-113">**ASP.NET and web development** workload</span></span>
* <span data-ttu-id="3f201-114">Charge de travail **Développement multiplateforme .NET Core**</span><span class="sxs-lookup"><span data-stu-id="3f201-114">**.NET Core cross-platform development** workload</span></span>
* <span data-ttu-id="3f201-115">Certificat de sécurité X.509 (pour la prise en charge HTTPS)</span><span class="sxs-lookup"><span data-stu-id="3f201-115">X.509 security certificate (for HTTPS support)</span></span>

## <a name="enable-iis"></a><span data-ttu-id="3f201-116">Activer IIS</span><span class="sxs-lookup"><span data-stu-id="3f201-116">Enable IIS</span></span>

1. <span data-ttu-id="3f201-117">Dans Windows, accédez à **Panneau de configuration** > **Programmes** > **Programmes et fonctionnalités** > **Activer ou désactiver des fonctionnalités Windows** (à gauche de l’écran).</span><span class="sxs-lookup"><span data-stu-id="3f201-117">In Windows, navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="3f201-118">Cochez la case **Services IIS (Internet Information Services)**.</span><span class="sxs-lookup"><span data-stu-id="3f201-118">Select the **Internet Information Services** check box.</span></span> <span data-ttu-id="3f201-119">Sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="3f201-119">Select **OK**.</span></span>

<span data-ttu-id="3f201-120">L’installation d’IIS peut nécessiter un redémarrage du système.</span><span class="sxs-lookup"><span data-stu-id="3f201-120">The IIS installation may require a system restart.</span></span>

## <a name="configure-iis"></a><span data-ttu-id="3f201-121">Configurer IIS</span><span class="sxs-lookup"><span data-stu-id="3f201-121">Configure IIS</span></span>

<span data-ttu-id="3f201-122">IIS doit avoir un site web configuré avec les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="3f201-122">IIS must have a website configured with the following:</span></span>

* <span data-ttu-id="3f201-123">**Nom d’hôte**: en général, le **site Web par défaut** est utilisé avec un **nom d’hôte** `localhost` .</span><span class="sxs-lookup"><span data-stu-id="3f201-123">**Host name**: Typically, the **Default Web Site** is used with a **Host name** of `localhost`.</span></span> <span data-ttu-id="3f201-124">Toutefois, n’importe quel site web IIS valide avec un nom d’hôte unique fonctionnera.</span><span class="sxs-lookup"><span data-stu-id="3f201-124">However, any valid IIS website with a unique host name works.</span></span>
* <span data-ttu-id="3f201-125">**Liaison de site**</span><span class="sxs-lookup"><span data-stu-id="3f201-125">**Site Binding**</span></span>
  * <span data-ttu-id="3f201-126">Pour les applications qui exigent le protocole HTTPS, créez une liaison au port 443 avec un certificat.</span><span class="sxs-lookup"><span data-stu-id="3f201-126">For apps that require HTTPS, create a binding to port 443 with a certificate.</span></span> <span data-ttu-id="3f201-127">On utilise en général le **certificat de développement IIS Express**, mais tous les certificats valides conviennent.</span><span class="sxs-lookup"><span data-stu-id="3f201-127">Typically, the **IIS Express Development Certificate** is used, but any valid certificate works.</span></span>
  * <span data-ttu-id="3f201-128">Pour les applications qui utilisent le protocole HTTP, vérifiez l’existence d’une liaison au port 80 ou créez-en une pour un nouveau site.</span><span class="sxs-lookup"><span data-stu-id="3f201-128">For apps that use HTTP, confirm the existence of a binding to post 80 or create a binding to port 80 for a new site.</span></span>
  * <span data-ttu-id="3f201-129">Utilisez une liaison unique pour HTTP ou HTTPS.</span><span class="sxs-lookup"><span data-stu-id="3f201-129">Use a single binding for either HTTP or HTTPS.</span></span> <span data-ttu-id="3f201-130">**La liaison simultanée aux ports HTTP et aux ports HTTPS n’est pas prise en charge.**</span><span class="sxs-lookup"><span data-stu-id="3f201-130">**Binding to both HTTP and HTTPS ports simultaneously isn't supported.**</span></span>

## <a name="enable-development-time-iis-support-in-visual-studio"></a><span data-ttu-id="3f201-131">Activer la prise en charge d’IIS pendant le développement dans Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3f201-131">Enable development-time IIS support in Visual Studio</span></span>

1. <span data-ttu-id="3f201-132">Lancez le programme d’installation de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="3f201-132">Launch the Visual Studio installer.</span></span>
1. <span data-ttu-id="3f201-133">Sélectionnez **Modifier** pour l’installation de Visual Studio que vous souhaitez utiliser dans le cadre de la prise en charge IIS lors du développement.</span><span class="sxs-lookup"><span data-stu-id="3f201-133">Select **Modify** for the Visual Studio installation that you plan to use for IIS development-time support.</span></span>
1. <span data-ttu-id="3f201-134">Pour la charge de travail **ASP.NET et développement web**, recherchez et installez le composant **Prise en charge IIS lors du développement**.</span><span class="sxs-lookup"><span data-stu-id="3f201-134">For the **ASP.NET and web development** workload, locate and install the **Development time IIS support** component.</span></span>

   <span data-ttu-id="3f201-135">Le composant se trouve dans la section **Facultatif**, sous **Prise en charge IIS lors du développement**, dans le volet **Détails de l’installation** à droite des charges de travail.</span><span class="sxs-lookup"><span data-stu-id="3f201-135">The component is listed in the **Optional** section under **Development time IIS support** in the **Installation details** panel to the right of the workloads.</span></span> <span data-ttu-id="3f201-136">Ce composant installe le [module ASP.NET Core](xref:host-and-deploy/aspnet-core-module), qui est un module IIS natif nécessaire à l’exécution des applications ASP.NET Core avec IIS.</span><span class="sxs-lookup"><span data-stu-id="3f201-136">The component installs the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), which is a native IIS module required to run ASP.NET Core apps with IIS.</span></span>

## <a name="configure-the-project"></a><span data-ttu-id="3f201-137">Configurer le projet</span><span class="sxs-lookup"><span data-stu-id="3f201-137">Configure the project</span></span>

### <a name="https-redirection"></a><span data-ttu-id="3f201-138">Redirection HTTPS</span><span class="sxs-lookup"><span data-stu-id="3f201-138">HTTPS redirection</span></span>

<span data-ttu-id="3f201-139">Pour un nouveau projet qui exige le protocole HTTPS, cochez la case **Configurer pour HTTPS** dans la fenêtre **Créer une application web ASP.NET Core**</span><span class="sxs-lookup"><span data-stu-id="3f201-139">For a new project that requires HTTPS, select the check box to **Configure for HTTPS** in the **Create a new ASP.NET Core Web Application** window.</span></span> <span data-ttu-id="3f201-140">afin d’ajouter [Redirection HTTPS et middleware HSTS](xref:security/enforcing-ssl) à l’application lors de sa création.</span><span class="sxs-lookup"><span data-stu-id="3f201-140">Selecting the check box adds [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) to the app when it's created.</span></span>

<span data-ttu-id="3f201-141">Pour un projet existant qui exige le protocole HTTPS, utilisez Redirection HTTPS et middleware HSTS dans `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="3f201-141">For an existing project that requires HTTPS, use HTTPS Redirection and HSTS Middleware in `Startup.Configure`.</span></span> <span data-ttu-id="3f201-142">Pour plus d'informations, consultez <xref:security/enforcing-ssl>.</span><span class="sxs-lookup"><span data-stu-id="3f201-142">For more information, see <xref:security/enforcing-ssl>.</span></span>

<span data-ttu-id="3f201-143">Pour un projet qui utilise le protocole HTTP, [Redirection HTTPS et middleware HSTS](xref:security/enforcing-ssl) ne sont pas ajoutés à l’application.</span><span class="sxs-lookup"><span data-stu-id="3f201-143">For a project that uses HTTP, [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) aren't added to the app.</span></span> <span data-ttu-id="3f201-144">Aucune configuration de l’application n’est nécessaire.</span><span class="sxs-lookup"><span data-stu-id="3f201-144">No app configuration is required.</span></span>

### <a name="iis-launch-profile"></a><span data-ttu-id="3f201-145">Profil de lancement IIS</span><span class="sxs-lookup"><span data-stu-id="3f201-145">IIS launch profile</span></span>

<span data-ttu-id="3f201-146">Créez un profil de lancement pour ajouter la prise en charge d’IIS pendant le développement :</span><span class="sxs-lookup"><span data-stu-id="3f201-146">Create a new launch profile to add development-time IIS support:</span></span>

1. <span data-ttu-id="3f201-147">Cliquez avec le bouton droit sur le projet dans **l’Explorateur de solutions**.</span><span class="sxs-lookup"><span data-stu-id="3f201-147">Right-click the project in **Solution Explorer**.</span></span> <span data-ttu-id="3f201-148">Sélectionner **Propriétés**.</span><span class="sxs-lookup"><span data-stu-id="3f201-148">Select **Properties**.</span></span> <span data-ttu-id="3f201-149">Ouvrez l’onglet **Déboguer** .</span><span class="sxs-lookup"><span data-stu-id="3f201-149">Open the **Debug** tab.</span></span>
1. <span data-ttu-id="3f201-150">Pour **Profil**, sélectionnez le bouton **Nouveau**.</span><span class="sxs-lookup"><span data-stu-id="3f201-150">For **Profile**, select the **New** button.</span></span> <span data-ttu-id="3f201-151">Nommez le profil « IIS » dans la fenêtre contextuelle.</span><span class="sxs-lookup"><span data-stu-id="3f201-151">Name the profile "IIS" in the popup window.</span></span> <span data-ttu-id="3f201-152">Sélectionnez **OK** pour créer le profil.</span><span class="sxs-lookup"><span data-stu-id="3f201-152">Select **OK** to create the profile.</span></span>
1. <span data-ttu-id="3f201-153">Pour le paramètre **Lancer**, sélectionnez **IIS** dans la liste.</span><span class="sxs-lookup"><span data-stu-id="3f201-153">For the **Launch** setting, select **IIS** from the list.</span></span>
1. <span data-ttu-id="3f201-154">Cochez la case **Lancer le navigateur** et indiquez l’URL du point de terminaison.</span><span class="sxs-lookup"><span data-stu-id="3f201-154">Select the check box for **Launch browser** and provide the endpoint URL.</span></span>

   <span data-ttu-id="3f201-155">Lorsque le protocole HTTPS est requis par l’application, utilisez un point de terminaison HTTPS (`https://`).</span><span class="sxs-lookup"><span data-stu-id="3f201-155">When the app requires HTTPS, use an HTTPS endpoint (`https://`).</span></span> <span data-ttu-id="3f201-156">Pour HTTP, utilisez un point de terminaison HTTP (`http://`).</span><span class="sxs-lookup"><span data-stu-id="3f201-156">For HTTP, use an HTTP (`http://`) endpoint.</span></span>

   <span data-ttu-id="3f201-157">Indiquez le nom d’hôte et le port utilisés par la [configuration IIS spécifiée précédemment](#configure-iis), en général `localhost`.</span><span class="sxs-lookup"><span data-stu-id="3f201-157">Provide the same host name and port as the [IIS configuration specified earlier uses](#configure-iis), typically `localhost`.</span></span>

   <span data-ttu-id="3f201-158">Indiquez le nom de l’application à la fin de l’URL.</span><span class="sxs-lookup"><span data-stu-id="3f201-158">Provide the name of the app at the end of the URL.</span></span>

   <span data-ttu-id="3f201-159">Par exemple, `https://localhost/WebApplication1` (HTTPS) ou `http://localhost/WebApplication1` (HTTP) sont des URL de point de terminaison valide.</span><span class="sxs-lookup"><span data-stu-id="3f201-159">For example, `https://localhost/WebApplication1` (HTTPS) or `http://localhost/WebApplication1` (HTTP) are valid endpoint URLs.</span></span>
1. <span data-ttu-id="3f201-160">Dans la section **Variables d’environnement**, sélectionnez le bouton **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="3f201-160">In the **Environment variables** section, select the **Add** button.</span></span> <span data-ttu-id="3f201-161">Indiquez une variable d’environnement ayant pour **Nom**`ASPNETCORE_ENVIRONMENT` et pour **Valeur**`Development`.</span><span class="sxs-lookup"><span data-stu-id="3f201-161">Provide an environment variable with a **Name** of `ASPNETCORE_ENVIRONMENT` and a **Value** of `Development`.</span></span>
1. <span data-ttu-id="3f201-162">Dans la zone **Paramètres de serveur web**, donnez à **URL de l’application** la valeur utilisée pour l’URL du point de terminaison **Lancer le navigateur**.</span><span class="sxs-lookup"><span data-stu-id="3f201-162">In the **Web Server Settings** area, set the **App URL** to the same value used for the **Launch browser** endpoint URL.</span></span>
1. <span data-ttu-id="3f201-163">Pour le paramètre **Modèle d’hébergement** dans Visual Studio 2019 (ou version ultérieure), sélectionnez **Par défaut** afin d’utiliser le modèle d’hébergement du projet.</span><span class="sxs-lookup"><span data-stu-id="3f201-163">For the **Hosting Model** setting in Visual Studio 2019 or later, select **Default** to use the hosting model used by the project.</span></span> <span data-ttu-id="3f201-164">C’est la valeur de la propriété `<AspNetCoreHostingModel>` (`InProcess` ou `OutOfProcess`) qui est employée si elle est définie par le projet dans son fichier de projet.</span><span class="sxs-lookup"><span data-stu-id="3f201-164">If the project sets the `<AspNetCoreHostingModel>` property in its project file, the value of the property (`InProcess` or `OutOfProcess`) is used.</span></span> <span data-ttu-id="3f201-165">En l’absence de cette propriété, le modèle d’hébergement par défaut de l’application est utilisé in-process.</span><span class="sxs-lookup"><span data-stu-id="3f201-165">If the property isn't present, the default hosting model of the app is used, which is in-process.</span></span> <span data-ttu-id="3f201-166">Si l’application réclame un paramètre de modèle d’hébergement explicite autre que son modèle normal, définissez le **Modèle d’hébergement** sur `In Process` ou `Out Of Process` en fonction des besoins.</span><span class="sxs-lookup"><span data-stu-id="3f201-166">If the app requires an explicit hosting model setting different from the app's normal hosting model, set the **Hosting Model** to either `In Process` or `Out Of Process` as needed.</span></span>
1. <span data-ttu-id="3f201-167">Enregistrez le profil.</span><span class="sxs-lookup"><span data-stu-id="3f201-167">Save the profile.</span></span>

<span data-ttu-id="3f201-168">Si vous n’utilisez pas Visual Studio, ajoutez manuellement un profil de lancement au fichier [launchSettings.json](https://json.schemastore.org/launchsettings) dans le dossier *Propriétés*.</span><span class="sxs-lookup"><span data-stu-id="3f201-168">When not using Visual Studio, manually add a launch profile to the [launchSettings.json](https://json.schemastore.org/launchsettings) file in the *Properties* folder.</span></span> <span data-ttu-id="3f201-169">L’exemple suivant configure le profil de façon à utiliser le protocole HTTPS :</span><span class="sxs-lookup"><span data-stu-id="3f201-169">The following example configures the profile to use the HTTPS protocol:</span></span>

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iis": {
      "applicationUrl": "https://localhost/WebApplication1",
      "sslPort": 0
    }
  },
  "profiles": {
    "IIS": {
      "commandName": "IIS",
      "launchBrowser": true,
      "launchUrl": "https://localhost/WebApplication1",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    }
  }
}
```

<span data-ttu-id="3f201-170">Vérifiez que les points de terminaison `applicationUrl` et `launchUrl` coïncident et utilisent le même protocole que la configuration de liaison IIS, c’est-à-dire HTTP ou HTTPS.</span><span class="sxs-lookup"><span data-stu-id="3f201-170">Confirm that the `applicationUrl` and `launchUrl` endpoints match and use the same protocol as the IIS binding configuration, either HTTP or HTTPS.</span></span>

## <a name="run-the-project"></a><span data-ttu-id="3f201-171">Exécuter le projet</span><span class="sxs-lookup"><span data-stu-id="3f201-171">Run the project</span></span>

<span data-ttu-id="3f201-172">Exécutez Visual Studio en tant qu’administrateur :</span><span class="sxs-lookup"><span data-stu-id="3f201-172">Run Visual Studio as an administrator:</span></span>

* <span data-ttu-id="3f201-173">Vérifiez que la liste déroulante des configurations de build est définie sur **Déboguer**.</span><span class="sxs-lookup"><span data-stu-id="3f201-173">Confirm that the build configuration drop-down list is set to **Debug**.</span></span>
* <span data-ttu-id="3f201-174">Définissez le [bouton Démarrer le débogage](/visualstudio/debugger/debugger-feature-tour) sur le profil **IIS** , puis sélectionnez le bouton pour démarrer l’application.</span><span class="sxs-lookup"><span data-stu-id="3f201-174">Set the [Start Debugging button](/visualstudio/debugger/debugger-feature-tour) to the **IIS** profile and select the button to start the app.</span></span>

<span data-ttu-id="3f201-175">Visual Studio peut demander un redémarrage si vous ne l’exécutez pas en tant qu’administrateur.</span><span class="sxs-lookup"><span data-stu-id="3f201-175">Visual Studio may prompt a restart if not running as an administrator.</span></span> <span data-ttu-id="3f201-176">Si vous y êtes invité, redémarrez Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="3f201-176">If prompted, restart Visual Studio.</span></span>

<span data-ttu-id="3f201-177">Si vous utilisez un certificat de développement non approuvé, le navigateur peut vous amener à créer une exception pour ce certificat.</span><span class="sxs-lookup"><span data-stu-id="3f201-177">If an untrusted development certificate is used, the browser may require you to create an exception for the untrusted certificate.</span></span>

> [!NOTE]
> <span data-ttu-id="3f201-178">Le débogage d’une configuration de build de mise en production avec [Uniquement mon code](/visualstudio/debugger/just-my-code) et des optimisations du compilateur entraîne une baisse des résultats.</span><span class="sxs-lookup"><span data-stu-id="3f201-178">Debugging a Release build configuration with [Just My Code](/visualstudio/debugger/just-my-code) and compiler optimizations results in a degraded experience.</span></span> <span data-ttu-id="3f201-179">Par exemple, les points d’arrêt ne sont pas atteints.</span><span class="sxs-lookup"><span data-stu-id="3f201-179">For example, break points aren't hit.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3f201-180">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="3f201-180">Additional resources</span></span>

* [<span data-ttu-id="3f201-181">Bien démarrer avec le Gestionnaire IIS dans IIS</span><span class="sxs-lookup"><span data-stu-id="3f201-181">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* <xref:security/enforcing-ssl>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="3f201-182">Cet article décrit la prise en charge de [Visual Studio](https://visualstudio.microsoft.com) pour le débogage des applications ASP.NET Core s’exécutant avec IIS sur Windows Server.</span><span class="sxs-lookup"><span data-stu-id="3f201-182">This article describes [Visual Studio](https://visualstudio.microsoft.com) support for debugging ASP.NET Core apps running with IIS on Windows Server.</span></span> <span data-ttu-id="3f201-183">Cette rubrique présente ce scénario et la configuration d’un projet.</span><span class="sxs-lookup"><span data-stu-id="3f201-183">This topic walks through enabling this scenario and setting up a project.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3f201-184">Prérequis</span><span class="sxs-lookup"><span data-stu-id="3f201-184">Prerequisites</span></span>

* [<span data-ttu-id="3f201-185">Visual Studio pour Windows</span><span class="sxs-lookup"><span data-stu-id="3f201-185">Visual Studio for Windows</span></span>](https://visualstudio.microsoft.com/downloads/)
* <span data-ttu-id="3f201-186">Charge de travail **Développement web et ASP.NET**</span><span class="sxs-lookup"><span data-stu-id="3f201-186">**ASP.NET and web development** workload</span></span>
* <span data-ttu-id="3f201-187">Charge de travail **Développement multiplateforme .NET Core**</span><span class="sxs-lookup"><span data-stu-id="3f201-187">**.NET Core cross-platform development** workload</span></span>
* <span data-ttu-id="3f201-188">Certificat de sécurité X.509 (pour la prise en charge HTTPS)</span><span class="sxs-lookup"><span data-stu-id="3f201-188">X.509 security certificate (for HTTPS support)</span></span>

## <a name="enable-iis"></a><span data-ttu-id="3f201-189">Activer IIS</span><span class="sxs-lookup"><span data-stu-id="3f201-189">Enable IIS</span></span>

1. <span data-ttu-id="3f201-190">Dans Windows, accédez à **Panneau de configuration** > **Programmes** > **Programmes et fonctionnalités** > **Activer ou désactiver des fonctionnalités Windows** (à gauche de l’écran).</span><span class="sxs-lookup"><span data-stu-id="3f201-190">In Windows, navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="3f201-191">Cochez la case **Services IIS (Internet Information Services)**.</span><span class="sxs-lookup"><span data-stu-id="3f201-191">Select the **Internet Information Services** check box.</span></span> <span data-ttu-id="3f201-192">Sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="3f201-192">Select **OK**.</span></span>

<span data-ttu-id="3f201-193">L’installation d’IIS peut nécessiter un redémarrage du système.</span><span class="sxs-lookup"><span data-stu-id="3f201-193">The IIS installation may require a system restart.</span></span>

## <a name="configure-iis"></a><span data-ttu-id="3f201-194">Configurer IIS</span><span class="sxs-lookup"><span data-stu-id="3f201-194">Configure IIS</span></span>

<span data-ttu-id="3f201-195">IIS doit avoir un site web configuré avec les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="3f201-195">IIS must have a website configured with the following:</span></span>

* <span data-ttu-id="3f201-196">**Nom d’hôte**: en général, le **site Web par défaut** est utilisé avec un **nom d’hôte** `localhost` .</span><span class="sxs-lookup"><span data-stu-id="3f201-196">**Host name**: Typically, the **Default Web Site** is used with a **Host name** of `localhost`.</span></span> <span data-ttu-id="3f201-197">Toutefois, n’importe quel site web IIS valide avec un nom d’hôte unique fonctionnera.</span><span class="sxs-lookup"><span data-stu-id="3f201-197">However, any valid IIS website with a unique host name works.</span></span>
* <span data-ttu-id="3f201-198">**Liaison de site**</span><span class="sxs-lookup"><span data-stu-id="3f201-198">**Site Binding**</span></span>
  * <span data-ttu-id="3f201-199">Pour les applications qui exigent le protocole HTTPS, créez une liaison au port 443 avec un certificat.</span><span class="sxs-lookup"><span data-stu-id="3f201-199">For apps that require HTTPS, create a binding to port 443 with a certificate.</span></span> <span data-ttu-id="3f201-200">On utilise en général le **certificat de développement IIS Express**, mais tous les certificats valides conviennent.</span><span class="sxs-lookup"><span data-stu-id="3f201-200">Typically, the **IIS Express Development Certificate** is used, but any valid certificate works.</span></span>
  * <span data-ttu-id="3f201-201">Pour les applications qui utilisent le protocole HTTP, vérifiez l’existence d’une liaison au port 80 ou créez-en une pour un nouveau site.</span><span class="sxs-lookup"><span data-stu-id="3f201-201">For apps that use HTTP, confirm the existence of a binding to post 80 or create a binding to port 80 for a new site.</span></span>
  * <span data-ttu-id="3f201-202">Utilisez une liaison unique pour HTTP ou HTTPS.</span><span class="sxs-lookup"><span data-stu-id="3f201-202">Use a single binding for either HTTP or HTTPS.</span></span> <span data-ttu-id="3f201-203">**La liaison simultanée aux ports HTTP et aux ports HTTPS n’est pas prise en charge.**</span><span class="sxs-lookup"><span data-stu-id="3f201-203">**Binding to both HTTP and HTTPS ports simultaneously isn't supported.**</span></span>

## <a name="enable-development-time-iis-support-in-visual-studio"></a><span data-ttu-id="3f201-204">Activer la prise en charge d’IIS pendant le développement dans Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3f201-204">Enable development-time IIS support in Visual Studio</span></span>

1. <span data-ttu-id="3f201-205">Lancez le programme d’installation de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="3f201-205">Launch the Visual Studio installer.</span></span>
1. <span data-ttu-id="3f201-206">Sélectionnez **Modifier** pour l’installation de Visual Studio que vous souhaitez utiliser dans le cadre de la prise en charge IIS lors du développement.</span><span class="sxs-lookup"><span data-stu-id="3f201-206">Select **Modify** for the Visual Studio installation that you plan to use for IIS development-time support.</span></span>
1. <span data-ttu-id="3f201-207">Pour la charge de travail **ASP.NET et développement web**, recherchez et installez le composant **Prise en charge IIS lors du développement**.</span><span class="sxs-lookup"><span data-stu-id="3f201-207">For the **ASP.NET and web development** workload, locate and install the **Development time IIS support** component.</span></span>

   <span data-ttu-id="3f201-208">Le composant se trouve dans la section **Facultatif**, sous **Prise en charge IIS lors du développement**, dans le volet **Détails de l’installation** à droite des charges de travail.</span><span class="sxs-lookup"><span data-stu-id="3f201-208">The component is listed in the **Optional** section under **Development time IIS support** in the **Installation details** panel to the right of the workloads.</span></span> <span data-ttu-id="3f201-209">Ce composant installe le [module ASP.NET Core](xref:host-and-deploy/aspnet-core-module), qui est un module IIS natif nécessaire à l’exécution des applications ASP.NET Core avec IIS.</span><span class="sxs-lookup"><span data-stu-id="3f201-209">The component installs the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), which is a native IIS module required to run ASP.NET Core apps with IIS.</span></span>

## <a name="configure-the-project"></a><span data-ttu-id="3f201-210">Configurer le projet</span><span class="sxs-lookup"><span data-stu-id="3f201-210">Configure the project</span></span>

### <a name="https-redirection"></a><span data-ttu-id="3f201-211">Redirection HTTPS</span><span class="sxs-lookup"><span data-stu-id="3f201-211">HTTPS redirection</span></span>

<span data-ttu-id="3f201-212">Pour un nouveau projet qui exige le protocole HTTPS, cochez la case **Configurer pour HTTPS** dans la fenêtre **Créer une application web ASP.NET Core**</span><span class="sxs-lookup"><span data-stu-id="3f201-212">For a new project that requires HTTPS, select the check box to **Configure for HTTPS** in the **Create a new ASP.NET Core Web Application** window.</span></span> <span data-ttu-id="3f201-213">afin d’ajouter [Redirection HTTPS et middleware HSTS](xref:security/enforcing-ssl) à l’application lors de sa création.</span><span class="sxs-lookup"><span data-stu-id="3f201-213">Selecting the check box adds [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) to the app when it's created.</span></span>

<span data-ttu-id="3f201-214">Pour un projet existant qui exige le protocole HTTPS, utilisez Redirection HTTPS et middleware HSTS dans `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="3f201-214">For an existing project that requires HTTPS, use HTTPS Redirection and HSTS Middleware in `Startup.Configure`.</span></span> <span data-ttu-id="3f201-215">Pour plus d'informations, consultez <xref:security/enforcing-ssl>.</span><span class="sxs-lookup"><span data-stu-id="3f201-215">For more information, see <xref:security/enforcing-ssl>.</span></span>

<span data-ttu-id="3f201-216">Pour un projet qui utilise le protocole HTTP, [Redirection HTTPS et middleware HSTS](xref:security/enforcing-ssl) ne sont pas ajoutés à l’application.</span><span class="sxs-lookup"><span data-stu-id="3f201-216">For a project that uses HTTP, [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) aren't added to the app.</span></span> <span data-ttu-id="3f201-217">Aucune configuration de l’application n’est nécessaire.</span><span class="sxs-lookup"><span data-stu-id="3f201-217">No app configuration is required.</span></span>

### <a name="iis-launch-profile"></a><span data-ttu-id="3f201-218">Profil de lancement IIS</span><span class="sxs-lookup"><span data-stu-id="3f201-218">IIS launch profile</span></span>

<span data-ttu-id="3f201-219">Créez un profil de lancement pour ajouter la prise en charge d’IIS pendant le développement :</span><span class="sxs-lookup"><span data-stu-id="3f201-219">Create a new launch profile to add development-time IIS support:</span></span>

1. <span data-ttu-id="3f201-220">Cliquez avec le bouton droit sur le projet dans **l’Explorateur de solutions**.</span><span class="sxs-lookup"><span data-stu-id="3f201-220">Right-click the project in **Solution Explorer**.</span></span> <span data-ttu-id="3f201-221">Sélectionner **Propriétés**.</span><span class="sxs-lookup"><span data-stu-id="3f201-221">Select **Properties**.</span></span> <span data-ttu-id="3f201-222">Ouvrez l’onglet **Déboguer** .</span><span class="sxs-lookup"><span data-stu-id="3f201-222">Open the **Debug** tab.</span></span>
1. <span data-ttu-id="3f201-223">Pour **Profil**, sélectionnez le bouton **Nouveau**.</span><span class="sxs-lookup"><span data-stu-id="3f201-223">For **Profile**, select the **New** button.</span></span> <span data-ttu-id="3f201-224">Nommez le profil « IIS » dans la fenêtre contextuelle.</span><span class="sxs-lookup"><span data-stu-id="3f201-224">Name the profile "IIS" in the popup window.</span></span> <span data-ttu-id="3f201-225">Sélectionnez **OK** pour créer le profil.</span><span class="sxs-lookup"><span data-stu-id="3f201-225">Select **OK** to create the profile.</span></span>
1. <span data-ttu-id="3f201-226">Pour le paramètre **Lancer**, sélectionnez **IIS** dans la liste.</span><span class="sxs-lookup"><span data-stu-id="3f201-226">For the **Launch** setting, select **IIS** from the list.</span></span>
1. <span data-ttu-id="3f201-227">Cochez la case **Lancer le navigateur** et indiquez l’URL du point de terminaison.</span><span class="sxs-lookup"><span data-stu-id="3f201-227">Select the check box for **Launch browser** and provide the endpoint URL.</span></span>

   <span data-ttu-id="3f201-228">Lorsque le protocole HTTPS est requis par l’application, utilisez un point de terminaison HTTPS (`https://`).</span><span class="sxs-lookup"><span data-stu-id="3f201-228">When the app requires HTTPS, use an HTTPS endpoint (`https://`).</span></span> <span data-ttu-id="3f201-229">Pour HTTP, utilisez un point de terminaison HTTP (`http://`).</span><span class="sxs-lookup"><span data-stu-id="3f201-229">For HTTP, use an HTTP (`http://`) endpoint.</span></span>

   <span data-ttu-id="3f201-230">Indiquez le nom d’hôte et le port utilisés par la [configuration IIS spécifiée précédemment](#configure-iis), en général `localhost`.</span><span class="sxs-lookup"><span data-stu-id="3f201-230">Provide the same host name and port as the [IIS configuration specified earlier uses](#configure-iis), typically `localhost`.</span></span>

   <span data-ttu-id="3f201-231">Indiquez le nom de l’application à la fin de l’URL.</span><span class="sxs-lookup"><span data-stu-id="3f201-231">Provide the name of the app at the end of the URL.</span></span>

   <span data-ttu-id="3f201-232">Par exemple, `https://localhost/WebApplication1` (HTTPS) ou `http://localhost/WebApplication1` (HTTP) sont des URL de point de terminaison valide.</span><span class="sxs-lookup"><span data-stu-id="3f201-232">For example, `https://localhost/WebApplication1` (HTTPS) or `http://localhost/WebApplication1` (HTTP) are valid endpoint URLs.</span></span>
1. <span data-ttu-id="3f201-233">Dans la section **Variables d’environnement**, sélectionnez le bouton **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="3f201-233">In the **Environment variables** section, select the **Add** button.</span></span> <span data-ttu-id="3f201-234">Indiquez une variable d’environnement ayant pour **Nom**`ASPNETCORE_ENVIRONMENT` et pour **Valeur**`Development`.</span><span class="sxs-lookup"><span data-stu-id="3f201-234">Provide an environment variable with a **Name** of `ASPNETCORE_ENVIRONMENT` and a **Value** of `Development`.</span></span>
1. <span data-ttu-id="3f201-235">Dans la zone **Paramètres de serveur web**, donnez à **URL de l’application** la valeur utilisée pour l’URL du point de terminaison **Lancer le navigateur**.</span><span class="sxs-lookup"><span data-stu-id="3f201-235">In the **Web Server Settings** area, set the **App URL** to the same value used for the **Launch browser** endpoint URL.</span></span>
1. <span data-ttu-id="3f201-236">Pour le paramètre **Modèle d’hébergement** dans Visual Studio 2019 (ou version ultérieure), sélectionnez **Par défaut** afin d’utiliser le modèle d’hébergement du projet.</span><span class="sxs-lookup"><span data-stu-id="3f201-236">For the **Hosting Model** setting in Visual Studio 2019 or later, select **Default** to use the hosting model used by the project.</span></span> <span data-ttu-id="3f201-237">C’est la valeur de la propriété `<AspNetCoreHostingModel>` (`InProcess` ou `OutOfProcess`) qui est employée si elle est définie par le projet dans son fichier de projet.</span><span class="sxs-lookup"><span data-stu-id="3f201-237">If the project sets the `<AspNetCoreHostingModel>` property in its project file, the value of the property (`InProcess` or `OutOfProcess`) is used.</span></span> <span data-ttu-id="3f201-238">En l’absence de cette propriété, le modèle d’hébergement par défaut de l’application est utilisé hors processus.</span><span class="sxs-lookup"><span data-stu-id="3f201-238">If the property isn't present, the default hosting model of the app is used, which is out-of-process.</span></span> <span data-ttu-id="3f201-239">Si l’application réclame un paramètre de modèle d’hébergement explicite autre que son modèle normal, définissez le **Modèle d’hébergement** sur `In Process` ou `Out Of Process` en fonction des besoins.</span><span class="sxs-lookup"><span data-stu-id="3f201-239">If the app requires an explicit hosting model setting different from the app's normal hosting model, set the **Hosting Model** to either `In Process` or `Out Of Process` as needed.</span></span>
1. <span data-ttu-id="3f201-240">Enregistrez le profil.</span><span class="sxs-lookup"><span data-stu-id="3f201-240">Save the profile.</span></span>

<span data-ttu-id="3f201-241">Si vous n’utilisez pas Visual Studio, ajoutez manuellement un profil de lancement au fichier [launchSettings.json](https://json.schemastore.org/launchsettings) dans le dossier *Propriétés*.</span><span class="sxs-lookup"><span data-stu-id="3f201-241">When not using Visual Studio, manually add a launch profile to the [launchSettings.json](https://json.schemastore.org/launchsettings) file in the *Properties* folder.</span></span> <span data-ttu-id="3f201-242">L’exemple suivant configure le profil de façon à utiliser le protocole HTTPS :</span><span class="sxs-lookup"><span data-stu-id="3f201-242">The following example configures the profile to use the HTTPS protocol:</span></span>

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iis": {
      "applicationUrl": "https://localhost/WebApplication1",
      "sslPort": 0
    }
  },
  "profiles": {
    "IIS": {
      "commandName": "IIS",
      "launchBrowser": true,
      "launchUrl": "https://localhost/WebApplication1",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    }
  }
}
```

<span data-ttu-id="3f201-243">Vérifiez que les points de terminaison `applicationUrl` et `launchUrl` coïncident et utilisent le même protocole que la configuration de liaison IIS, c’est-à-dire HTTP ou HTTPS.</span><span class="sxs-lookup"><span data-stu-id="3f201-243">Confirm that the `applicationUrl` and `launchUrl` endpoints match and use the same protocol as the IIS binding configuration, either HTTP or HTTPS.</span></span>

## <a name="run-the-project"></a><span data-ttu-id="3f201-244">Exécuter le projet</span><span class="sxs-lookup"><span data-stu-id="3f201-244">Run the project</span></span>

<span data-ttu-id="3f201-245">Exécutez Visual Studio en tant qu’administrateur :</span><span class="sxs-lookup"><span data-stu-id="3f201-245">Run Visual Studio as an administrator:</span></span>

* <span data-ttu-id="3f201-246">Vérifiez que la liste déroulante des configurations de build est définie sur **Déboguer**.</span><span class="sxs-lookup"><span data-stu-id="3f201-246">Confirm that the build configuration drop-down list is set to **Debug**.</span></span>
* <span data-ttu-id="3f201-247">Définissez le [bouton Démarrer le débogage](/visualstudio/debugger/debugger-feature-tour) sur le profil **IIS** , puis sélectionnez le bouton pour démarrer l’application.</span><span class="sxs-lookup"><span data-stu-id="3f201-247">Set the [Start Debugging button](/visualstudio/debugger/debugger-feature-tour) to the **IIS** profile and select the button to start the app.</span></span>

<span data-ttu-id="3f201-248">Visual Studio peut demander un redémarrage si vous ne l’exécutez pas en tant qu’administrateur.</span><span class="sxs-lookup"><span data-stu-id="3f201-248">Visual Studio may prompt a restart if not running as an administrator.</span></span> <span data-ttu-id="3f201-249">Si vous y êtes invité, redémarrez Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="3f201-249">If prompted, restart Visual Studio.</span></span>

<span data-ttu-id="3f201-250">Si vous utilisez un certificat de développement non approuvé, le navigateur peut vous amener à créer une exception pour ce certificat.</span><span class="sxs-lookup"><span data-stu-id="3f201-250">If an untrusted development certificate is used, the browser may require you to create an exception for the untrusted certificate.</span></span>

> [!NOTE]
> <span data-ttu-id="3f201-251">Le débogage d’une configuration de build de mise en production avec [Uniquement mon code](/visualstudio/debugger/just-my-code) et des optimisations du compilateur entraîne une baisse des résultats.</span><span class="sxs-lookup"><span data-stu-id="3f201-251">Debugging a Release build configuration with [Just My Code](/visualstudio/debugger/just-my-code) and compiler optimizations results in a degraded experience.</span></span> <span data-ttu-id="3f201-252">Par exemple, les points d’arrêt ne sont pas atteints.</span><span class="sxs-lookup"><span data-stu-id="3f201-252">For example, break points aren't hit.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3f201-253">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="3f201-253">Additional resources</span></span>

* [<span data-ttu-id="3f201-254">Bien démarrer avec le Gestionnaire IIS dans IIS</span><span class="sxs-lookup"><span data-stu-id="3f201-254">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* <xref:security/enforcing-ssl>

::: moniker-end
