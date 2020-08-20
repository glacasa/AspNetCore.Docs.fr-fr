---
title: Implémentation du serveur web Kestrel dans ASP.NET Core
author: rick-anderson
description: Découvrez plus d’informations sur Kestrel, serveur web multiplateforme pour ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
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
uid: fundamentals/servers/kestrel
ms.openlocfilehash: 5890e56f65712bcd781a3aad278a5aaa7914d0ea
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88635019"
---
# <a name="kestrel-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="aa84c-103">Implémentation du serveur web Kestrel dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="aa84c-103">Kestrel web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="aa84c-104">Par [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher) et [Stephen Halter](https://twitter.com/halter73)</span><span class="sxs-lookup"><span data-stu-id="aa84c-104">By [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher), and [Stephen Halter](https://twitter.com/halter73)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="aa84c-105">Kestrel est un [serveur Web multiplateforme pour ASP.net Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="aa84c-105">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="aa84c-106">Kestrel est le serveur web inclus par défaut dans les modèles de projets ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="aa84c-106">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="aa84c-107">Kestrel prend en charge les scénarios suivants :</span><span class="sxs-lookup"><span data-stu-id="aa84c-107">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="aa84c-108">HTTPS</span><span class="sxs-lookup"><span data-stu-id="aa84c-108">HTTPS</span></span>
* <span data-ttu-id="aa84c-109">Mise à niveau opaque utilisée pour activer les [WebSockets](https://github.com/aspnet/websockets)</span><span class="sxs-lookup"><span data-stu-id="aa84c-109">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="aa84c-110">Sockets UNIX pour des performances élevées derrière Nginx</span><span class="sxs-lookup"><span data-stu-id="aa84c-110">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="aa84c-111">HTTP/2 (sauf sur macOS&dagger;)</span><span class="sxs-lookup"><span data-stu-id="aa84c-111">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="aa84c-112">&dagger;HTTP/2 sera pris en charge sur macOS dans une prochaine version.</span><span class="sxs-lookup"><span data-stu-id="aa84c-112">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="aa84c-113">Kestrel est pris en charge sur toutes les plateformes et les versions prises en charge par .NET Core.</span><span class="sxs-lookup"><span data-stu-id="aa84c-113">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="aa84c-114">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="aa84c-114">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="aa84c-115">Assistance HTTP/2</span><span class="sxs-lookup"><span data-stu-id="aa84c-115">HTTP/2 support</span></span>

<span data-ttu-id="aa84c-116">[HTTP/2](https://httpwg.org/specs/rfc7540.html) est disponible pour les applications ASP.NET Core si les conditions de base suivantes sont remplies :</span><span class="sxs-lookup"><span data-stu-id="aa84c-116">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="aa84c-117">Système d’exploitation&dagger;</span><span class="sxs-lookup"><span data-stu-id="aa84c-117">Operating system&dagger;</span></span>
  * <span data-ttu-id="aa84c-118">Windows Server 2016/Windows 10 ou version ultérieure&Dagger;</span><span class="sxs-lookup"><span data-stu-id="aa84c-118">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="aa84c-119">Linux avec OpenSSL 1.0.2 ou version ultérieure (par exemple, Ubuntu 16.04 ou version ultérieure)</span><span class="sxs-lookup"><span data-stu-id="aa84c-119">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="aa84c-120">Version cible de .Net Framework : .NET Core 2.2 ou version ultérieure</span><span class="sxs-lookup"><span data-stu-id="aa84c-120">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="aa84c-121">Connexion [ALPN (Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3)</span><span class="sxs-lookup"><span data-stu-id="aa84c-121">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="aa84c-122">TLS 1.2 ou connexion ultérieure</span><span class="sxs-lookup"><span data-stu-id="aa84c-122">TLS 1.2 or later connection</span></span>

<span data-ttu-id="aa84c-123">&dagger;HTTP/2 sera pris en charge sur macOS dans une prochaine version.</span><span class="sxs-lookup"><span data-stu-id="aa84c-123">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="aa84c-124">&Dagger;Kestrel propose une prise en charge limitée de HTTP/2 sous Windows Server 2012 R2 et Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="aa84c-124">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="aa84c-125">La prise en charge est limitée car la liste des suites de chiffrement TLS prises en charge sur ces systèmes d’exploitation est limitée.</span><span class="sxs-lookup"><span data-stu-id="aa84c-125">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="aa84c-126">Un certificat généré à l’aide d’Elliptic Curve Digital Signature algorithme (ECDSA) peut être requis pour sécuriser les connexions TLS.</span><span class="sxs-lookup"><span data-stu-id="aa84c-126">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="aa84c-127">Si une connexion HTTP/2 est établie, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) retourne `HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="aa84c-127">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="aa84c-128">HTTP/2 est désactivé par défaut.</span><span class="sxs-lookup"><span data-stu-id="aa84c-128">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="aa84c-129">Pour plus d’informations sur la configuration, consultez les sections [Options Kestrel](#kestrel-options) et [ListenOptions.Protocols](#listenoptionsprotocols).</span><span class="sxs-lookup"><span data-stu-id="aa84c-129">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="aa84c-130">Quand utiliser Kestrel avec un proxy inverse ?</span><span class="sxs-lookup"><span data-stu-id="aa84c-130">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="aa84c-131">Vous pouvez utiliser Kestrel par lui-même ou avec un *serveur proxy inverse*, comme [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org) ou [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="aa84c-131">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="aa84c-132">Un serveur proxy inverse reçoit les requêtes HTTP en provenance du réseau et les transmet à Kestrel.</span><span class="sxs-lookup"><span data-stu-id="aa84c-132">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="aa84c-133">Kestrel utilisé comme serveur web edge (accessible sur Internet) :</span><span class="sxs-lookup"><span data-stu-id="aa84c-133">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel communique directement avec Internet sans serveur proxy inverse](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="aa84c-135">Kestrel utilisé dans une configuration de proxy inverse :</span><span class="sxs-lookup"><span data-stu-id="aa84c-135">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel communique indirectement avec Internet via un serveur proxy inverse, par exemple IIS, Nginx ou Apache](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="aa84c-137">L’une ou l’autre des configurations, avec ou sans serveur proxy inverse, est une configuration d’hébergement prise en charge.</span><span class="sxs-lookup"><span data-stu-id="aa84c-137">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="aa84c-138">Kestrel, s’il est utilisé comme serveur de périphérie sans serveur proxy inverse, ne prend pas en charge le partage de la même adresse IP et du même port entre plusieurs processus.</span><span class="sxs-lookup"><span data-stu-id="aa84c-138">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="aa84c-139">Quand Kestrel est configuré pour écouter sur un port, il gère tout le trafic pour ce port, quel que soit les en-têtes `Host` des requêtes.</span><span class="sxs-lookup"><span data-stu-id="aa84c-139">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="aa84c-140">Un proxy inverse qui peut partager des ports a la possibilité de transférer des requêtes à Kestrel sur une adresse IP et un port uniques.</span><span class="sxs-lookup"><span data-stu-id="aa84c-140">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="aa84c-141">Même si un serveur proxy inverse n’est pas nécessaire, en utiliser un peut être un bon choix.</span><span class="sxs-lookup"><span data-stu-id="aa84c-141">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="aa84c-142">Un proxy inverse :</span><span class="sxs-lookup"><span data-stu-id="aa84c-142">A reverse proxy:</span></span>

* <span data-ttu-id="aa84c-143">Peut limiter la surface publique exposée des applications qu’il héberge.</span><span class="sxs-lookup"><span data-stu-id="aa84c-143">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="aa84c-144">Fournit une couche supplémentaire de configuration et de défense.</span><span class="sxs-lookup"><span data-stu-id="aa84c-144">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="aa84c-145">Peut mieux s’intégrer à l’infrastructure existante.</span><span class="sxs-lookup"><span data-stu-id="aa84c-145">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="aa84c-146">Simplifie la configuration de l’équilibrage de charge et d’une communication sécurisée (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="aa84c-146">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="aa84c-147">Seul le serveur proxy inverse requiert un certificat X. 509 et ce serveur peut communiquer avec les serveurs de l’application sur le réseau interne à l’aide du protocole HTTP simple.</span><span class="sxs-lookup"><span data-stu-id="aa84c-147">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="aa84c-148">L’hébergement dans une configuration de proxy inverse nécessite le [filtrage d’hôte](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="aa84c-148">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="kestrel-in-aspnet-core-apps"></a><span data-ttu-id="aa84c-149">Kestrel dans les applications ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="aa84c-149">Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="aa84c-150">Les modèles de projet ASP.NET Core utilisent Kestrel par défaut.</span><span class="sxs-lookup"><span data-stu-id="aa84c-150">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="aa84c-151">Dans *Program.cs*, la <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> méthode appelle <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> :</span><span class="sxs-lookup"><span data-stu-id="aa84c-151">In *Program.cs*, the <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> method calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=8)]

<span data-ttu-id="aa84c-152">Pour plus d’informations sur la génération de l’hôte, consultez les sections *configurer un hôte* et les *paramètres du générateur par défaut* de <xref:fundamentals/host/generic-host#set-up-a-host> .</span><span class="sxs-lookup"><span data-stu-id="aa84c-152">For more information on building the host, see the *Set up a host* and *Default builder settings* sections of <xref:fundamentals/host/generic-host#set-up-a-host>.</span></span>

<span data-ttu-id="aa84c-153">Pour fournir une configuration supplémentaire après l’appel de `ConfigureWebHostDefaults`, utilisez `ConfigureKestrel` :</span><span class="sxs-lookup"><span data-stu-id="aa84c-153">To provide additional configuration after calling `ConfigureWebHostDefaults`, use `ConfigureKestrel`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(serverOptions =>
            {
                // Set properties and call methods on options
            })
            .UseStartup<Startup>();
        });
```

## <a name="kestrel-options"></a><span data-ttu-id="aa84c-154">Options Kestrel</span><span class="sxs-lookup"><span data-stu-id="aa84c-154">Kestrel options</span></span>

<span data-ttu-id="aa84c-155">Le serveur web Kestrel a des options de configuration de contrainte qui sont particulièrement utiles dans les déploiements exposés à Internet.</span><span class="sxs-lookup"><span data-stu-id="aa84c-155">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="aa84c-156">Définissez des contraintes sur la propriété <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> de la classe <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>.</span><span class="sxs-lookup"><span data-stu-id="aa84c-156">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="aa84c-157">La propriété `Limits` conserve une instance de la classe <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>.</span><span class="sxs-lookup"><span data-stu-id="aa84c-157">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="aa84c-158">Les exemples suivants utilisent l’espace de noms <xref:Microsoft.AspNetCore.Server.Kestrel.Core> :</span><span class="sxs-lookup"><span data-stu-id="aa84c-158">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="aa84c-159">Dans les exemples présentés plus loin dans cet article, les options Kestrel sont configurées dans le code C#.</span><span class="sxs-lookup"><span data-stu-id="aa84c-159">In examples shown later in this article, Kestrel options are configured in C# code.</span></span> <span data-ttu-id="aa84c-160">Les options Kestrel peuvent également être définies à l’aide d’un [fournisseur de configuration](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="aa84c-160">Kestrel options can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="aa84c-161">Par exemple, le [fournisseur de configuration de fichier](xref:fundamentals/configuration/index#file-configuration-provider) peut charger la configuration Kestrel à partir d’un *appsettings.jssur* ou *appSettings. { Fichier Environment}. JSON* :</span><span class="sxs-lookup"><span data-stu-id="aa84c-161">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider) can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

```json
{
  "Kestrel": {
    "Limits": {
      "MaxConcurrentConnections": 100,
      "MaxConcurrentUpgradedConnections": 100
    },
    "DisableStringReuse": true
  }
}
```

> [!NOTE]
> <span data-ttu-id="aa84c-162"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> et la [configuration du point de terminaison](#endpoint-configuration) sont configurables à partir des fournisseurs de configuration.</span><span class="sxs-lookup"><span data-stu-id="aa84c-162"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> and [endpoint configuration](#endpoint-configuration) are configurable from configuration providers.</span></span> <span data-ttu-id="aa84c-163">La configuration Kestrel restante doit être configurée dans le code C#.</span><span class="sxs-lookup"><span data-stu-id="aa84c-163">Remaining Kestrel configuration must be configured in C# code.</span></span>

<span data-ttu-id="aa84c-164">Utilisez l' **une** des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="aa84c-164">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="aa84c-165">Configurer Kestrel dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="aa84c-165">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="aa84c-166">Injecte une instance de `IConfiguration` dans la `Startup` classe.</span><span class="sxs-lookup"><span data-stu-id="aa84c-166">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="aa84c-167">L’exemple suivant suppose que la configuration injectée est assignée à la `Configuration` propriété.</span><span class="sxs-lookup"><span data-stu-id="aa84c-167">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="aa84c-168">Dans `Startup.ConfigureServices` , chargez la `Kestrel` section de configuration dans la configuration de Kestrel :</span><span class="sxs-lookup"><span data-stu-id="aa84c-168">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

     ```csharp
     using Microsoft.Extensions.Configuration
     
     public class Startup
     {
         public Startup(IConfiguration configuration)
         {
             Configuration = configuration;
         }

         public IConfiguration Configuration { get; }

         public void ConfigureServices(IServiceCollection services)
         {
             services.Configure<KestrelServerOptions>(
                 Configuration.GetSection("Kestrel"));
         }

         public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
         {
             ...
         }
     }
     ```

* <span data-ttu-id="aa84c-169">Configurez Kestrel lors de la génération de l’hôte :</span><span class="sxs-lookup"><span data-stu-id="aa84c-169">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="aa84c-170">Dans *Program.cs*, chargez la `Kestrel` section de configuration dans la configuration de Kestrel :</span><span class="sxs-lookup"><span data-stu-id="aa84c-170">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

  ```csharp
  // using Microsoft.Extensions.DependencyInjection;

  public static IHostBuilder CreateHostBuilder(string[] args) =>
      Host.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .ConfigureWebHostDefaults(webBuilder =>
          {
              webBuilder.UseStartup<Startup>();
          });
  ```

<span data-ttu-id="aa84c-171">Les deux approches précédentes fonctionnent avec n’importe quel [fournisseur de configuration](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="aa84c-171">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="aa84c-172">Délai d’expiration toujours actif</span><span class="sxs-lookup"><span data-stu-id="aa84c-172">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="aa84c-173">Obtient ou définit le [délai d’expiration toujours actif](https://tools.ietf.org/html/rfc7230#section-6.5).</span><span class="sxs-lookup"><span data-stu-id="aa84c-173">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="aa84c-174">La valeur par défaut est de 2 minutes.</span><span class="sxs-lookup"><span data-stu-id="aa84c-174">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=19-20)]

### <a name="maximum-client-connections"></a><span data-ttu-id="aa84c-175">Nombre maximale de connexions client</span><span class="sxs-lookup"><span data-stu-id="aa84c-175">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="aa84c-176">Le nombre maximal de connexions TCP ouvertes simultanées peut être défini pour l’application entière avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="aa84c-176">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="aa84c-177">Il existe une limite distincte pour les connexions qui ont été mises à niveau à partir de HTTP ou HTTPS vers un autre protocole (par exemple, sur une demande WebSocket).</span><span class="sxs-lookup"><span data-stu-id="aa84c-177">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="aa84c-178">Une fois mise à niveau, une connexion n’est pas prise en compte dans la limite `MaxConcurrentConnections`.</span><span class="sxs-lookup"><span data-stu-id="aa84c-178">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="aa84c-179">Le nombre maximal de connexions est illimité (null) par défaut.</span><span class="sxs-lookup"><span data-stu-id="aa84c-179">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="aa84c-180">Taille maximale du corps de la requête</span><span class="sxs-lookup"><span data-stu-id="aa84c-180">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="aa84c-181">La taille maximale par défaut du corps de la requête est de 30 000 000 octets, soit environ 28,6 Mo.</span><span class="sxs-lookup"><span data-stu-id="aa84c-181">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="aa84c-182">Pour remplacer la limite dans une application ASP.NET Core MVC, nous vous recommandons d’utiliser l’attribut <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> sur une méthode d’action :</span><span class="sxs-lookup"><span data-stu-id="aa84c-182">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="aa84c-183">Voici un exemple qui montre comment configurer la contrainte pour l’application sur chaque requête :</span><span class="sxs-lookup"><span data-stu-id="aa84c-183">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="aa84c-184">Remplacer le paramètre sur une demande spécifique dans l’intergiciel (middleware) :</span><span class="sxs-lookup"><span data-stu-id="aa84c-184">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="aa84c-185">Une exception est levée si l’application configure la limite sur une demande après que l’application a commencé à lire la demande.</span><span class="sxs-lookup"><span data-stu-id="aa84c-185">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="aa84c-186">Il existe une propriété `IsReadOnly` qui indique si la propriété `MaxRequestBodySize` est en lecture seule ; si tel est le cas, il est trop tard pour configurer la limite.</span><span class="sxs-lookup"><span data-stu-id="aa84c-186">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="aa84c-187">Quand une application est exécutée [hors processus](xref:host-and-deploy/iis/index#out-of-process-hosting-model) derrière le [Module ASP.NET Core](xref:host-and-deploy/aspnet-core-module), la limite de taille du corps de demande de Kestrel est désactivée, car IIS définit déjà la limite.</span><span class="sxs-lookup"><span data-stu-id="aa84c-187">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="aa84c-188">Débit données minimal du corps de la requête</span><span class="sxs-lookup"><span data-stu-id="aa84c-188">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="aa84c-189">Kestrel vérifie à chaque seconde si les données arrivent au débit spécifié en octets/seconde.</span><span class="sxs-lookup"><span data-stu-id="aa84c-189">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="aa84c-190">Si le taux chute au-dessous de la valeur minimale, le délai d’attente de la connexion est dépassé. La période de grâce correspond à la durée pendant laquelle Kestrel permet au client d’augmenter son taux d’envoi jusqu’à la valeur minimale. la vitesse n’est pas vérifiée pendant cette période.</span><span class="sxs-lookup"><span data-stu-id="aa84c-190">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="aa84c-191">La période de grâce permet d’éviter la suppression des connexions qui, initialement, envoient des données à une vitesse lente en raison de la lenteur du démarrage de TCP.</span><span class="sxs-lookup"><span data-stu-id="aa84c-191">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="aa84c-192">Le débit minimal par défaut est 240 octets/seconde, avec une période de grâce de 5 secondes.</span><span class="sxs-lookup"><span data-stu-id="aa84c-192">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="aa84c-193">Un débit minimal s’applique également à la réponse.</span><span class="sxs-lookup"><span data-stu-id="aa84c-193">A minimum rate also applies to the response.</span></span> <span data-ttu-id="aa84c-194">Le code pour définir les limites de demande et de réponse est identique à l’exception de `RequestBody` ou `Response` dans les noms de propriété et d’interface.</span><span class="sxs-lookup"><span data-stu-id="aa84c-194">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="aa84c-195">Voici un exemple qui montre comment configurer les débits de données minimaux dans *Program.cs* :</span><span class="sxs-lookup"><span data-stu-id="aa84c-195">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-11)]

<span data-ttu-id="aa84c-196">Remplacer les limites de taux minimum par demande dans l’intergiciel (middleware) :</span><span class="sxs-lookup"><span data-stu-id="aa84c-196">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="aa84c-197">Le <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature>débit référencé dans l’exemple précédent n’est pas présent dans `HttpContext.Features` pour les requêtes HTTP/2, car la modification des limites de débit par requête n’est généralement pas prise en charge pour HTTP/2 (le protocole prend en charge le multiplexage de requête).</span><span class="sxs-lookup"><span data-stu-id="aa84c-197">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> referenced in the prior sample is not present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis is generally not supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="aa84c-198">Toutefois, le <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> est toujours présent `HttpContext.Features` pour les requêtes HTTP/2, car la limite de débit de lecture peut toujours être *désactivée entièrement* sur une base par demande en définissant `IHttpMinRequestBodyDataRateFeature.MinDataRate` sur `null` même pour une requête HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="aa84c-198">However, the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> is still present `HttpContext.Features` for HTTP/2 requests, because the read rate limit can still be *disabled entirely* on a per-request basis by setting `IHttpMinRequestBodyDataRateFeature.MinDataRate` to `null` even for an HTTP/2 request.</span></span> <span data-ttu-id="aa84c-199">Une tentative de lecture de `IHttpMinRequestBodyDataRateFeature.MinDataRate` ou une tentative de définition sur une valeur autre que `null` entraîne une levée de `NotSupportedException` selon une requête HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="aa84c-199">Attempting to read `IHttpMinRequestBodyDataRateFeature.MinDataRate` or attempting to set it to a value other than `null` will result in a `NotSupportedException` being thrown given an HTTP/2 request.</span></span>

<span data-ttu-id="aa84c-200">Les limites de débit à l’échelle du serveur configurées par le biais de `KestrelServerOptions.Limits` s’appliquent encore aux connexions HTTP/1.x et HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="aa84c-200">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="aa84c-201">Délai d’expiration des en-têtes de requête</span><span class="sxs-lookup"><span data-stu-id="aa84c-201">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="aa84c-202">Obtient ou définit le temps maximal passé par le serveur à recevoir des en-têtes de requête.</span><span class="sxs-lookup"><span data-stu-id="aa84c-202">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="aa84c-203">La valeur par défaut est de 30 secondes.</span><span class="sxs-lookup"><span data-stu-id="aa84c-203">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=21-22)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="aa84c-204">Flux de données maximal par connexion</span><span class="sxs-lookup"><span data-stu-id="aa84c-204">Maximum streams per connection</span></span>

<span data-ttu-id="aa84c-205">`Http2.MaxStreamsPerConnection` limite le nombre de flux de requête simultanée par connexion HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="aa84c-205">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="aa84c-206">Les flux de données excédentaires sont refusés.</span><span class="sxs-lookup"><span data-stu-id="aa84c-206">Excess streams are refused.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
});
```

<span data-ttu-id="aa84c-207">La valeur par défaut est 100.</span><span class="sxs-lookup"><span data-stu-id="aa84c-207">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="aa84c-208">Taille de la table d’en-tête</span><span class="sxs-lookup"><span data-stu-id="aa84c-208">Header table size</span></span>

<span data-ttu-id="aa84c-209">Le décodeur HPACK décompresse les en-têtes HTTP pour les connexions HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="aa84c-209">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="aa84c-210">`Http2.HeaderTableSize` limite la taille de la table de compression d’en-tête que le décodeur HPACK utilise.</span><span class="sxs-lookup"><span data-stu-id="aa84c-210">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="aa84c-211">La valeur est fournie en octets et doit être supérieure à zéro (0).</span><span class="sxs-lookup"><span data-stu-id="aa84c-211">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.HeaderTableSize = 4096;
});
```

<span data-ttu-id="aa84c-212">La valeur par défaut est 4096.</span><span class="sxs-lookup"><span data-stu-id="aa84c-212">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="aa84c-213">Taille de trame maximale</span><span class="sxs-lookup"><span data-stu-id="aa84c-213">Maximum frame size</span></span>

<span data-ttu-id="aa84c-214">`Http2.MaxFrameSize` indique la taille maximale autorisée d’une charge utile de trame de connexion HTTP/2 reçue ou envoyée par le serveur.</span><span class="sxs-lookup"><span data-stu-id="aa84c-214">`Http2.MaxFrameSize` indicates the maximum allowed size of an HTTP/2 connection frame payload received or sent by the server.</span></span> <span data-ttu-id="aa84c-215">La valeur est fournie en octets et doit être comprise entre 2^14 (16,384) et 2^24-1 (16,777,215).</span><span class="sxs-lookup"><span data-stu-id="aa84c-215">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxFrameSize = 16384;
});
```

<span data-ttu-id="aa84c-216">La valeur par défaut est 2^14 (16,384).</span><span class="sxs-lookup"><span data-stu-id="aa84c-216">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="aa84c-217">Taille maximale d’en-tête de requête</span><span class="sxs-lookup"><span data-stu-id="aa84c-217">Maximum request header size</span></span>

<span data-ttu-id="aa84c-218">`Http2.MaxRequestHeaderFieldSize` indique la taille maximale autorisée en octets des valeurs d’en-tête de requête.</span><span class="sxs-lookup"><span data-stu-id="aa84c-218">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="aa84c-219">Cette limite s’applique à la fois au nom et à la valeur dans leurs représentations compressées et non compressées.</span><span class="sxs-lookup"><span data-stu-id="aa84c-219">This limit applies to both name and value in their compressed and uncompressed representations.</span></span> <span data-ttu-id="aa84c-220">La valeur doit être supérieure à zéro (0).</span><span class="sxs-lookup"><span data-stu-id="aa84c-220">The value must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
});
```

<span data-ttu-id="aa84c-221">La valeur par défaut est 8 192.</span><span class="sxs-lookup"><span data-stu-id="aa84c-221">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="aa84c-222">Taille de fenêtre de connexion initiale</span><span class="sxs-lookup"><span data-stu-id="aa84c-222">Initial connection window size</span></span>

<span data-ttu-id="aa84c-223">`Http2.InitialConnectionWindowSize` indique la quantité maximale de données de corps de requête, en octets, que le serveur met en mémoire tampon à un moment donné pour toutes les requêtes (flux) par connexion.</span><span class="sxs-lookup"><span data-stu-id="aa84c-223">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="aa84c-224">Les requêtes sont également limitées par `Http2.InitialStreamWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="aa84c-224">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="aa84c-225">La valeur doit être supérieure ou égale à 65 535 et inférieure à 2^31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="aa84c-225">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
});
```

