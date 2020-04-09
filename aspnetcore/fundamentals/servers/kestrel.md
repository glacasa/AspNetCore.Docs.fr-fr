---
title: Implémentation du serveur web Kestrel dans ASP.NET Core
author: rick-anderson
description: Découvrez plus d’informations sur Kestrel, serveur web multiplateforme pour ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/10/2020
uid: fundamentals/servers/kestrel
ms.openlocfilehash: 18846d60fd5c29f17cb4e59192795fd92251e2d0
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80976766"
---
# <a name="kestrel-web-server-implementation-in-aspnet-core"></a>Implémentation du serveur web Kestrel dans ASP.NET Core

Par [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher) et [Stephen Halter](https://twitter.com/halter73)

::: moniker range=">= aspnetcore-3.0"

Kestrel est un [serveur web](xref:fundamentals/servers/index)multiplateforme pour ASP.NET Core . Kestrel est le serveur web inclus par défaut dans les modèles de projets ASP.NET Core.

Kestrel prend en charge les scénarios suivants :

* HTTPS
* Mise à niveau opaque utilisée pour activer les [WebSockets](https://github.com/aspnet/websockets)
* Sockets UNIX pour des performances élevées derrière Nginx
* HTTP/2 (sauf sur macOS&dagger;)

&dagger;HTTP/2 sera pris en charge sur macOS dans une prochaine version.

Kestrel est pris en charge sur toutes les plateformes et les versions prises en charge par .NET Core.

[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([comment télécharger](xref:index#how-to-download-a-sample))

## <a name="http2-support"></a>Assistance HTTP/2

[HTTP/2](https://httpwg.org/specs/rfc7540.html) est disponible pour les applications ASP.NET Core si les conditions de base suivantes sont remplies :

* Système d’exploitation&dagger;
  * Windows Server 2016/Windows 10 ou version ultérieure&Dagger;
  * Linux avec OpenSSL 1.0.2 ou version ultérieure (par exemple, Ubuntu 16.04 ou version ultérieure)
* Version cible de .Net Framework : .NET Core 2.2 ou version ultérieure
* Connexion [ALPN (Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3)
* TLS 1.2 ou connexion ultérieure

&dagger;HTTP/2 sera pris en charge sur macOS dans une prochaine version.
&Dagger;Kestrel propose une prise en charge limitée de HTTP/2 sous Windows Server 2012 R2 et Windows 8.1. La prise en charge est limitée car la liste des suites de chiffrement TLS prises en charge sur ces systèmes d’exploitation est limitée. Un certificat généré à l’aide d’Elliptic Curve Digital Signature algorithme (ECDSA) peut être requis pour sécuriser les connexions TLS.

Si une connexion HTTP/2 est établie, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) retourne `HTTP/2`.

HTTP/2 est désactivé par défaut. Pour plus d’informations sur la configuration, consultez les sections [Options Kestrel](#kestrel-options) et [ListenOptions.Protocols](#listenoptionsprotocols).

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a>Quand utiliser Kestrel avec un proxy inverse ?

Vous pouvez utiliser Kestrel par lui-même ou avec un *serveur proxy inverse*, comme [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org) ou [Apache](https://httpd.apache.org/). Un serveur proxy inverse reçoit les requêtes HTTP en provenance du réseau et les transmet à Kestrel.

Kestrel utilisé comme serveur web edge (accessible sur Internet) :

![Kestrel communique directement avec Internet sans serveur proxy inverse](kestrel/_static/kestrel-to-internet2.png)

Kestrel utilisé dans une configuration de proxy inverse :

![Kestrel communique indirectement avec Internet via un serveur proxy inverse, par exemple IIS, Nginx ou Apache](kestrel/_static/kestrel-to-internet.png)

L’une ou l’autre configuration, avec ou sans serveur proxy inversé, est une configuration d’hébergement prise en charge.

Kestrel, s’il est utilisé comme serveur de périphérie sans serveur proxy inverse, ne prend pas en charge le partage de la même adresse IP et du même port entre plusieurs processus. Quand Kestrel est configuré pour écouter sur un port, il gère tout le trafic pour ce port, quel que soit les en-têtes `Host` des requêtes. Un proxy inverse qui peut partager des ports a la possibilité de transférer des requêtes à Kestrel sur une adresse IP et un port uniques.

Même si un serveur proxy inverse n’est pas nécessaire, en utiliser un peut être un bon choix.

Un proxy inverse :

* Peut limiter la surface publique exposée des applications qu’il héberge.
* Fournit une couche supplémentaire de configuration et de défense.
* Peut mieux s’intégrer à l’infrastructure existante.
* Simplifie la configuration de l’équilibrage de charge et d’une communication sécurisée (HTTPS). Seul le serveur proxy inversé nécessite un certificat X.509, et ce serveur peut communiquer avec les serveurs de l’application sur le réseau interne à l’aide de HTTP simple.

> [!WARNING]
> L’hébergement dans une configuration de proxy inverse nécessite le [filtrage d’hôte](#host-filtering).

## <a name="kestrel-in-aspnet-core-apps"></a>Kestrel dans ASP.NET applications Core

Les modèles de projet ASP.NET Core utilisent Kestrel par défaut. En *Program.cs*, la <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> méthode <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>appelle :

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=8)]

Pour plus d’informations sur la construction de l’hôte, voir la *mise en place d’un hôte* et les *sections paramètres de constructeur par défaut* de <xref:fundamentals/host/generic-host#set-up-a-host>.

Pour fournir une configuration supplémentaire après l’appel de `ConfigureWebHostDefaults`, utilisez `ConfigureKestrel` :

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

## <a name="kestrel-options"></a>Options Kestrel

Le serveur web Kestrel a des options de configuration de contrainte qui sont particulièrement utiles dans les déploiements exposés à Internet.

Définissez des contraintes sur la propriété <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> de la classe <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>. La propriété `Limits` conserve une instance de la classe <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>.

Les exemples suivants utilisent l’espace de noms <xref:Microsoft.AspNetCore.Server.Kestrel.Core> :

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

Dans des exemples montrés plus tard dans cet article, les options Kestrel sont configurées dans le code C. Les options Kestrel peuvent également être définies à l’aide d’un [fournisseur de configuration.](xref:fundamentals/configuration/index) Par exemple, le [fournisseur de configuration de fichiers](xref:fundamentals/configuration/index#file-configuration-provider) peut charger la configuration Kestrel à partir d’un *appsettings.json* ou *appsettings. Fichier Environnement-json* :

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
> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>et [la configuration des points de terminaison](#endpoint-configuration) sont configurables auprès des fournisseurs de configuration. La configuration Kestrel restante doit être configurée dans le code C.

Utilisez **l’une** des approches suivantes :

* Configurer Kestrel dans `Startup.ConfigureServices`:

  1. Injecter un `IConfiguration` exemple `Startup` de dans la classe. L’exemple suivant suppose que la configuration `Configuration` injectée est attribuée à la propriété.
  2. Dans `Startup.ConfigureServices`, `Kestrel` charger la section de configuration dans la configuration de Kestrel :

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

* Configurer Kestrel lors de la construction de l’hôte:

  Dans *Program.cs*, chargez `Kestrel` la section de configuration dans la configuration de Kestrel :

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

Les deux approches précédentes fonctionnent avec n’importe quel [fournisseur de configuration](xref:fundamentals/configuration/index).

### <a name="keep-alive-timeout"></a>Délai d’expiration toujours actif

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

Obtient ou définit le [délai d’expiration toujours actif](https://tools.ietf.org/html/rfc7230#section-6.5). La valeur par défaut est de 2 minutes.

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=19-20)]

### <a name="maximum-client-connections"></a>Nombre maximale de connexions client

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

Le nombre maximal de connexions TCP ouvertes simultanées peut être défini pour l’application entière avec le code suivant :

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

Il existe une limite distincte pour les connexions qui ont été mises à niveau à partir de HTTP ou HTTPS vers un autre protocole (par exemple, sur une demande WebSocket). Une fois mise à niveau, une connexion n’est pas prise en compte dans la limite `MaxConcurrentConnections`.

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

Le nombre maximal de connexions est illimité (null) par défaut.

### <a name="maximum-request-body-size"></a>Taille maximale du corps de la requête

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

La taille maximale par défaut du corps de la requête est de 30 000 000 octets, soit environ 28,6 Mo.

Pour remplacer la limite dans une application ASP.NET Core MVC, nous vous recommandons d’utiliser l’attribut <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> sur une méthode d’action :

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

Voici un exemple qui montre comment configurer la contrainte pour l’application sur chaque requête :

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

Remplacer le paramètre sur une demande spécifique dans middleware:

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

Une exception est lancée si l’application configure la limite sur une demande après que l’application a commencé à lire la demande. Il existe une propriété `IsReadOnly` qui indique si la propriété `MaxRequestBodySize` est en lecture seule ; si tel est le cas, il est trop tard pour configurer la limite.

Quand une application est exécutée [hors processus](xref:host-and-deploy/iis/index#out-of-process-hosting-model) derrière le [Module ASP.NET Core](xref:host-and-deploy/aspnet-core-module), la limite de taille du corps de demande de Kestrel est désactivée, car IIS définit déjà la limite.

### <a name="minimum-request-body-data-rate"></a>Débit données minimal du corps de la requête

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

Kestrel vérifie à chaque seconde si les données arrivent au débit spécifié en octets/seconde. Si le taux tombe en dessous du minimum, la connexion est chronométrée. Le délai de grâce est le temps que Kestrel donne au client pour augmenter son taux d’envoi jusqu’au minimum; le taux n’est pas vérifié pendant cette période. La période de grâce permet d’éviter la suppression des connexions qui, initialement, envoient des données à une vitesse lente en raison de la lenteur du démarrage de TCP.

Le débit minimal par défaut est 240 octets/seconde, avec une période de grâce de 5 secondes.

Un débit minimal s’applique également à la réponse. Le code pour définir les limites de demande et de réponse est identique à l’exception de `RequestBody` ou `Response` dans les noms de propriété et d’interface.

Voici un exemple qui montre comment configurer les débits de données minimaux dans *Program.cs* :

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-11)]

Remplacer les limites tarifaires minimales par demande dans middleware:

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

Le <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature>débit référencé dans l’exemple précédent n’est pas présent dans `HttpContext.Features` pour les requêtes HTTP/2, car la modification des limites de débit par requête n’est généralement pas prise en charge pour HTTP/2 (le protocole prend en charge le multiplexage de requête). Toutefois, le <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> est toujours présent `HttpContext.Features` pour les requêtes HTTP/2, car la limite de débit de lecture peut toujours être *désactivée entièrement* sur une base par demande en définissant `IHttpMinRequestBodyDataRateFeature.MinDataRate` sur `null` même pour une requête HTTP/2. Une tentative de lecture de `IHttpMinRequestBodyDataRateFeature.MinDataRate` ou une tentative de définition sur une valeur autre que `null` entraîne une levée de `NotSupportedException` selon une requête HTTP/2.

Les limites de débit à l’échelle du serveur configurées par le biais de `KestrelServerOptions.Limits` s’appliquent encore aux connexions HTTP/1.x et HTTP/2.

### <a name="request-headers-timeout"></a>Délai d’expiration des en-têtes de requête

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

Obtient ou définit le temps maximal passé par le serveur à recevoir des en-têtes de requête. La valeur par défaut est de 30 secondes.

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=21-22)]

### <a name="maximum-streams-per-connection"></a>Flux de données maximal par connexion

`Http2.MaxStreamsPerConnection` limite le nombre de flux de requête simultanée par connexion HTTP/2. Les flux de données excédentaires sont refusés.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
});
```

La valeur par défaut est 100.

### <a name="header-table-size"></a>Taille de la table d’en-tête

Le décodeur HPACK décompresse les en-têtes HTTP pour les connexions HTTP/2. `Http2.HeaderTableSize` limite la taille de la table de compression d’en-tête que le décodeur HPACK utilise. La valeur est fournie en octets et doit être supérieure à zéro (0).

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.HeaderTableSize = 4096;
});
```

La valeur par défaut est 4096.

### <a name="maximum-frame-size"></a>Taille de trame maximale

`Http2.MaxFrameSize`indique la taille maximale autorisée d’une charge utile DE cadre de connexion HTTP/2 reçue ou envoyée par le serveur. La valeur est fournie en octets et doit être comprise entre 2^14 (16,384) et 2^24-1 (16,777,215).

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxFrameSize = 16384;
});
```

La valeur par défaut est 2^14 (16,384).

### <a name="maximum-request-header-size"></a>Taille maximale d’en-tête de requête

`Http2.MaxRequestHeaderFieldSize` indique la taille maximale autorisée en octets des valeurs d’en-tête de requête. Cette limite s’applique à la fois au nom et à la valeur de leurs représentations compressées et non compressées. La valeur doit être supérieure à zéro (0).

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
});
```

La valeur par défaut est 8 192.

### <a name="initial-connection-window-size"></a>Taille de fenêtre de connexion initiale

`Http2.InitialConnectionWindowSize` indique la quantité maximale de données de corps de requête, en octets, que le serveur met en mémoire tampon à un moment donné pour toutes les requêtes (flux) par connexion. Les requêtes sont également limitées par `Http2.InitialStreamWindowSize`. La valeur doit être supérieure ou égale à 65 535 et inférieure à 2^31 (2 147 483 648).

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
});
```

La valeur par défaut est 128 Ko (131 072).

### <a name="initial-stream-window-size"></a>Taille de la fenêtre de flux initiale

`Http2.InitialStreamWindowSize` indique la quantité maximale de données de corps de requête, en octets, que le serveur met en mémoire tampon à un moment donné par requête (flux). Les requêtes sont également limitées par `Http2.InitialConnectionWindowSize`. La valeur doit être supérieure ou égale à 65 535 et inférieure à 2^31 (2 147 483 648).

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
});
```

