---
title: ASP.NET pratiques de base en matière de rendement
author: mjrousos
description: Conseils pour augmenter les performances dans ASP.NET applications Core et éviter les problèmes de performances courants.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 04/06/2020
no-loc:
- SignalR
uid: performance/performance-best-practices
ms.openlocfilehash: 068a35fbe410dad24030fe68c0dfd062b402212c
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977182"
---
# <a name="aspnet-core-performance-best-practices"></a>ASP.NET pratiques de base en matière de rendement

Par [Mike Rousos](https://github.com/mjrousos)

Cet article fournit des lignes directrices pour les meilleures pratiques de performance avec ASP.NET Core.

## <a name="cache-aggressively"></a>Cache agressivement

Le cachage est discuté dans plusieurs parties de ce document. Pour plus d’informations, consultez <xref:performance/caching/response>.

## <a name="understand-hot-code-paths"></a>Comprendre les chemins de code chaud

Dans ce document, un *chemin de code chaud* est défini comme un chemin de code qui est fréquemment appelé et où une grande partie du temps d’exécution se produit. Les chemins de code chaud limitent généralement l’échelle et les performances des applications et sont discutés dans plusieurs parties de ce document.

## <a name="avoid-blocking-calls"></a>Évitez de bloquer les appels

ASP.NET les applications Core doivent être conçues pour traiter de nombreuses demandes simultanément. Les API asynchrones permettent à un petit pool de threads de traiter des milliers de demandes simultanées en n’attendant pas le blocage des appels. Plutôt que d’attendre une tâche synchrone de longue durée à accomplir, le thread peut fonctionner sur une autre demande.

Un problème de performances courant dans ASP.NET applications Core bloque les appels qui pourraient être asynchrones. De nombreux appels de blocage synchrone conduisent à [la famine Thread Pool](https://blogs.msdn.microsoft.com/vancem/2018/10/16/diagnosing-net-core-threadpool-starvation-with-perfview-why-my-service-is-not-saturating-all-cores-or-seems-to-stall/) et les temps de réponse dégradés.

**Ne pas**:

* Bloquer l’exécution asynchrone en appelant [Task.Wait](/dotnet/api/system.threading.tasks.task.wait) ou [Task.Result](/dotnet/api/system.threading.tasks.task-1.result).
* Acquérir des verrous dans des chemins de code communs. ASP.NET les applications Core sont les plus performantes lorsqu’elles sont intégrées pour exécuter le code en parallèle.
* Appelez [Task.Run](/dotnet/api/system.threading.tasks.task.run) et attendez immédiatement. ASP.NET Core exécute déjà le code d’application sur les threads Norm Thread Pool, de sorte que l’appel Task.Run ne se traduit que par une planification supplémentaire inutile Thread Pool. Même si le code prévu bloquerait un thread, Task.Run n’empêche pas cela.

**Faire**:

* Rendre [les chemins de code chaud](#understand-hot-code-paths) asynchrones.
* Appelez l’accès aux données, I/O, et les opérations de longue durée API asynchrone si une API asynchrone est disponible. N’utilisez **pas** [Task.Run](/dotnet/api/system.threading.tasks.task.run) pour faire une API synchronisée asynchrone.
* Rendre les actions de contrôleur/Razor Page asynchrones. La pile d’appel entière est asynchrone afin de bénéficier de [modèles async/await.](/dotnet/csharp/programming-guide/concepts/async/)

Un profileur, tel que [PerfView](https://github.com/Microsoft/perfview), peut être utilisé pour trouver des fils fréquemment ajoutés à la [piscine thread](/windows/desktop/procthread/thread-pools). L’événement `Microsoft-Windows-DotNETRuntime/ThreadPoolWorkerThread/Start` indique un fil ajouté au pool de threads. <!--  For more information, see [async guidance docs](TBD-Link_To_Davifowl_Doc)  -->

## <a name="minimize-large-object-allocations"></a>Minimiser les allocations d’objets importants

Le [collecteur d’ordures .NET Core](/dotnet/standard/garbage-collection/) gère automatiquement l’allocation et la libération de mémoire dans ASP.NET applications Core. La collecte automatique des ordures signifie généralement que les développeurs n’ont pas besoin de s’inquiéter de la façon dont ou quand la mémoire est libérée. Cependant, le nettoyage des objets non référés prend du temps CPU, de sorte que les développeurs devraient minimiser l’attribution des objets dans [les chemins de code chaud](#understand-hot-code-paths). La collecte des ordures est particulièrement coûteuse sur les gros objets (> 85 octets K). Les gros objets sont stockés sur le [grand tas d’objets](/dotnet/standard/garbage-collection/large-object-heap) et nécessitent une collecte complète (de génération 2) des ordures pour nettoyer. Contrairement aux collections de génération 0 et de génération 1, une collection de génération 2 nécessite une suspension temporaire de l’exécution de l’application. L’attribution et la désémerssation fréquentes de gros objets peuvent entraîner des performances incohérentes.

Recommandations :

* **Envisagez de** mettre en cache de gros objets qui sont fréquemment utilisés. La mise en cache de gros objets empêche les allocations coûteuses.
* **Faites des** tampons de piscine en utilisant un [>ArrayPool\<T](/dotnet/api/system.buffers.arraypool-1) pour stocker de grands tableaux.
* **N’allouez pas** beaucoup, de courte durée de grands objets sur [les chemins de code chaud](#understand-hot-code-paths).

Les problèmes de mémoire, comme le précédent, peuvent être diagnostiqués en examinant les statistiques de collecte des ordures (GC) dans [PerfView](https://github.com/Microsoft/perfview) et en examinant :

* La collecte des ordures heure de pause.
* Quel pourcentage du temps de traitement est consacré à la collecte des ordures.
* Combien de collectes d’ordures sont des générations 0, 1 et 2.

Pour plus d’informations, voir [Collection d’ordures et performance](/dotnet/standard/garbage-collection/performance).

## <a name="optimize-data-access-and-io"></a>Optimiser l’accès aux données et I/O

Les interactions avec un magasin de données et d’autres services distants sont souvent les parties les plus lentes d’une application ASP.NET Core. La lecture et l’écriture efficace des données sont essentielles pour obtenir de bonnes performances.

Recommandations :

* **Appelez** toutes les API d’accès aux données de manière asynchrone.
* **Ne récupérez pas** plus de données que nécessaire. Écrivez des requêtes pour retourner uniquement les données nécessaires à la demande HTTP en cours.
* **Envisagez de** mettre en cache les données fréquemment accessibles extraites d’une base de données ou d’un service à distance si des données légèrement périmées sont acceptables. Selon le scénario, utilisez un [MemoryCache](xref:performance/caching/memory) ou un [DistributedCache](xref:performance/caching/distributed). Pour plus d’informations, consultez <xref:performance/caching/response>.
* **Minimisez** les allers-retours en réseau. L’objectif est de récupérer les données requises en un seul appel plutôt que plusieurs appels.
* **Utilisez** des [requêtes sans suivi](/ef/core/querying/tracking#no-tracking-queries) dans Entity Framework Core lors de l’accès aux données à des fins de lecture uniquement. EF Core peut retourner les résultats des requêtes sans suivi plus efficacement.
* **Filtrez** et agrégent les `.Where` `.Select`requêtes `.Sum` LINQ (avec, ou les relevés, par exemple) afin que le filtrage soit effectué par la base de données.
* **Considérez** que EF Core résout certains opérateurs de requête sur le client, ce qui peut conduire à une exécution inefficace des requêtes. Pour plus d’informations, voir [les problèmes de performance de l’évaluation des clients](/ef/core/querying/client-eval#client-evaluation-performance-issues).
* **N’utilisez pas** de requêtes de projection sur les collections, ce qui peut entraîner l’exécution de requêtes SQL « N et 1 ». Pour plus d’informations, voir [Optimisation des sous-ques corrélées](/ef/core/what-is-new/ef-core-2.1#optimization-of-correlated-subqueries).

Voir [EF High Performance](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries) pour les approches qui peuvent améliorer les performances dans les applications à haute échelle:

* [Regroupement DbContext](/ef/core/what-is-new/ef-core-2.0#dbcontext-pooling)
* [Requêtes compilées explicitement](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries)

Nous vous recommandons de mesurer l’impact des approches de haute performance précédentes avant d’engager la base de code. La complexité supplémentaire des requêtes compilées peut ne pas justifier l’amélioration du rendement.

Les problèmes de requête peuvent être détectés en examinant le temps passé à accéder aux données avec [Application Insights](/azure/application-insights/app-insights-overview) ou avec des outils de profilage. La plupart des bases de données rendent également disponibles des statistiques concernant les requêtes fréquemment exécutées.

## <a name="pool-http-connections-with-httpclientfactory"></a>Connexions POOL HTTP avec HttpClientFactory

Bien que [HttpClient](/dotnet/api/system.net.http.httpclient) implémente l’interface, `IDisposable` il est conçu pour la réutilisation. Les `HttpClient` cas fermés laissent `TIME_WAIT` les prises ouvertes dans l’état pendant une courte période de temps. Si un chemin de code `HttpClient` qui crée et dispose d’objets est fréquemment utilisé, l’application peut épuiser les prises disponibles. [HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) a été présenté dans ASP.NET Core 2.1 comme solution à ce problème. Il gère la mise en commun des connexions HTTP pour optimiser les performances et la fiabilité.

Recommandations :

* **Ne créez pas** `HttpClient` et ne disposez pas directement d’instances.
* **Utilisez** [HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) pour `HttpClient` récupérer les instances. Pour plus d’informations, consultez [Utiliser HttpClientFactory pour implémenter des requêtes HTTP résilientes](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests).

## <a name="keep-common-code-paths-fast"></a>Maintenir les chemins de code communs rapidement

Vous voulez que tout votre code soit rapide. Les chemins de code fréquemment appelés sont les plus critiques à optimiser. notamment :

* Les composants Middleware dans le pipeline de traitement de la demande de l’application, en particulier middleware fonctionner tôt dans le pipeline. Ces composants ont un impact important sur les performances.
* Code exécuté pour chaque demande ou plusieurs fois par demande. Par exemple, l’enregistrement personnalisé, les gestionnaires d’autorisations ou l’initialisation de services transitoires.

Recommandations :

* **N’utilisez pas** de composants middleware personnalisés avec des tâches de longue durée.
* **Utilisez des** outils de profilage de performance, tels que [Visual Studio Diagnostic Tools](/visualstudio/profiling/profiling-feature-tour) ou [PerfView](https://github.com/Microsoft/perfview)), pour identifier les [chemins de code chaud](#understand-hot-code-paths).

## <a name="complete-long-running-tasks-outside-of-http-requests"></a>Effectuer des tâches de longue durée en dehors des demandes HTTP

La plupart des demandes adressées à une application ASP.NET Core peuvent être traitées par un contrôleur ou un modèle de page appelant les services nécessaires et le retour d’une réponse HTTP. Pour certaines demandes qui impliquent des tâches de longue durée, il est préférable de rendre l’ensemble du processus de demande-réponse asynchrone.

Recommandations :

* **N’attendez pas** que les tâches de longue durée se terminent dans le cadre du traitement de demande HTTP ordinaire.
* **Envisagez de** traiter des demandes de longue durée avec [des services d’arrière-plan](xref:fundamentals/host/hosted-services) ou hors processus avec une [fonction Azure](/azure/azure-functions/). L’exécution du travail hors processus est particulièrement bénéfique pour les tâches à forte intensité de processeur.
* **Utilisez** des options de communication [SignalR](xref:signalr/introduction)en temps réel, telles que, pour communiquer avec les clients de façon asynchrone.

## <a name="minify-client-assets"></a>Minifier les actifs des clients

ASP.NET applications Core avec des extrémités avant complexes servent fréquemment de nombreux fichiers JavaScript, CSS ou image. Les performances des demandes initiales de charge peuvent être améliorées par :

* Groupage, qui combine plusieurs fichiers en un seul.
* Minification, ce qui réduit la taille des fichiers en supprimant l’espace blanc et les commentaires.

Recommandations :

* **Utilisez ASP.NET** [support intégré](xref:client-side/bundling-and-minification) de Core pour regrouper et minifier les actifs des clients.
* **Considérez d’autres** outils tiers, tels que [Webpack](https://webpack.js.org/), pour la gestion complexe d’actifs des clients.

## <a name="compress-responses"></a>Réponses compresses

 Réduire la taille de la réponse augmente généralement la réactivité d’une application, souvent de façon spectaculaire. Une façon de réduire la taille de la charge utile est de compresser les réponses d’une application. Pour plus d’informations, voir [Compression Réponse](xref:performance/response-compression).

## <a name="use-the-latest-aspnet-core-release"></a>Utilisez la dernière version ASP.NET Core

Chaque nouvelle version de ASP.NET Core comprend des améliorations de performance. Les optimisations dans .NET Core et ASP.NET Core signifient que les versions plus nouvelles surpassent généralement les versions plus anciennes. Par exemple, .NET Core 2.1 a ajouté un soutien pour les expressions régulières compilées et a bénéficié de [Span\<T>](https://msdn.microsoft.com/magazine/mt814808.aspx). ASP.NET Core 2.2 a ajouté un soutien pour HTTP/2. [ASP.NET Core 3.0 ajoute de nombreuses améliorations](xref:aspnetcore-3.0) qui réduisent l’utilisation de la mémoire et améliorent le débit. Si les performances sont une priorité, envisagez de passer à la version actuelle de ASP.NET Core.

## <a name="minimize-exceptions"></a>Minimiser les exceptions

Les exceptions devraient être rares. Le lancement et la capture d’exceptions sont lents par rapport à d’autres modèles de flux de code. Pour cette raison, les exceptions ne devraient pas être utilisées pour contrôler le flux normal du programme.

Recommandations :

* **N’utilisez pas** de lancer ou de capture d’exceptions comme moyen de flux de programme normal, en particulier dans [les chemins de code chaud](#understand-hot-code-paths).
* **Inclure** la logique dans l’application pour détecter et gérer les conditions qui causeraient une exception.
* **Faites** des exceptions de lancer ou d’attraper pour des conditions inhabituelles ou inattendues.

Les outils de diagnostic d’applications, tels que Application Insights, peuvent aider à identifier les exceptions courantes dans une application qui peuvent affecter les performances.

## <a name="performance-and-reliability"></a>Performances et fiabilité

Les sections suivantes fournissent des conseils de performance et des problèmes et solutions de fiabilité connus.

## <a name="avoid-synchronous-read-or-write-on-httprequesthttpresponse-body"></a>Évitez de lire ou d’écrire synchrone sur httpRequest/HttpResponse corps

Tout I/O dans ASP.NET Core est asynchrone. Les serveurs `Stream` implémentent l’interface, qui a des surcharges synchrones et asynchrones. Les asynchrones doivent être préférés pour éviter de bloquer les fils de piscine de fil. Le blocage des fils peut conduire à la famine de piscine de fil.

**Ne faites pas ceci :** L’exemple suivant <xref:System.IO.StreamReader.ReadToEnd*>utilise le . Il bloque le fil actuel pour attendre le résultat. Il s’agit d’un exemple de [synchronisation sur async](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#warning-sync-over-async
).

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet1)]

Dans le code `Get` précédent, lit en synchronisation l’ensemble du corps de demande HTTP dans la mémoire. Si le client est lentement télécharger, l’application fait la synchronisation sur async. L’application ne synchroniser sur async parce que Kestrel ne prend **pas** en charge les lectures synchrones.

**Faites ceci :** L’exemple <xref:System.IO.StreamReader.ReadToEndAsync*> suivant utilise et ne bloque pas le thread pendant la lecture.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet2)]

Le code précédent lit asynchronement l’ensemble du corps de demande HTTP dans la mémoire.

> [!WARNING]
> Si la demande est grande, la lecture de l’ensemble du corps de demande HTTP dans la mémoire pourrait conduire à une condition hors de mémoire (OOM). OOM peut entraîner un refus de service.  Pour plus d’informations, consultez [La mémoire des grands organes de demande ou des organes de réponse](#arlb) dans ce document.

**Faites ceci :** L’exemple suivant est entièrement asynchrone à l’aide d’un organisme de demande non tamponné :

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet3)]

Le code précédent détonne asynchronement le corps de demande dans un objet C.

## <a name="prefer-readformasync-over-requestform"></a>Préférez ReadFormAsync sur Request.Form

Utilisez `HttpContext.Request.ReadFormAsync` à la place de `HttpContext.Request.Form`.
`HttpContext.Request.Form`ne peuvent être lus en toute sécurité qu’avec les conditions suivantes :

* Le formulaire a été lu `ReadFormAsync`par un appel à , et
* La valeur de formulaire mise en cache est lue à l’aide`HttpContext.Request.Form`

**Ne faites pas ceci :** L’exemple `HttpContext.Request.Form`suivant utilise .  `HttpContext.Request.Form`utilise [la synchronisation sur l’async](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#warning-sync-over-async
) et peut conduire à la famine de piscine de fil.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MySecondController.cs?name=snippet1)]

**Faites ceci :** L’exemple `HttpContext.Request.ReadFormAsync` suivant utilise pour lire le corps de forme asynchrone.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MySecondController.cs?name=snippet2)]

<a name="arlb"></a>

## <a name="avoid-reading-large-request-bodies-or-response-bodies-into-memory"></a>Évitez de lire les grands organes de demande ou les organes de réponse dans la mémoire

En .NET, chaque allocation d’objet supérieure à 85 KB se retrouve dans le tas de gros objets ([LOH](https://blogs.msdn.microsoft.com/maoni/2006/04/19/large-object-heap/)). Les gros objets sont chers de deux façons :

* Le coût d’allocation est élevé parce que la mémoire d’un gros objet nouvellement attribué doit être effacée. Le CLR garantit que la mémoire de tous les objets nouvellement attribués est effacée.
* LOH est recueilli avec le reste du tas. LOH nécessite une collecte complète [des ordures](/dotnet/standard/garbage-collection/fundamentals) ou une [collection Gen2.](/dotnet/standard/garbage-collection/fundamentals#generations)

Ce [billet de blog](https://adamsitnik.com/Array-Pool/#the-problem) décrit le problème succinctement:

> Lorsqu’un gros objet est attribué, il est marqué comme objet Gen 2. Pas Gen 0 comme pour les petits objets. Les conséquences sont que si vous êtes à court de mémoire dans LOH, GC nettoie l’ensemble du tas géré, pas seulement LOH. Ainsi, il nettoie Gen 0, Gen 1 et Gen 2 y compris LOH. C’est ce qu’on appelle la collecte complète des ordures et est la collecte d’ordures la plus longue. Pour de nombreuses applications, il peut être acceptable. Mais certainement pas pour les serveurs Web haute performance, où peu de grands tampons de mémoire sont nécessaires pour gérer une demande web moyenne (lire à partir d’une prise, décompresse, décoder JSON & plus).

Stockage naïvement d’un grand `byte[]` `string`organisme de demande ou de réponse en un seul ou :

* Peut entraîner rapidement à court d’espace dans le LOH.
* Peut causer des problèmes de performances pour l’application en raison de l’exécution complète des CPG.

## <a name="working-with-a-synchronous-data-processing-api"></a>Travailler avec une API de traitement des données synchrone

Lorsque vous utilisez un sérialisateur/dé-serializer qui ne prend en charge que les lectures et les écrits synchrones (par exemple, [JSON.NET)](https://www.newtonsoft.com/json/help/html/Introduction.htm):

* Tamponner les données dans la mémoire asynchronement avant de les passer dans le sérialisateur / dé-serializer.

> [!WARNING]
> Si la demande est importante, elle pourrait conduire à une condition hors mémoire (OOM). OOM peut entraîner un refus de service.  Pour plus d’informations, consultez [La mémoire des grands organes de demande ou des organes de réponse](#arlb) dans ce document.

ASP.NET Core 3.0 utilise <xref:System.Text.Json> par défaut pour la sérialisation JSON. <xref:System.Text.Json>:

* Lit et écrit JSON asynchronement.
* Est optimisé pour le texte UTF-8.
* Des performances `Newtonsoft.Json`généralement supérieures à .

## <a name="do-not-store-ihttpcontextaccessorhttpcontext-in-a-field"></a>Ne stockez pas IHttpContextAccessor.HttpContext dans un champ

[L’IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext) `HttpContext` retourne la demande active lorsqu’il est accessible à partir du fil de demande. Le `IHttpContextAccessor.HttpContext` **ne** doit pas être stocké dans un champ ou une variable.

**Ne faites pas ceci :** L’exemple suivant `HttpContext` stocke le dans un champ, puis tente de l’utiliser plus tard.

[!code-csharp[](performance-best-practices/samples/3.0/MyType.cs?name=snippet1)]

Le code précédent capture fréquemment `HttpContext` un null ou incorrect dans le constructeur.

**Faites ceci :** L’exemple suivant :

* Stocke <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> le dans un champ.
* Utilise `HttpContext` le champ au bon `null`moment et vérifie pour .

[!code-csharp[](performance-best-practices/samples/3.0/MyType.cs?name=snippet2)]

## <a name="do-not-access-httpcontext-from-multiple-threads"></a>N’accédez pas à HttpContext à partir de plusieurs threads

`HttpContext`*n’est PAS* sans fil. L’accès `HttpContext` à partir de plusieurs threads en parallèle peut entraîner un comportement indéfini comme les pendaisons, les plantages et la corruption des données.

**Ne faites pas ceci :** L’exemple suivant fait trois demandes parallèles et enregistre le chemin de demande entrant avant et après la demande http://adresse suivante. Le chemin de demande est accessible à partir de plusieurs threads, potentiellement en parallèle.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncFirstController.cs?name=snippet1&highlight=25,28)]

**Faites ceci :** L’exemple suivant copie toutes les données de la demande entrante avant de faire les trois demandes parallèles.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncFirstController.cs?name=snippet2&highlight=6,8,22,28)]

## <a name="do-not-use-the-httpcontext-after-the-request-is-complete"></a>N’utilisez pas le httpContexte une fois la demande terminée

`HttpContext`n’est valable que tant qu’il y a une demande http://HTTP active dans le pipeline ASP.NET Core. L’ensemble du pipeline ASP.NET Core est une chaîne asynchrone de délégués qui exécute chaque demande. Lorsque `Task` le retour de cette `HttpContext` chaîne se termine, le est recyclé.

**Ne faites pas ceci :** L’exemple `async void` suivant utilise qui rend la `await` demande HTTP complète lorsque la première est atteinte :

* Ce qui est **TOUJOURS** une mauvaise pratique dans ASP.NET applications Core.
* Accès après `HttpResponse` la demande HTTP est terminée.
* Crashes le processus.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncBadVoidController.cs?name=snippet1)]

**Faites ceci :** L’exemple suivant `Task` renvoie un dans le cadre, de sorte que la demande HTTP ne se termine pas jusqu’à ce que l’action se termine.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncSecondController.cs?name=snippet1)]

## <a name="do-not-capture-the-httpcontext-in-background-threads"></a>Ne capturez pas le httpContexte dans les fils de fond

**Ne faites pas ceci :** L’exemple suivant montre qu’une `Controller` fermeture est la capture de la `HttpContext` propriété. Il s’agit d’une mauvaise pratique parce que l’élément de travail pourrait:

* Exécuter en dehors de la portée de la demande.
* Tentative de lire `HttpContext`le mauvais .

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetFirstController.cs?name=snippet1)]

**Faites ceci :** L’exemple suivant :

* Copie des données requises dans la tâche d’arrière-plan pendant la demande.
* Ne fait référence à rien du contrôleur.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetFirstController.cs?name=snippet2)]

Les tâches d’arrière-plan doivent être mises en œuvre en tant que services hébergés. Pour plus d’informations, consultez [Tâches en arrière-plan avec services hébergés](xref:fundamentals/host/hosted-services).

## <a name="do-not-capture-services-injected-into-the-controllers-on-background-threads"></a>Ne pas capturer les services injectés dans les contrôleurs sur les fils de fond

**Ne faites pas ceci :** L’exemple suivant montre qu’une fermeture capte le `DbContext` paramètre d’action. `Controller` C’est une mauvaise pratique.  L’élément de travail pourrait s’exécuter en dehors de la portée de la demande. Le `ContosoDbContext` est visé à la demande, résultant en un `ObjectDisposedException`.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet1)]

