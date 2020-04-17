---
title: Hébergez et déployez ASP.NET WebAssembly Core Blazor
author: guardrex
description: Découvrez comment héberger Blazor et déployer une application à l’aide de ASP.NET Core, Content Delivery Networks (CDN), de serveurs de fichiers et de pages GitHub.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/16/2020
no-loc:
- Blazor
- SignalR
uid: host-and-deploy/blazor/webassembly
ms.openlocfilehash: f3508144f1e472ee906a35e427fc57f536008ab6
ms.sourcegitcommit: 77c046331f3d633d7cc247ba77e58b89e254f487
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81488856"
---
# <a name="host-and-deploy-aspnet-core-opno-locblazor-webassembly"></a><span data-ttu-id="cc8d4-103">Hébergez et déployez ASP.NET WebAssembly Core Blazor</span><span class="sxs-lookup"><span data-stu-id="cc8d4-103">Host and deploy ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="cc8d4-104">Par [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) et [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="cc8d4-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="cc8d4-105">Avec le [ Blazor modèle d’hébergement WebAssembly](xref:blazor/hosting-models#blazor-webassembly):</span><span class="sxs-lookup"><span data-stu-id="cc8d4-105">With the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly):</span></span>

* <span data-ttu-id="cc8d4-106">L’application, Blazor ses dépendances et le temps d’exécution .NET sont téléchargés sur le navigateur en parallèle.</span><span class="sxs-lookup"><span data-stu-id="cc8d4-106">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser in parallel.</span></span>
* <span data-ttu-id="cc8d4-107">L’application est exécutée directement sur le thread d’interface utilisateur du navigateur.</span><span class="sxs-lookup"><span data-stu-id="cc8d4-107">The app is executed directly on the browser UI thread.</span></span>

<span data-ttu-id="cc8d4-108">Les stratégies de déploiement suivantes sont prises en charge :</span><span class="sxs-lookup"><span data-stu-id="cc8d4-108">The following deployment strategies are supported:</span></span>