La valeur par défaut est 96 Ko (98 304).

### <a name="synchronous-io"></a>E/S synchrone

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO>contrôle si la synchronisation I/O est autorisée pour la demande et la réponse. La valeur par défaut est `false`.

> [!WARNING]
> Un grand nombre d’opérations synchrones de blocage I/O peuvent conduire à la famine de piscine de fil, ce qui rend l’application insensible. Activez `AllowSynchronousIO` uniquement lorsque vous utilisez une bibliothèque qui ne prend pas en charge asynchrone I / O.

L’exemple suivant permet synchrone I/O:

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

Pour plus d’informations sur les autres options et limites de Kestrel, consultez :

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a>Configuration du point de terminaison

Par défaut, ASP.NET Core se lie à :

* `http://localhost:5000`
* `https://localhost:5001` (quand un certificat de développement local est présent)

Spécifiez les URL avec :

* La variable d’environnement `ASPNETCORE_URLS`.
* L’argument de ligne de commande `--urls`.
* La clé de configuration d’hôte `urls`.
* La méthode d’extension `UseUrls`.

La valeur fournie avec ces approches peut être un ou plusieurs points de terminaison HTTP et HTTPS (HTTPS si un certificat par défaut est disponible). Configurez la valeur sous forme de liste délimitée par des points-virgules (par exemple `"Urls": "http://localhost:8000;http://localhost:8001"`).

