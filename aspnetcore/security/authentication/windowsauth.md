---
title: Configurer l’authentification Windows dans ASP.NET Core
author: scottaddie
description: Découvrez comment configurer l’authentification Windows dans ASP.NET Core pour IIS et HTTP.sys.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 02/26/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/windowsauth
ms.openlocfilehash: 8f6dc8620df04bcebe996119869ca2e498cffccc
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/26/2020
ms.locfileid: "87330680"
---
# <a name="configure-windows-authentication-in-aspnet-core"></a><span data-ttu-id="18509-103">Configurer l’authentification Windows dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="18509-103">Configure Windows Authentication in ASP.NET Core</span></span>

<span data-ttu-id="18509-104">Par [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="18509-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="18509-105">L’authentification Windows (également appelée négociation, Kerberos ou authentification NTLM) peut être configurée pour ASP.NET Core les applications hébergées avec [IIS](xref:host-and-deploy/iis/index), [Kestrel](xref:fundamentals/servers/kestrel)ou [HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="18509-105">Windows Authentication (also known as Negotiate, Kerberos, or NTLM authentication) can be configured for ASP.NET Core apps hosted with [IIS](xref:host-and-deploy/iis/index), [Kestrel](xref:fundamentals/servers/kestrel), or [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="18509-106">L’authentification Windows (également appelée négociation, Kerberos ou authentification NTLM) peut être configurée pour les applications ASP.NET Core hébergées avec [IIS](xref:host-and-deploy/iis/index) ou [HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="18509-106">Windows Authentication (also known as Negotiate, Kerberos, or NTLM authentication) can be configured for ASP.NET Core apps hosted with [IIS](xref:host-and-deploy/iis/index) or [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

::: moniker-end

<span data-ttu-id="18509-107">L’authentification Windows s’appuie sur le système d’exploitation pour authentifier les utilisateurs des applications ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="18509-107">Windows Authentication relies on the operating system to authenticate users of ASP.NET Core apps.</span></span> <span data-ttu-id="18509-108">Vous pouvez utiliser l’authentification Windows lorsque votre serveur s’exécute sur un réseau d’entreprise à l’aide de Active Directory identités de domaine ou des comptes Windows pour identifier les utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="18509-108">You can use Windows Authentication when your server runs on a corporate network using Active Directory domain identities or Windows accounts to identify users.</span></span> <span data-ttu-id="18509-109">L’authentification Windows est mieux adaptée aux environnements intranet où les utilisateurs, les applications clientes et les serveurs Web appartiennent au même domaine Windows.</span><span class="sxs-lookup"><span data-stu-id="18509-109">Windows Authentication is best suited to intranet environments where users, client apps, and web servers belong to the same Windows domain.</span></span>

> [!NOTE]
> <span data-ttu-id="18509-110">L’authentification Windows n’est pas prise en charge avec HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="18509-110">Windows Authentication isn't supported with HTTP/2.</span></span> <span data-ttu-id="18509-111">Les défis liés à l’authentification peuvent être envoyés sur les réponses HTTP/2, mais le client doit passer à la version HTTP/1.1 avant l’authentification.</span><span class="sxs-lookup"><span data-stu-id="18509-111">Authentication challenges can be sent on HTTP/2 responses, but the client must downgrade to HTTP/1.1 before authenticating.</span></span>

## <a name="proxy-and-load-balancer-scenarios"></a><span data-ttu-id="18509-112">Scénarios de proxy et d’équilibrage de charge</span><span class="sxs-lookup"><span data-stu-id="18509-112">Proxy and load balancer scenarios</span></span>

<span data-ttu-id="18509-113">L’authentification Windows est un scénario avec état principalement utilisé dans un intranet, où un proxy ou un équilibreur de charge ne gère généralement pas le trafic entre les clients et les serveurs.</span><span class="sxs-lookup"><span data-stu-id="18509-113">Windows Authentication is a stateful scenario primarily used in an intranet, where a proxy or load balancer doesn't usually handle traffic between clients and servers.</span></span> <span data-ttu-id="18509-114">Si un proxy ou un équilibreur de charge est utilisé, l’authentification Windows fonctionne uniquement si le proxy ou l’équilibreur de charge :</span><span class="sxs-lookup"><span data-stu-id="18509-114">If a proxy or load balancer is used, Windows Authentication only works if the proxy or load balancer:</span></span>

* <span data-ttu-id="18509-115">Gère l’authentification.</span><span class="sxs-lookup"><span data-stu-id="18509-115">Handles the authentication.</span></span>
* <span data-ttu-id="18509-116">Transmet les informations d’authentification de l’utilisateur à l’application (par exemple, dans un en-tête de demande), qui agit sur les informations d’authentification.</span><span class="sxs-lookup"><span data-stu-id="18509-116">Passes the user authentication information to the app (for example, in a request header), which acts on the authentication information.</span></span>

<span data-ttu-id="18509-117">Une alternative à l’authentification Windows dans les environnements où les proxies et les équilibreurs de charge sont utilisés est Active Directory Services fédérés (ADFS) avec OpenID Connect (OIDC).</span><span class="sxs-lookup"><span data-stu-id="18509-117">An alternative to Windows Authentication in environments where proxies and load balancers are used is Active Directory Federated Services (ADFS) with OpenID Connect (OIDC).</span></span>

## <a name="iisiis-express"></a><span data-ttu-id="18509-118">IIS/IIS Express</span><span class="sxs-lookup"><span data-stu-id="18509-118">IIS/IIS Express</span></span>

<span data-ttu-id="18509-119">Ajoutez des services d’authentification en appelant <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> ( <xref:Microsoft.AspNetCore.Server.IISIntegration?displayProperty=fullName> espace de noms) dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="18509-119">Add authentication services by invoking <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> (<xref:Microsoft.AspNetCore.Server.IISIntegration?displayProperty=fullName> namespace) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddAuthentication(IISDefaults.AuthenticationScheme);
```

### <a name="launch-settings-debugger"></a><span data-ttu-id="18509-120">Paramètres de lancement (débogueur)</span><span class="sxs-lookup"><span data-stu-id="18509-120">Launch settings (debugger)</span></span>

<span data-ttu-id="18509-121">La configuration des paramètres de lancement affecte uniquement les *Propriétés/launchSettings.js* du fichier pour IIS Express et ne configure pas IIS pour l’authentification Windows.</span><span class="sxs-lookup"><span data-stu-id="18509-121">Configuration for launch settings only affects the *Properties/launchSettings.json* file for IIS Express and doesn't configure IIS for Windows Authentication.</span></span> <span data-ttu-id="18509-122">La configuration du serveur est expliquée dans la section [IIS](#iis) .</span><span class="sxs-lookup"><span data-stu-id="18509-122">Server configuration is explained in the [IIS](#iis) section.</span></span>

<span data-ttu-id="18509-123">Le modèle d' **application Web** disponible via Visual Studio ou le CLI .net Core peut être configuré pour prendre en charge l’authentification Windows, ce qui met automatiquement à jour les *Propriétés/launchSettings.jssur* le fichier.</span><span class="sxs-lookup"><span data-stu-id="18509-123">The **Web Application** template available via Visual Studio or the .NET Core CLI can be configured to support Windows Authentication, which updates the *Properties/launchSettings.json* file automatically.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="18509-124">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="18509-124">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="18509-125">**Nouveau projet**</span><span class="sxs-lookup"><span data-stu-id="18509-125">**New project**</span></span>

1. <span data-ttu-id="18509-126">Créez un projet.</span><span class="sxs-lookup"><span data-stu-id="18509-126">Create a new project.</span></span>
1. <span data-ttu-id="18509-127">Sélectionnez **Application web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="18509-127">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="18509-128">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="18509-128">Select **Next**.</span></span>
1. <span data-ttu-id="18509-129">Indiquez un nom dans le champ **nom du projet** .</span><span class="sxs-lookup"><span data-stu-id="18509-129">Provide a name in the **Project name** field.</span></span> <span data-ttu-id="18509-130">Confirmez que l’entrée d' **emplacement** est correcte ou indiquez un emplacement pour le projet.</span><span class="sxs-lookup"><span data-stu-id="18509-130">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="18509-131">Sélectionnez **Créer**.</span><span class="sxs-lookup"><span data-stu-id="18509-131">Select **Create**.</span></span>
1. <span data-ttu-id="18509-132">Sélectionnez **modifier** sous **authentification**.</span><span class="sxs-lookup"><span data-stu-id="18509-132">Select **Change** under **Authentication**.</span></span>
1. <span data-ttu-id="18509-133">Dans la fenêtre **modifier l’authentification** , sélectionnez **authentification Windows**.</span><span class="sxs-lookup"><span data-stu-id="18509-133">In the **Change Authentication** window, select **Windows Authentication**.</span></span> <span data-ttu-id="18509-134">Sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="18509-134">Select **OK**.</span></span>
1. <span data-ttu-id="18509-135">Sélectionnez **application Web**.</span><span class="sxs-lookup"><span data-stu-id="18509-135">Select **Web Application**.</span></span>
1. <span data-ttu-id="18509-136">Sélectionnez **Créer**.</span><span class="sxs-lookup"><span data-stu-id="18509-136">Select **Create**.</span></span>

<span data-ttu-id="18509-137">Exécutez l'application.</span><span class="sxs-lookup"><span data-stu-id="18509-137">Run the app.</span></span> <span data-ttu-id="18509-138">Le nom d’utilisateur s’affiche dans l’interface utilisateur de l’application rendue.</span><span class="sxs-lookup"><span data-stu-id="18509-138">The username appears in the rendered app's user interface.</span></span>

<span data-ttu-id="18509-139">**Projet existant**</span><span class="sxs-lookup"><span data-stu-id="18509-139">**Existing project**</span></span>

<span data-ttu-id="18509-140">Les propriétés du projet activent l’authentification Windows et désactivent l’authentification anonyme :</span><span class="sxs-lookup"><span data-stu-id="18509-140">The project's properties enable Windows Authentication and disable Anonymous Authentication:</span></span>

1. <span data-ttu-id="18509-141">Dans **Explorateur de solutions** , cliquez avec le bouton droit sur le projet, puis sélectionnez **Propriétés**.</span><span class="sxs-lookup"><span data-stu-id="18509-141">Right-click the project in **Solution Explorer** and select **Properties**.</span></span>
1. <span data-ttu-id="18509-142">Sélectionnez l’onglet **Débogage**.</span><span class="sxs-lookup"><span data-stu-id="18509-142">Select the **Debug** tab.</span></span>
1. <span data-ttu-id="18509-143">Désactivez la case à cocher **activer l’authentification anonyme**.</span><span class="sxs-lookup"><span data-stu-id="18509-143">Clear the check box for **Enable Anonymous Authentication**.</span></span>
1. <span data-ttu-id="18509-144">Activez la case à cocher **activer l’authentification Windows**.</span><span class="sxs-lookup"><span data-stu-id="18509-144">Select the check box for **Enable Windows Authentication**.</span></span>
1. <span data-ttu-id="18509-145">Enregistrez et fermez la page de propriétés.</span><span class="sxs-lookup"><span data-stu-id="18509-145">Save and close the property page.</span></span>

<span data-ttu-id="18509-146">Les propriétés peuvent également être configurées dans le `iisSettings` nœud de l' *launchSettings.jssur* le fichier :</span><span class="sxs-lookup"><span data-stu-id="18509-146">Alternatively, the properties can be configured in the `iisSettings` node of the *launchSettings.json* file:</span></span>

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

# <a name="net-core-cli"></a>[<span data-ttu-id="18509-147">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="18509-147">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="18509-148">**Nouveau projet**</span><span class="sxs-lookup"><span data-stu-id="18509-148">**New project**</span></span>

<span data-ttu-id="18509-149">Exécutez la commande [dotnet New](/dotnet/core/tools/dotnet-new) avec l' `webapp` argument (ASP.net Core application Web) et le `--auth Windows` commutateur :</span><span class="sxs-lookup"><span data-stu-id="18509-149">Execute the [dotnet new](/dotnet/core/tools/dotnet-new) command with the `webapp` argument (ASP.NET Core Web App) and `--auth Windows` switch:</span></span>

```dotnetcli
dotnet new webapp --auth Windows
```

<span data-ttu-id="18509-150">**Projet existant**</span><span class="sxs-lookup"><span data-stu-id="18509-150">**Existing project**</span></span>

<span data-ttu-id="18509-151">Mettez à jour le `iisSettings` nœud de l' *launchSettings.jssur* le fichier :</span><span class="sxs-lookup"><span data-stu-id="18509-151">Update the `iisSettings` node of the *launchSettings.json* file:</span></span>

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

---

<span data-ttu-id="18509-152">Lors de la modification d’un projet existant, vérifiez que le fichier projet contient une référence de package pour le package [Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) **ou** le package NuGet [Microsoft. AspNetCore. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) .</span><span class="sxs-lookup"><span data-stu-id="18509-152">When modifying an existing project, confirm that the project file includes a package reference for the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) **or** the [Microsoft.AspNetCore.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) NuGet package.</span></span>

### <a name="iis"></a><span data-ttu-id="18509-153">IIS</span><span class="sxs-lookup"><span data-stu-id="18509-153">IIS</span></span>

<span data-ttu-id="18509-154">IIS utilise le [Module ASP.net Core](xref:host-and-deploy/aspnet-core-module) pour héberger des applications ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="18509-154">IIS uses the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to host ASP.NET Core apps.</span></span> <span data-ttu-id="18509-155">L’authentification Windows est configurée pour IIS par le biais du fichier *web.config* .</span><span class="sxs-lookup"><span data-stu-id="18509-155">Windows Authentication is configured for IIS via the *web.config* file.</span></span> <span data-ttu-id="18509-156">Les sections suivantes décrivent diverses opérations :</span><span class="sxs-lookup"><span data-stu-id="18509-156">The following sections show how to:</span></span>

* <span data-ttu-id="18509-157">Fournissez un fichier de *web.config* local qui active l’authentification Windows sur le serveur lorsque l’application est déployée.</span><span class="sxs-lookup"><span data-stu-id="18509-157">Provide a local *web.config* file that activates Windows Authentication on the server when the app is deployed.</span></span>
* <span data-ttu-id="18509-158">Utilisez le gestionnaire des services Internet pour configurer le fichier *web.config* d’une application ASP.net core qui a déjà été déployée sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="18509-158">Use the IIS Manager to configure the *web.config* file of an ASP.NET Core app that has already been deployed to the server.</span></span>

<span data-ttu-id="18509-159">Si vous ne l’avez pas déjà fait, activez IIS pour héberger des applications ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="18509-159">If you haven't already done so, enable IIS to host ASP.NET Core apps.</span></span> <span data-ttu-id="18509-160">Pour plus d’informations, consultez <xref:host-and-deploy/iis/index>.</span><span class="sxs-lookup"><span data-stu-id="18509-160">For more information, see <xref:host-and-deploy/iis/index>.</span></span>

<span data-ttu-id="18509-161">Activez le service de rôle IIS pour l’authentification Windows.</span><span class="sxs-lookup"><span data-stu-id="18509-161">Enable the IIS Role Service for Windows Authentication.</span></span> <span data-ttu-id="18509-162">Pour plus d’informations, consultez [activer l’authentification Windows dans les services de rôle IIS (Voir l’étape 2)](xref:host-and-deploy/iis/index#iis-configuration).</span><span class="sxs-lookup"><span data-stu-id="18509-162">For more information, see [Enable Windows Authentication in IIS Role Services (see Step 2)](xref:host-and-deploy/iis/index#iis-configuration).</span></span>

<span data-ttu-id="18509-163">L' [intergiciel (middleware) d’intégration IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) est configuré pour authentifier automatiquement les demandes par défaut.</span><span class="sxs-lookup"><span data-stu-id="18509-163">[IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) is configured to automatically authenticate requests by default.</span></span> <span data-ttu-id="18509-164">Pour plus d’informations, voir [Host ASP.net Core on Windows with IIS : IIS options (AutomaticAuthentication) (](xref:host-and-deploy/iis/index#iis-options)en anglais).</span><span class="sxs-lookup"><span data-stu-id="18509-164">For more information, see [Host ASP.NET Core on Windows with IIS: IIS options (AutomaticAuthentication)](xref:host-and-deploy/iis/index#iis-options).</span></span>

<span data-ttu-id="18509-165">Le module ASP.NET Core est configuré pour transférer le jeton d’authentification Windows à l’application par défaut.</span><span class="sxs-lookup"><span data-stu-id="18509-165">The ASP.NET Core Module is configured to forward the Windows Authentication token to the app by default.</span></span> <span data-ttu-id="18509-166">Pour plus d’informations, consultez [ASP.net Core référence de configuration du module : attributs de l’élément aspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span><span class="sxs-lookup"><span data-stu-id="18509-166">For more information, see [ASP.NET Core Module configuration reference: Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

<span data-ttu-id="18509-167">Utilisez l' **une** des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="18509-167">Use **either** of the following approaches:</span></span>

* <span data-ttu-id="18509-168">**Avant de publier et de déployer le projet,** ajoutez le fichier *web.config* suivant à la racine du projet :</span><span class="sxs-lookup"><span data-stu-id="18509-168">**Before publishing and deploying the project,** add the following *web.config* file to the project root:</span></span>

  [!code-xml[](windowsauth/sample_snapshot/web_2.config)]

  <span data-ttu-id="18509-169">Lorsque le projet est publié par le kit SDK .NET Core (sans la `<IsTransformWebConfigDisabled>` propriété définie à `true` dans le fichier projet), le fichier *web.config* publié comprend la `<location><system.webServer><security><authentication>` section.</span><span class="sxs-lookup"><span data-stu-id="18509-169">When the project is published by the .NET Core SDK (without the `<IsTransformWebConfigDisabled>` property set to `true` in the project file), the published *web.config* file includes the `<location><system.webServer><security><authentication>` section.</span></span> <span data-ttu-id="18509-170">Pour plus d’informations sur la `<IsTransformWebConfigDisabled>` propriété, consultez <xref:host-and-deploy/iis/index#webconfig-file> .</span><span class="sxs-lookup"><span data-stu-id="18509-170">For more information on the `<IsTransformWebConfigDisabled>` property, see <xref:host-and-deploy/iis/index#webconfig-file>.</span></span>

* <span data-ttu-id="18509-171">**Après la publication et le déploiement du projet, effectuez une** configuration côté serveur à l’aide du gestionnaire des services Internet :</span><span class="sxs-lookup"><span data-stu-id="18509-171">**After publishing and deploying the project,** perform server-side configuration with the IIS Manager:</span></span>

  1. <span data-ttu-id="18509-172">Dans le gestionnaire des services Internet, sélectionnez le site IIS sous le nœud **sites** de la barre latérale **connexions** .</span><span class="sxs-lookup"><span data-stu-id="18509-172">In IIS Manager, select the IIS site under the **Sites** node of the **Connections** sidebar.</span></span>
  1. <span data-ttu-id="18509-173">Double-cliquez sur **authentification** dans la zone **IIS** .</span><span class="sxs-lookup"><span data-stu-id="18509-173">Double-click **Authentication** in the **IIS** area.</span></span>
  1. <span data-ttu-id="18509-174">Sélectionnez **authentification anonyme**.</span><span class="sxs-lookup"><span data-stu-id="18509-174">Select **Anonymous Authentication**.</span></span> <span data-ttu-id="18509-175">Sélectionnez **Désactiver** dans la barre latérale **actions** .</span><span class="sxs-lookup"><span data-stu-id="18509-175">Select **Disable** in the **Actions** sidebar.</span></span>
  1. <span data-ttu-id="18509-176">Sélectionnez **Authentification Windows**.</span><span class="sxs-lookup"><span data-stu-id="18509-176">Select **Windows Authentication**.</span></span> <span data-ttu-id="18509-177">Sélectionnez **activer** dans la barre latérale **actions** .</span><span class="sxs-lookup"><span data-stu-id="18509-177">Select **Enable** in the **Actions** sidebar.</span></span>

  <span data-ttu-id="18509-178">Lorsque ces actions sont effectuées, le gestionnaire des services Internet modifie le fichier *web.config* de l’application.</span><span class="sxs-lookup"><span data-stu-id="18509-178">When these actions are taken, IIS Manager modifies the app's *web.config* file.</span></span> <span data-ttu-id="18509-179">Un `<system.webServer><security><authentication>` nœud est ajouté avec les paramètres mis à jour pour `anonymousAuthentication` et `windowsAuthentication` :</span><span class="sxs-lookup"><span data-stu-id="18509-179">A `<system.webServer><security><authentication>` node is added with updated settings for `anonymousAuthentication` and `windowsAuthentication`:</span></span>

  [!code-xml[](windowsauth/sample_snapshot/web_1.config?highlight=4-5)]

  <span data-ttu-id="18509-180">La `<system.webServer>` section ajoutée au fichier *web.config* par le gestionnaire des services Internet est en dehors de la section de l’application `<location>` ajoutée par le kit SDK .net Core lors de la publication de l’application.</span><span class="sxs-lookup"><span data-stu-id="18509-180">The `<system.webServer>` section added to the *web.config* file by IIS Manager is outside of the app's `<location>` section added by the .NET Core SDK when the app is published.</span></span> <span data-ttu-id="18509-181">Étant donné que la section est ajoutée en dehors du `<location>` nœud, les paramètres sont hérités par toutes les [sous-applications](xref:host-and-deploy/iis/index#sub-applications) de l’application actuelle.</span><span class="sxs-lookup"><span data-stu-id="18509-181">Because the section is added outside of the `<location>` node, the settings are inherited by any [sub-apps](xref:host-and-deploy/iis/index#sub-applications) to the current app.</span></span> <span data-ttu-id="18509-182">Pour empêcher l’héritage, déplacez la `<security>` section ajoutée à l’intérieur de la `<location><system.webServer>` section que le kit SDK .net Core fourni.</span><span class="sxs-lookup"><span data-stu-id="18509-182">To prevent inheritance, move the added `<security>` section inside of the `<location><system.webServer>` section that the .NET Core SDK provided.</span></span>

  <span data-ttu-id="18509-183">Quand le gestionnaire des services Internet est utilisé pour ajouter la configuration IIS, il n’affecte que le fichier *web.config* de l’application sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="18509-183">When IIS Manager is used to add the IIS configuration, it only affects the app's *web.config* file on the server.</span></span> <span data-ttu-id="18509-184">Un déploiement ultérieur de l’application peut remplacer les paramètres sur le serveur si la copie du serveur de *web.config* est remplacée par le fichier *web.config* du projet.</span><span class="sxs-lookup"><span data-stu-id="18509-184">A subsequent deployment of the app may overwrite the settings on the server if the server's copy of *web.config* is replaced by the project's *web.config* file.</span></span> <span data-ttu-id="18509-185">Utilisez l' **une** des approches suivantes pour gérer les paramètres :</span><span class="sxs-lookup"><span data-stu-id="18509-185">Use **either** of the following approaches to manage the settings:</span></span>

  * <span data-ttu-id="18509-186">Utilisez le gestionnaire des services Internet pour réinitialiser les paramètres dans le fichier *web.config* une fois que le fichier a été remplacé lors du déploiement.</span><span class="sxs-lookup"><span data-stu-id="18509-186">Use IIS Manager to reset the settings in the *web.config* file after the file is overwritten on deployment.</span></span>
  * <span data-ttu-id="18509-187">Ajoutez un *fichierweb.config* à l’application localement avec les paramètres.</span><span class="sxs-lookup"><span data-stu-id="18509-187">Add a *web.config file* to the app locally with the settings.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="kestrel"></a><span data-ttu-id="18509-188">Kestrel</span><span class="sxs-lookup"><span data-stu-id="18509-188">Kestrel</span></span>

<span data-ttu-id="18509-189">Le package NuGet [Microsoft. AspNetCore. Authentication. Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) peut être utilisé avec [Kestrel](xref:fundamentals/servers/kestrel) pour prendre en charge l’authentification Windows à l’aide de Negotiate et Kerberos sur Windows, Linux et MacOS.</span><span class="sxs-lookup"><span data-stu-id="18509-189">The [Microsoft.AspNetCore.Authentication.Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) NuGet package can be used with [Kestrel](xref:fundamentals/servers/kestrel) to support Windows Authentication using Negotiate and Kerberos on Windows, Linux, and macOS.</span></span>

> [!WARNING]
> <span data-ttu-id="18509-190">Les informations d’identification peuvent être rendues persistantes entre les demandes sur une connexion.</span><span class="sxs-lookup"><span data-stu-id="18509-190">Credentials can be persisted across requests on a connection.</span></span> <span data-ttu-id="18509-191">*L’authentification par négociation ne doit pas être utilisée avec les proxies, sauf si le proxy gère une affinité de connexion 1:1 (une connexion permanente) avec Kestrel.*</span><span class="sxs-lookup"><span data-stu-id="18509-191">*Negotiate authentication must not be used with proxies unless the proxy maintains a 1:1 connection affinity (a persistent connection) with Kestrel.*</span></span>

> [!NOTE]
> <span data-ttu-id="18509-192">Le gestionnaire Negotiate détecte si le serveur sous-jacent prend en charge l’authentification Windows en mode natif et s’il est activé.</span><span class="sxs-lookup"><span data-stu-id="18509-192">The Negotiate handler detects if the underlying server supports Windows Authentication natively and if it's enabled.</span></span> <span data-ttu-id="18509-193">Si le serveur prend en charge l’authentification Windows, mais qu’elle est désactivée, une erreur est générée pour vous demander d’activer l’implémentation du serveur.</span><span class="sxs-lookup"><span data-stu-id="18509-193">If the server supports Windows Authentication but it's disabled, an error is thrown asking you to enable the server implementation.</span></span> <span data-ttu-id="18509-194">Lorsque l’authentification Windows est activée sur le serveur, le gestionnaire Negotiate le transmet en toute transparence.</span><span class="sxs-lookup"><span data-stu-id="18509-194">When Windows Authentication is enabled in the server, the Negotiate handler transparently forwards to it.</span></span>

<span data-ttu-id="18509-195">Ajoutez des services d’authentification en appelant <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> et <xref:Microsoft.Extensions.DependencyInjection.NegotiateExtensions.AddNegotiate*> dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="18509-195">Add authentication services by invoking <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.NegotiateExtensions.AddNegotiate*> in `Startup.ConfigureServices`:</span></span>

 ```csharp
// using Microsoft.AspNetCore.Authentication.Negotiate;
// using Microsoft.Extensions.DependencyInjection;

services.AddAuthentication(NegotiateDefaults.AuthenticationScheme)
    .AddNegotiate();
```

<span data-ttu-id="18509-196">Ajoutez l’intergiciel (middleware) d’authentification en appelant <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> dans `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="18509-196">Add Authentication Middleware by calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> in `Startup.Configure`:</span></span>

 ```csharp
app.UseAuthentication();
```

<span data-ttu-id="18509-197">Pour plus d’informations sur les intergiciels (middleware), consultez <xref:fundamentals/middleware/index> .</span><span class="sxs-lookup"><span data-stu-id="18509-197">For more information on middleware, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="18509-198">Les demandes anonymes sont autorisées.</span><span class="sxs-lookup"><span data-stu-id="18509-198">Anonymous requests are allowed.</span></span> <span data-ttu-id="18509-199">Utilisez [ASP.net Core autorisation](xref:security/authorization/introduction) pour défier les demandes anonymes pour l’authentification.</span><span class="sxs-lookup"><span data-stu-id="18509-199">Use [ASP.NET Core Authorization](xref:security/authorization/introduction) to challenge anonymous requests for authentication.</span></span>

### <a name="windows-environment-configuration"></a><span data-ttu-id="18509-200">Configuration de l’environnement Windows</span><span class="sxs-lookup"><span data-stu-id="18509-200">Windows environment configuration</span></span>

<span data-ttu-id="18509-201">Le composant [Microsoft. AspNetCore. Authentication. Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) effectue l’authentification en mode utilisateur.</span><span class="sxs-lookup"><span data-stu-id="18509-201">The [Microsoft.AspNetCore.Authentication.Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) component performs User Mode authentication.</span></span> <span data-ttu-id="18509-202">Les noms de principal du service (SPN) doivent être ajoutés au compte d’utilisateur qui exécute le service, et non au compte d’ordinateur.</span><span class="sxs-lookup"><span data-stu-id="18509-202">Service Principal Names (SPNs) must be added to the user account running the service, not the machine account.</span></span> <span data-ttu-id="18509-203">Exécutez `setspn -S HTTP/myservername.mydomain.com myuser` dans un interpréteur de commandes d’administration.</span><span class="sxs-lookup"><span data-stu-id="18509-203">Execute `setspn -S HTTP/myservername.mydomain.com myuser` in an administrative command shell.</span></span>

### <a name="linux-and-macos-environment-configuration"></a><span data-ttu-id="18509-204">Configuration de l’environnement Linux et macOS</span><span class="sxs-lookup"><span data-stu-id="18509-204">Linux and macOS environment configuration</span></span>

<span data-ttu-id="18509-205">Les instructions pour joindre un ordinateur Linux ou macOS à un domaine Windows sont disponibles dans l’article [se connecter Azure Data Studio à votre SQL Server à l’aide de l’authentification Windows-Kerberos](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller) .</span><span class="sxs-lookup"><span data-stu-id="18509-205">Instructions for joining a Linux or macOS machine to a Windows domain are available in the [Connect Azure Data Studio to your SQL Server using Windows authentication - Kerberos](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller) article.</span></span> <span data-ttu-id="18509-206">Les instructions créent un compte d’ordinateur pour la machine Linux sur le domaine.</span><span class="sxs-lookup"><span data-stu-id="18509-206">The instructions create a machine account for the Linux machine on the domain.</span></span> <span data-ttu-id="18509-207">Les noms de principal du service doivent être ajoutés à ce compte d’ordinateur.</span><span class="sxs-lookup"><span data-stu-id="18509-207">SPNs must be added to that machine account.</span></span>

> [!NOTE]
> <span data-ttu-id="18509-208">Lorsque vous suivez les instructions de l’article [se connecter Azure Data Studio à votre SQL Server à l’aide de l’authentification Windows-Kerberos](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller) , remplacez `python-software-properties` par `python3-software-properties` si nécessaire.</span><span class="sxs-lookup"><span data-stu-id="18509-208">When following the guidance in the [Connect Azure Data Studio to your SQL Server using Windows authentication - Kerberos](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller) article, replace `python-software-properties` with `python3-software-properties` if needed.</span></span>

<span data-ttu-id="18509-209">Une fois que l’ordinateur Linux ou macOS est joint au domaine, des étapes supplémentaires sont nécessaires pour fournir un [fichier keytab](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) avec les noms de principal du service (SPN) :</span><span class="sxs-lookup"><span data-stu-id="18509-209">Once the Linux or macOS machine is joined to the domain, additional steps are required to provide a [keytab file](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) with the SPNs:</span></span>

* <span data-ttu-id="18509-210">Sur le contrôleur de domaine, ajoutez de nouveaux noms de principal du service Web au compte d’ordinateur :</span><span class="sxs-lookup"><span data-stu-id="18509-210">On the domain controller, add new web service SPNs to the machine account:</span></span>
  * `setspn -S HTTP/mywebservice.mydomain.com mymachine`
  * `setspn -S HTTP/mywebservice@MYDOMAIN.COM mymachine`
* <span data-ttu-id="18509-211">Utilisez [Ktpass](/windows-server/administration/windows-commands/ktpass) pour générer un fichier keytab :</span><span class="sxs-lookup"><span data-stu-id="18509-211">Use [ktpass](/windows-server/administration/windows-commands/ktpass) to generate a keytab file:</span></span>
  * `ktpass -princ HTTP/mywebservice.mydomain.com@MYDOMAIN.COM -pass myKeyTabFilePassword -mapuser MYDOMAIN\mymachine$ -pType KRB5_NT_PRINCIPAL -out c:\temp\mymachine.HTTP.keytab -crypto AES256-SHA1`
  * <span data-ttu-id="18509-212">Certains champs doivent être spécifiés en majuscules, comme indiqué.</span><span class="sxs-lookup"><span data-stu-id="18509-212">Some fields must be specified in uppercase as indicated.</span></span>
* <span data-ttu-id="18509-213">Copiez le fichier keytab sur l’ordinateur Linux ou macOS.</span><span class="sxs-lookup"><span data-stu-id="18509-213">Copy the keytab file to the Linux or macOS machine.</span></span>
* <span data-ttu-id="18509-214">Sélectionnez le fichier keytab à l’aide d’une variable d’environnement :`export KRB5_KTNAME=/tmp/mymachine.HTTP.keytab`</span><span class="sxs-lookup"><span data-stu-id="18509-214">Select the keytab file via an environment variable: `export KRB5_KTNAME=/tmp/mymachine.HTTP.keytab`</span></span>
* <span data-ttu-id="18509-215">Appelez `klist` pour afficher les noms de principal du service actuellement disponibles.</span><span class="sxs-lookup"><span data-stu-id="18509-215">Invoke `klist` to show the SPNs currently available for use.</span></span>

> [!NOTE]
> <span data-ttu-id="18509-216">Un fichier keytab contient les informations d’identification d’accès au domaine et doit être protégé en conséquence.</span><span class="sxs-lookup"><span data-stu-id="18509-216">A keytab file contains domain access credentials and must be protected accordingly.</span></span>

::: moniker-end

## <a name="httpsys"></a><span data-ttu-id="18509-217">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="18509-217">HTTP.sys</span></span>

<span data-ttu-id="18509-218">[HTTP.sys](xref:fundamentals/servers/httpsys) prend en charge l’authentification Windows en mode noyau en utilisant Negotiate, NTLM ou l’authentification de base.</span><span class="sxs-lookup"><span data-stu-id="18509-218">[HTTP.sys](xref:fundamentals/servers/httpsys) supports Kernel Mode Windows Authentication using Negotiate, NTLM, or Basic authentication.</span></span>

<span data-ttu-id="18509-219">Ajoutez des services d’authentification en appelant <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> ( <xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> espace de noms) dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="18509-219">Add authentication services by invoking <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> (<xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> namespace) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
```

<span data-ttu-id="18509-220">Configurez l’hôte Web de l’application pour utiliser HTTP.sys avec l’authentification Windows (*Program.cs*).</span><span class="sxs-lookup"><span data-stu-id="18509-220">Configure the app's web host to use HTTP.sys with Windows Authentication (*Program.cs*).</span></span> <span data-ttu-id="18509-221"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*>se trouve dans l' <xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> espace de noms.</span><span class="sxs-lookup"><span data-stu-id="18509-221"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> is in the <xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> namespace.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](windowsauth/sample_snapshot/Program_GenericHost.cs?highlight=13-19)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](windowsauth/sample_snapshot/Program_WebHost.cs?highlight=9-15)]

::: moniker-end

> [!NOTE]
> <span data-ttu-id="18509-222">HTTP.sys délègue l’authentification en mode noyau avec le protocole d’authentification Kerberos.</span><span class="sxs-lookup"><span data-stu-id="18509-222">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="18509-223">L’authentification en mode utilisateur n’est pas prise en charge avec Kerberos et HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="18509-223">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="18509-224">Le compte d’ordinateur doit être utilisé pour déchiffrer le ticket/jeton Kerberos obtenu à partir d’Active Directory et transféré par le client au serveur afin d’authentifier l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="18509-224">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="18509-225">Inscrivez le nom de principal du service (SPN) pour l’hôte, et non l’utilisateur de l’application.</span><span class="sxs-lookup"><span data-stu-id="18509-225">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

> [!NOTE]
> <span data-ttu-id="18509-226">HTTP.sys n’est pas pris en charge sur nano Server version 1709 ou ultérieure.</span><span class="sxs-lookup"><span data-stu-id="18509-226">HTTP.sys isn't supported on Nano Server version 1709 or later.</span></span> <span data-ttu-id="18509-227">Pour utiliser l’authentification Windows et HTTP.sys avec Nano Server, utilisez un [conteneur Server Core (Microsoft/windowsservercore)](https://hub.docker.com/r/microsoft/windowsservercore/).</span><span class="sxs-lookup"><span data-stu-id="18509-227">To use Windows Authentication and HTTP.sys with Nano Server, use a [Server Core (microsoft/windowsservercore) container](https://hub.docker.com/r/microsoft/windowsservercore/).</span></span> <span data-ttu-id="18509-228">Pour plus d’informations sur Server Core, voir [qu’est-ce que l’option d’installation Server Core dans Windows Server ?](/windows-server/administration/server-core/what-is-server-core).</span><span class="sxs-lookup"><span data-stu-id="18509-228">For more information on Server Core, see [What is the Server Core installation option in Windows Server?](/windows-server/administration/server-core/what-is-server-core).</span></span>

## <a name="authorize-users"></a><span data-ttu-id="18509-229">Autoriser les utilisateurs</span><span class="sxs-lookup"><span data-stu-id="18509-229">Authorize users</span></span>

<span data-ttu-id="18509-230">L’état de configuration de l’accès anonyme détermine la façon dont `[Authorize]` les `[AllowAnonymous]` attributs et sont utilisés dans l’application.</span><span class="sxs-lookup"><span data-stu-id="18509-230">The configuration state of anonymous access determines the way in which the `[Authorize]` and `[AllowAnonymous]` attributes are used in the app.</span></span> <span data-ttu-id="18509-231">Les deux sections suivantes expliquent comment gérer les États de configuration non autorisés et autorisés de l’accès anonyme.</span><span class="sxs-lookup"><span data-stu-id="18509-231">The following two sections explain how to handle the disallowed and allowed configuration states of anonymous access.</span></span>

### <a name="disallow-anonymous-access"></a><span data-ttu-id="18509-232">Interdire l’accès anonyme</span><span class="sxs-lookup"><span data-stu-id="18509-232">Disallow anonymous access</span></span>

<span data-ttu-id="18509-233">Lorsque l’authentification Windows est activée et que l’accès anonyme est désactivé, les `[Authorize]` attributs et n' `[AllowAnonymous]` ont aucun effet.</span><span class="sxs-lookup"><span data-stu-id="18509-233">When Windows Authentication is enabled and anonymous access is disabled, the `[Authorize]` and `[AllowAnonymous]` attributes have no effect.</span></span> <span data-ttu-id="18509-234">Si un site IIS est configuré pour interdire l’accès anonyme, la demande n’atteint jamais l’application.</span><span class="sxs-lookup"><span data-stu-id="18509-234">If an IIS site is configured to disallow anonymous access, the request never reaches the app.</span></span> <span data-ttu-id="18509-235">Pour cette raison, l' `[AllowAnonymous]` attribut n’est pas applicable.</span><span class="sxs-lookup"><span data-stu-id="18509-235">For this reason, the `[AllowAnonymous]` attribute isn't applicable.</span></span>

### <a name="allow-anonymous-access"></a><span data-ttu-id="18509-236">Autoriser l’accès anonyme</span><span class="sxs-lookup"><span data-stu-id="18509-236">Allow anonymous access</span></span>

<span data-ttu-id="18509-237">Lorsque l’authentification Windows et l’accès anonyme sont activés, utilisez les `[Authorize]` `[AllowAnonymous]` attributs et.</span><span class="sxs-lookup"><span data-stu-id="18509-237">When both Windows Authentication and anonymous access are enabled, use the `[Authorize]` and `[AllowAnonymous]` attributes.</span></span> <span data-ttu-id="18509-238">L' `[Authorize]` attribut vous permet de sécuriser les points de terminaison de l’application qui requièrent une authentification.</span><span class="sxs-lookup"><span data-stu-id="18509-238">The `[Authorize]` attribute allows you to secure endpoints of the app which require authentication.</span></span> <span data-ttu-id="18509-239">L' `[AllowAnonymous]` attribut remplace l' `[Authorize]` attribut dans les applications qui autorisent l’accès anonyme.</span><span class="sxs-lookup"><span data-stu-id="18509-239">The `[AllowAnonymous]` attribute overrides the `[Authorize]` attribute in apps that allow anonymous access.</span></span> <span data-ttu-id="18509-240">Pour plus d’informations sur l’utilisation des attributs, consultez <xref:security/authorization/simple> .</span><span class="sxs-lookup"><span data-stu-id="18509-240">For attribute usage details, see <xref:security/authorization/simple>.</span></span>

> [!NOTE]
> <span data-ttu-id="18509-241">Par défaut, les utilisateurs qui n’ont pas l’autorisation d’accéder à une page s’affichent avec une réponse HTTP 403 vide.</span><span class="sxs-lookup"><span data-stu-id="18509-241">By default, users who lack authorization to access a page are presented with an empty HTTP 403 response.</span></span> <span data-ttu-id="18509-242">L' [intergiciel StatusCodePages](xref:fundamentals/error-handling#usestatuscodepages) peut être configuré pour fournir aux utilisateurs une expérience de « accès refusé » améliorée.</span><span class="sxs-lookup"><span data-stu-id="18509-242">The [StatusCodePages Middleware](xref:fundamentals/error-handling#usestatuscodepages) can be configured to provide users with a better "Access Denied" experience.</span></span>

## <a name="impersonation"></a><span data-ttu-id="18509-243">Emprunt d'identité</span><span class="sxs-lookup"><span data-stu-id="18509-243">Impersonation</span></span>

<span data-ttu-id="18509-244">ASP.NET Core n’implémente pas l’emprunt d’identité.</span><span class="sxs-lookup"><span data-stu-id="18509-244">ASP.NET Core doesn't implement impersonation.</span></span> <span data-ttu-id="18509-245">Les applications s’exécutent avec l’identité de l’application pour toutes les demandes, à l’aide du pool d’applications ou de l’identité du processus.</span><span class="sxs-lookup"><span data-stu-id="18509-245">Apps run with the app's identity for all requests, using app pool or process identity.</span></span> <span data-ttu-id="18509-246">Si l’application doit effectuer une action pour le compte d’un utilisateur, utilisez [WindowsIdentity. RunImpersonated](xref:System.Security.Principal.WindowsIdentity.RunImpersonated*) dans un intergiciel (middleware) en [ligne de terminal](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) dans `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="18509-246">If the app should perform an action on behalf of a user, use [WindowsIdentity.RunImpersonated](xref:System.Security.Principal.WindowsIdentity.RunImpersonated*) in a [terminal inline middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) in `Startup.Configure`.</span></span> <span data-ttu-id="18509-247">Exécutez une action unique dans ce contexte, puis fermez le contexte.</span><span class="sxs-lookup"><span data-stu-id="18509-247">Run a single action in this context and then close the context.</span></span>

[!code-csharp[](windowsauth/sample_snapshot/Startup.cs?highlight=10-19)]

<span data-ttu-id="18509-248">`RunImpersonated`ne prend pas en charge les opérations asynchrones et ne doit pas être utilisé pour les scénarios complexes.</span><span class="sxs-lookup"><span data-stu-id="18509-248">`RunImpersonated` doesn't support asynchronous operations and shouldn't be used for complex scenarios.</span></span> <span data-ttu-id="18509-249">Par exemple, l’encapsulage de demandes entières ou de chaînes middleware n’est pas pris en charge ou recommandé.</span><span class="sxs-lookup"><span data-stu-id="18509-249">For example, wrapping entire requests or middleware chains isn't supported or recommended.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="18509-250">Tandis que le package [Microsoft. AspNetCore. Authentication. Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) permet l’authentification sur Windows, Linux et MacOS, l’emprunt d’identité est pris en charge uniquement sur Windows.</span><span class="sxs-lookup"><span data-stu-id="18509-250">While the [Microsoft.AspNetCore.Authentication.Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) package enables authentication on Windows, Linux, and macOS, impersonation is only supported on Windows.</span></span>

::: moniker-end

## <a name="claims-transformations"></a><span data-ttu-id="18509-251">Transformations de revendication</span><span class="sxs-lookup"><span data-stu-id="18509-251">Claims transformations</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="18509-252">Lors de l’hébergement avec IIS, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> n’est pas appelé en interne pour initialiser un utilisateur.</span><span class="sxs-lookup"><span data-stu-id="18509-252">When hosting with IIS, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="18509-253">Par conséquent, une implémentation de <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> utilisée pour transformer les revendications après chaque authentification n’est pas activée par défaut.</span><span class="sxs-lookup"><span data-stu-id="18509-253">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="18509-254">Pour plus d’informations et pour obtenir un exemple de code qui active les transformations de revendications, consultez <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model> .</span><span class="sxs-lookup"><span data-stu-id="18509-254">For more information and a code example that activates claims transformations, see <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="18509-255">Lors de l’hébergement avec IIS en mode in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> n’est pas appelé en interne pour initialiser un utilisateur.</span><span class="sxs-lookup"><span data-stu-id="18509-255">When hosting with IIS in-process mode, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="18509-256">Par conséquent, une implémentation de <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> utilisée pour transformer les revendications après chaque authentification n’est pas activée par défaut.</span><span class="sxs-lookup"><span data-stu-id="18509-256">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="18509-257">Pour plus d’informations et pour obtenir un exemple de code qui active les transformations de revendications lors de l’hébergement in-process, consultez <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model> .</span><span class="sxs-lookup"><span data-stu-id="18509-257">For more information and a code example that activates claims transformations when hosting in-process, see <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model>.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="18509-258">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="18509-258">Additional resources</span></span>

* [<span data-ttu-id="18509-259">dotnet publish</span><span class="sxs-lookup"><span data-stu-id="18509-259">dotnet publish</span></span>](/dotnet/core/tools/dotnet-publish)
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/visual-studio-publish-profiles>