<span data-ttu-id="aa84c-226">La valeur par défaut est 128 Ko (131 072).</span><span class="sxs-lookup"><span data-stu-id="aa84c-226">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="aa84c-227">Taille de la fenêtre de flux initiale</span><span class="sxs-lookup"><span data-stu-id="aa84c-227">Initial stream window size</span></span>

<span data-ttu-id="aa84c-228">`Http2.InitialStreamWindowSize` indique la quantité maximale de données de corps de requête, en octets, que le serveur met en mémoire tampon à un moment donné par requête (flux).</span><span class="sxs-lookup"><span data-stu-id="aa84c-228">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="aa84c-229">Les requêtes sont également limitées par `Http2.InitialConnectionWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="aa84c-229">Requests are also limited by `Http2.InitialConnectionWindowSize`.</span></span> <span data-ttu-id="aa84c-230">La valeur doit être supérieure ou égale à 65 535 et inférieure à 2^31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="aa84c-230">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
});
```

<span data-ttu-id="aa84c-231">La valeur par défaut est 96 Ko (98 304).</span><span class="sxs-lookup"><span data-stu-id="aa84c-231">The default value is 96 KB (98,304).</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="aa84c-232">E/S synchrone</span><span class="sxs-lookup"><span data-stu-id="aa84c-232">Synchronous I/O</span></span>

<span data-ttu-id="aa84c-233"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> contrôle si des e/s synchrones sont autorisées pour la demande et la réponse.</span><span class="sxs-lookup"><span data-stu-id="aa84c-233"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="aa84c-234">La valeur par défaut est `false`.</span><span class="sxs-lookup"><span data-stu-id="aa84c-234">The default value is `false`.</span></span>

> [!WARNING]
> <span data-ttu-id="aa84c-235">Un grand nombre d’opérations d’e/s synchrones bloquantes peuvent entraîner une insuffisance du pool de threads, ce qui empêche l’application de répondre.</span><span class="sxs-lookup"><span data-stu-id="aa84c-235">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="aa84c-236">Activez uniquement `AllowSynchronousIO` lors de l’utilisation d’une bibliothèque qui ne prend pas en charge les e/s asynchrones.</span><span class="sxs-lookup"><span data-stu-id="aa84c-236">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="aa84c-237">L’exemple suivant active des e/s synchrones :</span><span class="sxs-lookup"><span data-stu-id="aa84c-237">The following example enables synchronous I/O:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="aa84c-238">Pour plus d’informations sur les autres options et limites de Kestrel, consultez :</span><span class="sxs-lookup"><span data-stu-id="aa84c-238">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="aa84c-239">Configuration du point de terminaison</span><span class="sxs-lookup"><span data-stu-id="aa84c-239">Endpoint configuration</span></span>

<span data-ttu-id="aa84c-240">Par défaut, ASP.NET Core se lie à :</span><span class="sxs-lookup"><span data-stu-id="aa84c-240">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="aa84c-241">`https://localhost:5001` (quand un certificat de développement local est présent)</span><span class="sxs-lookup"><span data-stu-id="aa84c-241">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="aa84c-242">Spécifiez les URL avec :</span><span class="sxs-lookup"><span data-stu-id="aa84c-242">Specify URLs using the:</span></span>

* <span data-ttu-id="aa84c-243">La variable d’environnement `ASPNETCORE_URLS`.</span><span class="sxs-lookup"><span data-stu-id="aa84c-243">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="aa84c-244">L’argument de ligne de commande `--urls`.</span><span class="sxs-lookup"><span data-stu-id="aa84c-244">`--urls` command-line argument.</span></span>
* <span data-ttu-id="aa84c-245">La clé de configuration d’hôte `urls`.</span><span class="sxs-lookup"><span data-stu-id="aa84c-245">`urls` host configuration key.</span></span>
* <span data-ttu-id="aa84c-246">La méthode d’extension `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="aa84c-246">`UseUrls` extension method.</span></span>

<span data-ttu-id="aa84c-247">La valeur fournie avec ces approches peut être un ou plusieurs points de terminaison HTTP et HTTPS (HTTPS si un certificat par défaut est disponible).</span><span class="sxs-lookup"><span data-stu-id="aa84c-247">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="aa84c-248">Configurez la valeur sous forme de liste délimitée par des points-virgules (par exemple `"Urls": "http://localhost:8000;http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="aa84c-248">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="aa84c-249">Pour plus d’informations sur ces approches, voir [URL de serveur](xref:fundamentals/host/web-host#server-urls) et [Remplacer la configuration](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="aa84c-249">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="aa84c-250">Un certificat de développement est créé :</span><span class="sxs-lookup"><span data-stu-id="aa84c-250">A development certificate is created:</span></span>

* <span data-ttu-id="aa84c-251">Quand le [SDK .NET Core](/dotnet/core/sdk) est installé.</span><span class="sxs-lookup"><span data-stu-id="aa84c-251">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="aa84c-252">[L’outil dev-certs](xref:aspnetcore-2.1#https) est utilisé pour créer un certificat.</span><span class="sxs-lookup"><span data-stu-id="aa84c-252">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="aa84c-253">Certains navigateurs requièrent l’octroi d’une autorisation explicite pour approuver le certificat de développement local.</span><span class="sxs-lookup"><span data-stu-id="aa84c-253">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="aa84c-254">Les modèles de projet configurent les applications à exécuter sur HTTPs par défaut et incluent [la redirection https et la prise en charge de HSTS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="aa84c-254">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="aa84c-255">Appelez les méthodes <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> ou <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> sur <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> pour configurer les préfixes et les ports d’URL pour Kestrel.</span><span class="sxs-lookup"><span data-stu-id="aa84c-255">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="aa84c-256">`UseUrls`, l’argument de ligne de commande `--urls`, la clé de configuration d’hôte `urls` et la variable d’environnement `ASPNETCORE_URLS` fonctionnent également, mais ils présentent les limitations indiquées plus loin dans cette section (un certificat par défaut doit être disponible pour la configuration du point de terminaison HTTPS).</span><span class="sxs-lookup"><span data-stu-id="aa84c-256">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="aa84c-257">`KestrelServerOptions` configuré</span><span class="sxs-lookup"><span data-stu-id="aa84c-257">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="aa84c-258">ConfigureEndpointDefaults (action \<ListenOptions> )</span><span class="sxs-lookup"><span data-stu-id="aa84c-258">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="aa84c-259">Spécifie une `Action` de configuration à exécuter pour chaque point de terminaison spécifié.</span><span class="sxs-lookup"><span data-stu-id="aa84c-259">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="aa84c-260">Le fait d’appeler `ConfigureEndpointDefaults` plusieurs fois remplace les `Action`s précédentes par la dernière `Action` spécifiée.</span><span class="sxs-lookup"><span data-stu-id="aa84c-260">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureEndpointDefaults(listenOptions =>
    {
        // Configure endpoint defaults
    });
});
```

> [!NOTE]
> <span data-ttu-id="aa84c-261"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> Les valeurs par défaut sont appliquées aux points de terminaison créés en appelant avant d’appeler.</span><span class="sxs-lookup"><span data-stu-id="aa84c-261">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="aa84c-262">ConfigureHttpsDefaults (action \<HttpsConnectionAdapterOptions> )</span><span class="sxs-lookup"><span data-stu-id="aa84c-262">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="aa84c-263">Spécifie une `Action` de configuration à exécuter pour chaque point de terminaison HTTPS.</span><span class="sxs-lookup"><span data-stu-id="aa84c-263">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="aa84c-264">Le fait d’appeler `ConfigureHttpsDefaults` plusieurs fois remplace les `Action`s précédentes par la dernière `Action` spécifiée.</span><span class="sxs-lookup"><span data-stu-id="aa84c-264">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        // certificate is an X509Certificate2
        listenOptions.ServerCertificate = certificate;
    });
});
```

> [!NOTE]
> <span data-ttu-id="aa84c-265"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> Les valeurs par défaut sont appliquées aux points de terminaison créés en appelant avant d’appeler.</span><span class="sxs-lookup"><span data-stu-id="aa84c-265">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="aa84c-266">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="aa84c-266">Configure(IConfiguration)</span></span>

<span data-ttu-id="aa84c-267">Crée un chargeur de configuration pour configurer Kestrel, qui prend en entrée une <xref:Microsoft.Extensions.Configuration.IConfiguration>.</span><span class="sxs-lookup"><span data-stu-id="aa84c-267">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="aa84c-268">La configuration doit être limitée à la section de configuration pour Kestrel.</span><span class="sxs-lookup"><span data-stu-id="aa84c-268">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="aa84c-269">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="aa84c-269">ListenOptions.UseHttps</span></span>

<span data-ttu-id="aa84c-270">Configure Kestrel pour l’utilisation de HTTPS.</span><span class="sxs-lookup"><span data-stu-id="aa84c-270">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="aa84c-271">Extensions de `ListenOptions.UseHttps` :</span><span class="sxs-lookup"><span data-stu-id="aa84c-271">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="aa84c-272">`UseHttps`: Configurez Kestrel pour utiliser le protocole HTTPs avec le certificat par défaut.</span><span class="sxs-lookup"><span data-stu-id="aa84c-272">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="aa84c-273">Lève une exception si aucun certificat par défaut n’est configuré.</span><span class="sxs-lookup"><span data-stu-id="aa84c-273">Throws an exception if no default certificate is configured.</span></span>
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

<span data-ttu-id="aa84c-274">Paramètres de `ListenOptions.UseHttps` :</span><span class="sxs-lookup"><span data-stu-id="aa84c-274">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="aa84c-275">`filename` est le chemin et le nom d’un fichier de certificat, relatif au répertoire qui contient les fichiers de contenu de l’application.</span><span class="sxs-lookup"><span data-stu-id="aa84c-275">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="aa84c-276">`password` est le mot de passe nécessaire pour accéder aux données du certificat X.509.</span><span class="sxs-lookup"><span data-stu-id="aa84c-276">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="aa84c-277">`configureOptions` est une `Action` pour configurer les `HttpsConnectionAdapterOptions`.</span><span class="sxs-lookup"><span data-stu-id="aa84c-277">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="aa84c-278">Retourne l'`ListenOptions`.</span><span class="sxs-lookup"><span data-stu-id="aa84c-278">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="aa84c-279">`storeName` est le magasin de certificats à partir duquel charger le certificat.</span><span class="sxs-lookup"><span data-stu-id="aa84c-279">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="aa84c-280">`subject` est le nom du sujet du certificat.</span><span class="sxs-lookup"><span data-stu-id="aa84c-280">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="aa84c-281">`allowInvalid` indique si les certificats non valides doivent être considérés, comme les certificats auto-signés.</span><span class="sxs-lookup"><span data-stu-id="aa84c-281">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="aa84c-282">`location` est l’emplacement du magasin à partir duquel charger le certificat.</span><span class="sxs-lookup"><span data-stu-id="aa84c-282">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="aa84c-283">`serverCertificate` est le certificat X.509.</span><span class="sxs-lookup"><span data-stu-id="aa84c-283">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="aa84c-284">En production, HTTPS doit être explicitement configuré.</span><span class="sxs-lookup"><span data-stu-id="aa84c-284">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="aa84c-285">Au minimum, un certificat par défaut doit être fourni.</span><span class="sxs-lookup"><span data-stu-id="aa84c-285">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="aa84c-286">Configurations prises en charge décrites dans la suite :</span><span class="sxs-lookup"><span data-stu-id="aa84c-286">Supported configurations described next:</span></span>

* <span data-ttu-id="aa84c-287">Pas de configuration</span><span class="sxs-lookup"><span data-stu-id="aa84c-287">No configuration</span></span>
* <span data-ttu-id="aa84c-288">Remplacer le certificat par défaut dans la configuration</span><span class="sxs-lookup"><span data-stu-id="aa84c-288">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="aa84c-289">Changer les valeurs par défaut dans le code</span><span class="sxs-lookup"><span data-stu-id="aa84c-289">Change the defaults in code</span></span>

<span data-ttu-id="aa84c-290">*Pas de configuration*</span><span class="sxs-lookup"><span data-stu-id="aa84c-290">*No configuration*</span></span>

<span data-ttu-id="aa84c-291">Kestrel écoute sur `http://localhost:5000` et sur `https://localhost:5001` (si un certificat par défaut est disponible).</span><span class="sxs-lookup"><span data-stu-id="aa84c-291">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="aa84c-292">*Remplacer le certificat par défaut dans la configuration*</span><span class="sxs-lookup"><span data-stu-id="aa84c-292">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="aa84c-293">Par défaut, `CreateDefaultBuilder` appelle `Configure(context.Configuration.GetSection("Kestrel"))` pour charger la configuration de Kestrel.</span><span class="sxs-lookup"><span data-stu-id="aa84c-293">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="aa84c-294">Un schéma de configuration des paramètres d’application HTTPS par défaut est disponible pour Kestrel.</span><span class="sxs-lookup"><span data-stu-id="aa84c-294">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="aa84c-295">Configurez plusieurs points de terminaison, notamment les URL et les certificats à utiliser, à partir d’un fichier sur disque ou d’un magasin de certificats.</span><span class="sxs-lookup"><span data-stu-id="aa84c-295">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="aa84c-296">Dans l’exemple de fichier *appsettings.json* suivant :</span><span class="sxs-lookup"><span data-stu-id="aa84c-296">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="aa84c-297">Définissez **AllowInvalid** sur `true` pour permettre l’utilisation de certificats non valides (par exemple des certificats auto-signés).</span><span class="sxs-lookup"><span data-stu-id="aa84c-297">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="aa84c-298">Tout point de terminaison HTTPS qui ne spécifie pas de certificat (**HttpsDefaultCert** dans l’exemple qui suit) revient au certificat défini sous **Certificats** > \*\*Par défaut \*\* ou au certificat de développement.</span><span class="sxs-lookup"><span data-stu-id="aa84c-298">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },
      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },
      "HttpsInlineCertStore": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },
      "HttpsDefaultCert": {
        "Url": "https://localhost:5003"
      },
      "Https": {
        "Url": "https://*:5004",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

<span data-ttu-id="aa84c-299">Une alternative à l’utilisation de **Chemin** et de **Mot de passe** pour un nœud de certificat consiste à spécifier le certificat avec des champs du magasin de certificats.</span><span class="sxs-lookup"><span data-stu-id="aa84c-299">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="aa84c-300">Par exemple, le **Certificates**  >  certificat**par défaut** des certificats peut être spécifié comme suit :</span><span class="sxs-lookup"><span data-stu-id="aa84c-300">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="aa84c-301">Notes de schéma :</span><span class="sxs-lookup"><span data-stu-id="aa84c-301">Schema notes:</span></span>

* <span data-ttu-id="aa84c-302">Les noms des points de terminaison ne respectent pas la casse.</span><span class="sxs-lookup"><span data-stu-id="aa84c-302">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="aa84c-303">Par exemple, `HTTPS` et `Https` sont valides.</span><span class="sxs-lookup"><span data-stu-id="aa84c-303">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="aa84c-304">Le paramètre `Url` est obligatoire pour chaque point de terminaison.</span><span class="sxs-lookup"><span data-stu-id="aa84c-304">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="aa84c-305">Le format de ce paramètre est le même que celui du paramètre de configuration `Urls` du plus haut niveau, sauf qu’il est limité à une seule valeur.</span><span class="sxs-lookup"><span data-stu-id="aa84c-305">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="aa84c-306">Ces points de terminaison remplacent ceux qui sont définis dans le paramètre de configuration `Urls` du plus haut niveau configuration, au lieu de s’y ajouter.</span><span class="sxs-lookup"><span data-stu-id="aa84c-306">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="aa84c-307">Les points de terminaison définis dans le code via `Listen` sont cumulatifs avec les points de terminaison définis dans la section de configuration.</span><span class="sxs-lookup"><span data-stu-id="aa84c-307">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="aa84c-308">La section `Certificate` est facultative.</span><span class="sxs-lookup"><span data-stu-id="aa84c-308">The `Certificate` section is optional.</span></span> <span data-ttu-id="aa84c-309">Si la section `Certificate` n’est pas spécifiée, les valeurs par défaut définies dans les scénarios précédents sont utilisées.</span><span class="sxs-lookup"><span data-stu-id="aa84c-309">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="aa84c-310">Si aucune valeur par défaut n’est disponible, le serveur lève une exception et son démarrage échoue.</span><span class="sxs-lookup"><span data-stu-id="aa84c-310">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="aa84c-311">La `Certificate` section prend en **Path**charge les &ndash; certificats**de mot de passe** de chemin d’accès et de magasin d' **objets** &ndash; **Store** .</span><span class="sxs-lookup"><span data-stu-id="aa84c-311">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="aa84c-312">Vous pouvez définir un nombre quelconque de points de terminaison de cette façon, pour autant qu’ils ne provoquent pas de conflits de port.</span><span class="sxs-lookup"><span data-stu-id="aa84c-312">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="aa84c-313">`options.Configure(context.Configuration.GetSection("{SECTION}"))` retourne un `KestrelConfigurationLoader` avec une méthode `.Endpoint(string name, listenOptions => { })` qui peut être utilisée pour compléter les paramètres d’un point de terminaison configuré :</span><span class="sxs-lookup"><span data-stu-id="aa84c-313">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

```csharp
webBuilder.UseKestrel((context, serverOptions) =>
{
    serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
        .Endpoint("HTTPS", listenOptions =>
        {
            listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
        });
});
```

<span data-ttu-id="aa84c-314">`KestrelServerOptions.ConfigurationLoader` peut être directement accessible pour poursuivre l’itération sur le chargeur existant, tel que celui fourni par <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> .</span><span class="sxs-lookup"><span data-stu-id="aa84c-314">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="aa84c-315">La section de configuration pour chaque point de terminaison est disponible sur les options de la `Endpoint` méthode afin que les paramètres personnalisés puissent être lus.</span><span class="sxs-lookup"><span data-stu-id="aa84c-315">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="aa84c-316">Plusieurs configurations peuvent être chargées en rappelant `options.Configure(context.Configuration.GetSection("{SECTION}"))` avec une autre section.</span><span class="sxs-lookup"><span data-stu-id="aa84c-316">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="aa84c-317">Seule la dernière configuration est utilisée, à moins que `Load` soit explicitement appelé sur les instances précédentes.</span><span class="sxs-lookup"><span data-stu-id="aa84c-317">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="aa84c-318">Le métapackage n’appelle pas `Load` : sa section de configuration par défaut peut donc être remplacée.</span><span class="sxs-lookup"><span data-stu-id="aa84c-318">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="aa84c-319">`KestrelConfigurationLoader` reflète la famille d’API `Listen` de `KestrelServerOptions` sous forme de surcharges de `Endpoint` : le code et les points de terminaison de configuration peuvent donc être configurés au même emplacement.</span><span class="sxs-lookup"><span data-stu-id="aa84c-319">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="aa84c-320">Ces surcharges n’utilisent pas de noms et consomment seulement les paramètres par défaut de la configuration.</span><span class="sxs-lookup"><span data-stu-id="aa84c-320">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="aa84c-321">*Changer les valeurs par défaut dans le code*</span><span class="sxs-lookup"><span data-stu-id="aa84c-321">*Change the defaults in code*</span></span>

<span data-ttu-id="aa84c-322">`ConfigureEndpointDefaults` et `ConfigureHttpsDefaults` peuvent être utilisés pour modifier les paramètres par défaut pour `ListenOptions` et `HttpsConnectionAdapterOptions`, notamment en remplaçant le certificat par défaut spécifié dans le scénario précédent.</span><span class="sxs-lookup"><span data-stu-id="aa84c-322">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="aa84c-323">`ConfigureEndpointDefaults` et `ConfigureHttpsDefaults` doivent être appelés avant que des points de terminaison soient configurés.</span><span class="sxs-lookup"><span data-stu-id="aa84c-323">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureEndpointDefaults(listenOptions =>
    {
        // Configure endpoint defaults
    });

    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        listenOptions.SslProtocols = SslProtocols.Tls12;
    });
});
```

<span data-ttu-id="aa84c-324">*Prise en charge de SNI par Kestrel*</span><span class="sxs-lookup"><span data-stu-id="aa84c-324">*Kestrel support for SNI*</span></span>

<span data-ttu-id="aa84c-325">[L’indication du nom de serveur (SNI, Server Name Indication)](https://tools.ietf.org/html/rfc6066#section-3) peut être utilisée pour héberger plusieurs domaines sur la même adresse IP et le même port.</span><span class="sxs-lookup"><span data-stu-id="aa84c-325">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="aa84c-326">Pour que SNI fonctionne, le client envoie le nom d’hôte pour la session sécurisée au serveur pendant la négociation TLS afin que le serveur puisse fournir le certificat correct.</span><span class="sxs-lookup"><span data-stu-id="aa84c-326">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="aa84c-327">Le client utilise le certificat fourni pour la communication chiffrée avec le serveur pendant la session sécurisée qui suit la négociation TLS.</span><span class="sxs-lookup"><span data-stu-id="aa84c-327">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="aa84c-328">Kestrel prend en charge SNI via le rappel de `ServerCertificateSelector`.</span><span class="sxs-lookup"><span data-stu-id="aa84c-328">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="aa84c-329">Le rappel est appelé une fois par connexion pour permettre à l’application d’inspecter le nom d’hôte et de sélectionner le certificat approprié.</span><span class="sxs-lookup"><span data-stu-id="aa84c-329">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="aa84c-330">La prise en charge de SNI nécessite les points suivants :</span><span class="sxs-lookup"><span data-stu-id="aa84c-330">SNI support requires:</span></span>

* <span data-ttu-id="aa84c-331">Exécution sur la version cible de .NET Framework `netcoreapp2.1` ou version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="aa84c-331">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="aa84c-332">Sur `net461` ou version ultérieure, le rappel est appelé, mais `name` est toujours `null` .</span><span class="sxs-lookup"><span data-stu-id="aa84c-332">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="aa84c-333">`name` est également `null` si le client ne fournit pas le paramètre du nom d’hôte dans la négociation TLS.</span><span class="sxs-lookup"><span data-stu-id="aa84c-333">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="aa84c-334">Tous les sites web s’exécutent sur la même instance Kestrel.</span><span class="sxs-lookup"><span data-stu-id="aa84c-334">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="aa84c-335">Kestrel ne prend pas en charge le partage d’une adresse IP et d’un port entre plusieurs instances sans un proxy inverse.</span><span class="sxs-lookup"><span data-stu-id="aa84c-335">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ListenAnyIP(5005, listenOptions =>
    {
        listenOptions.UseHttps(httpsOptions =>
        {
            var localhostCert = CertificateLoader.LoadFromStoreCert(
                "localhost", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var exampleCert = CertificateLoader.LoadFromStoreCert(
                "example.com", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var subExampleCert = CertificateLoader.LoadFromStoreCert(
                "sub.example.com", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var certs = new Dictionary<string, X509Certificate2>(
                StringComparer.OrdinalIgnoreCase);
            certs["localhost"] = localhostCert;
            certs["example.com"] = exampleCert;
            certs["sub.example.com"] = subExampleCert;

            httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
            {
                if (name != null && certs.TryGetValue(name, out var cert))
                {
                    return cert;
                }

                return exampleCert;
            };
        });
    });
});
```

### <a name="connection-logging"></a><span data-ttu-id="aa84c-336">Journalisation des connexions</span><span class="sxs-lookup"><span data-stu-id="aa84c-336">Connection logging</span></span>

<span data-ttu-id="aa84c-337">Appelez <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> pour émettre des journaux au niveau du débogage pour la communication au niveau de l’octet sur une connexion.</span><span class="sxs-lookup"><span data-stu-id="aa84c-337">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="aa84c-338">La journalisation des connexions est utile pour résoudre les problèmes de communication de bas niveau, par exemple lors du chiffrement TLS et derrière les proxies.</span><span class="sxs-lookup"><span data-stu-id="aa84c-338">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="aa84c-339">Si `UseConnectionLogging` est placé avant `UseHttps` , le trafic chiffré est journalisé.</span><span class="sxs-lookup"><span data-stu-id="aa84c-339">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="aa84c-340">Si `UseConnectionLogging` est placé après `UseHttps` , le trafic déchiffré est journalisé.</span><span class="sxs-lookup"><span data-stu-id="aa84c-340">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="aa84c-341">Lier à un socket TCP</span><span class="sxs-lookup"><span data-stu-id="aa84c-341">Bind to a TCP socket</span></span>

<span data-ttu-id="aa84c-342">La méthode <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> se lie à un socket TCP, et une expression lambda options permet la configuration d’un certificat X.509 :</span><span class="sxs-lookup"><span data-stu-id="aa84c-342">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=12-18)]

<span data-ttu-id="aa84c-343">L’exemple configure HTTPS pour un point de terminaison avec <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span><span class="sxs-lookup"><span data-stu-id="aa84c-343">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="aa84c-344">Utilisez la même API afin de configurer d’autres paramètres Kestrel pour des points de terminaison spécifiques.</span><span class="sxs-lookup"><span data-stu-id="aa84c-344">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="aa84c-345">Lier à un socket Unix</span><span class="sxs-lookup"><span data-stu-id="aa84c-345">Bind to a Unix socket</span></span>