Pour plus d’informations sur ces approches, voir [URL de serveur](xref:fundamentals/host/web-host#server-urls) et [Remplacer la configuration](xref:fundamentals/host/web-host#override-configuration).

Un certificat de développement est créé :

* Quand le [SDK .NET Core](/dotnet/core/sdk) est installé.
* [L’outil dev-certs](xref:aspnetcore-2.1#https) est utilisé pour créer un certificat.

Certains navigateurs exigent l’octroi d’une autorisation explicite pour faire confiance au certificat de développement local.

Les modèles de projet configurent les applications pour s’exécuter sur HTTPS par défaut et incluent [la redirection HTTPS et le support HSTS](xref:security/enforcing-ssl).

Appelez les méthodes <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> ou <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> sur <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> pour configurer les préfixes et les ports d’URL pour Kestrel.

`UseUrls`, l’argument de ligne de commande `--urls`, la clé de configuration d’hôte `urls` et la variable d’environnement `ASPNETCORE_URLS` fonctionnent également, mais ils présentent les limitations indiquées plus loin dans cette section (un certificat par défaut doit être disponible pour la configuration du point de terminaison HTTPS).

`KestrelServerOptions`Configuration:

### <a name="configureendpointdefaultsactionlistenoptions"></a>ConfigureEndpointDefaults (Action\<ListenOptions>)

Spécifie une `Action` de configuration à exécuter pour chaque point de terminaison spécifié. Le fait d’appeler `ConfigureEndpointDefaults` plusieurs fois remplace les `Action`s précédentes par la dernière `Action` spécifiée.

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
> Les points de <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> terminaison créés par l’appel **avant** l’appel <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> n’auront pas les défauts appliqués.

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a>ConfigureHttpsDefaults (Action\<HttpsConnectionAdapterOptions>)

Spécifie une `Action` de configuration à exécuter pour chaque point de terminaison HTTPS. Le fait d’appeler `ConfigureHttpsDefaults` plusieurs fois remplace les `Action`s précédentes par la dernière `Action` spécifiée.

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
> Les points de <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> terminaison créés par l’appel **avant** l’appel <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> n’auront pas les défauts appliqués.

### <a name="configureiconfiguration"></a>Configure(IConfiguration)

Crée un chargeur de configuration pour configurer Kestrel, qui prend en entrée une <xref:Microsoft.Extensions.Configuration.IConfiguration>. La configuration doit être limitée à la section de configuration pour Kestrel.

### <a name="listenoptionsusehttps"></a>ListenOptions.UseHttps

Configure Kestrel pour l’utilisation de HTTPS.

Extensions de `ListenOptions.UseHttps` :

* `UseHttps`&ndash; Configurez Kestrel pour utiliser HTTPS avec le certificat par défaut. Lève une exception si aucun certificat par défaut n’est configuré.
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

Paramètres de `ListenOptions.UseHttps` :

* `filename` est le chemin et le nom d’un fichier de certificat, relatif au répertoire qui contient les fichiers de contenu de l’application.
* `password` est le mot de passe nécessaire pour accéder aux données du certificat X.509.
* `configureOptions` est une `Action` pour configurer les `HttpsConnectionAdapterOptions`. Retourne l'`ListenOptions`.
* `storeName` est le magasin de certificats à partir duquel charger le certificat.
* `subject` est le nom du sujet du certificat.
* `allowInvalid` indique si les certificats non valides doivent être considérés, comme les certificats auto-signés.
* `location` est l’emplacement du magasin à partir duquel charger le certificat.
* `serverCertificate` est le certificat X.509.

En production, HTTPS doit être explicitement configuré. Au minimum, un certificat par défaut doit être fourni.

Configurations prises en charge décrites dans la suite :

* Pas de configuration
* Remplacer le certificat par défaut dans la configuration
* Changer les valeurs par défaut dans le code

*Pas de configuration*

Kestrel écoute sur `http://localhost:5000` et sur `https://localhost:5001` (si un certificat par défaut est disponible).

<a name="configuration"></a>

*Remplacer le certificat par défaut dans la configuration*

Par défaut, `CreateDefaultBuilder` appelle `Configure(context.Configuration.GetSection("Kestrel"))` pour charger la configuration de Kestrel. Un schéma de configuration des paramètres d’application HTTPS par défaut est disponible pour Kestrel. Configurez plusieurs points de terminaison, notamment les URL et les certificats à utiliser, à partir d’un fichier sur disque ou d’un magasin de certificats.

Dans l’exemple de fichier *appsettings.json* suivant :

* Définissez **AllowInvalid** sur `true` pour permettre l’utilisation de certificats non valides (par exemple des certificats auto-signés).
* Tout point de terminaison HTTPS qui ne spécifie pas de certificat (**HttpsDefaultCert** dans l’exemple qui suit) revient au certificat défini sous **Certificats** > **Par défaut ** ou au certificat de développement.

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

Une alternative à l’utilisation de **Chemin** et de **Mot de passe** pour un nœud de certificat consiste à spécifier le certificat avec des champs du magasin de certificats. Par exemple, le certificat**de défaut** **des certificats** > peut être spécifié sous la forme de :

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

Notes de schéma :

* Les noms des points de terminaison ne respectent pas la casse. Par exemple, `HTTPS` et `Https` sont valides.
* Le paramètre `Url` est obligatoire pour chaque point de terminaison. Le format de ce paramètre est le même que celui du paramètre de configuration `Urls` du plus haut niveau, sauf qu’il est limité à une seule valeur.
* Ces points de terminaison remplacent ceux qui sont définis dans le paramètre de configuration `Urls` du plus haut niveau configuration, au lieu de s’y ajouter. Les points de terminaison définis dans le code via `Listen` sont cumulatifs avec les points de terminaison définis dans la section de configuration.
* La section `Certificate` est facultative. Si la section `Certificate` n’est pas spécifiée, les valeurs par défaut définies dans les scénarios précédents sont utilisées. Si aucune valeur par défaut n’est disponible, le serveur lève une exception et son démarrage échoue.
* La `Certificate` section prend en charge à la fois **les**&ndash;certificats Path**Password** et **Subject**&ndash;**Store.**
* Vous pouvez définir un nombre quelconque de points de terminaison de cette façon, pour autant qu’ils ne provoquent pas de conflits de port.
* `options.Configure(context.Configuration.GetSection("{SECTION}"))` retourne un `KestrelConfigurationLoader` avec une méthode `.Endpoint(string name, listenOptions => { })` qui peut être utilisée pour compléter les paramètres d’un point de terminaison configuré :

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

`KestrelServerOptions.ConfigurationLoader`est directement accessible pour continuer à itérer sur la chargeuse <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>existante, comme celle fournie par .

* La section de configuration pour chaque point `Endpoint` de terminaison est disponible sur les options de la méthode afin que les paramètres personnalisés puissent être lus.
* Plusieurs configurations peuvent être chargées en rappelant `options.Configure(context.Configuration.GetSection("{SECTION}"))` avec une autre section. Seule la dernière configuration est utilisée, à moins que `Load` soit explicitement appelé sur les instances précédentes. Le métapackage n’appelle pas `Load` : sa section de configuration par défaut peut donc être remplacée.
* `KestrelConfigurationLoader` reflète la famille d’API `Listen` de `KestrelServerOptions` sous forme de surcharges de `Endpoint` : le code et les points de terminaison de configuration peuvent donc être configurés au même emplacement. Ces surcharges n’utilisent pas de noms et consomment seulement les paramètres par défaut de la configuration.

*Changer les valeurs par défaut dans le code*

`ConfigureEndpointDefaults` et `ConfigureHttpsDefaults` peuvent être utilisés pour modifier les paramètres par défaut pour `ListenOptions` et `HttpsConnectionAdapterOptions`, notamment en remplaçant le certificat par défaut spécifié dans le scénario précédent. `ConfigureEndpointDefaults` et `ConfigureHttpsDefaults` doivent être appelés avant que des points de terminaison soient configurés.

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

*Prise en charge de SNI par Kestrel*

[L’indication du nom de serveur (SNI, Server Name Indication)](https://tools.ietf.org/html/rfc6066#section-3) peut être utilisée pour héberger plusieurs domaines sur la même adresse IP et le même port. Pour que SNI fonctionne, le client envoie le nom d’hôte pour la session sécurisée au serveur pendant la négociation TLS afin que le serveur puisse fournir le certificat correct. Le client utilise le certificat fourni pour la communication chiffrée avec le serveur pendant la session sécurisée qui suit la négociation TLS.

Kestrel prend en charge SNI via le rappel de `ServerCertificateSelector`. Le rappel est appelé une fois par connexion pour permettre à l’application d’inspecter le nom d’hôte et de sélectionner le certificat approprié.

La prise en charge de SNI nécessite les points suivants :

* Exécution sur `netcoreapp2.1` le cadre cible ou plus tard. Sur `net461` ou plus tard, le rappel `name` est `null`invoqué, mais le est toujours . `name` est également `null` si le client ne fournit pas le paramètre du nom d’hôte dans la négociation TLS.
* Tous les sites web s’exécutent sur la même instance Kestrel. Kestrel ne prend pas en charge le partage d’une adresse IP et d’un port entre plusieurs instances sans un proxy inverse.

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

### <a name="connection-logging"></a>Enregistrement de connexion

Appelez <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> pour émettre des journaux de niveau Debug pour une communication au niveau des byte sur une connexion. L’enregistrement de connexion est utile pour les problèmes de dépannage dans la communication de bas niveau, comme pendant le cryptage TLS et derrière les procurations. Si `UseConnectionLogging` elle `UseHttps`est placée avant, le trafic crypté est enregistré. Si `UseConnectionLogging` elle `UseHttps`est placée après, le trafic décrypté est enregistré.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a>Lier à un socket TCP

La méthode <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> se lie à un socket TCP, et une expression lambda options permet la configuration d’un certificat X.509 :

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=12-18)]

L’exemple configure HTTPS pour un point de terminaison avec <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>. Utilisez la même API afin de configurer d’autres paramètres Kestrel pour des points de terminaison spécifiques.

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a>Lier à un socket Unix

Écoutez sur un socket Unix avec <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> pour améliorer les performances avec Nginx, comme illustré dans cet exemple :

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* Dans le fichier de configuration `server`  >  `location`  >  `proxy_pass` Nginx, définissez l’entrée à `http://unix:/tmp/{KESTREL SOCKET}:/;`. `{KESTREL SOCKET}`est le nom de la <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> prise fournie `kestrel-test.sock` à (par exemple, dans l’exemple précédent).
* Assurez-vous que la prise est écrivante `chmod go+w /tmp/kestrel-test.sock`par Nginx (par exemple, ).

### <a name="port-0"></a>Port 0

Si vous spécifiez le numéro de port `0`, Kestrel se lie dynamiquement à un port disponible. L’exemple suivant montre comment déterminer le port auquel Kestrel se lie à l’exécution :

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

Quand l’application est exécutée, la sortie de la fenêtre de console indique le port dynamique où l’application peut être atteinte :

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a>Limites

Configurez des points de terminaison avec les approches suivantes :

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* Arguments de ligne de commande `--urls`
* Clé de configuration d’hôte `urls`.
* `ASPNETCORE_URLS` variable d’environnement

Ces méthodes sont utiles si vous voulez que votre code fonctionne avec des serveurs autres que Kestrel. Toutefois, soyez conscient des limitations suivantes :

* HTTPS ne peut pas être utilisé avec ces approches, sauf si un certificat par défaut est fourni dans la configuration du point de terminaison HTTPS (par exemple avec la configuration de `KestrelServerOptions` ou un fichier de configuration, comme illustré plus haut dans cette rubrique).
* Quand les deux approches `Listen` et `UseUrls` sont utilisées simultanément, les points de terminaison `Listen` remplacent les points de terminaison `UseUrls`.

### <a name="iis-endpoint-configuration"></a>Configuration de point de terminaison IIS

Quand vous utilisez IIS, les liaisons d’URL pour IIS remplacent les liaisons qui sont définies par `Listen` ou par `UseUrls`. Pour plus d’informations, consultez la rubrique [Module ASP.NET Core](xref:host-and-deploy/aspnet-core-module).

### <a name="listenoptionsprotocols"></a>ListenOptions.Protocols

La propriété `Protocols` établit les protocoles HTTP (`HttpProtocols`) activés sur un point de terminaison de connexion ou pour le serveur. Affectez une valeur à la propriété `Protocols` à partir de l’énumération `HttpProtocols`.

| Valeur enum `HttpProtocols` | Protocole de connexion autorisé |
| -------------------------- | ----------------------------- |
| `Http1`                    | HTTP/1.1 uniquement. Peut être utilisé avec ou sans TLS. |
| `Http2`                    | HTTP/2 uniquement. Peut être utilisé sans TLS, uniquement si le client prend en charge un [mode de connaissance préalable (Prior Knowledge)](https://tools.ietf.org/html/rfc7540#section-3.4). |
| `Http1AndHttp2`            | HTTP/1.1 et HTTP/2. HTTP/2 exige que le client sélectionne HTTP/2 dans la poignée de main TLS [Application-Layer Protocol Negotiation (ALPN);](https://tools.ietf.org/html/rfc7301#section-3) autrement, la connexion par défaut à HTTP/1.1. |

La `ListenOptions.Protocols` valeur par défaut `HttpProtocols.Http1AndHttp2`pour n’importe quel critère d’évaluation est .

Restrictions TLS pour HTTP/2 :

* TLS version 1.2 ou ultérieure
* Renégociation désactivée
* Compression désactivée
* Tailles minimales de l’échange de clé éphémère :
  * Diffie-Hellman à courbe elliptique (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack; &ndash; 224 bits minimum
  * Finie champ Diffie-Hellman (DHE) &lbrack; `TLS12` &rbrack; &ndash; 2048 bits minimum
* Suite de chiffrement non inscrite sur liste rouge

`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`&lbrack; &lbrack; `FIPS186` &rbrack; avec la courbe elliptique P-256 est pris en charge par défaut. `TLS-ECDHE` &rbrack;

L’exemple suivant autorise les connexions HTTP/1.1 et HTTP/2 sur le port 8000. Les connexions sont sécurisées par TLS avec un certificat fourni :

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

Utilisez Connection Middleware pour filtrer les poignées de main TLS sur une base par connexion pour des chiffrements spécifiques si nécessaire.

L’exemple suivant <xref:System.NotSupportedException> donne suite à tout algorithme de chiffrement que l’application ne prend pas en charge. Alternativement, définir et comparer [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) à une liste de suites de chiffrement acceptable.

Aucun chiffrement n’est utilisé avec un algorithme de chiffrement [CipherAlgorithmType.Null.](xref:System.Security.Authentication.CipherAlgorithmType)

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

Le filtrage de connexion <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> peut également être configuré via un lambda :

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

Sur Linux, <xref:System.Net.Security.CipherSuitesPolicy> peut être utilisé pour filtrer les poignées de main TLS sur une base par connexion:

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

*Définir le protocole à partir de la configuration*

Par défaut, `CreateDefaultBuilder` appelle `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` pour charger la configuration de Kestrel.

L’exemple suivant *appsettings.json* établit HTTP/1.1 comme protocole de connexion par défaut pour tous les points de terminaison :

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1"
    }
  }
}
```

L’exemple suivant *de appsettings.json* établit le protocole de connexion HTTP/1.1 pour un critère d’évaluation spécifique :

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

Les protocoles spécifiés dans le code remplacent les valeurs définies par configuration.

## <a name="transport-configuration"></a>Configuration du transport

Pour les projets qui nécessitent<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>l’utilisation de Libuv ( ):

* Ajoutez une dépendance pour le package [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) au fichier projet de l’application :

   ```xml
   <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                     Version="{VERSION}" />
   ```

* Faites <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> appel `IWebHostBuilder`à la :

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

### <a name="url-prefixes"></a>Préfixes d’URL

Quand vous utilisez `UseUrls`, l’argument de ligne de commande `--urls`, la clé de configuration d’hôte `urls` ou la variable d’environnement `ASPNETCORE_URLS`, les préfixes d’URL peuvent être dans un des formats suivants.

Seuls les préfixes d’URL HTTP sont valides. Kestrel ne prend pas en charge HTTPS lors de la configuration de liaisons d’URL avec `UseUrls`.

* Adresse IPv4 avec numéro de port

  ```
  http://65.55.39.10:80/
  ```

  `0.0.0.0` est un cas spécial qui se lie à toutes les adresses IPv4.

* Adresse IPv6 avec numéro de port

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  `[::]` est l’équivalent IPv6 de `0.0.0.0` dans IPv4.

* Nom d’hôte avec numéro de port

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  Les noms d’hôte, `*` et `+` ne sont pas spéciaux. Tout ce qui n’est pas reconnu comme adresse IP valide ou `localhost` se lie à toutes les adresses IP IPv4 et IPv6. Pour lier différents noms d’hôte à différentes applications ASP.NET Core sur le même port, utilisez [HTTP.sys](xref:fundamentals/servers/httpsys) ou un serveur proxy inverse, comme IIS, Nginx ou Apache.

  > [!WARNING]
  > L’hébergement dans une configuration de proxy inverse nécessite le [filtrage d’hôte](#host-filtering).

* Nom `localhost` de l’hôte avec numéro de port ou adresse IP de bouclage avec numéro de port

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  Quand `localhost` est spécifié, Kestrel tente de se lier aux interfaces de bouclage IPv4 et IPv6. Si le port demandé est en cours d’utilisation par un autre service sur l’une des interfaces de bouclage, Kestrel ne démarre pas. Si l’une des interfaces de bouclage n’est pas disponible pour une raison quelconque (généralement du fait de la non-prise en charge d’IPv6), Kestrel journalise un avertissement.

## <a name="host-filtering"></a>Filtrage d’hôtes

Si Kestrel prend en charge la configuration basée sur les préfixes, comme `http://example.com:5000`, il ignore en grande partie le nom d’hôte. L’hôte `localhost` est un cas spécial utilisé pour la liaison à des adresses de bouclage. Tout hôte autre qu’une adresse IP explicite se lie à toutes les adresses IP publiques. Les en-têtes `Host` ne sont pas validés.

En guise de solution de contournement, utilisez le middleware de filtrage d’hôtes. Host Filtering Middleware est fourni par le package [Microsoft.AspNetCore.HostFiltering,](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) qui est implicitement prévu pour ASP.NET applications Core. L’intergiciel (middleware) est ajouté par <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, qui appelle <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> :

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

Le middleware de filtrage d’hôtes est désactivé par défaut. Pour activer le middleware, définissez une `AllowedHosts` clé dans les *applicationsettings.json*/*appsettings.\< EnvironmentName>.json*. La valeur est une liste délimitée par des points-virgules des noms d’hôte sans numéros de port :

*appsettings.json*:

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> Le [middleware des en-têtes transférés](xref:host-and-deploy/proxy-load-balancer) a aussi une option <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts>. Le middleware des en-têtes transférés et le middleware de filtrage d’hôtes ont des fonctionnalités similaires pour différents scénarios. La définition d’`AllowedHosts` avec le middleware des en-têtes transférés est appropriée quand l’en-tête `Host` n’est pas conservé durant le transfert des requêtes avec un serveur proxy inverse ou un équilibreur de charge. La définition d’`AllowedHosts` avec le middleware de filtrage d’hôtes est appropriée quand Kestrel est utilisé comme serveur de périphérie public ou que l’en-tête `Host` est directement transféré.
>
> Pour plus d’informations sur le middleware des en-têtes transférés, consultez <xref:host-and-deploy/proxy-load-balancer>.

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Kestrel est un [serveur web](xref:fundamentals/servers/index)multiplateforme pour ASP.NET Core . Kestrel est le serveur web inclus par défaut dans les modèles de projets ASP.NET Core.

Kestrel prend en charge les scénarios suivants :

* HTTPS
* Mise à niveau opaque utilisée pour activer les [WebSockets](https://github.com/aspnet/websockets)
* Sockets UNIX pour des performances élevées derrière Nginx
* HTTP/2 (sauf sur macOS&dagger;)

&dagger;HTTP/2 sera pris en charge sur macOS dans une prochaine version.

Kestrel est pris en charge sur toutes les plateformes et les versions prises en charge par .NET Core.

[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([comment télécharger](xref:index#how-to-download-a-sample))

## <a name="http2-support"></a>Assistance HTTP/2

[HTTP/2](https://httpwg.org/specs/rfc7540.html) est disponible pour les applications ASP.NET Core si les conditions de base suivantes sont remplies :

* Système d’exploitation&dagger;
  * Windows Server 2016/Windows 10 ou version ultérieure&Dagger;
  * Linux avec OpenSSL 1.0.2 ou version ultérieure (par exemple, Ubuntu 16.04 ou version ultérieure)
* Version cible de .Net Framework : .NET Core 2.2 ou version ultérieure
* Connexion [ALPN (Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3)
* TLS 1.2 ou connexion ultérieure

&dagger;HTTP/2 sera pris en charge sur macOS dans une prochaine version.
&Dagger;Kestrel propose une prise en charge limitée de HTTP/2 sous Windows Server 2012 R2 et Windows 8.1. La prise en charge est limitée car la liste des suites de chiffrement TLS prises en charge sur ces systèmes d’exploitation est limitée. Un certificat généré à l’aide d’Elliptic Curve Digital Signature algorithme (ECDSA) peut être requis pour sécuriser les connexions TLS.

Si une connexion HTTP/2 est établie, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) retourne `HTTP/2`.

HTTP/2 est désactivé par défaut. Pour plus d’informations sur la configuration, consultez les sections [Options Kestrel](#kestrel-options) et [ListenOptions.Protocols](#listenoptionsprotocols).

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a>Quand utiliser Kestrel avec un proxy inverse ?

Vous pouvez utiliser Kestrel par lui-même ou avec un *serveur proxy inverse*, comme [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org) ou [Apache](https://httpd.apache.org/). Un serveur proxy inverse reçoit les requêtes HTTP en provenance du réseau et les transmet à Kestrel.

Kestrel utilisé comme serveur web edge (accessible sur Internet) :

![Kestrel communique directement avec Internet sans serveur proxy inverse](kestrel/_static/kestrel-to-internet2.png)

Kestrel utilisé dans une configuration de proxy inverse :

![Kestrel communique indirectement avec Internet via un serveur proxy inverse, par exemple IIS, Nginx ou Apache](kestrel/_static/kestrel-to-internet.png)

L’une ou l’autre configuration, avec ou sans serveur proxy inversé, est une configuration d’hébergement prise en charge.

Kestrel, s’il est utilisé comme serveur de périphérie sans serveur proxy inverse, ne prend pas en charge le partage de la même adresse IP et du même port entre plusieurs processus. Quand Kestrel est configuré pour écouter sur un port, il gère tout le trafic pour ce port, quel que soit les en-têtes `Host` des requêtes. Un proxy inverse qui peut partager des ports a la possibilité de transférer des requêtes à Kestrel sur une adresse IP et un port uniques.

Même si un serveur proxy inverse n’est pas nécessaire, en utiliser un peut être un bon choix.

Un proxy inverse :

* Peut limiter la surface publique exposée des applications qu’il héberge.
* Fournit une couche supplémentaire de configuration et de défense.
* Peut mieux s’intégrer à l’infrastructure existante.
* Simplifie la configuration de l’équilibrage de charge et d’une communication sécurisée (HTTPS). Seul le serveur proxy inversé nécessite un certificat X.509, et ce serveur peut communiquer avec les serveurs de l’application sur le réseau interne à l’aide de HTTP simple.

> [!WARNING]
> L’hébergement dans une configuration de proxy inverse nécessite le [filtrage d’hôte](#host-filtering).

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a>Comment utiliser Kestrel dans les applications ASP.NET Core

Le package [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) est inclus dans le [métapackage Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).

Les modèles de projet ASP.NET Core utilisent Kestrel par défaut. Dans *Program.cs*, le modèle de code appelle <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, qui appelle <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> en arrière-plan.

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=7)]

Pour plus `CreateDefaultBuilder` d’informations sur et la construction de <xref:fundamentals/host/web-host#set-up-a-host>l’hôte, voir la mise en place *d’une* section hôte de .

Pour fournir une configuration supplémentaire après l’appel de `CreateDefaultBuilder`, utilisez `ConfigureKestrel` :

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        });
```

Si l’application n’appelle pas `CreateDefaultBuilder` pour configurer l’hôte, appelez <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **avant** d’appeler `ConfigureKestrel` :

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

## <a name="kestrel-options"></a>Options Kestrel

Le serveur web Kestrel a des options de configuration de contrainte qui sont particulièrement utiles dans les déploiements exposés à Internet.

Définissez des contraintes sur la propriété <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> de la classe <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>. La propriété `Limits` conserve une instance de la classe <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>.

Les exemples suivants utilisent l’espace de noms <xref:Microsoft.AspNetCore.Server.Kestrel.Core> :

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

Les options Kestrel, qui sont configurées dans le code C dans les exemples suivants, peuvent également être définies à l’aide d’un [fournisseur de configuration.](xref:fundamentals/configuration/index) Par exemple, le fournisseur de configuration de fichiers peut charger la configuration Kestrel à partir d’un *appsettings.json* ou *appsettings. Fichier Environnement-json* :

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

Utilisez **l’une** des approches suivantes :

* Configurer Kestrel dans `Startup.ConfigureServices`:

  1. Injecter un `IConfiguration` exemple `Startup` de dans la classe. L’exemple suivant suppose que la configuration `Configuration` injectée est attribuée à la propriété.
  2. Dans `Startup.ConfigureServices`, `Kestrel` charger la section de configuration dans la configuration de Kestrel :

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

* Configurer Kestrel lors de la construction de l’hôte:

  Dans *Program.cs*, chargez `Kestrel` la section de configuration dans la configuration de Kestrel :

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

Les deux approches précédentes fonctionnent avec n’importe quel [fournisseur de configuration](xref:fundamentals/configuration/index).

### <a name="keep-alive-timeout"></a>Délai d’expiration toujours actif

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

Obtient ou définit le [délai d’expiration toujours actif](https://tools.ietf.org/html/rfc7230#section-6.5). La valeur par défaut est de 2 minutes.

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=15)]

### <a name="maximum-client-connections"></a>Nombre maximale de connexions client

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

Le nombre maximal de connexions TCP ouvertes simultanées peut être défini pour l’application entière avec le code suivant :

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

Il existe une limite distincte pour les connexions qui ont été mises à niveau à partir de HTTP ou HTTPS vers un autre protocole (par exemple, sur une demande WebSocket). Une fois mise à niveau, une connexion n’est pas prise en compte dans la limite `MaxConcurrentConnections`.

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

Le nombre maximal de connexions est illimité (null) par défaut.

### <a name="maximum-request-body-size"></a>Taille maximale du corps de la requête

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

La taille maximale par défaut du corps de la requête est de 30 000 000 octets, soit environ 28,6 Mo.

Pour remplacer la limite dans une application ASP.NET Core MVC, nous vous recommandons d’utiliser l’attribut <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> sur une méthode d’action :

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

Voici un exemple qui montre comment configurer la contrainte pour l’application sur chaque requête :

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

Remplacer le paramètre sur une demande spécifique dans middleware:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

Une exception est lancée si l’application configure la limite sur une demande après que l’application a commencé à lire la demande. Il existe une propriété `IsReadOnly` qui indique si la propriété `MaxRequestBodySize` est en lecture seule ; si tel est le cas, il est trop tard pour configurer la limite.

Quand une application est exécutée [hors processus](xref:host-and-deploy/iis/index#out-of-process-hosting-model) derrière le [Module ASP.NET Core](xref:host-and-deploy/aspnet-core-module), la limite de taille du corps de demande de Kestrel est désactivée, car IIS définit déjà la limite.

### <a name="minimum-request-body-data-rate"></a>Débit données minimal du corps de la requête

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

Kestrel vérifie à chaque seconde si les données arrivent au débit spécifié en octets/seconde. Si le taux tombe en dessous du minimum, la connexion est chronométrée. Le délai de grâce est le temps que Kestrel donne au client pour augmenter son taux d’envoi jusqu’au minimum; le taux n’est pas vérifié pendant cette période. La période de grâce permet d’éviter la suppression des connexions qui, initialement, envoient des données à une vitesse lente en raison de la lenteur du démarrage de TCP.

Le débit minimal par défaut est 240 octets/seconde, avec une période de grâce de 5 secondes.

Un débit minimal s’applique également à la réponse. Le code pour définir les limites de demande et de réponse est identique à l’exception de `RequestBody` ou `Response` dans les noms de propriété et d’interface.

Voici un exemple qui montre comment configurer les débits de données minimaux dans *Program.cs* :

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-9)]

Remplacer les limites tarifaires minimales par demande dans middleware:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

Aucune des fonctionnalités de débit référencées dans l’exemple précédent n’est présente dans `HttpContext.Features` pour les requêtes HTTP/2, car la modification des limites de débit par requête n’est pas prise en charge pour HTTP/2 (le protocole prend en charge le multiplexage de requête). Les limites de débit à l’échelle du serveur configurées par le biais de `KestrelServerOptions.Limits` s’appliquent encore aux connexions HTTP/1.x et HTTP/2.

### <a name="request-headers-timeout"></a>Délai d’expiration des en-têtes de requête

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

Obtient ou définit le temps maximal passé par le serveur à recevoir des en-têtes de requête. La valeur par défaut est de 30 secondes.

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=16)]

### <a name="maximum-streams-per-connection"></a>Flux de données maximal par connexion

`Http2.MaxStreamsPerConnection` limite le nombre de flux de requête simultanée par connexion HTTP/2. Les flux de données excédentaires sont refusés.

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
        });
```

La valeur par défaut est 100.

### <a name="header-table-size"></a>Taille de la table d’en-tête

Le décodeur HPACK décompresse les en-têtes HTTP pour les connexions HTTP/2. `Http2.HeaderTableSize` limite la taille de la table de compression d’en-tête que le décodeur HPACK utilise. La valeur est fournie en octets et doit être supérieure à zéro (0).

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.HeaderTableSize = 4096;
        });
```

La valeur par défaut est 4096.

### <a name="maximum-frame-size"></a>Taille de trame maximale

`Http2.MaxFrameSize` Indique la taille maximale de charge utile dans la trame de connexion HTTP/2 à recevoir. La valeur est fournie en octets et doit être comprise entre 2^14 (16,384) et 2^24-1 (16,777,215).

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxFrameSize = 16384;
        });
```

La valeur par défaut est 2^14 (16,384).

### <a name="maximum-request-header-size"></a>Taille maximale d’en-tête de requête

`Http2.MaxRequestHeaderFieldSize` indique la taille maximale autorisée en octets des valeurs d’en-tête de requête. Cette limite s’applique au nom et à la valeur dans leurs représentations compressées et non compressées. La valeur doit être supérieure à zéro (0).

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
        });
```

La valeur par défaut est 8 192.

### <a name="initial-connection-window-size"></a>Taille de fenêtre de connexion initiale

`Http2.InitialConnectionWindowSize` indique la quantité maximale de données de corps de requête, en octets, que le serveur met en mémoire tampon à un moment donné pour toutes les requêtes (flux) par connexion. Les requêtes sont également limitées par `Http2.InitialStreamWindowSize`. La valeur doit être supérieure ou égale à 65 535 et inférieure à 2^31 (2 147 483 648).

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
        });
```

La valeur par défaut est 128 Ko (131 072).

### <a name="initial-stream-window-size"></a>Taille de la fenêtre de flux initiale

`Http2.InitialStreamWindowSize` indique la quantité maximale de données de corps de requête, en octets, que le serveur met en mémoire tampon à un moment donné par requête (flux). Les requêtes sont également limitées par `Http2.InitialStreamWindowSize`. La valeur doit être supérieure ou égale à 65 535 et inférieure à 2^31 (2 147 483 648).

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
        });
```

La valeur par défaut est 96 Ko (98 304).

### <a name="synchronous-io"></a>E/S synchrone

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO>contrôle si la synchronisation I/O est autorisée pour la demande et la réponse. La valeur par défaut est `true`.

> [!WARNING]
> Un grand nombre d’opérations synchrones de blocage I/O peuvent conduire à la famine de piscine de fil, ce qui rend l’application insensible. Activez `AllowSynchronousIO` uniquement lorsque vous utilisez une bibliothèque qui ne prend pas en charge asynchrone I / O.

L’exemple suivant permet synchrone I/O:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

Pour plus d’informations sur les autres options et limites de Kestrel, consultez :

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a>Configuration du point de terminaison

Par défaut, ASP.NET Core se lie à :

* `http://localhost:5000`
* `https://localhost:5001` (quand un certificat de développement local est présent)

Spécifiez les URL avec :

* La variable d’environnement `ASPNETCORE_URLS`.
* L’argument de ligne de commande `--urls`.
* La clé de configuration d’hôte `urls`.
* La méthode d’extension `UseUrls`.

La valeur fournie avec ces approches peut être un ou plusieurs points de terminaison HTTP et HTTPS (HTTPS si un certificat par défaut est disponible). Configurez la valeur sous forme de liste délimitée par des points-virgules (par exemple `"Urls": "http://localhost:8000;http://localhost:8001"`).

Pour plus d’informations sur ces approches, voir [URL de serveur](xref:fundamentals/host/web-host#server-urls) et [Remplacer la configuration](xref:fundamentals/host/web-host#override-configuration).

Un certificat de développement est créé :

* Quand le [SDK .NET Core](/dotnet/core/sdk) est installé.
* [L’outil dev-certs](xref:aspnetcore-2.1#https) est utilisé pour créer un certificat.

Certains navigateurs exigent l’octroi d’une autorisation explicite pour faire confiance au certificat de développement local.

Les modèles de projet configurent les applications pour s’exécuter sur HTTPS par défaut et incluent [la redirection HTTPS et le support HSTS](xref:security/enforcing-ssl).

Appelez les méthodes <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> ou <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> sur <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> pour configurer les préfixes et les ports d’URL pour Kestrel.

`UseUrls`, l’argument de ligne de commande `--urls`, la clé de configuration d’hôte `urls` et la variable d’environnement `ASPNETCORE_URLS` fonctionnent également, mais ils présentent les limitations indiquées plus loin dans cette section (un certificat par défaut doit être disponible pour la configuration du point de terminaison HTTPS).

`KestrelServerOptions`Configuration:

### <a name="configureendpointdefaultsactionlistenoptions"></a>ConfigureEndpointDefaults (Action\<ListenOptions>)

Spécifie une `Action` de configuration à exécuter pour chaque point de terminaison spécifié. Le fait d’appeler `ConfigureEndpointDefaults` plusieurs fois remplace les `Action`s précédentes par la dernière `Action` spécifiée.

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
> Les points de <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> terminaison créés par l’appel **avant** l’appel <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> n’auront pas les défauts appliqués.

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a>ConfigureHttpsDefaults (Action\<HttpsConnectionAdapterOptions>)

Spécifie une `Action` de configuration à exécuter pour chaque point de terminaison HTTPS. Le fait d’appeler `ConfigureHttpsDefaults` plusieurs fois remplace les `Action`s précédentes par la dernière `Action` spécifiée.

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
> Les points de <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> terminaison créés par l’appel **avant** l’appel <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> n’auront pas les défauts appliqués.


### <a name="configureiconfiguration"></a>Configure(IConfiguration)

Crée un chargeur de configuration pour configurer Kestrel, qui prend en entrée une <xref:Microsoft.Extensions.Configuration.IConfiguration>. La configuration doit être limitée à la section de configuration pour Kestrel.

### <a name="listenoptionsusehttps"></a>ListenOptions.UseHttps

Configure Kestrel pour l’utilisation de HTTPS.

Extensions de `ListenOptions.UseHttps` :

* `UseHttps`&ndash; Configurez Kestrel pour utiliser HTTPS avec le certificat par défaut. Lève une exception si aucun certificat par défaut n’est configuré.
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

Paramètres de `ListenOptions.UseHttps` :

* `filename` est le chemin et le nom d’un fichier de certificat, relatif au répertoire qui contient les fichiers de contenu de l’application.
* `password` est le mot de passe nécessaire pour accéder aux données du certificat X.509.
* `configureOptions` est une `Action` pour configurer les `HttpsConnectionAdapterOptions`. Retourne l'`ListenOptions`.
* `storeName` est le magasin de certificats à partir duquel charger le certificat.
* `subject` est le nom du sujet du certificat.
* `allowInvalid` indique si les certificats non valides doivent être considérés, comme les certificats auto-signés.
* `location` est l’emplacement du magasin à partir duquel charger le certificat.
* `serverCertificate` est le certificat X.509.

En production, HTTPS doit être explicitement configuré. Au minimum, un certificat par défaut doit être fourni.

Configurations prises en charge décrites dans la suite :

* Pas de configuration
* Remplacer le certificat par défaut dans la configuration
* Changer les valeurs par défaut dans le code

*Pas de configuration*

Kestrel écoute sur `http://localhost:5000` et sur `https://localhost:5001` (si un certificat par défaut est disponible).

<a name="configuration"></a>

*Remplacer le certificat par défaut dans la configuration*

Par défaut, `CreateDefaultBuilder` appelle `Configure(context.Configuration.GetSection("Kestrel"))` pour charger la configuration de Kestrel. Un schéma de configuration des paramètres d’application HTTPS par défaut est disponible pour Kestrel. Configurez plusieurs points de terminaison, notamment les URL et les certificats à utiliser, à partir d’un fichier sur disque ou d’un magasin de certificats.

Dans l’exemple de fichier *appsettings.json* suivant :

* Définissez **AllowInvalid** sur `true` pour permettre l’utilisation de certificats non valides (par exemple des certificats auto-signés).
* Tout point de terminaison HTTPS qui ne spécifie pas de certificat (**HttpsDefaultCert** dans l’exemple qui suit) revient au certificat défini sous **Certificats** > **Par défaut ** ou au certificat de développement.

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

Une alternative à l’utilisation de **Chemin** et de **Mot de passe** pour un nœud de certificat consiste à spécifier le certificat avec des champs du magasin de certificats. Par exemple, le certificat**de défaut** **des certificats** > peut être spécifié sous la forme de :

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

Notes de schéma :

* Les noms des points de terminaison ne respectent pas la casse. Par exemple, `HTTPS` et `Https` sont valides.
* Le paramètre `Url` est obligatoire pour chaque point de terminaison. Le format de ce paramètre est le même que celui du paramètre de configuration `Urls` du plus haut niveau, sauf qu’il est limité à une seule valeur.
* Ces points de terminaison remplacent ceux qui sont définis dans le paramètre de configuration `Urls` du plus haut niveau configuration, au lieu de s’y ajouter. Les points de terminaison définis dans le code via `Listen` sont cumulatifs avec les points de terminaison définis dans la section de configuration.
* La section `Certificate` est facultative. Si la section `Certificate` n’est pas spécifiée, les valeurs par défaut définies dans les scénarios précédents sont utilisées. Si aucune valeur par défaut n’est disponible, le serveur lève une exception et son démarrage échoue.
* La `Certificate` section prend en charge à la fois **les**&ndash;certificats Path**Password** et **Subject**&ndash;**Store.**
* Vous pouvez définir un nombre quelconque de points de terminaison de cette façon, pour autant qu’ils ne provoquent pas de conflits de port.
* `options.Configure(context.Configuration.GetSection("{SECTION}"))` retourne un `KestrelConfigurationLoader` avec une méthode `.Endpoint(string name, listenOptions => { })` qui peut être utilisée pour compléter les paramètres d’un point de terminaison configuré :

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

`KestrelServerOptions.ConfigurationLoader`est directement accessible pour continuer à itérer sur la chargeuse <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>existante, comme celle fournie par .

* La section de configuration pour chaque point `Endpoint` de terminaison est disponible sur les options de la méthode afin que les paramètres personnalisés puissent être lus.
* Plusieurs configurations peuvent être chargées en rappelant `options.Configure(context.Configuration.GetSection("{SECTION}"))` avec une autre section. Seule la dernière configuration est utilisée, à moins que `Load` soit explicitement appelé sur les instances précédentes. Le métapackage n’appelle pas `Load` : sa section de configuration par défaut peut donc être remplacée.
* `KestrelConfigurationLoader` reflète la famille d’API `Listen` de `KestrelServerOptions` sous forme de surcharges de `Endpoint` : le code et les points de terminaison de configuration peuvent donc être configurés au même emplacement. Ces surcharges n’utilisent pas de noms et consomment seulement les paramètres par défaut de la configuration.

*Changer les valeurs par défaut dans le code*

`ConfigureEndpointDefaults` et `ConfigureHttpsDefaults` peuvent être utilisés pour modifier les paramètres par défaut pour `ListenOptions` et `HttpsConnectionAdapterOptions`, notamment en remplaçant le certificat par défaut spécifié dans le scénario précédent. `ConfigureEndpointDefaults` et `ConfigureHttpsDefaults` doivent être appelés avant que des points de terminaison soient configurés.

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

*Prise en charge de SNI par Kestrel*

[L’indication du nom de serveur (SNI, Server Name Indication)](https://tools.ietf.org/html/rfc6066#section-3) peut être utilisée pour héberger plusieurs domaines sur la même adresse IP et le même port. Pour que SNI fonctionne, le client envoie le nom d’hôte pour la session sécurisée au serveur pendant la négociation TLS afin que le serveur puisse fournir le certificat correct. Le client utilise le certificat fourni pour la communication chiffrée avec le serveur pendant la session sécurisée qui suit la négociation TLS.

Kestrel prend en charge SNI via le rappel de `ServerCertificateSelector`. Le rappel est appelé une fois par connexion pour permettre à l’application d’inspecter le nom d’hôte et de sélectionner le certificat approprié.

La prise en charge de SNI nécessite les points suivants :

* Exécution sur `netcoreapp2.1` le cadre cible ou plus tard. Sur `net461` ou plus tard, le rappel `name` est `null`invoqué, mais le est toujours . `name` est également `null` si le client ne fournit pas le paramètre du nom d’hôte dans la négociation TLS.
* Tous les sites web s’exécutent sur la même instance Kestrel. Kestrel ne prend pas en charge le partage d’une adresse IP et d’un port entre plusieurs instances sans un proxy inverse.

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

### <a name="connection-logging"></a>Enregistrement de connexion

Appelez <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> pour émettre des journaux de niveau Debug pour une communication au niveau des byte sur une connexion. L’enregistrement de connexion est utile pour les problèmes de dépannage dans la communication de bas niveau, comme pendant le cryptage TLS et derrière les procurations. Si `UseConnectionLogging` elle `UseHttps`est placée avant, le trafic crypté est enregistré. Si `UseConnectionLogging` elle `UseHttps`est placée après, le trafic décrypté est enregistré.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a>Lier à un socket TCP

La méthode <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> se lie à un socket TCP, et une expression lambda options permet la configuration d’un certificat X.509 :

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=9-16)]

L’exemple configure HTTPS pour un point de terminaison avec <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>. Utilisez la même API afin de configurer d’autres paramètres Kestrel pour des points de terminaison spécifiques.

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a>Lier à un socket Unix

Écoutez sur un socket Unix avec <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> pour améliorer les performances avec Nginx, comme illustré dans cet exemple :

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* Dans le fichier de confiuguration `server`  >  `location`  >  `proxy_pass` Nginx, définissez l’entrée à `http://unix:/tmp/{KESTREL SOCKET}:/;`. `{KESTREL SOCKET}`est le nom de la <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> prise fournie `kestrel-test.sock` à (par exemple, dans l’exemple précédent).
* Assurez-vous que la prise est écrivante `chmod go+w /tmp/kestrel-test.sock`par Nginx (par exemple, ). 

### <a name="port-0"></a>Port 0

Si vous spécifiez le numéro de port `0`, Kestrel se lie dynamiquement à un port disponible. L’exemple suivant montre comment déterminer le port auquel Kestrel se lie à l’exécution :

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

Quand l’application est exécutée, la sortie de la fenêtre de console indique le port dynamique où l’application peut être atteinte :

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a>Limites

Configurez des points de terminaison avec les approches suivantes :

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* Arguments de ligne de commande `--urls`
* Clé de configuration d’hôte `urls`.
* `ASPNETCORE_URLS` variable d’environnement

Ces méthodes sont utiles si vous voulez que votre code fonctionne avec des serveurs autres que Kestrel. Toutefois, soyez conscient des limitations suivantes :

* HTTPS ne peut pas être utilisé avec ces approches, sauf si un certificat par défaut est fourni dans la configuration du point de terminaison HTTPS (par exemple avec la configuration de `KestrelServerOptions` ou un fichier de configuration, comme illustré plus haut dans cette rubrique).
* Quand les deux approches `Listen` et `UseUrls` sont utilisées simultanément, les points de terminaison `Listen` remplacent les points de terminaison `UseUrls`.

### <a name="iis-endpoint-configuration"></a>Configuration de point de terminaison IIS

Quand vous utilisez IIS, les liaisons d’URL pour IIS remplacent les liaisons qui sont définies par `Listen` ou par `UseUrls`. Pour plus d’informations, consultez la rubrique [Module ASP.NET Core](xref:host-and-deploy/aspnet-core-module).

### <a name="listenoptionsprotocols"></a>ListenOptions.Protocols

La propriété `Protocols` établit les protocoles HTTP (`HttpProtocols`) activés sur un point de terminaison de connexion ou pour le serveur. Affectez une valeur à la propriété `Protocols` à partir de l’énumération `HttpProtocols`.

| Valeur enum `HttpProtocols` | Protocole de connexion autorisé |
| -------------------------- | ----------------------------- |
| `Http1`                    | HTTP/1.1 uniquement. Peut être utilisé avec ou sans TLS. |
| `Http2`                    | HTTP/2 uniquement. Peut être utilisé sans TLS, uniquement si le client prend en charge un [mode de connaissance préalable (Prior Knowledge)](https://tools.ietf.org/html/rfc7540#section-3.4). |
| `Http1AndHttp2`            | HTTP/1.1 et HTTP/2. HTTP/2 nécessite une connexion TLS et [Négociation de protocole d’application-couche (ALPN);](https://tools.ietf.org/html/rfc7301#section-3) autrement, la connexion par défaut à HTTP/1.1. |

Le protocole par défaut est HTTP/1.1.

Restrictions TLS pour HTTP/2 :

* TLS version 1.2 ou ultérieure
* Renégociation désactivée
* Compression désactivée
* Tailles minimales de l’échange de clé éphémère :
  * Diffie-Hellman à courbe elliptique (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack; &ndash; 224 bits minimum
  * Finie champ Diffie-Hellman (DHE) &lbrack; `TLS12` &rbrack; &ndash; 2048 bits minimum
* Suite de chiffrement non inscrite sur liste rouge

`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`&lbrack; &lbrack; `FIPS186` &rbrack; avec la courbe elliptique P-256 est pris en charge par défaut. `TLS-ECDHE` &rbrack;

L’exemple suivant autorise les connexions HTTP/1.1 et HTTP/2 sur le port 8000. Les connexions sont sécurisées par TLS avec un certificat fourni :

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

Le cas échéant, créez une implémentation `IConnectionAdapter` pour filtrer les négociations TLS par connexion pour des chiffrements spécifiques :

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

*Définir le protocole à partir de la configuration*

Par défaut, <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> appelle `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` pour charger la configuration de Kestrel.

Dans l’exemple *appsettings.json* suivant, un protocole de connexion par défaut (HTTP/1.1 et HTTP/2) est établi pour l’ensemble des points de terminaison de Kestrel :

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1AndHttp2"
    }
  }
}
```

L’exemple de fichier de configuration suivant établit un protocole de connexion pour un point de terminaison spécifique :

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

Les protocoles spécifiés dans le code remplacent les valeurs définies par configuration.

## <a name="transport-configuration"></a>Configuration du transport

Dans ASP.NET Core 2.1, le transport par défaut de Kestrel n’est plus basé sur Libuv, mais sur des sockets managés. Il s’agit d’un changement cassant pour les applications ASP.NET Core 2.0 mises à niveau vers la version 2.1 qui appellent <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> et qui dépendent d’un des packages suivants :

* [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (référence de package directe)
* [Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

Pour les projets qui nécessitent l’utilisation de Libuv :

* Ajoutez une dépendance pour le package [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) au fichier projet de l’application :

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* Appelez <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> :

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

### <a name="url-prefixes"></a>Préfixes d’URL

Quand vous utilisez `UseUrls`, l’argument de ligne de commande `--urls`, la clé de configuration d’hôte `urls` ou la variable d’environnement `ASPNETCORE_URLS`, les préfixes d’URL peuvent être dans un des formats suivants.

Seuls les préfixes d’URL HTTP sont valides. Kestrel ne prend pas en charge HTTPS lors de la configuration de liaisons d’URL avec `UseUrls`.

* Adresse IPv4 avec numéro de port

  ```
  http://65.55.39.10:80/
  ```

  `0.0.0.0` est un cas spécial qui se lie à toutes les adresses IPv4.

* Adresse IPv6 avec numéro de port

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  `[::]` est l’équivalent IPv6 de `0.0.0.0` dans IPv4.

* Nom d’hôte avec numéro de port

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  Les noms d’hôte, `*` et `+` ne sont pas spéciaux. Tout ce qui n’est pas reconnu comme adresse IP valide ou `localhost` se lie à toutes les adresses IP IPv4 et IPv6. Pour lier différents noms d’hôte à différentes applications ASP.NET Core sur le même port, utilisez [HTTP.sys](xref:fundamentals/servers/httpsys) ou un serveur proxy inverse, comme IIS, Nginx ou Apache.

  > [!WARNING]
  > L’hébergement dans une configuration de proxy inverse nécessite le [filtrage d’hôte](#host-filtering).

* Nom `localhost` de l’hôte avec numéro de port ou adresse IP de bouclage avec numéro de port

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  Quand `localhost` est spécifié, Kestrel tente de se lier aux interfaces de bouclage IPv4 et IPv6. Si le port demandé est en cours d’utilisation par un autre service sur l’une des interfaces de bouclage, Kestrel ne démarre pas. Si l’une des interfaces de bouclage n’est pas disponible pour une raison quelconque (généralement du fait de la non-prise en charge d’IPv6), Kestrel journalise un avertissement.

## <a name="host-filtering"></a>Filtrage d’hôtes

Si Kestrel prend en charge la configuration basée sur les préfixes, comme `http://example.com:5000`, il ignore en grande partie le nom d’hôte. L’hôte `localhost` est un cas spécial utilisé pour la liaison à des adresses de bouclage. Tout hôte autre qu’une adresse IP explicite se lie à toutes les adresses IP publiques. Les en-têtes `Host` ne sont pas validés.

En guise de solution de contournement, utilisez le middleware de filtrage d’hôtes. Host Filtering Middleware est fourni par le package [Microsoft.AspNetCore.HostFiltering,](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) qui est inclus dans le [métapackage Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 ou 2.2). L’intergiciel (middleware) est ajouté par <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, qui appelle <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> :

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

Le middleware de filtrage d’hôtes est désactivé par défaut. Pour activer le middleware, définissez une `AllowedHosts` clé dans les *applicationsettings.json*/*appsettings.\< EnvironmentName>.json*. La valeur est une liste délimitée par des points-virgules des noms d’hôte sans numéros de port :

*appsettings.json*:

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> Le [middleware des en-têtes transférés](xref:host-and-deploy/proxy-load-balancer) a aussi une option <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts>. Le middleware des en-têtes transférés et le middleware de filtrage d’hôtes ont des fonctionnalités similaires pour différents scénarios. La définition d’`AllowedHosts` avec le middleware des en-têtes transférés est appropriée quand l’en-tête `Host` n’est pas conservé durant le transfert des requêtes avec un serveur proxy inverse ou un équilibreur de charge. La définition d’`AllowedHosts` avec le middleware de filtrage d’hôtes est appropriée quand Kestrel est utilisé comme serveur de périphérie public ou que l’en-tête `Host` est directement transféré.
>
> Pour plus d’informations sur le middleware des en-têtes transférés, consultez <xref:host-and-deploy/proxy-load-balancer>.

::: moniker-end

::: moniker range="= aspnetcore-2.1"

Kestrel est un [serveur web](xref:fundamentals/servers/index)multiplateforme pour ASP.NET Core . Kestrel est le serveur web inclus par défaut dans les modèles de projets ASP.NET Core.

Kestrel prend en charge les scénarios suivants :

* HTTPS
* Mise à niveau opaque utilisée pour activer les [WebSockets](https://github.com/aspnet/websockets)
* Sockets UNIX pour des performances élevées derrière Nginx

Kestrel est pris en charge sur toutes les plateformes et les versions prises en charge par .NET Core.

[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([comment télécharger](xref:index#how-to-download-a-sample))

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a>Quand utiliser Kestrel avec un proxy inverse ?

Vous pouvez utiliser Kestrel par lui-même ou avec un *serveur proxy inverse*, comme [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org) ou [Apache](https://httpd.apache.org/). Un serveur proxy inverse reçoit les requêtes HTTP en provenance du réseau et les transmet à Kestrel.

Kestrel utilisé comme serveur web edge (accessible sur Internet) :

![Kestrel communique directement avec Internet sans serveur proxy inverse](kestrel/_static/kestrel-to-internet2.png)

Kestrel utilisé dans une configuration de proxy inverse :

![Kestrel communique indirectement avec Internet via un serveur proxy inverse, par exemple IIS, Nginx ou Apache](kestrel/_static/kestrel-to-internet.png)

L’une ou l’autre configuration, avec ou sans serveur proxy inversé, est une configuration d’hébergement prise en charge.

Kestrel, s’il est utilisé comme serveur de périphérie sans serveur proxy inverse, ne prend pas en charge le partage de la même adresse IP et du même port entre plusieurs processus. Quand Kestrel est configuré pour écouter sur un port, il gère tout le trafic pour ce port, quel que soit les en-têtes `Host` des requêtes. Un proxy inverse qui peut partager des ports a la possibilité de transférer des requêtes à Kestrel sur une adresse IP et un port uniques.

Même si un serveur proxy inverse n’est pas nécessaire, en utiliser un peut être un bon choix.

Un proxy inverse :

* Peut limiter la surface publique exposée des applications qu’il héberge.
* Fournit une couche supplémentaire de configuration et de défense.
* Peut mieux s’intégrer à l’infrastructure existante.
* Simplifie la configuration de l’équilibrage de charge et d’une communication sécurisée (HTTPS). Seul le serveur proxy inversé nécessite un certificat X.509, et ce serveur peut communiquer avec les serveurs de l’application sur le réseau interne à l’aide de HTTP simple.

> [!WARNING]
> L’hébergement dans une configuration de proxy inverse nécessite le [filtrage d’hôte](#host-filtering).

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a>Comment utiliser Kestrel dans les applications ASP.NET Core

Le package [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) est inclus dans le [métapackage Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).

Les modèles de projet ASP.NET Core utilisent Kestrel par défaut. Dans *Program.cs*, le modèle de code appelle <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, qui appelle <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> en arrière-plan.

Pour fournir une configuration supplémentaire après l’appel de `CreateDefaultBuilder`, appelez <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> :

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            // Set properties and call methods on serverOptions
        });
```

Pour plus `CreateDefaultBuilder` d’informations sur et la construction de <xref:fundamentals/host/web-host#set-up-a-host>l’hôte, voir la mise en place *d’une* section hôte de .

## <a name="kestrel-options"></a>Options Kestrel

Le serveur web Kestrel a des options de configuration de contrainte qui sont particulièrement utiles dans les déploiements exposés à Internet.

Définissez des contraintes sur la propriété <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> de la classe <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>. La propriété `Limits` conserve une instance de la classe <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>.

Les exemples suivants utilisent l’espace de noms <xref:Microsoft.AspNetCore.Server.Kestrel.Core> :

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

Les options Kestrel, qui sont configurées dans le code C dans les exemples suivants, peuvent également être définies à l’aide d’un [fournisseur de configuration.](xref:fundamentals/configuration/index) Par exemple, le fournisseur de configuration de fichiers peut charger la configuration Kestrel à partir d’un *appsettings.json* ou *appsettings. Fichier Environnement-json* :

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

Utilisez **l’une** des approches suivantes :

* Configurer Kestrel dans `Startup.ConfigureServices`:

  1. Injecter un `IConfiguration` exemple `Startup` de dans la classe. L’exemple suivant suppose que la configuration `Configuration` injectée est attribuée à la propriété.
  2. Dans `Startup.ConfigureServices`, `Kestrel` charger la section de configuration dans la configuration de Kestrel :

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

* Configurer Kestrel lors de la construction de l’hôte:

  Dans *Program.cs*, chargez `Kestrel` la section de configuration dans la configuration de Kestrel :

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

Les deux approches précédentes fonctionnent avec n’importe quel [fournisseur de configuration](xref:fundamentals/configuration/index).

### <a name="keep-alive-timeout"></a>Délai d’expiration toujours actif

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

Obtient ou définit le [délai d’expiration toujours actif](https://tools.ietf.org/html/rfc7230#section-6.5). La valeur par défaut est de 2 minutes.

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.KeepAliveTimeout = TimeSpan.FromMinutes(2);
        });
```

### <a name="maximum-client-connections"></a>Nombre maximale de connexions client

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

Le nombre maximal de connexions TCP ouvertes simultanées peut être défini pour l’application entière avec le code suivant :

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentConnections = 100;
        });
```

Il existe une limite distincte pour les connexions qui ont été mises à niveau à partir de HTTP ou HTTPS vers un autre protocole (par exemple, sur une demande WebSocket). Une fois mise à niveau, une connexion n’est pas prise en compte dans la limite `MaxConcurrentConnections`.

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentUpgradedConnections = 100;
        });
```

Le nombre maximal de connexions est illimité (null) par défaut.

### <a name="maximum-request-body-size"></a>Taille maximale du corps de la requête

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

La taille maximale par défaut du corps de la requête est de 30 000 000 octets, soit environ 28,6 Mo.

Pour remplacer la limite dans une application ASP.NET Core MVC, nous vous recommandons d’utiliser l’attribut <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> sur une méthode d’action :

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

Voici un exemple qui montre comment configurer la contrainte pour l’application sur chaque requête :

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxRequestBodySize = 10 * 1024;
        });
```

Remplacer le paramètre sur une demande spécifique dans middleware:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

Une exception est lancée si l’application configure la limite sur une demande après que l’application a commencé à lire la demande. Il existe une propriété `IsReadOnly` qui indique si la propriété `MaxRequestBodySize` est en lecture seule ; si tel est le cas, il est trop tard pour configurer la limite.

Quand une application est exécutée [hors processus](xref:host-and-deploy/iis/index#out-of-process-hosting-model) derrière le [Module ASP.NET Core](xref:host-and-deploy/aspnet-core-module), la limite de taille du corps de demande de Kestrel est désactivée, car IIS définit déjà la limite.

### <a name="minimum-request-body-data-rate"></a>Débit données minimal du corps de la requête

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

Kestrel vérifie à chaque seconde si les données arrivent au débit spécifié en octets/seconde. Si le taux tombe en dessous du minimum, la connexion est chronométrée. Le délai de grâce est le temps que Kestrel donne au client pour augmenter son taux d’envoi jusqu’au minimum; le taux n’est pas vérifié pendant cette période. La période de grâce permet d’éviter la suppression des connexions qui, initialement, envoient des données à une vitesse lente en raison de la lenteur du démarrage de TCP.

Le débit minimal par défaut est 240 octets/seconde, avec une période de grâce de 5 secondes.

Un débit minimal s’applique également à la réponse. Le code pour définir les limites de demande et de réponse est identique à l’exception de `RequestBody` ou `Response` dans les noms de propriété et d’interface.

Voici un exemple qui montre comment configurer les débits de données minimaux dans *Program.cs* :

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

### <a name="request-headers-timeout"></a>Délai d’expiration des en-têtes de requête

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

Obtient ou définit le temps maximal passé par le serveur à recevoir des en-têtes de requête. La valeur par défaut est de 30 secondes.

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.RequestHeadersTimeout = TimeSpan.FromMinutes(1);
        });
```

### <a name="synchronous-io"></a>E/S synchrone

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO>contrôle si la synchronisation I/O est autorisée pour la demande et la réponse. La valeur par défaut est `true`.

> [!WARNING]
> Un grand nombre d’opérations synchrones de blocage I/O peuvent conduire à la famine de piscine de fil, ce qui rend l’application insensible. Activez `AllowSynchronousIO` uniquement lorsque vous utilisez une bibliothèque qui ne prend pas en charge asynchrone I / O.

L’exemple suivant désactive synchrone I/O :

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.AllowSynchronousIO = false;
        });
