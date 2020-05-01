---
title: Héberger et déployer Blazor ASP.net Core webassembly
author: guardrex
description: Découvrez comment héberger et déployer une Blazor application à l’aide de ASP.net Core, de réseaux de distribution de contenu (CDN), de serveurs de fichiers et de pages github.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/30/2020
no-loc:
- Blazor
- SignalR
uid: host-and-deploy/blazor/webassembly
ms.openlocfilehash: 2472fd499128a8807b76a3cc031d466140e180f5
ms.sourcegitcommit: 23243f6d6a3100303802e4310b0634860cc0b268
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82619367"
---
# <a name="host-and-deploy-aspnet-core-blazor-webassembly"></a><span data-ttu-id="ea518-103">Héberger et déployer ASP.NET Core éblouissant webassembly</span><span class="sxs-lookup"><span data-stu-id="ea518-103">Host and deploy ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="ea518-104">Par [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams)et [safia Abdalla](https://safia.rocks)</span><span class="sxs-lookup"><span data-stu-id="ea518-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams), and [Safia Abdalla](https://safia.rocks)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="ea518-105">Avec le [modèle d’hébergement de Webassembly éblouissant](xref:blazor/hosting-models#blazor-webassembly):</span><span class="sxs-lookup"><span data-stu-id="ea518-105">With the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly):</span></span>

* <span data-ttu-id="ea518-106">L’application éblouissant, ses dépendances et le Runtime .NET sont téléchargés dans le navigateur en parallèle.</span><span class="sxs-lookup"><span data-stu-id="ea518-106">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser in parallel.</span></span>
* <span data-ttu-id="ea518-107">L’application est exécutée directement sur le thread d’interface utilisateur du navigateur.</span><span class="sxs-lookup"><span data-stu-id="ea518-107">The app is executed directly on the browser UI thread.</span></span>

<span data-ttu-id="ea518-108">Les stratégies de déploiement suivantes sont prises en charge :</span><span class="sxs-lookup"><span data-stu-id="ea518-108">The following deployment strategies are supported:</span></span>