<span data-ttu-id="aa84c-346">Écoutez sur un socket Unix avec <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> pour améliorer les performances avec Nginx, comme illustré dans cet exemple :</span><span class="sxs-lookup"><span data-stu-id="aa84c-346">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="aa84c-347">Dans le fichier de configuration nginx, définissez l' `server`  >  `location`  >  `proxy_pass` entrée sur `http://unix:/tmp/{KESTREL SOCKET}:/;` .</span><span class="sxs-lookup"><span data-stu-id="aa84c-347">In the Nginx configuration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="aa84c-348">`{KESTREL SOCKET}` nom du Socket fourni à <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (par exemple, `kestrel-test.sock` dans l’exemple précédent).</span><span class="sxs-lookup"><span data-stu-id="aa84c-348">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="aa84c-349">Assurez-vous que le socket est accessible en écriture par Nginx (par exemple, `chmod go+w /tmp/kestrel-test.sock` ).</span><span class="sxs-lookup"><span data-stu-id="aa84c-349">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span>

### <a name="port-0"></a><span data-ttu-id="aa84c-350">Port 0</span><span class="sxs-lookup"><span data-stu-id="aa84c-350">Port 0</span></span>

<span data-ttu-id="aa84c-351">Si vous spécifiez le numéro de port `0`, Kestrel se lie dynamiquement à un port disponible.</span><span class="sxs-lookup"><span data-stu-id="aa84c-351">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="aa84c-352">L’exemple suivant montre comment déterminer le port auquel Kestrel se lie à l’exécution :</span><span class="sxs-lookup"><span data-stu-id="aa84c-352">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="aa84c-353">Quand l’application est exécutée, la sortie de la fenêtre de console indique le port dynamique où l’application peut être atteinte :</span><span class="sxs-lookup"><span data-stu-id="aa84c-353">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="aa84c-354">Limites</span><span class="sxs-lookup"><span data-stu-id="aa84c-354">Limitations</span></span>

<span data-ttu-id="aa84c-355">Configurez des points de terminaison avec les approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="aa84c-355">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="aa84c-356">Arguments de ligne de commande `--urls`</span><span class="sxs-lookup"><span data-stu-id="aa84c-356">`--urls` command-line argument</span></span>
* <span data-ttu-id="aa84c-357">Clé de configuration d’hôte `urls`.</span><span class="sxs-lookup"><span data-stu-id="aa84c-357">`urls` host configuration key</span></span>
* <span data-ttu-id="aa84c-358">`ASPNETCORE_URLS` variable d’environnement</span><span class="sxs-lookup"><span data-stu-id="aa84c-358">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="aa84c-359">Ces méthodes sont utiles si vous voulez que votre code fonctionne avec des serveurs autres que Kestrel.</span><span class="sxs-lookup"><span data-stu-id="aa84c-359">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="aa84c-360">Toutefois, soyez conscient des limitations suivantes :</span><span class="sxs-lookup"><span data-stu-id="aa84c-360">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="aa84c-361">HTTPS ne peut pas être utilisé avec ces approches, sauf si un certificat par défaut est fourni dans la configuration du point de terminaison HTTPS (par exemple avec la configuration de `KestrelServerOptions` ou un fichier de configuration, comme illustré plus haut dans cette rubrique).</span><span class="sxs-lookup"><span data-stu-id="aa84c-361">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="aa84c-362">Quand les deux approches `Listen` et `UseUrls` sont utilisées simultanément, les points de terminaison `Listen` remplacent les points de terminaison `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="aa84c-362">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="aa84c-363">Configuration de point de terminaison IIS</span><span class="sxs-lookup"><span data-stu-id="aa84c-363">IIS endpoint configuration</span></span>

<span data-ttu-id="aa84c-364">Quand vous utilisez IIS, les liaisons d’URL pour IIS remplacent les liaisons qui sont définies par `Listen` ou par `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="aa84c-364">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="aa84c-365">Pour plus d’informations, consultez la rubrique [Module ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="aa84c-365">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="aa84c-366">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="aa84c-366">ListenOptions.Protocols</span></span>

<span data-ttu-id="aa84c-367">La propriété `Protocols` établit les protocoles HTTP (`HttpProtocols`) activés sur un point de terminaison de connexion ou pour le serveur.</span><span class="sxs-lookup"><span data-stu-id="aa84c-367">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="aa84c-368">Affectez une valeur à la propriété `Protocols` à partir de l’énumération `HttpProtocols`.</span><span class="sxs-lookup"><span data-stu-id="aa84c-368">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="aa84c-369">Valeur enum `HttpProtocols`</span><span class="sxs-lookup"><span data-stu-id="aa84c-369">`HttpProtocols` enum value</span></span> | <span data-ttu-id="aa84c-370">Protocole de connexion autorisé</span><span class="sxs-lookup"><span data-stu-id="aa84c-370">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="aa84c-371">HTTP/1.1 uniquement.</span><span class="sxs-lookup"><span data-stu-id="aa84c-371">HTTP/1.1 only.</span></span> <span data-ttu-id="aa84c-372">Peut être utilisé avec ou sans TLS.</span><span class="sxs-lookup"><span data-stu-id="aa84c-372">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="aa84c-373">HTTP/2 uniquement.</span><span class="sxs-lookup"><span data-stu-id="aa84c-373">HTTP/2 only.</span></span> <span data-ttu-id="aa84c-374">Peut être utilisé sans TLS, uniquement si le client prend en charge un [mode de connaissance préalable (Prior Knowledge)](https://tools.ietf.org/html/rfc7540#section-3.4).</span><span class="sxs-lookup"><span data-stu-id="aa84c-374">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="aa84c-375">HTTP/1.1 et HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="aa84c-375">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="aa84c-376">HTTP/2 nécessite que le client sélectionne HTTP/2 dans le protocole de transfert de [négociation de protocole de couche d’application (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) TLS. dans le cas contraire, la connexion par défaut est HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="aa84c-376">HTTP/2 requires the client to select HTTP/2 in the TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="aa84c-377">La valeur par défaut `ListenOptions.Protocols` d’un point de terminaison est `HttpProtocols.Http1AndHttp2` .</span><span class="sxs-lookup"><span data-stu-id="aa84c-377">The default `ListenOptions.Protocols` value for any endpoint is `HttpProtocols.Http1AndHttp2`.</span></span>

<span data-ttu-id="aa84c-378">Restrictions TLS pour HTTP/2 :</span><span class="sxs-lookup"><span data-stu-id="aa84c-378">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="aa84c-379">TLS version 1.2 ou ultérieure</span><span class="sxs-lookup"><span data-stu-id="aa84c-379">TLS version 1.2 or later</span></span>
* <span data-ttu-id="aa84c-380">Renégociation désactivée</span><span class="sxs-lookup"><span data-stu-id="aa84c-380">Renegotiation disabled</span></span>
* <span data-ttu-id="aa84c-381">Compression désactivée</span><span class="sxs-lookup"><span data-stu-id="aa84c-381">Compression disabled</span></span>
* <span data-ttu-id="aa84c-382">Tailles minimales de l’échange de clé éphémère :</span><span class="sxs-lookup"><span data-stu-id="aa84c-382">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="aa84c-383">Elliptic Curve Diffie-Hellman (ECDHE) &lbrack; [RFC4492](https://www.ietf.org/rfc/rfc4492.txt) &rbrack; : 224 bits minimum</span><span class="sxs-lookup"><span data-stu-id="aa84c-383">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 224 bits minimum</span></span>
  * <span data-ttu-id="aa84c-384">Diffie-Hellman de champ fini (dhe) &lbrack; `TLS12` &rbrack; : 2048 bits minimum</span><span class="sxs-lookup"><span data-stu-id="aa84c-384">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;: 2048 bits minimum</span></span>
* <span data-ttu-id="aa84c-385">Suite de chiffrement non interdite.</span><span class="sxs-lookup"><span data-stu-id="aa84c-385">Cipher suite not prohibited.</span></span> 

<span data-ttu-id="aa84c-386">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`&lbrack;`TLS-ECDHE`&rbrack; avec la courbe elliptique P-256 &lbrack; `FIPS186` &rbrack; est pris en charge par défaut.</span><span class="sxs-lookup"><span data-stu-id="aa84c-386">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="aa84c-387">L’exemple suivant autorise les connexions HTTP/1.1 et HTTP/2 sur le port 8000.</span><span class="sxs-lookup"><span data-stu-id="aa84c-387">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="aa84c-388">Les connexions sont sécurisées par TLS avec un certificat fourni :</span><span class="sxs-lookup"><span data-stu-id="aa84c-388">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="aa84c-389">Utilisez l’intergiciel de connexion pour filtrer les négociations TLS en fonction de la connexion pour des chiffrements spécifiques, si nécessaire.</span><span class="sxs-lookup"><span data-stu-id="aa84c-389">Use Connection Middleware to filter TLS handshakes on a per-connection basis for specific ciphers if required.</span></span>

<span data-ttu-id="aa84c-390">L’exemple suivant lève <xref:System.NotSupportedException> une exception pour tous les algorithmes de chiffrement que l’application ne prend pas en charge.</span><span class="sxs-lookup"><span data-stu-id="aa84c-390">The following example throws <xref:System.NotSupportedException> for any cipher algorithm that the app doesn't support.</span></span> <span data-ttu-id="aa84c-391">Vous pouvez également définir et comparer [ITlsHandshakeFeature. CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) à une liste de suites de chiffrement acceptables.</span><span class="sxs-lookup"><span data-stu-id="aa84c-391">Alternatively, define and compare [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) to a list of acceptable cipher suites.</span></span>

<span data-ttu-id="aa84c-392">Aucun chiffrement n’est utilisé avec un algorithme de chiffrement [CipherAlgorithmType. null](xref:System.Security.Authentication.CipherAlgorithmType) .</span><span class="sxs-lookup"><span data-stu-id="aa84c-392">No encryption is used with a [CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) cipher algorithm.</span></span>

```csharp
// using System.Net;
// using Microsoft.AspNetCore.Connections;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.UseTlsFilter();
    });
});
```

```csharp
using System;
using System.Security.Authentication;
using Microsoft.AspNetCore.Connections.Features;

namespace Microsoft.AspNetCore.Connections
{
    public static class TlsFilterConnectionMiddlewareExtensions
    {
        public static IConnectionBuilder UseTlsFilter(
            this IConnectionBuilder builder)
        {
            return builder.Use((connection, next) =>
            {
                var tlsFeature = connection.Features.Get<ITlsHandshakeFeature>();

                if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
                {
                    throw new NotSupportedException("Prohibited cipher: " +
                        tlsFeature.CipherAlgorithm);
                }

                return next();
            });
        }
    }
}
```

<span data-ttu-id="aa84c-393">Le filtrage des connexions peut également être configuré par le biais d’une expression <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda :</span><span class="sxs-lookup"><span data-stu-id="aa84c-393">Connection filtering can also be configured via an <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda:</span></span>

```csharp
// using System;
// using System.Net;
// using System.Security.Authentication;
// using Microsoft.AspNetCore.Connections;
// using Microsoft.AspNetCore.Connections.Features;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.Use((context, next) =>
        {
            var tlsFeature = context.Features.Get<ITlsHandshakeFeature>();

            if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
            {
                throw new NotSupportedException(
                    $"Prohibited cipher: {tlsFeature.CipherAlgorithm}");
            }

            return next();
        });
    });
});
```

<span data-ttu-id="aa84c-394">Sur Linux, <xref:System.Net.Security.CipherSuitesPolicy> peut être utilisé pour filtrer les négociations TLS en fonction de la connexion :</span><span class="sxs-lookup"><span data-stu-id="aa84c-394">On Linux, <xref:System.Net.Security.CipherSuitesPolicy> can be used to filter TLS handshakes on a per-connection basis:</span></span>

```csharp
// using System.Net.Security;
// using Microsoft.AspNetCore.Hosting;
// using Microsoft.AspNetCore.Server.Kestrel.Core;
// using Microsoft.Extensions.DependencyInjection;
// using Microsoft.Extensions.Hosting;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        listenOptions.OnAuthenticate = (context, sslOptions) =>
        {
            sslOptions.CipherSuitesPolicy = new CipherSuitesPolicy(
                new[]
                {
                    TlsCipherSuite.TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256,
                    TlsCipherSuite.TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384,
                    // ...
                });
        };
    });
});
```

<span data-ttu-id="aa84c-395">*Définir le protocole à partir de la configuration*</span><span class="sxs-lookup"><span data-stu-id="aa84c-395">*Set the protocol from configuration*</span></span>

<span data-ttu-id="aa84c-396">Par défaut, `CreateDefaultBuilder` appelle `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` pour charger la configuration de Kestrel.</span><span class="sxs-lookup"><span data-stu-id="aa84c-396">`CreateDefaultBuilder` calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="aa84c-397">Le *appsettings.jssuivant sur* l’exemple établit http/1.1 comme protocole de connexion par défaut pour tous les points de terminaison :</span><span class="sxs-lookup"><span data-stu-id="aa84c-397">The following *appsettings.json* example establishes HTTP/1.1 as the default connection protocol for all endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1"
    }
  }
}
```

<span data-ttu-id="aa84c-398">L' *appsettings.js* suivant montre comment établir le protocole de connexion http/1.1 pour un point de terminaison spécifique :</span><span class="sxs-lookup"><span data-stu-id="aa84c-398">The following *appsettings.json* example establishes the HTTP/1.1 connection protocol for a specific endpoint:</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "HttpsDefaultCert": {
        "Url": "https://localhost:5001",
        "Protocols": "Http1"
      }
    }
  }
}
```

<span data-ttu-id="aa84c-399">Les protocoles spécifiés dans le code remplacent les valeurs définies par configuration.</span><span class="sxs-lookup"><span data-stu-id="aa84c-399">Protocols specified in code override values set by configuration.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="aa84c-400">Configuration du transport</span><span class="sxs-lookup"><span data-stu-id="aa84c-400">Transport configuration</span></span>

<span data-ttu-id="aa84c-401">Pour les projets qui requièrent l’utilisation de Libuv ( <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> ) :</span><span class="sxs-lookup"><span data-stu-id="aa84c-401">For projects that require the use of Libuv (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>):</span></span>

* <span data-ttu-id="aa84c-402">Ajoutez une dépendance pour le package [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) au fichier projet de l’application :</span><span class="sxs-lookup"><span data-stu-id="aa84c-402">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

   ```xml
   <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                     Version="{VERSION}" />
   ```

* <span data-ttu-id="aa84c-403">Appelez <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> sur le `IWebHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="aa84c-403">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> on the `IWebHostBuilder`:</span></span>

   ```csharp
   public class Program
   {
       public static void Main(string[] args)
       {
           CreateHostBuilder(args).Build().Run();
       }

       public static IHostBuilder CreateHostBuilder(string[] args) =>
           Host.CreateDefaultBuilder(args)
               .ConfigureWebHostDefaults(webBuilder =>
               {
                   webBuilder.UseLibuv();
                   webBuilder.UseStartup<Startup>();
               });
   }
   ```

### <a name="url-prefixes"></a><span data-ttu-id="aa84c-404">Préfixes d’URL</span><span class="sxs-lookup"><span data-stu-id="aa84c-404">URL prefixes</span></span>

<span data-ttu-id="aa84c-405">Quand vous utilisez `UseUrls`, l’argument de ligne de commande `--urls`, la clé de configuration d’hôte `urls` ou la variable d’environnement `ASPNETCORE_URLS`, les préfixes d’URL peuvent être dans un des formats suivants.</span><span class="sxs-lookup"><span data-stu-id="aa84c-405">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="aa84c-406">Seuls les préfixes d’URL HTTP sont valides.</span><span class="sxs-lookup"><span data-stu-id="aa84c-406">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="aa84c-407">Kestrel ne prend pas en charge HTTPS lors de la configuration de liaisons d’URL avec `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="aa84c-407">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="aa84c-408">Adresse IPv4 avec numéro de port</span><span class="sxs-lookup"><span data-stu-id="aa84c-408">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="aa84c-409">`0.0.0.0` est un cas spécial qui se lie à toutes les adresses IPv4.</span><span class="sxs-lookup"><span data-stu-id="aa84c-409">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="aa84c-410">Adresse IPv6 avec numéro de port</span><span class="sxs-lookup"><span data-stu-id="aa84c-410">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="aa84c-411">`[::]` est l’équivalent IPv6 de `0.0.0.0` dans IPv4.</span><span class="sxs-lookup"><span data-stu-id="aa84c-411">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="aa84c-412">Nom d’hôte avec numéro de port</span><span class="sxs-lookup"><span data-stu-id="aa84c-412">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="aa84c-413">Les noms d’hôte, `*` et `+` ne sont pas spéciaux.</span><span class="sxs-lookup"><span data-stu-id="aa84c-413">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="aa84c-414">Tout ce qui n’est pas reconnu comme adresse IP valide ou `localhost` se lie à toutes les adresses IP IPv4 et IPv6.</span><span class="sxs-lookup"><span data-stu-id="aa84c-414">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="aa84c-415">Pour lier différents noms d’hôte à différentes applications ASP.NET Core sur le même port, utilisez [HTTP.sys](xref:fundamentals/servers/httpsys) ou un serveur proxy inverse, comme IIS, Nginx ou Apache.</span><span class="sxs-lookup"><span data-stu-id="aa84c-415">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="aa84c-416">L’hébergement dans une configuration de proxy inverse nécessite le [filtrage d’hôte](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="aa84c-416">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="aa84c-417">Nom `localhost` de l’hôte avec numéro de port ou adresse IP de bouclage avec numéro de port</span><span class="sxs-lookup"><span data-stu-id="aa84c-417">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="aa84c-418">Quand `localhost` est spécifié, Kestrel tente de se lier aux interfaces de bouclage IPv4 et IPv6.</span><span class="sxs-lookup"><span data-stu-id="aa84c-418">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="aa84c-419">Si le port demandé est en cours d’utilisation par un autre service sur l’une des interfaces de bouclage, Kestrel ne démarre pas.</span><span class="sxs-lookup"><span data-stu-id="aa84c-419">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="aa84c-420">Si l’une des interfaces de bouclage n’est pas disponible pour une raison quelconque (généralement du fait de la non-prise en charge d’IPv6), Kestrel journalise un avertissement.</span><span class="sxs-lookup"><span data-stu-id="aa84c-420">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="aa84c-421">Filtrage d’hôtes</span><span class="sxs-lookup"><span data-stu-id="aa84c-421">Host filtering</span></span>

<span data-ttu-id="aa84c-422">Si Kestrel prend en charge la configuration basée sur les préfixes, comme `http://example.com:5000`, il ignore en grande partie le nom d’hôte.</span><span class="sxs-lookup"><span data-stu-id="aa84c-422">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="aa84c-423">L’hôte `localhost` est un cas spécial utilisé pour la liaison à des adresses de bouclage.</span><span class="sxs-lookup"><span data-stu-id="aa84c-423">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="aa84c-424">Tout hôte autre qu’une adresse IP explicite se lie à toutes les adresses IP publiques.</span><span class="sxs-lookup"><span data-stu-id="aa84c-424">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="aa84c-425">Les en-têtes `Host` ne sont pas validés.</span><span class="sxs-lookup"><span data-stu-id="aa84c-425">`Host` headers aren't validated.</span></span>

