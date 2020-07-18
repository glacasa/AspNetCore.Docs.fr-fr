---
title: Héberger et déployer des ASP.NET CoreBlazor WebAssembly
author: guardrex
description: Découvrez comment héberger et déployer une Blazor application à l’aide de ASP.net Core, de réseaux de distribution de contenu (CDN), de serveurs de fichiers et de pages github.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/09/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/host-and-deploy/webassembly
ms.openlocfilehash: 2a2b0dabc26c14624144ce7eceb5861fe56f1054
ms.sourcegitcommit: 384833762c614851db653b841cc09fbc944da463
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/17/2020
ms.locfileid: "86445136"
---
# <a name="host-and-deploy-aspnet-core-blazor-webassembly"></a><span data-ttu-id="9410b-103">Héberger et déployer des ASP.NET CoreBlazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="9410b-103">Host and deploy ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="9410b-104">Par [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams)et [safia Abdalla](https://safia.rocks)</span><span class="sxs-lookup"><span data-stu-id="9410b-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams), and [Safia Abdalla](https://safia.rocks)</span></span>

<span data-ttu-id="9410b-105">Avec le [ Blazor WebAssembly modèle d’hébergement](xref:blazor/hosting-models#blazor-webassembly):</span><span class="sxs-lookup"><span data-stu-id="9410b-105">With the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly):</span></span>

* <span data-ttu-id="9410b-106">L' Blazor application, ses dépendances et le Runtime .net sont téléchargés dans le navigateur en parallèle.</span><span class="sxs-lookup"><span data-stu-id="9410b-106">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser in parallel.</span></span>
* <span data-ttu-id="9410b-107">L’application est exécutée directement sur le thread d’interface utilisateur du navigateur.</span><span class="sxs-lookup"><span data-stu-id="9410b-107">The app is executed directly on the browser UI thread.</span></span>

<span data-ttu-id="9410b-108">Les stratégies de déploiement suivantes sont prises en charge :</span><span class="sxs-lookup"><span data-stu-id="9410b-108">The following deployment strategies are supported:</span></span>

* <span data-ttu-id="9410b-109">L' Blazor application est traitée par une application ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="9410b-109">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="9410b-110">Cette stratégie est abordée dans la section [Déploiement hébergé avec ASP.NET Core](#hosted-deployment-with-aspnet-core).</span><span class="sxs-lookup"><span data-stu-id="9410b-110">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>
* <span data-ttu-id="9410b-111">L' Blazor application est placée sur un service ou un serveur Web d’hébergement statique, où .net n’est pas utilisé pour traiter l' Blazor application.</span><span class="sxs-lookup"><span data-stu-id="9410b-111">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span> <span data-ttu-id="9410b-112">Cette stratégie est traitée dans la section [Déploiement autonome](#standalone-deployment) , qui comprend des informations sur l’hébergement d’une Blazor WebAssembly application en tant que sous-application IIS.</span><span class="sxs-lookup"><span data-stu-id="9410b-112">This strategy is covered in the [Standalone deployment](#standalone-deployment) section, which includes information on hosting a Blazor WebAssembly app as an IIS sub-app.</span></span>

## <a name="compression"></a><span data-ttu-id="9410b-113">Compression</span><span class="sxs-lookup"><span data-stu-id="9410b-113">Compression</span></span>

<span data-ttu-id="9410b-114">Quand une Blazor WebAssembly application est publiée, la sortie est compressée de manière statique lors de la publication afin de réduire la taille de l’application et de supprimer la surcharge liée à la compression du Runtime.</span><span class="sxs-lookup"><span data-stu-id="9410b-114">When a Blazor WebAssembly app is published, the output is statically compressed during publish to reduce the app's size and remove the overhead for runtime compression.</span></span> <span data-ttu-id="9410b-115">Les algorithmes de compression suivants sont utilisés :</span><span class="sxs-lookup"><span data-stu-id="9410b-115">The following compression algorithms are used:</span></span>

* <span data-ttu-id="9410b-116">[Brotli](https://tools.ietf.org/html/rfc7932) (niveau le plus élevé)</span><span class="sxs-lookup"><span data-stu-id="9410b-116">[Brotli](https://tools.ietf.org/html/rfc7932) (highest level)</span></span>
* [<span data-ttu-id="9410b-117">Gzip</span><span class="sxs-lookup"><span data-stu-id="9410b-117">Gzip</span></span>](https://tools.ietf.org/html/rfc1952)

Blazor<span data-ttu-id="9410b-118">s’appuie sur l’hôte pour servir les fichiers compressés appropriés.</span><span class="sxs-lookup"><span data-stu-id="9410b-118"> relies on the host to the serve the appropriate compressed files.</span></span> <span data-ttu-id="9410b-119">Lors de l’utilisation d’un projet hébergé ASP.NET Core, le projet hôte peut effectuer la négociation de contenu et traiter les fichiers compressés statiquement.</span><span class="sxs-lookup"><span data-stu-id="9410b-119">When using an ASP.NET Core hosted project, the host project is capable of performing content negotiation and serving the statically-compressed files.</span></span> <span data-ttu-id="9410b-120">Lors de l’hébergement d’une Blazor WebAssembly application autonome, un travail supplémentaire peut être nécessaire pour s’assurer que les fichiers compressés statiquement sont pris en charge :</span><span class="sxs-lookup"><span data-stu-id="9410b-120">When hosting a Blazor WebAssembly standalone app, additional work might be required to ensure that statically-compressed files are served:</span></span>

* <span data-ttu-id="9410b-121">Pour `web.config` la configuration de la compression IIS, consultez la section [IIS : Brotli et compression gzip](#brotli-and-gzip-compression) .</span><span class="sxs-lookup"><span data-stu-id="9410b-121">For IIS `web.config` compression configuration, see the [IIS: Brotli and Gzip compression](#brotli-and-gzip-compression) section.</span></span> 
* <span data-ttu-id="9410b-122">Lors de l’hébergement sur des solutions d’hébergement statiques qui ne prennent pas en charge la négociation de contenu de fichier compressée statiquement, telles que les pages GitHub, envisagez de configurer l’application pour extraire et décoder les fichiers compressés Brotli :</span><span class="sxs-lookup"><span data-stu-id="9410b-122">When hosting on static hosting solutions that don't support statically-compressed file content negotiation, such as GitHub Pages, consider configuring the app to fetch and decode Brotli compressed files:</span></span>

  * <span data-ttu-id="9410b-123">Référencez le décodeur Brotli à partir du [référentiel GitHub Google/Brotli](https://github.com/google/brotli/) dans l’application.</span><span class="sxs-lookup"><span data-stu-id="9410b-123">Reference the Brotli decoder from the [google/brotli GitHub repository](https://github.com/google/brotli/) in the app.</span></span>
  * <span data-ttu-id="9410b-124">Mettez à jour l’application pour utiliser le décodeur.</span><span class="sxs-lookup"><span data-stu-id="9410b-124">Update the app to use the decoder.</span></span> <span data-ttu-id="9410b-125">Remplacez le balisage `<body>` dans la balise de fermeture par `wwwroot/index.html` ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="9410b-125">Change the markup inside the the closing `<body>` tag in `wwwroot/index.html` to the following:</span></span>
  
    ```html
    <script src="brotli.decode.min.js"></script>
    <script src="_framework/blazor.webassembly.js" autostart="false"></script>
    <script>
    Blazor.start({
      loadBootResource: function (type, name, defaultUri, integrity) {
        if (type !== 'dotnetjs' && location.hostname !== 'localhost') {
          return (async function () {
            const response = await fetch(defaultUri + '.br', { cache: 'no-cache' });
            if (!response.ok) {
              throw new Error(response.statusText);
            }
            const originalResponseBuffer = await response.arrayBuffer();
            const originalResponseArray = new Int8Array(originalResponseBuffer);
            const decompressedResponseArray = BrotliDecode(originalResponseArray);
            const contentType = type === 
              'dotnetwasm' ? 'application/wasm' : 'application/octet-stream';
            return new Response(decompressedResponseArray, 
              { headers: { 'content-type': contentType } });
          })();
        }
      }
    });
    </script>
    ```
 
<span data-ttu-id="9410b-126">Pour désactiver la compression, ajoutez la `BlazorEnableCompression` propriété MSBuild au fichier projet de l’application et définissez la valeur sur `false` :</span><span class="sxs-lookup"><span data-stu-id="9410b-126">To disable compression, add the `BlazorEnableCompression` MSBuild property to the app's project file and set the value to `false`:</span></span>

```xml
<PropertyGroup>
  <BlazorEnableCompression>false</BlazorEnableCompression>
</PropertyGroup>
```

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="9410b-127">Réécriture d’URL pour un routage correct</span><span class="sxs-lookup"><span data-stu-id="9410b-127">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="9410b-128">Le routage des requêtes pour les composants de page dans une Blazor WebAssembly application n’est pas aussi simple que les demandes de routage dans une Blazor Server application hébergée.</span><span class="sxs-lookup"><span data-stu-id="9410b-128">Routing requests for page components in a Blazor WebAssembly app isn't as straightforward as routing requests in a Blazor Server, hosted app.</span></span> <span data-ttu-id="9410b-129">Prenons l’exemple d’une Blazor WebAssembly application avec deux composants :</span><span class="sxs-lookup"><span data-stu-id="9410b-129">Consider a Blazor WebAssembly app with two components:</span></span>

* <span data-ttu-id="9410b-130">`Main.razor`: Charge à la racine de l’application et contient un lien vers le `About` composant ( `href="About"` ).</span><span class="sxs-lookup"><span data-stu-id="9410b-130">`Main.razor`: Loads at the root of the app and contains a link to the `About` component (`href="About"`).</span></span>
* <span data-ttu-id="9410b-131">`About.razor`: `About` composant.</span><span class="sxs-lookup"><span data-stu-id="9410b-131">`About.razor`: `About` component.</span></span>

<span data-ttu-id="9410b-132">Quand le document par défaut de l’application est demandé à l’aide de la barre d’adresses du navigateur (par exemple, `https://www.contoso.com/`) :</span><span class="sxs-lookup"><span data-stu-id="9410b-132">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="9410b-133">Le navigateur effectue une requête.</span><span class="sxs-lookup"><span data-stu-id="9410b-133">The browser makes a request.</span></span>
1. <span data-ttu-id="9410b-134">La page par défaut est retournée, qui est généralement `index.html` .</span><span class="sxs-lookup"><span data-stu-id="9410b-134">The default page is returned, which is usually `index.html`.</span></span>
1. <span data-ttu-id="9410b-135">`index.html`amorce l’application.</span><span class="sxs-lookup"><span data-stu-id="9410b-135">`index.html` bootstraps the app.</span></span>
1. Blazor<span data-ttu-id="9410b-136">le routeur est chargé et le Razor `Main` composant est rendu.</span><span class="sxs-lookup"><span data-stu-id="9410b-136">'s router loads, and the Razor `Main` component is rendered.</span></span>

<span data-ttu-id="9410b-137">Dans la page principale, le fait de sélectionner le lien vers le `About` composant fonctionne sur le client car le Blazor routeur empêche le navigateur d’effectuer une requête sur Internet pour `www.contoso.com` `About` et sert le `About` composant rendu lui-même.</span><span class="sxs-lookup"><span data-stu-id="9410b-137">In the Main page, selecting the link to the `About` component works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the rendered `About` component itself.</span></span> <span data-ttu-id="9410b-138">Toutes les demandes de points de terminaison internes *au sein de l' Blazor WebAssembly application* fonctionnent de la même façon : les demandes ne déclenchent pas de requêtes basées sur un navigateur vers des ressources hébergées sur le serveur sur Internet.</span><span class="sxs-lookup"><span data-stu-id="9410b-138">All of the requests for internal endpoints *within the Blazor WebAssembly app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="9410b-139">Le routeur gère les requêtes en interne.</span><span class="sxs-lookup"><span data-stu-id="9410b-139">The router handles the requests internally.</span></span>

<span data-ttu-id="9410b-140">Si une requête pour `www.contoso.com/About` est effectuée à l’aide de la barre d’adresses du navigateur, elle échoue.</span><span class="sxs-lookup"><span data-stu-id="9410b-140">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="9410b-141">Comme cette ressource n’existe pas sur l’hôte Internet de l’application, une réponse *404 – Non trouvé* est retournée.</span><span class="sxs-lookup"><span data-stu-id="9410b-141">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="9410b-142">Étant donné que les navigateurs effectuent des demandes aux hôtes basés sur Internet pour les pages côté client, les serveurs Web et les services d’hébergement doivent réécrire toutes les demandes de ressources qui ne sont pas physiquement sur le serveur sur la `index.html` page.</span><span class="sxs-lookup"><span data-stu-id="9410b-142">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the `index.html` page.</span></span> <span data-ttu-id="9410b-143">Lorsque `index.html` est retourné, le routeur de l’application Blazor prend le relais et répond avec la ressource correcte.</span><span class="sxs-lookup"><span data-stu-id="9410b-143">When `index.html` is returned, the app's Blazor router takes over and responds with the correct resource.</span></span>

<span data-ttu-id="9410b-144">Lors du déploiement sur un serveur IIS, vous pouvez utiliser le module de réécriture d’URL avec le fichier publié de l’application `web.config` .</span><span class="sxs-lookup"><span data-stu-id="9410b-144">When deploying to an IIS server, you can use the URL Rewrite Module with the app's published `web.config` file.</span></span> <span data-ttu-id="9410b-145">Pour plus d’informations, consultez la section [IIS](#iis) .</span><span class="sxs-lookup"><span data-stu-id="9410b-145">For more information, see the [IIS](#iis) section.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="9410b-146">Déploiement hébergé avec ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9410b-146">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="9410b-147">Un *déploiement hébergé* sert l' Blazor WebAssembly application aux navigateurs à partir d’une [application ASP.net Core](xref:index) qui s’exécute sur un serveur Web.</span><span class="sxs-lookup"><span data-stu-id="9410b-147">A *hosted deployment* serves the Blazor WebAssembly app to browsers from an [ASP.NET Core app](xref:index) that runs on a web server.</span></span>

<span data-ttu-id="9410b-148">L’application cliente Blazor WebAssembly est publiée dans le `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` dossier de l’application serveur, ainsi que les autres ressources Web statiques de l’application serveur.</span><span class="sxs-lookup"><span data-stu-id="9410b-148">The client Blazor WebAssembly app is published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="9410b-149">Les deux applications sont déployées ensemble.</span><span class="sxs-lookup"><span data-stu-id="9410b-149">The two apps are deployed together.</span></span> <span data-ttu-id="9410b-150">Un serveur web capable d’héberger une application ASP.NET Core est nécessaire.</span><span class="sxs-lookup"><span data-stu-id="9410b-150">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="9410b-151">Pour un déploiement hébergé, Visual Studio comprend le modèle de projet d' \*\* Blazor WebAssembly application\*\* ( `blazorwasm` modèle lors de l’utilisation de la [`dotnet new`](/dotnet/core/tools/dotnet-new) commande) avec l' **`Hosted`** option sélectionnée (lors de l' `-ho|--hosted` utilisation de la `dotnet new` commande).</span><span class="sxs-lookup"><span data-stu-id="9410b-151">For a hosted deployment, Visual Studio includes the **Blazor WebAssembly App** project template (`blazorwasm` template when using the [`dotnet new`](/dotnet/core/tools/dotnet-new) command) with the **`Hosted`** option selected (`-ho|--hosted` when using the `dotnet new` command).</span></span>

<span data-ttu-id="9410b-152">Pour plus d’informations sur l’hébergement et le déploiement d’applications ASP.NET Core, consultez <xref:host-and-deploy/index>.</span><span class="sxs-lookup"><span data-stu-id="9410b-152">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="9410b-153">Pour plus d’informations concernant le déploiement sur Azure App Service, consultez <xref:tutorials/publish-to-azure-webapp-using-vs>.</span><span class="sxs-lookup"><span data-stu-id="9410b-153">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

## <a name="standalone-deployment"></a><span data-ttu-id="9410b-154">Déploiement autonome</span><span class="sxs-lookup"><span data-stu-id="9410b-154">Standalone deployment</span></span>

<span data-ttu-id="9410b-155">Un *Déploiement autonome* sert l' Blazor WebAssembly application sous la forme d’un ensemble de fichiers statiques demandés directement par les clients.</span><span class="sxs-lookup"><span data-stu-id="9410b-155">A *standalone deployment* serves the Blazor WebAssembly app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="9410b-156">Tout serveur de fichiers statique peut traiter l' Blazor application.</span><span class="sxs-lookup"><span data-stu-id="9410b-156">Any static file server is able to serve the Blazor app.</span></span>

<span data-ttu-id="9410b-157">Les ressources de déploiement autonomes sont publiées dans le `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` dossier.</span><span class="sxs-lookup"><span data-stu-id="9410b-157">Standalone deployment assets are published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="9410b-158">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="9410b-158">Azure App Service</span></span>

Blazor WebAssembly<span data-ttu-id="9410b-159">les applications peuvent être déployées sur Azure App services sur Windows, qui héberge l’application sur [IIS](#iis).</span><span class="sxs-lookup"><span data-stu-id="9410b-159"> apps can be deployed to Azure App Services on Windows, which hosts the app on [IIS](#iis).</span></span>

<span data-ttu-id="9410b-160">Le déploiement d’une Blazor WebAssembly application autonome sur Azure App service pour Linux n’est pas pris en charge actuellement.</span><span class="sxs-lookup"><span data-stu-id="9410b-160">Deploying a standalone Blazor WebAssembly app to Azure App Service for Linux isn't currently supported.</span></span> <span data-ttu-id="9410b-161">Une image de serveur Linux pour héberger l’application n’est pas disponible pour l’instant.</span><span class="sxs-lookup"><span data-stu-id="9410b-161">A Linux server image to host the app isn't available at this time.</span></span> <span data-ttu-id="9410b-162">Le travail est en cours pour activer ce scénario.</span><span class="sxs-lookup"><span data-stu-id="9410b-162">Work is in progress to enable this scenario.</span></span>

### <a name="iis"></a><span data-ttu-id="9410b-163">IIS</span><span class="sxs-lookup"><span data-stu-id="9410b-163">IIS</span></span>

<span data-ttu-id="9410b-164">IIS est un serveur de fichiers statiques et puissant pour les Blazor applications.</span><span class="sxs-lookup"><span data-stu-id="9410b-164">IIS is a capable static file server for Blazor apps.</span></span> <span data-ttu-id="9410b-165">Pour configurer IIS pour héberger Blazor , consultez [créer un site Web statique sur IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span><span class="sxs-lookup"><span data-stu-id="9410b-165">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="9410b-166">Les ressources publiées sont créées dans le `/bin/Release/{TARGET FRAMEWORK}/publish` dossier.</span><span class="sxs-lookup"><span data-stu-id="9410b-166">Published assets are created in the `/bin/Release/{TARGET FRAMEWORK}/publish` folder.</span></span> <span data-ttu-id="9410b-167">Hébergez le contenu du `publish` dossier sur le serveur Web ou le service d’hébergement.</span><span class="sxs-lookup"><span data-stu-id="9410b-167">Host the contents of the `publish` folder on the web server or hosting service.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="9410b-168">web.config</span><span class="sxs-lookup"><span data-stu-id="9410b-168">web.config</span></span>

<span data-ttu-id="9410b-169">Lorsqu’un Blazor projet est publié, un `web.config` fichier est créé avec la configuration IIS suivante :</span><span class="sxs-lookup"><span data-stu-id="9410b-169">When a Blazor project is published, a `web.config` file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="9410b-170">Les types MIME sont définis pour les extensions de fichiers suivantes :</span><span class="sxs-lookup"><span data-stu-id="9410b-170">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="9410b-171">`.dll`: `application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="9410b-171">`.dll`: `application/octet-stream`</span></span>
  * <span data-ttu-id="9410b-172">`.json`: `application/json`</span><span class="sxs-lookup"><span data-stu-id="9410b-172">`.json`: `application/json`</span></span>
  * <span data-ttu-id="9410b-173">`.wasm`: `application/wasm`</span><span class="sxs-lookup"><span data-stu-id="9410b-173">`.wasm`: `application/wasm`</span></span>
  * <span data-ttu-id="9410b-174">`.woff`: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="9410b-174">`.woff`: `application/font-woff`</span></span>
  * <span data-ttu-id="9410b-175">`.woff2`: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="9410b-175">`.woff2`: `application/font-woff`</span></span>
* <span data-ttu-id="9410b-176">La compression HTTP est activée pour les types MIME suivants :</span><span class="sxs-lookup"><span data-stu-id="9410b-176">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="9410b-177">Des règles du module de réécriture d’URL sont établies :</span><span class="sxs-lookup"><span data-stu-id="9410b-177">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="9410b-178">Servez-vous du sous-répertoire dans lequel résident les ressources statiques de l’application ( `wwwroot/{PATH REQUESTED}` ).</span><span class="sxs-lookup"><span data-stu-id="9410b-178">Serve the sub-directory where the app's static assets reside (`wwwroot/{PATH REQUESTED}`).</span></span>
  * <span data-ttu-id="9410b-179">Créez le routage de secours SPA afin que les demandes pour les ressources non-fichier soient redirigées vers le document par défaut de l’application dans son dossier ressources statiques ( `wwwroot/index.html` ).</span><span class="sxs-lookup"><span data-stu-id="9410b-179">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (`wwwroot/index.html`).</span></span>
  
#### <a name="use-a-custom-webconfig"></a><span data-ttu-id="9410b-180">Utiliser un web.config personnalisé</span><span class="sxs-lookup"><span data-stu-id="9410b-180">Use a custom web.config</span></span>

<span data-ttu-id="9410b-181">Pour utiliser un `web.config` fichier personnalisé, placez le `web.config` fichier personnalisé à la racine du dossier du projet et publiez le projet.</span><span class="sxs-lookup"><span data-stu-id="9410b-181">To use a custom `web.config` file, place the custom `web.config` file at the root of the project folder and publish the project.</span></span>

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="9410b-182">Installer le module de réécriture d’URL</span><span class="sxs-lookup"><span data-stu-id="9410b-182">Install the URL Rewrite Module</span></span>

<span data-ttu-id="9410b-183">Le [module de réécriture d’URL](https://www.iis.net/downloads/microsoft/url-rewrite) est nécessaire pour réécrire les URL.</span><span class="sxs-lookup"><span data-stu-id="9410b-183">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="9410b-184">Il n’est pas installé par défaut, et ne peut pas l’être en tant que fonctionnalité de service de rôle Serveur Web (IIS).</span><span class="sxs-lookup"><span data-stu-id="9410b-184">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span> <span data-ttu-id="9410b-185">Vous devez le télécharger à partir du site web IIS.</span><span class="sxs-lookup"><span data-stu-id="9410b-185">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="9410b-186">Utilisez Web Platform Installer pour installer le module :</span><span class="sxs-lookup"><span data-stu-id="9410b-186">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="9410b-187">Localement, accédez à la [page des téléchargements du Module de réécriture d’URL](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span><span class="sxs-lookup"><span data-stu-id="9410b-187">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="9410b-188">Pour la version anglaise, sélectionnez **WebPI** pour télécharger le programme d’installation WebPI.</span><span class="sxs-lookup"><span data-stu-id="9410b-188">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="9410b-189">Pour les autres langues, sélectionnez l’architecture appropriée pour le serveur (x86/x64) afin de télécharger le programme d’installation.</span><span class="sxs-lookup"><span data-stu-id="9410b-189">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="9410b-190">Copiez le programme d’installation sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="9410b-190">Copy the installer to the server.</span></span> <span data-ttu-id="9410b-191">Exécutez le programme d’installation.</span><span class="sxs-lookup"><span data-stu-id="9410b-191">Run the installer.</span></span> <span data-ttu-id="9410b-192">Sélectionnez le bouton **Installer** et acceptez les termes du contrat de licence.</span><span class="sxs-lookup"><span data-stu-id="9410b-192">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="9410b-193">Il n’est pas nécessaire de redémarrer le serveur après l’installation.</span><span class="sxs-lookup"><span data-stu-id="9410b-193">A server restart isn't required after the install completes.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="9410b-194">Configurer le site web</span><span class="sxs-lookup"><span data-stu-id="9410b-194">Configure the website</span></span>

<span data-ttu-id="9410b-195">Affectez le dossier de l’application comme **chemin physique** du site web.</span><span class="sxs-lookup"><span data-stu-id="9410b-195">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="9410b-196">Le dossier contient les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="9410b-196">The folder contains:</span></span>

* <span data-ttu-id="9410b-197">`web.config`Fichier utilisé par IIS pour configurer le site Web, y compris les règles de redirection et les types de contenu de fichier requis.</span><span class="sxs-lookup"><span data-stu-id="9410b-197">The `web.config` file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="9410b-198">Le dossier de ressources statiques de l’application</span><span class="sxs-lookup"><span data-stu-id="9410b-198">The app's static asset folder.</span></span>

#### <a name="host-as-an-iis-sub-app"></a><span data-ttu-id="9410b-199">Héberger en tant que sous-application IIS</span><span class="sxs-lookup"><span data-stu-id="9410b-199">Host as an IIS sub-app</span></span>

<span data-ttu-id="9410b-200">Si une application autonome est hébergée en tant que sous-application IIS, effectuez l’une des opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="9410b-200">If a standalone app is hosted as an IIS sub-app, perform either of the following:</span></span>

* <span data-ttu-id="9410b-201">Désactivez le gestionnaire de module ASP.NET Core hérité.</span><span class="sxs-lookup"><span data-stu-id="9410b-201">Disable the inherited ASP.NET Core Module handler.</span></span>

  <span data-ttu-id="9410b-202">Supprimez le gestionnaire dans le Blazor fichier publié de l’application `web.config` en ajoutant une `<handlers>` section au fichier :</span><span class="sxs-lookup"><span data-stu-id="9410b-202">Remove the handler in the Blazor app's published `web.config` file by adding a `<handlers>` section to the file:</span></span>

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* <span data-ttu-id="9410b-203">Désactivez l’héritage de la section de l’application racine (parent) `<system.webServer>` à l’aide d’un `<location>` élément dont la `inheritInChildApplications` valeur est `false` :</span><span class="sxs-lookup"><span data-stu-id="9410b-203">Disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

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

<span data-ttu-id="9410b-204">La suppression du gestionnaire ou la désactivation de l’héritage est effectuée en plus de [la configuration du chemin d’accès de base de l’application](xref:blazor/host-and-deploy/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="9410b-204">Removing the handler or disabling inheritance is performed in addition to [configuring the app's base path](xref:blazor/host-and-deploy/index#app-base-path).</span></span> <span data-ttu-id="9410b-205">Définissez le chemin d’accès de base de l’application dans le fichier de l’application `index.html` sur l’alias IIS utilisé lors de la configuration de la sous-application dans IIS.</span><span class="sxs-lookup"><span data-stu-id="9410b-205">Set the app base path in the app's `index.html` file to the IIS alias used when configuring the sub-app in IIS.</span></span>

#### <a name="brotli-and-gzip-compression"></a><span data-ttu-id="9410b-206">Compression Brotli et gzip</span><span class="sxs-lookup"><span data-stu-id="9410b-206">Brotli and Gzip compression</span></span>

<span data-ttu-id="9410b-207">IIS peut être configuré via `web.config` pour servir des ressources compressées Brotli ou gzip Blazor .</span><span class="sxs-lookup"><span data-stu-id="9410b-207">IIS can be configured via `web.config` to serve Brotli or Gzip compressed Blazor assets.</span></span> <span data-ttu-id="9410b-208">Pour obtenir un exemple de configuration, consultez [`web.config`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/web.config?raw=true) .</span><span class="sxs-lookup"><span data-stu-id="9410b-208">For an example configuration, see [`web.config`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/web.config?raw=true).</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="9410b-209">Résolution des problèmes</span><span class="sxs-lookup"><span data-stu-id="9410b-209">Troubleshooting</span></span>

<span data-ttu-id="9410b-210">Si vous recevez un message *500 – Erreur interne du serveur* et que le Gestionnaire IIS lève des erreurs quand vous tentez d’accéder à la configuration du site web, vérifiez que le module de réécriture d’URL est installé.</span><span class="sxs-lookup"><span data-stu-id="9410b-210">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="9410b-211">Lorsque le module n’est pas installé, le `web.config` fichier ne peut pas être analysé par IIS.</span><span class="sxs-lookup"><span data-stu-id="9410b-211">When the module isn't installed, the `web.config` file can't be parsed by IIS.</span></span> <span data-ttu-id="9410b-212">Cela empêche le gestionnaire des services Internet de charger la configuration du site Web et le site Web à partir des Blazor fichiers statiques de service.</span><span class="sxs-lookup"><span data-stu-id="9410b-212">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

<span data-ttu-id="9410b-213">Pour plus d’informations sur le dépannage des déploiements sur IIS, consultez <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="9410b-213">For more information on troubleshooting deployments to IIS, see <xref:test/troubleshoot-azure-iis>.</span></span>

### <a name="azure-storage"></a><span data-ttu-id="9410b-214">Stockage Azure</span><span class="sxs-lookup"><span data-stu-id="9410b-214">Azure Storage</span></span>

<span data-ttu-id="9410b-215">L’hébergement de fichiers statiques [Azure Storage](/azure/storage/) permet l’hébergement d’applications sans serveur Blazor .</span><span class="sxs-lookup"><span data-stu-id="9410b-215">[Azure Storage](/azure/storage/) static file hosting allows serverless Blazor app hosting.</span></span> <span data-ttu-id="9410b-216">Les noms de domaine personnalisé, le réseau de distribution de contenu Azure (CDN) et HTTPS sont pris en charge.</span><span class="sxs-lookup"><span data-stu-id="9410b-216">Custom domain names, the Azure Content Delivery Network (CDN), and HTTPS are supported.</span></span>

<span data-ttu-id="9410b-217">Lorsque le service blob est activé pour l’hébergement de site Web statique sur un compte de stockage :</span><span class="sxs-lookup"><span data-stu-id="9410b-217">When the blob service is enabled for static website hosting on a storage account:</span></span>

* <span data-ttu-id="9410b-218">Définissez le **nom du document d’index** sur `index.html`.</span><span class="sxs-lookup"><span data-stu-id="9410b-218">Set the **Index document name** to `index.html`.</span></span>
* <span data-ttu-id="9410b-219">Définissez le **chemin d’accès au document d’erreur** sur `index.html`.</span><span class="sxs-lookup"><span data-stu-id="9410b-219">Set the **Error document path** to `index.html`.</span></span> Razor<span data-ttu-id="9410b-220">les composants et autres points de terminaison non-fichier ne résident pas sur des chemins d’accès physiques dans le contenu statique stocké par le service BLOB.</span><span class="sxs-lookup"><span data-stu-id="9410b-220"> components and other non-file endpoints don't reside at physical paths in the static content stored by the blob service.</span></span> <span data-ttu-id="9410b-221">Lorsqu’une demande pour l’une de ces ressources est reçue que le Blazor routeur doit gérer, l’erreur *404-introuvable* générée par le service BLOB achemine la requête vers le **chemin du document d’erreur**.</span><span class="sxs-lookup"><span data-stu-id="9410b-221">When a request for one of these resources is received that the Blazor router should handle, the *404 - Not Found* error generated by the blob service routes the request to the **Error document path**.</span></span> <span data-ttu-id="9410b-222">L' `index.html` objet blob est retourné et le Blazor routeur charge et traite le chemin d’accès.</span><span class="sxs-lookup"><span data-stu-id="9410b-222">The `index.html` blob is returned, and the Blazor router loads and processes the path.</span></span>

<span data-ttu-id="9410b-223">Si les fichiers ne sont pas chargés au moment de l’exécution en raison de types MIME inappropriés dans les `Content-Type` en-têtes des fichiers, effectuez l’une des actions suivantes :</span><span class="sxs-lookup"><span data-stu-id="9410b-223">If files aren't loaded at runtime due to inappropriate MIME types in the files' `Content-Type` headers, take either of the following actions:</span></span>

* <span data-ttu-id="9410b-224">Configurez vos outils pour définir les types MIME corrects ( `Content-Type` en-têtes) lors du déploiement des fichiers.</span><span class="sxs-lookup"><span data-stu-id="9410b-224">Configure your tooling to set the correct MIME types (`Content-Type` headers) when the files are deployed.</span></span>
* <span data-ttu-id="9410b-225">Modifiez les types MIME ( `Content-Type` en-têtes) des fichiers après le déploiement de l’application.</span><span class="sxs-lookup"><span data-stu-id="9410b-225">Change the MIME types (`Content-Type` headers) for the files after the app is deployed.</span></span>

  <span data-ttu-id="9410b-226">En Explorateur Stockage (Portail Azure) pour chaque fichier :</span><span class="sxs-lookup"><span data-stu-id="9410b-226">In Storage Explorer (Azure portal) for each file:</span></span>
  
  1. <span data-ttu-id="9410b-227">Cliquez avec le bouton droit sur le fichier et sélectionnez **Propriétés**.</span><span class="sxs-lookup"><span data-stu-id="9410b-227">Right-click the file and select **Properties**.</span></span>
  1. <span data-ttu-id="9410b-228">Définissez le **ContentType** et sélectionnez le bouton **Enregistrer** .</span><span class="sxs-lookup"><span data-stu-id="9410b-228">Set the **ContentType** and select the **Save** button.</span></span>

<span data-ttu-id="9410b-229">Pour plus d’informations, consultez [Hébergement de sites web statiques dans le service Stockage Azure](/azure/storage/blobs/storage-blob-static-website).</span><span class="sxs-lookup"><span data-stu-id="9410b-229">For more information, see [Static website hosting in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span></span>

### <a name="nginx"></a><span data-ttu-id="9410b-230">Nginx</span><span class="sxs-lookup"><span data-stu-id="9410b-230">Nginx</span></span>

<span data-ttu-id="9410b-231">Le `nginx.conf` fichier suivant est simplifié pour montrer comment configurer Nginx pour envoyer le `index.html` fichier lorsqu’il ne peut pas trouver de fichier correspondant sur le disque.</span><span class="sxs-lookup"><span data-stu-id="9410b-231">The following `nginx.conf` file is simplified to show how to configure Nginx to send the `index.html` file whenever it can't find a corresponding file on disk.</span></span>

```
events { }
http {
    server {
        listen 80;

        location / {
            root      /usr/share/nginx/html;
            try_files $uri $uri/ /index.html =404;
        }
    }
}
```

<span data-ttu-id="9410b-232">Lors de la définition de la [limite de débit de rafales Nginx](https://www.nginx.com/blog/rate-limiting-nginx/#bursts) avec [`limit_req`](https://nginx.org/docs/http/ngx_http_limit_req_module.html#limit_req) , Blazor WebAssembly les applications peuvent nécessiter une `burst` valeur de paramètre élevée pour prendre en charge le nombre relativement élevé de demandes effectuées par une application.</span><span class="sxs-lookup"><span data-stu-id="9410b-232">When setting the [NGINX burst rate limit](https://www.nginx.com/blog/rate-limiting-nginx/#bursts) with [`limit_req`](https://nginx.org/docs/http/ngx_http_limit_req_module.html#limit_req), Blazor WebAssembly apps may require a large `burst` parameter value to accommodate the relatively large number of requests made by an app.</span></span> <span data-ttu-id="9410b-233">Au départ, définissez la valeur sur au moins 60 :</span><span class="sxs-lookup"><span data-stu-id="9410b-233">Initially, set the value to at least 60:</span></span>

```
http {
    server {
        ...

        location / {
            ...

            limit_req zone=one burst=60 nodelay;
        }
    }
}
```

<span data-ttu-id="9410b-234">Augmentez la valeur si outils de développement de navigateur ou outil de trafic réseau indique que les demandes reçoivent un code d’état *503-Service non disponible* .</span><span class="sxs-lookup"><span data-stu-id="9410b-234">Increase the value if browser developer tools or a network traffic tool indicates that requests are receiving a *503 - Service Unavailable* status code.</span></span>

<span data-ttu-id="9410b-235">Pour plus d’informations sur la configuration du serveur web Nginx de production, consultez [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/) (Création de fichiers de configuration NGINX et NGINX Plus).</span><span class="sxs-lookup"><span data-stu-id="9410b-235">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

### <a name="nginx-in-docker"></a><span data-ttu-id="9410b-236">Nginx dans Docker</span><span class="sxs-lookup"><span data-stu-id="9410b-236">Nginx in Docker</span></span>

<span data-ttu-id="9410b-237">Pour héberger Blazor dans l’ancrage à l’aide de nginx, configurez le fichier dockerfile pour utiliser l’image Nginx alpine.</span><span class="sxs-lookup"><span data-stu-id="9410b-237">To host Blazor in Docker using Nginx, setup the Dockerfile to use the Alpine-based Nginx image.</span></span> <span data-ttu-id="9410b-238">Mettez à jour le fichier dockerfile pour copier le `nginx.config` fichier dans le conteneur.</span><span class="sxs-lookup"><span data-stu-id="9410b-238">Update the Dockerfile to copy the `nginx.config` file into the container.</span></span>

<span data-ttu-id="9410b-239">Ajoutez une ligne au fichier Dockerfile, comme indiqué dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="9410b-239">Add one line to the Dockerfile, as shown in the following example:</span></span>

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a><span data-ttu-id="9410b-240">Apache</span><span class="sxs-lookup"><span data-stu-id="9410b-240">Apache</span></span>

<span data-ttu-id="9410b-241">Pour déployer une Blazor WebAssembly application sur CentOS 7 ou version ultérieure :</span><span class="sxs-lookup"><span data-stu-id="9410b-241">To deploy a Blazor WebAssembly app to CentOS 7 or later:</span></span>

1. <span data-ttu-id="9410b-242">Créez le fichier de configuration Apache.</span><span class="sxs-lookup"><span data-stu-id="9410b-242">Create the Apache configuration file.</span></span> <span data-ttu-id="9410b-243">L’exemple suivant est un fichier de configuration simplifié ( `blazorapp.config` ) :</span><span class="sxs-lookup"><span data-stu-id="9410b-243">The following example is a simplified configuration file (`blazorapp.config`):</span></span>

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

1. <span data-ttu-id="9410b-244">Placez le fichier de configuration Apache dans le `/etc/httpd/conf.d/` répertoire, qui est le répertoire de configuration Apache par défaut dans CentOS 7.</span><span class="sxs-lookup"><span data-stu-id="9410b-244">Place the Apache configuration file into the `/etc/httpd/conf.d/` directory, which is the default Apache configuration directory in CentOS 7.</span></span>

1. <span data-ttu-id="9410b-245">Placez les fichiers de l’application dans le `/var/www/blazorapp` répertoire (l’emplacement spécifié `DocumentRoot` dans le fichier de configuration).</span><span class="sxs-lookup"><span data-stu-id="9410b-245">Place the app's files into the `/var/www/blazorapp` directory (the location specified to `DocumentRoot` in the configuration file).</span></span>

1. <span data-ttu-id="9410b-246">Redémarrez le service Apache.</span><span class="sxs-lookup"><span data-stu-id="9410b-246">Restart the Apache service.</span></span>

<span data-ttu-id="9410b-247">Pour plus d’informations, consultez [`mod_mime`](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) et [`mod_deflate`](https://httpd.apache.org/docs/current/mod/mod_deflate.html) .</span><span class="sxs-lookup"><span data-stu-id="9410b-247">For more information, see [`mod_mime`](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) and [`mod_deflate`](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span></span>

### <a name="github-pages"></a><span data-ttu-id="9410b-248">GitHub Pages</span><span class="sxs-lookup"><span data-stu-id="9410b-248">GitHub Pages</span></span>

<span data-ttu-id="9410b-249">Pour gérer la réécriture d’URL, ajoutez un `404.html` fichier avec un script qui gère la redirection de la requête vers la `index.html` page.</span><span class="sxs-lookup"><span data-stu-id="9410b-249">To handle URL rewrites, add a `404.html` file with a script that handles redirecting the request to the `index.html` page.</span></span> <span data-ttu-id="9410b-250">Pour obtenir un exemple d’implémentation fournie par la communauté, consultez [Single Page Apps for GitHub Pages](https://spa-github-pages.rafrex.com/) (Applications à une seule page pour GitHub Pages) ([rafrex/spa-github-pages sur GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span><span class="sxs-lookup"><span data-stu-id="9410b-250">For an example implementation provided by the community, see [Single Page Apps for GitHub Pages](https://spa-github-pages.rafrex.com/) ([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span></span> <span data-ttu-id="9410b-251">Vous pouvez obtenir un exemple d’utilisation de cette approche à l’adresse [blazor-demo/blazor-demo.github.io sur GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([site actif](https://blazor-demo.github.io/)).</span><span class="sxs-lookup"><span data-stu-id="9410b-251">An example using the community approach can be seen at [blazor-demo/blazor-demo.github.io on GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([live site](https://blazor-demo.github.io/)).</span></span>

<span data-ttu-id="9410b-252">Lorsque vous utilisez un site de projet plutôt qu’un site d’organisation, ajoutez ou mettez à jour la `<base>` balise dans `index.html` .</span><span class="sxs-lookup"><span data-stu-id="9410b-252">When using a project site instead of an organization site, add or update the `<base>` tag in `index.html`.</span></span> <span data-ttu-id="9410b-253">Définissez la valeur d’attribut `href` sur le nom du dépôt GitHub avec une barre oblique (par exemple, `my-repository/`.</span><span class="sxs-lookup"><span data-stu-id="9410b-253">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `my-repository/`.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="9410b-254">Valeurs de configuration de l’hôte</span><span class="sxs-lookup"><span data-stu-id="9410b-254">Host configuration values</span></span>

<span data-ttu-id="9410b-255">les [ Blazor WebAssembly applications](xref:blazor/hosting-models#blazor-webassembly) peuvent accepter les valeurs de configuration d’hôte suivantes comme arguments de ligne de commande au moment de l’exécution dans l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="9410b-255">[Blazor WebAssembly apps](xref:blazor/hosting-models#blazor-webassembly) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="9410b-256">Racine de contenu</span><span class="sxs-lookup"><span data-stu-id="9410b-256">Content root</span></span>

<span data-ttu-id="9410b-257">L' `--contentroot` argument définit le chemin d’accès absolu au répertoire qui contient les fichiers de contenu de l’application ([racine du contenu](xref:fundamentals/index#content-root)).</span><span class="sxs-lookup"><span data-stu-id="9410b-257">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files ([content root](xref:fundamentals/index#content-root)).</span></span> <span data-ttu-id="9410b-258">Dans les exemples suivants, `/content-root-path` est le chemin racine du contenu de l’application.</span><span class="sxs-lookup"><span data-stu-id="9410b-258">In the following examples, `/content-root-path` is the app's content root path.</span></span>

* <span data-ttu-id="9410b-259">Passez l’argument lors de l’exécution de l’application localement à une invite de commandes.</span><span class="sxs-lookup"><span data-stu-id="9410b-259">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="9410b-260">À partir du répertoire de l’application, exécutez :</span><span class="sxs-lookup"><span data-stu-id="9410b-260">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="9410b-261">Ajoutez une entrée au fichier de l’application `launchSettings.json` dans le profil **IIS Express** .</span><span class="sxs-lookup"><span data-stu-id="9410b-261">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="9410b-262">Ce paramètre est utilisé en cas d’exécution de l’application avec le débogueur Visual Studio et dans une invite de commandes avec `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="9410b-262">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="9410b-263">Dans Visual Studio, spécifiez l’argument dans **Propriétés**  >  **Déboguer**les arguments de l'  >  **application**.</span><span class="sxs-lookup"><span data-stu-id="9410b-263">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="9410b-264">La définition de l’argument dans la page de propriétés de Visual Studio ajoute l’argument au `launchSettings.json` fichier.</span><span class="sxs-lookup"><span data-stu-id="9410b-264">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="9410b-265">Base du chemin</span><span class="sxs-lookup"><span data-stu-id="9410b-265">Path base</span></span>

<span data-ttu-id="9410b-266">L' `--pathbase` argument définit le chemin d’accès de base d’application pour une application exécutée localement avec un chemin d’URL relatif non racine (la `<base>` balise `href` est définie sur un chemin d’accès autre que `/` pour la mise en lots et la production).</span><span class="sxs-lookup"><span data-stu-id="9410b-266">The `--pathbase` argument sets the app base path for an app run locally with a non-root relative URL path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="9410b-267">Dans les exemples suivants, `/relative-URL-path` est la base du chemin de l’application.</span><span class="sxs-lookup"><span data-stu-id="9410b-267">In the following examples, `/relative-URL-path` is the app's path base.</span></span> <span data-ttu-id="9410b-268">Pour plus d’informations, consultez chemin de la base de l' [application](xref:blazor/host-and-deploy/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="9410b-268">For more information, see [App base path](xref:blazor/host-and-deploy/index#app-base-path).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="9410b-269">Contrairement au chemin fourni au `href` de la balise `<base>`, n’incluez pas de barre oblique (`/`) quand vous passez la valeur d’argument `--pathbase`.</span><span class="sxs-lookup"><span data-stu-id="9410b-269">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="9410b-270">Si vous spécifiez `<base href="/CoolApp/">` (inclut une barre oblique) comme chemin de base de l’application dans la balise `<base>`, passez `--pathbase=/CoolApp` (aucune barre oblique de fin) comme valeur d’argument de ligne de commande.</span><span class="sxs-lookup"><span data-stu-id="9410b-270">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="9410b-271">Passez l’argument lors de l’exécution de l’application localement à une invite de commandes.</span><span class="sxs-lookup"><span data-stu-id="9410b-271">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="9410b-272">À partir du répertoire de l’application, exécutez :</span><span class="sxs-lookup"><span data-stu-id="9410b-272">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* <span data-ttu-id="9410b-273">Ajoutez une entrée au fichier de l’application `launchSettings.json` dans le profil **IIS Express** .</span><span class="sxs-lookup"><span data-stu-id="9410b-273">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="9410b-274">Ce paramètre est utilisé en cas d’exécution de l’application avec le débogueur Visual Studio et dans une invite de commandes avec `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="9410b-274">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* <span data-ttu-id="9410b-275">Dans Visual Studio, spécifiez l’argument dans **Propriétés**  >  **Déboguer**les arguments de l'  >  **application**.</span><span class="sxs-lookup"><span data-stu-id="9410b-275">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="9410b-276">La définition de l’argument dans la page de propriétés de Visual Studio ajoute l’argument au `launchSettings.json` fichier.</span><span class="sxs-lookup"><span data-stu-id="9410b-276">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a><span data-ttu-id="9410b-277">URLs</span><span class="sxs-lookup"><span data-stu-id="9410b-277">URLs</span></span>

<span data-ttu-id="9410b-278">L’argument `--urls` définit les adresses IP ou les adresses d’hôtes avec les ports et protocoles sur lesquels il faut écouter les demandes.</span><span class="sxs-lookup"><span data-stu-id="9410b-278">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="9410b-279">Passez l’argument lors de l’exécution de l’application localement à une invite de commandes.</span><span class="sxs-lookup"><span data-stu-id="9410b-279">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="9410b-280">À partir du répertoire de l’application, exécutez :</span><span class="sxs-lookup"><span data-stu-id="9410b-280">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="9410b-281">Ajoutez une entrée au fichier de l’application `launchSettings.json` dans le profil **IIS Express** .</span><span class="sxs-lookup"><span data-stu-id="9410b-281">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="9410b-282">Ce paramètre est utilisé en cas d’exécution de l’application avec le débogueur Visual Studio et dans une invite de commandes avec `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="9410b-282">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="9410b-283">Dans Visual Studio, spécifiez l’argument dans **Propriétés**  >  **Déboguer**les arguments de l'  >  **application**.</span><span class="sxs-lookup"><span data-stu-id="9410b-283">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="9410b-284">La définition de l’argument dans la page de propriétés de Visual Studio ajoute l’argument au `launchSettings.json` fichier.</span><span class="sxs-lookup"><span data-stu-id="9410b-284">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="configure-the-linker"></a><span data-ttu-id="9410b-285">Configurer l'éditeur de liens</span><span class="sxs-lookup"><span data-stu-id="9410b-285">Configure the Linker</span></span>

Blazor<span data-ttu-id="9410b-286">effectue une liaison IL (Intermediate Language) sur chaque version de mise en production pour supprimer l’IL inutile des assemblys de sortie.</span><span class="sxs-lookup"><span data-stu-id="9410b-286"> performs Intermediate Language (IL) linking on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="9410b-287">Pour plus d’informations, consultez <xref:blazor/host-and-deploy/configure-linker>.</span><span class="sxs-lookup"><span data-stu-id="9410b-287">For more information, see <xref:blazor/host-and-deploy/configure-linker>.</span></span>

## <a name="custom-boot-resource-loading"></a><span data-ttu-id="9410b-288">Chargement des ressources de démarrage personnalisé</span><span class="sxs-lookup"><span data-stu-id="9410b-288">Custom boot resource loading</span></span>

<span data-ttu-id="9410b-289">Une Blazor WebAssembly application peut être initialisée avec la `loadBootResource` fonction pour remplacer le mécanisme de chargement des ressources de démarrage intégré.</span><span class="sxs-lookup"><span data-stu-id="9410b-289">A Blazor WebAssembly app can be initialized with the `loadBootResource` function to override the built-in boot resource loading mechanism.</span></span> <span data-ttu-id="9410b-290">Utilisez `loadBootResource` pour les scénarios suivants :</span><span class="sxs-lookup"><span data-stu-id="9410b-290">Use `loadBootResource` for the following scenarios:</span></span>

* <span data-ttu-id="9410b-291">Autorisez les utilisateurs à charger des ressources statiques, telles que des données de fuseau horaire ou `dotnet.wasm` à partir d’un CDN.</span><span class="sxs-lookup"><span data-stu-id="9410b-291">Allow users to load static resources, such as timezone data or `dotnet.wasm` from a CDN.</span></span>
* <span data-ttu-id="9410b-292">Chargez les assemblys compressés à l’aide d’une requête HTTP et décompressez-les sur le client pour les hôtes qui ne prennent pas en charge l’extraction du contenu compressé à partir du serveur.</span><span class="sxs-lookup"><span data-stu-id="9410b-292">Load compressed assemblies using an HTTP request and decompress them on the client for hosts that don't support fetching compressed contents from the server.</span></span>
* <span data-ttu-id="9410b-293">Alias des ressources à un autre nom en redirigeant chaque `fetch` requête vers un nouveau nom.</span><span class="sxs-lookup"><span data-stu-id="9410b-293">Alias resources to a different name by redirecting each `fetch` request to a new name.</span></span>

<span data-ttu-id="9410b-294">`loadBootResource`les paramètres s’affichent dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="9410b-294">`loadBootResource` parameters appear in the following table.</span></span>

| <span data-ttu-id="9410b-295">Paramètre</span><span class="sxs-lookup"><span data-stu-id="9410b-295">Parameter</span></span>    | <span data-ttu-id="9410b-296">Description</span><span class="sxs-lookup"><span data-stu-id="9410b-296">Description</span></span> |
| ------------ | ----------- |
| `type`       | <span data-ttu-id="9410b-297">Type de la ressource.</span><span class="sxs-lookup"><span data-stu-id="9410b-297">The type of the resource.</span></span> <span data-ttu-id="9410b-298">Types permissables : `assembly` , `pdb` , `dotnetjs` , `dotnetwasm` ,`timezonedata`</span><span class="sxs-lookup"><span data-stu-id="9410b-298">Permissable types: `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`, `timezonedata`</span></span> |
| `name`       | <span data-ttu-id="9410b-299">Nom de la ressource.</span><span class="sxs-lookup"><span data-stu-id="9410b-299">The name of the resource.</span></span> |
| `defaultUri` | <span data-ttu-id="9410b-300">URI relatif ou absolu de la ressource.</span><span class="sxs-lookup"><span data-stu-id="9410b-300">The relative or absolute URI of the resource.</span></span> |
| `integrity`  | <span data-ttu-id="9410b-301">Chaîne d’intégrité représentant le contenu attendu dans la réponse.</span><span class="sxs-lookup"><span data-stu-id="9410b-301">The integrity string representing the expected content in the response.</span></span> |

<span data-ttu-id="9410b-302">`loadBootResource`retourne l’un des éléments suivants pour remplacer le processus de chargement :</span><span class="sxs-lookup"><span data-stu-id="9410b-302">`loadBootResource` returns any of the following to override the loading process:</span></span>

* <span data-ttu-id="9410b-303">Chaîne d’URI.</span><span class="sxs-lookup"><span data-stu-id="9410b-303">URI string.</span></span> <span data-ttu-id="9410b-304">Dans l’exemple suivant ( `wwwroot/index.html` ), les fichiers suivants sont pris en charge à partir d’un CDN à l’adresse `https://my-awesome-cdn.com/` :</span><span class="sxs-lookup"><span data-stu-id="9410b-304">In the following example (`wwwroot/index.html`), the following files are served from a CDN at `https://my-awesome-cdn.com/`:</span></span>

  * `dotnet.*.js`
  * `dotnet.wasm`
  * <span data-ttu-id="9410b-305">Données de fuseau horaire</span><span class="sxs-lookup"><span data-stu-id="9410b-305">Timezone data</span></span>

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

* <span data-ttu-id="9410b-306">`Promise<Response>`.</span><span class="sxs-lookup"><span data-stu-id="9410b-306">`Promise<Response>`.</span></span> <span data-ttu-id="9410b-307">Transmettez le `integrity` paramètre dans un en-tête pour conserver le comportement de contrôle d’intégrité par défaut.</span><span class="sxs-lookup"><span data-stu-id="9410b-307">Pass the `integrity` parameter in a header to retain the default integrity-checking behavior.</span></span>

  <span data-ttu-id="9410b-308">L’exemple suivant ( `wwwroot/index.html` ) ajoute un en-tête HTTP personnalisé aux demandes sortantes et passe `integrity` le paramètre à l' `fetch` appel :</span><span class="sxs-lookup"><span data-stu-id="9410b-308">The following example (`wwwroot/index.html`) adds a custom HTTP header to the outbound requests and passes the `integrity` parameter through to the `fetch` call:</span></span>
  
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

* <span data-ttu-id="9410b-309">`null`/`undefined`, ce qui entraîne le comportement de chargement par défaut.</span><span class="sxs-lookup"><span data-stu-id="9410b-309">`null`/`undefined`, which results in the default loading behavior.</span></span>

<span data-ttu-id="9410b-310">Les sources externes doivent retourner les en-têtes CORS requis pour les navigateurs afin d’autoriser le chargement des ressources Cross-Origin.</span><span class="sxs-lookup"><span data-stu-id="9410b-310">External sources must return the required CORS headers for browsers to allow the cross-origin resource loading.</span></span> <span data-ttu-id="9410b-311">Les CDN fournissent généralement les en-têtes requis par défaut.</span><span class="sxs-lookup"><span data-stu-id="9410b-311">CDNs usually provide the required headers by default.</span></span>

<span data-ttu-id="9410b-312">Il vous suffit de spécifier des types pour les comportements personnalisés.</span><span class="sxs-lookup"><span data-stu-id="9410b-312">You only need to specify types for custom behaviors.</span></span> <span data-ttu-id="9410b-313">Les types non spécifiés à `loadBootResource` sont chargés par le Framework en fonction de leurs comportements de chargement par défaut.</span><span class="sxs-lookup"><span data-stu-id="9410b-313">Types not specified to `loadBootResource` are loaded by the framework per their default loading behaviors.</span></span>

## <a name="change-the-filename-extension-of-dll-files"></a><span data-ttu-id="9410b-314">Modifier l’extension de nom de fichier des fichiers DLL</span><span class="sxs-lookup"><span data-stu-id="9410b-314">Change the filename extension of DLL files</span></span>

<span data-ttu-id="9410b-315">Si vous avez besoin de modifier les extensions de nom de fichier des fichiers publiés de l’application `.dll` , suivez les instructions de cette section.</span><span class="sxs-lookup"><span data-stu-id="9410b-315">In case you have a need to change the filename extensions of the app's published `.dll` files, follow the guidance in this section.</span></span>

<span data-ttu-id="9410b-316">Après avoir publié l’application, utilisez un script d’interpréteur de commandes ou un pipeline de build DevOps pour renommer les `.dll` fichiers afin d’utiliser une extension de fichier différente.</span><span class="sxs-lookup"><span data-stu-id="9410b-316">After publishing the app, use a shell script or DevOps build pipeline to rename `.dll` files to use a different file extension.</span></span> <span data-ttu-id="9410b-317">Ciblez les `.dll` fichiers dans le `wwwroot` Répertoire de la sortie publiée de l’application (par exemple, `{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot` ).</span><span class="sxs-lookup"><span data-stu-id="9410b-317">Target the `.dll` files in the `wwwroot` directory of the app's published output (for example, `{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot`).</span></span>

<span data-ttu-id="9410b-318">Dans les exemples suivants, les `.dll` fichiers sont renommés pour utiliser l' `.bin` extension de fichier.</span><span class="sxs-lookup"><span data-stu-id="9410b-318">In the following examples, `.dll` files are renamed to use the `.bin` file extension.</span></span>

<span data-ttu-id="9410b-319">Sur Windows :</span><span class="sxs-lookup"><span data-stu-id="9410b-319">On Windows:</span></span>

```powershell
dir .\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content .\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content .\_framework\blazor.boot.json
```

<span data-ttu-id="9410b-320">Si des ressources de service Worker sont également utilisées, ajoutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="9410b-320">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content .\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content .\service-worker-assets.js
```

<span data-ttu-id="9410b-321">Sur Linux ou macOS :</span><span class="sxs-lookup"><span data-stu-id="9410b-321">On Linux or macOS:</span></span>

```console
for f in _framework/_bin/*; do mv "$f" "`echo $f | sed -e 's/\.dll\b/.bin/g'`"; done
sed -i 's/\.dll"/.bin"/g' _framework/blazor.boot.json
```

<span data-ttu-id="9410b-322">Si des ressources de service Worker sont également utilisées, ajoutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="9410b-322">If service worker assets are also in use, add the following command:</span></span>

```console
sed -i 's/\.dll"/.bin"/g' service-worker-assets.js
```
   
<span data-ttu-id="9410b-323">Pour utiliser une extension de fichier différente de `.bin` , remplacez `.bin` dans les commandes précédentes.</span><span class="sxs-lookup"><span data-stu-id="9410b-323">To use a different file extension than `.bin`, replace `.bin` in the preceding commands.</span></span>

<span data-ttu-id="9410b-324">Pour résoudre les fichiers et compressés `blazor.boot.json.gz` `blazor.boot.json.br` , adoptez l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="9410b-324">To address the compressed `blazor.boot.json.gz` and `blazor.boot.json.br` files, adopt either of the following approaches:</span></span>

* <span data-ttu-id="9410b-325">Supprimez les `blazor.boot.json.gz` fichiers et compressés `blazor.boot.json.br` .</span><span class="sxs-lookup"><span data-stu-id="9410b-325">Remove the compressed `blazor.boot.json.gz` and `blazor.boot.json.br` files.</span></span> <span data-ttu-id="9410b-326">La compression est désactivée avec cette approche.</span><span class="sxs-lookup"><span data-stu-id="9410b-326">Compression is disabled with this approach.</span></span>
* <span data-ttu-id="9410b-327">Recompressez le fichier mis à jour `blazor.boot.json` .</span><span class="sxs-lookup"><span data-stu-id="9410b-327">Recompress the updated `blazor.boot.json` file.</span></span>

<span data-ttu-id="9410b-328">Les instructions précédentes s’appliquent également lorsque les ressources de service Worker sont en cours d’utilisation.</span><span class="sxs-lookup"><span data-stu-id="9410b-328">The preceding guidance also applies when service worker assets are in use.</span></span> <span data-ttu-id="9410b-329">Supprimez ou recompressez `wwwroot/service-worker-assets.js.br` et `wwwroot/service-worker-assets.js.gz` .</span><span class="sxs-lookup"><span data-stu-id="9410b-329">Remove or recompress `wwwroot/service-worker-assets.js.br` and `wwwroot/service-worker-assets.js.gz`.</span></span> <span data-ttu-id="9410b-330">Dans le cas contraire, les vérifications de l’intégrité des fichiers échouent dans le navigateur.</span><span class="sxs-lookup"><span data-stu-id="9410b-330">Otherwise, file integrity checks fail in the browser.</span></span>

<span data-ttu-id="9410b-331">L’exemple Windows suivant utilise un script PowerShell placé à la racine du projet.</span><span class="sxs-lookup"><span data-stu-id="9410b-331">The following Windows example uses a PowerShell script placed at the root of the project.</span></span>

<span data-ttu-id="9410b-332">`ChangeDLLExtensions.ps1:`:</span><span class="sxs-lookup"><span data-stu-id="9410b-332">`ChangeDLLExtensions.ps1:`:</span></span>

```powershell
param([string]$filepath,[string]$tfm)
dir $filepath\bin\Release\$tfm\wwwroot\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json
Remove-Item $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json.gz
```

<span data-ttu-id="9410b-333">Si des ressources de service Worker sont également utilisées, ajoutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="9410b-333">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js
```

<span data-ttu-id="9410b-334">Dans le fichier projet, le script est exécuté après la publication de l’application :</span><span class="sxs-lookup"><span data-stu-id="9410b-334">In the project file, the script is run after publishing the app:</span></span>

```xml
<Target Name="ChangeDLLFileExtensions" AfterTargets="Publish" Condition="'$(Configuration)'=='Release'">
  <Exec Command="powershell.exe -command &quot;&amp; { .\ChangeDLLExtensions.ps1 '$(SolutionDir)' '$(TargetFramework)'}&quot;" />
</Target>
```

<span data-ttu-id="9410b-335">Pour nous faire part de vos commentaires, visitez [aspnetcore/issues #5477](https://github.com/dotnet/aspnetcore/issues/5477).</span><span class="sxs-lookup"><span data-stu-id="9410b-335">To provide feedback, visit [aspnetcore/issues #5477](https://github.com/dotnet/aspnetcore/issues/5477).</span></span>
 
