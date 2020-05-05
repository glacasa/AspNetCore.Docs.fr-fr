---
title: Tests d’intégration dans ASP.NET Core
author: rick-anderson
description: Découvrez comment les tests d’intégration garantissent le bon fonctionnement des composants d’une application au niveau de l’infrastructure, notamment la base de données, le système de fichiers et le réseau.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/06/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: test/integration-tests
ms.openlocfilehash: a01d2881133f39c1a26e4724ae25336c54746bc5
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82766392"
---
# <a name="integration-tests-in-aspnet-core"></a>Tests d’intégration dans ASP.NET Core

Par [Javier Calvarro Nelson](https://github.com/javiercn), [Steve Smith](https://ardalis.com/)et [Jos van der til](https://jvandertil.nl)

::: moniker range=">= aspnetcore-3.0"

Les tests d’intégration garantissent que les composants d’une application fonctionnent correctement à un niveau qui comprend l’infrastructure de prise en charge de l’application, telle que la base de données, le système de fichiers et le réseau. ASP.NET Core prend en charge les tests d’intégration à l’aide d’une infrastructure de tests unitaires avec un hôte Web de test et un serveur de test en mémoire.

Cette rubrique suppose une compréhension de base des tests unitaires. Si vous n’êtes pas familiarisé avec les concepts de test, consultez la rubrique [tests unitaires dans .net Core et .NET standard](/dotnet/core/testing/) et son contenu lié.

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

L’exemple d’application est une application Razor Pages et suppose une compréhension de base des Razor Pages. Si vous n’êtes pas familiarisé avec Razor Pages, consultez les rubriques suivantes :

* [Présentation des pages Razor](xref:razor-pages/index)
* [Bien démarrer avec les pages Razor](xref:tutorials/razor-pages/razor-pages-start)
* [Tests unitaires Pages Razor](xref:test/razor-pages-tests)

> [!NOTE]
> Pour tester la SPAs, nous vous recommandons d’utiliser un outil tel que le [sélénium](https://www.seleniumhq.org/), qui peut automatiser un navigateur.

## <a name="introduction-to-integration-tests"></a>Présentation des tests d’intégration

Les tests d’intégration évaluent les composants d’une application à un niveau plus large que les [tests unitaires](/dotnet/core/testing/). Les tests unitaires sont utilisés pour tester des composants logiciels isolés, tels que des méthodes de classe individuelles. Les tests d’intégration confirment que deux ou plusieurs composants d’application fonctionnent ensemble pour produire un résultat attendu, y compris éventuellement chaque composant requis pour traiter entièrement une demande.

Ces tests plus larges sont utilisés pour tester l’infrastructure de l’application et l’infrastructure entière, y compris les composants suivants :

* Base de données
* Système de fichiers
* Appliances réseau
* Pipeline de requête-réponse

Les tests unitaires utilisent des composants fabriqués, appelés objets *substituts* ou *factices*, à la place des composants d’infrastructure.

Contrairement aux tests unitaires, les tests d’intégration :

* Utilisez les composants réels utilisés par l’application en production.
* Nécessitent davantage de code et de traitement des données.
* Prend plus de temps pour s’exécuter.

Par conséquent, limitez l’utilisation des tests d’intégration aux scénarios d’infrastructure les plus importants. Si un comportement peut être testé à l’aide d’un test unitaire ou d’un test d’intégration, choisissez le test unitaire.

> [!TIP]
> N’écrivez pas de tests d’intégration pour chaque permutation possible d’accès aux données et aux fichiers avec les bases de données et les systèmes de fichiers. Quel que soit le nombre d’emplacements au sein d’une application qui interagissent avec les bases de données et les systèmes de fichiers, un ensemble de tests d’intégration de lecture, d’écriture, de mise à jour et de suppression est généralement en mesure de tester correctement les composants de base de données et de système de fichiers. Utilisez des tests unitaires pour les tests de routine de la logique de méthode qui interagissent avec ces composants. Dans les tests unitaires, l’utilisation de substituts ou de simulacres d’infrastructure entraîne une exécution plus rapide des tests.

> [!NOTE]
> Dans les discussions sur les tests d’intégration, le projet testé est fréquemment appelé le *système en cours de test*ou « St » pour l’instant.
>
> *« St » est utilisé dans cette rubrique pour faire référence à l’application ASP.NET Core testée.*

## <a name="aspnet-core-integration-tests"></a>ASP.NET Core les tests d’intégration

Les tests d’intégration dans ASP.NET Core requièrent les éléments suivants :

* Un projet de test est utilisé pour contenir et exécuter les tests. Le projet de test a une référence à St.
* Le projet de test crée un hôte Web de test pour le St et utilise un client de serveur de test pour gérer les demandes et les réponses avec le St.
* Un testeur de test est utilisé pour exécuter les tests et signaler les résultats des tests.

Les tests d’intégration suivent une séquence d’événements qui incluent les étapes de test *arrange*, *Act*et *Assert* habituelles :

1. L’hôte Web de St est configuré.
1. Un client de serveur de test est créé pour envoyer des demandes à l’application.
1. L’étape *organiser* le test est exécutée : l’application de test prépare une demande.
1. L’étape de test *Act* est exécutée : le client envoie la demande et reçoit la réponse.
1. L’étape de test d' *assertion* est exécutée : la réponse *réelle* est validée en tant que *réussite* ou *échec* en fonction d’une réponse *attendue* .
1. Le processus se poursuit jusqu’à ce que tous les tests soient exécutés.
1. Les résultats des tests sont signalés.

En règle générale, l’hôte Web de test est configuré différemment de l’hôte Web normal de l’application pour les séries de tests. Par exemple, une base de données différente ou des paramètres d’application différents peuvent être utilisés pour les tests.

Les composants d’infrastructure, tels que l’hôte Web de test et le serveur de test en mémoire ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)), sont fournis ou gérés par le package [Microsoft. AspNetCore. Mvc. testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) . L’utilisation de ce package simplifie la création et l’exécution des tests.

Le `Microsoft.AspNetCore.Mvc.Testing` package gère les tâches suivantes :