<span data-ttu-id="aa84c-426">En guise de solution de contournement, utilisez le middleware de filtrage d’hôtes.</span><span class="sxs-lookup"><span data-stu-id="aa84c-426">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="aa84c-427">L’intergiciel (middleware) de filtrage d’hôte est fourni par le package [Microsoft. AspNetCore. HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) , qui est fourni implicitement pour les applications ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="aa84c-427">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is implicitly provided for ASP.NET Core apps.</span></span> <span data-ttu-id="aa84c-428">L’intergiciel (middleware) est ajouté par <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, qui appelle <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> :</span><span class="sxs-lookup"><span data-stu-id="aa84c-428">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="aa84c-429">Le middleware de filtrage d’hôtes est désactivé par défaut.</span><span class="sxs-lookup"><span data-stu-id="aa84c-429">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="aa84c-430">Pour activer l’intergiciel (middleware), définissez une `AllowedHosts` clé dans *appsettings.jssur* / *appSettings. \<EnvironmentName> JSON*.</span><span class="sxs-lookup"><span data-stu-id="aa84c-430">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="aa84c-431">La valeur est une liste délimitée par des points-virgules des noms d’hôte sans numéros de port :</span><span class="sxs-lookup"><span data-stu-id="aa84c-431">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="aa84c-432">*appsettings.js*:</span><span class="sxs-lookup"><span data-stu-id="aa84c-432">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="aa84c-433">Le [middleware des en-têtes transférés](xref:host-and-deploy/proxy-load-balancer) a aussi une option <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts>.</span><span class="sxs-lookup"><span data-stu-id="aa84c-433">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="aa84c-434">Le middleware des en-têtes transférés et le middleware de filtrage d’hôtes ont des fonctionnalités similaires pour différents scénarios.</span><span class="sxs-lookup"><span data-stu-id="aa84c-434">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="aa84c-435">La définition d’`AllowedHosts` avec le middleware des en-têtes transférés est appropriée quand l’en-tête `Host` n’est pas conservé durant le transfert des requêtes avec un serveur proxy inverse ou un équilibreur de charge.</span><span class="sxs-lookup"><span data-stu-id="aa84c-435">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="aa84c-436">La définition d’`AllowedHosts` avec le middleware de filtrage d’hôtes est appropriée quand Kestrel est utilisé comme serveur de périphérie public ou que l’en-tête `Host` est directement transféré.</span><span class="sxs-lookup"><span data-stu-id="aa84c-436">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="aa84c-437">Pour plus d’informations sur le middleware des en-têtes transférés, consultez <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="aa84c-437">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="aa84c-438">Kestrel est un [serveur Web multiplateforme pour ASP.net Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="aa84c-438">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="aa84c-439">Kestrel est le serveur web inclus par défaut dans les modèles de projets ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="aa84c-439">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="aa84c-440">Kestrel prend en charge les scénarios suivants :</span><span class="sxs-lookup"><span data-stu-id="aa84c-440">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="aa84c-441">HTTPS</span><span class="sxs-lookup"><span data-stu-id="aa84c-441">HTTPS</span></span>
* <span data-ttu-id="aa84c-442">Mise à niveau opaque utilisée pour activer les [WebSockets](https://github.com/aspnet/websockets)</span><span class="sxs-lookup"><span data-stu-id="aa84c-442">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="aa84c-443">Sockets UNIX pour des performances élevées derrière Nginx</span><span class="sxs-lookup"><span data-stu-id="aa84c-443">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="aa84c-444">HTTP/2 (sauf sur macOS&dagger;)</span><span class="sxs-lookup"><span data-stu-id="aa84c-444">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="aa84c-445">&dagger;HTTP/2 sera pris en charge sur macOS dans une prochaine version.</span><span class="sxs-lookup"><span data-stu-id="aa84c-445">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="aa84c-446">Kestrel est pris en charge sur toutes les plateformes et les versions prises en charge par .NET Core.</span><span class="sxs-lookup"><span data-stu-id="aa84c-446">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="aa84c-447">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="aa84c-447">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="aa84c-448">Assistance HTTP/2</span><span class="sxs-lookup"><span data-stu-id="aa84c-448">HTTP/2 support</span></span>

<span data-ttu-id="aa84c-449">[HTTP/2](https://httpwg.org/specs/rfc7540.html) est disponible pour les applications ASP.NET Core si les conditions de base suivantes sont remplies :</span><span class="sxs-lookup"><span data-stu-id="aa84c-449">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="aa84c-450">Système d’exploitation&dagger;</span><span class="sxs-lookup"><span data-stu-id="aa84c-450">Operating system&dagger;</span></span>
  * <span data-ttu-id="aa84c-451">Windows Server 2016/Windows 10 ou version ultérieure&Dagger;</span><span class="sxs-lookup"><span data-stu-id="aa84c-451">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="aa84c-452">Linux avec OpenSSL 1.0.2 ou version ultérieure (par exemple, Ubuntu 16.04 ou version ultérieure)</span><span class="sxs-lookup"><span data-stu-id="aa84c-452">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="aa84c-453">Version cible de .Net Framework : .NET Core 2.2 ou version ultérieure</span><span class="sxs-lookup"><span data-stu-id="aa84c-453">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="aa84c-454">Connexion [ALPN (Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3)</span><span class="sxs-lookup"><span data-stu-id="aa84c-454">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="aa84c-455">TLS 1.2 ou connexion ultérieure</span><span class="sxs-lookup"><span data-stu-id="aa84c-455">TLS 1.2 or later connection</span></span>

<span data-ttu-id="aa84c-456">&dagger;HTTP/2 sera pris en charge sur macOS dans une prochaine version.</span><span class="sxs-lookup"><span data-stu-id="aa84c-456">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="aa84c-457">&Dagger;Kestrel propose une prise en charge limitée de HTTP/2 sous Windows Server 2012 R2 et Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="aa84c-457">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="aa84c-458">La prise en charge est limitée car la liste des suites de chiffrement TLS prises en charge sur ces systèmes d’exploitation est limitée.</span><span class="sxs-lookup"><span data-stu-id="aa84c-458">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="aa84c-459">Un certificat généré à l’aide d’Elliptic Curve Digital Signature algorithme (ECDSA) peut être requis pour sécuriser les connexions TLS.</span><span class="sxs-lookup"><span data-stu-id="aa84c-459">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="aa84c-460">Si une connexion HTTP/2 est établie, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) retourne `HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="aa84c-460">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="aa84c-461">HTTP/2 est désactivé par défaut.</span><span class="sxs-lookup"><span data-stu-id="aa84c-461">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="aa84c-462">Pour plus d’informations sur la configuration, consultez les sections [Options Kestrel](#kestrel-options) et [ListenOptions.Protocols](#listenoptionsprotocols).</span><span class="sxs-lookup"><span data-stu-id="aa84c-462">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="aa84c-463">Quand utiliser Kestrel avec un proxy inverse ?</span><span class="sxs-lookup"><span data-stu-id="aa84c-463">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="aa84c-464">Vous pouvez utiliser Kestrel par lui-même ou avec un *serveur proxy inverse*, comme [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org) ou [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="aa84c-464">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="aa84c-465">Un serveur proxy inverse reçoit les requêtes HTTP en provenance du réseau et les transmet à Kestrel.</span><span class="sxs-lookup"><span data-stu-id="aa84c-465">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="aa84c-466">Kestrel utilisé comme serveur web edge (accessible sur Internet) :</span><span class="sxs-lookup"><span data-stu-id="aa84c-466">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel communique directement avec Internet sans serveur proxy inverse](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="aa84c-468">Kestrel utilisé dans une configuration de proxy inverse :</span><span class="sxs-lookup"><span data-stu-id="aa84c-468">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel communique indirectement avec Internet via un serveur proxy inverse, par exemple IIS, Nginx ou Apache](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="aa84c-470">L’une ou l’autre des configurations, avec ou sans serveur proxy inverse, est une configuration d’hébergement prise en charge.</span><span class="sxs-lookup"><span data-stu-id="aa84c-470">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="aa84c-471">Kestrel, s’il est utilisé comme serveur de périphérie sans serveur proxy inverse, ne prend pas en charge le partage de la même adresse IP et du même port entre plusieurs processus.</span><span class="sxs-lookup"><span data-stu-id="aa84c-471">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="aa84c-472">Quand Kestrel est configuré pour écouter sur un port, il gère tout le trafic pour ce port, quel que soit les en-têtes `Host` des requêtes.</span><span class="sxs-lookup"><span data-stu-id="aa84c-472">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="aa84c-473">Un proxy inverse qui peut partager des ports a la possibilité de transférer des requêtes à Kestrel sur une adresse IP et un port uniques.</span><span class="sxs-lookup"><span data-stu-id="aa84c-473">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="aa84c-474">Même si un serveur proxy inverse n’est pas nécessaire, en utiliser un peut être un bon choix.</span><span class="sxs-lookup"><span data-stu-id="aa84c-474">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="aa84c-475">Un proxy inverse :</span><span class="sxs-lookup"><span data-stu-id="aa84c-475">A reverse proxy:</span></span>

* <span data-ttu-id="aa84c-476">Peut limiter la surface publique exposée des applications qu’il héberge.</span><span class="sxs-lookup"><span data-stu-id="aa84c-476">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="aa84c-477">Fournit une couche supplémentaire de configuration et de défense.</span><span class="sxs-lookup"><span data-stu-id="aa84c-477">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="aa84c-478">Peut mieux s’intégrer à l’infrastructure existante.</span><span class="sxs-lookup"><span data-stu-id="aa84c-478">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="aa84c-479">Simplifie la configuration de l’équilibrage de charge et d’une communication sécurisée (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="aa84c-479">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="aa84c-480">Seul le serveur proxy inverse requiert un certificat X. 509 et ce serveur peut communiquer avec les serveurs de l’application sur le réseau interne à l’aide du protocole HTTP simple.</span><span class="sxs-lookup"><span data-stu-id="aa84c-480">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="aa84c-481">L’hébergement dans une configuration de proxy inverse nécessite le [filtrage d’hôte](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="aa84c-481">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="aa84c-482">Comment utiliser Kestrel dans les applications ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="aa84c-482">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="aa84c-483">Le package [Microsoft. AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) est inclus dans le sous- [package Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="aa84c-483">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="aa84c-484">Les modèles de projet ASP.NET Core utilisent Kestrel par défaut.</span><span class="sxs-lookup"><span data-stu-id="aa84c-484">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="aa84c-485">Dans *Program.cs*, le modèle de code appelle <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, qui appelle <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> en arrière-plan.</span><span class="sxs-lookup"><span data-stu-id="aa84c-485">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=7)]

<span data-ttu-id="aa84c-486">Pour plus d’informations sur `CreateDefaultBuilder` et la génération de l’hôte, consultez la section *configurer un hôte* de la rubrique <xref:fundamentals/host/web-host#set-up-a-host> .</span><span class="sxs-lookup"><span data-stu-id="aa84c-486">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

<span data-ttu-id="aa84c-487">Pour fournir une configuration supplémentaire après l’appel de `CreateDefaultBuilder`, utilisez `ConfigureKestrel` :</span><span class="sxs-lookup"><span data-stu-id="aa84c-487">To provide additional configuration after calling `CreateDefaultBuilder`, use `ConfigureKestrel`:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="aa84c-488">Si l’application n’appelle pas `CreateDefaultBuilder` pour configurer l’hôte, appelez <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **avant** d’appeler `ConfigureKestrel` :</span><span class="sxs-lookup"><span data-stu-id="aa84c-488">If the app doesn't call `CreateDefaultBuilder` to set up the host, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **before** calling `ConfigureKestrel`:</span></span>

```csharp
public static void Main(string[] args)
{
    var host = new WebHostBuilder()
        .UseContentRoot(Directory.GetCurrentDirectory())
        .UseKestrel()
        .UseIISIntegration()
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        })
        .Build();

    host.Run();
}
```

## <a name="kestrel-options"></a><span data-ttu-id="aa84c-489">Options Kestrel</span><span class="sxs-lookup"><span data-stu-id="aa84c-489">Kestrel options</span></span>

<span data-ttu-id="aa84c-490">Le serveur web Kestrel a des options de configuration de contrainte qui sont particulièrement utiles dans les déploiements exposés à Internet.</span><span class="sxs-lookup"><span data-stu-id="aa84c-490">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="aa84c-491">Définissez des contraintes sur la propriété <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> de la classe <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>.</span><span class="sxs-lookup"><span data-stu-id="aa84c-491">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="aa84c-492">La propriété `Limits` conserve une instance de la classe <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>.</span><span class="sxs-lookup"><span data-stu-id="aa84c-492">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="aa84c-493">Les exemples suivants utilisent l’espace de noms <xref:Microsoft.AspNetCore.Server.Kestrel.Core> :</span><span class="sxs-lookup"><span data-stu-id="aa84c-493">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="aa84c-494">Les options Kestrel, qui sont configurées dans le code C# dans les exemples suivants, peuvent également être définies à l’aide d’un [fournisseur de configuration](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="aa84c-494">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="aa84c-495">Par exemple, le fournisseur de configuration de fichier peut charger la configuration Kestrel à partir d’un *appsettings.jssur* ou *appSettings. { Fichier Environment}. JSON* :</span><span class="sxs-lookup"><span data-stu-id="aa84c-495">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

```json
{
  "Kestrel": {
    "Limits": {
      "MaxConcurrentConnections": 100,
      "MaxConcurrentUpgradedConnections": 100
    }
  }
}
```

<span data-ttu-id="aa84c-496">Utilisez l' **une** des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="aa84c-496">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="aa84c-497">Configurer Kestrel dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="aa84c-497">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="aa84c-498">Injecte une instance de `IConfiguration` dans la `Startup` classe.</span><span class="sxs-lookup"><span data-stu-id="aa84c-498">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="aa84c-499">L’exemple suivant suppose que la configuration injectée est assignée à la `Configuration` propriété.</span><span class="sxs-lookup"><span data-stu-id="aa84c-499">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="aa84c-500">Dans `Startup.ConfigureServices` , chargez la `Kestrel` section de configuration dans la configuration de Kestrel :</span><span class="sxs-lookup"><span data-stu-id="aa84c-500">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

     ```csharp
     using Microsoft.Extensions.Configuration
     
     public class Startup
     {
         public Startup(IConfiguration configuration)
         {
             Configuration = configuration;
         }

         public IConfiguration Configuration { get; }

         public void ConfigureServices(IServiceCollection services)
         {
             services.Configure<KestrelServerOptions>(
                 Configuration.GetSection("Kestrel"));
         }

         public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
         {
             ...
         }
     }
     ```

* <span data-ttu-id="aa84c-501">Configurez Kestrel lors de la génération de l’hôte :</span><span class="sxs-lookup"><span data-stu-id="aa84c-501">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="aa84c-502">Dans *Program.cs*, chargez la `Kestrel` section de configuration dans la configuration de Kestrel :</span><span class="sxs-lookup"><span data-stu-id="aa84c-502">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

  ```csharp
  // using Microsoft.Extensions.DependencyInjection;

  public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
      WebHost.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .UseStartup<Startup>();
  ```

<span data-ttu-id="aa84c-503">Les deux approches précédentes fonctionnent avec n’importe quel [fournisseur de configuration](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="aa84c-503">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="aa84c-504">Délai d’expiration toujours actif</span><span class="sxs-lookup"><span data-stu-id="aa84c-504">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="aa84c-505">Obtient ou définit le [délai d’expiration toujours actif](https://tools.ietf.org/html/rfc7230#section-6.5).</span><span class="sxs-lookup"><span data-stu-id="aa84c-505">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="aa84c-506">La valeur par défaut est de 2 minutes.</span><span class="sxs-lookup"><span data-stu-id="aa84c-506">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=15)]

### <a name="maximum-client-connections"></a><span data-ttu-id="aa84c-507">Nombre maximale de connexions client</span><span class="sxs-lookup"><span data-stu-id="aa84c-507">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="aa84c-508">Le nombre maximal de connexions TCP ouvertes simultanées peut être défini pour l’application entière avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="aa84c-508">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="aa84c-509">Il existe une limite distincte pour les connexions qui ont été mises à niveau à partir de HTTP ou HTTPS vers un autre protocole (par exemple, sur une demande WebSocket).</span><span class="sxs-lookup"><span data-stu-id="aa84c-509">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="aa84c-510">Une fois mise à niveau, une connexion n’est pas prise en compte dans la limite `MaxConcurrentConnections`.</span><span class="sxs-lookup"><span data-stu-id="aa84c-510">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="aa84c-511">Le nombre maximal de connexions est illimité (null) par défaut.</span><span class="sxs-lookup"><span data-stu-id="aa84c-511">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="aa84c-512">Taille maximale du corps de la requête</span><span class="sxs-lookup"><span data-stu-id="aa84c-512">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="aa84c-513">La taille maximale par défaut du corps de la requête est de 30 000 000 octets, soit environ 28,6 Mo.</span><span class="sxs-lookup"><span data-stu-id="aa84c-513">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="aa84c-514">Pour remplacer la limite dans une application ASP.NET Core MVC, nous vous recommandons d’utiliser l’attribut <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> sur une méthode d’action :</span><span class="sxs-lookup"><span data-stu-id="aa84c-514">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="aa84c-515">Voici un exemple qui montre comment configurer la contrainte pour l’application sur chaque requête :</span><span class="sxs-lookup"><span data-stu-id="aa84c-515">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="aa84c-516">Remplacer le paramètre sur une demande spécifique dans l’intergiciel (middleware) :</span><span class="sxs-lookup"><span data-stu-id="aa84c-516">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="aa84c-517">Une exception est levée si l’application configure la limite sur une demande après que l’application a commencé à lire la demande.</span><span class="sxs-lookup"><span data-stu-id="aa84c-517">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="aa84c-518">Il existe une propriété `IsReadOnly` qui indique si la propriété `MaxRequestBodySize` est en lecture seule ; si tel est le cas, il est trop tard pour configurer la limite.</span><span class="sxs-lookup"><span data-stu-id="aa84c-518">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="aa84c-519">Quand une application est exécutée [hors processus](xref:host-and-deploy/iis/index#out-of-process-hosting-model) derrière le [Module ASP.NET Core](xref:host-and-deploy/aspnet-core-module), la limite de taille du corps de demande de Kestrel est désactivée, car IIS définit déjà la limite.</span><span class="sxs-lookup"><span data-stu-id="aa84c-519">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="aa84c-520">Débit données minimal du corps de la requête</span><span class="sxs-lookup"><span data-stu-id="aa84c-520">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="aa84c-521">Kestrel vérifie à chaque seconde si les données arrivent au débit spécifié en octets/seconde.</span><span class="sxs-lookup"><span data-stu-id="aa84c-521">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="aa84c-522">Si le taux chute au-dessous de la valeur minimale, le délai d’attente de la connexion est dépassé. La période de grâce correspond à la durée pendant laquelle Kestrel permet au client d’augmenter son taux d’envoi jusqu’à la valeur minimale. la vitesse n’est pas vérifiée pendant cette période.</span><span class="sxs-lookup"><span data-stu-id="aa84c-522">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="aa84c-523">La période de grâce permet d’éviter la suppression des connexions qui, initialement, envoient des données à une vitesse lente en raison de la lenteur du démarrage de TCP.</span><span class="sxs-lookup"><span data-stu-id="aa84c-523">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="aa84c-524">Le débit minimal par défaut est 240 octets/seconde, avec une période de grâce de 5 secondes.</span><span class="sxs-lookup"><span data-stu-id="aa84c-524">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="aa84c-525">Un débit minimal s’applique également à la réponse.</span><span class="sxs-lookup"><span data-stu-id="aa84c-525">A minimum rate also applies to the response.</span></span> <span data-ttu-id="aa84c-526">Le code pour définir les limites de demande et de réponse est identique à l’exception de `RequestBody` ou `Response` dans les noms de propriété et d’interface.</span><span class="sxs-lookup"><span data-stu-id="aa84c-526">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="aa84c-527">Voici un exemple qui montre comment configurer les débits de données minimaux dans *Program.cs* :</span><span class="sxs-lookup"><span data-stu-id="aa84c-527">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-9)]

<span data-ttu-id="aa84c-528">Remplacer les limites de taux minimum par demande dans l’intergiciel (middleware) :</span><span class="sxs-lookup"><span data-stu-id="aa84c-528">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="aa84c-529">Aucune des fonctionnalités de débit référencées dans l’exemple précédent n’est présente dans `HttpContext.Features` pour les requêtes HTTP/2, car la modification des limites de débit par requête n’est pas prise en charge pour HTTP/2 (le protocole prend en charge le multiplexage de requête).</span><span class="sxs-lookup"><span data-stu-id="aa84c-529">Neither rate feature referenced in the prior sample are present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis isn't supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="aa84c-530">Les limites de débit à l’échelle du serveur configurées par le biais de `KestrelServerOptions.Limits` s’appliquent encore aux connexions HTTP/1.x et HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="aa84c-530">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="aa84c-531">Délai d’expiration des en-têtes de requête</span><span class="sxs-lookup"><span data-stu-id="aa84c-531">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="aa84c-532">Obtient ou définit le temps maximal passé par le serveur à recevoir des en-têtes de requête.</span><span class="sxs-lookup"><span data-stu-id="aa84c-532">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="aa84c-533">La valeur par défaut est de 30 secondes.</span><span class="sxs-lookup"><span data-stu-id="aa84c-533">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=16)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="aa84c-534">Flux de données maximal par connexion</span><span class="sxs-lookup"><span data-stu-id="aa84c-534">Maximum streams per connection</span></span>

<span data-ttu-id="aa84c-535">`Http2.MaxStreamsPerConnection` limite le nombre de flux de requête simultanée par connexion HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="aa84c-535">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="aa84c-536">Les flux de données excédentaires sont refusés.</span><span class="sxs-lookup"><span data-stu-id="aa84c-536">Excess streams are refused.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
        });
```

<span data-ttu-id="aa84c-537">La valeur par défaut est 100.</span><span class="sxs-lookup"><span data-stu-id="aa84c-537">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="aa84c-538">Taille de la table d’en-tête</span><span class="sxs-lookup"><span data-stu-id="aa84c-538">Header table size</span></span>

<span data-ttu-id="aa84c-539">Le décodeur HPACK décompresse les en-têtes HTTP pour les connexions HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="aa84c-539">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="aa84c-540">`Http2.HeaderTableSize` limite la taille de la table de compression d’en-tête que le décodeur HPACK utilise.</span><span class="sxs-lookup"><span data-stu-id="aa84c-540">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="aa84c-541">La valeur est fournie en octets et doit être supérieure à zéro (0).</span><span class="sxs-lookup"><span data-stu-id="aa84c-541">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.HeaderTableSize = 4096;
        });
```

<span data-ttu-id="aa84c-542">La valeur par défaut est 4096.</span><span class="sxs-lookup"><span data-stu-id="aa84c-542">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="aa84c-543">Taille de trame maximale</span><span class="sxs-lookup"><span data-stu-id="aa84c-543">Maximum frame size</span></span>

<span data-ttu-id="aa84c-544">`Http2.MaxFrameSize` Indique la taille maximale de charge utile dans la trame de connexion HTTP/2 à recevoir.</span><span class="sxs-lookup"><span data-stu-id="aa84c-544">`Http2.MaxFrameSize` indicates the maximum size of the HTTP/2 connection frame payload to receive.</span></span> <span data-ttu-id="aa84c-545">La valeur est fournie en octets et doit être comprise entre 2^14 (16,384) et 2^24-1 (16,777,215).</span><span class="sxs-lookup"><span data-stu-id="aa84c-545">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxFrameSize = 16384;
        });
```

<span data-ttu-id="aa84c-546">La valeur par défaut est 2^14 (16,384).</span><span class="sxs-lookup"><span data-stu-id="aa84c-546">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="aa84c-547">Taille maximale d’en-tête de requête</span><span class="sxs-lookup"><span data-stu-id="aa84c-547">Maximum request header size</span></span>

<span data-ttu-id="aa84c-548">`Http2.MaxRequestHeaderFieldSize` indique la taille maximale autorisée en octets des valeurs d’en-tête de requête.</span><span class="sxs-lookup"><span data-stu-id="aa84c-548">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="aa84c-549">Cette limite s’applique au nom et à la valeur dans leurs représentations compressées et non compressées.</span><span class="sxs-lookup"><span data-stu-id="aa84c-549">This limit applies to both name and value together in their compressed and uncompressed representations.</span></span> <span data-ttu-id="aa84c-550">La valeur doit être supérieure à zéro (0).</span><span class="sxs-lookup"><span data-stu-id="aa84c-550">The value must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
        });
```

<span data-ttu-id="aa84c-551">La valeur par défaut est 8 192.</span><span class="sxs-lookup"><span data-stu-id="aa84c-551">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="aa84c-552">Taille de fenêtre de connexion initiale</span><span class="sxs-lookup"><span data-stu-id="aa84c-552">Initial connection window size</span></span>

<span data-ttu-id="aa84c-553">`Http2.InitialConnectionWindowSize` indique la quantité maximale de données de corps de requête, en octets, que le serveur met en mémoire tampon à un moment donné pour toutes les requêtes (flux) par connexion.</span><span class="sxs-lookup"><span data-stu-id="aa84c-553">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="aa84c-554">Les requêtes sont également limitées par `Http2.InitialStreamWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="aa84c-554">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="aa84c-555">La valeur doit être supérieure ou égale à 65 535 et inférieure à 2^31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="aa84c-555">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
        });
```

<span data-ttu-id="aa84c-556">La valeur par défaut est 128 Ko (131 072).</span><span class="sxs-lookup"><span data-stu-id="aa84c-556">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="aa84c-557">Taille de la fenêtre de flux initiale</span><span class="sxs-lookup"><span data-stu-id="aa84c-557">Initial stream window size</span></span>

<span data-ttu-id="aa84c-558">`Http2.InitialStreamWindowSize` indique la quantité maximale de données de corps de requête, en octets, que le serveur met en mémoire tampon à un moment donné par requête (flux).</span><span class="sxs-lookup"><span data-stu-id="aa84c-558">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="aa84c-559">Les requêtes sont également limitées par `Http2.InitialStreamWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="aa84c-559">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="aa84c-560">La valeur doit être supérieure ou égale à 65 535 et inférieure à 2^31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="aa84c-560">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
        });
```

<span data-ttu-id="aa84c-561">La valeur par défaut est 96 Ko (98 304).</span><span class="sxs-lookup"><span data-stu-id="aa84c-561">The default value is 96 KB (98,304).</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="aa84c-562">E/S synchrone</span><span class="sxs-lookup"><span data-stu-id="aa84c-562">Synchronous I/O</span></span>

<span data-ttu-id="aa84c-563"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> contrôle si des e/s synchrones sont autorisées pour la demande et la réponse.</span><span class="sxs-lookup"><span data-stu-id="aa84c-563"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="aa84c-564">La valeur par défaut est `true`.</span><span class="sxs-lookup"><span data-stu-id="aa84c-564">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="aa84c-565">Un grand nombre d’opérations d’e/s synchrones bloquantes peuvent entraîner une insuffisance du pool de threads, ce qui empêche l’application de répondre.</span><span class="sxs-lookup"><span data-stu-id="aa84c-565">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="aa84c-566">Activez uniquement `AllowSynchronousIO` lors de l’utilisation d’une bibliothèque qui ne prend pas en charge les e/s asynchrones.</span><span class="sxs-lookup"><span data-stu-id="aa84c-566">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="aa84c-567">L’exemple suivant active des e/s synchrones :</span><span class="sxs-lookup"><span data-stu-id="aa84c-567">The following example enables synchronous I/O:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="aa84c-568">Pour plus d’informations sur les autres options et limites de Kestrel, consultez :</span><span class="sxs-lookup"><span data-stu-id="aa84c-568">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="aa84c-569">Configuration du point de terminaison</span><span class="sxs-lookup"><span data-stu-id="aa84c-569">Endpoint configuration</span></span>

<span data-ttu-id="aa84c-570">Par défaut, ASP.NET Core se lie à :</span><span class="sxs-lookup"><span data-stu-id="aa84c-570">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="aa84c-571">`https://localhost:5001` (quand un certificat de développement local est présent)</span><span class="sxs-lookup"><span data-stu-id="aa84c-571">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="aa84c-572">Spécifiez les URL avec :</span><span class="sxs-lookup"><span data-stu-id="aa84c-572">Specify URLs using the:</span></span>

* <span data-ttu-id="aa84c-573">La variable d’environnement `ASPNETCORE_URLS`.</span><span class="sxs-lookup"><span data-stu-id="aa84c-573">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="aa84c-574">L’argument de ligne de commande `--urls`.</span><span class="sxs-lookup"><span data-stu-id="aa84c-574">`--urls` command-line argument.</span></span>
* <span data-ttu-id="aa84c-575">La clé de configuration d’hôte `urls`.</span><span class="sxs-lookup"><span data-stu-id="aa84c-575">`urls` host configuration key.</span></span>
* <span data-ttu-id="aa84c-576">La méthode d’extension `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="aa84c-576">`UseUrls` extension method.</span></span>

<span data-ttu-id="aa84c-577">La valeur fournie avec ces approches peut être un ou plusieurs points de terminaison HTTP et HTTPS (HTTPS si un certificat par défaut est disponible).</span><span class="sxs-lookup"><span data-stu-id="aa84c-577">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="aa84c-578">Configurez la valeur sous forme de liste délimitée par des points-virgules (par exemple `"Urls": "http://localhost:8000;http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="aa84c-578">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="aa84c-579">Pour plus d’informations sur ces approches, voir [URL de serveur](xref:fundamentals/host/web-host#server-urls) et [Remplacer la configuration](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="aa84c-579">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="aa84c-580">Un certificat de développement est créé :</span><span class="sxs-lookup"><span data-stu-id="aa84c-580">A development certificate is created:</span></span>

* <span data-ttu-id="aa84c-581">Quand le [SDK .NET Core](/dotnet/core/sdk) est installé.</span><span class="sxs-lookup"><span data-stu-id="aa84c-581">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="aa84c-582">[L’outil dev-certs](xref:aspnetcore-2.1#https) est utilisé pour créer un certificat.</span><span class="sxs-lookup"><span data-stu-id="aa84c-582">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="aa84c-583">Certains navigateurs requièrent l’octroi d’une autorisation explicite pour approuver le certificat de développement local.</span><span class="sxs-lookup"><span data-stu-id="aa84c-583">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="aa84c-584">Les modèles de projet configurent les applications à exécuter sur HTTPs par défaut et incluent [la redirection https et la prise en charge de HSTS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="aa84c-584">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="aa84c-585">Appelez les méthodes <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> ou <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> sur <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> pour configurer les préfixes et les ports d’URL pour Kestrel.</span><span class="sxs-lookup"><span data-stu-id="aa84c-585">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="aa84c-586">`UseUrls`, l’argument de ligne de commande `--urls`, la clé de configuration d’hôte `urls` et la variable d’environnement `ASPNETCORE_URLS` fonctionnent également, mais ils présentent les limitations indiquées plus loin dans cette section (un certificat par défaut doit être disponible pour la configuration du point de terminaison HTTPS).</span><span class="sxs-lookup"><span data-stu-id="aa84c-586">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="aa84c-587">`KestrelServerOptions` configuré</span><span class="sxs-lookup"><span data-stu-id="aa84c-587">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="aa84c-588">ConfigureEndpointDefaults (action \<ListenOptions> )</span><span class="sxs-lookup"><span data-stu-id="aa84c-588">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="aa84c-589">Spécifie une `Action` de configuration à exécuter pour chaque point de terminaison spécifié.</span><span class="sxs-lookup"><span data-stu-id="aa84c-589">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="aa84c-590">Le fait d’appeler `ConfigureEndpointDefaults` plusieurs fois remplace les `Action`s précédentes par la dernière `Action` spécifiée.</span><span class="sxs-lookup"><span data-stu-id="aa84c-590">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
        });
