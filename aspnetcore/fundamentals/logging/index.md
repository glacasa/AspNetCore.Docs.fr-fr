---
title: Journalisation dans .NET Core et ASP.NET Core
author: rick-anderson
description: Découvrez comment utiliser le framework de journalisation fourni par le package NuGet Microsoft.Extensions.Logging.
ms.author: riande
ms.custom: mvc
ms.date: 6/29/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/logging/index
ms.openlocfilehash: a4962c3132c60b68cb2d4b5a97e9b082aba15d15
ms.sourcegitcommit: 50e7c970f327dbe92d45eaf4c21caa001c9106d0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/10/2020
ms.locfileid: "87330733"
---
# <a name="logging-in-net-core-and-aspnet-core"></a>Journalisation dans .NET Core et ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Par [Kirk Larkin](https://twitter.com/serpent5), [Juergen Gutsch](https://github.com/JuergenGutsch) et [Rick Anderson](https://twitter.com/RickAndMSFT)

.NET Core prend en charge une API de journalisation qui fonctionne avec différents fournisseurs de journalisation tiers et intégrés. Cet article explique comment utiliser cette API de journalisation avec les fournisseurs de journalisation intégrés.

La plupart des exemples de code présentés dans cet article proviennent d’applications ASP.NET Core. Les parties spécifiques à la journalisation de ces extraits de code s’appliquent à n’importe quelle application .NET Core qui utilise l' [hôte générique](xref:fundamentals/host/generic-host). Les modèles d’application Web ASP.NET Core utilisent l’hôte générique.

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples/3.x) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

<a name="lp"></a>

## <a name="logging-providers"></a>Fournisseurs de journalisation

Les fournisseurs de journalisation stockent les journaux, à l’exception du `Console` fournisseur qui affiche les journaux. Par exemple, le fournisseur Azure Application Insights stocke les journaux dans Azure Application Insights. Plusieurs fournisseurs peuvent être activés.

Les modèles d’application Web ASP.NET Core par défaut :