**Faites ceci :** L’exemple suivant :

* Injecte <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> un afin de créer une portée dans l’élément de travail d’arrière-plan. `IServiceScopeFactory`est un singleton.
* Crée une nouvelle portée d’injection de dépendance dans le fil de fond.
* Ne fait référence à rien du contrôleur.
* Ne saisit `ContosoDbContext` pas la demande de la demande entrante.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet2)]

Le code mis en évidence ci-après:

* Crée une portée pour la durée de vie de l’opération de fond et résout les services de celui-ci.
* Utilise `ContosoDbContext` à partir de la portée correcte.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet2&highlight=9-16)]

## <a name="do-not-modify-the-status-code-or-headers-after-the-response-body-has-started"></a>Ne modifiez pas le code d’état ou les en-têtes après le début du corps de réponse

ASP.NET Core ne tamponne pas l’organisme de réponse HTTP. La première fois que la réponse est écrite :

* Les en-têtes sont envoyés avec ce morceau du corps au client.
* Il n’est plus possible de changer les en-têtes de réponse.

**Ne faites pas ceci :** Le code suivant tente d’ajouter des en-têtes de réponse après que la réponse a déjà commencé :

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet1)]

Dans le code `context.Response.Headers["test"] = "test value";` précédent, jetez une exception si `next()` elle a écrit à la réponse.

**Faites ceci :** L’exemple suivant vérifie si la réponse HTTP a commencé avant de modifier les en-têtes.

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet2)]

**Faites ceci :** L’exemple `HttpResponse.OnStarting` suivant sert à définir les en-têtes avant que les en-têtes de réponse ne soient rincés vers le client.

Vérifier si la réponse n’a pas commencé permet d’enregistrer un rappel qui sera invoqué juste avant que les en-têtes de réponse soient écrits. Vérifier si la réponse n’a pas commencé :

* Fournit la possibilité d’appendice ou de remplacer les en-têtes juste à temps.
* Il n’est pas nécessaire de connaître le prochain middleware dans le pipeline.

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet3)]

## <a name="do-not-call-next-if-you-have-already-started-writing-to-the-response-body"></a>N’appelez pas le prochain() si vous avez déjà commencé à écrire à l’organisme de réponse

Les composants ne s’attendent à être appelés que s’il est possible pour eux de manipuler et de manipuler la réponse.