```

> [!NOTE]
> <span data-ttu-id="aa84c-591"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> Les valeurs par défaut sont appliquées aux points de terminaison créés en appelant avant d’appeler.</span><span class="sxs-lookup"><span data-stu-id="aa84c-591">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="aa84c-592">ConfigureHttpsDefaults (action \<HttpsConnectionAdapterOptions> )</span><span class="sxs-lookup"><span data-stu-id="aa84c-592">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="aa84c-593">Spécifie une `Action` de configuration à exécuter pour chaque point de terminaison HTTPS.</span><span class="sxs-lookup"><span data-stu-id="aa84c-593">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="aa84c-594">Le fait d’appeler `ConfigureHttpsDefaults` plusieurs fois remplace les `Action`s précédentes par la dernière `Action` spécifiée.</span><span class="sxs-lookup"><span data-stu-id="aa84c-594">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                // certificate is an X509Certificate2
                listenOptions.ServerCertificate = certificate;
            });
        });
```

> [!NOTE]
> <span data-ttu-id="aa84c-595"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> Les valeurs par défaut sont appliquées aux points de terminaison créés en appelant avant d’appeler.</span><span class="sxs-lookup"><span data-stu-id="aa84c-595">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>


### <a name="configureiconfiguration"></a><span data-ttu-id="aa84c-596">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="aa84c-596">Configure(IConfiguration)</span></span>

<span data-ttu-id="aa84c-597">Crée un chargeur de configuration pour configurer Kestrel, qui prend en entrée une <xref:Microsoft.Extensions.Configuration.IConfiguration>.</span><span class="sxs-lookup"><span data-stu-id="aa84c-597">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="aa84c-598">La configuration doit être limitée à la section de configuration pour Kestrel.</span><span class="sxs-lookup"><span data-stu-id="aa84c-598">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="aa84c-599">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="aa84c-599">ListenOptions.UseHttps</span></span>

<span data-ttu-id="aa84c-600">Configure Kestrel pour l’utilisation de HTTPS.</span><span class="sxs-lookup"><span data-stu-id="aa84c-600">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="aa84c-601">Extensions de `ListenOptions.UseHttps` :</span><span class="sxs-lookup"><span data-stu-id="aa84c-601">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="aa84c-602">`UseHttps`: Configurez Kestrel pour utiliser le protocole HTTPs avec le certificat par défaut.</span><span class="sxs-lookup"><span data-stu-id="aa84c-602">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="aa84c-603">Lève une exception si aucun certificat par défaut n’est configuré.</span><span class="sxs-lookup"><span data-stu-id="aa84c-603">Throws an exception if no default certificate is configured.</span></span>
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

<span data-ttu-id="aa84c-604">Paramètres de `ListenOptions.UseHttps` :</span><span class="sxs-lookup"><span data-stu-id="aa84c-604">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="aa84c-605">`filename` est le chemin et le nom d’un fichier de certificat, relatif au répertoire qui contient les fichiers de contenu de l’application.</span><span class="sxs-lookup"><span data-stu-id="aa84c-605">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="aa84c-606">`password` est le mot de passe nécessaire pour accéder aux données du certificat X.509.</span><span class="sxs-lookup"><span data-stu-id="aa84c-606">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="aa84c-607">`configureOptions` est une `Action` pour configurer les `HttpsConnectionAdapterOptions`.</span><span class="sxs-lookup"><span data-stu-id="aa84c-607">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="aa84c-608">Retourne l'`ListenOptions`.</span><span class="sxs-lookup"><span data-stu-id="aa84c-608">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="aa84c-609">`storeName` est le magasin de certificats à partir duquel charger le certificat.</span><span class="sxs-lookup"><span data-stu-id="aa84c-609">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="aa84c-610">`subject` est le nom du sujet du certificat.</span><span class="sxs-lookup"><span data-stu-id="aa84c-610">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="aa84c-611">`allowInvalid` indique si les certificats non valides doivent être considérés, comme les certificats auto-signés.</span><span class="sxs-lookup"><span data-stu-id="aa84c-611">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="aa84c-612">`location` est l’emplacement du magasin à partir duquel charger le certificat.</span><span class="sxs-lookup"><span data-stu-id="aa84c-612">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="aa84c-613">`serverCertificate` est le certificat X.509.</span><span class="sxs-lookup"><span data-stu-id="aa84c-613">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="aa84c-614">En production, HTTPS doit être explicitement configuré.</span><span class="sxs-lookup"><span data-stu-id="aa84c-614">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="aa84c-615">Au minimum, un certificat par défaut doit être fourni.</span><span class="sxs-lookup"><span data-stu-id="aa84c-615">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="aa84c-616">Configurations prises en charge décrites dans la suite :</span><span class="sxs-lookup"><span data-stu-id="aa84c-616">Supported configurations described next:</span></span>

* <span data-ttu-id="aa84c-617">Pas de configuration</span><span class="sxs-lookup"><span data-stu-id="aa84c-617">No configuration</span></span>
* <span data-ttu-id="aa84c-618">Remplacer le certificat par défaut dans la configuration</span><span class="sxs-lookup"><span data-stu-id="aa84c-618">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="aa84c-619">Changer les valeurs par défaut dans le code</span><span class="sxs-lookup"><span data-stu-id="aa84c-619">Change the defaults in code</span></span>

<span data-ttu-id="aa84c-620">*Pas de configuration*</span><span class="sxs-lookup"><span data-stu-id="aa84c-620">*No configuration*</span></span>

<span data-ttu-id="aa84c-621">Kestrel écoute sur `http://localhost:5000` et sur `https://localhost:5001` (si un certificat par défaut est disponible).</span><span class="sxs-lookup"><span data-stu-id="aa84c-621">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="aa84c-622">*Remplacer le certificat par défaut dans la configuration*</span><span class="sxs-lookup"><span data-stu-id="aa84c-622">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="aa84c-623">Par défaut, `CreateDefaultBuilder` appelle `Configure(context.Configuration.GetSection("Kestrel"))` pour charger la configuration de Kestrel.</span><span class="sxs-lookup"><span data-stu-id="aa84c-623">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="aa84c-624">Un schéma de configuration des paramètres d’application HTTPS par défaut est disponible pour Kestrel.</span><span class="sxs-lookup"><span data-stu-id="aa84c-624">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="aa84c-625">Configurez plusieurs points de terminaison, notamment les URL et les certificats à utiliser, à partir d’un fichier sur disque ou d’un magasin de certificats.</span><span class="sxs-lookup"><span data-stu-id="aa84c-625">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="aa84c-626">Dans l’exemple de fichier *appsettings.json* suivant :</span><span class="sxs-lookup"><span data-stu-id="aa84c-626">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="aa84c-627">Définissez **AllowInvalid** sur `true` pour permettre l’utilisation de certificats non valides (par exemple des certificats auto-signés).</span><span class="sxs-lookup"><span data-stu-id="aa84c-627">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="aa84c-628">Tout point de terminaison HTTPS qui ne spécifie pas de certificat (**HttpsDefaultCert** dans l’exemple qui suit) revient au certificat défini sous **Certificats** > \*\*Par défaut \*\* ou au certificat de développement.</span><span class="sxs-lookup"><span data-stu-id="aa84c-628">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },

      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },

      "HttpsInlineCertStore": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },

      "HttpsDefaultCert": {
        "Url": "https://localhost:5003"
      },

      "Https": {
        "Url": "https://*:5004",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

<span data-ttu-id="aa84c-629">Une alternative à l’utilisation de **Chemin** et de **Mot de passe** pour un nœud de certificat consiste à spécifier le certificat avec des champs du magasin de certificats.</span><span class="sxs-lookup"><span data-stu-id="aa84c-629">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="aa84c-630">Par exemple, le **Certificates**  >  certificat**par défaut** des certificats peut être spécifié comme suit :</span><span class="sxs-lookup"><span data-stu-id="aa84c-630">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="aa84c-631">Notes de schéma :</span><span class="sxs-lookup"><span data-stu-id="aa84c-631">Schema notes:</span></span>

* <span data-ttu-id="aa84c-632">Les noms des points de terminaison ne respectent pas la casse.</span><span class="sxs-lookup"><span data-stu-id="aa84c-632">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="aa84c-633">Par exemple, `HTTPS` et `Https` sont valides.</span><span class="sxs-lookup"><span data-stu-id="aa84c-633">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="aa84c-634">Le paramètre `Url` est obligatoire pour chaque point de terminaison.</span><span class="sxs-lookup"><span data-stu-id="aa84c-634">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="aa84c-635">Le format de ce paramètre est le même que celui du paramètre de configuration `Urls` du plus haut niveau, sauf qu’il est limité à une seule valeur.</span><span class="sxs-lookup"><span data-stu-id="aa84c-635">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="aa84c-636">Ces points de terminaison remplacent ceux qui sont définis dans le paramètre de configuration `Urls` du plus haut niveau configuration, au lieu de s’y ajouter.</span><span class="sxs-lookup"><span data-stu-id="aa84c-636">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="aa84c-637">Les points de terminaison définis dans le code via `Listen` sont cumulatifs avec les points de terminaison définis dans la section de configuration.</span><span class="sxs-lookup"><span data-stu-id="aa84c-637">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="aa84c-638">La section `Certificate` est facultative.</span><span class="sxs-lookup"><span data-stu-id="aa84c-638">The `Certificate` section is optional.</span></span> <span data-ttu-id="aa84c-639">Si la section `Certificate` n’est pas spécifiée, les valeurs par défaut définies dans les scénarios précédents sont utilisées.</span><span class="sxs-lookup"><span data-stu-id="aa84c-639">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="aa84c-640">Si aucune valeur par défaut n’est disponible, le serveur lève une exception et son démarrage échoue.</span><span class="sxs-lookup"><span data-stu-id="aa84c-640">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="aa84c-641">La `Certificate` section prend en **Path**charge les &ndash; certificats**de mot de passe** de chemin d’accès et de magasin d' **objets** &ndash; **Store** .</span><span class="sxs-lookup"><span data-stu-id="aa84c-641">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="aa84c-642">Vous pouvez définir un nombre quelconque de points de terminaison de cette façon, pour autant qu’ils ne provoquent pas de conflits de port.</span><span class="sxs-lookup"><span data-stu-id="aa84c-642">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="aa84c-643">`options.Configure(context.Configuration.GetSection("{SECTION}"))` retourne un `KestrelConfigurationLoader` avec une méthode `.Endpoint(string name, listenOptions => { })` qui peut être utilisée pour compléter les paramètres d’un point de terminaison configuré :</span><span class="sxs-lookup"><span data-stu-id="aa84c-643">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
                .Endpoint("HTTPS", listenOptions =>
                {
                    listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
                });
        });
```

<span data-ttu-id="aa84c-644">`KestrelServerOptions.ConfigurationLoader` peut être directement accessible pour poursuivre l’itération sur le chargeur existant, tel que celui fourni par <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> .</span><span class="sxs-lookup"><span data-stu-id="aa84c-644">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="aa84c-645">La section de configuration pour chaque point de terminaison est disponible sur les options de la `Endpoint` méthode afin que les paramètres personnalisés puissent être lus.</span><span class="sxs-lookup"><span data-stu-id="aa84c-645">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="aa84c-646">Plusieurs configurations peuvent être chargées en rappelant `options.Configure(context.Configuration.GetSection("{SECTION}"))` avec une autre section.</span><span class="sxs-lookup"><span data-stu-id="aa84c-646">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="aa84c-647">Seule la dernière configuration est utilisée, à moins que `Load` soit explicitement appelé sur les instances précédentes.</span><span class="sxs-lookup"><span data-stu-id="aa84c-647">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="aa84c-648">Le métapackage n’appelle pas `Load` : sa section de configuration par défaut peut donc être remplacée.</span><span class="sxs-lookup"><span data-stu-id="aa84c-648">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="aa84c-649">`KestrelConfigurationLoader` reflète la famille d’API `Listen` de `KestrelServerOptions` sous forme de surcharges de `Endpoint` : le code et les points de terminaison de configuration peuvent donc être configurés au même emplacement.</span><span class="sxs-lookup"><span data-stu-id="aa84c-649">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="aa84c-650">Ces surcharges n’utilisent pas de noms et consomment seulement les paramètres par défaut de la configuration.</span><span class="sxs-lookup"><span data-stu-id="aa84c-650">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="aa84c-651">*Changer les valeurs par défaut dans le code*</span><span class="sxs-lookup"><span data-stu-id="aa84c-651">*Change the defaults in code*</span></span>

<span data-ttu-id="aa84c-652">`ConfigureEndpointDefaults` et `ConfigureHttpsDefaults` peuvent être utilisés pour modifier les paramètres par défaut pour `ListenOptions` et `HttpsConnectionAdapterOptions`, notamment en remplaçant le certificat par défaut spécifié dans le scénario précédent.</span><span class="sxs-lookup"><span data-stu-id="aa84c-652">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="aa84c-653">`ConfigureEndpointDefaults` et `ConfigureHttpsDefaults` doivent être appelés avant que des points de terminaison soient configurés.</span><span class="sxs-lookup"><span data-stu-id="aa84c-653">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
            
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                listenOptions.SslProtocols = SslProtocols.Tls12;
            });
        });
```

<span data-ttu-id="aa84c-654">*Prise en charge de SNI par Kestrel*</span><span class="sxs-lookup"><span data-stu-id="aa84c-654">*Kestrel support for SNI*</span></span>

<span data-ttu-id="aa84c-655">[L’indication du nom de serveur (SNI, Server Name Indication)](https://tools.ietf.org/html/rfc6066#section-3) peut être utilisée pour héberger plusieurs domaines sur la même adresse IP et le même port.</span><span class="sxs-lookup"><span data-stu-id="aa84c-655">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="aa84c-656">Pour que SNI fonctionne, le client envoie le nom d’hôte pour la session sécurisée au serveur pendant la négociation TLS afin que le serveur puisse fournir le certificat correct.</span><span class="sxs-lookup"><span data-stu-id="aa84c-656">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="aa84c-657">Le client utilise le certificat fourni pour la communication chiffrée avec le serveur pendant la session sécurisée qui suit la négociation TLS.</span><span class="sxs-lookup"><span data-stu-id="aa84c-657">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="aa84c-658">Kestrel prend en charge SNI via le rappel de `ServerCertificateSelector`.</span><span class="sxs-lookup"><span data-stu-id="aa84c-658">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="aa84c-659">Le rappel est appelé une fois par connexion pour permettre à l’application d’inspecter le nom d’hôte et de sélectionner le certificat approprié.</span><span class="sxs-lookup"><span data-stu-id="aa84c-659">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="aa84c-660">La prise en charge de SNI nécessite les points suivants :</span><span class="sxs-lookup"><span data-stu-id="aa84c-660">SNI support requires:</span></span>

* <span data-ttu-id="aa84c-661">Exécution sur la version cible de .NET Framework `netcoreapp2.1` ou version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="aa84c-661">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="aa84c-662">Sur `net461` ou version ultérieure, le rappel est appelé, mais `name` est toujours `null` .</span><span class="sxs-lookup"><span data-stu-id="aa84c-662">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="aa84c-663">`name` est également `null` si le client ne fournit pas le paramètre du nom d’hôte dans la négociation TLS.</span><span class="sxs-lookup"><span data-stu-id="aa84c-663">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="aa84c-664">Tous les sites web s’exécutent sur la même instance Kestrel.</span><span class="sxs-lookup"><span data-stu-id="aa84c-664">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="aa84c-665">Kestrel ne prend pas en charge le partage d’une adresse IP et d’un port entre plusieurs instances sans un proxy inverse.</span><span class="sxs-lookup"><span data-stu-id="aa84c-665">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ListenAnyIP(5005, listenOptions =>
            {
                listenOptions.UseHttps(httpsOptions =>
                {
                    var localhostCert = CertificateLoader.LoadFromStoreCert(
                        "localhost", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var exampleCert = CertificateLoader.LoadFromStoreCert(
                        "example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var subExampleCert = CertificateLoader.LoadFromStoreCert(
                        "sub.example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var certs = new Dictionary<string, X509Certificate2>(
                        StringComparer.OrdinalIgnoreCase);
                    certs["localhost"] = localhostCert;
                    certs["example.com"] = exampleCert;
                    certs["sub.example.com"] = subExampleCert;

                    httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
                    {
                        if (name != null && certs.TryGetValue(name, out var cert))
                        {
                            return cert;
                        }

                        return exampleCert;
                    };
                });
            });
        });
```

### <a name="connection-logging"></a><span data-ttu-id="aa84c-666">Journalisation des connexions</span><span class="sxs-lookup"><span data-stu-id="aa84c-666">Connection logging</span></span>

<span data-ttu-id="aa84c-667">Appelez <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> pour émettre des journaux au niveau du débogage pour la communication au niveau de l’octet sur une connexion.</span><span class="sxs-lookup"><span data-stu-id="aa84c-667">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="aa84c-668">La journalisation des connexions est utile pour résoudre les problèmes de communication de bas niveau, par exemple lors du chiffrement TLS et derrière les proxies.</span><span class="sxs-lookup"><span data-stu-id="aa84c-668">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="aa84c-669">Si `UseConnectionLogging` est placé avant `UseHttps` , le trafic chiffré est journalisé.</span><span class="sxs-lookup"><span data-stu-id="aa84c-669">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="aa84c-670">Si `UseConnectionLogging` est placé après `UseHttps` , le trafic déchiffré est journalisé.</span><span class="sxs-lookup"><span data-stu-id="aa84c-670">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="aa84c-671">Lier à un socket TCP</span><span class="sxs-lookup"><span data-stu-id="aa84c-671">Bind to a TCP socket</span></span>

<span data-ttu-id="aa84c-672">La méthode <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> se lie à un socket TCP, et une expression lambda options permet la configuration d’un certificat X.509 :</span><span class="sxs-lookup"><span data-stu-id="aa84c-672">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=9-16)]

<span data-ttu-id="aa84c-673">L’exemple configure HTTPS pour un point de terminaison avec <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span><span class="sxs-lookup"><span data-stu-id="aa84c-673">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="aa84c-674">Utilisez la même API afin de configurer d’autres paramètres Kestrel pour des points de terminaison spécifiques.</span><span class="sxs-lookup"><span data-stu-id="aa84c-674">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="aa84c-675">Lier à un socket Unix</span><span class="sxs-lookup"><span data-stu-id="aa84c-675">Bind to a Unix socket</span></span>

<span data-ttu-id="aa84c-676">Écoutez sur un socket Unix avec <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> pour améliorer les performances avec Nginx, comme illustré dans cet exemple :</span><span class="sxs-lookup"><span data-stu-id="aa84c-676">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="aa84c-677">Dans le fichier Nginx confiuguration, affectez `server`  >  `location`  >  `proxy_pass` à l’entrée `http://unix:/tmp/{KESTREL SOCKET}:/;` .</span><span class="sxs-lookup"><span data-stu-id="aa84c-677">In the Nginx confiuguration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="aa84c-678">`{KESTREL SOCKET}` nom du Socket fourni à <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (par exemple, `kestrel-test.sock` dans l’exemple précédent).</span><span class="sxs-lookup"><span data-stu-id="aa84c-678">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="aa84c-679">Assurez-vous que le socket est accessible en écriture par Nginx (par exemple, `chmod go+w /tmp/kestrel-test.sock` ).</span><span class="sxs-lookup"><span data-stu-id="aa84c-679">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span> 

### <a name="port-0"></a><span data-ttu-id="aa84c-680">Port 0</span><span class="sxs-lookup"><span data-stu-id="aa84c-680">Port 0</span></span>

<span data-ttu-id="aa84c-681">Si vous spécifiez le numéro de port `0`, Kestrel se lie dynamiquement à un port disponible.</span><span class="sxs-lookup"><span data-stu-id="aa84c-681">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="aa84c-682">L’exemple suivant montre comment déterminer le port auquel Kestrel se lie à l’exécution :</span><span class="sxs-lookup"><span data-stu-id="aa84c-682">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="aa84c-683">Quand l’application est exécutée, la sortie de la fenêtre de console indique le port dynamique où l’application peut être atteinte :</span><span class="sxs-lookup"><span data-stu-id="aa84c-683">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="aa84c-684">Limites</span><span class="sxs-lookup"><span data-stu-id="aa84c-684">Limitations</span></span>

<span data-ttu-id="aa84c-685">Configurez des points de terminaison avec les approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="aa84c-685">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="aa84c-686">Arguments de ligne de commande `--urls`</span><span class="sxs-lookup"><span data-stu-id="aa84c-686">`--urls` command-line argument</span></span>
* <span data-ttu-id="aa84c-687">Clé de configuration d’hôte `urls`.</span><span class="sxs-lookup"><span data-stu-id="aa84c-687">`urls` host configuration key</span></span>
* <span data-ttu-id="aa84c-688">`ASPNETCORE_URLS` variable d’environnement</span><span class="sxs-lookup"><span data-stu-id="aa84c-688">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="aa84c-689">Ces méthodes sont utiles si vous voulez que votre code fonctionne avec des serveurs autres que Kestrel.</span><span class="sxs-lookup"><span data-stu-id="aa84c-689">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="aa84c-690">Toutefois, soyez conscient des limitations suivantes :</span><span class="sxs-lookup"><span data-stu-id="aa84c-690">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="aa84c-691">HTTPS ne peut pas être utilisé avec ces approches, sauf si un certificat par défaut est fourni dans la configuration du point de terminaison HTTPS (par exemple avec la configuration de `KestrelServerOptions` ou un fichier de configuration, comme illustré plus haut dans cette rubrique).</span><span class="sxs-lookup"><span data-stu-id="aa84c-691">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="aa84c-692">Quand les deux approches `Listen` et `UseUrls` sont utilisées simultanément, les points de terminaison `Listen` remplacent les points de terminaison `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="aa84c-692">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="aa84c-693">Configuration de point de terminaison IIS</span><span class="sxs-lookup"><span data-stu-id="aa84c-693">IIS endpoint configuration</span></span>

<span data-ttu-id="aa84c-694">Quand vous utilisez IIS, les liaisons d’URL pour IIS remplacent les liaisons qui sont définies par `Listen` ou par `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="aa84c-694">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="aa84c-695">Pour plus d’informations, consultez la rubrique [Module ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="aa84c-695">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="aa84c-696">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="aa84c-696">ListenOptions.Protocols</span></span>

<span data-ttu-id="aa84c-697">La propriété `Protocols` établit les protocoles HTTP (`HttpProtocols`) activés sur un point de terminaison de connexion ou pour le serveur.</span><span class="sxs-lookup"><span data-stu-id="aa84c-697">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="aa84c-698">Affectez une valeur à la propriété `Protocols` à partir de l’énumération `HttpProtocols`.</span><span class="sxs-lookup"><span data-stu-id="aa84c-698">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="aa84c-699">Valeur enum `HttpProtocols`</span><span class="sxs-lookup"><span data-stu-id="aa84c-699">`HttpProtocols` enum value</span></span> | <span data-ttu-id="aa84c-700">Protocole de connexion autorisé</span><span class="sxs-lookup"><span data-stu-id="aa84c-700">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="aa84c-701">HTTP/1.1 uniquement.</span><span class="sxs-lookup"><span data-stu-id="aa84c-701">HTTP/1.1 only.</span></span> <span data-ttu-id="aa84c-702">Peut être utilisé avec ou sans TLS.</span><span class="sxs-lookup"><span data-stu-id="aa84c-702">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="aa84c-703">HTTP/2 uniquement.</span><span class="sxs-lookup"><span data-stu-id="aa84c-703">HTTP/2 only.</span></span> <span data-ttu-id="aa84c-704">Peut être utilisé sans TLS, uniquement si le client prend en charge un [mode de connaissance préalable (Prior Knowledge)](https://tools.ietf.org/html/rfc7540#section-3.4).</span><span class="sxs-lookup"><span data-stu-id="aa84c-704">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="aa84c-705">HTTP/1.1 et HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="aa84c-705">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="aa84c-706">HTTP/2 requiert une connexion TLS et la [négociation de protocole de couche application (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) ; dans le cas contraire, la connexion par défaut est HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="aa84c-706">HTTP/2 requires a TLS and [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="aa84c-707">Le protocole par défaut est HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="aa84c-707">The default protocol is HTTP/1.1.</span></span>

<span data-ttu-id="aa84c-708">Restrictions TLS pour HTTP/2 :</span><span class="sxs-lookup"><span data-stu-id="aa84c-708">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="aa84c-709">TLS version 1.2 ou ultérieure</span><span class="sxs-lookup"><span data-stu-id="aa84c-709">TLS version 1.2 or later</span></span>
* <span data-ttu-id="aa84c-710">Renégociation désactivée</span><span class="sxs-lookup"><span data-stu-id="aa84c-710">Renegotiation disabled</span></span>
* <span data-ttu-id="aa84c-711">Compression désactivée</span><span class="sxs-lookup"><span data-stu-id="aa84c-711">Compression disabled</span></span>
* <span data-ttu-id="aa84c-712">Tailles minimales de l’échange de clé éphémère :</span><span class="sxs-lookup"><span data-stu-id="aa84c-712">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="aa84c-713">Elliptic Curve Diffie-Hellman (ECDHE) &lbrack; [RFC4492](https://www.ietf.org/rfc/rfc4492.txt) &rbrack; : 224 bits minimum</span><span class="sxs-lookup"><span data-stu-id="aa84c-713">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 224 bits minimum</span></span>
  * <span data-ttu-id="aa84c-714">Diffie-Hellman de champ fini (dhe) &lbrack; `TLS12` &rbrack; : 2048 bits minimum</span><span class="sxs-lookup"><span data-stu-id="aa84c-714">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;: 2048 bits minimum</span></span>
* <span data-ttu-id="aa84c-715">Suite de chiffrement non bloquée</span><span class="sxs-lookup"><span data-stu-id="aa84c-715">Cipher suite not blocked</span></span>

<span data-ttu-id="aa84c-716">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`&lbrack;`TLS-ECDHE`&rbrack; avec la courbe elliptique P-256 &lbrack; `FIPS186` &rbrack; est pris en charge par défaut.</span><span class="sxs-lookup"><span data-stu-id="aa84c-716">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="aa84c-717">L’exemple suivant autorise les connexions HTTP/1.1 et HTTP/2 sur le port 8000.</span><span class="sxs-lookup"><span data-stu-id="aa84c-717">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="aa84c-718">Les connexions sont sécurisées par TLS avec un certificat fourni :</span><span class="sxs-lookup"><span data-stu-id="aa84c-718">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
.ConfigureKestrel((context, serverOptions) =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.Protocols = HttpProtocols.Http1AndHttp2;
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="aa84c-719">Le cas échéant, créez une implémentation `IConnectionAdapter` pour filtrer les négociations TLS par connexion pour des chiffrements spécifiques :</span><span class="sxs-lookup"><span data-stu-id="aa84c-719">Optionally create an `IConnectionAdapter` implementation to filter TLS handshakes on a per-connection basis for specific ciphers:</span></span>

```csharp
.ConfigureKestrel((context, serverOptions) =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.Protocols = HttpProtocols.Http1AndHttp2;
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.ConnectionAdapters.Add(new TlsFilterAdapter());
    });
});
```

```csharp
private class TlsFilterAdapter : IConnectionAdapter
{
    public bool IsHttps => false;

