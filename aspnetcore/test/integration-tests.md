---
title: Tests d’intégration dans ASP.NET Core
author: rick-anderson
description: Découvrez comment les tests d’intégration garantissent le bon fonctionnement des composants d’une application au niveau de l’infrastructure, notamment la base de données, le système de fichiers et le réseau.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/06/2019
uid: test/integration-tests
ms.openlocfilehash: 414e47b9c5a1c843755bd79d313f503b554945db
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664695"
---
# <a name="integration-tests-in-aspnet-core"></a>Tests d’intégration dans ASP.NET Core

Par [Javier Calvarro Nelson](https://github.com/javiercn), Steve [Smith](https://ardalis.com/), et Jos van [der Til](https://jvandertil.nl)

::: moniker range=">= aspnetcore-3.0"

Les tests d’intégration garantissent que les composants d’une application fonctionnent correctement à un niveau qui inclut l’infrastructure de support de l’application, comme la base de données, le système de fichiers et le réseau. ASP.NET Core prend en charge les tests d’intégration à l’aide d’un cadre de test unitaire avec un hébergeur de test et un serveur de test en mémoire.

Ce sujet suppose une compréhension de base des tests unitaires. S’il n’est pas familier avec les concepts de test, voir le [test unitaire dans .NET Core et .NET Standard](/dotnet/core/testing/) sujet et son contenu lié.

[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([comment télécharger](xref:index#how-to-download-a-sample))

L’application échantillon est une application Razor Pages et suppose une compréhension de base de Razor Pages. Si vous n’êtes pas familier avec Razor Pages, voir les sujets suivants:

* [Présentation des pages Razor](xref:razor-pages/index)
* [Bien démarrer avec les pages Razor](xref:tutorials/razor-pages/razor-pages-start)
* [Tests unitaires Pages Razor](xref:test/razor-pages-tests)

> [!NOTE]
> Pour tester les SPA, nous avons recommandé un outil tel que [le sélénium](https://www.seleniumhq.org/), qui peut automatiser un navigateur.

## <a name="introduction-to-integration-tests"></a>Introduction aux tests d’intégration

Les tests d’intégration évaluent les composants d’une application à un niveau plus large que [les tests unitaires.](/dotnet/core/testing/) Les tests unitaires sont utilisés pour tester des composants logiciels isolés, tels que des méthodes de classe individuelles. Les tests d’intégration confirment que deux composants d’applications ou plus travaillent ensemble pour produire un résultat attendu, y compris éventuellement chaque composant nécessaire pour traiter pleinement une demande.

Ces tests plus larges sont utilisés pour tester l’infrastructure de l’application et l’ensemble du cadre, y compris souvent les composants suivants :

* Base de données
* Système de fichiers
* Appliances réseau
* Pipeline de demande-réponse

Les tests unitaires utilisent des composants fabriqués, connus sous le nom de faux ou *d’objets* *simulés,* à la place de composants d’infrastructure.

Contrairement aux tests unitaires, les tests d’intégration :

* Utilisez les composants réels que l’application utilise dans la production.
* Exiger plus de code et de traitement des données.
* Il faut plus de temps pour courir.

Par conséquent, limitez l’utilisation des tests d’intégration aux scénarios d’infrastructure les plus importants. Si un comportement peut être testé à l’aide d’un test unitaire ou d’un test d’intégration, choisissez le test unitaire.

> [!TIP]
> N’écrivez pas de tests d’intégration pour chaque permutation possible des données et n’ez pas l’accès aux fichiers avec des bases de données et des systèmes de fichiers. Indépendamment du nombre d’endroits dans une application interagissent avec les bases de données et les systèmes de fichiers, un ensemble ciblé de tests de lecture, d’écriture, de mise à jour et de suppression des tests d’intégration sont généralement capables de tester adéquatement les composants des bases de données et des fichiers. Utilisez des tests unitaires pour des tests de routine de la logique de la méthode qui interagissent avec ces composants. Dans les tests unitaires, l’utilisation de faux/mocks d’infrastructure entraîne une exécution plus rapide des tests.

> [!NOTE]
> Dans les discussions sur les tests d’intégration, le projet testé est souvent appelé le *système en cours d’essai*, ou "SUT" pour faire court.
>
> *"SUT" est utilisé tout au long de ce sujet pour se référer à l’application ASP.NET Core testée.*

## <a name="aspnet-core-integration-tests"></a>tests d’intégration de base ASP.NET

Les tests d’intégration dans ASP.NET Core exigent ce qui suit :

* Un projet de test est utilisé pour contenir et exécuter les tests. Le projet d’essai a une référence au SUT.
* Le projet de test crée un hébergeur de test pour le SUT et utilise un client serveur de test pour traiter les demandes et les réponses avec le SUT.
* Un coureur de test est utilisé pour exécuter les tests et signaler les résultats des tests.

Les tests d’intégration suivent une séquence d’événements qui comprennent les étapes *habituelles d’arrangement,* *d’acte*et *d’essai d’Assert* :

1. L’hébergeur du SUT est configuré.
1. Un client serveur de test est créé pour soumettre des demandes à l’application.
1. *L’étape de* test Arrange est exécutée : l’application de test prépare une demande.
1. L’étape de test *de la Loi* est exécutée : le client soumet la demande et reçoit la réponse.
1. *L’étape* de test Assert est exécutée : la réponse *réelle* est validée en tant que *passage* ou *échec* en fonction d’une réponse *attendue.*
1. Le processus se poursuit jusqu’à ce que tous les tests soient exécutés.
1. Les résultats des tests sont communiqués.

Habituellement, l’hébergeur de test est configuré différemment de l’hébergeur Web normal de l’application pour les essais. Par exemple, une base de données différente ou différents paramètres d’application peuvent être utilisés pour les tests.

Les composants d’infrastructure, tels que l’hébergeur de test et le serveur de test en mémoire ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)), sont fournis ou gérés par le package [Microsoft.AspNetCore.Mvc.Testing.](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) L’utilisation de ce paquet simplifie la création et l’exécution des tests.

Le `Microsoft.AspNetCore.Mvc.Testing` paquet s’occupe des tâches suivantes :

* Copies du fichier des dépendances (*.deps*) de la SUT dans l’annuaire *des bacs* du projet d’essai.
* Définit la racine de [contenu](xref:fundamentals/index#content-root) à la racine du projet du SUT de sorte que des fichiers statiques et des pages/vues sont trouvés lorsque les tests sont exécutés.
* Fournit la classe [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) pour rationaliser bootstrapping le SUT avec `TestServer`.

La documentation [des tests unitaire](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) décrit comment mettre en place un projet d’essai et un coureur d’essai, ainsi que des instructions détaillées sur la façon d’exécuter des tests et des recommandations sur la façon de nommer les tests et les classes de test.

> [!NOTE]
> Lors de la création d’un projet de test pour une application, séparez les tests unitaires des tests d’intégration dans différents projets. Cela permet de s’assurer que les composants d’analyse d’infrastructure ne sont pas accidentellement inclus dans les tests unitaires. La séparation des tests d’unité et d’intégration permet également de contrôler l’ensemble des tests qui sont exécutés.

Il n’y a pratiquement aucune différence entre la configuration pour les tests des applications Razor Pages et les applications MVC. La seule différence réside dans la façon dont les tests sont nommés. Dans une application Razor Pages, les tests des paramètres de page sont `IndexPageTests` généralement nommés d’après la classe de modèle de page (par exemple, pour tester l’intégration des composants pour la page Index). Dans une application MVC, les tests sont généralement organisés par des classes `HomeControllerTests` de contrôleur et nommés d’après les contrôleurs qu’ils testent (par exemple, pour tester l’intégration des composants pour le contrôleur à domicile).

## <a name="test-app-prerequisites"></a>Conditions préalables de l’application de test

Le projet d’essai doit :

* Référence du package [Microsoft.AspNetCore.Mvc.Testing.](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing)
* Spécifiez le SDK`<Project Sdk="Microsoft.NET.Sdk.Web">`Web dans le fichier du projet ( ).

Ces conditions préalables peuvent être vues dans [l’application d’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/). Inspectez les *tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj.* L’application d’échantillon utilise le cadre de test [xUnit](https://xunit.github.io/) et la bibliothèque de parser [AngleSharp,](https://anglesharp.github.io/) de sorte que l’application d’échantillon fait également référence :

* [xunit](https://www.nuget.org/packages/xunit)
* [xunit.runner.visualstudio](https://www.nuget.org/packages/xunit.runner.visualstudio)
* [AngleSharp (angleSharp)](https://www.nuget.org/packages/AngleSharp)

Entity Framework Core est également utilisé dans les tests. Les références de l’application:

* [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore)
* [Microsoft.AspNetCore.Identity.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity.EntityFrameworkCore)
* [Microsoft.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore)
* [Microsoft.EntityFrameworkCore.InMemory](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory)
* [Microsoft.EntityFrameworkCore.Tools (en anglais seulement)](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools)

## <a name="sut-environment"></a>Environnement SUT

Si [l’environnement](xref:fundamentals/environments) du SUT n’est pas défini, l’environnement est par défaut pour le développement.

## <a name="basic-tests-with-the-default-webapplicationfactory"></a>Tests de base avec la webApplicationFactory par défaut

[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) est utilisé pour créer un [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) pour les tests d’intégration. `TEntryPoint`est la classe de point d’entrée du SUT, généralement la `Startup` classe.

Les classes de test implémentent une interface *de montage* de classe ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) pour indiquer que la classe contient des tests et fournissent des instances d’objets partagés à travers les tests de la classe.

La classe `BasicTests`d’essai `WebApplicationFactory` suivante, , utilise le bootstrap le SUT `Get_EndpointsReturnSuccessAndCorrectContentType`et de fournir un [HttpClient](/dotnet/api/system.net.http.httpclient) à une méthode d’essai, . La méthode vérifie si le code d’état de réponse est réussi (codes d’état dans la plage 200-299) et l’en-tête `Content-Type` est `text/html; charset=utf-8` pour plusieurs pages d’application.

[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) crée un `HttpClient` exemple qui suit automatiquement les redirections et gère les cookies.

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

Par défaut, les cookies non essentiels ne sont pas conservés entre les demandes lorsque la [politique de consentement GDPR](xref:security/gdpr) est activée. Pour préserver les cookies non essentiels, tels que ceux utilisés par le fournisseur TempData, marquez-les comme essentiels dans vos tests. Pour obtenir des instructions sur le marquage d’un cookie comme essentiel, voir [les cookies Essentiels](xref:security/gdpr#essential-cookies).

## <a name="customize-webapplicationfactory"></a>Personnaliser WebApplicationFactory

La configuration de l’hôte Web peut être `WebApplicationFactory` créée indépendamment des classes de test en héritant de créer une ou plusieurs usines personnalisées :

1. Héritez de `WebApplicationFactory` [configureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost)et remplacez. [L’IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) permet la configuration de la collection de services avec [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices):

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   L’ensemencement de base `InitializeDbForTests` de données dans l’application [d’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) est effectué par la méthode. La méthode est décrite dans [l’échantillon de tests d’intégration : Section de l’organisation des applications de test.](#test-app-organization)

   Le contexte de la base de `Startup.ConfigureServices` données du SUT est enregistré dans sa méthode. Le rappel de `builder.ConfigureServices` l’application de test est `Startup.ConfigureServices` exécuté *après* l’exécution du code de l’application. L’ordre d’exécution est un changement de rupture pour [l’hôte générique](xref:fundamentals/host/generic-host) avec la sortie de ASP.NET Core 3.0. Pour utiliser une base de données différente pour les tests que la base `builder.ConfigureServices`de données de l’application, le contexte de base de données de l’application doit être remplacé .

   L’application d’échantillon trouve le descripteur de service pour le contexte de base de données et utilise le descripteur pour supprimer l’enregistrement de service. Ensuite, l’usine `ApplicationDbContext` ajoute un nouveau qui utilise une base de données en mémoire pour les tests.

   Pour vous connecter à une base de données `UseInMemoryDatabase` différente de celle de la base de données en mémoire, modifiez l’appel pour connecter le contexte à une base de données différente. Pour utiliser une base de données de test SQL Server :

   * Référence du package [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/) NuGet dans le fichier du projet.
   * Appelez `UseSqlServer` avec une chaîne de connexion à la base de données.

   ```csharp
   services.AddDbContext<ApplicationDbContext>((options, context) => 
   {
       context.UseSqlServer(
           Configuration.GetConnectionString("TestingDbConnectionString"));
   });
   ```

2. Utilisez la `CustomWebApplicationFactory` coutume dans les classes de test. L’exemple suivant utilise `IndexPageTests` l’usine de la classe :

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   Le client de l’application de `HttpClient` l’échantillon est configuré pour empêcher les redirections suivantes. Comme expliqué plus tard dans la section [d’authentification Mock,](#mock-authentication) cela permet aux tests de vérifier le résultat de la première réponse de l’application. La première réponse est une redirection `Location` dans beaucoup de ces tests avec un en-tête.

3. Un test typique `HttpClient` utilise les méthodes et les méthodes d’aide pour traiter la demande et la réponse :

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

Toute demande POST au SUT doit satisfaire le contrôle antiforgery qui est automatiquement faite par le [système antiforgery](xref:security/data-protection/introduction)de protection des données de l’application . Afin d’organiser la demande POST d’un test, l’application de test doit :

1. Faites une demande pour la page.
1. Parse le cookie antiforgery et demander le jeton de validation de la réponse.
1. Faites la demande POST avec le cookie antiforgery et demandez le jeton de validation en place.

Les `SendAsync` méthodes d’extension de l’aide (*Helpers/HttpClientExtensions.cs*) et `GetDocumentAsync` la méthode d’aide *(Helpers/HtmlHelpers.cs*) dans [l’application d’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) utilisent le parser [AngleSharp](https://anglesharp.github.io/) pour manipuler le contrôle antiforgery avec les méthodes suivantes :

* `GetDocumentAsync`&ndash; Reçoit le [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) `IHtmlDocument`et retourne un . `GetDocumentAsync`utilise une usine qui prépare une réponse `HttpResponseMessage` *virtuelle* basée sur l’original . Pour plus d’informations, voir la [documentation AngleSharp](https://github.com/AngleSharp/AngleSharp#documentation).
* `SendAsync`méthodes d’extension pour la `HttpClient` composition d’un [httpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) et appeler [SendAsync (HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) pour soumettre des demandes au SUT. Surcharges pour `SendAsync` accepter le`IHtmlFormElement`formulaire HTML ( ) et ce qui suit:
  * Soumettre le bouton`IHtmlElement`du formulaire ( )
  * Collection de`IEnumerable<KeyValuePair<string, string>>`valeurs de formulaire ( )
  * Soumettre des`IHtmlElement`valeurs de`IEnumerable<KeyValuePair<string, string>>`bouton ( ) et de forme ( )

> [!NOTE]
> [AngleSharp](https://anglesharp.github.io/) est une bibliothèque d’analyse tierce utilisée à des fins de démonstration dans ce sujet et l’application d’échantillon. AngleSharp n’est pas pris en charge ou requis pour les tests d’intégration des applications ASP.NET Core. D’autres analyseurs peuvent être utilisés, tels que le [Pack d’agilité Html (HAP)](https://html-agility-pack.net/). Une autre approche consiste à écrire du code pour gérer directement le jeton de vérification des demandes et le cookie antiforgery du système antiforgery.

## <a name="customize-the-client-with-withwebhostbuilder"></a>Personnalisez le client avec WithWebHostBuilder

Lorsque une configuration supplémentaire est nécessaire dans une méthode `WebApplicationFactory` de test, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) crée un nouveau avec un [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) qui est encore personnalisé par configuration.

La `Post_DeleteMessageHandler_ReturnsRedirectToRoot` méthode de test de l’application [d’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) démontre l’utilisation de `WithWebHostBuilder`. Ce test effectue une suppression d’enregistrements dans la base de données en déclenchant une soumission de formulaire dans le SUT.

Étant donné qu’un autre test de la `IndexPageTests` classe effectue une opération qui `Post_DeleteMessageHandler_ReturnsRedirectToRoot` supprime tous les enregistrements de la base de données et peut s’exécuter avant la méthode, la base de données est réédée dans cette méthode de test pour s’assurer qu’un enregistrement est présent pour que le SUT puisse le supprimer. La sélection du premier `messages` bouton de suppression du formulaire dans le SUT est simulée dans la demande au SUT :

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a>Options clients

Le tableau suivant affiche la valeur Par défaut [de WebApplicationFactoryClientOptions disponibles](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) lors de la création d’instances. `HttpClient`

| Option | Description | Default |
| ------ | ----------- | ------- |
| [AutoriserAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | Obtient ou définit `HttpClient` si les instances doivent suivre automatiquement les réponses de redirection. | `true` |
| [BaseAddress (baseAddress)](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | Obtient ou définit l’adresse de base des `HttpClient` instances. | `http://localhost` |
| [HandleCookies (en)](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | Obtient ou `HttpClient` définit si les instances doivent gérer les cookies. | `true` |
| [MaxAutomaticRedirections](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | Obtient ou définit le nombre maximum `HttpClient` de réponses de redirection que les instances doivent suivre. | 7 |

Créez `WebApplicationFactoryClientOptions` la classe et transmettez-la à la méthode [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) (les valeurs par défaut sont affichées dans l’exemple du code) :

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

Les services peuvent être remplacés lors d’un test avec un appel à [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) sur le constructeur hôte. **Pour injecter des services fictifs, `Startup` le `Startup.ConfigureServices` SUT doit avoir une classe avec une méthode.**

L’échantillon SUT comprend un service à portée qui renvoie un devis. La citation est intégrée dans un champ caché sur la page Index lorsque la page Index est demandée.

*Services/IQuoteService.cs*:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

*Services/QuoteService.cs*:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

*Pages/Index.cshtml.cs* :

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

*Pages/Index.cs*:

[!code-cshtml[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

La balisage suivante est générée lorsque l’application SUT est exécutée :

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

Pour tester le service et citer l’injection dans un test d’intégration, un service de simulation est injecté dans le SUT par le test. Le service de simulation remplace `QuoteService` l’application par un service `TestQuoteService`fourni par l’application de test, appelé :

*IntegrationTests.IndexPageTests.cs*:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

`ConfigureTestServices`est appelé, et le service de portée est enregistré:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

La majoration produite lors de l’exécution du `TestQuoteService`test reflète le texte de citation fourni par , donc l’affirmation passe:

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="mock-authentication"></a>Authentification simulée

Les tests `AuthTests` dans la classe vérifient qu’un point de terminaison sécurisé :

* Redirige un utilisateur non authentique vers la page Login de l’application.
* Retourne le contenu d’un utilisateur authentifié.

Dans le SUT, la `/SecurePage` page utilise une convention [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) pour appliquer un [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) à la page. Pour plus d’informations, voir [les conventions d’autorisation de Razor Pages](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

Dans `Get_SecurePageRedirectsAnUnauthenticatedUser` le test, un [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) est configuré pour refuser `false`les redirections en définissant [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) à :

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet2)]

En interdisant au client de suivre la redirection, les vérifications suivantes peuvent être effectuées :

* Le code d’état retourné par le SUT peut être vérifié par rapport au résultat [attendu httpStatusCode.Redirect,](/dotnet/api/system.net.httpstatuscode) et non au code d’état final après la redirection vers la page Login, qui serait [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode).
* La `Location` valeur d’en-tête dans les en-têtes de réponse est vérifiée pour confirmer qu’il commence par `http://localhost/Identity/Account/Login`, pas la réponse finale de la page Login, où l’en-tête `Location` ne serait pas présent.

L’application de <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> test peut se moquer d’un in [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) afin de tester les aspects de l’authentification et de l’autorisation. Un scénario minimal renvoie un [AuthenticateResult.Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*):

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet4&highlight=11-18)]

Le `TestAuthHandler` est appelé à authentifier un utilisateur `Test` `AddAuthentication` lorsque le `ConfigureTestServices`système d’authentification est réglé à l’endroit où est enregistré pour :

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet3&highlight=7-12)]

Pour plus `WebApplicationFactoryClientOptions`d’informations sur , voir la section [options Client.](#client-options)

## <a name="set-the-environment"></a>Définir l’environnement

Par défaut, l’environnement d’hôte et d’application du SUT est configuré pour utiliser l’environnement de développement. Pour remplacer l’environnement du SUT :

* Définissez `ASPNETCORE_ENVIRONMENT` la variable de `Staging` `Production`l’environnement (par exemple, , , ou toute autre valeur personnalisée, telle que `Testing`).
* Remplacer `CreateHostBuilder` dans l’application de test pour `ASPNETCORE`lire les variables de l’environnement préfixées avec .

```csharp
protected override IHostBuilder CreateHostBuilder() => 
    base.CreateHostBuilder()
        .ConfigureHostConfiguration(
            config => config.AddEnvironmentVariables("ASPNETCORE"));
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a>Comment l’infrastructure de test déduit le chemin de racine du contenu de l’application

Le `WebApplicationFactory` constructeur déduit le chemin [de racine de contenu](xref:fundamentals/index#content-root) de l’application en recherchant un [WebApplicationFactoryContentRotentAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) sur l’assemblage contenant les tests d’intégration avec une clé égale à l’assemblage `TEntryPoint` `System.Reflection.Assembly.FullName`. Dans le cas où un attribut `WebApplicationFactory` avec la clé correcte n’est pas trouvé, retombe à la recherche d’un fichier de solution (*.sln*) et joint le nom de l’assemblage `TEntryPoint` à l’annuaire de la solution. Le répertoire racine de l’application (le chemin de racine de contenu) est utilisé pour découvrir les vues et les fichiers de contenu.

## <a name="disable-shadow-copying"></a>Copie d’ombre de désactiver

La copie d’ombre provoque l’exécution des tests dans un répertoire différent de celui du répertoire de sortie. Pour que les tests fonctionnent correctement, la copie d’ombre doit être désactivée. [L’application d’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) utilise xUnit et désactive la copie d’ombre pour xUnit en incluant un fichier *xunit.runner.json* avec le réglage de configuration correct. Pour plus d’informations, voir [Configuring xUnit avec JSON](https://xunit.github.io/docs/configuring-with-json.html).

Ajouter le fichier *xunit.runner.json* à la racine du projet de test avec le contenu suivant :

```json
{
  "shadowCopy": false
}
```

## <a name="disposal-of-objects"></a>Élimination des objets

Après l’exécution `IClassFixture` des tests de la mise en œuvre, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) et [HttpClient](/dotnet/api/system.net.http.httpclient) sont éliminés lorsque xUnit dispose de la [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1). Si les objets instantanés par le développeur nécessitent `IClassFixture` leur élimination, les disposer dans la mise en œuvre. Pour plus d’informations, voir [Implementing a Dispose method](/dotnet/standard/garbage-collection/implementing-dispose).

## <a name="integration-tests-sample"></a>Échantillon de tests d’intégration

[L’application échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) est composée de deux applications:

| Application | Répertoire du projet | Description |
| --- | ----------------- | ----------- |
| Application de message (le SUT) | *src/RazorPagesProjet* | Permet à un utilisateur d’en ajouter, de supprimer un, de supprimer tous et d’analyser des messages. |
| Tester une application | *tests/RazorPagesProject.Tests* | Utilisé pour tester l’intégration du SUT. |

Les tests peuvent être exécutés à l’aide des fonctionnalités de test intégrées d’un IDE, tels que [Visual Studio](https://visualstudio.microsoft.com). Si vous utilisez [visual Studio Code](https://code.visualstudio.com/) ou la ligne de commande, exécutez la commande suivante à une invite de commande dans le répertoire *tests/RazorPagesProject.Tests* :

```console
dotnet test
```

### <a name="message-app-sut-organization"></a>Organisation de l’application de message (SUT)

Le SUT est un système de messages Razor Pages avec les caractéristiques suivantes :

* La page d’index de l’application *(Pages/Index.cshtml* et *Pages/Index.cshtml.cs*) fournit une interface utilisateur et des méthodes de modèle de page pour contrôler l’ajout, la suppression et l’analyse des messages (mots moyens par message).
* Un message est `Message` décrit par la classe (*Data/Message.cs*) avec deux propriétés: `Id` (clé) et `Text` (message). La `Text` propriété est requise et limitée à 200 caractères.
* Les messages sont stockés [à l’aide de la base de données en mémoire d’Entity Framework](/ef/core/providers/in-memory/)&#8224;.
* L’application contient une couche d’accès aux `AppDbContext` données (DAL) dans sa classe de contexte de base de données, (*Data/AppDbContext.cs*).
* Si la base de données est vide sur le démarrage de l’application, le magasin de messages est paraséfié avec trois messages.
* L’application `/SecurePage` comprend un qui ne peut être consulté que par un utilisateur authentifié.

&#8224;Le sujet EF, [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), explique comment utiliser une base de données en mémoire pour les tests avec MSTest. Ce sujet utilise le cadre de test [xUnit.](https://xunit.github.io/) Les concepts de test et les implémentations de tests dans différents cadres de test sont similaires mais non identiques.

Bien que l’application n’utilise pas le modèle de référentiel et n’est pas un exemple efficace du [modèle de l’Unité de travail (UoW),](https://martinfowler.com/eaaCatalog/unitOfWork.html)Razor Pages prend en charge ces modèles de développement. Pour plus d’informations, voir [Concevoir la couche de persistance de l’infrastructure](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) et la logique du contrôleur de [test](/aspnet/core/mvc/controllers/testing) (l’échantillon implémente le modèle de dépôt).

### <a name="test-app-organization"></a>Organisation d’applications de test

L’application de test est une application console à l’intérieur du *répertoire tests/RazorPagesProject.Tests.*

| Annuaire d’applications de test | Description |
| ------------------ | ----------- |
| *AuthTests* | Contient des méthodes de test pour :<ul><li>Accès à une page sécurisée par un utilisateur non athéné.</li><li>Accéder à une page sécurisée par un <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>utilisateur authentifié avec un simulacre .</li><li>Obtenir un profil d’utilisateur GitHub et vérifier la connexion utilisateur du profil.</li></ul> |
| *Tests de base* | Contient une méthode de test pour le routage et le type de contenu. |
| *IntegrationTests* | Contient les tests d’intégration `WebApplicationFactory` pour la page Index à l’aide de la classe personnalisée. |
| *Aides/Services publics* | <ul><li>*Utilities.cs* contient la `InitializeDbForTests` méthode utilisée pour ensemencer la base de données avec des données de test.</li><li>*HtmlHelpers.cs* fournit une méthode pour retourner `IHtmlDocument` un AngleSharp pour une utilisation par les méthodes d’essai.</li><li>*HttpClientExtensions.cs* fournir des surcharges `SendAsync` pour soumettre des demandes au SUT.</li></ul> |

Le cadre d’essai est [xUnit](https://xunit.github.io/). Les tests d’intégration sont effectués à l’aide de [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost), qui comprend le [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver). Étant donné que le package [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) est utilisé `TestHost` pour `TestServer` configurer l’hôte de test et le serveur de test, le paquet et les paquets ne nécessitent pas de références de paquets directs dans le fichier de projet ou la configuration du développeur de l’application de test dans l’application de test.

**Ensemencement de la base de données pour les tests**

Les tests d’intégration nécessitent généralement un petit jeu de données dans la base de données avant l’exécution du test. Par exemple, un test de suppression nécessite une suppression d’enregistrement de base de données, de sorte que la base de données doit avoir au moins un enregistrement pour que la demande de suppression réussisse.

L’application échantillon de graines de la base de données avec trois messages dans *Utilities.cs* que les tests peuvent utiliser quand ils exécutent:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

Le contexte de la base de `Startup.ConfigureServices` données du SUT est enregistré dans sa méthode. Le rappel de `builder.ConfigureServices` l’application de test est `Startup.ConfigureServices` exécuté *après* l’exécution du code de l’application. Pour utiliser une base de données différente pour les tests, le contexte de base de données de l’application doit être remplacé . `builder.ConfigureServices` Pour plus d’informations, consultez la section [Customize WebApplicationFactory.](#customize-webapplicationfactory)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Les tests d’intégration garantissent que les composants d’une application fonctionnent correctement à un niveau qui inclut l’infrastructure de support de l’application, comme la base de données, le système de fichiers et le réseau. ASP.NET Core prend en charge les tests d’intégration à l’aide d’un cadre de test unitaire avec un hébergeur de test et un serveur de test en mémoire.

Ce sujet suppose une compréhension de base des tests unitaires. S’il n’est pas familier avec les concepts de test, voir le [test unitaire dans .NET Core et .NET Standard](/dotnet/core/testing/) sujet et son contenu lié.

[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([comment télécharger](xref:index#how-to-download-a-sample))

L’application échantillon est une application Razor Pages et suppose une compréhension de base de Razor Pages. Si vous n’êtes pas familier avec Razor Pages, voir les sujets suivants:

* [Présentation des pages Razor](xref:razor-pages/index)
* [Bien démarrer avec les pages Razor](xref:tutorials/razor-pages/razor-pages-start)
* [Tests unitaires Pages Razor](xref:test/razor-pages-tests)

> [!NOTE]
> Pour tester les SPA, nous avons recommandé un outil tel que [le sélénium](https://www.seleniumhq.org/), qui peut automatiser un navigateur.

## <a name="introduction-to-integration-tests"></a>Introduction aux tests d’intégration

Les tests d’intégration évaluent les composants d’une application à un niveau plus large que [les tests unitaires.](/dotnet/core/testing/) Les tests unitaires sont utilisés pour tester des composants logiciels isolés, tels que des méthodes de classe individuelles. Les tests d’intégration confirment que deux composants d’applications ou plus travaillent ensemble pour produire un résultat attendu, y compris éventuellement chaque composant nécessaire pour traiter pleinement une demande.

Ces tests plus larges sont utilisés pour tester l’infrastructure de l’application et l’ensemble du cadre, y compris souvent les composants suivants :

* Base de données
* Système de fichiers
* Appliances réseau
* Pipeline de demande-réponse

Les tests unitaires utilisent des composants fabriqués, connus sous le nom de faux ou *d’objets* *simulés,* à la place de composants d’infrastructure.

Contrairement aux tests unitaires, les tests d’intégration :

* Utilisez les composants réels que l’application utilise dans la production.
* Exiger plus de code et de traitement des données.
* Il faut plus de temps pour courir.

Par conséquent, limitez l’utilisation des tests d’intégration aux scénarios d’infrastructure les plus importants. Si un comportement peut être testé à l’aide d’un test unitaire ou d’un test d’intégration, choisissez le test unitaire.

> [!TIP]
> N’écrivez pas de tests d’intégration pour chaque permutation possible des données et n’ez pas l’accès aux fichiers avec des bases de données et des systèmes de fichiers. Indépendamment du nombre d’endroits dans une application interagissent avec les bases de données et les systèmes de fichiers, un ensemble ciblé de tests de lecture, d’écriture, de mise à jour et de suppression des tests d’intégration sont généralement capables de tester adéquatement les composants des bases de données et des fichiers. Utilisez des tests unitaires pour des tests de routine de la logique de la méthode qui interagissent avec ces composants. Dans les tests unitaires, l’utilisation de faux/mocks d’infrastructure entraîne une exécution plus rapide des tests.

> [!NOTE]
> Dans les discussions sur les tests d’intégration, le projet testé est souvent appelé le *système en cours d’essai*, ou "SUT" pour faire court.
>
> *"SUT" est utilisé tout au long de ce sujet pour se référer à l’application ASP.NET Core testée.*

## <a name="aspnet-core-integration-tests"></a>tests d’intégration de base ASP.NET

Les tests d’intégration dans ASP.NET Core exigent ce qui suit :

* Un projet de test est utilisé pour contenir et exécuter les tests. Le projet d’essai a une référence au SUT.
* Le projet de test crée un hébergeur de test pour le SUT et utilise un client serveur de test pour traiter les demandes et les réponses avec le SUT.
* Un coureur de test est utilisé pour exécuter les tests et signaler les résultats des tests.

Les tests d’intégration suivent une séquence d’événements qui comprennent les étapes *habituelles d’arrangement,* *d’acte*et *d’essai d’Assert* :

1. L’hébergeur du SUT est configuré.
1. Un client serveur de test est créé pour soumettre des demandes à l’application.
1. *L’étape de* test Arrange est exécutée : l’application de test prépare une demande.
1. L’étape de test *de la Loi* est exécutée : le client soumet la demande et reçoit la réponse.
1. *L’étape* de test Assert est exécutée : la réponse *réelle* est validée en tant que *passage* ou *échec* en fonction d’une réponse *attendue.*
1. Le processus se poursuit jusqu’à ce que tous les tests soient exécutés.
1. Les résultats des tests sont communiqués.

Habituellement, l’hébergeur de test est configuré différemment de l’hébergeur Web normal de l’application pour les essais. Par exemple, une base de données différente ou différents paramètres d’application peuvent être utilisés pour les tests.

Les composants d’infrastructure, tels que l’hébergeur de test et le serveur de test en mémoire ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)), sont fournis ou gérés par le package [Microsoft.AspNetCore.Mvc.Testing.](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) L’utilisation de ce paquet simplifie la création et l’exécution des tests.

Le `Microsoft.AspNetCore.Mvc.Testing` paquet s’occupe des tâches suivantes :

* Copies du fichier des dépendances (*.deps*) de la SUT dans l’annuaire *des bacs* du projet d’essai.
* Définit la racine de [contenu](xref:fundamentals/index#content-root) à la racine du projet du SUT de sorte que des fichiers statiques et des pages/vues sont trouvés lorsque les tests sont exécutés.
* Fournit la classe [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) pour rationaliser bootstrapping le SUT avec `TestServer`.

La documentation [des tests unitaire](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) décrit comment mettre en place un projet d’essai et un coureur d’essai, ainsi que des instructions détaillées sur la façon d’exécuter des tests et des recommandations sur la façon de nommer les tests et les classes de test.

> [!NOTE]
> Lors de la création d’un projet de test pour une application, séparez les tests unitaires des tests d’intégration dans différents projets. Cela permet de s’assurer que les composants d’analyse d’infrastructure ne sont pas accidentellement inclus dans les tests unitaires. La séparation des tests d’unité et d’intégration permet également de contrôler l’ensemble des tests qui sont exécutés.

Il n’y a pratiquement aucune différence entre la configuration pour les tests des applications Razor Pages et les applications MVC. La seule différence réside dans la façon dont les tests sont nommés. Dans une application Razor Pages, les tests des paramètres de page sont `IndexPageTests` généralement nommés d’après la classe de modèle de page (par exemple, pour tester l’intégration des composants pour la page Index). Dans une application MVC, les tests sont généralement organisés par des classes `HomeControllerTests` de contrôleur et nommés d’après les contrôleurs qu’ils testent (par exemple, pour tester l’intégration des composants pour le contrôleur à domicile).

## <a name="test-app-prerequisites"></a>Conditions préalables de l’application de test

Le projet d’essai doit :

* Référence des paquets suivants :
  * [Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)
  * [Microsoft.AspNetCore.Mvc.Testing (en anglais seulement)](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/)
* Spécifiez le SDK`<Project Sdk="Microsoft.NET.Sdk.Web">`Web dans le fichier du projet ( ). Le Web SDK est nécessaire lors du référencement de la [métapackage Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).

Ces conditions préalables peuvent être vues dans [l’application d’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/). Inspectez les *tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj.* L’application d’échantillon utilise le cadre de test [xUnit](https://xunit.github.io/) et la bibliothèque de parser [AngleSharp,](https://anglesharp.github.io/) de sorte que l’application d’échantillon fait également référence :

* [xunit](https://www.nuget.org/packages/xunit/)
* [xunit.runner.visualstudio](https://www.nuget.org/packages/xunit.runner.visualstudio/)
* [AngleSharp (angleSharp)](https://www.nuget.org/packages/AngleSharp/)

## <a name="sut-environment"></a>Environnement SUT

Si [l’environnement](xref:fundamentals/environments) du SUT n’est pas défini, l’environnement est par défaut pour le développement.

## <a name="basic-tests-with-the-default-webapplicationfactory"></a>Tests de base avec la webApplicationFactory par défaut

[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) est utilisé pour créer un [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) pour les tests d’intégration. `TEntryPoint`est la classe de point d’entrée du SUT, généralement la `Startup` classe.

Les classes de test implémentent une interface *de montage* de classe ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) pour indiquer que la classe contient des tests et fournissent des instances d’objets partagés à travers les tests de la classe.

La classe `BasicTests`d’essai `WebApplicationFactory` suivante, , utilise le bootstrap le SUT `Get_EndpointsReturnSuccessAndCorrectContentType`et de fournir un [HttpClient](/dotnet/api/system.net.http.httpclient) à une méthode d’essai, . La méthode vérifie si le code d’état de réponse est réussi (codes d’état dans la plage 200-299) et l’en-tête `Content-Type` est `text/html; charset=utf-8` pour plusieurs pages d’application.

[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) crée un `HttpClient` exemple qui suit automatiquement les redirections et gère les cookies.

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

Par défaut, les cookies non essentiels ne sont pas conservés entre les demandes lorsque la [politique de consentement GDPR](xref:security/gdpr) est activée. Pour préserver les cookies non essentiels, tels que ceux utilisés par le fournisseur TempData, marquez-les comme essentiels dans vos tests. Pour obtenir des instructions sur le marquage d’un cookie comme essentiel, voir [les cookies Essentiels](xref:security/gdpr#essential-cookies).

## <a name="customize-webapplicationfactory"></a>Personnaliser WebApplicationFactory

La configuration de l’hôte Web peut être `WebApplicationFactory` créée indépendamment des classes de test en héritant de créer une ou plusieurs usines personnalisées :

1. Héritez de `WebApplicationFactory` [configureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost)et remplacez. [L’IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) permet la configuration de la collection de services avec [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices):

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   L’ensemencement de base `InitializeDbForTests` de données dans l’application [d’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) est effectué par la méthode. La méthode est décrite dans [l’échantillon de tests d’intégration : Section de l’organisation des applications de test.](#test-app-organization)

2. Utilisez la `CustomWebApplicationFactory` coutume dans les classes de test. L’exemple suivant utilise `IndexPageTests` l’usine de la classe :

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   Le client de l’application de `HttpClient` l’échantillon est configuré pour empêcher les redirections suivantes. Comme expliqué plus tard dans la section [d’authentification Mock,](#mock-authentication) cela permet aux tests de vérifier le résultat de la première réponse de l’application. La première réponse est une redirection `Location` dans beaucoup de ces tests avec un en-tête.

3. Un test typique `HttpClient` utilise les méthodes et les méthodes d’aide pour traiter la demande et la réponse :

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

Toute demande POST au SUT doit satisfaire le contrôle antiforgery qui est automatiquement faite par le [système antiforgery](xref:security/data-protection/introduction)de protection des données de l’application . Afin d’organiser la demande POST d’un test, l’application de test doit :

1. Faites une demande pour la page.
1. Parse le cookie antiforgery et demander le jeton de validation de la réponse.
1. Faites la demande POST avec le cookie antiforgery et demandez le jeton de validation en place.

Les `SendAsync` méthodes d’extension de l’aide (*Helpers/HttpClientExtensions.cs*) et `GetDocumentAsync` la méthode d’aide *(Helpers/HtmlHelpers.cs*) dans [l’application d’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) utilisent le parser [AngleSharp](https://anglesharp.github.io/) pour manipuler le contrôle antiforgery avec les méthodes suivantes :

* `GetDocumentAsync`&ndash; Reçoit le [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) `IHtmlDocument`et retourne un . `GetDocumentAsync`utilise une usine qui prépare une réponse `HttpResponseMessage` *virtuelle* basée sur l’original . Pour plus d’informations, voir la [documentation AngleSharp](https://github.com/AngleSharp/AngleSharp#documentation).
* `SendAsync`méthodes d’extension pour la `HttpClient` composition d’un [httpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) et appeler [SendAsync (HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) pour soumettre des demandes au SUT. Surcharges pour `SendAsync` accepter le`IHtmlFormElement`formulaire HTML ( ) et ce qui suit:
  * Soumettre le bouton`IHtmlElement`du formulaire ( )
  * Collection de`IEnumerable<KeyValuePair<string, string>>`valeurs de formulaire ( )
  * Soumettre des`IHtmlElement`valeurs de`IEnumerable<KeyValuePair<string, string>>`bouton ( ) et de forme ( )

> [!NOTE]
> [AngleSharp](https://anglesharp.github.io/) est une bibliothèque d’analyse tierce utilisée à des fins de démonstration dans ce sujet et l’application d’échantillon. AngleSharp n’est pas pris en charge ou requis pour les tests d’intégration des applications ASP.NET Core. D’autres analyseurs peuvent être utilisés, tels que le [Pack d’agilité Html (HAP)](https://html-agility-pack.net/). Une autre approche consiste à écrire du code pour gérer directement le jeton de vérification des demandes et le cookie antiforgery du système antiforgery.

## <a name="customize-the-client-with-withwebhostbuilder"></a>Personnalisez le client avec WithWebHostBuilder

Lorsque une configuration supplémentaire est nécessaire dans une méthode `WebApplicationFactory` de test, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) crée un nouveau avec un [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) qui est encore personnalisé par configuration.

La `Post_DeleteMessageHandler_ReturnsRedirectToRoot` méthode de test de l’application [d’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) démontre l’utilisation de `WithWebHostBuilder`. Ce test effectue une suppression d’enregistrements dans la base de données en déclenchant une soumission de formulaire dans le SUT.

Étant donné qu’un autre test de la `IndexPageTests` classe effectue une opération qui `Post_DeleteMessageHandler_ReturnsRedirectToRoot` supprime tous les enregistrements de la base de données et peut s’exécuter avant la méthode, la base de données est réédée dans cette méthode de test pour s’assurer qu’un enregistrement est présent pour que le SUT puisse le supprimer. La sélection du premier `messages` bouton de suppression du formulaire dans le SUT est simulée dans la demande au SUT :

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a>Options clients

Le tableau suivant affiche la valeur Par défaut [de WebApplicationFactoryClientOptions disponibles](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) lors de la création d’instances. `HttpClient`

| Option | Description | Default |
| ------ | ----------- | ------- |
| [AutoriserAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | Obtient ou définit `HttpClient` si les instances doivent suivre automatiquement les réponses de redirection. | `true` |
| [BaseAddress (baseAddress)](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | Obtient ou définit l’adresse de base des `HttpClient` instances. | `http://localhost` |
| [HandleCookies (en)](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | Obtient ou `HttpClient` définit si les instances doivent gérer les cookies. | `true` |
| [MaxAutomaticRedirections](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | Obtient ou définit le nombre maximum `HttpClient` de réponses de redirection que les instances doivent suivre. | 7 |

Créez `WebApplicationFactoryClientOptions` la classe et transmettez-la à la méthode [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) (les valeurs par défaut sont affichées dans l’exemple du code) :

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

Les services peuvent être remplacés lors d’un test avec un appel à [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) sur le constructeur hôte. **Pour injecter des services fictifs, `Startup` le `Startup.ConfigureServices` SUT doit avoir une classe avec une méthode.**

L’échantillon SUT comprend un service à portée qui renvoie un devis. La citation est intégrée dans un champ caché sur la page Index lorsque la page Index est demandée.

*Services/IQuoteService.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

*Services/QuoteService.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

*Pages/Index.cshtml.cs* :

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

*Pages/Index.cs*:

[!code-cshtml[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

La balisage suivante est générée lorsque l’application SUT est exécutée :

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

Pour tester le service et citer l’injection dans un test d’intégration, un service de simulation est injecté dans le SUT par le test. Le service de simulation remplace `QuoteService` l’application par un service `TestQuoteService`fourni par l’application de test, appelé :

*IntegrationTests.IndexPageTests.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

`ConfigureTestServices`est appelé, et le service de portée est enregistré:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

La majoration produite lors de l’exécution du `TestQuoteService`test reflète le texte de citation fourni par , donc l’affirmation passe:

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="mock-authentication"></a>Authentification simulée

Les tests `AuthTests` dans la classe vérifient qu’un point de terminaison sécurisé :

* Redirige un utilisateur non authentique vers la page Login de l’application.
* Retourne le contenu d’un utilisateur authentifié.

Dans le SUT, la `/SecurePage` page utilise une convention [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) pour appliquer un [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) à la page. Pour plus d’informations, voir [les conventions d’autorisation de Razor Pages](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

Dans `Get_SecurePageRedirectsAnUnauthenticatedUser` le test, un [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) est configuré pour refuser `false`les redirections en définissant [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) à :

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet2)]

En interdisant au client de suivre la redirection, les vérifications suivantes peuvent être effectuées :

* Le code d’état retourné par le SUT peut être vérifié par rapport au résultat [attendu httpStatusCode.Redirect,](/dotnet/api/system.net.httpstatuscode) et non au code d’état final après la redirection vers la page Login, qui serait [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode).
* La `Location` valeur d’en-tête dans les en-têtes de réponse est vérifiée pour confirmer qu’il commence par `http://localhost/Identity/Account/Login`, pas la réponse finale de la page Login, où l’en-tête `Location` ne serait pas présent.

L’application de <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> test peut se moquer d’un in [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) afin de tester les aspects de l’authentification et de l’autorisation. Un scénario minimal renvoie un [AuthenticateResult.Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*):

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet4&highlight=11-18)]

Le `TestAuthHandler` est appelé à authentifier un utilisateur `Test` `AddAuthentication` lorsque le `ConfigureTestServices`système d’authentification est réglé à l’endroit où est enregistré pour :

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet3&highlight=7-12)]

Pour plus `WebApplicationFactoryClientOptions`d’informations sur , voir la section [options Client.](#client-options)

## <a name="set-the-environment"></a>Définir l’environnement

Par défaut, l’environnement d’hôte et d’application du SUT est configuré pour utiliser l’environnement de développement. Pour remplacer l’environnement du SUT :

* Définissez `ASPNETCORE_ENVIRONMENT` la variable de `Staging` `Production`l’environnement (par exemple, , , ou toute autre valeur personnalisée, telle que `Testing`).
* Remplacer `CreateHostBuilder` dans l’application de test pour `ASPNETCORE`lire les variables de l’environnement préfixées avec .

```csharp
protected override IHostBuilder CreateHostBuilder() => 
    base.CreateHostBuilder()
        .ConfigureHostConfiguration(
            config => config.AddEnvironmentVariables("ASPNETCORE"));
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a>Comment l’infrastructure de test déduit le chemin de racine du contenu de l’application

Le `WebApplicationFactory` constructeur déduit le chemin [de racine de contenu](xref:fundamentals/index#content-root) de l’application en recherchant un [WebApplicationFactoryContentRotentAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) sur l’assemblage contenant les tests d’intégration avec une clé égale à l’assemblage `TEntryPoint` `System.Reflection.Assembly.FullName`. Dans le cas où un attribut `WebApplicationFactory` avec la clé correcte n’est pas trouvé, retombe à la recherche d’un fichier de solution (*.sln*) et joint le nom de l’assemblage `TEntryPoint` à l’annuaire de la solution. Le répertoire racine de l’application (le chemin de racine de contenu) est utilisé pour découvrir les vues et les fichiers de contenu.

## <a name="disable-shadow-copying"></a>Copie d’ombre de désactiver

La copie d’ombre provoque l’exécution des tests dans un répertoire différent de celui du répertoire de sortie. Pour que les tests fonctionnent correctement, la copie d’ombre doit être désactivée. [L’application d’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) utilise xUnit et désactive la copie d’ombre pour xUnit en incluant un fichier *xunit.runner.json* avec le réglage de configuration correct. Pour plus d’informations, voir [Configuring xUnit avec JSON](https://xunit.github.io/docs/configuring-with-json.html).

Ajouter le fichier *xunit.runner.json* à la racine du projet de test avec le contenu suivant :

```json
{
  "shadowCopy": false
}
```

Si vous utilisez Visual Studio, définissez la copie du fichier à la propriété **de l’annuaire de sortie** pour copier **toujours**. Si vous n’utilisez `Content` pas Visual Studio, ajoutez une cible au fichier de projet de l’application de test :

```xml
<ItemGroup>
  <Content Update="xunit.runner.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
  </Content>
</ItemGroup>
```

## <a name="disposal-of-objects"></a>Élimination des objets

Après l’exécution `IClassFixture` des tests de la mise en œuvre, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) et [HttpClient](/dotnet/api/system.net.http.httpclient) sont éliminés lorsque xUnit dispose de la [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1). Si les objets instantanés par le développeur nécessitent `IClassFixture` leur élimination, les disposer dans la mise en œuvre. Pour plus d’informations, voir [Implementing a Dispose method](/dotnet/standard/garbage-collection/implementing-dispose).

## <a name="integration-tests-sample"></a>Échantillon de tests d’intégration

[L’application échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) est composée de deux applications:

| Application | Répertoire du projet | Description |
| --- | ----------------- | ----------- |
| Application de message (le SUT) | *src/RazorPagesProjet* | Permet à un utilisateur d’en ajouter, de supprimer un, de supprimer tous et d’analyser des messages. |
| Tester une application | *tests/RazorPagesProject.Tests* | Utilisé pour tester l’intégration du SUT. |

Les tests peuvent être exécutés à l’aide des fonctionnalités de test intégrées d’un IDE, tels que [Visual Studio](https://visualstudio.microsoft.com). Si vous utilisez [visual Studio Code](https://code.visualstudio.com/) ou la ligne de commande, exécutez la commande suivante à une invite de commande dans le répertoire *tests/RazorPagesProject.Tests* :

```dotnetcli
dotnet test
```

### <a name="message-app-sut-organization"></a>Organisation de l’application de message (SUT)

Le SUT est un système de messages Razor Pages avec les caractéristiques suivantes :

* La page d’index de l’application *(Pages/Index.cshtml* et *Pages/Index.cshtml.cs*) fournit une interface utilisateur et des méthodes de modèle de page pour contrôler l’ajout, la suppression et l’analyse des messages (mots moyens par message).
* Un message est `Message` décrit par la classe (*Data/Message.cs*) avec deux propriétés: `Id` (clé) et `Text` (message). La `Text` propriété est requise et limitée à 200 caractères.
* Les messages sont stockés [à l’aide de la base de données en mémoire d’Entity Framework](/ef/core/providers/in-memory/)&#8224;.
* L’application contient une couche d’accès aux `AppDbContext` données (DAL) dans sa classe de contexte de base de données, (*Data/AppDbContext.cs*).
* Si la base de données est vide sur le démarrage de l’application, le magasin de messages est paraséfié avec trois messages.
* L’application `/SecurePage` comprend un qui ne peut être consulté que par un utilisateur authentifié.

&#8224;Le sujet EF, [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), explique comment utiliser une base de données en mémoire pour les tests avec MSTest. Ce sujet utilise le cadre de test [xUnit.](https://xunit.github.io/) Les concepts de test et les implémentations de tests dans différents cadres de test sont similaires mais non identiques.

Bien que l’application n’utilise pas le modèle de référentiel et n’est pas un exemple efficace du [modèle de l’Unité de travail (UoW),](https://martinfowler.com/eaaCatalog/unitOfWork.html)Razor Pages prend en charge ces modèles de développement. Pour plus d’informations, voir [Concevoir la couche de persistance de l’infrastructure](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) et la logique du contrôleur de [test](/aspnet/core/mvc/controllers/testing) (l’échantillon implémente le modèle de dépôt).

### <a name="test-app-organization"></a>Organisation d’applications de test

L’application de test est une application console à l’intérieur du *répertoire tests/RazorPagesProject.Tests.*

| Annuaire d’applications de test | Description |
| ------------------ | ----------- |
| *AuthTests* | Contient des méthodes de test pour :<ul><li>Accès à une page sécurisée par un utilisateur non athéné.</li><li>Accéder à une page sécurisée par un <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>utilisateur authentifié avec un simulacre .</li><li>Obtenir un profil d’utilisateur GitHub et vérifier la connexion utilisateur du profil.</li></ul> |
| *Tests de base* | Contient une méthode de test pour le routage et le type de contenu. |
| *IntegrationTests* | Contient les tests d’intégration `WebApplicationFactory` pour la page Index à l’aide de la classe personnalisée. |
| *Aides/Services publics* | <ul><li>*Utilities.cs* contient la `InitializeDbForTests` méthode utilisée pour ensemencer la base de données avec des données de test.</li><li>*HtmlHelpers.cs* fournit une méthode pour retourner `IHtmlDocument` un AngleSharp pour une utilisation par les méthodes d’essai.</li><li>*HttpClientExtensions.cs* fournir des surcharges `SendAsync` pour soumettre des demandes au SUT.</li></ul> |

Le cadre d’essai est [xUnit](https://xunit.github.io/). Les tests d’intégration sont effectués à l’aide de [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost), qui comprend le [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver). Étant donné que le package [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) est utilisé `TestHost` pour `TestServer` configurer l’hôte de test et le serveur de test, le paquet et les paquets ne nécessitent pas de références de paquets directs dans le fichier de projet ou la configuration du développeur de l’application de test dans l’application de test.

**Ensemencement de la base de données pour les tests**

Les tests d’intégration nécessitent généralement un petit jeu de données dans la base de données avant l’exécution du test. Par exemple, un test de suppression nécessite une suppression d’enregistrement de base de données, de sorte que la base de données doit avoir au moins un enregistrement pour que la demande de suppression réussisse.

L’application échantillon de graines de la base de données avec trois messages dans *Utilities.cs* que les tests peuvent utiliser quand ils exécutent:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

::: moniker-end

## <a name="additional-resources"></a>Ressources supplémentaires

* [Tests unitaires](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:test/razor-pages-tests>
* <xref:fundamentals/middleware/index>
* <xref:mvc/controllers/testing>