* <span data-ttu-id="cc8d4-109">L’application Blazor est desservie par une application ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="cc8d4-109">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="cc8d4-110">Cette stratégie est abordée dans la section [Déploiement hébergé avec ASP.NET Core](#hosted-deployment-with-aspnet-core).</span><span class="sxs-lookup"><span data-stu-id="cc8d4-110">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>
* <span data-ttu-id="cc8d4-111">L’application Blazor est placée sur un serveur ou un service Web d’hébergement statique, où .NET n’est pas utilisé pour servir l’application. Blazor</span><span class="sxs-lookup"><span data-stu-id="cc8d4-111">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span> <span data-ttu-id="cc8d4-112">Cette stratégie est couverte dans la section de déploiement Blazor [Standalone,](#standalone-deployment) qui comprend des informations sur l’hébergement d’une application WebAssembly sous forme de sous-application IIS.</span><span class="sxs-lookup"><span data-stu-id="cc8d4-112">This strategy is covered in the [Standalone deployment](#standalone-deployment) section, which includes information on hosting a Blazor WebAssembly app as an IIS sub-app.</span></span>

## <a name="brotli-precompression"></a><span data-ttu-id="cc8d4-113">Précompression Brotli</span><span class="sxs-lookup"><span data-stu-id="cc8d4-113">Brotli precompression</span></span>

<span data-ttu-id="cc8d4-114">Lorsqu’une Blazor application WebAssembly est publiée, la sortie est précompressée à l’aide de [l’algorithme de compression Brotli](https://tools.ietf.org/html/rfc7932) au plus haut niveau pour réduire la taille de l’application et supprimer le besoin de compression en temps d’exécution.</span><span class="sxs-lookup"><span data-stu-id="cc8d4-114">When a Blazor WebAssembly app is published, the output is precompressed using the [Brotli compression algorithm](https://tools.ietf.org/html/rfc7932) at the highest level to reduce the app size and remove the need for runtime compression.</span></span>

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="cc8d4-115">Réécriture d’URL pour un routage correct</span><span class="sxs-lookup"><span data-stu-id="cc8d4-115">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="cc8d4-116">Le routage des demandes Blazor de composants de page dans une application WebAssembly n’est pas aussi simple que les demandes de routage dans une Blazor application hébergée Server.</span><span class="sxs-lookup"><span data-stu-id="cc8d4-116">Routing requests for page components in a Blazor WebAssembly app isn't as straightforward as routing requests in a Blazor Server, hosted app.</span></span> <span data-ttu-id="cc8d4-117">Envisagez Blazor une application WebAssembly avec deux composants :</span><span class="sxs-lookup"><span data-stu-id="cc8d4-117">Consider a Blazor WebAssembly app with two components:</span></span>

* <span data-ttu-id="cc8d4-118">*Main.razor* &ndash; Se charge à la racine de l’application et contient un lien vers le composant `About` (`href="About"`).</span><span class="sxs-lookup"><span data-stu-id="cc8d4-118">*Main.razor* &ndash; Loads at the root of the app and contains a link to the `About` component (`href="About"`).</span></span>
* <span data-ttu-id="cc8d4-119">Composant *About.Razor* &ndash; `About`.</span><span class="sxs-lookup"><span data-stu-id="cc8d4-119">*About.razor* &ndash; `About` component.</span></span>

<span data-ttu-id="cc8d4-120">Quand le document par défaut de l’application est demandé à l’aide de la barre d’adresses du navigateur (par exemple, `https://www.contoso.com/`) :</span><span class="sxs-lookup"><span data-stu-id="cc8d4-120">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="cc8d4-121">Le navigateur effectue une requête.</span><span class="sxs-lookup"><span data-stu-id="cc8d4-121">The browser makes a request.</span></span>
1. <span data-ttu-id="cc8d4-122">La page par défaut est retournée, généralement *index.html*.</span><span class="sxs-lookup"><span data-stu-id="cc8d4-122">The default page is returned, which is usually *index.html*.</span></span>
1. <span data-ttu-id="cc8d4-123">*index.HTML* amorce l’application.</span><span class="sxs-lookup"><span data-stu-id="cc8d4-123">*index.html* bootstraps the app.</span></span>
1. Blazor<span data-ttu-id="cc8d4-124">'s routeur charges, et `Main` le composant Razor est rendu.</span><span class="sxs-lookup"><span data-stu-id="cc8d4-124">'s router loads, and the Razor `Main` component is rendered.</span></span>

<span data-ttu-id="cc8d4-125">Dans la page Principale, la `About` sélection du lien vers Blazor le composant fonctionne sur le client `www.contoso.com` parce `About` que le `About` routeur empêche le navigateur de faire une demande sur Internet pour et sert le composant rendu lui-même.</span><span class="sxs-lookup"><span data-stu-id="cc8d4-125">In the Main page, selecting the link to the `About` component works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the rendered `About` component itself.</span></span> <span data-ttu-id="cc8d4-126">Toutes les demandes de points de terminaison internes *dans l’application Blazor WebAssembly* fonctionnent de la même manière : les demandes ne déclenchent pas de demandes basées sur le navigateur vers des ressources hébergées par le serveur sur Internet.</span><span class="sxs-lookup"><span data-stu-id="cc8d4-126">All of the requests for internal endpoints *within the Blazor WebAssembly app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="cc8d4-127">Le routeur gère les requêtes en interne.</span><span class="sxs-lookup"><span data-stu-id="cc8d4-127">The router handles the requests internally.</span></span>

<span data-ttu-id="cc8d4-128">Si une requête pour `www.contoso.com/About` est effectuée à l’aide de la barre d’adresses du navigateur, elle échoue.</span><span class="sxs-lookup"><span data-stu-id="cc8d4-128">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="cc8d4-129">Comme cette ressource n’existe pas sur l’hôte Internet de l’application, une réponse *404 – Non trouvé* est retournée.</span><span class="sxs-lookup"><span data-stu-id="cc8d4-129">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="cc8d4-130">Étant donné que les navigateurs envoient des requêtes aux hôtes basés sur Internet pour des pages côté client, les serveurs web et les services d’hébergement doivent réécrire toutes les requêtes pour les ressources qui ne se trouvent pas physiquement sur le serveur afin qu’elles pointent vers la page *index.html*.</span><span class="sxs-lookup"><span data-stu-id="cc8d4-130">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the *index.html* page.</span></span> <span data-ttu-id="cc8d4-131">Lorsque *index.html* est retourné, Blazor le routeur de l’application prend le relais et répond avec la bonne ressource.</span><span class="sxs-lookup"><span data-stu-id="cc8d4-131">When *index.html* is returned, the app's Blazor router takes over and responds with the correct resource.</span></span>

<span data-ttu-id="cc8d4-132">Lors du déploiement sur un serveur IIS, vous pouvez utiliser le module de réécriture d’URL avec le fichier *web.config* publié par l’application.</span><span class="sxs-lookup"><span data-stu-id="cc8d4-132">When deploying to an IIS server, you can use the URL Rewrite Module with the app's published *web.config* file.</span></span> <span data-ttu-id="cc8d4-133">Pour plus d’informations, consultez la section [IIS.](#iis)</span><span class="sxs-lookup"><span data-stu-id="cc8d4-133">For more information, see the [IIS](#iis) section.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="cc8d4-134">Déploiement hébergé avec ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cc8d4-134">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="cc8d4-135">Un *déploiement hébergé* Blazor sert l’application WebAssembly aux navigateurs à partir d’une [application ASP.NET Core](xref:index) qui s’exécute sur un serveur Web.</span><span class="sxs-lookup"><span data-stu-id="cc8d4-135">A *hosted deployment* serves the Blazor WebAssembly app to browsers from an [ASP.NET Core app](xref:index) that runs on a web server.</span></span>

<span data-ttu-id="cc8d4-136">L’application WebAssembly client Blazor est publiée dans le dossier */bin/Release/TARGET FRAMEWORK/publish/wwwroot* de l’application serveur, ainsi que tous les autres actifs Web statiques de l’application serveur.</span><span class="sxs-lookup"><span data-stu-id="cc8d4-136">The client Blazor WebAssembly app is published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="cc8d4-137">Les deux applications sont déployées ensemble.</span><span class="sxs-lookup"><span data-stu-id="cc8d4-137">The two apps are deployed together.</span></span> <span data-ttu-id="cc8d4-138">Un serveur web capable d’héberger une application ASP.NET Core est nécessaire.</span><span class="sxs-lookup"><span data-stu-id="cc8d4-138">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="cc8d4-139">Pour un déploiement hébergé, Visual Studio inclut le modèle`blazorwasm` de`-ho|--hosted` `dotnet new` \*\* Blazor projet WebAssembly App\*\* (modèle lors de l’utilisation de la nouvelle commande [dotnet)](/dotnet/core/tools/dotnet-new) avec **l’option Hébergée** sélectionnée (lors de l’utilisation de la commande).</span><span class="sxs-lookup"><span data-stu-id="cc8d4-139">For a hosted deployment, Visual Studio includes the **Blazor WebAssembly App** project template (`blazorwasm` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command) with the **Hosted** option selected (`-ho|--hosted` when using the `dotnet new` command).</span></span>

<span data-ttu-id="cc8d4-140">Pour plus d’informations sur l’hébergement et le déploiement d’applications ASP.NET Core, consultez <xref:host-and-deploy/index>.</span><span class="sxs-lookup"><span data-stu-id="cc8d4-140">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="cc8d4-141">Pour plus d’informations concernant le déploiement sur Azure App Service, consultez <xref:tutorials/publish-to-azure-webapp-using-vs>.</span><span class="sxs-lookup"><span data-stu-id="cc8d4-141">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

## <a name="standalone-deployment"></a><span data-ttu-id="cc8d4-142">Déploiement autonome</span><span class="sxs-lookup"><span data-stu-id="cc8d4-142">Standalone deployment</span></span>

<span data-ttu-id="cc8d4-143">Un *déploiement autonome* sert Blazor l’application WebAssembly comme un ensemble de fichiers statiques qui sont demandés directement par les clients.</span><span class="sxs-lookup"><span data-stu-id="cc8d4-143">A *standalone deployment* serves the Blazor WebAssembly app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="cc8d4-144">N’importe quel serveur de Blazor fichiers statique est en mesure de servir l’application.</span><span class="sxs-lookup"><span data-stu-id="cc8d4-144">Any static file server is able to serve the Blazor app.</span></span>

<span data-ttu-id="cc8d4-145">Les actifs de déploiement autonomes sont publiés dans le *dossier /bin/Release/TARGET FRAMEWORKMD/publish/wwwroot.*</span><span class="sxs-lookup"><span data-stu-id="cc8d4-145">Standalone deployment assets are published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder.</span></span>

### <a name="iis"></a><span data-ttu-id="cc8d4-146">IIS</span><span class="sxs-lookup"><span data-stu-id="cc8d4-146">IIS</span></span>

<span data-ttu-id="cc8d4-147">IIS est un serveur Blazor de fichiers statique capable pour les applications.</span><span class="sxs-lookup"><span data-stu-id="cc8d4-147">IIS is a capable static file server for Blazor apps.</span></span> <span data-ttu-id="cc8d4-148">Pour configurer IIS pour héberger, Blazorvoir Construire un site Statique sur [IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span><span class="sxs-lookup"><span data-stu-id="cc8d4-148">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="cc8d4-149">Les ressources publiées sont créées dans le dossier */bin/Release/{FRAMEWORK CIBLE}/publish*.</span><span class="sxs-lookup"><span data-stu-id="cc8d4-149">Published assets are created in the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span> <span data-ttu-id="cc8d4-150">Hébergez le contenu du dossier *publish* sur le serveur web ou le service d’hébergement.</span><span class="sxs-lookup"><span data-stu-id="cc8d4-150">Host the contents of the *publish* folder on the web server or hosting service.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="cc8d4-151">web.config</span><span class="sxs-lookup"><span data-stu-id="cc8d4-151">web.config</span></span>

<span data-ttu-id="cc8d4-152">Lorsqu’un Blazor projet est publié, un fichier *web.config* est créé avec la configuration IIS suivante :</span><span class="sxs-lookup"><span data-stu-id="cc8d4-152">When a Blazor project is published, a *web.config* file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="cc8d4-153">Les types MIME sont définis pour les extensions de fichiers suivantes :</span><span class="sxs-lookup"><span data-stu-id="cc8d4-153">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="cc8d4-154">*.dll* &ndash;`application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="cc8d4-154">*.dll* &ndash; `application/octet-stream`</span></span>
  * <span data-ttu-id="cc8d4-155">*.json (json)* &ndash;`application/json`</span><span class="sxs-lookup"><span data-stu-id="cc8d4-155">*.json* &ndash; `application/json`</span></span>
  * <span data-ttu-id="cc8d4-156">*.wasm* &ndash;`application/wasm`</span><span class="sxs-lookup"><span data-stu-id="cc8d4-156">*.wasm* &ndash; `application/wasm`</span></span>
  * <span data-ttu-id="cc8d4-157">*.woff* &ndash;`application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="cc8d4-157">*.woff* &ndash; `application/font-woff`</span></span>
  * <span data-ttu-id="cc8d4-158">*.woff2* &ndash;`application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="cc8d4-158">*.woff2* &ndash; `application/font-woff`</span></span>
* <span data-ttu-id="cc8d4-159">La compression HTTP est activée pour les types MIME suivants :</span><span class="sxs-lookup"><span data-stu-id="cc8d4-159">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="cc8d4-160">Des règles du module de réécriture d’URL sont établies :</span><span class="sxs-lookup"><span data-stu-id="cc8d4-160">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="cc8d4-161">Servez le sous-répertoire où résident les actifs statiques de l’application *(wwwroot/'PATH REQUESTED').*</span><span class="sxs-lookup"><span data-stu-id="cc8d4-161">Serve the sub-directory where the app's static assets reside (*wwwroot/{PATH REQUESTED}*).</span></span>
  * <span data-ttu-id="cc8d4-162">Créez un itinéraire de repli SPA de sorte que les demandes d’actifs autres que les fichiers soient redirigées vers le document par défaut de l’application dans son dossier d’actifs statiques *(wwwroot/index.html*).</span><span class="sxs-lookup"><span data-stu-id="cc8d4-162">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (*wwwroot/index.html*).</span></span>
  
#### <a name="use-a-custom-webconfig"></a><span data-ttu-id="cc8d4-163">Utilisez un web.config personnalisé</span><span class="sxs-lookup"><span data-stu-id="cc8d4-163">Use a custom web.config</span></span>

<span data-ttu-id="cc8d4-164">Pour utiliser un fichier *web.config* personnalisé :</span><span class="sxs-lookup"><span data-stu-id="cc8d4-164">To use a custom *web.config* file:</span></span>

1. <span data-ttu-id="cc8d4-165">Placez le fichier *web.config* personnalisé à la racine du dossier de projet.</span><span class="sxs-lookup"><span data-stu-id="cc8d4-165">Place the custom *web.config* file at the root of the project folder.</span></span>
1. <span data-ttu-id="cc8d4-166">Ajoutez la cible suivante au fichier du projet (*.csproj*) :</span><span class="sxs-lookup"><span data-stu-id="cc8d4-166">Add the following target to the project file (*.csproj*):</span></span>

   ```xml
   <Target Name="CopyWebConfigOnPublish" AfterTargets="Publish">
     <Copy SourceFiles="web.config" DestinationFolder="$(PublishDir)" />
   </Target>
   ```
   
> [!NOTE]
> <span data-ttu-id="cc8d4-167">L’utilisation de la `<IsWebConfigTransformDisabled>` propriété `true` MSBuild Blazor mis à n’est pas pris en charge dans les applications WebAssembly [comme il est pour ASP.NET applications Core déployées à IIS](xref:host-and-deploy/iis/index#webconfig-file).</span><span class="sxs-lookup"><span data-stu-id="cc8d4-167">Use of the MSBuild property `<IsWebConfigTransformDisabled>` set to `true` isn't supported in Blazor WebAssembly apps [as it is for ASP.NET Core apps deployed to IIS](xref:host-and-deploy/iis/index#webconfig-file).</span></span> <span data-ttu-id="cc8d4-168">Pour plus d’informations, voir [La cible Copy requise pour fournir wasM web.config personnalisé Blazor (dotnet/aspnetcore #20569)](https://github.com/dotnet/aspnetcore/issues/20569).</span><span class="sxs-lookup"><span data-stu-id="cc8d4-168">For more information, see [Copy target required to provide custom Blazor WASM web.config (dotnet/aspnetcore #20569)](https://github.com/dotnet/aspnetcore/issues/20569).</span></span>

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="cc8d4-169">Installer le module de réécriture d’URL</span><span class="sxs-lookup"><span data-stu-id="cc8d4-169">Install the URL Rewrite Module</span></span>

<span data-ttu-id="cc8d4-170">Le [module de réécriture d’URL](https://www.iis.net/downloads/microsoft/url-rewrite) est nécessaire pour réécrire les URL.</span><span class="sxs-lookup"><span data-stu-id="cc8d4-170">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="cc8d4-171">Il n’est pas installé par défaut, et ne peut pas l’être en tant que fonctionnalité de service de rôle Serveur Web (IIS).</span><span class="sxs-lookup"><span data-stu-id="cc8d4-171">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span> <span data-ttu-id="cc8d4-172">Vous devez le télécharger à partir du site web IIS.</span><span class="sxs-lookup"><span data-stu-id="cc8d4-172">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="cc8d4-173">Utilisez Web Platform Installer pour installer le module :</span><span class="sxs-lookup"><span data-stu-id="cc8d4-173">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="cc8d4-174">Localement, accédez à la [page des téléchargements du Module de réécriture d’URL](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span><span class="sxs-lookup"><span data-stu-id="cc8d4-174">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="cc8d4-175">Pour la version anglaise, sélectionnez **WebPI** pour télécharger le programme d’installation WebPI.</span><span class="sxs-lookup"><span data-stu-id="cc8d4-175">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="cc8d4-176">Pour les autres langues, sélectionnez l’architecture appropriée pour le serveur (x86/x64) afin de télécharger le programme d’installation.</span><span class="sxs-lookup"><span data-stu-id="cc8d4-176">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="cc8d4-177">Copiez le programme d’installation sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="cc8d4-177">Copy the installer to the server.</span></span> <span data-ttu-id="cc8d4-178">Exécutez le programme d’installation.</span><span class="sxs-lookup"><span data-stu-id="cc8d4-178">Run the installer.</span></span> <span data-ttu-id="cc8d4-179">Sélectionnez le bouton **Installer** et acceptez les termes du contrat de licence.</span><span class="sxs-lookup"><span data-stu-id="cc8d4-179">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="cc8d4-180">Il n’est pas nécessaire de redémarrer le serveur après l’installation.</span><span class="sxs-lookup"><span data-stu-id="cc8d4-180">A server restart isn't required after the install completes.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="cc8d4-181">Configurer le site web</span><span class="sxs-lookup"><span data-stu-id="cc8d4-181">Configure the website</span></span>

<span data-ttu-id="cc8d4-182">Affectez le dossier de l’application comme **chemin physique** du site web.</span><span class="sxs-lookup"><span data-stu-id="cc8d4-182">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="cc8d4-183">Le dossier contient les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="cc8d4-183">The folder contains:</span></span>

* <span data-ttu-id="cc8d4-184">Le fichier *web.config* utilisé par IIS pour configurer le site web, notamment les règles de redirection nécessaires et les types de contenu de fichiers</span><span class="sxs-lookup"><span data-stu-id="cc8d4-184">The *web.config* file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="cc8d4-185">Le dossier de ressources statiques de l’application</span><span class="sxs-lookup"><span data-stu-id="cc8d4-185">The app's static asset folder.</span></span>

#### <a name="host-as-an-iis-sub-app"></a><span data-ttu-id="cc8d4-186">Hôte en tant que sous-application IIS</span><span class="sxs-lookup"><span data-stu-id="cc8d4-186">Host as an IIS sub-app</span></span>

<span data-ttu-id="cc8d4-187">Si une application autonome est hébergée sous forme de sous-application IIS, effectuez l’une ou l’autre des applications suivantes :</span><span class="sxs-lookup"><span data-stu-id="cc8d4-187">If a standalone app is hosted as an IIS sub-app, perform either of the following:</span></span>

* <span data-ttu-id="cc8d4-188">Désactiver le gestionnaire hérité ASP.NET module de base.</span><span class="sxs-lookup"><span data-stu-id="cc8d4-188">Disable the inherited ASP.NET Core Module handler.</span></span>

  <span data-ttu-id="cc8d4-189">Supprimer le gestionnaire Blazor dans le fichier *web.config* `<handlers>` publié par l’application en ajoutant une section au fichier :</span><span class="sxs-lookup"><span data-stu-id="cc8d4-189">Remove the handler in the Blazor app's published *web.config* file by adding a `<handlers>` section to the file:</span></span>

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* <span data-ttu-id="cc8d4-190">Désactiver l’héritage de la section `<system.webServer>` de l’application `inheritInChildApplications` racine `false`(parent) à l’aide d’un `<location>` élément avec ensemble à :</span><span class="sxs-lookup"><span data-stu-id="cc8d4-190">Disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

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

<span data-ttu-id="cc8d4-191">La suppression du gestionnaire ou l’héritage invalidant est effectuée en plus de [configurer le chemin de base de l’application](xref:host-and-deploy/blazor/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="cc8d4-191">Removing the handler or disabling inheritance is performed in addition to [configuring the app's base path](xref:host-and-deploy/blazor/index#app-base-path).</span></span> <span data-ttu-id="cc8d4-192">Dans le fichier *index.html* de l’application, définissez le chemin de base de l’application sur l’alias IIS utilisé lors de la configuration de la sous-application dans IIS.</span><span class="sxs-lookup"><span data-stu-id="cc8d4-192">Set the app base path in the app's *index.html* file to the IIS alias used when configuring the sub-app in IIS.</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="cc8d4-193">Dépannage</span><span class="sxs-lookup"><span data-stu-id="cc8d4-193">Troubleshooting</span></span>

<span data-ttu-id="cc8d4-194">Si vous recevez un message *500 – Erreur interne du serveur* et que le Gestionnaire IIS lève des erreurs quand vous tentez d’accéder à la configuration du site web, vérifiez que le module de réécriture d’URL est installé.</span><span class="sxs-lookup"><span data-stu-id="cc8d4-194">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="cc8d4-195">Quand le module n’est pas installé, le fichier *web.config* ne peut pas être analysé par IIS.</span><span class="sxs-lookup"><span data-stu-id="cc8d4-195">When the module isn't installed, the *web.config* file can't be parsed by IIS.</span></span> <span data-ttu-id="cc8d4-196">Cela empêche le gestionnaire de l’IIS de charger Blazorla configuration du site Web et le site Web de servir les fichiers statiques de l’IIS.</span><span class="sxs-lookup"><span data-stu-id="cc8d4-196">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

<span data-ttu-id="cc8d4-197">Pour plus d’informations sur le dépannage des déploiements sur IIS, consultez <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="cc8d4-197">For more information on troubleshooting deployments to IIS, see <xref:test/troubleshoot-azure-iis>.</span></span>

### <a name="azure-storage"></a><span data-ttu-id="cc8d4-198">Stockage Azure</span><span class="sxs-lookup"><span data-stu-id="cc8d4-198">Azure Storage</span></span>

<span data-ttu-id="cc8d4-199">[Azure Storage](/azure/storage/) static file Blazor hosting permet l’hébergement d’applications sans serveur.</span><span class="sxs-lookup"><span data-stu-id="cc8d4-199">[Azure Storage](/azure/storage/) static file hosting allows serverless Blazor app hosting.</span></span> <span data-ttu-id="cc8d4-200">Les noms de domaine personnalisé, le réseau de distribution de contenu Azure (CDN) et HTTPS sont pris en charge.</span><span class="sxs-lookup"><span data-stu-id="cc8d4-200">Custom domain names, the Azure Content Delivery Network (CDN), and HTTPS are supported.</span></span>

<span data-ttu-id="cc8d4-201">Lorsque le service blob est activé pour l’hébergement de site Web statique sur un compte de stockage :</span><span class="sxs-lookup"><span data-stu-id="cc8d4-201">When the blob service is enabled for static website hosting on a storage account:</span></span>

* <span data-ttu-id="cc8d4-202">Définissez le **nom du document d’index** sur `index.html`.</span><span class="sxs-lookup"><span data-stu-id="cc8d4-202">Set the **Index document name** to `index.html`.</span></span>
* <span data-ttu-id="cc8d4-203">Définissez le **chemin d’accès au document d’erreur** sur `index.html`.</span><span class="sxs-lookup"><span data-stu-id="cc8d4-203">Set the **Error document path** to `index.html`.</span></span> <span data-ttu-id="cc8d4-204">Les composants de Razor et autres points de terminaison non-fichier ne se trouvent sur les chemins d’accès physiques dans le contenu statique stocké par le service blob.</span><span class="sxs-lookup"><span data-stu-id="cc8d4-204">Razor components and other non-file endpoints don't reside at physical paths in the static content stored by the blob service.</span></span> <span data-ttu-id="cc8d4-205">Lorsqu’une demande pour l’une Blazor de ces ressources est reçue que le routeur doit gérer, l’erreur *404 - Non trouvé* générée par le service blob itinéraires de la demande à la **voie de document d’erreur**.</span><span class="sxs-lookup"><span data-stu-id="cc8d4-205">When a request for one of these resources is received that the Blazor router should handle, the *404 - Not Found* error generated by the blob service routes the request to the **Error document path**.</span></span> <span data-ttu-id="cc8d4-206">Le blob *index.html* est Blazor retourné, et le routeur charge et traite le chemin.</span><span class="sxs-lookup"><span data-stu-id="cc8d4-206">The *index.html* blob is returned, and the Blazor router loads and processes the path.</span></span>

<span data-ttu-id="cc8d4-207">Pour plus d’informations, consultez [Hébergement de sites web statiques dans le service Stockage Azure](/azure/storage/blobs/storage-blob-static-website).</span><span class="sxs-lookup"><span data-stu-id="cc8d4-207">For more information, see [Static website hosting in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span></span>

### <a name="nginx"></a><span data-ttu-id="cc8d4-208">Nginx</span><span class="sxs-lookup"><span data-stu-id="cc8d4-208">Nginx</span></span>

<span data-ttu-id="cc8d4-209">Le fichier *nginx.conf* suivant est simplifié pour montrer comment configurer Nginx pour envoyer le fichier *index.html* chaque fois qu’il ne peut pas trouver un fichier correspondant sur le disque.</span><span class="sxs-lookup"><span data-stu-id="cc8d4-209">The following *nginx.conf* file is simplified to show how to configure Nginx to send the *index.html* file whenever it can't find a corresponding file on disk.</span></span>

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

<span data-ttu-id="cc8d4-210">Pour plus d’informations sur la configuration du serveur web Nginx de production, consultez [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/) (Création de fichiers de configuration NGINX et NGINX Plus).</span><span class="sxs-lookup"><span data-stu-id="cc8d4-210">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

### <a name="nginx-in-docker"></a><span data-ttu-id="cc8d4-211">Nginx dans Docker</span><span class="sxs-lookup"><span data-stu-id="cc8d4-211">Nginx in Docker</span></span>

<span data-ttu-id="cc8d4-212">Pour Blazor accueillir dans Docker en utilisant Nginx, configurez le Dockerfile pour utiliser l’image Nginx basée sur alpine.</span><span class="sxs-lookup"><span data-stu-id="cc8d4-212">To host Blazor in Docker using Nginx, setup the Dockerfile to use the Alpine-based Nginx image.</span></span> <span data-ttu-id="cc8d4-213">Mettez à jour le fichier Dockerfile pour copier le fichier *nginx.config* dans le conteneur.</span><span class="sxs-lookup"><span data-stu-id="cc8d4-213">Update the Dockerfile to copy the *nginx.config* file into the container.</span></span>

<span data-ttu-id="cc8d4-214">Ajoutez une ligne au fichier Dockerfile, comme indiqué dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="cc8d4-214">Add one line to the Dockerfile, as shown in the following example:</span></span>

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a><span data-ttu-id="cc8d4-215">Apache</span><span class="sxs-lookup"><span data-stu-id="cc8d4-215">Apache</span></span>

<span data-ttu-id="cc8d4-216">Pour déployer Blazor une application WebAssembly sur CentOS 7 ou plus tard :</span><span class="sxs-lookup"><span data-stu-id="cc8d4-216">To deploy a Blazor WebAssembly app to CentOS 7 or later:</span></span>

1. <span data-ttu-id="cc8d4-217">Créez le fichier de configuration Apache.</span><span class="sxs-lookup"><span data-stu-id="cc8d4-217">Create the Apache configuration file.</span></span> <span data-ttu-id="cc8d4-218">L’exemple suivant est un fichier de configuration simplifié (*blazorapp.config*) :</span><span class="sxs-lookup"><span data-stu-id="cc8d4-218">The following example is a simplified configuration file (*blazorapp.config*):</span></span>

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

1. <span data-ttu-id="cc8d4-219">Placez le fichier de `/etc/httpd/conf.d/` configuration Apache dans l’annuaire, qui est le répertoire de configuration Apache par défaut dans CentOS 7.</span><span class="sxs-lookup"><span data-stu-id="cc8d4-219">Place the Apache configuration file into the `/etc/httpd/conf.d/` directory, which is the default Apache configuration directory in CentOS 7.</span></span>

1. <span data-ttu-id="cc8d4-220">Placez les fichiers de `/var/www/blazorapp` l’application dans `DocumentRoot` l’annuaire (l’emplacement spécifié dans le fichier de configuration).</span><span class="sxs-lookup"><span data-stu-id="cc8d4-220">Place the app's files into the `/var/www/blazorapp` directory (the location specified to `DocumentRoot` in the configuration file).</span></span>

1. <span data-ttu-id="cc8d4-221">Redémarrer le service Apache.</span><span class="sxs-lookup"><span data-stu-id="cc8d4-221">Restart the Apache service.</span></span>

<span data-ttu-id="cc8d4-222">Pour plus d’informations, voir [mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) et [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span><span class="sxs-lookup"><span data-stu-id="cc8d4-222">For more information, see [mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) and [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span></span>

### <a name="github-pages"></a><span data-ttu-id="cc8d4-223">GitHub Pages</span><span class="sxs-lookup"><span data-stu-id="cc8d4-223">GitHub Pages</span></span>

<span data-ttu-id="cc8d4-224">Pour gérer les réécritures d’URL, ajoutez un fichier *404.html* avec un script qui gère la redirection de la requête vers la page *index.html*.</span><span class="sxs-lookup"><span data-stu-id="cc8d4-224">To handle URL rewrites, add a *404.html* file with a script that handles redirecting the request to the *index.html* page.</span></span> <span data-ttu-id="cc8d4-225">Pour obtenir un exemple d’implémentation fournie par la communauté, consultez [Single Page Apps for GitHub Pages](https://spa-github-pages.rafrex.com/) (Applications à une seule page pour GitHub Pages) ([rafrex/spa-github-pages sur GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span><span class="sxs-lookup"><span data-stu-id="cc8d4-225">For an example implementation provided by the community, see [Single Page Apps for GitHub Pages](https://spa-github-pages.rafrex.com/) ([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span></span> <span data-ttu-id="cc8d4-226">Vous pouvez obtenir un exemple d’utilisation de cette approche à l’adresse [blazor-demo/blazor-demo.github.io sur GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([site actif](https://blazor-demo.github.io/)).</span><span class="sxs-lookup"><span data-stu-id="cc8d4-226">An example using the community approach can be seen at [blazor-demo/blazor-demo.github.io on GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([live site](https://blazor-demo.github.io/)).</span></span>

<span data-ttu-id="cc8d4-227">Quand vous utilisez un site de projet plutôt qu’un site d’entreprise, ajoutez ou mettez à jour la balise `<base>` dans *index.html*.</span><span class="sxs-lookup"><span data-stu-id="cc8d4-227">When using a project site instead of an organization site, add or update the `<base>` tag in *index.html*.</span></span> <span data-ttu-id="cc8d4-228">Définissez la valeur d’attribut `href` sur le nom du dépôt GitHub avec une barre oblique (par exemple, `my-repository/`.</span><span class="sxs-lookup"><span data-stu-id="cc8d4-228">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `my-repository/`.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="cc8d4-229">Valeurs de configuration de l’hôte</span><span class="sxs-lookup"><span data-stu-id="cc8d4-229">Host configuration values</span></span>

<span data-ttu-id="cc8d4-230">Les applications WebAssembly peuvent accepter les valeurs de configuration d’hôte suivantes comme arguments de ligne de commande au moment de l’exécution dans l’environnement de développement. [ Blazor ](xref:blazor/hosting-models#blazor-webassembly)</span><span class="sxs-lookup"><span data-stu-id="cc8d4-230">[Blazor WebAssembly apps](xref:blazor/hosting-models#blazor-webassembly) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="cc8d4-231">Racine de contenu</span><span class="sxs-lookup"><span data-stu-id="cc8d4-231">Content root</span></span>

<span data-ttu-id="cc8d4-232">L’argument `--contentroot` définit le chemin absolu vers l’annuaire qui contient les fichiers de contenu de l’application[(racine](xref:fundamentals/index#content-root)de contenu ).</span><span class="sxs-lookup"><span data-stu-id="cc8d4-232">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files ([content root](xref:fundamentals/index#content-root)).</span></span> <span data-ttu-id="cc8d4-233">Dans les exemples suivants, `/content-root-path` est le chemin racine du contenu de l’application.</span><span class="sxs-lookup"><span data-stu-id="cc8d4-233">In the following examples, `/content-root-path` is the app's content root path.</span></span>

* <span data-ttu-id="cc8d4-234">Passez l’argument lors de l’exécution de l’application localement à une invite de commandes.</span><span class="sxs-lookup"><span data-stu-id="cc8d4-234">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="cc8d4-235">À partir du répertoire de l’application, exécutez :</span><span class="sxs-lookup"><span data-stu-id="cc8d4-235">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="cc8d4-236">Ajoutez une entrée au fichier *launchSettings.json* de l’application dans le profil **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="cc8d4-236">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="cc8d4-237">Ce paramètre est utilisé en cas d’exécution de l’application avec le débogueur Visual Studio et dans une invite de commandes avec `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="cc8d4-237">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="cc8d4-238">Dans Visual Studio, spécifiez l’argument dans **Properties** > **Debug** > **Arguments .**</span><span class="sxs-lookup"><span data-stu-id="cc8d4-238">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="cc8d4-239">Le fait de définir l’argument dans la page de propriétés Visual Studio l’ajoute au fichier *launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="cc8d4-239">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="cc8d4-240">Base du chemin</span><span class="sxs-lookup"><span data-stu-id="cc8d4-240">Path base</span></span>

<span data-ttu-id="cc8d4-241">`--pathbase` L’argument définit le chemin de base de l’application pour une `<base>` `href` application exécutée localement `/` avec un chemin d’URL relatif non-root (l’étiquette est réglée sur un chemin autre que pour la mise en scène et la production).</span><span class="sxs-lookup"><span data-stu-id="cc8d4-241">The `--pathbase` argument sets the app base path for an app run locally with a non-root relative URL path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="cc8d4-242">Dans les exemples suivants, `/relative-URL-path` est la base du chemin de l’application.</span><span class="sxs-lookup"><span data-stu-id="cc8d4-242">In the following examples, `/relative-URL-path` is the app's path base.</span></span> <span data-ttu-id="cc8d4-243">Pour plus d’informations, voir [le chemin de base de l’application](xref:host-and-deploy/blazor/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="cc8d4-243">For more information, see [App base path](xref:host-and-deploy/blazor/index#app-base-path).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="cc8d4-244">Contrairement au chemin fourni au `href` de la balise `<base>`, n’incluez pas de barre oblique (`/`) quand vous passez la valeur d’argument `--pathbase`.</span><span class="sxs-lookup"><span data-stu-id="cc8d4-244">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="cc8d4-245">Si vous spécifiez `<base href="/CoolApp/">` (inclut une barre oblique) comme chemin de base de l’application dans la balise `<base>`, passez `--pathbase=/CoolApp` (aucune barre oblique de fin) comme valeur d’argument de ligne de commande.</span><span class="sxs-lookup"><span data-stu-id="cc8d4-245">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="cc8d4-246">Passez l’argument lors de l’exécution de l’application localement à une invite de commandes.</span><span class="sxs-lookup"><span data-stu-id="cc8d4-246">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="cc8d4-247">À partir du répertoire de l’application, exécutez :</span><span class="sxs-lookup"><span data-stu-id="cc8d4-247">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* <span data-ttu-id="cc8d4-248">Ajoutez une entrée au fichier *launchSettings.json* de l’application dans le profil **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="cc8d4-248">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="cc8d4-249">Ce paramètre est utilisé en cas d’exécution de l’application avec le débogueur Visual Studio et dans une invite de commandes avec `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="cc8d4-249">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* <span data-ttu-id="cc8d4-250">Dans Visual Studio, spécifiez l’argument dans **Properties** > **Debug** > **Arguments .**</span><span class="sxs-lookup"><span data-stu-id="cc8d4-250">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="cc8d4-251">Le fait de définir l’argument dans la page de propriétés Visual Studio l’ajoute au fichier *launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="cc8d4-251">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a><span data-ttu-id="cc8d4-252">URLs</span><span class="sxs-lookup"><span data-stu-id="cc8d4-252">URLs</span></span>

<span data-ttu-id="cc8d4-253">L’argument `--urls` définit les adresses IP ou les adresses d’hôtes avec les ports et protocoles sur lesquels il faut écouter les demandes.</span><span class="sxs-lookup"><span data-stu-id="cc8d4-253">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="cc8d4-254">Passez l’argument lors de l’exécution de l’application localement à une invite de commandes.</span><span class="sxs-lookup"><span data-stu-id="cc8d4-254">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="cc8d4-255">À partir du répertoire de l’application, exécutez :</span><span class="sxs-lookup"><span data-stu-id="cc8d4-255">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="cc8d4-256">Ajoutez une entrée au fichier *launchSettings.json* de l’application dans le profil **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="cc8d4-256">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="cc8d4-257">Ce paramètre est utilisé en cas d’exécution de l’application avec le débogueur Visual Studio et dans une invite de commandes avec `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="cc8d4-257">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="cc8d4-258">Dans Visual Studio, spécifiez l’argument dans **Properties** > **Debug** > **Arguments .**</span><span class="sxs-lookup"><span data-stu-id="cc8d4-258">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="cc8d4-259">Le fait de définir l’argument dans la page de propriétés Visual Studio l’ajoute au fichier *launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="cc8d4-259">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="configure-the-linker"></a><span data-ttu-id="cc8d4-260">Configurer l'éditeur de liens</span><span class="sxs-lookup"><span data-stu-id="cc8d4-260">Configure the Linker</span></span>

Blazor<span data-ttu-id="cc8d4-261">effectue des liens de langage intermédiaire (IL) sur chaque version de version pour supprimer l’IL inutile des assemblages de sortie.</span><span class="sxs-lookup"><span data-stu-id="cc8d4-261"> performs Intermediate Language (IL) linking on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="cc8d4-262">Pour plus d’informations, consultez <xref:host-and-deploy/blazor/configure-linker>.</span><span class="sxs-lookup"><span data-stu-id="cc8d4-262">For more information, see <xref:host-and-deploy/blazor/configure-linker>.</span></span>