    public Task<IAdaptedConnection> OnConnectionAsync(ConnectionAdapterContext context)
    {
        var tlsFeature = context.Features.Get<ITlsHandshakeFeature>();

        // Throw NotSupportedException for any cipher algorithm that the app doesn't
        // wish to support. Alternatively, define and compare
        // ITlsHandshakeFeature.CipherAlgorithm to a list of acceptable cipher
        // suites.
        //
        // No encryption is used with a CipherAlgorithmType.Null cipher algorithm.
        if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
        {
            throw new NotSupportedException("Prohibited cipher: " + tlsFeature.CipherAlgorithm);
        }

        return Task.FromResult<IAdaptedConnection>(new AdaptedConnection(context.ConnectionStream));
    }

    private class AdaptedConnection : IAdaptedConnection
    {
        public AdaptedConnection(Stream adaptedStream)
        {
            ConnectionStream = adaptedStream;
        }

        public Stream ConnectionStream { get; }

        public void Dispose()
        {
        }
    }
}
```

<span data-ttu-id="aa84c-720">*Définir le protocole à partir de la configuration*</span><span class="sxs-lookup"><span data-stu-id="aa84c-720">*Set the protocol from configuration*</span></span>

<span data-ttu-id="aa84c-721">Par défaut, <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> appelle `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` pour charger la configuration de Kestrel.</span><span class="sxs-lookup"><span data-stu-id="aa84c-721"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="aa84c-722">Dans l’exemple *appsettings.json* suivant, un protocole de connexion par défaut (HTTP/1.1 et HTTP/2) est établi pour l’ensemble des points de terminaison de Kestrel :</span><span class="sxs-lookup"><span data-stu-id="aa84c-722">In the following *appsettings.json* example, a default connection protocol (HTTP/1.1 and HTTP/2) is established for all of Kestrel's endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1AndHttp2"
    }
  }
}
```

<span data-ttu-id="aa84c-723">L’exemple de fichier de configuration suivant établit un protocole de connexion pour un point de terminaison spécifique :</span><span class="sxs-lookup"><span data-stu-id="aa84c-723">The following configuration file example establishes a connection protocol for a specific endpoint:</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "HttpsDefaultCert": {
        "Url": "https://localhost:5001",
        "Protocols": "Http1AndHttp2"
      }
    }
  }
}
```

<span data-ttu-id="aa84c-724">Les protocoles spécifiés dans le code remplacent les valeurs définies par configuration.</span><span class="sxs-lookup"><span data-stu-id="aa84c-724">Protocols specified in code override values set by configuration.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="aa84c-725">Configuration du transport</span><span class="sxs-lookup"><span data-stu-id="aa84c-725">Transport configuration</span></span>

<span data-ttu-id="aa84c-726">Dans ASP.NET Core 2.1, le transport par défaut de Kestrel n’est plus basé sur Libuv, mais sur des sockets managés.</span><span class="sxs-lookup"><span data-stu-id="aa84c-726">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="aa84c-727">Il s’agit d’un changement cassant pour les applications ASP.NET Core 2.0 mises à niveau vers la version 2.1 qui appellent <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> et qui dépendent d’un des packages suivants :</span><span class="sxs-lookup"><span data-stu-id="aa84c-727">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="aa84c-728">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (référence de package directe)</span><span class="sxs-lookup"><span data-stu-id="aa84c-728">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="aa84c-729">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="aa84c-729">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="aa84c-730">Pour les projets qui requièrent l’utilisation de Libuv :</span><span class="sxs-lookup"><span data-stu-id="aa84c-730">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="aa84c-731">Ajoutez une dépendance pour le package [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) au fichier projet de l’application :</span><span class="sxs-lookup"><span data-stu-id="aa84c-731">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="aa84c-732">Appelez <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> :</span><span class="sxs-lookup"><span data-stu-id="aa84c-732">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

  ```csharp
  public class Program
  {
      public static void Main(string[] args)
      {
          CreateWebHostBuilder(args).Build().Run();
      }

      public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
          WebHost.CreateDefaultBuilder(args)
              .UseLibuv()
              .UseStartup<Startup>();
  }
  ```

### <a name="url-prefixes"></a><span data-ttu-id="aa84c-733">Préfixes d’URL</span><span class="sxs-lookup"><span data-stu-id="aa84c-733">URL prefixes</span></span>

<span data-ttu-id="aa84c-734">Quand vous utilisez `UseUrls`, l’argument de ligne de commande `--urls`, la clé de configuration d’hôte `urls` ou la variable d’environnement `ASPNETCORE_URLS`, les préfixes d’URL peuvent être dans un des formats suivants.</span><span class="sxs-lookup"><span data-stu-id="aa84c-734">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="aa84c-735">Seuls les préfixes d’URL HTTP sont valides.</span><span class="sxs-lookup"><span data-stu-id="aa84c-735">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="aa84c-736">Kestrel ne prend pas en charge HTTPS lors de la configuration de liaisons d’URL avec `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="aa84c-736">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="aa84c-737">Adresse IPv4 avec numéro de port</span><span class="sxs-lookup"><span data-stu-id="aa84c-737">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="aa84c-738">`0.0.0.0` est un cas spécial qui se lie à toutes les adresses IPv4.</span><span class="sxs-lookup"><span data-stu-id="aa84c-738">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="aa84c-739">Adresse IPv6 avec numéro de port</span><span class="sxs-lookup"><span data-stu-id="aa84c-739">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="aa84c-740">`[::]` est l’équivalent IPv6 de `0.0.0.0` dans IPv4.</span><span class="sxs-lookup"><span data-stu-id="aa84c-740">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="aa84c-741">Nom d’hôte avec numéro de port</span><span class="sxs-lookup"><span data-stu-id="aa84c-741">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="aa84c-742">Les noms d’hôte, `*` et `+` ne sont pas spéciaux.</span><span class="sxs-lookup"><span data-stu-id="aa84c-742">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="aa84c-743">Tout ce qui n’est pas reconnu comme adresse IP valide ou `localhost` se lie à toutes les adresses IP IPv4 et IPv6.</span><span class="sxs-lookup"><span data-stu-id="aa84c-743">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="aa84c-744">Pour lier différents noms d’hôte à différentes applications ASP.NET Core sur le même port, utilisez [HTTP.sys](xref:fundamentals/servers/httpsys) ou un serveur proxy inverse, comme IIS, Nginx ou Apache.</span><span class="sxs-lookup"><span data-stu-id="aa84c-744">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="aa84c-745">L’hébergement dans une configuration de proxy inverse nécessite le [filtrage d’hôte](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="aa84c-745">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="aa84c-746">Nom `localhost` de l’hôte avec numéro de port ou adresse IP de bouclage avec numéro de port</span><span class="sxs-lookup"><span data-stu-id="aa84c-746">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="aa84c-747">Quand `localhost` est spécifié, Kestrel tente de se lier aux interfaces de bouclage IPv4 et IPv6.</span><span class="sxs-lookup"><span data-stu-id="aa84c-747">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="aa84c-748">Si le port demandé est en cours d’utilisation par un autre service sur l’une des interfaces de bouclage, Kestrel ne démarre pas.</span><span class="sxs-lookup"><span data-stu-id="aa84c-748">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="aa84c-749">Si l’une des interfaces de bouclage n’est pas disponible pour une raison quelconque (généralement du fait de la non-prise en charge d’IPv6), Kestrel journalise un avertissement.</span><span class="sxs-lookup"><span data-stu-id="aa84c-749">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="aa84c-750">Filtrage d’hôtes</span><span class="sxs-lookup"><span data-stu-id="aa84c-750">Host filtering</span></span>

<span data-ttu-id="aa84c-751">Si Kestrel prend en charge la configuration basée sur les préfixes, comme `http://example.com:5000`, il ignore en grande partie le nom d’hôte.</span><span class="sxs-lookup"><span data-stu-id="aa84c-751">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="aa84c-752">L’hôte `localhost` est un cas spécial utilisé pour la liaison à des adresses de bouclage.</span><span class="sxs-lookup"><span data-stu-id="aa84c-752">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="aa84c-753">Tout hôte autre qu’une adresse IP explicite se lie à toutes les adresses IP publiques.</span><span class="sxs-lookup"><span data-stu-id="aa84c-753">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="aa84c-754">Les en-têtes `Host` ne sont pas validés.</span><span class="sxs-lookup"><span data-stu-id="aa84c-754">`Host` headers aren't validated.</span></span>

<span data-ttu-id="aa84c-755">En guise de solution de contournement, utilisez le middleware de filtrage d’hôtes.</span><span class="sxs-lookup"><span data-stu-id="aa84c-755">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="aa84c-756">L’intergiciel (middleware) de filtrage d’hôte est fourni par le package [Microsoft. AspNetCore. HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) , qui est inclus dans le de [Microsoft. AspNetCore. App](xref:fundamentals/metapackage-app) (ASP.net Core 2,1 ou 2,2).</span><span class="sxs-lookup"><span data-stu-id="aa84c-756">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="aa84c-757">L’intergiciel (middleware) est ajouté par <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, qui appelle <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> :</span><span class="sxs-lookup"><span data-stu-id="aa84c-757">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="aa84c-758">Le middleware de filtrage d’hôtes est désactivé par défaut.</span><span class="sxs-lookup"><span data-stu-id="aa84c-758">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="aa84c-759">Pour activer l’intergiciel (middleware), définissez une `AllowedHosts` clé dans *appsettings.jssur* / *appSettings. \<EnvironmentName> JSON*.</span><span class="sxs-lookup"><span data-stu-id="aa84c-759">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="aa84c-760">La valeur est une liste délimitée par des points-virgules des noms d’hôte sans numéros de port :</span><span class="sxs-lookup"><span data-stu-id="aa84c-760">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="aa84c-761">*appsettings.js*:</span><span class="sxs-lookup"><span data-stu-id="aa84c-761">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="aa84c-762">Le [middleware des en-têtes transférés](xref:host-and-deploy/proxy-load-balancer) a aussi une option <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts>.</span><span class="sxs-lookup"><span data-stu-id="aa84c-762">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="aa84c-763">Le middleware des en-têtes transférés et le middleware de filtrage d’hôtes ont des fonctionnalités similaires pour différents scénarios.</span><span class="sxs-lookup"><span data-stu-id="aa84c-763">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="aa84c-764">La définition d’`AllowedHosts` avec le middleware des en-têtes transférés est appropriée quand l’en-tête `Host` n’est pas conservé durant le transfert des requêtes avec un serveur proxy inverse ou un équilibreur de charge.</span><span class="sxs-lookup"><span data-stu-id="aa84c-764">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="aa84c-765">La définition d’`AllowedHosts` avec le middleware de filtrage d’hôtes est appropriée quand Kestrel est utilisé comme serveur de périphérie public ou que l’en-tête `Host` est directement transféré.</span><span class="sxs-lookup"><span data-stu-id="aa84c-765">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="aa84c-766">Pour plus d’informations sur le middleware des en-têtes transférés, consultez <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="aa84c-766">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="aa84c-767">Kestrel est un [serveur Web multiplateforme pour ASP.net Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="aa84c-767">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="aa84c-768">Kestrel est le serveur web inclus par défaut dans les modèles de projets ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="aa84c-768">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="aa84c-769">Kestrel prend en charge les scénarios suivants :</span><span class="sxs-lookup"><span data-stu-id="aa84c-769">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="aa84c-770">HTTPS</span><span class="sxs-lookup"><span data-stu-id="aa84c-770">HTTPS</span></span>
* <span data-ttu-id="aa84c-771">Mise à niveau opaque utilisée pour activer les [WebSockets](https://github.com/aspnet/websockets)</span><span class="sxs-lookup"><span data-stu-id="aa84c-771">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="aa84c-772">Sockets UNIX pour des performances élevées derrière Nginx</span><span class="sxs-lookup"><span data-stu-id="aa84c-772">Unix sockets for high performance behind Nginx</span></span>

<span data-ttu-id="aa84c-773">Kestrel est pris en charge sur toutes les plateformes et les versions prises en charge par .NET Core.</span><span class="sxs-lookup"><span data-stu-id="aa84c-773">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="aa84c-774">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="aa84c-774">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="aa84c-775">Quand utiliser Kestrel avec un proxy inverse ?</span><span class="sxs-lookup"><span data-stu-id="aa84c-775">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="aa84c-776">Vous pouvez utiliser Kestrel par lui-même ou avec un *serveur proxy inverse*, comme [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org) ou [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="aa84c-776">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="aa84c-777">Un serveur proxy inverse reçoit les requêtes HTTP en provenance du réseau et les transmet à Kestrel.</span><span class="sxs-lookup"><span data-stu-id="aa84c-777">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="aa84c-778">Kestrel utilisé comme serveur web edge (accessible sur Internet) :</span><span class="sxs-lookup"><span data-stu-id="aa84c-778">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel communique directement avec Internet sans serveur proxy inverse](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="aa84c-780">Kestrel utilisé dans une configuration de proxy inverse :</span><span class="sxs-lookup"><span data-stu-id="aa84c-780">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel communique indirectement avec Internet via un serveur proxy inverse, par exemple IIS, Nginx ou Apache](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="aa84c-782">L’une ou l’autre des configurations, avec ou sans serveur proxy inverse, est une configuration d’hébergement prise en charge.</span><span class="sxs-lookup"><span data-stu-id="aa84c-782">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="aa84c-783">Kestrel, s’il est utilisé comme serveur de périphérie sans serveur proxy inverse, ne prend pas en charge le partage de la même adresse IP et du même port entre plusieurs processus.</span><span class="sxs-lookup"><span data-stu-id="aa84c-783">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="aa84c-784">Quand Kestrel est configuré pour écouter sur un port, il gère tout le trafic pour ce port, quel que soit les en-têtes `Host` des requêtes.</span><span class="sxs-lookup"><span data-stu-id="aa84c-784">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="aa84c-785">Un proxy inverse qui peut partager des ports a la possibilité de transférer des requêtes à Kestrel sur une adresse IP et un port uniques.</span><span class="sxs-lookup"><span data-stu-id="aa84c-785">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="aa84c-786">Même si un serveur proxy inverse n’est pas nécessaire, en utiliser un peut être un bon choix.</span><span class="sxs-lookup"><span data-stu-id="aa84c-786">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="aa84c-787">Un proxy inverse :</span><span class="sxs-lookup"><span data-stu-id="aa84c-787">A reverse proxy:</span></span>

* <span data-ttu-id="aa84c-788">Peut limiter la surface publique exposée des applications qu’il héberge.</span><span class="sxs-lookup"><span data-stu-id="aa84c-788">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="aa84c-789">Fournit une couche supplémentaire de configuration et de défense.</span><span class="sxs-lookup"><span data-stu-id="aa84c-789">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="aa84c-790">Peut mieux s’intégrer à l’infrastructure existante.</span><span class="sxs-lookup"><span data-stu-id="aa84c-790">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="aa84c-791">Simplifie la configuration de l’équilibrage de charge et d’une communication sécurisée (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="aa84c-791">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="aa84c-792">Seul le serveur proxy inverse requiert un certificat X. 509 et ce serveur peut communiquer avec les serveurs de l’application sur le réseau interne à l’aide du protocole HTTP simple.</span><span class="sxs-lookup"><span data-stu-id="aa84c-792">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="aa84c-793">L’hébergement dans une configuration de proxy inverse nécessite le [filtrage d’hôte](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="aa84c-793">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="aa84c-794">Comment utiliser Kestrel dans les applications ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="aa84c-794">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="aa84c-795">Le package [Microsoft. AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) est inclus dans le sous- [package Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="aa84c-795">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="aa84c-796">Les modèles de projet ASP.NET Core utilisent Kestrel par défaut.</span><span class="sxs-lookup"><span data-stu-id="aa84c-796">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="aa84c-797">Dans *Program.cs*, le modèle de code appelle <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, qui appelle <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> en arrière-plan.</span><span class="sxs-lookup"><span data-stu-id="aa84c-797">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

<span data-ttu-id="aa84c-798">Pour fournir une configuration supplémentaire après l’appel de `CreateDefaultBuilder`, appelez <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> :</span><span class="sxs-lookup"><span data-stu-id="aa84c-798">To provide additional configuration after calling `CreateDefaultBuilder`, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="aa84c-799">Pour plus d’informations sur `CreateDefaultBuilder` et la génération de l’hôte, consultez la section *configurer un hôte* de la rubrique <xref:fundamentals/host/web-host#set-up-a-host> .</span><span class="sxs-lookup"><span data-stu-id="aa84c-799">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

## <a name="kestrel-options"></a><span data-ttu-id="aa84c-800">Options Kestrel</span><span class="sxs-lookup"><span data-stu-id="aa84c-800">Kestrel options</span></span>

<span data-ttu-id="aa84c-801">Le serveur web Kestrel a des options de configuration de contrainte qui sont particulièrement utiles dans les déploiements exposés à Internet.</span><span class="sxs-lookup"><span data-stu-id="aa84c-801">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="aa84c-802">Définissez des contraintes sur la propriété <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> de la classe <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>.</span><span class="sxs-lookup"><span data-stu-id="aa84c-802">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="aa84c-803">La propriété `Limits` conserve une instance de la classe <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>.</span><span class="sxs-lookup"><span data-stu-id="aa84c-803">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="aa84c-804">Les exemples suivants utilisent l’espace de noms <xref:Microsoft.AspNetCore.Server.Kestrel.Core> :</span><span class="sxs-lookup"><span data-stu-id="aa84c-804">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="aa84c-805">Les options Kestrel, qui sont configurées dans le code C# dans les exemples suivants, peuvent également être définies à l’aide d’un [fournisseur de configuration](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="aa84c-805">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="aa84c-806">Par exemple, le fournisseur de configuration de fichier peut charger la configuration Kestrel à partir d’un *appsettings.jssur* ou *appSettings. { Fichier Environment}. JSON* :</span><span class="sxs-lookup"><span data-stu-id="aa84c-806">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

```json
{
  "Kestrel": {
    "Limits": {
      "MaxConcurrentConnections": 100,
      "MaxConcurrentUpgradedConnections": 100
    }
  }
}
```

<span data-ttu-id="aa84c-807">Utilisez l' **une** des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="aa84c-807">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="aa84c-808">Configurer Kestrel dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="aa84c-808">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="aa84c-809">Injecte une instance de `IConfiguration` dans la `Startup` classe.</span><span class="sxs-lookup"><span data-stu-id="aa84c-809">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="aa84c-810">L’exemple suivant suppose que la configuration injectée est assignée à la `Configuration` propriété.</span><span class="sxs-lookup"><span data-stu-id="aa84c-810">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="aa84c-811">Dans `Startup.ConfigureServices` , chargez la `Kestrel` section de configuration dans la configuration de Kestrel :</span><span class="sxs-lookup"><span data-stu-id="aa84c-811">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

     ```csharp
     using Microsoft.Extensions.Configuration
     
     public class Startup
     {
         public Startup(IConfiguration configuration)
         {
             Configuration = configuration;
         }

         public IConfiguration Configuration { get; }

         public void ConfigureServices(IServiceCollection services)
         {
             services.Configure<KestrelServerOptions>(
                 Configuration.GetSection("Kestrel"));
         }

         public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
         {
             ...
         }
     }
     ```

* <span data-ttu-id="aa84c-812">Configurez Kestrel lors de la génération de l’hôte :</span><span class="sxs-lookup"><span data-stu-id="aa84c-812">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="aa84c-813">Dans *Program.cs*, chargez la `Kestrel` section de configuration dans la configuration de Kestrel :</span><span class="sxs-lookup"><span data-stu-id="aa84c-813">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

  ```csharp
  // using Microsoft.Extensions.DependencyInjection;

  public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
      WebHost.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .UseStartup<Startup>();
  ```

<span data-ttu-id="aa84c-814">Les deux approches précédentes fonctionnent avec n’importe quel [fournisseur de configuration](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="aa84c-814">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="aa84c-815">Délai d’expiration toujours actif</span><span class="sxs-lookup"><span data-stu-id="aa84c-815">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="aa84c-816">Obtient ou définit le [délai d’expiration toujours actif](https://tools.ietf.org/html/rfc7230#section-6.5).</span><span class="sxs-lookup"><span data-stu-id="aa84c-816">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="aa84c-817">La valeur par défaut est de 2 minutes.</span><span class="sxs-lookup"><span data-stu-id="aa84c-817">Defaults to 2 minutes.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.KeepAliveTimeout = TimeSpan.FromMinutes(2);
        });
```

### <a name="maximum-client-connections"></a><span data-ttu-id="aa84c-818">Nombre maximale de connexions client</span><span class="sxs-lookup"><span data-stu-id="aa84c-818">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="aa84c-819">Le nombre maximal de connexions TCP ouvertes simultanées peut être défini pour l’application entière avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="aa84c-819">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentConnections = 100;
        });
```

<span data-ttu-id="aa84c-820">Il existe une limite distincte pour les connexions qui ont été mises à niveau à partir de HTTP ou HTTPS vers un autre protocole (par exemple, sur une demande WebSocket).</span><span class="sxs-lookup"><span data-stu-id="aa84c-820">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="aa84c-821">Une fois mise à niveau, une connexion n’est pas prise en compte dans la limite `MaxConcurrentConnections`.</span><span class="sxs-lookup"><span data-stu-id="aa84c-821">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentUpgradedConnections = 100;
        });
```

<span data-ttu-id="aa84c-822">Le nombre maximal de connexions est illimité (null) par défaut.</span><span class="sxs-lookup"><span data-stu-id="aa84c-822">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="aa84c-823">Taille maximale du corps de la requête</span><span class="sxs-lookup"><span data-stu-id="aa84c-823">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="aa84c-824">La taille maximale par défaut du corps de la requête est de 30 000 000 octets, soit environ 28,6 Mo.</span><span class="sxs-lookup"><span data-stu-id="aa84c-824">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="aa84c-825">Pour remplacer la limite dans une application ASP.NET Core MVC, nous vous recommandons d’utiliser l’attribut <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> sur une méthode d’action :</span><span class="sxs-lookup"><span data-stu-id="aa84c-825">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="aa84c-826">Voici un exemple qui montre comment configurer la contrainte pour l’application sur chaque requête :</span><span class="sxs-lookup"><span data-stu-id="aa84c-826">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxRequestBodySize = 10 * 1024;
        });
```

<span data-ttu-id="aa84c-827">Remplacer le paramètre sur une demande spécifique dans l’intergiciel (middleware) :</span><span class="sxs-lookup"><span data-stu-id="aa84c-827">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="aa84c-828">Une exception est levée si l’application configure la limite sur une demande après que l’application a commencé à lire la demande.</span><span class="sxs-lookup"><span data-stu-id="aa84c-828">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="aa84c-829">Il existe une propriété `IsReadOnly` qui indique si la propriété `MaxRequestBodySize` est en lecture seule ; si tel est le cas, il est trop tard pour configurer la limite.</span><span class="sxs-lookup"><span data-stu-id="aa84c-829">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="aa84c-830">Quand une application est exécutée [hors processus](xref:host-and-deploy/iis/index#out-of-process-hosting-model) derrière le [Module ASP.NET Core](xref:host-and-deploy/aspnet-core-module), la limite de taille du corps de demande de Kestrel est désactivée, car IIS définit déjà la limite.</span><span class="sxs-lookup"><span data-stu-id="aa84c-830">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="aa84c-831">Débit données minimal du corps de la requête</span><span class="sxs-lookup"><span data-stu-id="aa84c-831">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="aa84c-832">Kestrel vérifie à chaque seconde si les données arrivent au débit spécifié en octets/seconde.</span><span class="sxs-lookup"><span data-stu-id="aa84c-832">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="aa84c-833">Si le taux chute au-dessous de la valeur minimale, le délai d’attente de la connexion est dépassé. La période de grâce correspond à la durée pendant laquelle Kestrel permet au client d’augmenter son taux d’envoi jusqu’à la valeur minimale. la vitesse n’est pas vérifiée pendant cette période.</span><span class="sxs-lookup"><span data-stu-id="aa84c-833">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="aa84c-834">La période de grâce permet d’éviter la suppression des connexions qui, initialement, envoient des données à une vitesse lente en raison de la lenteur du démarrage de TCP.</span><span class="sxs-lookup"><span data-stu-id="aa84c-834">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="aa84c-835">Le débit minimal par défaut est 240 octets/seconde, avec une période de grâce de 5 secondes.</span><span class="sxs-lookup"><span data-stu-id="aa84c-835">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="aa84c-836">Un débit minimal s’applique également à la réponse.</span><span class="sxs-lookup"><span data-stu-id="aa84c-836">A minimum rate also applies to the response.</span></span> <span data-ttu-id="aa84c-837">Le code pour définir les limites de demande et de réponse est identique à l’exception de `RequestBody` ou `Response` dans les noms de propriété et d’interface.</span><span class="sxs-lookup"><span data-stu-id="aa84c-837">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="aa84c-838">Voici un exemple qui montre comment configurer les débits de données minimaux dans *Program.cs* :</span><span class="sxs-lookup"><span data-stu-id="aa84c-838">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MinRequestBodyDataRate =
                new MinDataRate(bytesPerSecond: 100, gracePeriod: TimeSpan.FromSeconds(10));
            serverOptions.Limits.MinResponseDataRate =
                new MinDataRate(bytesPerSecond: 100, gracePeriod: TimeSpan.FromSeconds(10));
        });
```

### <a name="request-headers-timeout"></a><span data-ttu-id="aa84c-839">Délai d’expiration des en-têtes de requête</span><span class="sxs-lookup"><span data-stu-id="aa84c-839">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="aa84c-840">Obtient ou définit le temps maximal passé par le serveur à recevoir des en-têtes de requête.</span><span class="sxs-lookup"><span data-stu-id="aa84c-840">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="aa84c-841">La valeur par défaut est de 30 secondes.</span><span class="sxs-lookup"><span data-stu-id="aa84c-841">Defaults to 30 seconds.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.RequestHeadersTimeout = TimeSpan.FromMinutes(1);
        });
```

### <a name="synchronous-io"></a><span data-ttu-id="aa84c-842">E/S synchrone</span><span class="sxs-lookup"><span data-stu-id="aa84c-842">Synchronous I/O</span></span>

<span data-ttu-id="aa84c-843"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> contrôle si des e/s synchrones sont autorisées pour la demande et la réponse.</span><span class="sxs-lookup"><span data-stu-id="aa84c-843"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="aa84c-844">La valeur par défaut est `true`.</span><span class="sxs-lookup"><span data-stu-id="aa84c-844">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="aa84c-845">Un grand nombre d’opérations d’e/s synchrones bloquantes peuvent entraîner une insuffisance du pool de threads, ce qui empêche l’application de répondre.</span><span class="sxs-lookup"><span data-stu-id="aa84c-845">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="aa84c-846">Activez uniquement `AllowSynchronousIO` lors de l’utilisation d’une bibliothèque qui ne prend pas en charge les e/s asynchrones.</span><span class="sxs-lookup"><span data-stu-id="aa84c-846">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="aa84c-847">L’exemple suivant désactive les e/s synchrones :</span><span class="sxs-lookup"><span data-stu-id="aa84c-847">The following example disables synchronous I/O:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.AllowSynchronousIO = false;
        });
```

<span data-ttu-id="aa84c-848">Pour plus d’informations sur les autres options et limites de Kestrel, consultez :</span><span class="sxs-lookup"><span data-stu-id="aa84c-848">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="aa84c-849">Configuration du point de terminaison</span><span class="sxs-lookup"><span data-stu-id="aa84c-849">Endpoint configuration</span></span>

<span data-ttu-id="aa84c-850">Par défaut, ASP.NET Core se lie à :</span><span class="sxs-lookup"><span data-stu-id="aa84c-850">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="aa84c-851">`https://localhost:5001` (quand un certificat de développement local est présent)</span><span class="sxs-lookup"><span data-stu-id="aa84c-851">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="aa84c-852">Spécifiez les URL avec :</span><span class="sxs-lookup"><span data-stu-id="aa84c-852">Specify URLs using the:</span></span>

* <span data-ttu-id="aa84c-853">La variable d’environnement `ASPNETCORE_URLS`.</span><span class="sxs-lookup"><span data-stu-id="aa84c-853">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="aa84c-854">L’argument de ligne de commande `--urls`.</span><span class="sxs-lookup"><span data-stu-id="aa84c-854">`--urls` command-line argument.</span></span>
* <span data-ttu-id="aa84c-855">La clé de configuration d’hôte `urls`.</span><span class="sxs-lookup"><span data-stu-id="aa84c-855">`urls` host configuration key.</span></span>
* <span data-ttu-id="aa84c-856">La méthode d’extension `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="aa84c-856">`UseUrls` extension method.</span></span>

<span data-ttu-id="aa84c-857">La valeur fournie avec ces approches peut être un ou plusieurs points de terminaison HTTP et HTTPS (HTTPS si un certificat par défaut est disponible).</span><span class="sxs-lookup"><span data-stu-id="aa84c-857">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="aa84c-858">Configurez la valeur sous forme de liste délimitée par des points-virgules (par exemple `"Urls": "http://localhost:8000;http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="aa84c-858">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="aa84c-859">Pour plus d’informations sur ces approches, voir [URL de serveur](xref:fundamentals/host/web-host#server-urls) et [Remplacer la configuration](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="aa84c-859">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="aa84c-860">Un certificat de développement est créé :</span><span class="sxs-lookup"><span data-stu-id="aa84c-860">A development certificate is created:</span></span>

* <span data-ttu-id="aa84c-861">Quand le [SDK .NET Core](/dotnet/core/sdk) est installé.</span><span class="sxs-lookup"><span data-stu-id="aa84c-861">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="aa84c-862">[L’outil dev-certs](xref:aspnetcore-2.1#https) est utilisé pour créer un certificat.</span><span class="sxs-lookup"><span data-stu-id="aa84c-862">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="aa84c-863">Certains navigateurs requièrent l’octroi d’une autorisation explicite pour approuver le certificat de développement local.</span><span class="sxs-lookup"><span data-stu-id="aa84c-863">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="aa84c-864">Les modèles de projet configurent les applications à exécuter sur HTTPs par défaut et incluent [la redirection https et la prise en charge de HSTS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="aa84c-864">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="aa84c-865">Appelez les méthodes <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> ou <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> sur <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> pour configurer les préfixes et les ports d’URL pour Kestrel.</span><span class="sxs-lookup"><span data-stu-id="aa84c-865">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="aa84c-866">`UseUrls`, l’argument de ligne de commande `--urls`, la clé de configuration d’hôte `urls` et la variable d’environnement `ASPNETCORE_URLS` fonctionnent également, mais ils présentent les limitations indiquées plus loin dans cette section (un certificat par défaut doit être disponible pour la configuration du point de terminaison HTTPS).</span><span class="sxs-lookup"><span data-stu-id="aa84c-866">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="aa84c-867">`KestrelServerOptions` configuré</span><span class="sxs-lookup"><span data-stu-id="aa84c-867">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="aa84c-868">ConfigureEndpointDefaults (action \<ListenOptions> )</span><span class="sxs-lookup"><span data-stu-id="aa84c-868">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="aa84c-869">Spécifie une `Action` de configuration à exécuter pour chaque point de terminaison spécifié.</span><span class="sxs-lookup"><span data-stu-id="aa84c-869">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="aa84c-870">Le fait d’appeler `ConfigureEndpointDefaults` plusieurs fois remplace les `Action`s précédentes par la dernière `Action` spécifiée.</span><span class="sxs-lookup"><span data-stu-id="aa84c-870">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
        });
```

> [!NOTE]
> <span data-ttu-id="aa84c-871"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> Les valeurs par défaut sont appliquées aux points de terminaison créés en appelant avant d’appeler.</span><span class="sxs-lookup"><span data-stu-id="aa84c-871">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="aa84c-872">ConfigureHttpsDefaults (action \<HttpsConnectionAdapterOptions> )</span><span class="sxs-lookup"><span data-stu-id="aa84c-872">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="aa84c-873">Spécifie une `Action` de configuration à exécuter pour chaque point de terminaison HTTPS.</span><span class="sxs-lookup"><span data-stu-id="aa84c-873">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="aa84c-874">Le fait d’appeler `ConfigureHttpsDefaults` plusieurs fois remplace les `Action`s précédentes par la dernière `Action` spécifiée.</span><span class="sxs-lookup"><span data-stu-id="aa84c-874">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                // certificate is an X509Certificate2
                listenOptions.ServerCertificate = certificate;
            });
        });
```

> [!NOTE]
> <span data-ttu-id="aa84c-875"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> Les valeurs par défaut sont appliquées aux points de terminaison créés en appelant avant d’appeler.</span><span class="sxs-lookup"><span data-stu-id="aa84c-875">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="aa84c-876">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="aa84c-876">Configure(IConfiguration)</span></span>

<span data-ttu-id="aa84c-877">Crée un chargeur de configuration pour configurer Kestrel, qui prend en entrée une <xref:Microsoft.Extensions.Configuration.IConfiguration>.</span><span class="sxs-lookup"><span data-stu-id="aa84c-877">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="aa84c-878">La configuration doit être limitée à la section de configuration pour Kestrel.</span><span class="sxs-lookup"><span data-stu-id="aa84c-878">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="aa84c-879">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="aa84c-879">ListenOptions.UseHttps</span></span>

<span data-ttu-id="aa84c-880">Configure Kestrel pour l’utilisation de HTTPS.</span><span class="sxs-lookup"><span data-stu-id="aa84c-880">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="aa84c-881">Extensions de `ListenOptions.UseHttps` :</span><span class="sxs-lookup"><span data-stu-id="aa84c-881">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="aa84c-882">`UseHttps`: Configurez Kestrel pour utiliser le protocole HTTPs avec le certificat par défaut.</span><span class="sxs-lookup"><span data-stu-id="aa84c-882">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="aa84c-883">Lève une exception si aucun certificat par défaut n’est configuré.</span><span class="sxs-lookup"><span data-stu-id="aa84c-883">Throws an exception if no default certificate is configured.</span></span>
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

<span data-ttu-id="aa84c-884">Paramètres de `ListenOptions.UseHttps` :</span><span class="sxs-lookup"><span data-stu-id="aa84c-884">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="aa84c-885">`filename` est le chemin et le nom d’un fichier de certificat, relatif au répertoire qui contient les fichiers de contenu de l’application.</span><span class="sxs-lookup"><span data-stu-id="aa84c-885">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="aa84c-886">`password` est le mot de passe nécessaire pour accéder aux données du certificat X.509.</span><span class="sxs-lookup"><span data-stu-id="aa84c-886">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="aa84c-887">`configureOptions` est une `Action` pour configurer les `HttpsConnectionAdapterOptions`.</span><span class="sxs-lookup"><span data-stu-id="aa84c-887">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="aa84c-888">Retourne l'`ListenOptions`.</span><span class="sxs-lookup"><span data-stu-id="aa84c-888">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="aa84c-889">`storeName` est le magasin de certificats à partir duquel charger le certificat.</span><span class="sxs-lookup"><span data-stu-id="aa84c-889">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="aa84c-890">`subject` est le nom du sujet du certificat.</span><span class="sxs-lookup"><span data-stu-id="aa84c-890">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="aa84c-891">`allowInvalid` indique si les certificats non valides doivent être considérés, comme les certificats auto-signés.</span><span class="sxs-lookup"><span data-stu-id="aa84c-891">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="aa84c-892">`location` est l’emplacement du magasin à partir duquel charger le certificat.</span><span class="sxs-lookup"><span data-stu-id="aa84c-892">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="aa84c-893">`serverCertificate` est le certificat X.509.</span><span class="sxs-lookup"><span data-stu-id="aa84c-893">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="aa84c-894">En production, HTTPS doit être explicitement configuré.</span><span class="sxs-lookup"><span data-stu-id="aa84c-894">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="aa84c-895">Au minimum, un certificat par défaut doit être fourni.</span><span class="sxs-lookup"><span data-stu-id="aa84c-895">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="aa84c-896">Configurations prises en charge décrites dans la suite :</span><span class="sxs-lookup"><span data-stu-id="aa84c-896">Supported configurations described next:</span></span>

* <span data-ttu-id="aa84c-897">Pas de configuration</span><span class="sxs-lookup"><span data-stu-id="aa84c-897">No configuration</span></span>
* <span data-ttu-id="aa84c-898">Remplacer le certificat par défaut dans la configuration</span><span class="sxs-lookup"><span data-stu-id="aa84c-898">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="aa84c-899">Changer les valeurs par défaut dans le code</span><span class="sxs-lookup"><span data-stu-id="aa84c-899">Change the defaults in code</span></span>

<span data-ttu-id="aa84c-900">*Pas de configuration*</span><span class="sxs-lookup"><span data-stu-id="aa84c-900">*No configuration*</span></span>

<span data-ttu-id="aa84c-901">Kestrel écoute sur `http://localhost:5000` et sur `https://localhost:5001` (si un certificat par défaut est disponible).</span><span class="sxs-lookup"><span data-stu-id="aa84c-901">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="aa84c-902">*Remplacer le certificat par défaut dans la configuration*</span><span class="sxs-lookup"><span data-stu-id="aa84c-902">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="aa84c-903">Par défaut, `CreateDefaultBuilder` appelle `Configure(context.Configuration.GetSection("Kestrel"))` pour charger la configuration de Kestrel.</span><span class="sxs-lookup"><span data-stu-id="aa84c-903">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="aa84c-904">Un schéma de configuration des paramètres d’application HTTPS par défaut est disponible pour Kestrel.</span><span class="sxs-lookup"><span data-stu-id="aa84c-904">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="aa84c-905">Configurez plusieurs points de terminaison, notamment les URL et les certificats à utiliser, à partir d’un fichier sur disque ou d’un magasin de certificats.</span><span class="sxs-lookup"><span data-stu-id="aa84c-905">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="aa84c-906">Dans l’exemple de fichier *appsettings.json* suivant :</span><span class="sxs-lookup"><span data-stu-id="aa84c-906">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="aa84c-907">Définissez **AllowInvalid** sur `true` pour permettre l’utilisation de certificats non valides (par exemple des certificats auto-signés).</span><span class="sxs-lookup"><span data-stu-id="aa84c-907">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="aa84c-908">Tout point de terminaison HTTPS qui ne spécifie pas de certificat (**HttpsDefaultCert** dans l’exemple qui suit) revient au certificat défini sous **Certificats** > \*\*Par défaut \*\* ou au certificat de développement.</span><span class="sxs-lookup"><span data-stu-id="aa84c-908">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },

      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },

      "HttpsInlineCertStore": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },

      "HttpsDefaultCert": {
        "Url": "https://localhost:5003"
      },

      "Https": {
        "Url": "https://*:5004",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

<span data-ttu-id="aa84c-909">Une alternative à l’utilisation de **Chemin** et de **Mot de passe** pour un nœud de certificat consiste à spécifier le certificat avec des champs du magasin de certificats.</span><span class="sxs-lookup"><span data-stu-id="aa84c-909">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="aa84c-910">Par exemple, le **Certificates**  >  certificat**par défaut** des certificats peut être spécifié comme suit :</span><span class="sxs-lookup"><span data-stu-id="aa84c-910">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="aa84c-911">Notes de schéma :</span><span class="sxs-lookup"><span data-stu-id="aa84c-911">Schema notes:</span></span>

* <span data-ttu-id="aa84c-912">Les noms des points de terminaison ne respectent pas la casse.</span><span class="sxs-lookup"><span data-stu-id="aa84c-912">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="aa84c-913">Par exemple, `HTTPS` et `Https` sont valides.</span><span class="sxs-lookup"><span data-stu-id="aa84c-913">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="aa84c-914">Le paramètre `Url` est obligatoire pour chaque point de terminaison.</span><span class="sxs-lookup"><span data-stu-id="aa84c-914">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="aa84c-915">Le format de ce paramètre est le même que celui du paramètre de configuration `Urls` du plus haut niveau, sauf qu’il est limité à une seule valeur.</span><span class="sxs-lookup"><span data-stu-id="aa84c-915">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="aa84c-916">Ces points de terminaison remplacent ceux qui sont définis dans le paramètre de configuration `Urls` du plus haut niveau configuration, au lieu de s’y ajouter.</span><span class="sxs-lookup"><span data-stu-id="aa84c-916">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="aa84c-917">Les points de terminaison définis dans le code via `Listen` sont cumulatifs avec les points de terminaison définis dans la section de configuration.</span><span class="sxs-lookup"><span data-stu-id="aa84c-917">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="aa84c-918">La section `Certificate` est facultative.</span><span class="sxs-lookup"><span data-stu-id="aa84c-918">The `Certificate` section is optional.</span></span> <span data-ttu-id="aa84c-919">Si la section `Certificate` n’est pas spécifiée, les valeurs par défaut définies dans les scénarios précédents sont utilisées.</span><span class="sxs-lookup"><span data-stu-id="aa84c-919">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="aa84c-920">Si aucune valeur par défaut n’est disponible, le serveur lève une exception et son démarrage échoue.</span><span class="sxs-lookup"><span data-stu-id="aa84c-920">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="aa84c-921">La `Certificate` section prend en **Path**charge les &ndash; certificats**de mot de passe** de chemin d’accès et de magasin d' **objets** &ndash; **Store** .</span><span class="sxs-lookup"><span data-stu-id="aa84c-921">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="aa84c-922">Vous pouvez définir un nombre quelconque de points de terminaison de cette façon, pour autant qu’ils ne provoquent pas de conflits de port.</span><span class="sxs-lookup"><span data-stu-id="aa84c-922">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="aa84c-923">`options.Configure(context.Configuration.GetSection("{SECTION}"))` retourne un `KestrelConfigurationLoader` avec une méthode `.Endpoint(string name, listenOptions => { })` qui peut être utilisée pour compléter les paramètres d’un point de terminaison configuré :</span><span class="sxs-lookup"><span data-stu-id="aa84c-923">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
                .Endpoint("HTTPS", listenOptions =>
                {
                    listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
                });
        });