* Copie le fichier de dépendances (*. DEPS*) à partir du St dans le répertoire *bin* du projet de test.
* Définit la racine du [contenu](xref:fundamentals/index#content-root) sur la racine du projet St pour que les fichiers et les pages statiques soient trouvés lors de l’exécution des tests.
* Fournit la classe [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) pour simplifier l’amorçage du St avec `TestServer`.

La documentation sur les [tests unitaires](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) décrit comment configurer un projet de test et Test Runner, ainsi que des instructions détaillées sur la façon d’exécuter des tests et des recommandations sur la façon de nommer des tests et des classes de test.

> [!NOTE]
> Lorsque vous créez un projet de test pour une application, séparez les tests unitaires des tests d’intégration dans différents projets. Cela permet de s’assurer que les composants de test d’infrastructure ne sont pas accidentellement inclus dans les tests unitaires. La séparation des tests d’unité et d’intégration permet également de contrôler l’ensemble des tests exécutés.

Il n’existe pratiquement aucune différence entre la configuration des tests d’applications Razor Pages et les applications MVC. La seule différence réside dans la façon dont les tests sont nommés. Dans une application Razor Pages, les tests des points de terminaison de page sont généralement nommés après la classe de modèle de `IndexPageTests` page (par exemple, pour tester l’intégration des composants pour la page d’index). Dans une application MVC, les tests sont généralement organisés par classes de contrôleur et nommés après les contrôleurs testés ( `HomeControllerTests` par exemple, pour tester l’intégration des composants pour le contrôleur de démarrage).

## <a name="test-app-prerequisites"></a>Conditions préalables pour les applications de test

Le projet de test doit :

* Référencez le package [Microsoft. AspNetCore. Mvc. testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) .
* Spécifiez le kit de développement logiciel (SDK`<Project Sdk="Microsoft.NET.Sdk.Web">`) Web dans le fichier projet ().

Ces conditions préalables peuvent être consultées dans l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/). Inspectez le fichier *tests/RazorPagesProject. tests/RazorPagesProject. tests. csproj* . L’exemple d’application utilise l’infrastructure de test [xUnit](https://xunit.github.io/) et la bibliothèque de l’analyseur [AngleSharp](https://anglesharp.github.io/) , de sorte que l’exemple d’application référence également :

* [xunit](https://www.nuget.org/packages/xunit)
* [xUnit. Runner. VisualStudio](https://www.nuget.org/packages/xunit.runner.visualstudio)
* [AngleSharp](https://www.nuget.org/packages/AngleSharp)

Entity Framework Core est également utilisé dans les tests. L’application référence :

* [Microsoft. AspNetCore. Diagnostics. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore)
* [Microsoft. AspNetCore. Identity. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity.EntityFrameworkCore)
* [Microsoft.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore)
* [Microsoft.EntityFrameworkCore.InMemory](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory)
* [Microsoft. EntityFrameworkCore. Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools)

## <a name="sut-environment"></a>Environnement St

Si l' [environnement](xref:fundamentals/environments) de St n’est pas défini, l’environnement est par défaut développement.

## <a name="basic-tests-with-the-default-webapplicationfactory"></a>Tests de base avec le WebApplicationFactory par défaut

[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) est utilisé pour créer un [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) pour les tests d’intégration. `TEntryPoint`est la classe de point d’entrée de St, généralement `Startup` la classe.

Les classes de test implémentent une interface de *contexte de classe* ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) pour indiquer que la classe contient des tests et fournir des instances d’objet partagé dans les tests de la classe.

La classe de test suivante `BasicTests`,, utilise `WebApplicationFactory` le pour démarrer le St et fournir un [httpclient](/dotnet/api/system.net.http.httpclient) à une méthode de `Get_EndpointsReturnSuccessAndCorrectContentType`test,. La méthode vérifie si le code d’état de réponse a réussi (codes d’État dans la plage 200-299 `Content-Type` ) et `text/html; charset=utf-8` si l’en-tête est pour plusieurs pages d’application.

[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) crée une instance de `HttpClient` qui suit automatiquement les redirections et gère les cookies.

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

Par défaut, les cookies non essentiels ne sont pas conservés entre les demandes lorsque la [stratégie de consentement RGPD](xref:security/gdpr) est activée. Pour conserver les cookies non essentiels, tels que ceux utilisés par le fournisseur TempData, marquez-les comme essentiels dans vos tests. Pour obtenir des instructions sur le marquage d’un cookie comme étant essentiel, consultez [les cookies essentiels](xref:security/gdpr#essential-cookies).

## <a name="customize-webapplicationfactory"></a>Personnaliser WebApplicationFactory

La configuration d’hôte Web peut être créée indépendamment des classes de test en héritant de `WebApplicationFactory` pour créer une ou plusieurs fabriques personnalisées :

1. Hériter `WebApplicationFactory` de et substituer [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost). [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) permet la configuration de la collection de services avec [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices):

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   L’amorçage de base de données dans l' [exemple](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) d’application `InitializeDbForTests` est effectué par la méthode. La méthode est décrite dans la section [exemple de tests d’intégration : organisation](#test-app-organization) d’une application de test.

   Le contexte de base de données de St est `Startup.ConfigureServices` inscrit dans sa méthode. Le rappel de l' `builder.ConfigureServices` application de test est exécuté *après* l' `Startup.ConfigureServices` exécution du code de l’application. L’ordre d’exécution est une modification avec rupture pour l' [hôte générique](xref:fundamentals/host/generic-host) avec la version de ASP.net Core 3,0. Pour utiliser une base de données différente pour les tests que la base de données de l’application, le contexte de base `builder.ConfigureServices`de données de l’application doit être remplacé dans.

   L’exemple d’application recherche le descripteur de service pour le contexte de base de données et utilise le descripteur pour supprimer l’inscription du service. Ensuite, la fabrique ajoute un nouveau `ApplicationDbContext` qui utilise une base de données en mémoire pour les tests.

   Pour vous connecter à une base de données différente de celle de la base de `UseInMemoryDatabase` données en mémoire, modifiez l’appel pour connecter le contexte à une autre base de données. Pour utiliser une base de données de test SQL Server :

   * Référencez le package NuGet [Microsoft. EntityFrameworkCore. SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/) dans le fichier projet.
   * Appelez `UseSqlServer` avec une chaîne de connexion à la base de données.

   ```csharp
   services.AddDbContext<ApplicationDbContext>((options, context) => 
   {
       context.UseSqlServer(
           Configuration.GetConnectionString("TestingDbConnectionString"));
   });
   ```

2. Utilisez le personnalisé `CustomWebApplicationFactory` dans les classes de test. L’exemple suivant utilise la fabrique dans la `IndexPageTests` classe :

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   Le client de l’exemple d’application est configuré pour `HttpClient` empêcher la suite de redirections. Comme expliqué plus loin dans la section [d’authentification des simulacres](#mock-authentication) , cela permet aux tests de vérifier le résultat de la première réponse de l’application. La première réponse est une redirection dans un grand nombre de ces tests `Location` avec un en-tête.

3. Un test standard utilise les `HttpClient` méthodes d’assistance et pour traiter la demande et la réponse :

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

Toute demande de publication à l’St doit être conforme à la vérification anti-contrefaçon effectuée automatiquement par le [système anti-contrefaçon de protection des données](xref:security/data-protection/introduction)de l’application. Pour organiser la requête de publication d’un test, l’application de test doit :

1. Effectuez une demande pour la page.
1. Analysez le cookie anti-contrefaçon et le jeton de validation de demande à partir de la réponse.
1. Effectuez la demande de publication avec le cookie anti-contrefaçon et le jeton de validation de demande en place.

Les `SendAsync` méthodes d’extension d’assistance *(helpers/HttpClientExtensions. cs*) et `GetDocumentAsync` la méthode d’assistance (*helpers/HtmlHelper. cs*) dans l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) utilisent l’analyseur [AngleSharp](https://anglesharp.github.io/) pour gérer la vérification anti-falsification avec les méthodes suivantes :

* `GetDocumentAsync`&ndash; Reçoit le [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) et retourne un `IHtmlDocument`. `GetDocumentAsync`utilise une fabrique qui prépare une *réponse virtuelle* en fonction de l’original `HttpResponseMessage`. Pour plus d’informations, consultez la [documentation AngleSharp](https://github.com/AngleSharp/AngleSharp#documentation).
* `SendAsync`les `HttpClient` méthodes d’extension pour compose un [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) et appellent [SendAsync (HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) pour envoyer des demandes au St. Les surcharges `SendAsync` pour acceptent le formulaire HTML`IHtmlFormElement`() et les éléments suivants :
  * Bouton envoyer du formulaire (`IHtmlElement`)
  * Collection de valeurs de`IEnumerable<KeyValuePair<string, string>>`formulaire ()
  * Bouton envoyer (`IHtmlElement`) et valeurs de formulaire`IEnumerable<KeyValuePair<string, string>>`()

> [!NOTE]
> [AngleSharp](https://anglesharp.github.io/) est une bibliothèque d’analyse tierce utilisée à des fins de démonstration dans cette rubrique et l’exemple d’application. AngleSharp n’est pas pris en charge ou requis pour le test d’intégration des applications ASP.NET Core. D’autres analyseurs peuvent être utilisés, tels que le [HAP (html agilité Pack)](https://html-agility-pack.net/). Une autre approche consiste à écrire du code pour gérer directement le jeton de vérification de demande et le cookie anti-contrefaçon du système anti-contrefaçon.

## <a name="customize-the-client-with-withwebhostbuilder"></a>Personnaliser le client avec WithWebHostBuilder

Quand une configuration supplémentaire est requise dans une méthode de test, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) crée `WebApplicationFactory` un nouveau avec un [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) qui est ensuite personnalisé par la configuration.

La `Post_DeleteMessageHandler_ReturnsRedirectToRoot` méthode de test de l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) montre l' `WithWebHostBuilder`utilisation de. Ce test effectue une suppression d’enregistrement dans la base de données en déclenchant une soumission de formulaire dans St.

Étant donné qu’un autre `IndexPageTests` test de la classe effectue une opération qui supprime tous les enregistrements de la base de données et peut `Post_DeleteMessageHandler_ReturnsRedirectToRoot` s’exécuter avant la méthode, la base de données est réamorcée dans cette méthode de test pour s’assurer qu’un enregistrement est présent pour que le St puisse être supprimé. La sélection du premier bouton supprimer du `messages` formulaire dans le St est simulée dans la requête adressée au St :

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a>Options du client

Le tableau suivant indique les [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) par défaut disponibles lors `HttpClient` de la création d’instances.

| Option | Description | Default |
| ------ | ----------- | ------- |
| [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | Obtient ou définit une valeur indiquant `HttpClient` si les instances de doivent suivre automatiquement les réponses de redirection. | `true` |
| [BaseAddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | Obtient ou définit l’adresse de base `HttpClient` des instances. | `http://localhost` |
| [HandleCookies](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | Obtient ou définit une `HttpClient` valeur indiquant si les instances doivent gérer les cookies. | `true` |
| [MaxAutomaticRedirections](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | Obtient ou définit le nombre maximal de réponses de redirection `HttpClient` que les instances doivent suivre. | 7 |

Créez la `WebApplicationFactoryClientOptions` classe et transmettez-la à la méthode [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) (les valeurs par défaut sont affichées dans l’exemple de code) :

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a>Injecter des services fictifs

Les services peuvent être substitués dans un test à l’aide d’un appel à [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) sur le générateur de l’hôte. **Pour injecter des services fictifs, St doit `Startup` avoir une classe `Startup.ConfigureServices` avec une méthode.**

L’exemple de St comprend un service étendu qui retourne un guillemet. Le guillemet est incorporé dans un champ masqué sur la page d’index lorsque la page d’index est demandée.

*Services/IQuoteService. cs*:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

*Services/QuoteService. cs*:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

*Pages/Index.cshtml.cs* :

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

*Pages/index. cs*:

[!code-cshtml[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

Le balisage suivant est généré lors de l’exécution de l’application St :

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

Pour tester l’injection de service et de guillemets dans un test d’intégration, un service fictif est injecté dans le St par le test. Le service factice remplace l’application `QuoteService` par un service fourni par l’application de test, appelé `TestQuoteService`:

*IntegrationTests.IndexPageTests.cs*:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

`ConfigureTestServices`est appelé, et le service étendu est inscrit :

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

Le balisage produit pendant l’exécution du test reflète le texte du devis `TestQuoteService`fourni par, donc l’assertion réussit :

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="mock-authentication"></a>Simuler l’authentification

Les tests de `AuthTests` la classe vérifient qu’un point de terminaison sécurisé :

* Redirige un utilisateur non authentifié vers la page de connexion de l’application.
* Retourne le contenu d’un utilisateur authentifié.

Dans St, la `/SecurePage` page utilise une convention [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) pour appliquer un [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) à la page. Pour plus d’informations, consultez [Razor pages conventions d’autorisation](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

Dans le `Get_SecurePageRedirectsAnUnauthenticatedUser` test, un [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) est défini pour interdire les redirections en affectant `false`à [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) la valeur :

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet2)]

En interautorisant le client à suivre la redirection, vous pouvez effectuer les vérifications suivantes :

* Le code d’état retourné par le St peut être vérifié par rapport au résultat [HttpStatusCode. Redirect](/dotnet/api/system.net.httpstatuscode) attendu, pas le code d’état final après la redirection vers la page de connexion, qui serait [HttpStatusCode. OK](/dotnet/api/system.net.httpstatuscode).
* La `Location` valeur d’en-tête dans les en-têtes de réponse est vérifiée `http://localhost/Identity/Account/Login`pour confirmer qu’elle commence par, et non par `Location` la réponse de page de connexion finale, où l’en-tête n’est pas présent.

L’application de test peut simuler un <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> dans [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) afin de tester les aspects de l’authentification et de l’autorisation. Un scénario minimal renvoie un [AuthenticateResult. Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*):

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet4&highlight=11-18)]

La `TestAuthHandler` méthode est appelée pour authentifier un utilisateur lorsque le schéma d’authentification `Test` est `AddAuthentication` défini sur où `ConfigureTestServices`est inscrit pour :

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet3&highlight=7-12)]

Pour plus d’informations `WebApplicationFactoryClientOptions`sur, consultez la section [Options du client](#client-options) .

## <a name="set-the-environment"></a>Définir l’environnement

Par défaut, l’environnement de l’hôte et de l’application de St est configuré pour utiliser l’environnement de développement. Pour remplacer l’environnement de St :

* Définissez la `ASPNETCORE_ENVIRONMENT` variable d’environnement (par exemple `Staging` `Production`,, ou une autre valeur personnalisée, telle `Testing`que).
* Remplacez `CreateHostBuilder` dans l’application de test pour lire les variables d’environnement précédées de `ASPNETCORE`.

```csharp
protected override IHostBuilder CreateHostBuilder() => 
    base.CreateHostBuilder()
        .ConfigureHostConfiguration(
            config => config.AddEnvironmentVariables("ASPNETCORE"));
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a>Comment l’infrastructure de test déduit le chemin racine du contenu de l’application

Le `WebApplicationFactory` constructeur déduit le chemin d' [accès racine du contenu](xref:fundamentals/index#content-root) de l’application en recherchant un [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) sur l’assembly contenant les tests d’intégration avec `TEntryPoint` une `System.Reflection.Assembly.FullName`clé égale à l’assembly. Si un attribut avec la clé correcte est introuvable `WebApplicationFactory` , revient à la recherche d’un fichier solution (*. sln*) et ajoute le `TEntryPoint` nom de l’assembly au répertoire de la solution. Le répertoire racine de l’application (chemin racine du contenu) est utilisé pour découvrir les affichages et les fichiers de contenu.

## <a name="disable-shadow-copying"></a>Désactiver les clichés instantanés

Les clichés instantanés provoquent l’exécution des tests dans un répertoire différent de celui du répertoire de sortie. Pour que les tests fonctionnent correctement, les clichés instantanés doivent être désactivés. L' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) utilise xUnit et désactive les clichés instantanés pour xUnit en incluant un fichier *xUnit. Runner. JSON* avec le paramètre de configuration correct. Pour plus d’informations, consultez [configuration de xUnit avec JSON](https://xunit.github.io/docs/configuring-with-json.html).

Ajoutez le fichier *xUnit. Runner. JSON* à la racine du projet de test avec le contenu suivant :

```json
{
  "shadowCopy": false
}
```

## <a name="disposal-of-objects"></a>Suppression d’objets

Après l’exécution des tests `IClassFixture` de l’implémentation, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) et [httpclient](/dotnet/api/system.net.http.httpclient) sont supprimés quand xUnit supprime le [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1). Si les objets instanciés par le développeur nécessitent une suppression, supprimez- `IClassFixture` les dans l’implémentation. Pour plus d’informations, consultez [implémentation d’une méthode dispose](/dotnet/standard/garbage-collection/implementing-dispose).

## <a name="integration-tests-sample"></a>Exemple de tests d’intégration

L' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) se compose de deux applications :

| Application | Répertoire du projet | Description |
| --- | ----------------- | ----------- |
| Application message (St) | *SRC/RazorPagesProject* | Permet à l’utilisateur d’ajouter, de supprimer, de supprimer et d’analyser des messages. |
| Tester une application | *tests/RazorPagesProject. tests* | Utilisé pour tester l’intégration du St. |

Les tests peuvent être exécutés à l’aide des fonctionnalités de test intégrées d’un environnement de développement intégré (IDE), telles que [Visual Studio](https://visualstudio.microsoft.com). Si vous utilisez [Visual Studio code](https://code.visualstudio.com/) ou la ligne de commande, exécutez la commande suivante à l’invite de commandes dans le répertoire *tests/RazorPagesProject. tests* :

```console
dotnet test
```

### <a name="message-app-sut-organization"></a>Organisation message App (St)

Le St est un système de messagerie Razor Pages avec les caractéristiques suivantes :

* La page d’index de l’application (*pages/index. cshtml* et *pages/index. cshtml. cs*) fournit une interface utilisateur et des méthodes de modèle de page pour contrôler l’ajout, la suppression et l’analyse de messages (mots moyens par message).
* Un message est décrit par la `Message` classe (*Data/message. cs*) avec deux propriétés : `Id` (Key) et `Text` (message). La `Text` propriété est obligatoire et limitée à 200 caractères.
* Les messages sont stockés à l’aide [de la base de données en mémoire de Entity Framework](/ef/core/providers/in-memory/)&#8224;.
* L’application contient une couche d’accès aux données (DAL) dans sa classe de `AppDbContext` contexte de base de données, (*Data/AppDbContext. cs*).
* Si la base de données est vide au démarrage de l’application, la Banque de messages est initialisée avec trois messages.
* L’application comprend un `/SecurePage` qui n’est accessible qu’à un utilisateur authentifié.

&#8224;la rubrique EF [test with InMemory](/ef/core/miscellaneous/testing/in-memory), explique comment utiliser une base de données en mémoire pour les tests avec MSTest. Cette rubrique utilise l’infrastructure de test [xUnit](https://xunit.github.io/) . Les concepts de test et les implémentations de test dans différents frameworks de test sont similaires, mais pas identiques.

Bien que l’application n’utilise pas le modèle de référentiel et ne soit pas un exemple efficace de [modèle d’unité de travail (UOW)](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor pages prend en charge ces modèles de développement. Pour plus d’informations, consultez [conception de la couche de persistance de l’infrastructure](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) et [logique du contrôleur de test](/aspnet/core/mvc/controllers/testing) (l’exemple implémente le modèle de référentiel).

### <a name="test-app-organization"></a>Organisation des applications de test

L’application de test est une application console dans le répertoire *tests/RazorPagesProject. tests* .

| Tester le répertoire de l’application | Description |
| ------------------ | ----------- |
| *AuthTests* | Contient des méthodes de test pour :<ul><li>Accès à une page sécurisée par un utilisateur non authentifié.</li><li>Accès à une page sécurisée par un utilisateur authentifié avec un simulacre <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>.</li><li>Obtention d’un profil utilisateur GitHub et vérification de la connexion de l’utilisateur du profil.</li></ul> |
| *BasicTests* | Contient une méthode de test pour le routage et le type de contenu. |
| *IntegrationTests* | Contient les tests d’intégration de la page d’index `WebApplicationFactory` à l’aide de la classe personnalisée. |
| *Programmes d’assistance/utilitaires* | <ul><li>*Utilities.cs* contient la `InitializeDbForTests` méthode utilisée pour amorcer la base de données avec des données de test.</li><li>*HtmlHelpers.cs* fournit une méthode pour retourner un AngleSharp `IHtmlDocument` pour une utilisation par les méthodes de test.</li><li>*HttpClientExtensions.cs* fournissent des surcharges `SendAsync` pour envoyer des demandes au St.</li></ul> |

L’infrastructure de test est [xUnit](https://xunit.github.io/). Les tests d’intégration sont effectués à l’aide de [Microsoft. AspNetCore. TestHost](/dotnet/api/microsoft.aspnetcore.testhost), qui comprend le [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver). Étant donné que le package [Microsoft. AspNetCore. Mvc. testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) est utilisé pour configurer l’hôte de test et le `TestHost` serveur `TestServer` de test, les packages et ne nécessitent pas de références de package directes dans le fichier projet ou la configuration de développeur de l’application de test dans l’application de test.

**Amorçage de la base de données à des fins de test**

Les tests d’intégration nécessitent généralement un petit jeu de données dans la base de données avant l’exécution du test. Par exemple, un test Delete requiert la suppression d’un enregistrement de base de données, de sorte que la base de données doit avoir au moins un enregistrement pour que la demande Delete aboutisse.

L’exemple d’application amorce la base de données avec trois messages dans *Utilities.cs* que les tests peuvent utiliser lorsqu’ils exécutent :

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

Le contexte de base de données de St est `Startup.ConfigureServices` inscrit dans sa méthode. Le rappel de l' `builder.ConfigureServices` application de test est exécuté *après* l' `Startup.ConfigureServices` exécution du code de l’application. Pour utiliser une base de données différente pour les tests, le contexte de base de données de `builder.ConfigureServices`l’application doit être remplacé dans. Pour plus d’informations, consultez la section [personnaliser WebApplicationFactory](#customize-webapplicationfactory) .

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Les tests d’intégration garantissent que les composants d’une application fonctionnent correctement à un niveau qui comprend l’infrastructure de prise en charge de l’application, telle que la base de données, le système de fichiers et le réseau. ASP.NET Core prend en charge les tests d’intégration à l’aide d’une infrastructure de tests unitaires avec un hôte Web de test et un serveur de test en mémoire.

Cette rubrique suppose une compréhension de base des tests unitaires. Si vous n’êtes pas familiarisé avec les concepts de test, consultez la rubrique [tests unitaires dans .net Core et .NET standard](/dotnet/core/testing/) et son contenu lié.

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

L’exemple d’application est une application Razor Pages et suppose une compréhension de base des Razor Pages. Si vous n’êtes pas familiarisé avec Razor Pages, consultez les rubriques suivantes :

* [Présentation des pages Razor](xref:razor-pages/index)
* [Bien démarrer avec les pages Razor](xref:tutorials/razor-pages/razor-pages-start)
* [Tests unitaires Pages Razor](xref:test/razor-pages-tests)

> [!NOTE]
> Pour tester la SPAs, nous vous recommandons d’utiliser un outil tel que le [sélénium](https://www.seleniumhq.org/), qui peut automatiser un navigateur.

## <a name="introduction-to-integration-tests"></a>Présentation des tests d’intégration

Les tests d’intégration évaluent les composants d’une application à un niveau plus large que les [tests unitaires](/dotnet/core/testing/). Les tests unitaires sont utilisés pour tester des composants logiciels isolés, tels que des méthodes de classe individuelles. Les tests d’intégration confirment que deux ou plusieurs composants d’application fonctionnent ensemble pour produire un résultat attendu, y compris éventuellement chaque composant requis pour traiter entièrement une demande.

Ces tests plus larges sont utilisés pour tester l’infrastructure de l’application et l’infrastructure entière, y compris les composants suivants :

* Base de données
* Système de fichiers
* Appliances réseau
* Pipeline de requête-réponse

Les tests unitaires utilisent des composants fabriqués, appelés objets *substituts* ou *factices*, à la place des composants d’infrastructure.

Contrairement aux tests unitaires, les tests d’intégration :

* Utilisez les composants réels utilisés par l’application en production.
* Nécessitent davantage de code et de traitement des données.
* Prend plus de temps pour s’exécuter.

Par conséquent, limitez l’utilisation des tests d’intégration aux scénarios d’infrastructure les plus importants. Si un comportement peut être testé à l’aide d’un test unitaire ou d’un test d’intégration, choisissez le test unitaire.

> [!TIP]
> N’écrivez pas de tests d’intégration pour chaque permutation possible d’accès aux données et aux fichiers avec les bases de données et les systèmes de fichiers. Quel que soit le nombre d’emplacements au sein d’une application qui interagissent avec les bases de données et les systèmes de fichiers, un ensemble de tests d’intégration de lecture, d’écriture, de mise à jour et de suppression est généralement en mesure de tester correctement les composants de base de données et de système de fichiers. Utilisez des tests unitaires pour les tests de routine de la logique de méthode qui interagissent avec ces composants. Dans les tests unitaires, l’utilisation de substituts ou de simulacres d’infrastructure entraîne une exécution plus rapide des tests.

> [!NOTE]
> Dans les discussions sur les tests d’intégration, le projet testé est fréquemment appelé le *système en cours de test*ou « St » pour l’instant.
>
> *« St » est utilisé dans cette rubrique pour faire référence à l’application ASP.NET Core testée.*

## <a name="aspnet-core-integration-tests"></a>ASP.NET Core les tests d’intégration

Les tests d’intégration dans ASP.NET Core requièrent les éléments suivants :

* Un projet de test est utilisé pour contenir et exécuter les tests. Le projet de test a une référence à St.
* Le projet de test crée un hôte Web de test pour le St et utilise un client de serveur de test pour gérer les demandes et les réponses avec le St.
* Un testeur de test est utilisé pour exécuter les tests et signaler les résultats des tests.

Les tests d’intégration suivent une séquence d’événements qui incluent les étapes de test *arrange*, *Act*et *Assert* habituelles :

1. L’hôte Web de St est configuré.
1. Un client de serveur de test est créé pour envoyer des demandes à l’application.
1. L’étape *organiser* le test est exécutée : l’application de test prépare une demande.
1. L’étape de test *Act* est exécutée : le client envoie la demande et reçoit la réponse.
1. L’étape de test d' *assertion* est exécutée : la réponse *réelle* est validée en tant que *réussite* ou *échec* en fonction d’une réponse *attendue* .
1. Le processus se poursuit jusqu’à ce que tous les tests soient exécutés.
1. Les résultats des tests sont signalés.

En règle générale, l’hôte Web de test est configuré différemment de l’hôte Web normal de l’application pour les séries de tests. Par exemple, une base de données différente ou des paramètres d’application différents peuvent être utilisés pour les tests.

Les composants d’infrastructure, tels que l’hôte Web de test et le serveur de test en mémoire ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)), sont fournis ou gérés par le package [Microsoft. AspNetCore. Mvc. testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) . L’utilisation de ce package simplifie la création et l’exécution des tests.

Le `Microsoft.AspNetCore.Mvc.Testing` package gère les tâches suivantes :

* Copie le fichier de dépendances (*. DEPS*) à partir du St dans le répertoire *bin* du projet de test.
* Définit la racine du [contenu](xref:fundamentals/index#content-root) sur la racine du projet St pour que les fichiers et les pages statiques soient trouvés lors de l’exécution des tests.
* Fournit la classe [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) pour simplifier l’amorçage du St avec `TestServer`.

La documentation sur les [tests unitaires](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) décrit comment configurer un projet de test et Test Runner, ainsi que des instructions détaillées sur la façon d’exécuter des tests et des recommandations sur la façon de nommer des tests et des classes de test.

> [!NOTE]
> Lorsque vous créez un projet de test pour une application, séparez les tests unitaires des tests d’intégration dans différents projets. Cela permet de s’assurer que les composants de test d’infrastructure ne sont pas accidentellement inclus dans les tests unitaires. La séparation des tests d’unité et d’intégration permet également de contrôler l’ensemble des tests exécutés.

Il n’existe pratiquement aucune différence entre la configuration des tests d’applications Razor Pages et les applications MVC. La seule différence réside dans la façon dont les tests sont nommés. Dans une application Razor Pages, les tests des points de terminaison de page sont généralement nommés après la classe de modèle de `IndexPageTests` page (par exemple, pour tester l’intégration des composants pour la page d’index). Dans une application MVC, les tests sont généralement organisés par classes de contrôleur et nommés après les contrôleurs testés ( `HomeControllerTests` par exemple, pour tester l’intégration des composants pour le contrôleur de démarrage).

## <a name="test-app-prerequisites"></a>Conditions préalables pour les applications de test

Le projet de test doit :

* Référencez les packages suivants :
  * [Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)
  * [Microsoft. AspNetCore. Mvc. testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/)
* Spécifiez le kit de développement logiciel (SDK`<Project Sdk="Microsoft.NET.Sdk.Web">`) Web dans le fichier projet (). Le kit de développement logiciel (SDK) Web est requis pour référencer le [AspNetCore](xref:fundamentals/metapackage-app).

Ces conditions préalables peuvent être consultées dans l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/). Inspectez le fichier *tests/RazorPagesProject. tests/RazorPagesProject. tests. csproj* . L’exemple d’application utilise l’infrastructure de test [xUnit](https://xunit.github.io/) et la bibliothèque de l’analyseur [AngleSharp](https://anglesharp.github.io/) , de sorte que l’exemple d’application référence également :

* [xunit](https://www.nuget.org/packages/xunit/)
* [xUnit. Runner. VisualStudio](https://www.nuget.org/packages/xunit.runner.visualstudio/)
* [AngleSharp](https://www.nuget.org/packages/AngleSharp/)

## <a name="sut-environment"></a>Environnement St

Si l' [environnement](xref:fundamentals/environments) de St n’est pas défini, l’environnement est par défaut développement.

## <a name="basic-tests-with-the-default-webapplicationfactory"></a>Tests de base avec le WebApplicationFactory par défaut

[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) est utilisé pour créer un [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) pour les tests d’intégration. `TEntryPoint`est la classe de point d’entrée de St, généralement `Startup` la classe.

Les classes de test implémentent une interface de *contexte de classe* ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) pour indiquer que la classe contient des tests et fournir des instances d’objet partagé dans les tests de la classe.

La classe de test suivante `BasicTests`,, utilise `WebApplicationFactory` le pour démarrer le St et fournir un [httpclient](/dotnet/api/system.net.http.httpclient) à une méthode de `Get_EndpointsReturnSuccessAndCorrectContentType`test,. La méthode vérifie si le code d’état de réponse a réussi (codes d’État dans la plage 200-299 `Content-Type` ) et `text/html; charset=utf-8` si l’en-tête est pour plusieurs pages d’application.

[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) crée une instance de `HttpClient` qui suit automatiquement les redirections et gère les cookies.

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

Par défaut, les cookies non essentiels ne sont pas conservés entre les demandes lorsque la [stratégie de consentement RGPD](xref:security/gdpr) est activée. Pour conserver les cookies non essentiels, tels que ceux utilisés par le fournisseur TempData, marquez-les comme essentiels dans vos tests. Pour obtenir des instructions sur le marquage d’un cookie comme étant essentiel, consultez [les cookies essentiels](xref:security/gdpr#essential-cookies).

## <a name="customize-webapplicationfactory"></a>Personnaliser WebApplicationFactory

La configuration d’hôte Web peut être créée indépendamment des classes de test en héritant de `WebApplicationFactory` pour créer une ou plusieurs fabriques personnalisées :

1. Hériter `WebApplicationFactory` de et substituer [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost). [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) permet la configuration de la collection de services avec [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices):

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   L’amorçage de base de données dans l' [exemple](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) d’application `InitializeDbForTests` est effectué par la méthode. La méthode est décrite dans la section [exemple de tests d’intégration : organisation](#test-app-organization) d’une application de test.

2. Utilisez le personnalisé `CustomWebApplicationFactory` dans les classes de test. L’exemple suivant utilise la fabrique dans la `IndexPageTests` classe :

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   Le client de l’exemple d’application est configuré pour `HttpClient` empêcher la suite de redirections. Comme expliqué plus loin dans la section [d’authentification des simulacres](#mock-authentication) , cela permet aux tests de vérifier le résultat de la première réponse de l’application. La première réponse est une redirection dans un grand nombre de ces tests `Location` avec un en-tête.

3. Un test standard utilise les `HttpClient` méthodes d’assistance et pour traiter la demande et la réponse :

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

Toute demande de publication à l’St doit être conforme à la vérification anti-contrefaçon effectuée automatiquement par le [système anti-contrefaçon de protection des données](xref:security/data-protection/introduction)de l’application. Pour organiser la requête de publication d’un test, l’application de test doit :

1. Effectuez une demande pour la page.
1. Analysez le cookie anti-contrefaçon et le jeton de validation de demande à partir de la réponse.
1. Effectuez la demande de publication avec le cookie anti-contrefaçon et le jeton de validation de demande en place.

Les `SendAsync` méthodes d’extension d’assistance *(helpers/HttpClientExtensions. cs*) et `GetDocumentAsync` la méthode d’assistance (*helpers/HtmlHelper. cs*) dans l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) utilisent l’analyseur [AngleSharp](https://anglesharp.github.io/) pour gérer la vérification anti-falsification avec les méthodes suivantes :

* `GetDocumentAsync`&ndash; Reçoit le [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) et retourne un `IHtmlDocument`. `GetDocumentAsync`utilise une fabrique qui prépare une *réponse virtuelle* en fonction de l’original `HttpResponseMessage`. Pour plus d’informations, consultez la [documentation AngleSharp](https://github.com/AngleSharp/AngleSharp#documentation).
* `SendAsync`les `HttpClient` méthodes d’extension pour compose un [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) et appellent [SendAsync (HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) pour envoyer des demandes au St. Les surcharges `SendAsync` pour acceptent le formulaire HTML`IHtmlFormElement`() et les éléments suivants :
  * Bouton envoyer du formulaire (`IHtmlElement`)
  * Collection de valeurs de`IEnumerable<KeyValuePair<string, string>>`formulaire ()
  * Bouton envoyer (`IHtmlElement`) et valeurs de formulaire`IEnumerable<KeyValuePair<string, string>>`()

> [!NOTE]
> [AngleSharp](https://anglesharp.github.io/) est une bibliothèque d’analyse tierce utilisée à des fins de démonstration dans cette rubrique et l’exemple d’application. AngleSharp n’est pas pris en charge ou requis pour le test d’intégration des applications ASP.NET Core. D’autres analyseurs peuvent être utilisés, tels que le [HAP (html agilité Pack)](https://html-agility-pack.net/). Une autre approche consiste à écrire du code pour gérer directement le jeton de vérification de demande et le cookie anti-contrefaçon du système anti-contrefaçon.

## <a name="customize-the-client-with-withwebhostbuilder"></a>Personnaliser le client avec WithWebHostBuilder

Quand une configuration supplémentaire est requise dans une méthode de test, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) crée `WebApplicationFactory` un nouveau avec un [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) qui est ensuite personnalisé par la configuration.

La `Post_DeleteMessageHandler_ReturnsRedirectToRoot` méthode de test de l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) montre l' `WithWebHostBuilder`utilisation de. Ce test effectue une suppression d’enregistrement dans la base de données en déclenchant une soumission de formulaire dans St.

Étant donné qu’un autre `IndexPageTests` test de la classe effectue une opération qui supprime tous les enregistrements de la base de données et peut `Post_DeleteMessageHandler_ReturnsRedirectToRoot` s’exécuter avant la méthode, la base de données est réamorcée dans cette méthode de test pour s’assurer qu’un enregistrement est présent pour que le St puisse être supprimé. La sélection du premier bouton supprimer du `messages` formulaire dans le St est simulée dans la requête adressée au St :

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a>Options du client

Le tableau suivant indique les [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) par défaut disponibles lors `HttpClient` de la création d’instances.

| Option | Description | Default |
| ------ | ----------- | ------- |
| [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | Obtient ou définit une valeur indiquant `HttpClient` si les instances de doivent suivre automatiquement les réponses de redirection. | `true` |
| [BaseAddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | Obtient ou définit l’adresse de base `HttpClient` des instances. | `http://localhost` |
| [HandleCookies](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | Obtient ou définit une `HttpClient` valeur indiquant si les instances doivent gérer les cookies. | `true` |
| [MaxAutomaticRedirections](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | Obtient ou définit le nombre maximal de réponses de redirection `HttpClient` que les instances doivent suivre. | 7 |

Créez la `WebApplicationFactoryClientOptions` classe et transmettez-la à la méthode [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) (les valeurs par défaut sont affichées dans l’exemple de code) :

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a>Injecter des services fictifs

Les services peuvent être substitués dans un test à l’aide d’un appel à [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) sur le générateur de l’hôte. **Pour injecter des services fictifs, St doit `Startup` avoir une classe `Startup.ConfigureServices` avec une méthode.**

L’exemple de St comprend un service étendu qui retourne un guillemet. Le guillemet est incorporé dans un champ masqué sur la page d’index lorsque la page d’index est demandée.

*Services/IQuoteService. cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

*Services/QuoteService. cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

*Pages/Index.cshtml.cs* :

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

*Pages/index. cs*:

[!code-cshtml[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

Le balisage suivant est généré lors de l’exécution de l’application St :

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

Pour tester l’injection de service et de guillemets dans un test d’intégration, un service fictif est injecté dans le St par le test. Le service factice remplace l’application `QuoteService` par un service fourni par l’application de test, appelé `TestQuoteService`:

*IntegrationTests.IndexPageTests.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

`ConfigureTestServices`est appelé, et le service étendu est inscrit :

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

Le balisage produit pendant l’exécution du test reflète le texte du devis `TestQuoteService`fourni par, donc l’assertion réussit :

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="mock-authentication"></a>Simuler l’authentification

Les tests de `AuthTests` la classe vérifient qu’un point de terminaison sécurisé :

* Redirige un utilisateur non authentifié vers la page de connexion de l’application.
* Retourne le contenu d’un utilisateur authentifié.

Dans St, la `/SecurePage` page utilise une convention [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) pour appliquer un [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) à la page. Pour plus d’informations, consultez [Razor pages conventions d’autorisation](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

Dans le `Get_SecurePageRedirectsAnUnauthenticatedUser` test, un [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) est défini pour interdire les redirections en affectant `false`à [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) la valeur :

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet2)]

En interautorisant le client à suivre la redirection, vous pouvez effectuer les vérifications suivantes :

* Le code d’état retourné par le St peut être vérifié par rapport au résultat [HttpStatusCode. Redirect](/dotnet/api/system.net.httpstatuscode) attendu, pas le code d’état final après la redirection vers la page de connexion, qui serait [HttpStatusCode. OK](/dotnet/api/system.net.httpstatuscode).
* La `Location` valeur d’en-tête dans les en-têtes de réponse est vérifiée `http://localhost/Identity/Account/Login`pour confirmer qu’elle commence par, et non par `Location` la réponse de page de connexion finale, où l’en-tête n’est pas présent.

L’application de test peut simuler un <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> dans [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) afin de tester les aspects de l’authentification et de l’autorisation. Un scénario minimal renvoie un [AuthenticateResult. Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*):

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet4&highlight=11-18)]

La `TestAuthHandler` méthode est appelée pour authentifier un utilisateur lorsque le schéma d’authentification `Test` est `AddAuthentication` défini sur où `ConfigureTestServices`est inscrit pour :

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet3&highlight=7-12)]

Pour plus d’informations `WebApplicationFactoryClientOptions`sur, consultez la section [Options du client](#client-options) .

## <a name="set-the-environment"></a>Définir l’environnement

Par défaut, l’environnement de l’hôte et de l’application de St est configuré pour utiliser l’environnement de développement. Pour remplacer l’environnement de St :

* Définissez la `ASPNETCORE_ENVIRONMENT` variable d’environnement (par exemple `Staging` `Production`,, ou une autre valeur personnalisée, telle `Testing`que).
* Remplacez `CreateHostBuilder` dans l’application de test pour lire les variables d’environnement précédées de `ASPNETCORE`.

```csharp
protected override IHostBuilder CreateHostBuilder() => 
    base.CreateHostBuilder()
        .ConfigureHostConfiguration(
            config => config.AddEnvironmentVariables("ASPNETCORE"));
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a>Comment l’infrastructure de test déduit le chemin racine du contenu de l’application

Le `WebApplicationFactory` constructeur déduit le chemin d' [accès racine du contenu](xref:fundamentals/index#content-root) de l’application en recherchant un [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) sur l’assembly contenant les tests d’intégration avec `TEntryPoint` une `System.Reflection.Assembly.FullName`clé égale à l’assembly. Si un attribut avec la clé correcte est introuvable `WebApplicationFactory` , revient à la recherche d’un fichier solution (*. sln*) et ajoute le `TEntryPoint` nom de l’assembly au répertoire de la solution. Le répertoire racine de l’application (chemin racine du contenu) est utilisé pour découvrir les affichages et les fichiers de contenu.

## <a name="disable-shadow-copying"></a>Désactiver les clichés instantanés

Les clichés instantanés provoquent l’exécution des tests dans un répertoire différent de celui du répertoire de sortie. Pour que les tests fonctionnent correctement, les clichés instantanés doivent être désactivés. L' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) utilise xUnit et désactive les clichés instantanés pour xUnit en incluant un fichier *xUnit. Runner. JSON* avec le paramètre de configuration correct. Pour plus d’informations, consultez [configuration de xUnit avec JSON](https://xunit.github.io/docs/configuring-with-json.html).

Ajoutez le fichier *xUnit. Runner. JSON* à la racine du projet de test avec le contenu suivant :

```json
{
  "shadowCopy": false
}
```

Si vous utilisez Visual Studio, définissez la propriété **copier dans le répertoire de sortie** du fichier sur **toujours copier**. Si vous n’utilisez pas Visual Studio, `Content` ajoutez une cible au fichier projet de l’application de test :

```xml
<ItemGroup>
  <Content Update="xunit.runner.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
  </Content>
</ItemGroup>
```

## <a name="disposal-of-objects"></a>Suppression d’objets

Après l’exécution des tests `IClassFixture` de l’implémentation, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) et [httpclient](/dotnet/api/system.net.http.httpclient) sont supprimés quand xUnit supprime le [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1). Si les objets instanciés par le développeur nécessitent une suppression, supprimez- `IClassFixture` les dans l’implémentation. Pour plus d’informations, consultez [implémentation d’une méthode dispose](/dotnet/standard/garbage-collection/implementing-dispose).

## <a name="integration-tests-sample"></a>Exemple de tests d’intégration

L' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) se compose de deux applications :

| Application | Répertoire du projet | Description |
| --- | ----------------- | ----------- |
| Application message (St) | *SRC/RazorPagesProject* | Permet à l’utilisateur d’ajouter, de supprimer, de supprimer et d’analyser des messages. |
| Tester une application | *tests/RazorPagesProject. tests* | Utilisé pour tester l’intégration du St. |

Les tests peuvent être exécutés à l’aide des fonctionnalités de test intégrées d’un environnement de développement intégré (IDE), telles que [Visual Studio](https://visualstudio.microsoft.com). Si vous utilisez [Visual Studio code](https://code.visualstudio.com/) ou la ligne de commande, exécutez la commande suivante à l’invite de commandes dans le répertoire *tests/RazorPagesProject. tests* :

```dotnetcli
dotnet test
```

### <a name="message-app-sut-organization"></a>Organisation message App (St)

Le St est un système de messagerie Razor Pages avec les caractéristiques suivantes :

* La page d’index de l’application (*pages/index. cshtml* et *pages/index. cshtml. cs*) fournit une interface utilisateur et des méthodes de modèle de page pour contrôler l’ajout, la suppression et l’analyse de messages (mots moyens par message).
* Un message est décrit par la `Message` classe (*Data/message. cs*) avec deux propriétés : `Id` (Key) et `Text` (message). La `Text` propriété est obligatoire et limitée à 200 caractères.
* Les messages sont stockés à l’aide [de la base de données en mémoire de Entity Framework](/ef/core/providers/in-memory/)&#8224;.
* L’application contient une couche d’accès aux données (DAL) dans sa classe de `AppDbContext` contexte de base de données, (*Data/AppDbContext. cs*).
* Si la base de données est vide au démarrage de l’application, la Banque de messages est initialisée avec trois messages.
* L’application comprend un `/SecurePage` qui n’est accessible qu’à un utilisateur authentifié.

&#8224;la rubrique EF [test with InMemory](/ef/core/miscellaneous/testing/in-memory), explique comment utiliser une base de données en mémoire pour les tests avec MSTest. Cette rubrique utilise l’infrastructure de test [xUnit](https://xunit.github.io/) . Les concepts de test et les implémentations de test dans différents frameworks de test sont similaires, mais pas identiques.

Bien que l’application n’utilise pas le modèle de référentiel et ne soit pas un exemple efficace de [modèle d’unité de travail (UOW)](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor pages prend en charge ces modèles de développement. Pour plus d’informations, consultez [conception de la couche de persistance de l’infrastructure](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) et [logique du contrôleur de test](/aspnet/core/mvc/controllers/testing) (l’exemple implémente le modèle de référentiel).

### <a name="test-app-organization"></a>Organisation des applications de test

L’application de test est une application console dans le répertoire *tests/RazorPagesProject. tests* .

| Tester le répertoire de l’application | Description |
| ------------------ | ----------- |
| *AuthTests* | Contient des méthodes de test pour :<ul><li>Accès à une page sécurisée par un utilisateur non authentifié.</li><li>Accès à une page sécurisée par un utilisateur authentifié avec un simulacre <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>.</li><li>Obtention d’un profil utilisateur GitHub et vérification de la connexion de l’utilisateur du profil.</li></ul> |
| *BasicTests* | Contient une méthode de test pour le routage et le type de contenu. |
| *IntegrationTests* | Contient les tests d’intégration de la page d’index `WebApplicationFactory` à l’aide de la classe personnalisée. |
| *Programmes d’assistance/utilitaires* | <ul><li>*Utilities.cs* contient la `InitializeDbForTests` méthode utilisée pour amorcer la base de données avec des données de test.</li><li>*HtmlHelpers.cs* fournit une méthode pour retourner un AngleSharp `IHtmlDocument` pour une utilisation par les méthodes de test.</li><li>*HttpClientExtensions.cs* fournissent des surcharges `SendAsync` pour envoyer des demandes au St.</li></ul> |

L’infrastructure de test est [xUnit](https://xunit.github.io/). Les tests d’intégration sont effectués à l’aide de [Microsoft. AspNetCore. TestHost](/dotnet/api/microsoft.aspnetcore.testhost), qui comprend le [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver). Étant donné que le package [Microsoft. AspNetCore. Mvc. testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) est utilisé pour configurer l’hôte de test et le `TestHost` serveur `TestServer` de test, les packages et ne nécessitent pas de références de package directes dans le fichier projet ou la configuration de développeur de l’application de test dans l’application de test.

**Amorçage de la base de données à des fins de test**

Les tests d’intégration nécessitent généralement un petit jeu de données dans la base de données avant l’exécution du test. Par exemple, un test Delete requiert la suppression d’un enregistrement de base de données, de sorte que la base de données doit avoir au moins un enregistrement pour que la demande Delete aboutisse.

L’exemple d’application amorce la base de données avec trois messages dans *Utilities.cs* que les tests peuvent utiliser lorsqu’ils exécutent :

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

::: moniker-end

## <a name="additional-resources"></a>Ressources supplémentaires

* [Tests unitaires](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:test/razor-pages-tests>
* <xref:fundamentals/middleware/index>
* <xref:mvc/controllers/testing>