```

Pour plus d’informations sur les autres options et limites de Kestrel, consultez :

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a>Configuration du point de terminaison

Par défaut, ASP.NET Core se lie à :

* `http://localhost:5000`
* `https://localhost:5001` (quand un certificat de développement local est présent)

Spécifiez les URL avec :

* La variable d’environnement `ASPNETCORE_URLS`.
* L’argument de ligne de commande `--urls`.
* La clé de configuration d’hôte `urls`.
* La méthode d’extension `UseUrls`.

La valeur fournie avec ces approches peut être un ou plusieurs points de terminaison HTTP et HTTPS (HTTPS si un certificat par défaut est disponible). Configurez la valeur sous forme de liste délimitée par des points-virgules (par exemple `"Urls": "http://localhost:8000;http://localhost:8001"`).

Pour plus d’informations sur ces approches, voir [URL de serveur](xref:fundamentals/host/web-host#server-urls) et [Remplacer la configuration](xref:fundamentals/host/web-host#override-configuration).

Un certificat de développement est créé :

* Quand le [SDK .NET Core](/dotnet/core/sdk) est installé.
* [L’outil dev-certs](xref:aspnetcore-2.1#https) est utilisé pour créer un certificat.

Certains navigateurs exigent l’octroi d’une autorisation explicite pour faire confiance au certificat de développement local.

Les modèles de projet configurent les applications pour s’exécuter sur HTTPS par défaut et incluent [la redirection HTTPS et le support HSTS](xref:security/enforcing-ssl).

Appelez les méthodes <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> ou <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> sur <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> pour configurer les préfixes et les ports d’URL pour Kestrel.

`UseUrls`, l’argument de ligne de commande `--urls`, la clé de configuration d’hôte `urls` et la variable d’environnement `ASPNETCORE_URLS` fonctionnent également, mais ils présentent les limitations indiquées plus loin dans cette section (un certificat par défaut doit être disponible pour la configuration du point de terminaison HTTPS).

`KestrelServerOptions`Configuration:

### <a name="configureendpointdefaultsactionlistenoptions"></a>ConfigureEndpointDefaults (Action\<ListenOptions>)

Spécifie une `Action` de configuration à exécuter pour chaque point de terminaison spécifié. Le fait d’appeler `ConfigureEndpointDefaults` plusieurs fois remplace les `Action`s précédentes par la dernière `Action` spécifiée.

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
> Les points de <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> terminaison créés par l’appel **avant** l’appel <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> n’auront pas les défauts appliqués.

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a>ConfigureHttpsDefaults (Action\<HttpsConnectionAdapterOptions>)

Spécifie une `Action` de configuration à exécuter pour chaque point de terminaison HTTPS. Le fait d’appeler `ConfigureHttpsDefaults` plusieurs fois remplace les `Action`s précédentes par la dernière `Action` spécifiée.

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
> Les points de <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> terminaison créés par l’appel **avant** l’appel <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> n’auront pas les défauts appliqués.

### <a name="configureiconfiguration"></a>Configure(IConfiguration)

Crée un chargeur de configuration pour configurer Kestrel, qui prend en entrée une <xref:Microsoft.Extensions.Configuration.IConfiguration>. La configuration doit être limitée à la section de configuration pour Kestrel.

### <a name="listenoptionsusehttps"></a>ListenOptions.UseHttps

Configure Kestrel pour l’utilisation de HTTPS.

Extensions de `ListenOptions.UseHttps` :

* `UseHttps`&ndash; Configurez Kestrel pour utiliser HTTPS avec le certificat par défaut. Lève une exception si aucun certificat par défaut n’est configuré.
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

Paramètres de `ListenOptions.UseHttps` :

* `filename` est le chemin et le nom d’un fichier de certificat, relatif au répertoire qui contient les fichiers de contenu de l’application.
* `password` est le mot de passe nécessaire pour accéder aux données du certificat X.509.
* `configureOptions` est une `Action` pour configurer les `HttpsConnectionAdapterOptions`. Retourne l'`ListenOptions`.
* `storeName` est le magasin de certificats à partir duquel charger le certificat.
* `subject` est le nom du sujet du certificat.
* `allowInvalid` indique si les certificats non valides doivent être considérés, comme les certificats auto-signés.
* `location` est l’emplacement du magasin à partir duquel charger le certificat.
* `serverCertificate` est le certificat X.509.

En production, HTTPS doit être explicitement configuré. Au minimum, un certificat par défaut doit être fourni.

Configurations prises en charge décrites dans la suite :

* Pas de configuration
* Remplacer le certificat par défaut dans la configuration
* Changer les valeurs par défaut dans le code

*Pas de configuration*

Kestrel écoute sur `http://localhost:5000` et sur `https://localhost:5001` (si un certificat par défaut est disponible).

<a name="configuration"></a>

*Remplacer le certificat par défaut dans la configuration*

Par défaut, `CreateDefaultBuilder` appelle `Configure(context.Configuration.GetSection("Kestrel"))` pour charger la configuration de Kestrel. Un schéma de configuration des paramètres d’application HTTPS par défaut est disponible pour Kestrel. Configurez plusieurs points de terminaison, notamment les URL et les certificats à utiliser, à partir d’un fichier sur disque ou d’un magasin de certificats.

Dans l’exemple de fichier *appsettings.json* suivant :

* Définissez **AllowInvalid** sur `true` pour permettre l’utilisation de certificats non valides (par exemple des certificats auto-signés).
* Tout point de terminaison HTTPS qui ne spécifie pas de certificat (**HttpsDefaultCert** dans l’exemple qui suit) revient au certificat défini sous **Certificats** > **Par défaut ** ou au certificat de développement.

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

Une alternative à l’utilisation de **Chemin** et de **Mot de passe** pour un nœud de certificat consiste à spécifier le certificat avec des champs du magasin de certificats. Par exemple, le certificat**de défaut** **des certificats** > peut être spécifié sous la forme de :

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

Notes de schéma :

* Les noms des points de terminaison ne respectent pas la casse. Par exemple, `HTTPS` et `Https` sont valides.
* Le paramètre `Url` est obligatoire pour chaque point de terminaison. Le format de ce paramètre est le même que celui du paramètre de configuration `Urls` du plus haut niveau, sauf qu’il est limité à une seule valeur.
* Ces points de terminaison remplacent ceux qui sont définis dans le paramètre de configuration `Urls` du plus haut niveau configuration, au lieu de s’y ajouter. Les points de terminaison définis dans le code via `Listen` sont cumulatifs avec les points de terminaison définis dans la section de configuration.
* La section `Certificate` est facultative. Si la section `Certificate` n’est pas spécifiée, les valeurs par défaut définies dans les scénarios précédents sont utilisées. Si aucune valeur par défaut n’est disponible, le serveur lève une exception et son démarrage échoue.
* La `Certificate` section prend en charge à la fois **les**&ndash;certificats Path**Password** et **Subject**&ndash;**Store.**
* Vous pouvez définir un nombre quelconque de points de terminaison de cette façon, pour autant qu’ils ne provoquent pas de conflits de port.
* `options.Configure(context.Configuration.GetSection("{SECTION}"))` retourne un `KestrelConfigurationLoader` avec une méthode `.Endpoint(string name, listenOptions => { })` qui peut être utilisée pour compléter les paramètres d’un point de terminaison configuré :

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

`KestrelServerOptions.ConfigurationLoader`est directement accessible pour continuer à itérer sur la chargeuse <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>existante, comme celle fournie par .

* La section de configuration pour chaque point `Endpoint` de terminaison est disponible sur les options de la méthode afin que les paramètres personnalisés puissent être lus.
* Plusieurs configurations peuvent être chargées en rappelant `options.Configure(context.Configuration.GetSection("{SECTION}"))` avec une autre section. Seule la dernière configuration est utilisée, à moins que `Load` soit explicitement appelé sur les instances précédentes. Le métapackage n’appelle pas `Load` : sa section de configuration par défaut peut donc être remplacée.
* `KestrelConfigurationLoader` reflète la famille d’API `Listen` de `KestrelServerOptions` sous forme de surcharges de `Endpoint` : le code et les points de terminaison de configuration peuvent donc être configurés au même emplacement. Ces surcharges n’utilisent pas de noms et consomment seulement les paramètres par défaut de la configuration.

*Changer les valeurs par défaut dans le code*

`ConfigureEndpointDefaults` et `ConfigureHttpsDefaults` peuvent être utilisés pour modifier les paramètres par défaut pour `ListenOptions` et `HttpsConnectionAdapterOptions`, notamment en remplaçant le certificat par défaut spécifié dans le scénario précédent. `ConfigureEndpointDefaults` et `ConfigureHttpsDefaults` doivent être appelés avant que des points de terminaison soient configurés.

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

*Prise en charge de SNI par Kestrel*

[L’indication du nom de serveur (SNI, Server Name Indication)](https://tools.ietf.org/html/rfc6066#section-3) peut être utilisée pour héberger plusieurs domaines sur la même adresse IP et le même port. Pour que SNI fonctionne, le client envoie le nom d’hôte pour la session sécurisée au serveur pendant la négociation TLS afin que le serveur puisse fournir le certificat correct. Le client utilise le certificat fourni pour la communication chiffrée avec le serveur pendant la session sécurisée qui suit la négociation TLS.

Kestrel prend en charge SNI via le rappel de `ServerCertificateSelector`. Le rappel est appelé une fois par connexion pour permettre à l’application d’inspecter le nom d’hôte et de sélectionner le certificat approprié.

La prise en charge de SNI nécessite les points suivants :

* Exécution sur `netcoreapp2.1` le cadre cible ou plus tard. Sur `net461` ou plus tard, le rappel `name` est `null`invoqué, mais le est toujours . `name` est également `null` si le client ne fournit pas le paramètre du nom d’hôte dans la négociation TLS.
* Tous les sites web s’exécutent sur la même instance Kestrel. Kestrel ne prend pas en charge le partage d’une adresse IP et d’un port entre plusieurs instances sans un proxy inverse.

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

### <a name="connection-logging"></a>Enregistrement de connexion

Appelez <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> pour émettre des journaux de niveau Debug pour une communication au niveau des byte sur une connexion. L’enregistrement de connexion est utile pour les problèmes de dépannage dans la communication de bas niveau, comme pendant le cryptage TLS et derrière les procurations. Si `UseConnectionLogging` elle `UseHttps`est placée avant, le trafic crypté est enregistré. Si `UseConnectionLogging` elle `UseHttps`est placée après, le trafic décrypté est enregistré.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a>Lier à un socket TCP

La méthode <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> se lie à un socket TCP, et une expression lambda options permet la configuration d’un certificat X.509 :

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

L’exemple configure HTTPS pour un point de terminaison avec <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>. Utilisez la même API afin de configurer d’autres paramètres Kestrel pour des points de terminaison spécifiques.

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a>Lier à un socket Unix

Écoutez sur un socket Unix avec <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> pour améliorer les performances avec Nginx, comme illustré dans cet exemple :

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

* Dans le fichier de confiuguration `server`  >  `location`  >  `proxy_pass` Nginx, définissez l’entrée à `http://unix:/tmp/{KESTREL SOCKET}:/;`. `{KESTREL SOCKET}`est le nom de la <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> prise fournie `kestrel-test.sock` à (par exemple, dans l’exemple précédent).
* Assurez-vous que la prise est écrivante `chmod go+w /tmp/kestrel-test.sock`par Nginx (par exemple, ). 

### <a name="port-0"></a>Port 0

Si vous spécifiez le numéro de port `0`, Kestrel se lie dynamiquement à un port disponible. L’exemple suivant montre comment déterminer le port auquel Kestrel se lie à l’exécution :

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

Quand l’application est exécutée, la sortie de la fenêtre de console indique le port dynamique où l’application peut être atteinte :

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a>Limites

Configurez des points de terminaison avec les approches suivantes :

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* Arguments de ligne de commande `--urls`
* Clé de configuration d’hôte `urls`.
* `ASPNETCORE_URLS` variable d’environnement

Ces méthodes sont utiles si vous voulez que votre code fonctionne avec des serveurs autres que Kestrel. Toutefois, soyez conscient des limitations suivantes :

* HTTPS ne peut pas être utilisé avec ces approches, sauf si un certificat par défaut est fourni dans la configuration du point de terminaison HTTPS (par exemple avec la configuration de `KestrelServerOptions` ou un fichier de configuration, comme illustré plus haut dans cette rubrique).
* Quand les deux approches `Listen` et `UseUrls` sont utilisées simultanément, les points de terminaison `Listen` remplacent les points de terminaison `UseUrls`.

### <a name="iis-endpoint-configuration"></a>Configuration de point de terminaison IIS

Quand vous utilisez IIS, les liaisons d’URL pour IIS remplacent les liaisons qui sont définies par `Listen` ou par `UseUrls`. Pour plus d’informations, consultez la rubrique [Module ASP.NET Core](xref:host-and-deploy/aspnet-core-module).

## <a name="transport-configuration"></a>Configuration du transport

Dans ASP.NET Core 2.1, le transport par défaut de Kestrel n’est plus basé sur Libuv, mais sur des sockets managés. Il s’agit d’un changement cassant pour les applications ASP.NET Core 2.0 mises à niveau vers la version 2.1 qui appellent <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> et qui dépendent d’un des packages suivants :

* [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (référence de package directe)
* [Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

Pour les projets qui nécessitent l’utilisation de Libuv :

* Ajoutez une dépendance pour le package [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) au fichier projet de l’application :

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* Appelez <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> :

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

### <a name="url-prefixes"></a>Préfixes d’URL

Quand vous utilisez `UseUrls`, l’argument de ligne de commande `--urls`, la clé de configuration d’hôte `urls` ou la variable d’environnement `ASPNETCORE_URLS`, les préfixes d’URL peuvent être dans un des formats suivants.

Seuls les préfixes d’URL HTTP sont valides. Kestrel ne prend pas en charge HTTPS lors de la configuration de liaisons d’URL avec `UseUrls`.

* Adresse IPv4 avec numéro de port

  ```
  http://65.55.39.10:80/
  ```

  `0.0.0.0` est un cas spécial qui se lie à toutes les adresses IPv4.

* Adresse IPv6 avec numéro de port

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  `[::]` est l’équivalent IPv6 de `0.0.0.0` dans IPv4.

* Nom d’hôte avec numéro de port

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  Les noms d’hôte, `*` et `+` ne sont pas spéciaux. Tout ce qui n’est pas reconnu comme adresse IP valide ou `localhost` se lie à toutes les adresses IP IPv4 et IPv6. Pour lier différents noms d’hôte à différentes applications ASP.NET Core sur le même port, utilisez [HTTP.sys](xref:fundamentals/servers/httpsys) ou un serveur proxy inverse, comme IIS, Nginx ou Apache.

  > [!WARNING]
  > L’hébergement dans une configuration de proxy inverse nécessite le [filtrage d’hôte](#host-filtering).

* Nom `localhost` de l’hôte avec numéro de port ou adresse IP de bouclage avec numéro de port

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  Quand `localhost` est spécifié, Kestrel tente de se lier aux interfaces de bouclage IPv4 et IPv6. Si le port demandé est en cours d’utilisation par un autre service sur l’une des interfaces de bouclage, Kestrel ne démarre pas. Si l’une des interfaces de bouclage n’est pas disponible pour une raison quelconque (généralement du fait de la non-prise en charge d’IPv6), Kestrel journalise un avertissement.

## <a name="host-filtering"></a>Filtrage d’hôtes

Si Kestrel prend en charge la configuration basée sur les préfixes, comme `http://example.com:5000`, il ignore en grande partie le nom d’hôte. L’hôte `localhost` est un cas spécial utilisé pour la liaison à des adresses de bouclage. Tout hôte autre qu’une adresse IP explicite se lie à toutes les adresses IP publiques. Les en-têtes `Host` ne sont pas validés.

En guise de solution de contournement, utilisez le middleware de filtrage d’hôtes. Host Filtering Middleware est fourni par le package [Microsoft.AspNetCore.HostFiltering,](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) qui est inclus dans le [métapackage Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 ou 2.2). L’intergiciel (middleware) est ajouté par <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, qui appelle <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> :

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

Le middleware de filtrage d’hôtes est désactivé par défaut. Pour activer le middleware, définissez une `AllowedHosts` clé dans les *applicationsettings.json*/*appsettings.\< EnvironmentName>.json*. La valeur est une liste délimitée par des points-virgules des noms d’hôte sans numéros de port :

*appsettings.json*:

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> Le [middleware des en-têtes transférés](xref:host-and-deploy/proxy-load-balancer) a aussi une option <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts>. Le middleware des en-têtes transférés et le middleware de filtrage d’hôtes ont des fonctionnalités similaires pour différents scénarios. La définition d’`AllowedHosts` avec le middleware des en-têtes transférés est appropriée quand l’en-tête `Host` n’est pas conservé durant le transfert des requêtes avec un serveur proxy inverse ou un équilibreur de charge. La définition d’`AllowedHosts` avec le middleware de filtrage d’hôtes est appropriée quand Kestrel est utilisé comme serveur de périphérie public ou que l’en-tête `Host` est directement transféré.
>
> Pour plus d’informations sur le middleware des en-têtes transférés, consultez <xref:host-and-deploy/proxy-load-balancer>.

::: moniker-end

## <a name="additional-resources"></a>Ressources supplémentaires

* Lorsque vous utilisez des prises UNIX sur Linux, la prise n’est pas automatiquement supprimée sur l’arrêt de l’application. Pour plus d’informations, consultez [ce problème GitHub](https://github.com/dotnet/aspnetcore/issues/14134).
* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [Document RFC 7230 : syntaxe et routage des messages (section 5.4 : Hôte)](https://tools.ietf.org/html/rfc7230#section-5.4)