```

<span data-ttu-id="aa84c-924">`KestrelServerOptions.ConfigurationLoader` peut être directement accessible pour poursuivre l’itération sur le chargeur existant, tel que celui fourni par <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> .</span><span class="sxs-lookup"><span data-stu-id="aa84c-924">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="aa84c-925">La section de configuration pour chaque point de terminaison est disponible sur les options de la `Endpoint` méthode afin que les paramètres personnalisés puissent être lus.</span><span class="sxs-lookup"><span data-stu-id="aa84c-925">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="aa84c-926">Plusieurs configurations peuvent être chargées en rappelant `options.Configure(context.Configuration.GetSection("{SECTION}"))` avec une autre section.</span><span class="sxs-lookup"><span data-stu-id="aa84c-926">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="aa84c-927">Seule la dernière configuration est utilisée, à moins que `Load` soit explicitement appelé sur les instances précédentes.</span><span class="sxs-lookup"><span data-stu-id="aa84c-927">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="aa84c-928">Le métapackage n’appelle pas `Load` : sa section de configuration par défaut peut donc être remplacée.</span><span class="sxs-lookup"><span data-stu-id="aa84c-928">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="aa84c-929">`KestrelConfigurationLoader` reflète la famille d’API `Listen` de `KestrelServerOptions` sous forme de surcharges de `Endpoint` : le code et les points de terminaison de configuration peuvent donc être configurés au même emplacement.</span><span class="sxs-lookup"><span data-stu-id="aa84c-929">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="aa84c-930">Ces surcharges n’utilisent pas de noms et consomment seulement les paramètres par défaut de la configuration.</span><span class="sxs-lookup"><span data-stu-id="aa84c-930">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="aa84c-931">*Changer les valeurs par défaut dans le code*</span><span class="sxs-lookup"><span data-stu-id="aa84c-931">*Change the defaults in code*</span></span>

<span data-ttu-id="aa84c-932">`ConfigureEndpointDefaults` et `ConfigureHttpsDefaults` peuvent être utilisés pour modifier les paramètres par défaut pour `ListenOptions` et `HttpsConnectionAdapterOptions`, notamment en remplaçant le certificat par défaut spécifié dans le scénario précédent.</span><span class="sxs-lookup"><span data-stu-id="aa84c-932">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="aa84c-933">`ConfigureEndpointDefaults` et `ConfigureHttpsDefaults` doivent être appelés avant que des points de terminaison soient configurés.</span><span class="sxs-lookup"><span data-stu-id="aa84c-933">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
            
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                listenOptions.SslProtocols = SslProtocols.Tls12;
            });
        });
```

<span data-ttu-id="aa84c-934">*Prise en charge de SNI par Kestrel*</span><span class="sxs-lookup"><span data-stu-id="aa84c-934">*Kestrel support for SNI*</span></span>

<span data-ttu-id="aa84c-935">[L’indication du nom de serveur (SNI, Server Name Indication)](https://tools.ietf.org/html/rfc6066#section-3) peut être utilisée pour héberger plusieurs domaines sur la même adresse IP et le même port.</span><span class="sxs-lookup"><span data-stu-id="aa84c-935">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="aa84c-936">Pour que SNI fonctionne, le client envoie le nom d’hôte pour la session sécurisée au serveur pendant la négociation TLS afin que le serveur puisse fournir le certificat correct.</span><span class="sxs-lookup"><span data-stu-id="aa84c-936">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="aa84c-937">Le client utilise le certificat fourni pour la communication chiffrée avec le serveur pendant la session sécurisée qui suit la négociation TLS.</span><span class="sxs-lookup"><span data-stu-id="aa84c-937">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="aa84c-938">Kestrel prend en charge SNI via le rappel de `ServerCertificateSelector`.</span><span class="sxs-lookup"><span data-stu-id="aa84c-938">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="aa84c-939">Le rappel est appelé une fois par connexion pour permettre à l’application d’inspecter le nom d’hôte et de sélectionner le certificat approprié.</span><span class="sxs-lookup"><span data-stu-id="aa84c-939">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="aa84c-940">La prise en charge de SNI nécessite les points suivants :</span><span class="sxs-lookup"><span data-stu-id="aa84c-940">SNI support requires:</span></span>

* <span data-ttu-id="aa84c-941">Exécution sur la version cible de .NET Framework `netcoreapp2.1` ou version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="aa84c-941">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="aa84c-942">Sur `net461` ou version ultérieure, le rappel est appelé, mais `name` est toujours `null` .</span><span class="sxs-lookup"><span data-stu-id="aa84c-942">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="aa84c-943">`name` est également `null` si le client ne fournit pas le paramètre du nom d’hôte dans la négociation TLS.</span><span class="sxs-lookup"><span data-stu-id="aa84c-943">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="aa84c-944">Tous les sites web s’exécutent sur la même instance Kestrel.</span><span class="sxs-lookup"><span data-stu-id="aa84c-944">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="aa84c-945">Kestrel ne prend pas en charge le partage d’une adresse IP et d’un port entre plusieurs instances sans un proxy inverse.</span><span class="sxs-lookup"><span data-stu-id="aa84c-945">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.ListenAnyIP(5005, listenOptions =>
            {
                listenOptions.UseHttps(httpsOptions =>
                {
                    var localhostCert = CertificateLoader.LoadFromStoreCert(
                        "localhost", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var exampleCert = CertificateLoader.LoadFromStoreCert(
                        "example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var subExampleCert = CertificateLoader.LoadFromStoreCert(
                        "sub.example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var certs = new Dictionary<string, X509Certificate2>(
                        StringComparer.OrdinalIgnoreCase);
                    certs["localhost"] = localhostCert;
                    certs["example.com"] = exampleCert;
                    certs["sub.example.com"] = subExampleCert;

                    httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
                    {
                        if (name != null && certs.TryGetValue(name, out var cert))
                        {
                            return cert;
                        }

                        return exampleCert;
                    };
                });
            });
        })
        .Build();
