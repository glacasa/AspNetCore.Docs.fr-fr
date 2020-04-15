---
title: Notions de base d’ASP.NET Core
author: rick-anderson
description: Découvrez les concepts de base permettant de créer des applications ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/30/2020
uid: fundamentals/index
ms.openlocfilehash: c675644d8480ef7a5290045067e6cec2ea6f4764
ms.sourcegitcommit: f29a12486313e38e0163a643d8a97c8cecc7e871
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81384059"
---
# <a name="aspnet-core-fundamentals"></a>Notions de base d’ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Cet article donne un aperçu des sujets clés pour comprendre comment développer ASP.NET applications Core.

## <a name="the-startup-class"></a>Classe Startup

La classe `Startup` est l’endroit où :

* Les services nécessaires à l’application sont configurés.
* Le pipeline de traitement des demandes de l’application est défini comme une série de composants middleware.

Voici un exemple de classe `Startup` :

[!code-csharp[](index/samples_snapshot/3.x/Startup.cs?highlight=3,12)]

Pour plus d’informations, consultez <xref:fundamentals/startup>.

## <a name="dependency-injection-services"></a>Injection de dépendances (services)

ASP.NET Core comprend un cadre intégré d’injection de dépendance (DI) qui rend les services configurés disponibles dans l’ensemble d’une application. Par exemple, un composant de journalisation est un service.

Le code pour configurer (ou *enregistrer*) des services est ajouté à la méthode `Startup.ConfigureServices`. Par exemple :

[!code-csharp[](index/samples_snapshot/3.x/ConfigureServices.cs)]

Les services sont généralement résolus à partir de DI à l’aide de l’injection de constructeurs. Avec l’injection de constructeurs, une classe déclare un paramètre constructeur du type requis ou d’une interface. Le cadre DI fournit un exemple de ce service à l’heure de l’exécution.

L’exemple suivant utilise l’injection de constructeurs pour résoudre un `RazorPagesMovieContext` de DI:

[!code-csharp[](index/samples_snapshot/3.x/Index.cshtml.cs?highlight=5)]

Si le conteneur intégré Inversion of Control (IoC) ne répond pas à tous les besoins d’une application, un conteneur IoC tiers peut être utilisé à la place.

Pour plus d’informations, consultez <xref:fundamentals/dependency-injection>.

## <a name="middleware"></a>Middlewares

Le pipeline de traitement des requêtes est composé comme une série de composants d’intergiciel (middleware). Chaque composant effectue des `HttpContext` opérations sur un et invoque le middleware suivant dans le pipeline ou met fin à la demande.

Par convention, un composant middleware est ajouté au `Use...` pipeline en `Startup.Configure` invoquant une méthode d’extension dans la méthode. Par exemple, pour activer le rendu des fichiers statiques, appelez `UseStaticFiles`.

L’exemple suivant configure un pipeline de traitement des demandes :

[!code-csharp[](index/samples_snapshot/3.x/Configure.cs)]

ASP.NET Core comprend un riche ensemble de middleware intégré. Les composants middleware personnalisés peuvent également être écrits.

Pour plus d’informations, consultez <xref:fundamentals/middleware/index>.

## <a name="host"></a>Host

Sur le démarrage, une application ASP.NET Core construit un *hôte*. L’hôte encapsule toutes les ressources de l’application, telles que :

* Une implémentation serveur HTTP
* Composants d’intergiciel (middleware)
* Journalisation
* Services d’injection de dépendance (DI)
* Configuration

Il y a deux hôtes différents : 

* Hôte générique .NET
* Hôte web ASP.NET Core

L’hôte générique .NET est recommandé. Le ASP.NET Core Web Host n’est disponible que pour la compatibilité vers l’arrière.

L’exemple suivant crée un hôte générique .NET:

[!code-csharp[](index/samples_snapshot/3.x/Program.cs)]