* Utilisez l' [hôte générique](xref:fundamentals/host/generic-host).
* Appelez <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> , qui ajoute les fournisseurs de journalisation suivants :
  * [Console](#console)
  * [Déboguer](#debug)
  * [EventSource](#event-source)
  * [EventLog](#welog): Windows uniquement

[!code-csharp[](index/samples/3.x/TodoApiDTO/Program.cs?name=snippet_TemplateCode&highlight=9)]

Le code précédent montre la `Program` classe créée avec les modèles d’application web ASP.net core. Les sections suivantes fournissent des exemples basés sur les modèles d’application Web ASP.NET Core, qui utilisent l’hôte générique. Les [applications de console non-hôte](#nhca) sont présentées plus loin dans ce document.

Pour remplacer l’ensemble par défaut des fournisseurs de journalisation ajoutés par `Host.CreateDefaultBuilder` , appelez `ClearProviders` et ajoutez les fournisseurs de journalisation requis. Par exemple, le code suivant :

* Appelle <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> pour supprimer toutes les <xref:Microsoft.Extensions.Logging.ILoggerProvider> instances du générateur.
* Ajoute le fournisseur de journalisation de la [console](#console) .

[!code-csharp[](index/samples/3.x/TodoApiDTO/Program.cs?name=snippet_AddProvider&highlight=5-6)]

Pour obtenir des fournisseurs supplémentaires, consultez :

* [Fournisseurs de journalisation intégrés](#bilp)
* [Fournisseurs de journalisation tiers](#third-party-logging-providers).

## <a name="create-logs"></a>Créer des journaux

Pour créer des journaux, utilisez un <xref:Microsoft.Extensions.Logging.ILogger%601> objet à partir de l' [injection de dépendances](xref:fundamentals/dependency-injection) (di).

L’exemple suivant :

* Crée un enregistreur d’événements, `ILogger<AboutModel>` , qui utilise une *catégorie* de journal du nom qualifié complet du type `AboutModel` . La catégorie du journal est une chaîne associée à chaque journal.
* Appels <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> à consigner au `Information` niveau. Le *niveau* du journal indique la gravité de l’événement consigné.

[!code-csharp[](index/samples/3.x/TodoApiDTO/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=5,14)]

Les [niveaux](#log-level) et les [catégories](#log-category) sont expliqués plus en détail plus loin dans ce document.

Pour plus d’informations sur Blazor , consultez [créer des journaux dans Blazor et Blazor WebAssembly ](#clib) dans ce document.

[Créer des journaux dans main et Startup](#clms) montre comment créer des journaux dans `Main` et `Startup` .

## <a name="configure-logging"></a>Configuration de la journalisation

La configuration de la journalisation est généralement fournie par la `Logging` section de *appSettings*. `{Environment}` fichiers *. JSON* . Le *appsettings.Development.jssuivant sur* le fichier est généré par les modèles d’application Web ASP.net Core :

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.Development.json)]

Dans le code JSON précédent :

* Les `"Default"` `"Microsoft"` catégories, et `"Microsoft.Hosting.Lifetime"` sont spécifiées.
* La `"Microsoft"` catégorie s’applique à toutes les catégories qui commencent par `"Microsoft"` . Par exemple, ce paramètre s’applique à la `"Microsoft.AspNetCore.Routing.EndpointMiddleware"` catégorie.
* La `"Microsoft"` catégorie enregistre les journaux au niveau du journal et au niveau `Warning` supérieur.
* La catégorie `"Microsoft.Hosting.Lifetime"` est plus spécifique que la catégorie `"Microsoft"` , de sorte que la `"Microsoft.Hosting.Lifetime"` catégorie enregistre les journaux au niveau du journal « information » et supérieur.
* Un module fournisseur d’informations spécifique n’étant pas spécifié, `LogLevel` s’applique à tous les fournisseurs de journalisation activés, à l’exception du journal des [événements Windows](#welog).

La `Logging` propriété peut avoir <xref:Microsoft.Extensions.Logging.LogLevel> et enregistrer les propriétés du fournisseur. `LogLevel`Spécifie le [niveau](#log-level) minimal à enregistrer pour les catégories sélectionnées. Dans le code JSON précédent, les `Information` `Warning` niveaux de journalisation sont spécifiés. `LogLevel`indique la gravité du journal et les plages de 0 à 6 :

`Trace`= 0, `Debug` = 1, `Information` = 2, `Warning` = 3, `Error` = 4, `Critical` = 5 et `None` = 6.

Lorsqu’un `LogLevel` est spécifié, la journalisation est activée pour les messages au niveau spécifié et supérieur. Dans le code JSON précédent, la `Default` catégorie est journalisée pour `Information` et les versions ultérieures. Par exemple, les messages,, `Information` `Warning` `Error` et `Critical` sont journalisés. Si aucun `LogLevel` n’est spécifié, la journalisation est définie par défaut sur le `Information` niveau. Pour plus d’informations, consultez [niveaux de journalisation](#llvl).

Une propriété de fournisseur peut spécifier une `LogLevel` propriété. `LogLevel`sous un fournisseur spécifie les niveaux de journalisation pour ce fournisseur, et remplace les paramètres du journal non fournisseur. Prenez en compte les *appsettings.jssuivantes sur* le fichier :

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.Prod2.json)]

Paramètres dans les `Logging.{providername}.LogLevel` paramètres de remplacement dans `Logging.LogLevel` . Dans le code JSON précédent, le `Debug` niveau de journalisation par défaut du fournisseur est défini sur `Information` :

`Logging:Debug:LogLevel:Default:Information`

Le paramètre précédent spécifie le `Information` niveau de journalisation pour chaque `Logging:Debug:` catégorie, à l’exception de `Microsoft.Hosting` . Quand une catégorie spécifique est indiquée, la catégorie spécifique remplace la catégorie par défaut. Dans le JSON précédent, les `Logging:Debug:LogLevel` catégories `"Microsoft.Hosting"` et `"Default"` remplacent les paramètres dans`Logging:LogLevel`

Le niveau de journal minimal peut être spécifié pour l’un des éléments suivants :

* Fournisseurs spécifiques : par exemple,`Logging:EventSource:LogLevel:Default:Information`
* Catégories spécifiques : par exemple,`Logging:LogLevel:Microsoft:Warning`
* Tous les fournisseurs et toutes les catégories :`Logging:LogLevel:Default:Warning`

Les journaux situés en dessous du niveau minimal ne sont ***pas***:

* Passé au fournisseur.
* Consigné ou affiché.

Pour supprimer tous les journaux, spécifiez [LogLevel. None](xref:Microsoft.Extensions.Logging.LogLevel). `LogLevel.None`a une valeur de 6, qui est supérieure à `LogLevel.Critical` (5).

Si un fournisseur prend en charge les [étendues de journal](#logscopes), `IncludeScopes` indique s’ils sont activés. Pour plus d’informations, consultez [étendues de journaux](#logscopes)

Le *appsettings.jssuivant sur* le fichier contient tous les fournisseurs activés par défaut :

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.Production.json)]

Dans l’exemple précédent :

* Les catégories et les niveaux ne sont pas des valeurs suggérées. L’exemple est fourni pour afficher tous les fournisseurs par défaut.
* Paramètres dans les `Logging.{providername}.LogLevel` paramètres de remplacement dans `Logging.LogLevel` . Par exemple, le niveau dans `Debug.LogLevel.Default` remplace le niveau dans `LogLevel.Default` .
* Chaque *alias* de fournisseur par défaut est utilisé. Chaque fournisseur définit un *alias* qui peut être utilisé dans la configuration à la place du nom de type complet. Les alias des fournisseurs intégrés sont les suivants :
  * Console
  * Débogage
  * EventSource
  * EventLog
  * AzureAppServicesFile
  * AzureAppServicesBlob
  * ApplicationInsights

## <a name="set-log-level-by-command-line-environment-variables-and-other-configuration"></a>Définir le niveau de journalisation à l’aide d’une ligne de commande, de variables d’environnement et d’autres configurations

Le niveau de journal peut être défini par l’un des [fournisseurs de configuration](xref:fundamentals/configuration/index). 

[!INCLUDE[](~/includes/environmentVarableColon.md)]

Les commandes suivantes :

* Affectez à la clé d’environnement la `Logging:LogLevel:Microsoft` valeur `Information` sur Windows.
* Testez les paramètres lors de l’utilisation d’une application créée avec les modèles d’application Web ASP.NET Core. La `dotnet run` commande doit être exécutée dans le répertoire du projet après l’utilisation de `set` .

```cmd
set Logging__LogLevel__Microsoft=Information
dotnet run
```

Le paramètre d’environnement précédent :

* Est défini uniquement dans les processus lancés à partir de la fenêtre de commande dans laquelle ils ont été définis.
* N’est pas lu par les navigateurs lancés avec Visual Studio.

La commande [setx](/windows-server/administration/windows-commands/setx) suivante définit également la clé et la valeur de l’environnement sur Windows. Contrairement à `set` , `setx` les paramètres sont conservés. Le `/M` commutateur définit la variable dans l’environnement système. Si `/M` n’est pas utilisé, une variable d’environnement utilisateur est définie.

```cmd
setx Logging__LogLevel__Microsoft=Information /M
```

Dans [Azure App service](https://azure.microsoft.com/services/app-service/), sélectionnez **nouveau paramètre d’application** dans la page **paramètres > configuration** . Azure App Service paramètres de l’application sont les suivants :

* Chiffré au repos et transmis sur un canal chiffré.
* Exposés en tant que variables d’environnement.

Pour plus d’informations, consultez [Azure Apps : remplacer la configuration de l’application à l’aide du portail Azure](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).

Pour plus d’informations sur la définition de valeurs de configuration ASP.NET Core à l’aide de variables d’environnement, consultez [variables d’environnement](xref:fundamentals/configuration/index#environment-variables). Pour plus d’informations sur l’utilisation d’autres sources de configuration, notamment la ligne de commande, Azure Key Vault, la configuration de Azure App, d’autres formats de fichier, etc., consultez <xref:fundamentals/configuration/index> .

## <a name="how-filtering-rules-are-applied"></a>Mode d’application des règles de filtre

À la création d’un objet <xref:Microsoft.Extensions.Logging.ILogger%601>, l’objet <xref:Microsoft.Extensions.Logging.ILoggerFactory> sélectionne une seule règle à appliquer à cet enregistrement d’événements par fournisseur. Tous les messages écrits par une instance `ILogger` sont filtrés selon les règles sélectionnées. La règle la plus spécifique pour chaque paire fournisseur et catégorie est sélectionnée parmi les règles disponibles.

L’algorithme suivant est utilisé pour chaque fournisseur quand un objet `ILogger` est créé pour une catégorie donnée :

* Sélectionnez toutes les règles qui correspondent au fournisseur ou à son alias. Si aucune correspondance n’est trouvée, sélectionnez toutes les règles avec un fournisseur vide.
* À partir du résultat de l’étape précédente, sélectionnez les règles ayant le plus long préfixe de catégorie correspondant. Si aucune correspondance n’est trouvée, sélectionnez toutes les règles qui ne spécifient pas de catégorie.
* Si plusieurs règles sont sélectionnées, prenez la **dernière**.
* Si aucune règle n’est sélectionnée, utilisez `MinimumLevel`.

<a name="dnrvs"></a>

## <a name="logging-output-from-dotnet-run-and-visual-studio"></a>Journalisation de la sortie de dotnet Run et de Visual Studio

Les journaux créés avec les [fournisseurs de journalisation par défaut](#lp) sont affichés :

* Dans Visual Studio
  * Dans la fenêtre sortie de débogage lors du débogage.
  * Dans la fenêtre serveur Web ASP.NET Core.
* Dans la fenêtre de console lorsque l’application est exécutée `dotnet run` .

Les journaux qui commencent par des catégories « Microsoft » proviennent du code ASP.NET Core Framework. Les ASP.NET Core et le code d’application utilisent les mêmes API de journalisation et fournisseurs.

<a name="lcat"></a>

## <a name="log-category"></a>Catégorie de journal

Lorsqu’un `ILogger` objet est créé, une *catégorie* est spécifiée. Cette catégorie est incluse dans tous les messages de journal créés par cette instance de `ILogger`. La chaîne de catégorie est arbitraire, mais la Convention consiste à utiliser le nom de la classe. Par exemple, dans un contrôleur, le nom peut être `"TodoApi.Controllers.TodoController"` . Les applications Web ASP.NET Core utilisent `ILogger<T>` pour recevoir automatiquement une `ILogger` instance qui utilise le nom de type qualifié complet de `T` comme catégorie :

[!code-csharp[](index/samples/3.x/TodoApiDTO/Pages/Privacy.cshtml.cs?name=snippet)]

Pour spécifier explicitement la catégorie, appelez `ILoggerFactory.CreateLogger` :

[!code-csharp[](index/samples/3.x/TodoApiDTO/Pages/Contact.cshtml.cs?name=snippet)]

`ILogger<T>` équivaut à appeler `CreateLogger` avec le nom de type complet `T`.

<a name="llvl"></a>

## <a name="log-level"></a>Log level

Le tableau suivant répertorie les <xref:Microsoft.Extensions.Logging.LogLevel> valeurs, la `Log{LogLevel}` méthode d’extension de commodité et l’utilisation suggérée :

| LogLevel | Valeur | Méthode | Description |
| -------- | ----- | ------ | ----------- |
| [Trace](xref:Microsoft.Extensions.Logging.LogLevel) | 0 | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogTrace%2A> | Contiennent les messages les plus détaillés. Ces messages peuvent contenir des données d’application sensibles. Ces messages sont désactivés par défaut et ***ne doivent pas*** être activés en production. |
| [Déboguer](xref:Microsoft.Extensions.Logging.LogLevel) | 1 | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug%2A> | Pour le débogage et le développement. Utilisez avec précaution en production en raison du volume élevé. |
| [Informations](xref:Microsoft.Extensions.Logging.LogLevel) | 2 | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation%2A> | Effectue le suivi du déroulement général de l’application. Peut avoir une valeur à long terme. |
| [Avertissement](xref:Microsoft.Extensions.Logging.LogLevel) | 3 | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> | Pour les événements anormaux ou inattendus. Comprend généralement des erreurs ou des conditions qui ne provoquent pas l’échec de l’application. |
| [Error](xref:Microsoft.Extensions.Logging.LogLevel) | 4 | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A> | Fournit des informations sur des erreurs et des exceptions qui ne peuvent pas être gérées. Ces messages indiquent un échec dans l’opération ou la demande en cours, et non dans l’ensemble de l’application. |
| [Critical](xref:Microsoft.Extensions.Logging.LogLevel) | 5 | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogCritical%2A> | Fournit des informations sur des échecs qui nécessitent un examen immédiat. Exemples : perte de données, espace disque insuffisant. |
| [Aucun](xref:Microsoft.Extensions.Logging.LogLevel) | 6 | | Spécifie qu’une catégorie de journalisation ne doit écrire aucun message. |

Dans le tableau précédent, la `LogLevel` est indiquée du niveau de gravité le plus bas au plus élevé.

Le premier paramètre de la méthode de [journalisation](xref:Microsoft.Extensions.Logging.LoggerExtensions) , <xref:Microsoft.Extensions.Logging.LogLevel> , indique la gravité du journal. Au lieu d’appeler `Log(LogLevel, ...)` , la plupart des développeurs appellent les méthodes d’extension [log {LogLevel}](xref:Microsoft.Extensions.Logging.LoggerExtensions) . Les `Log{LogLevel}` méthodes d’extension [appellent la méthode log et spécifient LogLevel](https://github.com/dotnet/extensions/blob/release/3.1/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs). Par exemple, les deux appels de journalisation suivants sont fonctionnellement équivalents et produisent le même journal :

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TestController.cs?name=snippet0&highlight=6-7)]

`MyLogEvents.TestItem`est l’ID de l’événement. `MyLogEvents`fait partie de l’exemple d’application et s’affiche dans la section [enregistrer l’ID d’événement](#leid) .

[!INCLUDE[](~/includes/MyDisplayRouteInfoBoth.md)]

Le code suivant crée des journaux `Information` et `Warning` :

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet_CallLogMethods&highlight=4,10)]

Dans le code précédent, le premier `Log{LogLevel}` paramètre, `MyLogEvents.GetItem` , est l' [ID d’événement du journal](#leid). Le deuxième paramètre est un modèle de message contenant des espaces réservés pour les valeurs d’argument fournies par les autres paramètres de méthode. Les paramètres de méthode sont expliqués dans la section [modèle de message](#lmt) , plus loin dans ce document.

Appelez la `Log{LogLevel}` méthode appropriée pour contrôler la quantité de sortie de journal écrite sur un support de stockage particulier. Par exemple :

* En production :
  * La journalisation au `Trace` niveau des ou de `Information` génère un volume important de messages journaux détaillés. Pour contrôler les coûts et ne pas dépasser les limites de stockage des données, `Trace` `Information` les messages de journal et de niveau dans un magasin de données volumineux et à faible coût. Envisagez `Trace` de limiter et `Information` à des catégories spécifiques.
  * La journalisation au `Warning` `Critical` niveau des niveaux doit produire peu de messages de journal.
    * Les coûts et les limites de stockage ne sont généralement pas un problème.
    * Certains journaux offrent davantage de flexibilité dans les choix de magasins de données.
* En cours de développement :
  * Défini sur `Warning`.
  * Ajoutez `Trace` `Information` des messages ou lors de la résolution des problèmes. Pour limiter la sortie, `Trace` la définition ou `Information` uniquement pour les catégories en cours d’investigation.

ASP.NET Core écrit des journaux pour les événements de framework. Par exemple, considérez la sortie du journal pour :

* RazorApplication pages créée avec les modèles ASP.net core.
* Journalisation défini sur`Logging:Console:LogLevel:Microsoft:Information`
* Navigation vers la page confidentialité :

```console
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 GET https://localhost:5001/Privacy
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint '/Privacy'
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[3]
      Route matched with {page = "/Privacy"}. Executing page /Privacy
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[101]
      Executing handler method DefaultRP.Pages.PrivacyModel.OnGet - ModelState is Valid
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[102]
      Executed handler method OnGet, returned result .
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[103]
      Executing an implicit handler method - ModelState is Valid
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[104]
      Executed an implicit handler method, returned result Microsoft.AspNetCore.Mvc.RazorPages.PageResult.
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[4]
      Executed page /Privacy in 74.5188ms
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint '/Privacy'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 149.3023ms 200 text/html; charset=utf-8
```

Le code JSON suivant définit `Logging:Console:LogLevel:Microsoft:Information` :

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.MSFT.json)]

<a name="leid"></a>

## <a name="log-event-id"></a>ID d’événement de log

Chaque journal peut spécifier un *ID d’événement*. L’exemple d’application utilise la `MyLogEvents` classe pour définir les ID d’événement :

<!-- Review: to bad there is no way to use an enum for event ID's -->
[!code-csharp[](index/samples/3.x/TodoApiDTO/Models/MyLogEvents.cs?name=snippet_LoggingEvents)]

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet_CallLogMethods&highlight=4,10)]

Un ID d’événement associe un jeu d’événements. Par exemple, tous les journaux liés à l’affichage d’une liste d’éléments sur une page peuvent être 1001.

Le fournisseur de journalisation peut stocker l’ID d’événement dans un champ ID, dans le message de journalisation, ou pas du tout. Le fournisseur Debug n’affiche pas les ID d’événements. Le fournisseur Console affiche les ID d’événements entre crochets après la catégorie :

```console
info: TodoApi.Controllers.TodoItemsController[1002]
      Getting item 1
warn: TodoApi.Controllers.TodoItemsController[4000]
      Get(1) NOT FOUND
```

Certains fournisseurs de journalisation stockent l’ID d’événement dans un champ, ce qui permet de filtrer l’ID.

<a name="lmt"></a>

## <a name="log-message-template"></a>Modèle de message de journal
<!-- Review, Each log API uses a message template. -->
Chaque API de journal utilise un modèle de message. Ce dernier peut contenir des espaces réservés pour lesquels les arguments sont fournis. Utilisez des noms et non des nombres pour les espaces réservés.

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet_CallLogMethods&highlight=4,10)]

L’ordre des espaces réservés, pas leurs noms, détermine quels paramètres sont utilisés pour fournir leurs valeurs. Dans le code suivant, les noms de paramètres sont hors séquence dans le modèle de message :

```csharp
string p1 = "param1";
string p2 = "param2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

Le code précédent crée un message de journal avec les valeurs de paramètre dans l’ordre :

```text
Parameter values: param1, param2
```

Cette approche permet aux fournisseurs de journalisation d’implémenter la [journalisation sémantique ou structurée](https://github.com/NLog/NLog/wiki/How-to-use-structured-logging). Les arguments proprement dits, et pas seulement le modèle de message mis en forme, sont transmis au système de journalisation. Cela permet aux fournisseurs de journalisation de stocker les valeurs de paramètres en tant que champs. Par exemple, considérez la méthode d’enregistreur d’événements suivante :

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

Par exemple, lors de la journalisation dans le stockage table Azure :

* Chaque entité de table Azure peut avoir des `ID` `RequestTime` Propriétés et.
* Les tables avec des propriétés simplifient les requêtes sur les données journalisées. Par exemple, une requête peut trouver tous les journaux d’une `RequestTime` plage donnée sans avoir à analyser le délai d’attente du message texte.

## <a name="log-exceptions"></a>Journaliser les exceptions

Les méthodes d’enregistreur d’événements ont des surcharges qui prennent un paramètre d’exception :

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TestController.cs?name=snippet_Exp)]

[!INCLUDE[](~/includes/MyDisplayRouteInfoBoth.md)]

La journalisation des exceptions est spécifique au fournisseur.

### <a name="default-log-level"></a>Niveau de journalisation par défaut

Si le niveau de journalisation par défaut n’est pas défini, la valeur par défaut du niveau de journal est `Information` .

Par exemple, considérez l’application Web suivante :

* Créé avec les modèles d’application Web ASP.NET.
* *appsettings.jssur* et *appsettings.Development.jssur* supprimé ou renommé.

Avec la configuration précédente, la navigation vers la page confidentialité ou d’origine génère de nombreux `Trace` `Debug` messages, et `Information` avec `Microsoft` dans le nom de catégorie.

Le code suivant définit le niveau de journalisation par défaut lorsque le niveau de journalisation par défaut n’est pas défini dans la configuration :

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_MinLevel&highlight=10)]

<!-- review required: I say this a couple times -->
En règle générale, les niveaux de journalisation doivent être spécifiés dans la configuration et non dans le code.

### <a name="filter-function"></a>fonction Filter

Une fonction de filtre est appelée pour tous les fournisseurs et toutes les catégories pour lesquels aucune règle n’est assignée par la configuration ou le code :

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_FilterFunction)]

Le code précédent affiche les journaux de console lorsque la catégorie contient `Controller` ou `Microsoft` et que le niveau de journal est `Information` ou supérieur.

En règle générale, les niveaux de journalisation doivent être spécifiés dans la configuration et non dans le code.

## <a name="aspnet-core-and-ef-core-categories"></a>Catégories ASP.NET Core et EF Core

Le tableau suivant contient des catégories utilisées par ASP.NET Core et Entity Framework Core, avec des remarques sur les journaux :

| Category                            | Notes |
| ----------------------------------- | ----- |
| Microsoft.AspNetCore                | Diagnostics ASP.NET Core généraux. |
| Microsoft.AspNetCore.DataProtection | Liste des clés considérées, trouvées et utilisées. |
| Microsoft.AspNetCore.HostFiltering  | Hôtes autorisés. |
| Microsoft.AspNetCore.Hosting        | Temps de traitement des requêtes HTTP et heure de démarrage. Liste des assemblys de démarrage d’hébergement chargés. |
| Microsoft.AspNetCore.Mvc            | MVC et Razor Diagnostics. Liaison de données, exécution de filtres, compilation de vues, sélection d’actions. |
| Microsoft.AspNetCore.Routing        | Informations de correspondance des itinéraires. |
| Microsoft.AspNetCore.Server         | Démarrage et arrêt de la connexion, et réponses persistantes. Informations du certificat HTTPS. |
| Microsoft.AspNetCore.StaticFiles    | Fichiers pris en charge. |
| Microsoft.EntityFrameworkCore       | Diagnostics Entity Framework Core généraux. Activité et configuration de la base de données, détection des modifications, migrations. |

Pour afficher plus de catégories dans la fenêtre de la console, définissez **appsettings.Development.jssur** ce qui suit :

[!code-json[](index/samples/3.x/MyMain/appsettings.Trace.json)]

<!-- Review: What other providers support scopes? Console is not generally used in staging/production  -->

<a name="logscopes"></a>

## <a name="log-scopes"></a>Étendues de journal

 Une *étendue* peut regrouper un ensemble d’opérations logiques. Ce regroupement permet de joindre les mêmes données à tous les journaux créés au sein d’un ensemble. Par exemple, chaque journal créé dans le cadre du traitement d’une transaction peut contenir l’ID de la transaction.

Une étendue :

* Est un <xref:System.IDisposable> type qui est retourné par la <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> méthode.
* Dure jusqu’à ce qu’il soit supprimé.

Les fournisseurs suivants prennent en charge les étendues :

* `Console`
* [AzureAppServicesFile et AzureAppServicesBlob](xref:Microsoft.Extensions.Logging.AzureAppServices.BatchingLoggerOptions.IncludeScopes)

Utilisez une étendue en incluant les appels de l’enregistrement d’événements dans un bloc `using` :

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TestController.cs?name=snippet_Scopes)]

Le code JSON suivant active des étendues pour le fournisseur de console :

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.Scopes.json)]

Le code suivant active les étendues pour le fournisseur Console :

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_Scopes)]

En règle générale, la journalisation doit être spécifiée dans la configuration et non dans le code.

<a name="bilp"></a>

## <a name="built-in-logging-providers"></a>Fournisseurs de journalisation intégrés

ASP.NET Core comprend les fournisseurs de journalisation suivants :

* [Console](#console)
* [Déboguer](#debug)
* [EventSource](#event-source)
* [EventLog](#welog)
* [AzureAppServicesFile et AzureAppServicesBlob](#azure-app-service)
* [ApplicationInsights](#azure-application-insights)

Pour plus d’informations sur `stdout` et le débogage de la journalisation avec le Module ASP.net Core, consultez <xref:test/troubleshoot-azure-iis> et <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection> .

### <a name="console"></a>Console

Le `Console` fournisseur journalise la sortie dans la console. Pour plus d’informations sur `Console` l’affichage des journaux dans le développement, consultez [journalisation de la sortie de dotnet Run et de Visual Studio](#dnrvs).

### <a name="debug"></a>Débogage

Le `Debug` fournisseur écrit la sortie du journal à l’aide de la classe [System. Diagnostics. Debug](/dotnet/api/system.diagnostics.debug) . Appelle pour `System.Diagnostics.Debug.WriteLine` écrire dans le `Debug` fournisseur.

Sur Linux, l' `Debug` emplacement du journal du fournisseur est dépendant de la distribution et peut être l’un des suivants :

* */var/log/message*
* */var/log/syslog*

### <a name="event-source"></a>Source de l’événement

Le `EventSource` fournisseur écrit dans une source d’événements multiplateforme portant le nom `Microsoft-Extensions-Logging` . Sur Windows, le fournisseur utilise [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).

#### <a name="dotnet-trace-tooling"></a>outils de trace dotnet

L’outil [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) est un outil global de l’interface CLI multiplateforme qui permet la collecte des traces .net core d’un processus en cours d’exécution. L’outil collecte les <xref:Microsoft.Extensions.Logging.EventSource> données du fournisseur à l’aide d’un <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource> .

Consultez [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) pour obtenir des instructions d’installation.

Utilisez les outils de trace dotnet pour collecter une trace à partir d’une application :

1. Exécutez l’application avec la `dotnet run` commande.
1. Déterminez l’identificateur de processus (PID) de l’application .NET Core :
   * Sur Windows, utilisez l’une des approches suivantes :
     * Gestionnaire des tâches (Ctrl + Alt + Suppr)
     * [TaskList, commande](/windows-server/administration/windows-commands/tasklist)
     * [Commande PowerShell d’extraction de processus](/powershell/module/microsoft.powershell.management/get-process)
   * Sur Linux, utilisez la [commande pidof](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html).

   Recherchez le PID pour le processus qui porte le même nom que l’assembly de l’application.

1. Exécutez la `dotnet trace` commande.

   Syntaxe de la commande générale :

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"
   ```

   Quand vous utilisez une interface de commande PowerShell, placez la `--providers` valeur entre guillemets simples ( `'` ) :

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers 'Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"'
   ```

   Sur les plateformes non-Windows, ajoutez l' `-f speedscope` option permettant de modifier le format du fichier de trace de sortie en `speedscope` .

   | Mot clé | Description |
   | :-----: | ----------- |
   | 1       | Consignez les événements méta sur le `LoggingEventSource` . N’enregistre pas les événements à partir de `ILogger` ). |
   | 2       | Active l' `Message` événement lorsque `ILogger.Log()` est appelé. Fournit des informations d’une façon de programmation (non mise en forme). |
   | 4       | Active l' `FormatMessage` événement lorsque `ILogger.Log()` est appelé. Fournit la version de chaîne mise en forme des informations. |
   | 8       | Active l' `MessageJson` événement lorsque `ILogger.Log()` est appelé. Fournit une représentation JSON des arguments. |

   | Niveau d'événement | Description     |
   | :---------: | --------------- |
   | 0           | `LogAlways`     |
   | 1           | `Critical`      |
   | 2           | `Error`         |
   | 3           | `Warning`       |
   | 4           | `Informational` |
   | 5           | `Verbose`       |

   `FilterSpecs`les entrées pour `{Logger Category}` et `{Event Level}` représentent des conditions de filtrage de journal supplémentaires. Séparez `FilterSpecs` les entrées par un point-virgule ( `;` ).

   Exemple utilisant un interpréteur de commandes Windows (**sans** guillemets simples autour de la `--providers` valeur) :

   ```dotnetcli
   dotnet trace collect -p {PID} --providers Microsoft-Extensions-Logging:4:2:FilterSpecs=\"Microsoft.AspNetCore.Hosting*:4\"
   ```

   La commande précédente active :

   * Enregistreur d’événements de la source d’événements pour produire des chaînes mises en forme ( `4` ) pour les erreurs ( `2` ).
   * `Microsoft.AspNetCore.Hosting`journalisation au `Informational` niveau de la journalisation ( `4` ).

1. Arrêtez les outils de trace dotnet en appuyant sur la touche entrée ou Ctrl + C.

   La trace est enregistrée avec le nom *trace. NetTrace* dans le dossier où la `dotnet trace` commande est exécutée.

1. Ouvrez la trace avec [Perfview](#perfview). Ouvrez le fichier *trace. NetTrace* et explorez les événements de trace.

Si l’application ne crée pas l’hôte avec `CreateDefaultBuilder` , ajoutez le fournisseur de la [source d’événements](#event-source-provider) à la configuration de journalisation de l’application.

Pour plus d’informations, consultez :

* [Utilitaire trace for Performance Analysis (dotnet-trace)](/dotnet/core/diagnostics/dotnet-trace) (documentation .net Core)
* [Utilitaire trace for Performance Analysis (dotnet-trace) (documentation sur le](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) référentiel GitHub dotnet/Diagnostics)
* [LoggingEventSource, classe](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (Explorateur d’API .net)
* <xref:System.Diagnostics.Tracing.EventLevel>
* [Source de référence LoggingEventSource (3,0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs): pour obtenir la source de référence pour une version différente, remplacez la branche par `release/{Version}` , où `{Version}` est la version de ASP.net Core souhaitée.
* [Perfview](#perfview): utile pour l’affichage des traces de la source d’événements.

#### <a name="perfview"></a>Perfview

Utilisez l' [utilitaire PerfView](https://github.com/Microsoft/perfview) pour collecter et afficher les journaux. Il existe d’autres outils d’affichage des journaux ETW, mais PerfView est l’outil recommandé pour gérer les événements ETW générés par ASP.NET Core.

Pour configurer PerfView afin qu’il collecte les événements enregistrés par ce fournisseur, ajoutez la chaîne `*Microsoft-Extensions-Logging` à la liste des **fournisseurs supplémentaires**. Ne manquez pas le `*` au début de la chaîne.

<a name="welog"></a>

### <a name="windows-eventlog"></a>Journal des événements Windows

Le `EventLog` fournisseur envoie la sortie de journal dans le journal des événements Windows. Contrairement aux autres fournisseurs, le `EventLog` fournisseur n’hérite ***pas*** des paramètres non-fournisseur par défaut. Si `EventLog` les paramètres du journal ne sont pas spécifiés, ils utilisent [par défaut LogLevel. Warning](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L99-L103).

Pour consigner les événements inférieurs à <xref:Microsoft.Extensions.Logging.LogLevel.Warning?displayProperty=nameWithType> , définissez explicitement le niveau de journalisation. L’exemple suivant définit le niveau de journalisation par défaut du journal des événements sur <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType> :

```json
"Logging": {
  "EventLog": {
    "LogLevel": {
      "Default": "Information"
    }
  }
}
```

Les [surcharges AddEventLog](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) peuvent passer <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings> . Si `null` ou n’est pas spécifié, les paramètres par défaut suivants sont utilisés :

* `LogName`: « Application »
* `SourceName`: « .NET Runtime »
* `MachineName`: Le nom de l’ordinateur local est utilisé.

Le code suivant remplace la `SourceName` valeur par défaut de par `".NET Runtime"` `MyLogs` :

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippetEventLog)]

### <a name="azure-app-service"></a>Azure App Service

Le package de fournisseur [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) écrit les enregistrements de journal dans des fichiers texte dans le système de fichiers d’une application Azure App Service, et dans un [stockage Blob](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) dans un compte de stockage Azure.

Le fournisseur de package n’est pas inclus dans le framework partagé. Pour utiliser le fournisseur, ajoutez le package du fournisseur au projet.

Pour configurer les paramètres du fournisseur, utilisez <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> et <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, comme illustré dans l’exemple suivant :

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_AzLogOptions)]

Lors du déploiement sur Azure App Service, l’application utilise les paramètres de la section [app service les journaux](/azure/app-service/web-sites-enable-diagnostic-log/#enable-application-logging-windows) de la page **app service** du portail Azure. Quand les paramètres suivants sont mis à jour, les changements prennent effet immédiatement sans avoir besoin de redémarrer ou redéployer l’application.

* **Journalisation des applications (système de fichiers)**
* **Journalisation des applications (objet blob)**

L’emplacement par défaut des fichiers journaux est le dossier *D:\\racine\\LogFiles\\Application*, et le nom de fichier par défaut est *diagnostics-aaaammjj.txt*. La limite de taille de fichier par défaut est de 10 Mo, et le nombre maximal de fichiers conservés par défaut est de 2. Le nom par défaut du blob est *{app-name}{timestamp}/aaaa/mm/jj/hh/{guid}-applicationLog.txt*.

Ce fournisseur se connecte uniquement lorsque le projet s’exécute dans l’environnement Azure.

#### <a name="azure-log-streaming"></a>Streaming des journaux Azure

Azure log streaming prend en charge l’affichage de l’activité des journaux en temps réel à partir de :

* Serveur d'applications
* Serveur web
* Suivi des demandes ayant échoué

Pour configurer le streaming des journaux Azure :

* Accédez à la page **journaux App service** à partir de la page du portail de l’application.
* Définissez **Journal des applications (Système de fichiers)** sur **Activé**.
* Choisissez le **niveau** du journal. Ce paramètre s’applique uniquement à la diffusion en continu de journaux Azure.

Accédez à la page **flux de journal** pour afficher les journaux. Les messages journalisés sont enregistrés avec l' `ILogger` interface.

### <a name="azure-application-insights"></a>Azure Application Insights

Le package du fournisseur [Microsoft. extensions. Logging. ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) écrit les journaux dans [Azure application Insights](/azure/azure-monitor/app/cloudservices). Application Insights est un service qui surveille une application web et fournit des outils pour interroger et analyser les données de télémétrie. Si vous utilisez ce fournisseur, vous pouvez interroger et analyser vos journaux à l’aide des outils Application Insights.

Le fournisseur de journalisation est inclus en tant que dépendance de [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), le package qui fournit toutes les données de télémétrie disponibles pour ASP.NET Core. Si vous utilisez ce package, vous n’avez pas besoin d’installer le package du fournisseur.

Le package [Microsoft. ApplicationInsights. Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) est destiné à ASP.net 4. x, et non ASP.net core.

Pour plus d’informations, consultez les ressources suivantes :

* [Vue d’ensemble d’Application Insights](/azure/application-insights/app-insights-overview)
* [Application Insights pour les applications ASP.NET Core](/azure/azure-monitor/app/asp-net-core) : commencez ici si vous souhaitez implémenter la gamme complète des données de télémétrie d’Application Insights en même temps que la journalisation.
* [Journaux ApplicationInsightsLoggerProvider pour .NET Core ILogger](/azure/azure-monitor/app/ilogger) : commencez ici si vous souhaitez implémenter le fournisseur de journalisation sans le reste des données de télémétrie Application Insights.
* [Adaptateurs de journalisation Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).
* [Installer, configurer et initialiser le kit de développement logiciel (SDK) Application Insights](/learn/modules/instrument-web-app-code-with-application-insights) : tutoriel interactif sur le site Microsoft Learn.

## <a name="third-party-logging-providers"></a>Fournisseurs de journalisation tiers

Frameworks de journalisation tiers qui sont pris en charge dans ASP.NET Core :

* [elmah.io](https://elmah.io/) ([dépôt GitHub](https://github.com/elmahio/Elmah.Io.Extensions.Logging))
* [Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([Dépôt GitHub](https://github.com/mattwcole/gelf-extensions-logging))
* [JSNLog](https://jsnlog.com/) ([dépôt GitHub](https://github.com/mperdeck/jsnlog))
* [KissLog.net](https://kisslog.net/) ([référentiel GitHub](https://github.com/catalingavan/KissLog-net))
* [Log4net](https://logging.apache.org/log4net/) ([GitHub référentiel](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))
* [Loggr](https://loggr.net/) ([dépôt GitHub](https://github.com/imobile3/Loggr.Extensions.Logging))
* [NLog](https://nlog-project.org/) ([dépôt GitHub](https://github.com/NLog/NLog.Extensions.Logging))
* [PLogger](https://www.nuget.org/packages/InvertedSoftware.PLogger.Core/) ([GitHub référentiel](https://github.com/invertedsoftware/InvertedSoftware.PLogger.Core))
* [Sentry](https://sentry.io/welcome/) ([dépôt GitHub](https://github.com/getsentry/sentry-dotnet))
* [Serilog](https://serilog.net/) ([dépôt GitHub](https://github.com/serilog/serilog-aspnetcore))
* [Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))

Certains frameworks tiers prennent en charge la [journalisation sémantique, également appelée journalisation structurée](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

L’utilisation d’un framework tiers est semblable à l’utilisation des fournisseurs intégrés :

1. Ajoutez un package NuGet à votre projet.
1. Appeler une `ILoggerFactory` méthode d’extension fournie par le Framework de journalisation.

Pour plus d’informations, consultez la documentation de chaque fournisseur. Les fournisseurs de journalisation tiers ne sont pas pris en charge par Microsoft.

<a name="nhca"></a>

## <a name="non-host-console-app"></a>Application console non-hôte

Pour obtenir un exemple d’utilisation de l’hôte générique dans une application de console non Web, consultez le fichier *Program.cs* de l' [exemple d’application tâches en arrière-plan](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) ( <xref:fundamentals/host/hosted-services> ).

Le code de journalisation pour les applications sans hôte générique diffère dans la façon dont les [fournisseurs sont ajoutés](#add-providers) et les [enregistreurs d'événements créés](#create-logs). 

### <a name="logging-providers"></a>Fournisseurs de journalisation

Dans une application de console non hôte, appelez la méthode d’extension `Add{provider name}` du fournisseur lors de la création d’un `LoggerFactory` :

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=11-12)]

### <a name="create-logs"></a>Créer des journaux

Pour créer des journaux, utilisez un objet <xref:Microsoft.Extensions.Logging.ILogger%601>. Utilisez `LoggerFactory` pour créer un `ILogger` .

L’exemple suivant crée un enregistreur d’événements avec `LoggingConsoleApp.Program` comme catégorie.

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=14)]

Dans les exemples de base ASP.NET suivants, l’enregistreur d’événements est utilisé pour créer des journaux avec `Information` comme niveau. Le *niveau* du journal indique la gravité de l’événement consigné.

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=15)]

Les [niveaux](#log-level) et les [catégories](#log-category) sont expliqués plus en détail dans ce document.

<a name="lhc"></a>

## <a name="log-during-host-construction"></a>Journal lors de la construction de l’hôte

La journalisation pendant la construction de l’hôte n’est pas prise en charge directement. Toutefois, un enregistreur d’événements distinct peut être utilisé. Dans l’exemple suivant, un enregistreur d’événements [Serilog](https://serilog.net/) est utilisé pour se connecter `CreateHostBuilder` . `AddSerilog`utilise la configuration statique spécifiée dans `Log.Logger` :

```csharp
using System;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Hosting;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args)
    {
        var builtConfig = new ConfigurationBuilder()
            .AddJsonFile("appsettings.json")
            .AddCommandLine(args)
            .Build();

        Log.Logger = new LoggerConfiguration()
            .WriteTo.Console()
            .WriteTo.File(builtConfig["Logging:FilePath"])
            .CreateLogger();

        try
        {
            return Host.CreateDefaultBuilder(args)
                .ConfigureServices((context, services) =>
                {
                    services.AddRazorPages();
                })
                .ConfigureAppConfiguration((hostingContext, config) =>
                {
                    config.AddConfiguration(builtConfig);
                })
                .ConfigureLogging(logging =>
                {   
                    logging.AddSerilog();
                })
                .ConfigureWebHostDefaults(webBuilder =>
                {
                    webBuilder.UseStartup<Startup>();
                });
        }
        catch (Exception ex)
        {
            Log.Fatal(ex, "Host builder error");

            throw;
        }
        finally
        {
            Log.CloseAndFlush();
        }
    }
}
```

<a name="csdi"></a>

## <a name="configure-a-service-that-depends-on-ilogger"></a>Configurer un service qui dépend de ILogger

L’injection de constructeur d’un enregistreur d’événements dans `Startup` fonctionne dans les versions antérieures d’ASP.NET Core, car un conteneur d’injection de dépendances distinct est créé pour l’hôte web. Pour plus d’informations sur la raison de la création d’un seul conteneur pour l’hôte générique, consultez l’[annonce de changement cassant](https://github.com/aspnet/Announcements/issues/353).

Pour configurer un service qui dépend de `ILogger<T>` , utilisez l’injection de constructeur ou fournissez une méthode de fabrique. L’approche de la méthode de fabrique est recommandée uniquement s’il n’y a aucune autre option. Par exemple, considérez un service qui a besoin d’une `ILogger<T>` instance fournie par di :

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup2.cs?name=snippet_ConfigureServices&highlight=6-10)]

Le code en surbrillance précédent est un [Func](/dotnet/api/system.func-2) qui s’exécute la première fois que le conteneur di doit construire une instance de `MyService` . Vous pouvez accéder à tous les services inscrits de cette manière.

<a name="clms"></a>

## <a name="create-logs-in-main"></a>Créer des journaux dans main

Le code suivant se connecte `Main` en obtenant une `ILogger` instance à partir de di après avoir généré l’hôte :

[!code-csharp[](index/samples/3.x/TodoApiDTO/Program.cs?name=snippet_LogProgram)]

### <a name="create-logs-in-startup"></a>Créer des journaux au démarrage

Le code suivant écrit les journaux dans `Startup.Configure` :

[!code-csharp[](index/samples/3.x/TodoApiDTO/Startup.cs?name=snippet_Configure)]

L’écriture de journaux avant la fin de l’installation du conteneur d’injection de dépendances dans la méthode `Startup.ConfigureServices` n’est pas prise en charge :

* L’injection d’un enregistreur d’événements dans le constructeur `Startup` n’est pas prise en charge.
* L’injection d’un enregistreur d’événements dans la signature de méthode `Startup.ConfigureServices` n’est pas prise en charge.

La raison de cette restriction est que la journalisation dépend de l’injection de dépendances et de la configuration qui, à son tour, dépend de l’injection de dépendances. Le conteneur d’injection de dépendances n’est pas configuré avant que `ConfigureServices` soit terminé.

Pour plus d’informations sur la configuration d’un service qui dépend de `ILogger<T>` ou de la raison pour laquelle l’injection d’un enregistreur d’événements dans les `Startup` versions antérieures, consultez [configurer un service qui dépend de ILogger](#csdi)

### <a name="no-asynchronous-logger-methods"></a>Aucune méthode d’enregistreur d’événements asynchrone

La journalisation doit être suffisamment rapide par rapport au coût du code asynchrone en matière de performances. Si un magasin de données de journalisation est lent, n’écrivez pas directement dessus. Envisagez d’écrire les messages du journal dans un magasin rapide, puis de les déplacer ultérieurement vers la Banque lente. Par exemple, lorsque vous vous connectez à SQL Server, ne le faites pas directement dans une `Log` méthode, puisque les `Log` méthodes sont synchrones. Ajoutez plutôt de façon synchronisée des messages de journal à une file d’attente en mémoire, puis configurez un traitement en arrière-plan afin d’extraire les messages de la file d’attente et d’effectuer le travail asynchrone d’envoi des données vers SQL Server. Pour plus d’informations, consultez [ce](https://github.com/dotnet/AspNetCore.Docs/issues/11801) problème github.

<a name="clib"></a>

## <a name="change-log-levels-in-a-running-app"></a>Modifier les niveaux de journal dans une application en cours d’exécution

L’API de journalisation n’inclut pas de scénario pour modifier les niveaux de journal lorsqu’une application est en cours d’exécution. Toutefois, certains fournisseurs de configuration sont en charge du rechargement de la configuration, ce qui prend immédiatement effet sur la configuration de la journalisation. Par exemple, le [fournisseur de configuration de fichier](xref:fundamentals/configuration/index#file-configuration-provider), recharge la configuration de journalisation par défaut. Si la configuration est modifiée dans le code pendant qu’une application est en cours d’exécution, l’application peut appeler [IConfigurationRoot. Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) pour mettre à jour la configuration de journalisation de l’application.

## <a name="ilogger-and-iloggerfactory"></a>ILogger et ILoggerFactory

Les <xref:Microsoft.Extensions.Logging.ILogger%601> <xref:Microsoft.Extensions.Logging.ILoggerFactory> interfaces et et les implémentations sont incluses dans la kit SDK .net core. Ils sont également disponibles dans les packages NuGet suivants :  

* Les interfaces se trouvent dans [Microsoft. extensions. Logging. Abstracts](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/).
* Les implémentations par défaut se trouvent dans [Microsoft. extensions. Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).

<!-- review. Why would you want to hard code filtering rules in code? -->
<a name="fric"></a>

## <a name="apply-log-filter-rules-in-code"></a>Appliquer les règles de filtre de journal dans le code

L’approche recommandée pour définir des règles de filtre de journal consiste à utiliser la [configuration](xref:fundamentals/configuration/index).

L'exemple suivant montre comment enregistrer des règles de filtre dans le code :

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_FilterInCode)]

`logging.AddFilter("System", LogLevel.Debug)`spécifie la `System` catégorie et le niveau de journalisation `Debug` . Le filtre est appliqué à tous les fournisseurs, car un fournisseur spécifique n’a pas été configuré.

`AddFilter<DebugLoggerProvider>("Microsoft", LogLevel.Information)`indiquant

* `Debug`Fournisseur de journalisation.
* Niveau de journalisation `Information` et supérieur.
* Toutes les catégories commençant par `"Microsoft"` .

## <a name="create-a-custom-logger"></a>Créer un enregistreur d’événements personnalisé

Pour ajouter un enregistreur d’événements personnalisé, ajoutez un <xref:Microsoft.Extensions.Logging.ILoggerProvider> avec <xref:Microsoft.Extensions.Logging.ILoggerFactory> :

```csharp
public void Configure(
    IApplicationBuilder app,
    IWebHostEnvironment env,
    ILoggerFactory loggerFactory)
{
    loggerFactory.AddProvider(new CustomLoggerProvider(new CustomLoggerConfiguration()));
```

Le `ILoggerProvider` crée une ou plusieurs `ILogger` instances. Les `ILogger` instances sont utilisées par le Framework pour enregistrer les informations.

### <a name="sample-custom-logger-configuration"></a>Exemple de configuration d’un enregistreur d’événements personnalisé

L'exemple :

* Est conçu pour être un exemple très basique qui définit la couleur de la console du journal par l’ID d’événement et le niveau de journalisation. Les journaux ne sont généralement pas modifiés par l’ID d’événement et ne sont pas spécifiques au niveau du journal.
* Crée différentes entrées de console couleur par niveau de journalisation et ID d’événement en utilisant le type de configuration suivant :

[!code-csharp[](index/samples/3.x/CustomLogger/ColorConsoleLogger/ColorConsoleLoggerConfiguration.cs?name=snippet)]

Le code précédent affecte au niveau par défaut la valeur `Warning` et à la couleur `Yellow` . Si le `EventId` est défini sur 0, tous les événements seront journalisés.

### <a name="create-the-custom-logger"></a>Créer l’enregistreur d’événements personnalisé

Le `ILogger` nom de catégorie d’implémentation correspond généralement à la source de journalisation. Par exemple, le type dans lequel l’enregistreur d’événements est créé :

[!code-csharp[](index/samples/3.x/CustomLogger/ColorConsoleLogger/ColorConsoleLogger.cs?name=snippet)]

Le code précédent :

* Crée une instance d’enregistreur d’événements par nom de catégorie.
* Archive `logLevel == _config.LogLevel` `IsEnabled` , donc chaque `logLevel` a un enregistreur d’événements unique. En règle générale, les enregistreurs d’événements doivent également être activés pour tous les niveaux de journalisation les plus élevés :

```csharp
public bool IsEnabled(LogLevel logLevel)
{
    return logLevel >= _config.LogLevel;
}
```

### <a name="create-the-custom-loggerprovider"></a>Créer le LoggerProvider personnalisé

`LoggerProvider`Est la classe qui crée les instances d’enregistreur d’événements. Il n’est peut-être pas nécessaire de créer une instance d’enregistreur d’événements par catégorie, mais cela est logique pour certains enregistreurs d’événements, tels que NLog ou log4net. En procédant ainsi, vous pouvez choisir différentes cibles de sortie de journalisation par catégorie, si nécessaire :

[!code-csharp[](index/samples/3.x/CustomLogger/ColorConsoleLogger/ColorConsoleLoggerProvider.cs?name=snippet)]

Dans le code précédent, <xref:Microsoft.Build.Logging.LoggerDescription.CreateLogger*> crée une seule instance de `ColorConsoleLogger` par nom de catégorie et la stocke dans le [`ConcurrentDictionary<TKey,TValue>`](/dotnet/api/system.collections.concurrent.concurrentdictionary-2) ;

### <a name="usage-and-registration-of-the-custom-logger"></a>Utilisation et inscription de l’enregistreur d’événements personnalisé

Inscrire l’enregistreur d’événements dans `Startup.Configure` :

[!code-csharp[](index/samples/3.x/CustomLogger/Startup.cs?name=snippet)]

Pour le code précédent, fournissez au moins une méthode d’extension pour le `ILoggerFactory` :

[!code-csharp[](index/samples/3.x/CustomLogger/ColorConsoleLogger/ColorConsoleLoggerExtensions.cs?name=snippet)]

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:fundamentals/logging/loggermessage>
* Les bogues de journalisation doivent être créés dans le référentiel [github.com/dotnet/Runtime/](https://github.com/dotnet/runtime/issues) .
* <xref:blazor/fundamentals/logging>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Par [Tom Dykstra](https://github.com/tdykstra) et [Steve Smith](https://ardalis.com/)

.NET Core prend en charge une API de journalisation qui fonctionne avec différents fournisseurs de journalisation tiers et intégrés. Cet article explique comment utiliser cette API de journalisation avec les fournisseurs de journalisation intégrés.

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

## <a name="add-providers"></a>Ajouter des fournisseurs

Un fournisseur de journalisation affiche ou stocke des journaux. Par exemple, le fournisseur Console affiche les journaux dans la console, et le fournisseur Azure Application Insights les stocke dans Azure Application Insights. Il est possible d’envoyer les journaux à plusieurs endroits en ajoutant plusieurs fournisseurs.

Pour ajouter un fournisseur, appelez la méthode d’extension `Add{provider name}` du fournisseur dans *Program.cs* :

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=18-20)]

Le code précédent nécessite des références à `Microsoft.Extensions.Logging` et `Microsoft.Extensions.Configuration`.

Le modèle de projet par défaut appelle <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, qui ajoute les fournisseurs de journalisation suivants :

* Console
* Débogage
* EventSource (à partir d’ASP.NET Core 2.2)

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_TemplateCode&highlight=7)]

Si vous utilisez `CreateDefaultBuilder`, vous pouvez remplacer les fournisseurs par défaut par ceux de votre choix. Appelez <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> et ajoutez les fournisseurs que vous souhaitez.

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=18-22)]

Vous trouverez des informations sur les [fournisseurs de journalisation intégrés](#built-in-logging-providers) et les [fournisseurs de journalisation tiers](#third-party-logging-providers) plus loin dans cet article.

## <a name="create-logs"></a>Créer des journaux

Pour créer des journaux, utilisez un objet <xref:Microsoft.Extensions.Logging.ILogger%601>. Dans une application web ou un service hébergé, obtenez un `ILogger` à partir de l’injection de dépendances (DI). Dans les applications de console non hôtes, utilisez le `LoggerFactory` pour créer un `ILogger`.

L’exemple d’ASP.NET Core suivant crée un enregistreur d’événements de catégorie `TodoApiSample.Pages.AboutModel`. La *catégorie* du journal est une chaîne associée à chaque journal. L’instance `ILogger<T>` fournie par l’injection de dépendances crée des journaux ayant comme catégorie un nom complet de type `T`. 

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

Dans les exemples d’ASP.NET Core et d’application de console suivants, l’enregistreur d’événements est utilisé pour créer des journaux de niveau `Information`. Le *niveau* du journal indique la gravité de l’événement consigné.

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

Les [niveaux](#log-level) et les [catégories](#log-category) sont expliqués plus en détail plus loin dans cet article.

### <a name="create-logs-in-startup"></a>Créer des journaux au démarrage

Pour écrire des journaux dans la classe `Startup`, ajoutez un paramètre `ILogger` dans la signature de constructeur :

[!code-csharp[](index/samples/2.x/TodoApiSample/Startup.cs?name=snippet_Startup&highlight=3,5,8,20,27)]

### <a name="create-logs-in-the-program-class"></a>Créer des journaux dans la classe Programme

Pour écrire des journaux dans la classe `Program`, récupérez une instance `ILogger` auprès de l’injection de dépendances :

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

La journalisation pendant la construction de l’hôte n’est pas prise en charge directement. Toutefois, un enregistreur d’événements distinct peut être utilisé. Dans l’exemple suivant, un enregistreur d’événements [Serilog](https://serilog.net/) est utilisé pour se connecter `CreateWebHostBuilder` . `AddSerilog`utilise la configuration statique spécifiée dans `Log.Logger` :

```csharp
using System;
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args)
    {
        var builtConfig = new ConfigurationBuilder()
            .AddJsonFile("appsettings.json")
            .AddCommandLine(args)
            .Build();

        Log.Logger = new LoggerConfiguration()
            .WriteTo.Console()
            .WriteTo.File(builtConfig["Logging:FilePath"])
            .CreateLogger();

        try
        {
            return WebHost.CreateDefaultBuilder(args)
                .ConfigureServices((context, services) =>
                {
                    services.AddMvc();
                })
                .ConfigureAppConfiguration((hostingContext, config) =>
                {
                    config.AddConfiguration(builtConfig);
                })
                .ConfigureLogging(logging =>
                {
                    logging.AddSerilog();
                })
                .UseStartup<Startup>();
        }
        catch (Exception ex)
        {
            Log.Fatal(ex, "Host builder error");

            throw;
        }
        finally
        {
            Log.CloseAndFlush();
        }
    }
}
```

### <a name="no-asynchronous-logger-methods"></a>Aucune méthode d’enregistreur d’événements asynchrone

La journalisation doit être suffisamment rapide par rapport au coût du code asynchrone en matière de performances. Si votre magasin de données de journalisation est lent, n’écrivez pas directement dedans. Écrivez au départ les messages de journal dans un magasin rapide, puis déplacez-les dans le magasin lent. Par exemple, si vous vous connectez à SQL Server, évitez de le faire directement dans une méthode `Log`, car les méthodes `Log` sont synchrones. Ajoutez plutôt de façon synchronisée des messages de journal à une file d’attente en mémoire, puis configurez un traitement en arrière-plan afin d’extraire les messages de la file d’attente et d’effectuer le travail asynchrone d’envoi des données vers SQL Server. Pour plus d’informations, consultez [ce](https://github.com/dotnet/AspNetCore.Docs/issues/11801) problème github.

## <a name="configuration"></a>Configuration

La configuration de fournisseur de journalisation est fournie par un ou plusieurs fournisseurs de configuration :

* Formats de fichiers (INI, JSON et XML).
* Arguments de ligne de commande
* Variables d'environnement.
* Objets .NET en mémoire.
* Stockage [Secret Manager](xref:security/app-secrets) non chiffré.
* Magasin utilisateur chiffré comme [Azure Key Vault](xref:security/key-vault-configuration).
* Fournisseurs personnalisés (installés ou créés).

Par exemple, la configuration de journalisation est généralement fournie par la section `Logging` des fichiers de paramètres d’application. L’exemple suivant montre le contenu d’un fichier *appsettings.Development.json* standard :

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Debug",
      "System": "Information",
      "Microsoft": "Information"
    },
    "Console":
    {
      "IncludeScopes": true
    }
  }
}
```

La propriété `Logging` peut avoir un niveau `LogLevel` et des propriétés de module fournisseur d'informations (Console ici).

La propriété `LogLevel` sous `Logging` spécifie le [niveau](#log-level) de journalisation minimal pour les catégories sélectionnées. Dans l’exemple, les catégories `System` et `Microsoft` sont consignées au niveau `Information`, et toutes les autres au niveau `Debug`.

Les autres propriétés sous `Logging` spécifient les fournisseurs de journalisation. Cet exemple concerne le fournisseur Console. Si un fournisseur prend en charge les [étendues de journal](#log-scopes), `IncludeScopes` indique s’ils sont activés. Une propriété de fournisseur (comme `Console` dans l’exemple) peut également spécifier une propriété `LogLevel`. `LogLevel` sous un fournisseur spécifie les niveaux à consigner pour ce fournisseur.

Si les niveaux sont spécifiés dans `Logging.{providername}.LogLevel`, ils remplacent ce qui est défini dans `Logging.LogLevel`. Considérons, par exemple, le code JSON suivant :

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.MSFT.json)]

Dans le code JSON précédent, les `Console` paramètres du fournisseur remplacent le niveau de journalisation précédent (par défaut).

L’API de journalisation n’inclut pas de scénario pour modifier les niveaux de journal lorsqu’une application est en cours d’exécution. Toutefois, certains fournisseurs de configuration sont en charge du rechargement de la configuration, ce qui prend immédiatement effet sur la configuration de la journalisation. Par exemple, le [fournisseur de configuration de fichier](xref:fundamentals/configuration/index#file-configuration-provider), qui est ajouté par `CreateDefaultBuilder` pour lire les fichiers de paramètres, recharge la configuration de journalisation par défaut. Si la configuration est modifiée dans le code pendant qu’une application est en cours d’exécution, l’application peut appeler [IConfigurationRoot. Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) pour mettre à jour la configuration de journalisation de l’application.

Pour plus d’informations sur l’implémentation des fournisseurs de configuration, consultez <xref:fundamentals/configuration/index>.

## <a name="sample-logging-output"></a>Exemple de sortie de la journalisation

Avec l’exemple de code présenté dans la section précédente, les journaux s’affichent dans la console lorsque l’application est exécutée en ligne de commande. Voici un exemple de sortie de la console :

```console
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:5000/api/todo/0
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
info: TodoApi.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 42.9286ms
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[2]
      Request finished in 148.889ms 404
```

Les journaux précédents ont été générés par une requête HTTP Get à l’exemple d’application à l’adresse `http://localhost:5000/api/todo/0`.

Voici un exemple des mêmes journaux tels qu’ils s’affichent dans la fenêtre Débogage quand vous exécutez l’exemple d’application dans Visual Studio :

```console
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request starting HTTP/1.1 GET http://localhost:53104/api/todo/0  
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
TodoApi.Controllers.TodoController:Information: Getting item 0
TodoApi.Controllers.TodoController:Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult:Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 152.5657ms
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request finished in 316.3195ms 404
```

Les journaux créés par les appels de `ILogger` illustrés dans la section précédente commencent par « TodoApi ». Ceux qui commencent par les catégories « Microsoft » proviennent du code du framework ASP.NET Core. ASP.NET Core et le code d’application utilisent la même API de journalisation et les mêmes fournisseurs.

Les autres sections de cet article détaillent certains points et présentent les options de journalisation.

## <a name="nuget-packages"></a>Packages NuGet

Les interfaces `ILogger` et `ILoggerFactory` se trouvent dans [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), et leurs implémentations par défaut se trouvent dans [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).

## <a name="log-category"></a>Catégorie de journal

Quand un objet `ILogger` est créé, une *catégorie* lui est spécifiée. Cette catégorie est incluse dans tous les messages de journal créés par cette instance de `ILogger`. Si la catégorie peut être n’importe quelle chaîne, la convention est d’utiliser le nom de la classe, par exemple « TodoApi.Controllers.TodoController ».

Utilisez `ILogger<T>` pour obtenir une instance de `ILogger` qui utilise le nom de type complet `T` comme catégorie :

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

Pour spécifier explicitement la catégorie, appelez `ILoggerFactory.CreateLogger` :

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

`ILogger<T>` équivaut à appeler `CreateLogger` avec le nom de type complet `T`.

## <a name="log-level"></a>Log level

Chaque journal spécifie une valeur <xref:Microsoft.Extensions.Logging.LogLevel>. Le niveau de journalisation indique la gravité ou l’importance. Vous pourriez par exemple écrire un journal `Information` lorsqu’une méthode se termine normalement et un journal `Warning` lorsqu’une méthode retourne un code de statut *404 Not Found*.

Le code suivant crée des journaux `Information` et `Warning` :

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

Dans le code précédent, les `MyLogEvents.GetItem` `MyLogEvents.GetItemNotFound` paramètres et sont l' [ID d’événement du journal](#log-event-id). Le deuxième paramètre est un modèle de message contenant des espaces réservés pour les valeurs d’argument fournies par les autres paramètres de méthode. Les paramètres de méthode sont expliqués dans la [section modèle de message de journal](#lmt) de cet article.

Les méthodes de journal qui comportent le niveau dans leur nom (par exemple, `LogInformation` et `LogWarning`) sont des [méthodes d’extension pour ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions). Elles appellent une méthode `Log` qui prend un paramètre `LogLevel`. Vous pouvez appeler la méthode `Log` directement au lieu d’appeler ces méthodes d’extension, mais la syntaxe est relativement complexe. Pour plus d’informations, voir <xref:Microsoft.Extensions.Logging.ILogger> et le [code source des extensions d’enregistreur d'événements](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).

ASP.NET Core définit les niveaux de journalisation suivants, classés selon leur degré de gravité (du plus faible au plus élevé).

* Trace = 0

  Informations en général exclusivement utiles à des fins de débogage. Ces messages peuvent contenir des données d’application sensibles. Ils ne doivent donc pas être activés dans un environnement de production. *Désactivé par défaut.*

* Debug = 1

  Informations qui peuvent être utiles dans le développement et le débogage. Exemple : `Entering method Configure with flag set to true.` En raison de leur volume élevé, n’activez les journaux de niveau `Debug` en production que pour résoudre des problèmes.

* Information = 2

  Informations de suivi du flux général de l’application. Ces journaux ont généralement une utilité à long terme. Exemple : `Request received for path /api/todo`

* Warning = 3

  Informations sur les événements anormaux ou inattendus dans le flux de l’application. Il peut s’agir d’erreurs ou d’autres situations qui ne provoquent pas l’arrêt de l’application, mais qu’il peut être intéressant d’examiner. Le niveau de journalisation `Warning` est généralement utilisé pour les exceptions gérées. Exemple : `FileNotFoundException for file quotes.txt.`

* Error = 4

  Fournit des informations sur des erreurs et des exceptions qui ne peuvent pas être gérées. Ces messages indiquent un échec de l’activité ou de l’opération en cours (par exemple, la requête HTTP actuellement exécutée), pas un échec au niveau de l’application. Exemple de message de journal : `Cannot insert record due to duplicate key violation.`

* Critical = 5

  Fournit des informations sur des échecs qui nécessitent un examen immédiat. Exemples : perte de données, espace disque insuffisant.

Le niveau de journalisation permet de contrôler le volume de la sortie de journal écrite sur un support de stockage ou dans une fenêtre d’affichage. Par exemple :

* En production :
  * La journalisation `Trace` au `Information` niveau des niveaux de production génère un volume important de messages journaux détaillés. Pour contrôler les coûts et ne pas dépasser les limites de stockage des données, consignez les `Trace` `Information` messages de niveau dans un magasin de données volumineux et à faible coût.
  * La journalisation au niveau `Warning` `Critical` des niveaux produit généralement moins de messages journaux plus petits. Par conséquent, les coûts et les limites de stockage ne sont généralement pas un problème, ce qui se traduit par une plus grande flexibilité du choix des magasins de données.
* Lors du développement :
  * Journalisation `Warning` `Critical` des messages dans la console.
  * Ajoutez `Trace` `Information` des messages lors de la résolution des problèmes.

La section [Filtrage de journal](#log-filtering) plus loin dans cet article explique comment déterminer les niveaux de journalisation gérés par un fournisseur.

ASP.NET Core écrit des journaux pour les événements de framework. Aucun journal du niveau `Debug` ou `Trace` n’était créé dans les exemples de journaux présentés plus haut dans cet article, puisque les journaux au-dessous du niveau `Information` étaient exclus. Voici un exemple de journaux Console produits par l’exemple d’application configurée pour afficher les journaux `Debug` :

```console
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:62555/api/todo/0
dbug: Microsoft.AspNetCore.Routing.Tree.TreeRouter[1]
      Request successfully matched the route with name 'GetTodo' and template 'api/Todo/{id}'.
dbug: Microsoft.AspNetCore.Mvc.Internal.ActionSelector[2]
      Action 'TodoApi.Controllers.TodoController.Update (TodoApi)' with id '089d59b6-92ec-472d-b552-cc613dfd625d' did not match the constraint 'Microsoft.AspNetCore.Mvc.Internal.HttpMethodActionConstraint'
dbug: Microsoft.AspNetCore.Mvc.Internal.ActionSelector[2]
      Action 'TodoApi.Controllers.TodoController.Delete (TodoApi)' with id 'f3476abe-4bd9-4ad3-9261-3ead09607366' did not match the constraint 'Microsoft.AspNetCore.Mvc.Internal.HttpMethodActionConstraint'
dbug: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action TodoApi.Controllers.TodoController.GetById (TodoApi)
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
info: TodoApi.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
dbug: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action method TodoApi.Controllers.TodoController.GetById (TodoApi), returned result Microsoft.AspNetCore.Mvc.NotFoundResult.
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 0.8788ms
dbug: Microsoft.AspNetCore.Server.Kestrel[9]
      Connection id "0HL6L7NEFF2QD" completed keep alive response.
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[2]
      Request finished in 2.7286ms 404
```

## <a name="log-event-id"></a>ID d’événement de log

Chaque journal peut spécifier un *ID d’événement*. Pour cela, l’exemple d’application utilise une classe `LoggingEvents` définie localement :

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

Un ID d’événement associe un jeu d’événements. Par exemple, tous les journaux liés à l’affichage d’une liste d’éléments sur une page peuvent être 1001.

Le fournisseur de journalisation peut stocker l’ID d’événement dans un champ ID, dans le message de journalisation, ou pas du tout. Le fournisseur Debug n’affiche pas les ID d’événements. Le fournisseur Console affiche les ID d’événements entre crochets après la catégorie :

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a>Modèle de message de journal

Chaque journal spécifie un modèle de message. Ce dernier peut contenir des espaces réservés pour lesquels les arguments sont fournis. Utilisez des noms et non des nombres pour les espaces réservés.

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

L’ordre des espaces réservés, pas leurs noms, détermine quels paramètres sont utilisés pour fournir leurs valeurs. Dans le code suivant, on voit que les noms de paramètres sont hors séquence dans le modèle de message :

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

Ce code crée un message de journal avec les valeurs des paramètres dans la séquence :

```text
Parameter values: parm1, parm2
```

Le framework de journalisation fonctionne ainsi pour permettre aux fournisseurs de journalisation d’implémenter la [journalisation sémantique, également appelée journalisation structurée](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging). Les arguments proprement dits, et pas seulement le modèle de message mis en forme, sont transmis au système de journalisation. C’est grâce à ces informations que les fournisseurs de journalisation peuvent stocker les valeurs des paramètres sous forme de champs. Supposons par exemple que les appels de méthodes d’enregistreur d’événements se présentent ainsi :

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

Si vous envoyez les journaux au Stockage Table Azure, chaque entité Table Azure peut avoir les propriétés `ID` et `RequestTime`, ce qui simplifie les requêtes sur les données de journaux. Une requête peut rechercher tous les journaux compris dans une plage `RequestTime` spécifique sans analyser le délai d’expiration du message texte.

## <a name="logging-exceptions"></a>Journalisation des exceptions

Les méthodes logger ont des surcharges qui vous permettent de passer une exception, comme dans l’exemple suivant :

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

Tous les fournisseurs ne gèrent pas les informations sur les exceptions de la même façon. Voici un exemple de sortie du fournisseur Debug extrait du code montré plus haut.

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a>Filtrage de journal

Vous pouvez spécifier un niveau de journalisation minimum pour une catégorie ou un fournisseur spécifique, ou pour l’ensemble des fournisseurs ou des catégories. Les enregistrements de journal en dessous du niveau minimum ne sont pas passés à ce fournisseur, et ne sont donc pas affichés ou stockés.

Pour supprimer tous les journaux, choisissez `LogLevel.None` comme niveau de journalisation minimal. La valeur entière de `LogLevel.None` est 6, soit un niveau supérieur à `LogLevel.Critical` (5).

### <a name="create-filter-rules-in-configuration"></a>Créer des règles de filtre dans la configuration

Le code du modèle de projet appelle `CreateDefaultBuilder` pour configurer la journalisation des fournisseurs de console, de débogage et EventSource (ASP.NET Core 2,2 ou version ultérieure). La méthode `CreateDefaultBuilder` définit également la journalisation pour rechercher la configuration dans une section `Logging`, conformément à ce qui a été expliqué [plus haut dans cet article](#configuration).

Les données de configuration spécifient des niveaux de journalisation minimum par fournisseur et par catégorie, comme dans l’exemple suivant :

[!code-json[](index/samples/2.x/TodoApiSample/appsettings.json)]

Ce code JSON crée six règles de filtre : une pour le fournisseur Debug, quatre pour le fournisseur Console et une pour tous les fournisseurs. Une seule règle est choisie pour chaque fournisseur à la création d’un objet `ILogger`.

### <a name="filter-rules-in-code"></a>Règles de filtre dans le code

L'exemple suivant montre comment enregistrer des règles de filtre dans le code :

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

Le second `AddFilter` spécifie le fournisseur Debug par son nom de type. Le premier `AddFilter` s’applique à tous les fournisseurs, car il ne spécifie aucun type de fournisseur.

### <a name="how-filtering-rules-are-applied"></a>Mode d’application des règles de filtre

Les données de configuration et le code `AddFilter` contenus dans les exemples précédents créent les règles présentées dans le tableau suivant. Les six premières proviennent de l’exemple de configuration et les deux dernières, de l’exemple de code.

| Number | Fournisseur      | Catégories commençant par...          | Niveau de journalisation minimum |
| :----: | ------------- | --------------------------------------- | ----------------- |
| 1      | Débogage         | Toutes les catégories                          | Information       |
| 2      | Console       | Microsoft. AspNetCore. Mvc. Razor . Intérieurs | Avertissement           |
| 3      | Console       | Microsoft. AspNetCore. Mvc. Razor .Razor    | Débogage             |
| 4      | Console       | Microsoft. AspNetCore. Mvc.Razor          | Error             |
| 5      | Console       | Toutes les catégories                          | Information       |
| 6      | Tous les fournisseurs | Toutes les catégories                          | Débogage             |
| 7      | Tous les fournisseurs | Système                                  | Débogage             |
| 8      | Débogage         | Microsoft                               | Trace             |

À la création d’un objet `ILogger`, l’objet `ILoggerFactory` sélectionne une seule règle à appliquer à cet enregistrement d’événements par fournisseur. Tous les messages écrits par une instance `ILogger` sont filtrés selon les règles sélectionnées. La règle la plus spécifique pouvant être appliquée à chaque paire catégorie/fournisseur est sélectionnée parmi les règles disponibles.

L’algorithme suivant est utilisé pour chaque fournisseur quand un objet `ILogger` est créé pour une catégorie donnée :

* Sélectionnez toutes les règles qui correspondent au fournisseur ou à son alias. Si aucune correspondance n’est trouvée, sélectionnez toutes les règles avec un fournisseur vide.
* À partir du résultat de l’étape précédente, sélectionnez les règles ayant le plus long préfixe de catégorie correspondant. Si aucune correspondance n’est trouvée, sélectionnez toutes les règles qui ne spécifient pas de catégorie.
* Si plusieurs règles sont sélectionnées, prenez la **dernière**.
* Si aucune règle n’est sélectionnée, utilisez `MinimumLevel`.

Avec la liste de règles ci-dessus, supposons que vous créez un `ILogger` objet pour la catégorie «Microsoft. AspNetCore. Mvc. Razor Razor Moteur " :

* Les règles 1, 6 et 8 s’appliquent au fournisseur Debug. La règle 8 est sélectionnée, car c’est la plus spécifique.
* Les règles 3, 4, 5 et 6 s’appliquent au fournisseur Console. La règle 3 est la plus spécifique.

L’instance `ILogger` ainsi produite envoie des journaux de niveau `Trace` ou supérieur au fournisseur Debug. Les journaux de niveau `Debug` et supérieurs sont envoyés au fournisseur Console.

### <a name="provider-aliases"></a>Alias de fournisseur

Chaque fournisseur définit un *alias* qui peut être utilisé dans la configuration à la place du nom de type complet.  Pour les fournisseurs intégrés, utilisez les alias suivants :

* Console
* Débogage
* EventSource
* EventLog
* TraceSource
* AzureAppServicesFile
* AzureAppServicesBlob
* ApplicationInsights

### <a name="default-minimum-level"></a>Niveau minimum par défaut

Un paramètre de niveau minimum est utilisé uniquement si aucune règle de la configuration ou du code ne s’applique à une catégorie ou un fournisseur spécifique. L’exemple suivant montre comment définir le niveau minimum :

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

Si vous ne définissez pas explicitement le niveau minimum, la valeur par défaut est `Information`, ce qui signifie que les niveaux `Trace` et `Debug` sont ignorés.

### <a name="filter-functions"></a>Fonctions de filtrage

Une fonction de filtre est appelée pour tous les fournisseurs et toutes les catégories pour lesquels la configuration ou le code n’applique aucune règle. Le code de la fonction a accès au type de fournisseur, à la catégorie et au niveau de journalisation. Par exemple :

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

## <a name="system-categories-and-levels"></a>Niveaux et les catégories de système

Voici quelques catégories utilisées par ASP.NET Core et Entity Framework Core, avec des notes sur les journaux associés :

| Category                            | Notes |
| ----------------------------------- | ----- |
| Microsoft.AspNetCore                | Diagnostics ASP.NET Core généraux. |
| Microsoft.AspNetCore.DataProtection | Liste des clés considérées, trouvées et utilisées. |
| Microsoft.AspNetCore.HostFiltering  | Hôtes autorisés. |
| Microsoft.AspNetCore.Hosting        | Temps de traitement des requêtes HTTP et heure de démarrage. Liste des assemblys de démarrage d’hébergement chargés. |
| Microsoft.AspNetCore.Mvc            | MVC et Razor Diagnostics. Liaison de données, exécution de filtres, compilation de vues, sélection d’actions. |
| Microsoft.AspNetCore.Routing        | Informations de correspondance des itinéraires. |
| Microsoft.AspNetCore.Server         | Démarrage et arrêt de la connexion, et réponses persistantes. Informations du certificat HTTPS. |
| Microsoft.AspNetCore.StaticFiles    | Fichiers pris en charge. |
| Microsoft.EntityFrameworkCore       | Diagnostics Entity Framework Core généraux. Activité et configuration de la base de données, détection des modifications, migrations. |

## <a name="log-scopes"></a>Étendues de journal

 Une *étendue* peut regrouper un ensemble d’opérations logiques. Ce regroupement permet de joindre les mêmes données à tous les journaux créés au sein d’un ensemble. Par exemple, chaque journal créé dans le cadre du traitement d’une transaction peut contenir l’ID de la transaction.

Une étendue est un type `IDisposable` qui est retourné par la méthode <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*>. Elle s’applique tant qu’elle n’est pas supprimée. Utilisez une étendue en incluant les appels de l’enregistrement d’événements dans un bloc `using` :

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

Le code suivant active les étendues pour le fournisseur Console :

*Program.cs*:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

> [!NOTE]
> La configuration de l’option logger `IncludeScopes` pour la console est nécessaire pour activer la journalisation basée sur des étendues.
>
> Pour plus d'informations sur la configuration, reportez-vous à la section [Configuration](#configuration).

Chaque message de journal fournit les informations incluses dans l’étendue :

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a>Fournisseurs de journalisation intégrés

ASP.NET Core contient les fournisseurs suivants :

* [Console](#console-provider)
* [Déboguer](#debug-provider)
* [EventSource](#event-source-provider)
* [EventLog](#windows-eventlog-provider)
* [TraceSource](#tracesource-provider)
* [AzureAppServicesFile](#azure-app-service-provider)
* [AzureAppServicesBlob](#azure-app-service-provider)
* [ApplicationInsights](#azure-application-insights-trace-logging)

Pour plus d’informations sur stdout et sur la journalisation du débogage avec le module ASP.NET Core, consultez <xref:test/troubleshoot-azure-iis> et <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.

### <a name="console-provider"></a>Fournisseur Console

Le package de fournisseur [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) envoie la sortie de journal dans la console. 

```csharp
logging.AddConsole();
```

Pour voir la sortie de la journalisation Console, ouvrez une invite de commandes dans le dossier du projet et exécutez la commande suivante :

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a>Fournisseur Debug

Le package de fournisseur [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) écrit la sortie de journal à l’aide de la classe [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) (appels de la méthode `Debug.WriteLine`).

Sur Linux, ce fournisseur écrit la sortie de log dans */var/log/message*.

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a>Fournisseur de source d’événements

Le package du fournisseur [Microsoft. extensions. Logging. EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) écrit dans une source d’événement multiplateforme portant le nom `Microsoft-Extensions-Logging` . Sur Windows, le fournisseur utilise [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).

```csharp
logging.AddEventSourceLogger();
```

Le fournisseur de la source d’événements est ajouté automatiquement lorsque `CreateDefaultBuilder` est appelé pour générer l’hôte.

Utilisez l' [utilitaire PerfView](https://github.com/Microsoft/perfview) pour collecter et afficher les journaux. Il existe d’autres outils d’affichage des journaux ETW, mais PerfView est l’outil recommandé pour gérer les événements ETW générés par ASP.NET Core.

Pour configurer PerfView afin qu’il collecte les événements enregistrés par ce fournisseur, ajoutez la chaîne `*Microsoft-Extensions-Logging` à la liste des **fournisseurs supplémentaires**. (N’oubliez pas d’inclure l’astérisque au début de la chaîne.)

![Fournisseurs supplémentaires dans PerfView](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a>Fournisseur EventLog Windows

Le package de fournisseur [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) envoie la sortie de journal dans le journal des événements Windows.

```csharp
logging.AddEventLog();
```

Les [surcharges AddEventLog](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) vous permettent de passer à <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>. Si `null` ou n’est pas spécifié, les paramètres par défaut suivants sont utilisés :

* `LogName`: « Application »
* `SourceName`: « .NET Runtime »
* `MachineName`: Le nom de l’ordinateur local est utilisé.

Les événements sont consignés pour le [niveau d’avertissement et supérieur](#log-level). L’exemple suivant définit le niveau de journalisation par défaut du journal des événements sur <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType> :

```json
"Logging": {
  "EventLog": {
    "LogLevel": {
      "Default": "Information"
    }
  }
}
```

### <a name="tracesource-provider"></a>Fournisseur TraceSource

Le package de fournisseur [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) utilise les bibliothèques et fournisseurs <xref:System.Diagnostics.TraceSource>.

```csharp
logging.AddTraceSource(sourceSwitchName);
```

Les [surcharges AddTraceSource](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) vous permettent de passer un commutateur de source et un écouteur de suivi.

Pour pouvoir utiliser ce fournisseur, il faut que l’application s’exécute sur .NET Framework (au lieu de .NET Core). Le fournisseur peut acheminer les messages vers différents [détecteurs d’événements](/dotnet/framework/debug-trace-profile/trace-listeners), comme <xref:System.Diagnostics.TextWriterTraceListener> (utilisé dans l’exemple d’application).

### <a name="azure-app-service-provider"></a>Fournisseur Azure App Service

Le package de fournisseur [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) écrit les enregistrements de journal dans des fichiers texte dans le système de fichiers d’une application Azure App Service, et dans un [stockage Blob](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) dans un compte de stockage Azure.

```csharp
logging.AddAzureWebAppDiagnostics();
```

Le package du fournisseur n’est pas inclus dans le [métapaquet Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app). Lorsque vous ciblez .NET framework ou référencez le métapackage `Microsoft.AspNetCore.App`, ajoutez le package de fournisseur dans le projet. 

Une surcharge <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> permet de transmettre <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>. L’objet de paramètres peut remplacer les paramètres par défaut, comme le modèle de sortie de journalisation, le nom d’objet blob et la limite de taille de fichier. (*Modèle de sortie* est un modèle de message qui s’applique à tous les journaux en plus de ce qui est fourni avec un appel de méthode `ILogger`.)

En cas de déploiement sur une application App Service, celle-ci applique les paramètres définis dans la section [Journaux App Service](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) de la page **App Service** du portail Azure. Quand les paramètres suivants sont mis à jour, les changements prennent effet immédiatement sans avoir besoin de redémarrer ou redéployer l’application.

* **Journalisation des applications (système de fichiers)**
* **Journalisation des applications (objet blob)**

L’emplacement par défaut des fichiers journaux est le dossier *D:\\racine\\LogFiles\\Application*, et le nom de fichier par défaut est *diagnostics-aaaammjj.txt*. La limite de taille de fichier par défaut est de 10 Mo, et le nombre maximal de fichiers conservés par défaut est de 2. Le nom par défaut du blob est *{app-name}{timestamp}/aaaa/mm/jj/hh/{guid}-applicationLog.txt*.

Le fournisseur fonctionne uniquement quand le projet s’exécute dans l’environnement Azure. Il n’a aucun effet si le projet s’exécute localement &mdash; il n’écrit pas d’enregistrements dans les fichiers locaux ou dans le stockage de développement local pour les objets blob.

#### <a name="azure-log-streaming"></a>Streaming des journaux Azure

La diffusion en continu des journaux Azure permet d’afficher l’activité de journalisation en temps réel aux emplacements suivants :

* Serveur d'applications
* Serveur web
* Suivi des demandes ayant échoué

Pour configurer le streaming des journaux Azure :

* Accédez à la page **Journaux App Service** dans le portail de votre application.
* Définissez **Journal des applications (Système de fichiers)** sur **Activé**.
* Choisissez le **niveau** du journal. Ce paramètre s’applique uniquement à la diffusion en continu de journaux Azure, pas aux autres fournisseurs de journalisation de l’application.

Accédez à la page **Streaming des journaux** pour voir les messages d’application. Ils sont consignés par application par le biais de l’interface `ILogger`.

### <a name="azure-application-insights-trace-logging"></a>Journalisation des traces Azure Application Insights

Le package de fournisseur [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) écrit des journaux dans Azure Application Insights. Application Insights est un service qui surveille une application web et fournit des outils pour interroger et analyser les données de télémétrie. Si vous utilisez ce fournisseur, vous pouvez interroger et analyser vos journaux à l’aide des outils Application Insights.

Le fournisseur de journalisation est inclus en tant que dépendance de [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), le package qui fournit toutes les données de télémétrie disponibles pour ASP.NET Core. Si vous utilisez ce package, vous n’avez pas besoin d’installer le package du fournisseur.

N’utilisez pas le [package Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package&mdash;destiné à ASP.NET 4.x.

Pour plus d’informations, consultez les ressources suivantes :

* [Vue d’ensemble d’Application Insights](/azure/application-insights/app-insights-overview)
* [Application Insights pour les applications ASP.NET Core](/azure/azure-monitor/app/asp-net-core) : commencez ici si vous souhaitez implémenter la gamme complète des données de télémétrie d’Application Insights en même temps que la journalisation.
* [Journaux ApplicationInsightsLoggerProvider pour .NET Core ILogger](/azure/azure-monitor/app/ilogger) : commencez ici si vous souhaitez implémenter le fournisseur de journalisation sans le reste des données de télémétrie Application Insights.
* [Adaptateurs de journalisation Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).
* [Installer, configurer et initialiser le kit de développement logiciel (SDK) Application Insights](/learn/modules/instrument-web-app-code-with-application-insights) : tutoriel interactif sur le site Microsoft Learn.

## <a name="third-party-logging-providers"></a>Fournisseurs de journalisation tiers

Frameworks de journalisation tiers qui sont pris en charge dans ASP.NET Core :

* [elmah.io](https://elmah.io/) ([dépôt GitHub](https://github.com/elmahio/Elmah.Io.Extensions.Logging))
* [Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([Dépôt GitHub](https://github.com/mattwcole/gelf-extensions-logging))
* [JSNLog](https://jsnlog.com/) ([dépôt GitHub](https://github.com/mperdeck/jsnlog))
* [KissLog.net](https://kisslog.net/) ([référentiel GitHub](https://github.com/catalingavan/KissLog-net))
* [Log4net](https://logging.apache.org/log4net/) ([GitHub référentiel](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))
* [Loggr](https://loggr.net/) ([dépôt GitHub](https://github.com/imobile3/Loggr.Extensions.Logging))
* [NLog](https://nlog-project.org/) ([dépôt GitHub](https://github.com/NLog/NLog.Extensions.Logging))
* [Sentry](https://sentry.io/welcome/) ([dépôt GitHub](https://github.com/getsentry/sentry-dotnet))
* [Serilog](https://serilog.net/) ([dépôt GitHub](https://github.com/serilog/serilog-aspnetcore))
* [Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))

Certains frameworks tiers prennent en charge la [journalisation sémantique, également appelée journalisation structurée](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

L’utilisation d’un framework tiers est semblable à l’utilisation des fournisseurs intégrés :

1. Ajoutez un package NuGet à votre projet.
1. Appeler une `ILoggerFactory` méthode d’extension fournie par le Framework de journalisation.

Pour plus d’informations, consultez la documentation de chaque fournisseur. Les fournisseurs de journalisation tiers ne sont pas pris en charge par Microsoft.

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:fundamentals/logging/loggermessage>

::: moniker-end