```

### <a name="connection-logging"></a><span data-ttu-id="aa84c-946">Journalisation des connexions</span><span class="sxs-lookup"><span data-stu-id="aa84c-946">Connection logging</span></span>

<span data-ttu-id="aa84c-947">Appelez <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> pour émettre des journaux au niveau du débogage pour la communication au niveau de l’octet sur une connexion.</span><span class="sxs-lookup"><span data-stu-id="aa84c-947">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="aa84c-948">La journalisation des connexions est utile pour résoudre les problèmes de communication de bas niveau, par exemple lors du chiffrement TLS et derrière les proxies.</span><span class="sxs-lookup"><span data-stu-id="aa84c-948">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="aa84c-949">Si `UseConnectionLogging` est placé avant `UseHttps` , le trafic chiffré est journalisé.</span><span class="sxs-lookup"><span data-stu-id="aa84c-949">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="aa84c-950">Si `UseConnectionLogging` est placé après `UseHttps` , le trafic déchiffré est journalisé.</span><span class="sxs-lookup"><span data-stu-id="aa84c-950">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="aa84c-951">Lier à un socket TCP</span><span class="sxs-lookup"><span data-stu-id="aa84c-951">Bind to a TCP socket</span></span>

<span data-ttu-id="aa84c-952">La méthode <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> se lie à un socket TCP, et une expression lambda options permet la configuration d’un certificat X.509 :</span><span class="sxs-lookup"><span data-stu-id="aa84c-952">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Listen(IPAddress.Loopback, 5000);
            serverOptions.Listen(IPAddress.Loopback, 5001, listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testPassword");
            });
        });
```

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Listen(IPAddress.Loopback, 5000);
            serverOptions.Listen(IPAddress.Loopback, 5001, listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testPassword");
            });
        });
```

<span data-ttu-id="aa84c-953">L’exemple configure HTTPS pour un point de terminaison avec <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span><span class="sxs-lookup"><span data-stu-id="aa84c-953">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="aa84c-954">Utilisez la même API afin de configurer d’autres paramètres Kestrel pour des points de terminaison spécifiques.</span><span class="sxs-lookup"><span data-stu-id="aa84c-954">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="aa84c-955">Lier à un socket Unix</span><span class="sxs-lookup"><span data-stu-id="aa84c-955">Bind to a Unix socket</span></span>

<span data-ttu-id="aa84c-956">Écoutez sur un socket Unix avec <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> pour améliorer les performances avec Nginx, comme illustré dans cet exemple :</span><span class="sxs-lookup"><span data-stu-id="aa84c-956">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.ListenUnixSocket("/tmp/kestrel-test.sock");
            serverOptions.ListenUnixSocket("/tmp/kestrel-test.sock", listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testpassword");
            });
        });
```

* <span data-ttu-id="aa84c-957">Dans le fichier Nginx confiuguration, affectez `server`  >  `location`  >  `proxy_pass` à l’entrée `http://unix:/tmp/{KESTREL SOCKET}:/;` .</span><span class="sxs-lookup"><span data-stu-id="aa84c-957">In the Nginx confiuguration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="aa84c-958">`{KESTREL SOCKET}` nom du Socket fourni à <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (par exemple, `kestrel-test.sock` dans l’exemple précédent).</span><span class="sxs-lookup"><span data-stu-id="aa84c-958">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="aa84c-959">Assurez-vous que le socket est accessible en écriture par Nginx (par exemple, `chmod go+w /tmp/kestrel-test.sock` ).</span><span class="sxs-lookup"><span data-stu-id="aa84c-959">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span> 

### <a name="port-0"></a><span data-ttu-id="aa84c-960">Port 0</span><span class="sxs-lookup"><span data-stu-id="aa84c-960">Port 0</span></span>

<span data-ttu-id="aa84c-961">Si vous spécifiez le numéro de port `0`, Kestrel se lie dynamiquement à un port disponible.</span><span class="sxs-lookup"><span data-stu-id="aa84c-961">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="aa84c-962">L’exemple suivant montre comment déterminer le port auquel Kestrel se lie à l’exécution :</span><span class="sxs-lookup"><span data-stu-id="aa84c-962">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="aa84c-963">Quand l’application est exécutée, la sortie de la fenêtre de console indique le port dynamique où l’application peut être atteinte :</span><span class="sxs-lookup"><span data-stu-id="aa84c-963">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="aa84c-964">Limites</span><span class="sxs-lookup"><span data-stu-id="aa84c-964">Limitations</span></span>

<span data-ttu-id="aa84c-965">Configurez des points de terminaison avec les approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="aa84c-965">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="aa84c-966">Arguments de ligne de commande `--urls`</span><span class="sxs-lookup"><span data-stu-id="aa84c-966">`--urls` command-line argument</span></span>
* <span data-ttu-id="aa84c-967">Clé de configuration d’hôte `urls`.</span><span class="sxs-lookup"><span data-stu-id="aa84c-967">`urls` host configuration key</span></span>
* <span data-ttu-id="aa84c-968">`ASPNETCORE_URLS` variable d’environnement</span><span class="sxs-lookup"><span data-stu-id="aa84c-968">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="aa84c-969">Ces méthodes sont utiles si vous voulez que votre code fonctionne avec des serveurs autres que Kestrel.</span><span class="sxs-lookup"><span data-stu-id="aa84c-969">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="aa84c-970">Toutefois, soyez conscient des limitations suivantes :</span><span class="sxs-lookup"><span data-stu-id="aa84c-970">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="aa84c-971">HTTPS ne peut pas être utilisé avec ces approches, sauf si un certificat par défaut est fourni dans la configuration du point de terminaison HTTPS (par exemple avec la configuration de `KestrelServerOptions` ou un fichier de configuration, comme illustré plus haut dans cette rubrique).</span><span class="sxs-lookup"><span data-stu-id="aa84c-971">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="aa84c-972">Quand les deux approches `Listen` et `UseUrls` sont utilisées simultanément, les points de terminaison `Listen` remplacent les points de terminaison `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="aa84c-972">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="aa84c-973">Configuration de point de terminaison IIS</span><span class="sxs-lookup"><span data-stu-id="aa84c-973">IIS endpoint configuration</span></span>

<span data-ttu-id="aa84c-974">Quand vous utilisez IIS, les liaisons d’URL pour IIS remplacent les liaisons qui sont définies par `Listen` ou par `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="aa84c-974">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="aa84c-975">Pour plus d’informations, consultez la rubrique [Module ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="aa84c-975">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="aa84c-976">Configuration du transport</span><span class="sxs-lookup"><span data-stu-id="aa84c-976">Transport configuration</span></span>

<span data-ttu-id="aa84c-977">Dans ASP.NET Core 2.1, le transport par défaut de Kestrel n’est plus basé sur Libuv, mais sur des sockets managés.</span><span class="sxs-lookup"><span data-stu-id="aa84c-977">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="aa84c-978">Il s’agit d’un changement cassant pour les applications ASP.NET Core 2.0 mises à niveau vers la version 2.1 qui appellent <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> et qui dépendent d’un des packages suivants :</span><span class="sxs-lookup"><span data-stu-id="aa84c-978">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="aa84c-979">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (référence de package directe)</span><span class="sxs-lookup"><span data-stu-id="aa84c-979">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="aa84c-980">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="aa84c-980">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="aa84c-981">Pour les projets qui requièrent l’utilisation de Libuv :</span><span class="sxs-lookup"><span data-stu-id="aa84c-981">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="aa84c-982">Ajoutez une dépendance pour le package [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) au fichier projet de l’application :</span><span class="sxs-lookup"><span data-stu-id="aa84c-982">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="aa84c-983">Appelez <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> :</span><span class="sxs-lookup"><span data-stu-id="aa84c-983">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

  ```csharp
  public class Program
  {
      public static void Main(string[] args)
      {
          CreateWebHostBuilder(args).Build().Run();
      }

      public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
          WebHost.CreateDefaultBuilder(args)
              .UseLibuv()
              .UseStartup<Startup>();
  }
  ```

### <a name="url-prefixes"></a><span data-ttu-id="aa84c-984">Préfixes d’URL</span><span class="sxs-lookup"><span data-stu-id="aa84c-984">URL prefixes</span></span>

<span data-ttu-id="aa84c-985">Quand vous utilisez `UseUrls`, l’argument de ligne de commande `--urls`, la clé de configuration d’hôte `urls` ou la variable d’environnement `ASPNETCORE_URLS`, les préfixes d’URL peuvent être dans un des formats suivants.</span><span class="sxs-lookup"><span data-stu-id="aa84c-985">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="aa84c-986">Seuls les préfixes d’URL HTTP sont valides.</span><span class="sxs-lookup"><span data-stu-id="aa84c-986">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="aa84c-987">Kestrel ne prend pas en charge HTTPS lors de la configuration de liaisons d’URL avec `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="aa84c-987">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="aa84c-988">Adresse IPv4 avec numéro de port</span><span class="sxs-lookup"><span data-stu-id="aa84c-988">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="aa84c-989">`0.0.0.0` est un cas spécial qui se lie à toutes les adresses IPv4.</span><span class="sxs-lookup"><span data-stu-id="aa84c-989">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="aa84c-990">Adresse IPv6 avec numéro de port</span><span class="sxs-lookup"><span data-stu-id="aa84c-990">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="aa84c-991">`[::]` est l’équivalent IPv6 de `0.0.0.0` dans IPv4.</span><span class="sxs-lookup"><span data-stu-id="aa84c-991">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="aa84c-992">Nom d’hôte avec numéro de port</span><span class="sxs-lookup"><span data-stu-id="aa84c-992">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="aa84c-993">Les noms d’hôte, `*` et `+` ne sont pas spéciaux.</span><span class="sxs-lookup"><span data-stu-id="aa84c-993">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="aa84c-994">Tout ce qui n’est pas reconnu comme adresse IP valide ou `localhost` se lie à toutes les adresses IP IPv4 et IPv6.</span><span class="sxs-lookup"><span data-stu-id="aa84c-994">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="aa84c-995">Pour lier différents noms d’hôte à différentes applications ASP.NET Core sur le même port, utilisez [HTTP.sys](xref:fundamentals/servers/httpsys) ou un serveur proxy inverse, comme IIS, Nginx ou Apache.</span><span class="sxs-lookup"><span data-stu-id="aa84c-995">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="aa84c-996">L’hébergement dans une configuration de proxy inverse nécessite le [filtrage d’hôte](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="aa84c-996">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="aa84c-997">Nom `localhost` de l’hôte avec numéro de port ou adresse IP de bouclage avec numéro de port</span><span class="sxs-lookup"><span data-stu-id="aa84c-997">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="aa84c-998">Quand `localhost` est spécifié, Kestrel tente de se lier aux interfaces de bouclage IPv4 et IPv6.</span><span class="sxs-lookup"><span data-stu-id="aa84c-998">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="aa84c-999">Si le port demandé est en cours d’utilisation par un autre service sur l’une des interfaces de bouclage, Kestrel ne démarre pas.</span><span class="sxs-lookup"><span data-stu-id="aa84c-999">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="aa84c-1000">Si l’une des interfaces de bouclage n’est pas disponible pour une raison quelconque (généralement du fait de la non-prise en charge d’IPv6), Kestrel journalise un avertissement.</span><span class="sxs-lookup"><span data-stu-id="aa84c-1000">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="aa84c-1001">Filtrage d’hôtes</span><span class="sxs-lookup"><span data-stu-id="aa84c-1001">Host filtering</span></span>

<span data-ttu-id="aa84c-1002">Si Kestrel prend en charge la configuration basée sur les préfixes, comme `http://example.com:5000`, il ignore en grande partie le nom d’hôte.</span><span class="sxs-lookup"><span data-stu-id="aa84c-1002">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="aa84c-1003">L’hôte `localhost` est un cas spécial utilisé pour la liaison à des adresses de bouclage.</span><span class="sxs-lookup"><span data-stu-id="aa84c-1003">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="aa84c-1004">Tout hôte autre qu’une adresse IP explicite se lie à toutes les adresses IP publiques.</span><span class="sxs-lookup"><span data-stu-id="aa84c-1004">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="aa84c-1005">Les en-têtes `Host` ne sont pas validés.</span><span class="sxs-lookup"><span data-stu-id="aa84c-1005">`Host` headers aren't validated.</span></span>

<span data-ttu-id="aa84c-1006">En guise de solution de contournement, utilisez le middleware de filtrage d’hôtes.</span><span class="sxs-lookup"><span data-stu-id="aa84c-1006">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="aa84c-1007">L’intergiciel (middleware) de filtrage d’hôte est fourni par le package [Microsoft. AspNetCore. HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) , qui est inclus dans le de [Microsoft. AspNetCore. App](xref:fundamentals/metapackage-app) (ASP.net Core 2,1 ou 2,2).</span><span class="sxs-lookup"><span data-stu-id="aa84c-1007">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="aa84c-1008">L’intergiciel (middleware) est ajouté par <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, qui appelle <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> :</span><span class="sxs-lookup"><span data-stu-id="aa84c-1008">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="aa84c-1009">Le middleware de filtrage d’hôtes est désactivé par défaut.</span><span class="sxs-lookup"><span data-stu-id="aa84c-1009">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="aa84c-1010">Pour activer l’intergiciel (middleware), définissez une `AllowedHosts` clé dans *appsettings.jssur* / *appSettings. \<EnvironmentName> JSON*.</span><span class="sxs-lookup"><span data-stu-id="aa84c-1010">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="aa84c-1011">La valeur est une liste délimitée par des points-virgules des noms d’hôte sans numéros de port :</span><span class="sxs-lookup"><span data-stu-id="aa84c-1011">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="aa84c-1012">*appsettings.js*:</span><span class="sxs-lookup"><span data-stu-id="aa84c-1012">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="aa84c-1013">Le [middleware des en-têtes transférés](xref:host-and-deploy/proxy-load-balancer) a aussi une option <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts>.</span><span class="sxs-lookup"><span data-stu-id="aa84c-1013">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="aa84c-1014">Le middleware des en-têtes transférés et le middleware de filtrage d’hôtes ont des fonctionnalités similaires pour différents scénarios.</span><span class="sxs-lookup"><span data-stu-id="aa84c-1014">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="aa84c-1015">La définition d’`AllowedHosts` avec le middleware des en-têtes transférés est appropriée quand l’en-tête `Host` n’est pas conservé durant le transfert des requêtes avec un serveur proxy inverse ou un équilibreur de charge.</span><span class="sxs-lookup"><span data-stu-id="aa84c-1015">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="aa84c-1016">La définition d’`AllowedHosts` avec le middleware de filtrage d’hôtes est appropriée quand Kestrel est utilisé comme serveur de périphérie public ou que l’en-tête `Host` est directement transféré.</span><span class="sxs-lookup"><span data-stu-id="aa84c-1016">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="aa84c-1017">Pour plus d’informations sur le middleware des en-têtes transférés, consultez <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="aa84c-1017">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

## <a name="http11-request-draining"></a><span data-ttu-id="aa84c-1018">Demande de drainage HTTP/1.1</span><span class="sxs-lookup"><span data-stu-id="aa84c-1018">HTTP/1.1 request draining</span></span>

<span data-ttu-id="aa84c-1019">L’ouverture de connexions HTTP prend beaucoup de temps.</span><span class="sxs-lookup"><span data-stu-id="aa84c-1019">Opening HTTP connections is time consuming.</span></span> <span data-ttu-id="aa84c-1020">Pour le protocole HTTPs, il s’agit également d’une utilisation intensive des ressources.</span><span class="sxs-lookup"><span data-stu-id="aa84c-1020">For HTTPS, it's also resource intensive.</span></span> <span data-ttu-id="aa84c-1021">Par conséquent, Kestrel tente de réutiliser les connexions par protocole HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="aa84c-1021">Therefore, Kestrel tries to reuse connections per the HTTP/1.1 protocol.</span></span> <span data-ttu-id="aa84c-1022">Un corps de demande doit être entièrement consommé pour permettre la réutilisation de la connexion.</span><span class="sxs-lookup"><span data-stu-id="aa84c-1022">A request body must be fully consumed to allow the connection to be reused.</span></span> <span data-ttu-id="aa84c-1023">L’application ne consomme pas toujours le corps de la requête, par exemple les `POST` demandes où le serveur retourne une réponse de redirection ou 404.</span><span class="sxs-lookup"><span data-stu-id="aa84c-1023">The app doesn't always consume the request body, such as a `POST` requests where the server returns a redirect or 404 response.</span></span> <span data-ttu-id="aa84c-1024">Dans le `POST` cas de redirection :</span><span class="sxs-lookup"><span data-stu-id="aa84c-1024">In the `POST`-redirect case:</span></span>

* <span data-ttu-id="aa84c-1025">Le client a peut-être déjà envoyé une partie des `POST` données.</span><span class="sxs-lookup"><span data-stu-id="aa84c-1025">The client may already have sent part of the `POST` data.</span></span>
* <span data-ttu-id="aa84c-1026">Le serveur écrit la réponse 301.</span><span class="sxs-lookup"><span data-stu-id="aa84c-1026">The server writes the 301 response.</span></span>
* <span data-ttu-id="aa84c-1027">La connexion ne peut pas être utilisée pour une nouvelle demande tant que les `POST` données du corps de la requête précédente n’ont pas été entièrement lues.</span><span class="sxs-lookup"><span data-stu-id="aa84c-1027">The connection can't be used for a new request until the `POST` data from the previous request body has been fully read.</span></span>
* <span data-ttu-id="aa84c-1028">Kestrel tente de vider le corps de la requête.</span><span class="sxs-lookup"><span data-stu-id="aa84c-1028">Kestrel tries to drain the request body.</span></span> <span data-ttu-id="aa84c-1029">Le drainage du corps de la demande signifie lire et ignorer les données sans les traiter.</span><span class="sxs-lookup"><span data-stu-id="aa84c-1029">Draining the request body means reading and discarding the data without processing it.</span></span>

<span data-ttu-id="aa84c-1030">Le processus de drainage rend un tradoff entre l’autorisation de réutilisation de la connexion et le temps nécessaire pour décharger les données restantes :</span><span class="sxs-lookup"><span data-stu-id="aa84c-1030">The draining process makes a tradoff between allowing the connection to be reused and the time it takes to drain any remaining data:</span></span>

* <span data-ttu-id="aa84c-1031">La vidange a un délai d’expiration de cinq secondes, ce qui n’est pas configurable.</span><span class="sxs-lookup"><span data-stu-id="aa84c-1031">Draining has a timeout of five seconds, which isn't configurable.</span></span>
* <span data-ttu-id="aa84c-1032">Si toutes les données spécifiées par l' `Content-Length` `Transfer-Encoding` en-tête ou n’ont pas été lues avant le délai d’attente, la connexion est fermée.</span><span class="sxs-lookup"><span data-stu-id="aa84c-1032">If all of the data specified by the `Content-Length` or `Transfer-Encoding` header hasn't been read before the timeout, the connection is closed.</span></span>

<span data-ttu-id="aa84c-1033">Il peut arriver que vous souhaitiez mettre fin à la demande immédiatement, avant ou après l’écriture de la réponse.</span><span class="sxs-lookup"><span data-stu-id="aa84c-1033">Sometimes you may want to terminate the request immediately, before or after writing the response.</span></span> <span data-ttu-id="aa84c-1034">Par exemple, les clients peuvent avoir des limites de données restrictives, de sorte que la limitation des données chargées peut être une priorité.</span><span class="sxs-lookup"><span data-stu-id="aa84c-1034">For example, clients may have restrictive data caps, so limiting uploaded data might be a priority.</span></span> <span data-ttu-id="aa84c-1035">Dans ce cas, pour mettre fin à une demande, appelez [HttpContext. Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) à partir d’un contrôleur, d’une Razor page ou d’un intergiciel (middleware).</span><span class="sxs-lookup"><span data-stu-id="aa84c-1035">In such cases to terminate a request, call [HttpContext.Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) from a controller, Razor Page, or middleware.</span></span>

<span data-ttu-id="aa84c-1036">Il existe des inconvénients à l’appel de `Abort` :</span><span class="sxs-lookup"><span data-stu-id="aa84c-1036">There are caveats to calling `Abort`:</span></span>

* <span data-ttu-id="aa84c-1037">La création de nouvelles connexions peut être lente et coûteuse.</span><span class="sxs-lookup"><span data-stu-id="aa84c-1037">Creating new connections can be slow and expensive.</span></span>
* <span data-ttu-id="aa84c-1038">Il n’y a aucune garantie que le client a lu la réponse avant la fermeture de la connexion.</span><span class="sxs-lookup"><span data-stu-id="aa84c-1038">There's no guarantee that the client has read the response before the connection closes.</span></span>
* <span data-ttu-id="aa84c-1039">L’appel `Abort` de doit être rare et réservé aux cas d’erreur graves, et non aux erreurs courantes.</span><span class="sxs-lookup"><span data-stu-id="aa84c-1039">Calling `Abort` should be rare and reserved for severe error cases, not common errors.</span></span>
  * <span data-ttu-id="aa84c-1040">Appelez uniquement `Abort` lorsqu’un problème spécifique doit être résolu.</span><span class="sxs-lookup"><span data-stu-id="aa84c-1040">Only call `Abort` when a specific problem needs to be solved.</span></span> <span data-ttu-id="aa84c-1041">Par exemple, appelez `Abort` si des clients malveillants essaient des `POST` données ou s’il existe un bogue dans le code client qui génère des demandes volumineuses ou de nombreuses requêtes.</span><span class="sxs-lookup"><span data-stu-id="aa84c-1041">For example, call `Abort` if malicious clients are trying to `POST` data or when there's a bug in client code that causes large or numerous requests.</span></span>
  * <span data-ttu-id="aa84c-1042">N’appelez pas `Abort` pour les situations d’erreur courantes, telles que HTTP 404 (introuvable).</span><span class="sxs-lookup"><span data-stu-id="aa84c-1042">Don't call `Abort` for common error situations, such as HTTP 404 (Not Found).</span></span>

<span data-ttu-id="aa84c-1043">L’appel de [HttpResponse. CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) avant d’appeler `Abort` garantit que le serveur a terminé l’écriture de la réponse.</span><span class="sxs-lookup"><span data-stu-id="aa84c-1043">Calling [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) before calling `Abort` ensures that the server has completed writing the response.</span></span> <span data-ttu-id="aa84c-1044">Toutefois, le comportement du client n’est pas prévisible et il est possible qu’il ne lise pas la réponse avant l’abandon de la connexion.</span><span class="sxs-lookup"><span data-stu-id="aa84c-1044">However, client behavior isn't predictable and they may not read the response before the connection is aborted.</span></span>

<span data-ttu-id="aa84c-1045">Ce processus est différent pour HTTP/2, car le protocole prend en charge l’abandon des flux de demande individuels sans fermer la connexion.</span><span class="sxs-lookup"><span data-stu-id="aa84c-1045">This process is different for HTTP/2 because the protocol supports aborting individual request streams without closing the connection.</span></span> <span data-ttu-id="aa84c-1046">Le délai d’attente de drainage de cinq secondes ne s’applique pas.</span><span class="sxs-lookup"><span data-stu-id="aa84c-1046">The five second drain timeout doesn't apply.</span></span> <span data-ttu-id="aa84c-1047">S’il existe des données de corps de demande non lues après avoir complété une réponse, le serveur envoie une trame HTTP/2 RST.</span><span class="sxs-lookup"><span data-stu-id="aa84c-1047">If there's any unread request body data after completing a response, then the server sends an HTTP/2 RST frame.</span></span> <span data-ttu-id="aa84c-1048">Les trames de données de corps de requête supplémentaires sont ignorées.</span><span class="sxs-lookup"><span data-stu-id="aa84c-1048">Additional request body data frames are ignored.</span></span>

<span data-ttu-id="aa84c-1049">Si possible, il est préférable que les clients utilisent l’en-tête de demande [expect : 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) et attendent que le serveur réponde avant de commencer à envoyer le corps de la demande.</span><span class="sxs-lookup"><span data-stu-id="aa84c-1049">If possible, it's better for clients to utilize the [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) request header and wait for the server to respond before starting to send the request body.</span></span> <span data-ttu-id="aa84c-1050">Cela donne au client la possibilité d’examiner la réponse et de l’abandonner avant d’envoyer des données inutiles.</span><span class="sxs-lookup"><span data-stu-id="aa84c-1050">That gives the client an opportunity to examine the response and abort before sending unneeded data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="aa84c-1051">Ressources complémentaires</span><span class="sxs-lookup"><span data-stu-id="aa84c-1051">Additional resources</span></span>

* <span data-ttu-id="aa84c-1052">Lorsque vous utilisez des sockets UNIX sur Linux, le socket n’est pas supprimé automatiquement lors de l’arrêt de l’application.</span><span class="sxs-lookup"><span data-stu-id="aa84c-1052">When using UNIX sockets on Linux, the socket is not automatically deleted on app shut down.</span></span> <span data-ttu-id="aa84c-1053">Pour plus d’informations, consultez [ce problème GitHub](https://github.com/dotnet/aspnetcore/issues/14134).</span><span class="sxs-lookup"><span data-stu-id="aa84c-1053">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/14134).</span></span>
* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="aa84c-1054">Document RFC 7230 : syntaxe et routage des messages (section 5.4 : Hôte)</span><span class="sxs-lookup"><span data-stu-id="aa84c-1054">RFC 7230: Message Syntax and Routing (Section 5.4: Host)</span></span>](https://tools.ietf.org/html/rfc7230#section-5.4)