* <span data-ttu-id="ea518-109">L’application Blazor est fournie par une application ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ea518-109">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="ea518-110">Cette stratégie est abordée dans la section [Déploiement hébergé avec ASP.NET Core](#hosted-deployment-with-aspnet-core).</span><span class="sxs-lookup"><span data-stu-id="ea518-110">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>
* <span data-ttu-id="ea518-111">L’application Blazor est placée sur un service ou un serveur web d’hébergement statique, où .NET n’est pas utilisé pour servir l’application Blazor.</span><span class="sxs-lookup"><span data-stu-id="ea518-111">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span> <span data-ttu-id="ea518-112">Cette stratégie est traitée dans la section [Déploiement autonome](#standalone-deployment) , qui comprend des informations sur l’hébergement d’une application de webassembly éblouissante en tant que sous-application IIS.</span><span class="sxs-lookup"><span data-stu-id="ea518-112">This strategy is covered in the [Standalone deployment](#standalone-deployment) section, which includes information on hosting a Blazor WebAssembly app as an IIS sub-app.</span></span>

## <a name="brotli-precompression"></a><span data-ttu-id="ea518-113">Précompression Brotli</span><span class="sxs-lookup"><span data-stu-id="ea518-113">Brotli precompression</span></span>

<span data-ttu-id="ea518-114">Quand une application de webassembly éblouissant est publiée, la sortie est précompressée à l’aide de l' [algorithme de compression Brotli](https://tools.ietf.org/html/rfc7932) au niveau le plus élevé afin de réduire la taille de l’application et de la nécessité de compresser le Runtime.</span><span class="sxs-lookup"><span data-stu-id="ea518-114">When a Blazor WebAssembly app is published, the output is precompressed using the [Brotli compression algorithm](https://tools.ietf.org/html/rfc7932) at the highest level to reduce the app size and remove the need for runtime compression.</span></span>

<span data-ttu-id="ea518-115">Pour la configuration de la compression IIS *Web. config* , consultez la section [IIS : Brotli et compression gzip](#brotli-and-gzip-compression) .</span><span class="sxs-lookup"><span data-stu-id="ea518-115">For IIS *web.config* compression configuration, see the [IIS: Brotli and Gzip compression](#brotli-and-gzip-compression) section.</span></span>

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="ea518-116">Réécriture d’URL pour un routage correct</span><span class="sxs-lookup"><span data-stu-id="ea518-116">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="ea518-117">Le routage des requêtes pour les composants de page dans une application de webassembly éblouissante n’est pas aussi simple que le routage des demandes dans un serveur éblouissant, une application hébergée.</span><span class="sxs-lookup"><span data-stu-id="ea518-117">Routing requests for page components in a Blazor WebAssembly app isn't as straightforward as routing requests in a Blazor Server, hosted app.</span></span> <span data-ttu-id="ea518-118">Prenons l’exemple d’une application de webassembly éblouissant avec deux composants :</span><span class="sxs-lookup"><span data-stu-id="ea518-118">Consider a Blazor WebAssembly app with two components:</span></span>

* <span data-ttu-id="ea518-119">*Main.razor* &ndash; Se charge à la racine de l’application et contient un lien vers le composant `About` (`href="About"`).</span><span class="sxs-lookup"><span data-stu-id="ea518-119">*Main.razor* &ndash; Loads at the root of the app and contains a link to the `About` component (`href="About"`).</span></span>
* <span data-ttu-id="ea518-120">Composant *About.Razor* &ndash; `About`.</span><span class="sxs-lookup"><span data-stu-id="ea518-120">*About.razor* &ndash; `About` component.</span></span>

<span data-ttu-id="ea518-121">Quand le document par défaut de l’application est demandé à l’aide de la barre d’adresses du navigateur (par exemple, `https://www.contoso.com/`) :</span><span class="sxs-lookup"><span data-stu-id="ea518-121">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="ea518-122">Le navigateur effectue une requête.</span><span class="sxs-lookup"><span data-stu-id="ea518-122">The browser makes a request.</span></span>
1. <span data-ttu-id="ea518-123">La page par défaut est retournée, généralement *index.html*.</span><span class="sxs-lookup"><span data-stu-id="ea518-123">The default page is returned, which is usually *index.html*.</span></span>
1. <span data-ttu-id="ea518-124">*index.HTML* amorce l’application.</span><span class="sxs-lookup"><span data-stu-id="ea518-124">*index.html* bootstraps the app.</span></span>
1. <span data-ttu-id="ea518-125">Le routeur de Blazor se charge et le composant `Main` Razor est rendu.</span><span class="sxs-lookup"><span data-stu-id="ea518-125">Blazor's router loads, and the Razor `Main` component is rendered.</span></span>

<span data-ttu-id="ea518-126">Dans la page principale, la sélection du composant `About` fonctionne sur le client, car le routeur Blazor empêche le navigateur d’effectuer une requête sur Internet à `www.contoso.com` pour `About` et fournit lui-même le composant `About` rendu.</span><span class="sxs-lookup"><span data-stu-id="ea518-126">In the Main page, selecting the link to the `About` component works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the rendered `About` component itself.</span></span> <span data-ttu-id="ea518-127">Toutes les requêtes pour les points de terminaison internes de *l’application* Web de l’outil éblouissant fonctionnent de la même façon : les demandes ne déclenchent pas de requêtes basées sur un navigateur vers des ressources hébergées sur le serveur sur Internet.</span><span class="sxs-lookup"><span data-stu-id="ea518-127">All of the requests for internal endpoints *within the Blazor WebAssembly app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="ea518-128">Le routeur gère les requêtes en interne.</span><span class="sxs-lookup"><span data-stu-id="ea518-128">The router handles the requests internally.</span></span>

<span data-ttu-id="ea518-129">Si une requête pour `www.contoso.com/About` est effectuée à l’aide de la barre d’adresses du navigateur, elle échoue.</span><span class="sxs-lookup"><span data-stu-id="ea518-129">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="ea518-130">Comme cette ressource n’existe pas sur l’hôte Internet de l’application, une réponse *404 – Non trouvé* est retournée.</span><span class="sxs-lookup"><span data-stu-id="ea518-130">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="ea518-131">Étant donné que les navigateurs envoient des requêtes aux hôtes basés sur Internet pour des pages côté client, les serveurs web et les services d’hébergement doivent réécrire toutes les requêtes pour les ressources qui ne se trouvent pas physiquement sur le serveur afin qu’elles pointent vers la page *index.html*.</span><span class="sxs-lookup"><span data-stu-id="ea518-131">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the *index.html* page.</span></span> <span data-ttu-id="ea518-132">Quand *index. html* est retourné, le routeur éblouissant de l’application prend le relais et répond avec la ressource correcte.</span><span class="sxs-lookup"><span data-stu-id="ea518-132">When *index.html* is returned, the app's Blazor router takes over and responds with the correct resource.</span></span>

<span data-ttu-id="ea518-133">Lors du déploiement sur un serveur IIS, vous pouvez utiliser le module de réécriture d’URL avec le fichier *Web. config* publié de l’application.</span><span class="sxs-lookup"><span data-stu-id="ea518-133">When deploying to an IIS server, you can use the URL Rewrite Module with the app's published *web.config* file.</span></span> <span data-ttu-id="ea518-134">Pour plus d’informations, consultez la section [IIS](#iis) .</span><span class="sxs-lookup"><span data-stu-id="ea518-134">For more information, see the [IIS](#iis) section.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="ea518-135">Déploiement hébergé avec ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ea518-135">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="ea518-136">Un *déploiement hébergé* sert l’application de webassembly éblouissant à des navigateurs à partir d’une [application ASP.net Core](xref:index) qui s’exécute sur un serveur Web.</span><span class="sxs-lookup"><span data-stu-id="ea518-136">A *hosted deployment* serves the Blazor WebAssembly app to browsers from an [ASP.NET Core app](xref:index) that runs on a web server.</span></span>

<span data-ttu-id="ea518-137">L’application client éblouissant webassembly est publiée dans le dossier */bin/Release/{Target Framework}/Publish/wwwroot* de l’application serveur, ainsi que les autres ressources Web statiques de l’application serveur.</span><span class="sxs-lookup"><span data-stu-id="ea518-137">The client Blazor WebAssembly app is published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="ea518-138">Les deux applications sont déployées ensemble.</span><span class="sxs-lookup"><span data-stu-id="ea518-138">The two apps are deployed together.</span></span> <span data-ttu-id="ea518-139">Un serveur web capable d’héberger une application ASP.NET Core est nécessaire.</span><span class="sxs-lookup"><span data-stu-id="ea518-139">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="ea518-140">Pour un déploiement hébergé, Visual Studio comprend le modèle de projet **application Webassembly éblouissant** (`blazorwasm` modèle lors de l’utilisation de la commande [dotnet New](/dotnet/core/tools/dotnet-new) ) avec l'`-ho|--hosted` option **hébergée** sélectionnée (lors de l’utilisation de la `dotnet new` commande).</span><span class="sxs-lookup"><span data-stu-id="ea518-140">For a hosted deployment, Visual Studio includes the **Blazor WebAssembly App** project template (`blazorwasm` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command) with the **Hosted** option selected (`-ho|--hosted` when using the `dotnet new` command).</span></span>

<span data-ttu-id="ea518-141">Pour plus d’informations sur l’hébergement et le déploiement d’applications ASP.NET Core, consultez <xref:host-and-deploy/index>.</span><span class="sxs-lookup"><span data-stu-id="ea518-141">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="ea518-142">Pour plus d’informations concernant le déploiement sur Azure App Service, consultez <xref:tutorials/publish-to-azure-webapp-using-vs>.</span><span class="sxs-lookup"><span data-stu-id="ea518-142">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

## <a name="standalone-deployment"></a><span data-ttu-id="ea518-143">Déploiement autonome</span><span class="sxs-lookup"><span data-stu-id="ea518-143">Standalone deployment</span></span>

<span data-ttu-id="ea518-144">Un *Déploiement autonome* sert l’application de webassembly éblouissant sous la forme d’un ensemble de fichiers statiques demandés directement par les clients.</span><span class="sxs-lookup"><span data-stu-id="ea518-144">A *standalone deployment* serves the Blazor WebAssembly app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="ea518-145">N’importe quel serveur de fichiers statiques est capable de servir l’application Blazor.</span><span class="sxs-lookup"><span data-stu-id="ea518-145">Any static file server is able to serve the Blazor app.</span></span>

<span data-ttu-id="ea518-146">Les ressources de déploiement autonomes sont publiées dans le dossier */bin/Release/{Target Framework}/Publish/wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="ea518-146">Standalone deployment assets are published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder.</span></span>

### <a name="iis"></a><span data-ttu-id="ea518-147">IIS</span><span class="sxs-lookup"><span data-stu-id="ea518-147">IIS</span></span>

<span data-ttu-id="ea518-148">IIS est un serveur de fichiers statiques compatible avec les applications Blazor.</span><span class="sxs-lookup"><span data-stu-id="ea518-148">IIS is a capable static file server for Blazor apps.</span></span> <span data-ttu-id="ea518-149">Pour configurer IIS afin d’héberger Blazor, consultez [Générer un site web statique sur IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span><span class="sxs-lookup"><span data-stu-id="ea518-149">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="ea518-150">Les ressources publiées sont créées dans le dossier */bin/Release/{FRAMEWORK CIBLE}/publish*.</span><span class="sxs-lookup"><span data-stu-id="ea518-150">Published assets are created in the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span> <span data-ttu-id="ea518-151">Hébergez le contenu du dossier *publish* sur le serveur web ou le service d’hébergement.</span><span class="sxs-lookup"><span data-stu-id="ea518-151">Host the contents of the *publish* folder on the web server or hosting service.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="ea518-152">web.config</span><span class="sxs-lookup"><span data-stu-id="ea518-152">web.config</span></span>

<span data-ttu-id="ea518-153">Quand un projet Blazor est publié, un fichier *web.config* est créé avec la configuration IIS suivante :</span><span class="sxs-lookup"><span data-stu-id="ea518-153">When a Blazor project is published, a *web.config* file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="ea518-154">Les types MIME sont définis pour les extensions de fichiers suivantes :</span><span class="sxs-lookup"><span data-stu-id="ea518-154">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="ea518-155">*. dll* &ndash;`application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="ea518-155">*.dll* &ndash; `application/octet-stream`</span></span>
  * <span data-ttu-id="ea518-156">*. JSON* &ndash;`application/json`</span><span class="sxs-lookup"><span data-stu-id="ea518-156">*.json* &ndash; `application/json`</span></span>
  * <span data-ttu-id="ea518-157">*. WASM* &ndash;`application/wasm`</span><span class="sxs-lookup"><span data-stu-id="ea518-157">*.wasm* &ndash; `application/wasm`</span></span>
  * <span data-ttu-id="ea518-158">*. WOFF* &ndash;`application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="ea518-158">*.woff* &ndash; `application/font-woff`</span></span>
  * <span data-ttu-id="ea518-159">*. woff2* &ndash;`application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="ea518-159">*.woff2* &ndash; `application/font-woff`</span></span>
* <span data-ttu-id="ea518-160">La compression HTTP est activée pour les types MIME suivants :</span><span class="sxs-lookup"><span data-stu-id="ea518-160">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="ea518-161">Des règles du module de réécriture d’URL sont établies :</span><span class="sxs-lookup"><span data-stu-id="ea518-161">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="ea518-162">Servez-vous du sous-répertoire dans lequel résident les ressources statiques de l’application (*wwwroot/{chemin demandé}*).</span><span class="sxs-lookup"><span data-stu-id="ea518-162">Serve the sub-directory where the app's static assets reside (*wwwroot/{PATH REQUESTED}*).</span></span>
  * <span data-ttu-id="ea518-163">Créez le routage de secours SPA afin que les demandes pour les ressources non-fichier soient redirigées vers le document par défaut de l’application dans son dossier des ressources statiques (*wwwroot/index.html*).</span><span class="sxs-lookup"><span data-stu-id="ea518-163">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (*wwwroot/index.html*).</span></span>
  
#### <a name="use-a-custom-webconfig"></a><span data-ttu-id="ea518-164">Utiliser un fichier Web. config personnalisé</span><span class="sxs-lookup"><span data-stu-id="ea518-164">Use a custom web.config</span></span>

<span data-ttu-id="ea518-165">Pour utiliser un fichier *Web. config* personnalisé, placez le fichier *Web. config* personnalisé à la racine du dossier du projet et publiez le projet.</span><span class="sxs-lookup"><span data-stu-id="ea518-165">To use a custom *web.config* file, place the custom *web.config* file at the root of the project folder and publish the project.</span></span>

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="ea518-166">Installer le module de réécriture d’URL</span><span class="sxs-lookup"><span data-stu-id="ea518-166">Install the URL Rewrite Module</span></span>

<span data-ttu-id="ea518-167">Le [module de réécriture d’URL](https://www.iis.net/downloads/microsoft/url-rewrite) est nécessaire pour réécrire les URL.</span><span class="sxs-lookup"><span data-stu-id="ea518-167">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="ea518-168">Il n’est pas installé par défaut, et ne peut pas l’être en tant que fonctionnalité de service de rôle Serveur Web (IIS).</span><span class="sxs-lookup"><span data-stu-id="ea518-168">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span> <span data-ttu-id="ea518-169">Vous devez le télécharger à partir du site web IIS.</span><span class="sxs-lookup"><span data-stu-id="ea518-169">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="ea518-170">Utilisez Web Platform Installer pour installer le module :</span><span class="sxs-lookup"><span data-stu-id="ea518-170">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="ea518-171">Localement, accédez à la [page des téléchargements du Module de réécriture d’URL](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span><span class="sxs-lookup"><span data-stu-id="ea518-171">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="ea518-172">Pour la version anglaise, sélectionnez **WebPI** pour télécharger le programme d’installation WebPI.</span><span class="sxs-lookup"><span data-stu-id="ea518-172">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="ea518-173">Pour les autres langues, sélectionnez l’architecture appropriée pour le serveur (x86/x64) afin de télécharger le programme d’installation.</span><span class="sxs-lookup"><span data-stu-id="ea518-173">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="ea518-174">Copiez le programme d’installation sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="ea518-174">Copy the installer to the server.</span></span> <span data-ttu-id="ea518-175">Exécutez le programme d’installation.</span><span class="sxs-lookup"><span data-stu-id="ea518-175">Run the installer.</span></span> <span data-ttu-id="ea518-176">Sélectionnez le bouton **Installer** et acceptez les termes du contrat de licence.</span><span class="sxs-lookup"><span data-stu-id="ea518-176">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="ea518-177">Il n’est pas nécessaire de redémarrer le serveur après l’installation.</span><span class="sxs-lookup"><span data-stu-id="ea518-177">A server restart isn't required after the install completes.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="ea518-178">Configurer le site web</span><span class="sxs-lookup"><span data-stu-id="ea518-178">Configure the website</span></span>

<span data-ttu-id="ea518-179">Affectez le dossier de l’application comme **chemin physique** du site web.</span><span class="sxs-lookup"><span data-stu-id="ea518-179">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="ea518-180">Le dossier contient les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="ea518-180">The folder contains:</span></span>

* <span data-ttu-id="ea518-181">Le fichier *web.config* utilisé par IIS pour configurer le site web, notamment les règles de redirection nécessaires et les types de contenu de fichiers</span><span class="sxs-lookup"><span data-stu-id="ea518-181">The *web.config* file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="ea518-182">Le dossier de ressources statiques de l’application</span><span class="sxs-lookup"><span data-stu-id="ea518-182">The app's static asset folder.</span></span>

#### <a name="host-as-an-iis-sub-app"></a><span data-ttu-id="ea518-183">Héberger en tant que sous-application IIS</span><span class="sxs-lookup"><span data-stu-id="ea518-183">Host as an IIS sub-app</span></span>

<span data-ttu-id="ea518-184">Si une application autonome est hébergée en tant que sous-application IIS, effectuez l’une des opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="ea518-184">If a standalone app is hosted as an IIS sub-app, perform either of the following:</span></span>

* <span data-ttu-id="ea518-185">Désactivez le gestionnaire de module ASP.NET Core hérité.</span><span class="sxs-lookup"><span data-stu-id="ea518-185">Disable the inherited ASP.NET Core Module handler.</span></span>

  <span data-ttu-id="ea518-186">Supprimez le gestionnaire dans le fichier *Web. config* publié de l’application éblouissant en ajoutant `<handlers>` une section au fichier :</span><span class="sxs-lookup"><span data-stu-id="ea518-186">Remove the handler in the Blazor app's published *web.config* file by adding a `<handlers>` section to the file:</span></span>

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* <span data-ttu-id="ea518-187">Désactivez l’héritage de la section de l' `<system.webServer>` application racine ( `<location>` parent) `inheritInChildApplications` à l' `false`aide d’un élément dont la valeur est :</span><span class="sxs-lookup"><span data-stu-id="ea518-187">Disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <handlers>
          <add name="aspNetCore" ... />
        </handlers>
        <aspNetCore ... />
      </system.webServer>
    </location>
  </configuration>
  ```

<span data-ttu-id="ea518-188">La suppression du gestionnaire ou la désactivation de l’héritage est effectuée en plus de [la configuration du chemin d’accès de base de l’application](xref:host-and-deploy/blazor/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="ea518-188">Removing the handler or disabling inheritance is performed in addition to [configuring the app's base path](xref:host-and-deploy/blazor/index#app-base-path).</span></span> <span data-ttu-id="ea518-189">Dans le fichier *index.html* de l’application, définissez le chemin de base de l’application sur l’alias IIS utilisé lors de la configuration de la sous-application dans IIS.</span><span class="sxs-lookup"><span data-stu-id="ea518-189">Set the app base path in the app's *index.html* file to the IIS alias used when configuring the sub-app in IIS.</span></span>

#### <a name="brotli-and-gzip-compression"></a><span data-ttu-id="ea518-190">Compression Brotli et gzip</span><span class="sxs-lookup"><span data-stu-id="ea518-190">Brotli and Gzip compression</span></span>

<span data-ttu-id="ea518-191">IIS peut être configuré par le biais de *Web. config* pour servir les ressources éblouissantes Brotli ou GZip compressées.</span><span class="sxs-lookup"><span data-stu-id="ea518-191">IIS can be configured via *web.config* to serve Brotli or Gzip compressed Blazor assets.</span></span> <span data-ttu-id="ea518-192">Pour obtenir un exemple de configuration, consultez [Web. config](webassembly/_samples/web.config?raw=true).</span><span class="sxs-lookup"><span data-stu-id="ea518-192">For an example configuration, see [web.config](webassembly/_samples/web.config?raw=true).</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="ea518-193">Dépannage</span><span class="sxs-lookup"><span data-stu-id="ea518-193">Troubleshooting</span></span>

<span data-ttu-id="ea518-194">Si vous recevez un message *500 – Erreur interne du serveur* et que le Gestionnaire IIS lève des erreurs quand vous tentez d’accéder à la configuration du site web, vérifiez que le module de réécriture d’URL est installé.</span><span class="sxs-lookup"><span data-stu-id="ea518-194">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="ea518-195">Quand le module n’est pas installé, le fichier *web.config* ne peut pas être analysé par IIS.</span><span class="sxs-lookup"><span data-stu-id="ea518-195">When the module isn't installed, the *web.config* file can't be parsed by IIS.</span></span> <span data-ttu-id="ea518-196">Cela empêche le Gestionnaire IIS de charger la configuration du site web et empêche le site web de fournir les fichiers statiques Blazor.</span><span class="sxs-lookup"><span data-stu-id="ea518-196">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

<span data-ttu-id="ea518-197">Pour plus d’informations sur le dépannage des déploiements sur IIS, consultez <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="ea518-197">For more information on troubleshooting deployments to IIS, see <xref:test/troubleshoot-azure-iis>.</span></span>

### <a name="azure-storage"></a><span data-ttu-id="ea518-198">Stockage Azure</span><span class="sxs-lookup"><span data-stu-id="ea518-198">Azure Storage</span></span>

<span data-ttu-id="ea518-199">L’hébergement de fichiers statiques [Azure Storage](/azure/storage/) permet l’hébergement d’applications éblouissantes sans serveur.</span><span class="sxs-lookup"><span data-stu-id="ea518-199">[Azure Storage](/azure/storage/) static file hosting allows serverless Blazor app hosting.</span></span> <span data-ttu-id="ea518-200">Les noms de domaine personnalisé, le réseau de distribution de contenu Azure (CDN) et HTTPS sont pris en charge.</span><span class="sxs-lookup"><span data-stu-id="ea518-200">Custom domain names, the Azure Content Delivery Network (CDN), and HTTPS are supported.</span></span>

<span data-ttu-id="ea518-201">Lorsque le service blob est activé pour l’hébergement de site Web statique sur un compte de stockage :</span><span class="sxs-lookup"><span data-stu-id="ea518-201">When the blob service is enabled for static website hosting on a storage account:</span></span>

* <span data-ttu-id="ea518-202">Définissez le **nom du document d’index** sur `index.html`.</span><span class="sxs-lookup"><span data-stu-id="ea518-202">Set the **Index document name** to `index.html`.</span></span>
* <span data-ttu-id="ea518-203">Définissez le **chemin d’accès au document d’erreur** sur `index.html`.</span><span class="sxs-lookup"><span data-stu-id="ea518-203">Set the **Error document path** to `index.html`.</span></span> <span data-ttu-id="ea518-204">Les composants de Razor et autres points de terminaison non-fichier ne se trouvent sur les chemins d’accès physiques dans le contenu statique stocké par le service blob.</span><span class="sxs-lookup"><span data-stu-id="ea518-204">Razor components and other non-file endpoints don't reside at physical paths in the static content stored by the blob service.</span></span> <span data-ttu-id="ea518-205">Lors de la réception d’une requête pour l’une de ces ressources que le routeur Blazor doit gérer, l’erreur *404 - introuvable* générée par le service blob achemine la requête vers le **chemin d’accès au document d’erreur**.</span><span class="sxs-lookup"><span data-stu-id="ea518-205">When a request for one of these resources is received that the Blazor router should handle, the *404 - Not Found* error generated by the blob service routes the request to the **Error document path**.</span></span> <span data-ttu-id="ea518-206">Le blob *index.html* est retourné et le routeur Blazor charge et traite le chemin d’accès.</span><span class="sxs-lookup"><span data-stu-id="ea518-206">The *index.html* blob is returned, and the Blazor router loads and processes the path.</span></span>

<span data-ttu-id="ea518-207">Pour plus d’informations, consultez [Hébergement de sites web statiques dans le service Stockage Azure](/azure/storage/blobs/storage-blob-static-website).</span><span class="sxs-lookup"><span data-stu-id="ea518-207">For more information, see [Static website hosting in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span></span>

### <a name="nginx"></a><span data-ttu-id="ea518-208">Nginx</span><span class="sxs-lookup"><span data-stu-id="ea518-208">Nginx</span></span>

<span data-ttu-id="ea518-209">Le fichier *nginx. conf* suivant est simplifié pour montrer comment configurer Nginx pour envoyer le fichier *index. html* lorsqu’il ne peut pas trouver de fichier correspondant sur le disque.</span><span class="sxs-lookup"><span data-stu-id="ea518-209">The following *nginx.conf* file is simplified to show how to configure Nginx to send the *index.html* file whenever it can't find a corresponding file on disk.</span></span>

```
events { }
http {
    server {
        listen 80;

        location / {
            root /usr/share/nginx/html;
            try_files $uri $uri/ /index.html =404;
        }
    }
}
```

<span data-ttu-id="ea518-210">Pour plus d’informations sur la configuration du serveur web Nginx de production, consultez [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/) (Création de fichiers de configuration NGINX et NGINX Plus).</span><span class="sxs-lookup"><span data-stu-id="ea518-210">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

### <a name="nginx-in-docker"></a><span data-ttu-id="ea518-211">Nginx dans Docker</span><span class="sxs-lookup"><span data-stu-id="ea518-211">Nginx in Docker</span></span>

<span data-ttu-id="ea518-212">Pour héberger Blazor dans Docker à l’aide de Nginx, configurez le fichier Dockerfile pour utiliser l’image Nginx basée sur Alpine.</span><span class="sxs-lookup"><span data-stu-id="ea518-212">To host Blazor in Docker using Nginx, setup the Dockerfile to use the Alpine-based Nginx image.</span></span> <span data-ttu-id="ea518-213">Mettez à jour le fichier Dockerfile pour copier le fichier *nginx.config* dans le conteneur.</span><span class="sxs-lookup"><span data-stu-id="ea518-213">Update the Dockerfile to copy the *nginx.config* file into the container.</span></span>

<span data-ttu-id="ea518-214">Ajoutez une ligne au fichier Dockerfile, comme indiqué dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="ea518-214">Add one line to the Dockerfile, as shown in the following example:</span></span>

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a><span data-ttu-id="ea518-215">Apache</span><span class="sxs-lookup"><span data-stu-id="ea518-215">Apache</span></span>

<span data-ttu-id="ea518-216">Pour déployer une application de webassembly éblouissante sur CentOS 7 ou version ultérieure :</span><span class="sxs-lookup"><span data-stu-id="ea518-216">To deploy a Blazor WebAssembly app to CentOS 7 or later:</span></span>

1. <span data-ttu-id="ea518-217">Créez le fichier de configuration Apache.</span><span class="sxs-lookup"><span data-stu-id="ea518-217">Create the Apache configuration file.</span></span> <span data-ttu-id="ea518-218">L’exemple suivant est un fichier de configuration simplifié (*blazorapp. config*) :</span><span class="sxs-lookup"><span data-stu-id="ea518-218">The following example is a simplified configuration file (*blazorapp.config*):</span></span>

   ```
   <VirtualHost *:80>
       ServerName www.example.com
       ServerAlias *.example.com

       DocumentRoot "/var/www/blazorapp"
       ErrorDocument 404 /index.html

       AddType application/wasm .wasm
       AddType application/octet-stream .dll
   
       <Directory "/var/www/blazorapp">
           Options -Indexes
           AllowOverride None
       </Directory>

       <IfModule mod_deflate.c>
           AddOutputFilterByType DEFLATE text/css
           AddOutputFilterByType DEFLATE application/javascript
           AddOutputFilterByType DEFLATE text/html
           AddOutputFilterByType DEFLATE application/octet-stream
           AddOutputFilterByType DEFLATE application/wasm
           <IfModule mod_setenvif.c>
           BrowserMatch ^Mozilla/4 gzip-only-text/html
           BrowserMatch ^Mozilla/4.0[678] no-gzip
           BrowserMatch bMSIE !no-gzip !gzip-only-text/html
       </IfModule>
       </IfModule>

       ErrorLog /var/log/httpd/blazorapp-error.log
       CustomLog /var/log/httpd/blazorapp-access.log common
   </VirtualHost>
   ```

1. <span data-ttu-id="ea518-219">Placez le fichier de configuration Apache dans `/etc/httpd/conf.d/` le répertoire, qui est le répertoire de configuration Apache par défaut dans CentOS 7.</span><span class="sxs-lookup"><span data-stu-id="ea518-219">Place the Apache configuration file into the `/etc/httpd/conf.d/` directory, which is the default Apache configuration directory in CentOS 7.</span></span>

1. <span data-ttu-id="ea518-220">Placez les fichiers de l’application dans `/var/www/blazorapp` le répertoire (l’emplacement spécifié `DocumentRoot` dans le fichier de configuration).</span><span class="sxs-lookup"><span data-stu-id="ea518-220">Place the app's files into the `/var/www/blazorapp` directory (the location specified to `DocumentRoot` in the configuration file).</span></span>

1. <span data-ttu-id="ea518-221">Redémarrez le service Apache.</span><span class="sxs-lookup"><span data-stu-id="ea518-221">Restart the Apache service.</span></span>

<span data-ttu-id="ea518-222">Pour plus d’informations, consultez [mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) et [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span><span class="sxs-lookup"><span data-stu-id="ea518-222">For more information, see [mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) and [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span></span>

### <a name="github-pages"></a><span data-ttu-id="ea518-223">GitHub Pages</span><span class="sxs-lookup"><span data-stu-id="ea518-223">GitHub Pages</span></span>

<span data-ttu-id="ea518-224">Pour gérer les réécritures d’URL, ajoutez un fichier *404.html* avec un script qui gère la redirection de la requête vers la page *index.html*.</span><span class="sxs-lookup"><span data-stu-id="ea518-224">To handle URL rewrites, add a *404.html* file with a script that handles redirecting the request to the *index.html* page.</span></span> <span data-ttu-id="ea518-225">Pour obtenir un exemple d’implémentation fournie par la communauté, consultez [Single Page Apps for GitHub Pages](https://spa-github-pages.rafrex.com/) (Applications à une seule page pour GitHub Pages) ([rafrex/spa-github-pages sur GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span><span class="sxs-lookup"><span data-stu-id="ea518-225">For an example implementation provided by the community, see [Single Page Apps for GitHub Pages](https://spa-github-pages.rafrex.com/) ([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span></span> <span data-ttu-id="ea518-226">Vous pouvez obtenir un exemple d’utilisation de cette approche à l’adresse [blazor-demo/blazor-demo.github.io sur GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([site actif](https://blazor-demo.github.io/)).</span><span class="sxs-lookup"><span data-stu-id="ea518-226">An example using the community approach can be seen at [blazor-demo/blazor-demo.github.io on GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([live site](https://blazor-demo.github.io/)).</span></span>

<span data-ttu-id="ea518-227">Quand vous utilisez un site de projet plutôt qu’un site d’entreprise, ajoutez ou mettez à jour la balise `<base>` dans *index.html*.</span><span class="sxs-lookup"><span data-stu-id="ea518-227">When using a project site instead of an organization site, add or update the `<base>` tag in *index.html*.</span></span> <span data-ttu-id="ea518-228">Définissez la valeur d’attribut `href` sur le nom du dépôt GitHub avec une barre oblique (par exemple, `my-repository/`.</span><span class="sxs-lookup"><span data-stu-id="ea518-228">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `my-repository/`.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="ea518-229">Valeurs de configuration de l’hôte</span><span class="sxs-lookup"><span data-stu-id="ea518-229">Host configuration values</span></span>

<span data-ttu-id="ea518-230">Les [applications Webassembly éblouissantes](xref:blazor/hosting-models#blazor-webassembly) peuvent accepter les valeurs de configuration d’hôte suivantes comme arguments de ligne de commande au moment de l’exécution dans l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="ea518-230">[Blazor WebAssembly apps](xref:blazor/hosting-models#blazor-webassembly) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="ea518-231">Racine de contenu</span><span class="sxs-lookup"><span data-stu-id="ea518-231">Content root</span></span>

<span data-ttu-id="ea518-232">L' `--contentroot` argument définit le chemin d’accès absolu au répertoire qui contient les fichiers de contenu de l’application ([racine du contenu](xref:fundamentals/index#content-root)).</span><span class="sxs-lookup"><span data-stu-id="ea518-232">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files ([content root](xref:fundamentals/index#content-root)).</span></span> <span data-ttu-id="ea518-233">Dans les exemples suivants, `/content-root-path` est le chemin racine du contenu de l’application.</span><span class="sxs-lookup"><span data-stu-id="ea518-233">In the following examples, `/content-root-path` is the app's content root path.</span></span>

* <span data-ttu-id="ea518-234">Passez l’argument lors de l’exécution de l’application localement à une invite de commandes.</span><span class="sxs-lookup"><span data-stu-id="ea518-234">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="ea518-235">À partir du répertoire de l’application, exécutez :</span><span class="sxs-lookup"><span data-stu-id="ea518-235">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="ea518-236">Ajoutez une entrée au fichier *launchSettings.json* de l’application dans le profil **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="ea518-236">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="ea518-237">Ce paramètre est utilisé en cas d’exécution de l’application avec le débogueur Visual Studio et dans une invite de commandes avec `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="ea518-237">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="ea518-238">Dans Visual Studio, spécifiez l’argument dans **Propriétés** > **Déboguer** > les arguments de l'**application**.</span><span class="sxs-lookup"><span data-stu-id="ea518-238">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="ea518-239">Le fait de définir l’argument dans la page de propriétés Visual Studio l’ajoute au fichier *launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="ea518-239">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="ea518-240">Base du chemin</span><span class="sxs-lookup"><span data-stu-id="ea518-240">Path base</span></span>

<span data-ttu-id="ea518-241">L' `--pathbase` argument définit le chemin d’accès de base d’application pour une application exécutée localement avec un chemin d’URL `<base>` relatif `href` non racine (la balise est `/` définie sur un chemin d’accès autre que pour la mise en lots et la production).</span><span class="sxs-lookup"><span data-stu-id="ea518-241">The `--pathbase` argument sets the app base path for an app run locally with a non-root relative URL path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="ea518-242">Dans les exemples suivants, `/relative-URL-path` est la base du chemin de l’application.</span><span class="sxs-lookup"><span data-stu-id="ea518-242">In the following examples, `/relative-URL-path` is the app's path base.</span></span> <span data-ttu-id="ea518-243">Pour plus d’informations, consultez chemin de la base de l' [application](xref:host-and-deploy/blazor/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="ea518-243">For more information, see [App base path](xref:host-and-deploy/blazor/index#app-base-path).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="ea518-244">Contrairement au chemin fourni au `href` de la balise `<base>`, n’incluez pas de barre oblique (`/`) quand vous passez la valeur d’argument `--pathbase`.</span><span class="sxs-lookup"><span data-stu-id="ea518-244">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="ea518-245">Si vous spécifiez `<base href="/CoolApp/">` (inclut une barre oblique) comme chemin de base de l’application dans la balise `<base>`, passez `--pathbase=/CoolApp` (aucune barre oblique de fin) comme valeur d’argument de ligne de commande.</span><span class="sxs-lookup"><span data-stu-id="ea518-245">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="ea518-246">Passez l’argument lors de l’exécution de l’application localement à une invite de commandes.</span><span class="sxs-lookup"><span data-stu-id="ea518-246">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="ea518-247">À partir du répertoire de l’application, exécutez :</span><span class="sxs-lookup"><span data-stu-id="ea518-247">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* <span data-ttu-id="ea518-248">Ajoutez une entrée au fichier *launchSettings.json* de l’application dans le profil **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="ea518-248">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="ea518-249">Ce paramètre est utilisé en cas d’exécution de l’application avec le débogueur Visual Studio et dans une invite de commandes avec `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="ea518-249">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* <span data-ttu-id="ea518-250">Dans Visual Studio, spécifiez l’argument dans **Propriétés** > **Déboguer** > les arguments de l'**application**.</span><span class="sxs-lookup"><span data-stu-id="ea518-250">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="ea518-251">Le fait de définir l’argument dans la page de propriétés Visual Studio l’ajoute au fichier *launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="ea518-251">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a><span data-ttu-id="ea518-252">URLs</span><span class="sxs-lookup"><span data-stu-id="ea518-252">URLs</span></span>

<span data-ttu-id="ea518-253">L’argument `--urls` définit les adresses IP ou les adresses d’hôtes avec les ports et protocoles sur lesquels il faut écouter les demandes.</span><span class="sxs-lookup"><span data-stu-id="ea518-253">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="ea518-254">Passez l’argument lors de l’exécution de l’application localement à une invite de commandes.</span><span class="sxs-lookup"><span data-stu-id="ea518-254">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="ea518-255">À partir du répertoire de l’application, exécutez :</span><span class="sxs-lookup"><span data-stu-id="ea518-255">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="ea518-256">Ajoutez une entrée au fichier *launchSettings.json* de l’application dans le profil **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="ea518-256">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="ea518-257">Ce paramètre est utilisé en cas d’exécution de l’application avec le débogueur Visual Studio et dans une invite de commandes avec `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="ea518-257">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="ea518-258">Dans Visual Studio, spécifiez l’argument dans **Propriétés** > **Déboguer** > les arguments de l'**application**.</span><span class="sxs-lookup"><span data-stu-id="ea518-258">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="ea518-259">Le fait de définir l’argument dans la page de propriétés Visual Studio l’ajoute au fichier *launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="ea518-259">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="configure-the-linker"></a><span data-ttu-id="ea518-260">Configurer l'éditeur de liens</span><span class="sxs-lookup"><span data-stu-id="ea518-260">Configure the Linker</span></span>

<span data-ttu-id="ea518-261">Éblouissant effectue une liaison IL (Intermediate Language) sur chaque version de mise en production pour supprimer l’IL inutile des assemblys de sortie.</span><span class="sxs-lookup"><span data-stu-id="ea518-261">Blazor performs Intermediate Language (IL) linking on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="ea518-262">Pour plus d’informations, consultez <xref:host-and-deploy/blazor/configure-linker>.</span><span class="sxs-lookup"><span data-stu-id="ea518-262">For more information, see <xref:host-and-deploy/blazor/configure-linker>.</span></span>

## <a name="custom-boot-resource-loading"></a><span data-ttu-id="ea518-263">Chargement des ressources de démarrage personnalisé</span><span class="sxs-lookup"><span data-stu-id="ea518-263">Custom boot resource loading</span></span>

<span data-ttu-id="ea518-264">Une application de webassembly éblouissant peut être initialisée avec `loadBootResource` la fonction pour remplacer le mécanisme de chargement des ressources de démarrage intégré.</span><span class="sxs-lookup"><span data-stu-id="ea518-264">A Blazor WebAssembly app can be initialized with the `loadBootResource` function to override the built-in boot resource loading mechanism.</span></span> <span data-ttu-id="ea518-265">Utilisez `loadBootResource` pour les scénarios suivants :</span><span class="sxs-lookup"><span data-stu-id="ea518-265">Use `loadBootResource` for the following scenarios:</span></span>

* <span data-ttu-id="ea518-266">Autorisez les utilisateurs à charger des ressources statiques, telles que des données de fuseau horaire ou *dotnet. WASM* à partir d’un CDN.</span><span class="sxs-lookup"><span data-stu-id="ea518-266">Allow users to load static resources, such as timezone data or *dotnet.wasm* from a CDN.</span></span>
* <span data-ttu-id="ea518-267">Chargez les assemblys compressés à l’aide d’une requête HTTP et décompressez-les sur le client pour les hôtes qui ne prennent pas en charge l’extraction du contenu compressé à partir du serveur.</span><span class="sxs-lookup"><span data-stu-id="ea518-267">Load compressed assemblies using an HTTP request and decompress them on the client for hosts that don't support fetching compressed contents from the server.</span></span>
* <span data-ttu-id="ea518-268">Alias des ressources à un autre nom en redirigeant `fetch` chaque requête vers un nouveau nom.</span><span class="sxs-lookup"><span data-stu-id="ea518-268">Alias resources to a different name by redirecting each `fetch` request to a new name.</span></span>

<span data-ttu-id="ea518-269">`loadBootResource`les paramètres s’affichent dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="ea518-269">`loadBootResource` parameters appear in the following table.</span></span>

| <span data-ttu-id="ea518-270">Paramètre</span><span class="sxs-lookup"><span data-stu-id="ea518-270">Parameter</span></span>    | <span data-ttu-id="ea518-271">Description</span><span class="sxs-lookup"><span data-stu-id="ea518-271">Description</span></span> |
| ------------ | ----------- |
| `type`       | <span data-ttu-id="ea518-272">Type de la ressource.</span><span class="sxs-lookup"><span data-stu-id="ea518-272">The type of the resource.</span></span> <span data-ttu-id="ea518-273">Types permissables : `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`,`timezonedata`</span><span class="sxs-lookup"><span data-stu-id="ea518-273">Permissable types: `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`, `timezonedata`</span></span> |
| `name`       | <span data-ttu-id="ea518-274">Nom de la ressource.</span><span class="sxs-lookup"><span data-stu-id="ea518-274">The name of the resource.</span></span> |
| `defaultUri` | <span data-ttu-id="ea518-275">URI relatif ou absolu de la ressource.</span><span class="sxs-lookup"><span data-stu-id="ea518-275">The relative or absolute URI of the resource.</span></span> |
| `integrity`  | <span data-ttu-id="ea518-276">Chaîne d’intégrité représentant le contenu attendu dans la réponse.</span><span class="sxs-lookup"><span data-stu-id="ea518-276">The integrity string representing the expected content in the response.</span></span> |

<span data-ttu-id="ea518-277">`loadBootResource`retourne l’un des éléments suivants pour remplacer le processus de chargement :</span><span class="sxs-lookup"><span data-stu-id="ea518-277">`loadBootResource` returns any of the following to override the loading process:</span></span>

* <span data-ttu-id="ea518-278">Chaîne d’URI.</span><span class="sxs-lookup"><span data-stu-id="ea518-278">URI string.</span></span> <span data-ttu-id="ea518-279">Dans l’exemple suivant (*wwwroot/index.html*), les fichiers suivants sont pris en charge à partir `https://my-awesome-cdn.com/`d’un CDN à l’adresse :</span><span class="sxs-lookup"><span data-stu-id="ea518-279">In the following example (*wwwroot/index.html*), the following files are served from a CDN at `https://my-awesome-cdn.com/`:</span></span>

  * <span data-ttu-id="ea518-280">*dotnet. \*. js*</span><span class="sxs-lookup"><span data-stu-id="ea518-280">*dotnet.\*.js*</span></span>
  * <span data-ttu-id="ea518-281">*dotnet. WASM*</span><span class="sxs-lookup"><span data-stu-id="ea518-281">*dotnet.wasm*</span></span>
  * <span data-ttu-id="ea518-282">Données de fuseau horaire</span><span class="sxs-lookup"><span data-stu-id="ea518-282">Timezone data</span></span>

  ```html
  ...

  <script src="_framework/blazor.webassembly.js" autostart="false"></script>
  <script>
    Blazor.start({
      loadBootResource: function (type, name, defaultUri, integrity) {
        console.log(`Loading: '${type}', '${name}', '${defaultUri}', '${integrity}'`);
        switch (type) {
          case 'dotnetjs':
          case 'dotnetwasm':
          case 'timezonedata':
            return `https://my-awesome-cdn.com/blazorwebassembly/3.2.0/${name}`;
        }
      }
    });
  </script>
  ```

* <span data-ttu-id="ea518-283">`Promise<Response>`.</span><span class="sxs-lookup"><span data-stu-id="ea518-283">`Promise<Response>`.</span></span> <span data-ttu-id="ea518-284">Transmettez `integrity` le paramètre dans un en-tête pour conserver le comportement de contrôle d’intégrité par défaut.</span><span class="sxs-lookup"><span data-stu-id="ea518-284">Pass the `integrity` parameter in a header to retain the default integrity-checking behavior.</span></span>

  <span data-ttu-id="ea518-285">L’exemple suivant (*wwwroot/index.html*) ajoute un en-tête HTTP personnalisé aux demandes sortantes et passe `integrity` le paramètre à l' `fetch` appel :</span><span class="sxs-lookup"><span data-stu-id="ea518-285">The following example (*wwwroot/index.html*) adds a custom HTTP header to the outbound requests and passes the `integrity` parameter through to the `fetch` call:</span></span>
  
  ```html
  <script src="_framework/blazor.webassembly.js" autostart="false"></script>
  <script>
    Blazor.start({
      loadBootResource: function (type, name, defaultUri, integrity) {
        return fetch(defaultUri, { 
          cache: 'no-cache',
          integrity: integrity,
          headers: { 'MyCustomHeader': 'My custom value' }
        });
      }
    });
  </script>
  ```

* <span data-ttu-id="ea518-286">`null`/`undefined`, ce qui entraîne le comportement de chargement par défaut.</span><span class="sxs-lookup"><span data-stu-id="ea518-286">`null`/`undefined`, which results in the default loading behavior.</span></span>

<span data-ttu-id="ea518-287">Les sources externes doivent retourner les en-têtes CORS requis pour les navigateurs afin d’autoriser le chargement des ressources Cross-Origin.</span><span class="sxs-lookup"><span data-stu-id="ea518-287">External sources must return the required CORS headers for browsers to allow the cross-origin resource loading.</span></span> <span data-ttu-id="ea518-288">Les CDN fournissent généralement les en-têtes requis par défaut.</span><span class="sxs-lookup"><span data-stu-id="ea518-288">CDNs usually provide the required headers by default.</span></span>

<span data-ttu-id="ea518-289">Il vous suffit de spécifier des types pour les comportements personnalisés.</span><span class="sxs-lookup"><span data-stu-id="ea518-289">You only need to specify types for custom behaviors.</span></span> <span data-ttu-id="ea518-290">Les types non spécifiés à sont chargés par le Framework en fonction de leurs comportements de `loadBootResource` chargement par défaut.</span><span class="sxs-lookup"><span data-stu-id="ea518-290">Types not specified to `loadBootResource` are loaded by the framework per their default loading behaviors.</span></span>