Les `CreateDefaultBuilder` `ConfigureWebHostDefaults` méthodes et les méthodes configurent un hôte avec un ensemble d’options par défaut, telles que :

* Utilisez [Kestrel](#servers) en tant que serveur web et activez l’intégration IIS.
* Chargez la configuration à partir de *appsettings.json*, *appsettings.{Environment Name}.json*, des variables d’environnement, des arguments de ligne de commande et d’autres sources de configuration.
* Envoyez la sortie de journalisation aux fournisseurs Console et Debug.

Pour plus d’informations, consultez <xref:fundamentals/host/generic-host>.

### <a name="non-web-scenarios"></a>Scénarios non basés sur le web

L’hôte générique permet à d’autres types d’application d’utiliser des extensions de framework composites, par exemple la journalisation, l’injection de dépendance, la configuration et la gestion de la durée de vie de l’application. Pour plus d’informations, consultez <xref:fundamentals/host/generic-host> et <xref:fundamentals/host/hosted-services>.

## <a name="servers"></a>Serveurs

Une application ASP.NET Core utilise une implémentation de serveur HTTP pour écouter les requêtes HTTP. Les surfaces de serveur envoient des requêtes à l’application comme un ensemble de [fonctionnalités de requête](xref:fundamentals/request-features) composées dans un `HttpContext`.

# <a name="windows"></a>[Windows](#tab/windows)

ASP.NET Core fournit les implémentations de serveur suivantes :

* *Kestrel* est un serveur web multiplateforme. Kestrel est souvent exécuté dans une configuration de proxy inverse à l’aide d’[IIS](https://www.iis.net/). Dans ASP.NET Core 2.0 ou versions ultérieures, Kestrel peut être exécuté en tant que serveur de périphérie public exposé directement à Internet.
* *IIS HTTP Server* est un serveur pour Windows qui utilise IIS. Avec ce serveur, l’application ASP.NET Core et IIS s’exécutent dans le même processus.
* *HTTP.sys* est un serveur Windows qui n’est pas utilisé avec IIS.

# <a name="macos"></a>[macOS](#tab/macos)

ASP.NET Core fournit l’implémentation du serveur multiplateforme *Kestrel*. Dans ASP.NET Core 2.0 ou plus tard, Kestrel peut fonctionner comme un serveur de bord orienté vers le public directement à Internet. Kestrel est souvent exécuté dans une configuration de proxy inverse avec [Nginx](https://nginx.org) ou [Apache](https://httpd.apache.org/).

# <a name="linux"></a>[Linux](#tab/linux)

ASP.NET Core fournit l’implémentation du serveur multiplateforme *Kestrel*. Dans ASP.NET Core 2.0 ou plus tard, Kestrel peut fonctionner comme un serveur de bord orienté vers le public directement à Internet. Kestrel est souvent exécuté dans une configuration de proxy inverse avec [Nginx](https://nginx.org) ou [Apache](https://httpd.apache.org/).

---

Pour plus d’informations, consultez <xref:fundamentals/servers/index>.

## <a name="configuration"></a>Configuration

ASP.NET Core fournit une infrastructure de configuration qui obtient des paramètres en tant que paires nom-valeur à partir d’un ensemble ordonné de fournisseurs de configuration. Les fournisseurs de configuration intégrés sont disponibles pour une variété de sources, telles que les fichiers *.json,* les fichiers *.xml,* les variables de l’environnement et les arguments de ligne de commande. Écrivez des fournisseurs de configuration personnalisés pour prendre en charge d’autres sources.

Par [défaut,](xref:fundamentals/configuration/index#default)ASP.NET les applications Core sont configurées pour lire à partir de *appsettings.json*, variables de l’environnement, la ligne de commande, et plus encore. Lorsque la configuration de l’application est chargée, les valeurs des variables de l’environnement remplacent les valeurs de *appsettings.json*.

La façon préférée de lire les valeurs de configuration connexes est d’utiliser le [modèle d’options](xref:fundamentals/configuration/options). Pour plus d’informations, voir [Les données hiérarchiques de configuration Bind à l’aide du modèle d’options](xref:fundamentals/configuration/index#optpat).

Pour gérer les données de configuration confidentielles telles que les mots de passe, ASP.NET Core fournit le [Secret Manager](xref:security/app-secrets#secret-manager). Pour les secrets de production, nous vous recommandons [Azure Key Vault](xref:security/key-vault-configuration).

Pour plus d’informations, consultez <xref:fundamentals/configuration/index>.

## <a name="environments"></a>Environnements

Les environnements `Development`d’exécution, tels que , `Staging`, et `Production`, sont une notion de première classe dans ASP.NET Core. Spécifier l’environnement dans `ASPNETCORE_ENVIRONMENT` lequel une application s’exécute en définissant la variable de l’environnement. ASP.NET Core lit la variable d’environnement au démarrage de l’application et stocke la valeur dans une implémentation `IWebHostEnvironment`. Cette implémentation est disponible n’importe où dans une application via injection de dépendance (DI).

L’exemple suivant configure l’application pour fournir `Development` des informations détaillées sur les erreurs lorsqu’elle s’exécute dans l’environnement :

[!code-csharp[](index/samples_snapshot/3.x/StartupConfigure.cs?highlight=3-6)]

Pour plus d’informations, consultez <xref:fundamentals/environments>.

## <a name="logging"></a>Journalisation

ASP.NET Core prend en charge une API de journalisation qui fonctionne avec un large éventail de fournisseurs de journalisation intégrés et tiers. Les fournisseurs disponibles comprennent :

* Console
* Débogage
* Suivi des événements sur Windows
* Journal des événements Windows
* TraceSource
* Azure App Service
* Azure Application Insights

Pour créer des journaux, résoudre un <xref:Microsoft.Extensions.Logging.ILogger%601> service contre l’injection <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>de dépendance (DI) et appeler les méthodes d’enregistrement telles que . Par exemple :

[!code-csharp[](index/samples_snapshot/3.x/TodoController.cs?highlight=5,13,19)]

Méthodes d’exploitation `LogInformation` forestière telles que le soutien d’un certain nombre de champs. Ces champs sont couramment utilisés `string`pour construire un message, mais certains fournisseurs d’exploitation forestière les envoient à un magasin de données comme champs distincts. Cette fonctionnalité permet aux fournisseurs de journalisation d’implémenter la [journalisation sémantique, également appelée journalisation structurée](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

Pour plus d’informations, consultez <xref:fundamentals/logging/index>.

## <a name="routing"></a>Routage

Un *itinéraire* est un modèle d’URL qui est mappé à un gestionnaire. Le gestionnaire est généralement une page Razor, une méthode d’action dans un contrôleur MVC, ou un intergiciel (middleware). Le routage ASP.NET Core vous permet de contrôler les URL utilisées par votre application.

Pour plus d’informations, consultez <xref:fundamentals/routing>.

## <a name="error-handling"></a>Gestion des erreurs

ASP.NET Core offre des fonctionnalités intégrées pour gérer des erreurs, telles que :

* Une page d’exceptions du développeur
* Pages d’erreur personnalisées
* Pages de codes d’état statique
* Gestion des exceptions de démarrage

Pour plus d’informations, consultez <xref:fundamentals/error-handling>.

## <a name="make-http-requests"></a>Effectuer des requêtes HTTP

Une implémentation de `IHttpClientFactory` est disponible pour la création d’instances `HttpClient`. La fabrique :

* Fournit un emplacement central pour le nommage et la configuration d’instance de `HttpClient` logiques. Par exemple, inscrivez et configurez un client *github* pour accéder à GitHub. Inscrivez-vous et configurez un client par défaut à d’autres fins.
* Prend en charge l’inscription et le chaînage de plusieurs gestionnaires de délégation pour créer un pipeline de middlewares pour les requêtes sortantes. Ce modèle est similaire à ASP.NET pipeline intermédiaire de Middleware de Core. Le modèle fournit un mécanisme pour gérer les préoccupations transversales pour les demandes de HTTP, y compris la mise en cache, la gestion des erreurs, la sérialisation et l’enregistrement.
* S’intègre à *Polly*, une bibliothèque tierce populaire pour la gestion des erreurs temporaires.
* Gère la mise en `HttpClientHandler` commun et la durée de vie `HttpClient` des instances sous-jacentes afin d’éviter les problèmes DNS courants qui se produisent lors de la gestion manuelle des durées de vie.
* Ajoute une expérience de journalisation configurable via <xref:Microsoft.Extensions.Logging.ILogger> pour toutes les demandes envoyées par les clients créés par l’usine.

Pour plus d’informations, consultez <xref:fundamentals/http-requests>.

## <a name="content-root"></a>Racine de contenu

La racine de contenu est le chemin de base pour:

* L’hébergement exécutable de l’application (*.exe*).
* Assemblages compilés qui composent l’application (*.dll*).
* Fichiers de contenu utilisés par l’application, tels que :
  * Fichiers de rasoir (*.cshtml*, *.razor*)
  * Fichiers de configuration (*.json*, *.xml*)
  * Fichiers de données (*.db*)
* La [racine Web](#web-root), généralement le dossier *wwwroot.*

Pendant le développement, la racine de contenu par défaut à l’annuaire racine du projet. Ce répertoire est également le chemin de base pour les fichiers de contenu de l’application et la [racine Web](#web-root). Spécifier une racine de contenu différente en fixant son chemin lors [de la construction de l’hôte](#host). Pour plus d’informations, consultez [Racine de contenu](xref:fundamentals/host/generic-host#contentroot).

## <a name="web-root"></a>Racine web

La racine du Web est le chemin de base pour les fichiers de ressources publiques et statiques, tels que :

* Feuilles de style (*.css*)
* JavaScript (*.js*)
* Images (*.png*, *.jpg*)

Par défaut, les fichiers statiques ne sont servis que par le répertoire de racine web et ses sous-répertoires. Le chemin de racine web par défaut à *'contenu racine'/wwwroot*. Spécifier une racine web différente en fixant son chemin lors [de la construction de l’hôte](#host). Pour plus d’informations, consultez [Racine web](xref:fundamentals/host/generic-host#webroot).

Empêchez de publier des fichiers dans *wwwroot* avec [ \<l’élément de projet Content>](/visualstudio/msbuild/common-msbuild-project-items#content) dans le fichier du projet. L’exemple suivant empêche la publication de contenu dans *wwwroot/local* et ses sous-annuaires :

```xml
<ItemGroup>
  <Content Update="wwwroot\local\**\*.*" CopyToPublishDirectory="Never" />
</ItemGroup>
```

Dans les fichiers Razor *.cshtml,* tilde-slash (`~/`) pointe vers la racine web. Un chemin `~/` commençant par est appelé un *chemin virtuel*.

Pour plus d’informations, consultez <xref:fundamentals/static-files>.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Cet article est une vue d’ensemble des sujets clés pour comprendre comment développer des applications ASP.NET Core.

## <a name="the-startup-class"></a>Classe Startup

La classe `Startup` est l’endroit où :

* Les services nécessaires à l’application sont configurés.
* Le pipeline de traitement des requêtes est défini.

Les *services* sont des composants utilisés par l’application. Par exemple, un composant de journalisation est un service. Le code pour configurer (ou *enregistrer*) des services est ajouté à la méthode `Startup.ConfigureServices`.

Le pipeline de traitement des demandes est composé comme une série de composants *middleware.* Par exemple, un intergiciel (middleware) peut gérer les demandes de fichiers statiques ou rediriger les requêtes HTTP vers HTTPS. Chaque intergiciel (middleware) effectue des opérations asynchrones sur `HttpContext`, puis appelle l’intergiciel (middleware) suivant dans le pipeline ou met fin à la requête. Le code pour configurer le pipeline de traitement des requêtes est ajouté à la méthode `Startup.Configure`.

Voici un exemple de classe `Startup` :

[!code-csharp[](index/samples_snapshot/2.x/Startup.cs?highlight=3,12)]

Pour plus d’informations, consultez <xref:fundamentals/startup>.

## <a name="dependency-injection-services"></a>Injection de dépendances (services)

ASP.NET Core offre une infrastructure d’injection de dépendances (DI) intégrée qui rend disponibles les services configurés aux classes d’une application. Pour obtenir une instance d’un service dans une classe, vous pouvez créer un constructeur avec un paramètre du type requis. Le paramètre peut être le type de service ou une interface. Le système d’injection de dépendances fournit le service lors de l’exécution.

Voici une classe qui utilise l’injection de dépendances pour obtenir un objet de contexte Entity Framework Core. La ligne en surbrillance est un exemple d’injection de constructeur :

[!code-csharp[](index/samples_snapshot/2.x/Index.cshtml.cs?highlight=5)]

Si l’injection de dépendances est intégrée, elle est conçue pour vous permettre d’incorporer un conteneur d’inversion de contrôle (IoC) tiers si vous préférez.

Pour plus d’informations, consultez <xref:fundamentals/dependency-injection>.

## <a name="middleware"></a>Middlewares

Le pipeline de traitement des requêtes est composé comme une série de composants d’intergiciel (middleware). Chaque composant effectue des opérations asynchrones sur `HttpContext`, puis appelle l’intergiciel (middleware) suivant dans le pipeline ou met fin à la requête.

Par convention, un composant d’intergiciel (middleware) est ajouté au pipeline en appelant sa méthode d’extension `Use...` dans la méthode `Startup.Configure`. Par exemple, pour activer le rendu des fichiers statiques, appelez `UseStaticFiles`.

Le code en surbrillance dans l’exemple suivant configure le pipeline de traitement des requêtes :

[!code-csharp[](index/samples_snapshot/2.x/Startup.cs?highlight=14-16)]

ASP.NET Core inclut un ensemble complet de middlewares intégrés, et vous pouvez écrire un middleware personnalisé.

Pour plus d’informations, consultez <xref:fundamentals/middleware/index>.

## <a name="host"></a>Host

Une application ASP.NET Core génère un *hôte* au démarrage. L’hôte est un objet qui encapsule toutes les ressources de l’application, telles que :

* Une implémentation serveur HTTP
* Composants d’intergiciel (middleware)
* Journalisation
* DI
* Configuration

La principale raison d’inclure toutes les ressources interdépendantes de l’application dans un objet est la gestion de la durée de vie : contrôler le démarrage de l’application et l’arrêt approprié.

Deux hôtes sont disponibles : l’hôte web et l’hôte générique. En ASP.NET Core 2.x, l’hôte générique est destiné uniquement aux scénarios non basés sur le web.

Le code permettant de créer un hôte se trouve dans `Program.Main` :

[!code-csharp[](index/samples_snapshot/2.x/Program.cs)]

La méthode `CreateDefaultBuilder` permet de configurer un hôte avec les options fréquemment utilisées, notamment :

* Utilisez [Kestrel](#servers) en tant que serveur web et activez l’intégration IIS.
* Chargez la configuration à partir de *appsettings.json*, *appsettings.{Environment Name}.json*, des variables d’environnement, des arguments de ligne de commande et d’autres sources de configuration.
* Envoyez la sortie de journalisation aux fournisseurs Console et Debug.

Pour plus d’informations, consultez <xref:fundamentals/host/web-host>.

### <a name="non-web-scenarios"></a>Scénarios non basés sur le web

L’hôte générique permet à d’autres types d’application d’utiliser des extensions de framework composites, par exemple la journalisation, l’injection de dépendance, la configuration et la gestion de la durée de vie de l’application. Pour plus d’informations, consultez <xref:fundamentals/host/generic-host> et <xref:fundamentals/host/hosted-services>.

## <a name="servers"></a>Serveurs

Une application ASP.NET Core utilise une implémentation de serveur HTTP pour écouter les requêtes HTTP. Les surfaces de serveur envoient des requêtes à l’application comme un ensemble de [fonctionnalités de requête](xref:fundamentals/request-features) composées dans un `HttpContext`.

::: moniker-end

::: moniker range="= aspnetcore-2.2"

# <a name="windows"></a>[Windows](#tab/windows)

ASP.NET Core fournit les implémentations de serveur suivantes :

* *Kestrel* est un serveur web multiplateforme. Kestrel est souvent exécuté dans une configuration de proxy inverse à l’aide d’[IIS](https://www.iis.net/). Kestrel peut être exécuté comme un serveur de bord orienté vers le public directement à Internet.
* *Le serveur HTTP IIS* est un serveur pour Windows qui utilise IIS. Avec ce serveur, l’application ASP.NET Core et IIS s’exécutent dans le même processus.
* *HTTP.sys* est un serveur Windows qui n’est pas utilisé avec IIS.

# <a name="macos"></a>[macOS](#tab/macos)

ASP.NET Core fournit l’implémentation du serveur multiplateforme *Kestrel*. Kestrel peut être exécuté comme un serveur de bord orienté vers le public directement à Internet. Kestrel est souvent exécuté dans une configuration de proxy inverse avec [Nginx](https://nginx.org) ou [Apache](https://httpd.apache.org/).

# <a name="linux"></a>[Linux](#tab/linux)

ASP.NET Core fournit l’implémentation du serveur multiplateforme *Kestrel*. Kestrel peut être exécuté comme un serveur de bord orienté vers le public directement à Internet. Kestrel est souvent exécuté dans une configuration de proxy inverse avec [Nginx](https://nginx.org) ou [Apache](https://httpd.apache.org/).

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windows"></a>[Windows](#tab/windows)

ASP.NET Core fournit les implémentations de serveur suivantes :

* *Kestrel* est un serveur web multiplateforme. Kestrel est souvent exécuté dans une configuration de proxy inverse à l’aide d’[IIS](https://www.iis.net/). Kestrel peut être exécuté comme un serveur de bord orienté vers le public directement à Internet.
* *HTTP.sys* est un serveur Windows qui n’est pas utilisé avec IIS.

# <a name="macos"></a>[macOS](#tab/macos)

ASP.NET Core fournit l’implémentation du serveur multiplateforme *Kestrel*. Kestrel peut être exécuté comme un serveur de bord orienté vers le public directement à Internet. Kestrel est souvent exécuté dans une configuration de proxy inverse avec [Nginx](https://nginx.org) ou [Apache](https://httpd.apache.org/).

# <a name="linux"></a>[Linux](#tab/linux)

ASP.NET Core fournit l’implémentation du serveur multiplateforme *Kestrel*. Kestrel peut être exécuté comme un serveur de bord orienté vers le public directement à Internet. Kestrel est souvent exécuté dans une configuration de proxy inverse avec [Nginx](https://nginx.org) ou [Apache](https://httpd.apache.org/).

---

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Pour plus d’informations, consultez <xref:fundamentals/servers/index>.

## <a name="configuration"></a>Configuration

ASP.NET Core fournit une infrastructure de configuration qui obtient des paramètres en tant que paires nom-valeur à partir d’un ensemble ordonné de fournisseurs de configuration. Il existe des fournisseurs de configuration intégrés pour une grande variété de sources, tels que des fichiers *.json*, *.xml*, des variables d’environnement et des arguments de ligne de commande. Vous pouvez également écrire des fournisseurs de configuration.

Par exemple, vous pouvez spécifier que la configuration provient de *appsettings.json* et de variables d’environnement. Lorsque la valeur de *ConnectionString* est demandée, l’infrastructure recherche d’abord dans le fichier *appsettings.json*. Si la valeur est trouvée dans ce fichier, mais également dans une variable d’environnement, la valeur de la variable d’environnement est prioritaire.

Pour gérer des données de configuration confidentielles telles que les mots de passe, ASP.NET Core fournit un [outil Secret Manager](xref:security/app-secrets). Pour les secrets de production, nous vous recommandons [Azure Key Vault](xref:security/key-vault-configuration).

Pour plus d’informations, consultez <xref:fundamentals/configuration/index>.

## <a name="options"></a>Options

Lorsque cela est possible, ASP.NET Core suit le *modèle d’options* pour stocker et récupérer des valeurs de configuration. Le modèle d’options utilise des classes pour représenter les groupes de paramètres associés.

Par exemple, le code suivant définit des options WebSockets :

[!code-csharp[](index/samples_snapshot/2.x/UseWebSockets.cs)]

Pour plus d’informations, consultez <xref:fundamentals/configuration/options>.

## <a name="environments"></a>Environnements

Les environnements d’exécution, tels que *Développement*, *Mise en lots* et *Production*, sont une notion de premier plan dans ASP.NET Core. Vous pouvez spécifier l’environnement d’exécution d’une application en définissant la variable d’environnement `ASPNETCORE_ENVIRONMENT`. ASP.NET Core lit la variable d’environnement au démarrage de l’application et stocke la valeur dans une implémentation `IHostingEnvironment`. L’objet d’environnement est disponible partout dans l’application par le biais de l’injection de dépendances.

L’exemple de code suivant de la classe `Startup` configure l’application pour fournir des informations d’erreur détaillées uniquement lorsqu’elle s’exécute en développement :

[!code-csharp[](index/samples_snapshot/2.x/StartupConfigure.cs?highlight=3-6)]

Pour plus d’informations, consultez <xref:fundamentals/environments>.

## <a name="logging"></a>Journalisation

ASP.NET Core prend en charge une API de journalisation qui fonctionne avec un large éventail de fournisseurs de journalisation intégrés et tiers. Les fournisseurs disponibles sont les suivants :

* Console
* Débogage
* Suivi des événements sur Windows
* Journal des événements Windows
* TraceSource
* Azure App Service
* Azure Application Insights

Écrivez des journaux à partir de n’importe quel emplacement dans le code d’une application en obtenant un objet `ILogger` à partir de l’injection de dépendances et en appelant les méthodes de journal.

Voici un exemple de code qui utilise un objet `ILogger`, avec l’injection de constructeur et les appels de méthode de journalisation mis en surbrillance.

[!code-csharp[](index/samples_snapshot/2.x/TodoController.cs?highlight=5,13,17)]

L’interface `ILogger` vous permet de passer un certain nombre de champs au fournisseur de journalisation. Les champs sont couramment utilisés pour construire une chaîne de message, mais le fournisseur peut également les envoyer en tant que champs distincts dans un magasin de données. Cette fonctionnalité permet aux fournisseurs de journalisation d’implémenter la [journalisation sémantique, également appelée journalisation structurée](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

Pour plus d’informations, consultez <xref:fundamentals/logging/index>.

## <a name="routing"></a>Routage

Un *itinéraire* est un modèle d’URL qui est mappé à un gestionnaire. Le gestionnaire est généralement une page Razor, une méthode d’action dans un contrôleur MVC, ou un intergiciel (middleware). Le routage ASP.NET Core vous permet de contrôler les URL utilisées par votre application.

Pour plus d’informations, consultez <xref:fundamentals/routing>.

## <a name="error-handling"></a>Gestion des erreurs

ASP.NET Core offre des fonctionnalités intégrées pour gérer des erreurs, telles que :

* Une page d’exceptions du développeur
* Pages d’erreur personnalisées
* Pages de codes d’état statique
* Gestion des exceptions de démarrage

Pour plus d’informations, consultez <xref:fundamentals/error-handling>.

## <a name="make-http-requests"></a>Effectuer des requêtes HTTP

Une implémentation de `IHttpClientFactory` est disponible pour la création d’instances `HttpClient`. La fabrique :

* Fournit un emplacement central pour le nommage et la configuration d’instance de `HttpClient` logiques. Par exemple, un client *github* peut être enregistré et configuré pour accéder à GitHub. Un client par défaut peut être inscrit à d’autres fins.
* Prend en charge l’inscription et le chaînage de plusieurs gestionnaires de délégation pour créer un pipeline de middlewares pour les requêtes sortantes. Ce modèle est similaire au pipeline de middlewares entrants dans ASP.NET Core. Le modèle fournit un mécanisme permettant de gérer les problèmes transversaux liés aux des requêtes HTTP, notamment la mise en cache, la gestion des erreurs, la sérialisation et la journalisation.
* S’intègre à *Polly*, une bibliothèque tierce populaire pour la gestion des erreurs temporaires.
* Gère le regroupement et la durée de vie des instances de `HttpClientHandler` sous-jacentes pour éviter les problèmes DNS courants qui se produisent lors de la gestion manuelle des durées de vie de `HttpClient`.
* Ajoute une expérience de journalisation configurable (via `ILogger`) pour toutes les requêtes envoyées via des clients créés par la fabrique.

Pour plus d’informations, consultez <xref:fundamentals/http-requests>.

## <a name="content-root"></a>Racine de contenu

La racine de contenu est le chemin de base vers le:

* Hébergement exécutable de l’application (*.exe*).
* Assemblages compilés qui composent l’application (*.dll*).
* Fichiers de contenu non codés utilisés par l’application, tels que :
  * Fichiers de rasoir (*.cshtml*, *.razor*)
  * Fichiers de configuration (*.json*, *.xml*)
  * Fichiers de données (*.db*)
* [Racine Web](#web-root), généralement le dossier *wwwroot* publié.

Pendant le développement :

* La racine de contenu par défaut à l’annuaire racine du projet.
* Le répertoire racine du projet est utilisé pour créer le :
  * Chemin vers les fichiers de contenu non-code de l’application dans l’annuaire racine du projet.
  * [Racine Web](#web-root), généralement le dossier *wwwroot* dans le répertoire racine du projet.

Un autre chemin de racine de contenu peut être spécifié lors [de la construction de l’hôte](#host). Pour plus d’informations, consultez <xref:fundamentals/host/web-host#content-root>.

## <a name="web-root"></a>Racine web

La racine du Web est le chemin de base vers des fichiers de ressources statiques publics, non-code, tels que :

* Feuilles de style (*.css*)
* JavaScript (*.js*)
* Images (*.png*, *.jpg*)

Les fichiers statiques ne sont servis que par défaut à partir de l’annuaire racine web (et sous-répertoires).

Le chemin de racine web par défaut à *'contenu racine'/wwwroot*, mais une racine web différente peut être spécifiée lors [de la construction de l’hôte](#host). Pour plus d’informations, consultez [Racine web](xref:fundamentals/host/web-host#web-root).

Empêchez de publier des fichiers dans *wwwroot* avec [ \<l’élément de projet Content>](/visualstudio/msbuild/common-msbuild-project-items#content) dans le fichier du projet. L’exemple suivant empêche la publication de contenu dans l’annuaire et les sous-annuaires *wwwroot/local* :

```xml
<ItemGroup>
  <Content Update="wwwroot\local\**\*.*" CopyToPublishDirectory="Never" />
</ItemGroup>
```

Dans les fichiers Razor (*.cshtml),*`~/`le tilde-slash ( ) pointe vers la racine web. Un chemin `~/` commençant par est appelé un *chemin virtuel*.

Pour plus d’informations, consultez <xref:fundamentals/static-files>.

::: moniker-end
