---
<span data-ttu-id="329b2-101">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="329b2-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="329b2-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="329b2-102">'Blazor'</span></span>
- <span data-ttu-id="329b2-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="329b2-103">'Identity'</span></span>
- <span data-ttu-id="329b2-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="329b2-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="329b2-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="329b2-105">'Razor'</span></span>
- <span data-ttu-id="329b2-106">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="329b2-106">'SignalR' uid:</span></span> 

---
# <a name="integration-tests-in-aspnet-core"></a><span data-ttu-id="329b2-107">Tests d’intégration dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="329b2-107">Integration tests in ASP.NET Core</span></span>

<span data-ttu-id="329b2-108">Par [Javier Calvarro Nelson](https://github.com/javiercn), [Steve Smith](https://ardalis.com/)et [Jos van der til](https://jvandertil.nl)</span><span class="sxs-lookup"><span data-stu-id="329b2-108">By [Javier Calvarro Nelson](https://github.com/javiercn), [Steve Smith](https://ardalis.com/), and [Jos van der Til](https://jvandertil.nl)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="329b2-109">Les tests d’intégration garantissent que les composants d’une application fonctionnent correctement à un niveau qui comprend l’infrastructure de prise en charge de l’application, telle que la base de données, le système de fichiers et le réseau.</span><span class="sxs-lookup"><span data-stu-id="329b2-109">Integration tests ensure that an app's components function correctly at a level that includes the app's supporting infrastructure, such as the database, file system, and network.</span></span> <span data-ttu-id="329b2-110">ASP.NET Core prend en charge les tests d’intégration à l’aide d’une infrastructure de tests unitaires avec un hôte Web de test et un serveur de test en mémoire.</span><span class="sxs-lookup"><span data-stu-id="329b2-110">ASP.NET Core supports integration tests using a unit test framework with a test web host and an in-memory test server.</span></span>

<span data-ttu-id="329b2-111">Cette rubrique suppose une compréhension de base des tests unitaires.</span><span class="sxs-lookup"><span data-stu-id="329b2-111">This topic assumes a basic understanding of unit tests.</span></span> <span data-ttu-id="329b2-112">Si vous n’êtes pas familiarisé avec les concepts de test, consultez la rubrique [tests unitaires dans .net Core et .NET standard](/dotnet/core/testing/) et son contenu lié.</span><span class="sxs-lookup"><span data-stu-id="329b2-112">If unfamiliar with test concepts, see the [Unit Testing in .NET Core and .NET Standard](/dotnet/core/testing/) topic and its linked content.</span></span>

<span data-ttu-id="329b2-113">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="329b2-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="329b2-114">L’exemple d’application est une Razor application pages et suppose une compréhension de base des Razor pages.</span><span class="sxs-lookup"><span data-stu-id="329b2-114">The sample app is a Razor Pages app and assumes a basic understanding of Razor Pages.</span></span> <span data-ttu-id="329b2-115">Si vous n’êtes pas familiarisé avec Razor les pages, consultez les rubriques suivantes :</span><span class="sxs-lookup"><span data-stu-id="329b2-115">If unfamiliar with Razor Pages, see the following topics:</span></span>

* <span data-ttu-id="329b2-116">[Présentation des Razor pages](xref:razor-pages/index)</span><span class="sxs-lookup"><span data-stu-id="329b2-116">[Introduction to Razor Pages](xref:razor-pages/index)</span></span>
* <span data-ttu-id="329b2-117">[Prise en main des Razor pages](xref:tutorials/razor-pages/razor-pages-start)</span><span class="sxs-lookup"><span data-stu-id="329b2-117">[Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start)</span></span>
* <span data-ttu-id="329b2-118">[RazorPages tests unitaires](xref:test/razor-pages-tests)</span><span class="sxs-lookup"><span data-stu-id="329b2-118">[Razor Pages unit tests](xref:test/razor-pages-tests)</span></span>

> [!NOTE]
> <span data-ttu-id="329b2-119">Pour tester la SPAs, nous vous recommandons d’utiliser un outil tel que le [sélénium](https://www.seleniumhq.org/), qui peut automatiser un navigateur.</span><span class="sxs-lookup"><span data-stu-id="329b2-119">For testing SPAs, we recommended a tool such as [Selenium](https://www.seleniumhq.org/), which can automate a browser.</span></span>

## <a name="introduction-to-integration-tests"></a><span data-ttu-id="329b2-120">Présentation des tests d’intégration</span><span class="sxs-lookup"><span data-stu-id="329b2-120">Introduction to integration tests</span></span>

<span data-ttu-id="329b2-121">Les tests d’intégration évaluent les composants d’une application à un niveau plus large que les [tests unitaires](/dotnet/core/testing/).</span><span class="sxs-lookup"><span data-stu-id="329b2-121">Integration tests evaluate an app's components on a broader level than [unit tests](/dotnet/core/testing/).</span></span> <span data-ttu-id="329b2-122">Les tests unitaires sont utilisés pour tester des composants logiciels isolés, tels que des méthodes de classe individuelles.</span><span class="sxs-lookup"><span data-stu-id="329b2-122">Unit tests are used to test isolated software components, such as individual class methods.</span></span> <span data-ttu-id="329b2-123">Les tests d’intégration confirment que deux ou plusieurs composants d’application fonctionnent ensemble pour produire un résultat attendu, y compris éventuellement chaque composant requis pour traiter entièrement une demande.</span><span class="sxs-lookup"><span data-stu-id="329b2-123">Integration tests confirm that two or more app components work together to produce an expected result, possibly including every component required to fully process a request.</span></span>

<span data-ttu-id="329b2-124">Ces tests plus larges sont utilisés pour tester l’infrastructure de l’application et l’infrastructure entière, y compris les composants suivants :</span><span class="sxs-lookup"><span data-stu-id="329b2-124">These broader tests are used to test the app's infrastructure and whole framework, often including the following components:</span></span>

* <span data-ttu-id="329b2-125">Base de données</span><span class="sxs-lookup"><span data-stu-id="329b2-125">Database</span></span>
* <span data-ttu-id="329b2-126">Système de fichiers</span><span class="sxs-lookup"><span data-stu-id="329b2-126">File system</span></span>
* <span data-ttu-id="329b2-127">Appliances réseau</span><span class="sxs-lookup"><span data-stu-id="329b2-127">Network appliances</span></span>
* <span data-ttu-id="329b2-128">Pipeline de requête-réponse</span><span class="sxs-lookup"><span data-stu-id="329b2-128">Request-response pipeline</span></span>

<span data-ttu-id="329b2-129">Les tests unitaires utilisent des composants fabriqués, appelés objets *substituts* ou *factices*, à la place des composants d’infrastructure.</span><span class="sxs-lookup"><span data-stu-id="329b2-129">Unit tests use fabricated components, known as *fakes* or *mock objects*, in place of infrastructure components.</span></span>

<span data-ttu-id="329b2-130">Contrairement aux tests unitaires, les tests d’intégration :</span><span class="sxs-lookup"><span data-stu-id="329b2-130">In contrast to unit tests, integration tests:</span></span>

* <span data-ttu-id="329b2-131">Utilisez les composants réels utilisés par l’application en production.</span><span class="sxs-lookup"><span data-stu-id="329b2-131">Use the actual components that the app uses in production.</span></span>
* <span data-ttu-id="329b2-132">Nécessitent davantage de code et de traitement des données.</span><span class="sxs-lookup"><span data-stu-id="329b2-132">Require more code and data processing.</span></span>
* <span data-ttu-id="329b2-133">Prend plus de temps pour s’exécuter.</span><span class="sxs-lookup"><span data-stu-id="329b2-133">Take longer to run.</span></span>

<span data-ttu-id="329b2-134">Par conséquent, limitez l’utilisation des tests d’intégration aux scénarios d’infrastructure les plus importants.</span><span class="sxs-lookup"><span data-stu-id="329b2-134">Therefore, limit the use of integration tests to the most important infrastructure scenarios.</span></span> <span data-ttu-id="329b2-135">Si un comportement peut être testé à l’aide d’un test unitaire ou d’un test d’intégration, choisissez le test unitaire.</span><span class="sxs-lookup"><span data-stu-id="329b2-135">If a behavior can be tested using either a unit test or an integration test, choose the unit test.</span></span>

> [!TIP]
> <span data-ttu-id="329b2-136">N’écrivez pas de tests d’intégration pour chaque permutation possible d’accès aux données et aux fichiers avec les bases de données et les systèmes de fichiers.</span><span class="sxs-lookup"><span data-stu-id="329b2-136">Don't write integration tests for every possible permutation of data and file access with databases and file systems.</span></span> <span data-ttu-id="329b2-137">Quel que soit le nombre d’emplacements au sein d’une application qui interagissent avec les bases de données et les systèmes de fichiers, un ensemble de tests d’intégration de lecture, d’écriture, de mise à jour et de suppression est généralement en mesure de tester correctement les composants de base de données et de système de fichiers.</span><span class="sxs-lookup"><span data-stu-id="329b2-137">Regardless of how many places across an app interact with databases and file systems, a focused set of read, write, update, and delete integration tests are usually capable of adequately testing database and file system components.</span></span> <span data-ttu-id="329b2-138">Utilisez des tests unitaires pour les tests de routine de la logique de méthode qui interagissent avec ces composants.</span><span class="sxs-lookup"><span data-stu-id="329b2-138">Use unit tests for routine tests of method logic that interact with these components.</span></span> <span data-ttu-id="329b2-139">Dans les tests unitaires, l’utilisation de substituts ou de simulacres d’infrastructure entraîne une exécution plus rapide des tests.</span><span class="sxs-lookup"><span data-stu-id="329b2-139">In unit tests, the use of infrastructure fakes/mocks result in faster test execution.</span></span>

> [!NOTE]
> <span data-ttu-id="329b2-140">Dans les discussions sur les tests d’intégration, le projet testé est fréquemment appelé le *système en cours de test*ou « St » pour l’instant.</span><span class="sxs-lookup"><span data-stu-id="329b2-140">In discussions of integration tests, the tested project is frequently called the *System Under Test*, or "SUT" for short.</span></span>
>
> <span data-ttu-id="329b2-141">*« St » est utilisé dans cette rubrique pour faire référence à l’application ASP.NET Core testée.*</span><span class="sxs-lookup"><span data-stu-id="329b2-141">*"SUT" is used throughout this topic to refer to the tested ASP.NET Core app.*</span></span>

## <a name="aspnet-core-integration-tests"></a><span data-ttu-id="329b2-142">ASP.NET Core les tests d’intégration</span><span class="sxs-lookup"><span data-stu-id="329b2-142">ASP.NET Core integration tests</span></span>

<span data-ttu-id="329b2-143">Les tests d’intégration dans ASP.NET Core requièrent les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="329b2-143">Integration tests in ASP.NET Core require the following:</span></span>

* <span data-ttu-id="329b2-144">Un projet de test est utilisé pour contenir et exécuter les tests.</span><span class="sxs-lookup"><span data-stu-id="329b2-144">A test project is used to contain and execute the tests.</span></span> <span data-ttu-id="329b2-145">Le projet de test a une référence à St.</span><span class="sxs-lookup"><span data-stu-id="329b2-145">The test project has a reference to the SUT.</span></span>
* <span data-ttu-id="329b2-146">Le projet de test crée un hôte Web de test pour le St et utilise un client de serveur de test pour gérer les demandes et les réponses avec le St.</span><span class="sxs-lookup"><span data-stu-id="329b2-146">The test project creates a test web host for the SUT and uses a test server client to handle requests and responses with the SUT.</span></span>
* <span data-ttu-id="329b2-147">Un testeur de test est utilisé pour exécuter les tests et signaler les résultats des tests.</span><span class="sxs-lookup"><span data-stu-id="329b2-147">A test runner is used to execute the tests and report the test results.</span></span>

<span data-ttu-id="329b2-148">Les tests d’intégration suivent une séquence d’événements qui incluent les étapes de test *arrange*, *Act*et *Assert* habituelles :</span><span class="sxs-lookup"><span data-stu-id="329b2-148">Integration tests follow a sequence of events that include the usual *Arrange*, *Act*, and *Assert* test steps:</span></span>

1. <span data-ttu-id="329b2-149">L’hôte Web de St est configuré.</span><span class="sxs-lookup"><span data-stu-id="329b2-149">The SUT's web host is configured.</span></span>
1. <span data-ttu-id="329b2-150">Un client de serveur de test est créé pour envoyer des demandes à l’application.</span><span class="sxs-lookup"><span data-stu-id="329b2-150">A test server client is created to submit requests to the app.</span></span>
1. <span data-ttu-id="329b2-151">L’étape *organiser* le test est exécutée : l’application de test prépare une demande.</span><span class="sxs-lookup"><span data-stu-id="329b2-151">The *Arrange* test step is executed: The test app prepares a request.</span></span>
1. <span data-ttu-id="329b2-152">L’étape de test *Act* est exécutée : le client envoie la demande et reçoit la réponse.</span><span class="sxs-lookup"><span data-stu-id="329b2-152">The *Act* test step is executed: The client submits the request and receives the response.</span></span>
1. <span data-ttu-id="329b2-153">L’étape de test d' *assertion* est exécutée : la réponse *réelle* est validée en tant que *réussite* ou *échec* en fonction d’une réponse *attendue* .</span><span class="sxs-lookup"><span data-stu-id="329b2-153">The *Assert* test step is executed: The *actual* response is validated as a *pass* or *fail* based on an *expected* response.</span></span>
1. <span data-ttu-id="329b2-154">Le processus se poursuit jusqu’à ce que tous les tests soient exécutés.</span><span class="sxs-lookup"><span data-stu-id="329b2-154">The process continues until all of the tests are executed.</span></span>
1. <span data-ttu-id="329b2-155">Les résultats des tests sont signalés.</span><span class="sxs-lookup"><span data-stu-id="329b2-155">The test results are reported.</span></span>

<span data-ttu-id="329b2-156">En règle générale, l’hôte Web de test est configuré différemment de l’hôte Web normal de l’application pour les séries de tests.</span><span class="sxs-lookup"><span data-stu-id="329b2-156">Usually, the test web host is configured differently than the app's normal web host for the test runs.</span></span> <span data-ttu-id="329b2-157">Par exemple, une base de données différente ou des paramètres d’application différents peuvent être utilisés pour les tests.</span><span class="sxs-lookup"><span data-stu-id="329b2-157">For example, a different database or different app settings might be used for the tests.</span></span>

<span data-ttu-id="329b2-158">Les composants d’infrastructure, tels que l’hôte Web de test et le serveur de test en mémoire ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)), sont fournis ou gérés par le package [Microsoft. AspNetCore. Mvc. testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) .</span><span class="sxs-lookup"><span data-stu-id="329b2-158">Infrastructure components, such as the test web host and in-memory test server ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)), are provided or managed by the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package.</span></span> <span data-ttu-id="329b2-159">L’utilisation de ce package simplifie la création et l’exécution des tests.</span><span class="sxs-lookup"><span data-stu-id="329b2-159">Use of this package streamlines test creation and execution.</span></span>

<span data-ttu-id="329b2-160">Le `Microsoft.AspNetCore.Mvc.Testing` package gère les tâches suivantes :</span><span class="sxs-lookup"><span data-stu-id="329b2-160">The `Microsoft.AspNetCore.Mvc.Testing` package handles the following tasks:</span></span>

* <span data-ttu-id="329b2-161">Copie le fichier de dépendances (*. DEPS*) à partir du St dans le répertoire *bin* du projet de test.</span><span class="sxs-lookup"><span data-stu-id="329b2-161">Copies the dependencies file (*.deps*) from the SUT into the test project's *bin* directory.</span></span>
* <span data-ttu-id="329b2-162">Définit la racine du [contenu](xref:fundamentals/index#content-root) sur la racine du projet St pour que les fichiers et les pages statiques soient trouvés lors de l’exécution des tests.</span><span class="sxs-lookup"><span data-stu-id="329b2-162">Sets the [content root](xref:fundamentals/index#content-root) to the SUT's project root so that static files and pages/views are found when the tests are executed.</span></span>
* <span data-ttu-id="329b2-163">Fournit la classe [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) pour simplifier l’amorçage du St avec `TestServer` .</span><span class="sxs-lookup"><span data-stu-id="329b2-163">Provides the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) class to streamline bootstrapping the SUT with `TestServer`.</span></span>

<span data-ttu-id="329b2-164">La documentation sur les [tests unitaires](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) décrit comment configurer un projet de test et Test Runner, ainsi que des instructions détaillées sur la façon d’exécuter des tests et des recommandations sur la façon de nommer des tests et des classes de test.</span><span class="sxs-lookup"><span data-stu-id="329b2-164">The [unit tests](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) documentation describes how to set up a test project and test runner, along with detailed instructions on how to run tests and recommendations for how to name tests and test classes.</span></span>

> [!NOTE]
> <span data-ttu-id="329b2-165">Lorsque vous créez un projet de test pour une application, séparez les tests unitaires des tests d’intégration dans différents projets.</span><span class="sxs-lookup"><span data-stu-id="329b2-165">When creating a test project for an app, separate the unit tests from the integration tests into different projects.</span></span> <span data-ttu-id="329b2-166">Cela permet de s’assurer que les composants de test d’infrastructure ne sont pas accidentellement inclus dans les tests unitaires.</span><span class="sxs-lookup"><span data-stu-id="329b2-166">This helps ensure that infrastructure testing components aren't accidentally included in the unit tests.</span></span> <span data-ttu-id="329b2-167">La séparation des tests d’unité et d’intégration permet également de contrôler l’ensemble des tests exécutés.</span><span class="sxs-lookup"><span data-stu-id="329b2-167">Separation of unit and integration tests also allows control over which set of tests are run.</span></span>

<span data-ttu-id="329b2-168">Il n’existe pratiquement aucune différence entre la configuration des tests d' Razor applications de pages et les applications MVC.</span><span class="sxs-lookup"><span data-stu-id="329b2-168">There's virtually no difference between the configuration for tests of Razor Pages apps and MVC apps.</span></span> <span data-ttu-id="329b2-169">La seule différence réside dans la façon dont les tests sont nommés.</span><span class="sxs-lookup"><span data-stu-id="329b2-169">The only difference is in how the tests are named.</span></span> <span data-ttu-id="329b2-170">Dans une Razor application de pages, les tests des points de terminaison de page sont généralement nommés après la classe de modèle de page (par exemple, `IndexPageTests` pour tester l’intégration des composants pour la page d’index).</span><span class="sxs-lookup"><span data-stu-id="329b2-170">In a Razor Pages app, tests of page endpoints are usually named after the page model class (for example, `IndexPageTests` to test component integration for the Index page).</span></span> <span data-ttu-id="329b2-171">Dans une application MVC, les tests sont généralement organisés par classes de contrôleur et nommés après les contrôleurs testés (par exemple, `HomeControllerTests` pour tester l’intégration des composants pour le contrôleur de démarrage).</span><span class="sxs-lookup"><span data-stu-id="329b2-171">In an MVC app, tests are usually organized by controller classes and named after the controllers they test (for example, `HomeControllerTests` to test component integration for the Home controller).</span></span>

## <a name="test-app-prerequisites"></a><span data-ttu-id="329b2-172">Conditions préalables pour les applications de test</span><span class="sxs-lookup"><span data-stu-id="329b2-172">Test app prerequisites</span></span>

<span data-ttu-id="329b2-173">Le projet de test doit :</span><span class="sxs-lookup"><span data-stu-id="329b2-173">The test project must:</span></span>

* <span data-ttu-id="329b2-174">Référencez le package [Microsoft. AspNetCore. Mvc. testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) .</span><span class="sxs-lookup"><span data-stu-id="329b2-174">Reference the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package.</span></span>
* <span data-ttu-id="329b2-175">Spécifiez le kit de développement logiciel (SDK) Web dans le fichier projet ( `<Project Sdk="Microsoft.NET.Sdk.Web">` ).</span><span class="sxs-lookup"><span data-stu-id="329b2-175">Specify the Web SDK in the project file (`<Project Sdk="Microsoft.NET.Sdk.Web">`).</span></span>

<span data-ttu-id="329b2-176">Ces conditions préalables peuvent être consultées dans l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/).</span><span class="sxs-lookup"><span data-stu-id="329b2-176">These prerequisites can be seen in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/).</span></span> <span data-ttu-id="329b2-177">Inspectez le fichier *tests/RazorPagesProject. tests/RazorPagesProject. tests. csproj* .</span><span class="sxs-lookup"><span data-stu-id="329b2-177">Inspect the *tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj* file.</span></span> <span data-ttu-id="329b2-178">L’exemple d’application utilise l’infrastructure de test [xUnit](https://xunit.github.io/) et la bibliothèque de l’analyseur [AngleSharp](https://anglesharp.github.io/) , de sorte que l’exemple d’application référence également :</span><span class="sxs-lookup"><span data-stu-id="329b2-178">The sample app uses the [xUnit](https://xunit.github.io/) test framework and the [AngleSharp](https://anglesharp.github.io/) parser library, so the sample app also references:</span></span>

* [<span data-ttu-id="329b2-179">xUnit</span><span class="sxs-lookup"><span data-stu-id="329b2-179">xunit</span></span>](https://www.nuget.org/packages/xunit)
* [<span data-ttu-id="329b2-180">xUnit. Runner. VisualStudio</span><span class="sxs-lookup"><span data-stu-id="329b2-180">xunit.runner.visualstudio</span></span>](https://www.nuget.org/packages/xunit.runner.visualstudio)
* [<span data-ttu-id="329b2-181">AngleSharp</span><span class="sxs-lookup"><span data-stu-id="329b2-181">AngleSharp</span></span>](https://www.nuget.org/packages/AngleSharp)

<span data-ttu-id="329b2-182">Entity Framework Core est également utilisé dans les tests.</span><span class="sxs-lookup"><span data-stu-id="329b2-182">Entity Framework Core is also used in the tests.</span></span> <span data-ttu-id="329b2-183">L’application référence :</span><span class="sxs-lookup"><span data-stu-id="329b2-183">The app references:</span></span>

* [<span data-ttu-id="329b2-184">Microsoft. AspNetCore. Diagnostics. EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="329b2-184">Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore)
* <span data-ttu-id="329b2-185">[Microsoft. AspNetCore. Identity . EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity.EntityFrameworkCore)</span><span class="sxs-lookup"><span data-stu-id="329b2-185">[Microsoft.AspNetCore.Identity.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity.EntityFrameworkCore)</span></span>
* [<span data-ttu-id="329b2-186">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="329b2-186">Microsoft.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore)
* [<span data-ttu-id="329b2-187">Microsoft.EntityFrameworkCore.InMemory</span><span class="sxs-lookup"><span data-stu-id="329b2-187">Microsoft.EntityFrameworkCore.InMemory</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory)
* [<span data-ttu-id="329b2-188">Microsoft. EntityFrameworkCore. Tools</span><span class="sxs-lookup"><span data-stu-id="329b2-188">Microsoft.EntityFrameworkCore.Tools</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools)

## <a name="sut-environment"></a><span data-ttu-id="329b2-189">Environnement St</span><span class="sxs-lookup"><span data-stu-id="329b2-189">SUT environment</span></span>

<span data-ttu-id="329b2-190">Si l' [environnement](xref:fundamentals/environments) de St n’est pas défini, l’environnement est par défaut développement.</span><span class="sxs-lookup"><span data-stu-id="329b2-190">If the SUT's [environment](xref:fundamentals/environments) isn't set, the environment defaults to Development.</span></span>

## <a name="basic-tests-with-the-default-webapplicationfactory"></a><span data-ttu-id="329b2-191">Tests de base avec le WebApplicationFactory par défaut</span><span class="sxs-lookup"><span data-stu-id="329b2-191">Basic tests with the default WebApplicationFactory</span></span>

<span data-ttu-id="329b2-192">[WebApplicationFactory \< TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) est utilisé pour créer un [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) pour les tests d’intégration.</span><span class="sxs-lookup"><span data-stu-id="329b2-192">[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) is used to create a [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) for the integration tests.</span></span> <span data-ttu-id="329b2-193">`TEntryPoint`est la classe de point d’entrée de St, généralement la `Startup` classe.</span><span class="sxs-lookup"><span data-stu-id="329b2-193">`TEntryPoint` is the entry point class of the SUT, usually the `Startup` class.</span></span>

<span data-ttu-id="329b2-194">Les classes de test implémentent une interface de *contexte de classe* ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) pour indiquer que la classe contient des tests et fournir des instances d’objet partagé dans les tests de la classe.</span><span class="sxs-lookup"><span data-stu-id="329b2-194">Test classes implement a *class fixture* interface ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) to indicate the class contains tests and provide shared object instances across the tests in the class.</span></span>

<span data-ttu-id="329b2-195">La classe de test suivante, `BasicTests` , utilise le `WebApplicationFactory` pour démarrer le St et fournir un [httpclient](/dotnet/api/system.net.http.httpclient) à une méthode de test, `Get_EndpointsReturnSuccessAndCorrectContentType` .</span><span class="sxs-lookup"><span data-stu-id="329b2-195">The following test class, `BasicTests`, uses the `WebApplicationFactory` to bootstrap the SUT and provide an [HttpClient](/dotnet/api/system.net.http.httpclient) to a test method, `Get_EndpointsReturnSuccessAndCorrectContentType`.</span></span> <span data-ttu-id="329b2-196">La méthode vérifie si le code d’état de réponse a réussi (codes d’État dans la plage 200-299) et si l' `Content-Type` en-tête est `text/html; charset=utf-8` pour plusieurs pages d’application.</span><span class="sxs-lookup"><span data-stu-id="329b2-196">The method checks if the response status code is successful (status codes in the range 200-299) and the `Content-Type` header is `text/html; charset=utf-8` for several app pages.</span></span>

<span data-ttu-id="329b2-197">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) crée une instance de `HttpClient` qui suit automatiquement les redirections et gère les cookies.</span><span class="sxs-lookup"><span data-stu-id="329b2-197">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) creates an instance of `HttpClient` that automatically follows redirects and handles cookies.</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

<span data-ttu-id="329b2-198">Par défaut, les cookies non essentiels ne sont pas conservés entre les demandes lorsque la [stratégie de consentement RGPD](xref:security/gdpr) est activée.</span><span class="sxs-lookup"><span data-stu-id="329b2-198">By default, non-essential cookies aren't preserved across requests when the [GDPR consent policy](xref:security/gdpr) is enabled.</span></span> <span data-ttu-id="329b2-199">Pour conserver les cookies non essentiels, tels que ceux utilisés par le fournisseur TempData, marquez-les comme essentiels dans vos tests.</span><span class="sxs-lookup"><span data-stu-id="329b2-199">To preserve non-essential cookies, such as those used by the TempData provider, mark them as essential in your tests.</span></span> <span data-ttu-id="329b2-200">Pour obtenir des instructions sur le marquage d’un cookie comme étant essentiel, consultez [les cookies essentiels](xref:security/gdpr#essential-cookies).</span><span class="sxs-lookup"><span data-stu-id="329b2-200">For instructions on marking a cookie as essential, see [Essential cookies](xref:security/gdpr#essential-cookies).</span></span>

## <a name="customize-webapplicationfactory"></a><span data-ttu-id="329b2-201">Personnaliser WebApplicationFactory</span><span class="sxs-lookup"><span data-stu-id="329b2-201">Customize WebApplicationFactory</span></span>

<span data-ttu-id="329b2-202">La configuration d’hôte Web peut être créée indépendamment des classes de test en héritant de `WebApplicationFactory` pour créer une ou plusieurs fabriques personnalisées :</span><span class="sxs-lookup"><span data-stu-id="329b2-202">Web host configuration can be created independently of the test classes by inheriting from `WebApplicationFactory` to create one or more custom factories:</span></span>

1. <span data-ttu-id="329b2-203">Hériter de `WebApplicationFactory` et substituer [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost).</span><span class="sxs-lookup"><span data-stu-id="329b2-203">Inherit from `WebApplicationFactory` and override [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost).</span></span> <span data-ttu-id="329b2-204">[IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) permet la configuration de la collection de services avec [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices):</span><span class="sxs-lookup"><span data-stu-id="329b2-204">The [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) allows the configuration of the service collection with [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices):</span></span>

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   <span data-ttu-id="329b2-205">L’amorçage de base de données dans l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) est effectué par la `InitializeDbForTests` méthode.</span><span class="sxs-lookup"><span data-stu-id="329b2-205">Database seeding in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is performed by the `InitializeDbForTests` method.</span></span> <span data-ttu-id="329b2-206">La méthode est décrite dans la section [exemple de tests d’intégration : organisation](#test-app-organization) d’une application de test.</span><span class="sxs-lookup"><span data-stu-id="329b2-206">The method is described in the [Integration tests sample: Test app organization](#test-app-organization) section.</span></span>

   <span data-ttu-id="329b2-207">Le contexte de base de données de St est inscrit dans sa `Startup.ConfigureServices` méthode.</span><span class="sxs-lookup"><span data-stu-id="329b2-207">The SUT's database context is registered in its `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="329b2-208">Le rappel de l’application de test `builder.ConfigureServices` est exécuté *après* l’exécution du code de l’application `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="329b2-208">The test app's `builder.ConfigureServices` callback is executed *after* the app's `Startup.ConfigureServices` code is executed.</span></span> <span data-ttu-id="329b2-209">L’ordre d’exécution est une modification avec rupture pour l' [hôte générique](xref:fundamentals/host/generic-host) avec la version de ASP.net Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="329b2-209">The execution order is a breaking change for the [Generic Host](xref:fundamentals/host/generic-host) with the release of ASP.NET Core 3.0.</span></span> <span data-ttu-id="329b2-210">Pour utiliser une base de données différente pour les tests que la base de données de l’application, le contexte de base de données de l’application doit être remplacé dans `builder.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="329b2-210">To use a different database for the tests than the app's database, the app's database context must be replaced in `builder.ConfigureServices`.</span></span>

   <span data-ttu-id="329b2-211">Pour les SUTs qui utilisent toujours l' [hôte Web](xref:fundamentals/host/web-host), le rappel de l’application de test `builder.ConfigureServices` est exécuté *avant* le code du St `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="329b2-211">For SUTs that still use the [Web Host](xref:fundamentals/host/web-host), the test app's `builder.ConfigureServices` callback is executed *before* the SUT's `Startup.ConfigureServices` code.</span></span> <span data-ttu-id="329b2-212">Le rappel de l’application de test `builder.ConfigureTestServices` est exécuté *après*.</span><span class="sxs-lookup"><span data-stu-id="329b2-212">The test app's `builder.ConfigureTestServices` callback is executed *after*.</span></span>

   <span data-ttu-id="329b2-213">L’exemple d’application recherche le descripteur de service pour le contexte de base de données et utilise le descripteur pour supprimer l’inscription du service.</span><span class="sxs-lookup"><span data-stu-id="329b2-213">The sample app finds the service descriptor for the database context and uses the descriptor to remove the service registration.</span></span> <span data-ttu-id="329b2-214">Ensuite, la fabrique ajoute un nouveau `ApplicationDbContext` qui utilise une base de données en mémoire pour les tests.</span><span class="sxs-lookup"><span data-stu-id="329b2-214">Next, the factory adds a new `ApplicationDbContext` that uses an in-memory database for the tests.</span></span>

   <span data-ttu-id="329b2-215">Pour vous connecter à une base de données différente de celle de la base de données en mémoire, modifiez l' `UseInMemoryDatabase` appel pour connecter le contexte à une autre base de données.</span><span class="sxs-lookup"><span data-stu-id="329b2-215">To connect to a different database than the in-memory database, change the `UseInMemoryDatabase` call to connect the context to a different database.</span></span> <span data-ttu-id="329b2-216">Pour utiliser une base de données de test SQL Server :</span><span class="sxs-lookup"><span data-stu-id="329b2-216">To use a SQL Server test database:</span></span>

   * <span data-ttu-id="329b2-217">Référencez le package NuGet [Microsoft. EntityFrameworkCore. SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/) dans le fichier projet.</span><span class="sxs-lookup"><span data-stu-id="329b2-217">Reference the [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/) NuGet package in the project file.</span></span>
   * <span data-ttu-id="329b2-218">Appelez `UseSqlServer` avec une chaîne de connexion à la base de données.</span><span class="sxs-lookup"><span data-stu-id="329b2-218">Call `UseSqlServer` with a connection string to the database.</span></span>

   ```csharp
   services.AddDbContext<ApplicationDbContext>((options, context) => 
   {
       context.UseSqlServer(
           Configuration.GetConnectionString("TestingDbConnectionString"));
   });
   ```

2. <span data-ttu-id="329b2-219">Utilisez le personnalisé `CustomWebApplicationFactory` dans les classes de test.</span><span class="sxs-lookup"><span data-stu-id="329b2-219">Use the custom `CustomWebApplicationFactory` in test classes.</span></span> <span data-ttu-id="329b2-220">L’exemple suivant utilise la fabrique dans la `IndexPageTests` classe :</span><span class="sxs-lookup"><span data-stu-id="329b2-220">The following example uses the factory in the `IndexPageTests` class:</span></span>

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   <span data-ttu-id="329b2-221">Le client de l’exemple d’application est configuré pour empêcher la `HttpClient` suite de redirections.</span><span class="sxs-lookup"><span data-stu-id="329b2-221">The sample app's client is configured to prevent the `HttpClient` from following redirects.</span></span> <span data-ttu-id="329b2-222">Comme expliqué plus loin dans la section [d’authentification des simulacres](#mock-authentication) , cela permet aux tests de vérifier le résultat de la première réponse de l’application.</span><span class="sxs-lookup"><span data-stu-id="329b2-222">As explained later in the [Mock authentication](#mock-authentication) section, this permits tests to check the result of the app's first response.</span></span> <span data-ttu-id="329b2-223">La première réponse est une redirection dans un grand nombre de ces tests avec un `Location` en-tête.</span><span class="sxs-lookup"><span data-stu-id="329b2-223">The first response is a redirect in many of these tests with a `Location` header.</span></span>

3. <span data-ttu-id="329b2-224">Un test standard utilise les `HttpClient` méthodes d’assistance et pour traiter la demande et la réponse :</span><span class="sxs-lookup"><span data-stu-id="329b2-224">A typical test uses the `HttpClient` and helper methods to process the request and the response:</span></span>

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

<span data-ttu-id="329b2-225">Toute demande de publication à l’St doit être conforme à la vérification anti-contrefaçon effectuée automatiquement par le [système anti-contrefaçon de protection des données](xref:security/data-protection/introduction)de l’application.</span><span class="sxs-lookup"><span data-stu-id="329b2-225">Any POST request to the SUT must satisfy the antiforgery check that's automatically made by the app's [data protection antiforgery system](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="329b2-226">Pour organiser la requête de publication d’un test, l’application de test doit :</span><span class="sxs-lookup"><span data-stu-id="329b2-226">In order to arrange for a test's POST request, the test app must:</span></span>

1. <span data-ttu-id="329b2-227">Effectuez une demande pour la page.</span><span class="sxs-lookup"><span data-stu-id="329b2-227">Make a request for the page.</span></span>
1. <span data-ttu-id="329b2-228">Analysez le cookie anti-contrefaçon et le jeton de validation de demande à partir de la réponse.</span><span class="sxs-lookup"><span data-stu-id="329b2-228">Parse the antiforgery cookie and request validation token from the response.</span></span>
1. <span data-ttu-id="329b2-229">Effectuez la demande de publication avec le cookie anti-contrefaçon et le jeton de validation de demande en place.</span><span class="sxs-lookup"><span data-stu-id="329b2-229">Make the POST request with the antiforgery cookie and request validation token in place.</span></span>

<span data-ttu-id="329b2-230">Les `SendAsync` méthodes d’extension d’assistance (*helpers/HttpClientExtensions. cs*) et la `GetDocumentAsync` méthode d’assistance (*helpers/HtmlHelper. cs*) dans l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) utilisent l’analyseur [AngleSharp](https://anglesharp.github.io/) pour gérer la vérification anti-falsification avec les méthodes suivantes :</span><span class="sxs-lookup"><span data-stu-id="329b2-230">The `SendAsync` helper extension methods (*Helpers/HttpClientExtensions.cs*) and the `GetDocumentAsync` helper method (*Helpers/HtmlHelpers.cs*) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) use the [AngleSharp](https://anglesharp.github.io/) parser to handle the antiforgery check with the following methods:</span></span>

* <span data-ttu-id="329b2-231">`GetDocumentAsync`&ndash;Reçoit le [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) et retourne un `IHtmlDocument` .</span><span class="sxs-lookup"><span data-stu-id="329b2-231">`GetDocumentAsync` &ndash; Receives the [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) and returns an `IHtmlDocument`.</span></span> <span data-ttu-id="329b2-232">`GetDocumentAsync`utilise une fabrique qui prépare une *réponse virtuelle* en fonction de l’original `HttpResponseMessage` .</span><span class="sxs-lookup"><span data-stu-id="329b2-232">`GetDocumentAsync` uses a factory that prepares a *virtual response* based on the original `HttpResponseMessage`.</span></span> <span data-ttu-id="329b2-233">Pour plus d’informations, consultez la [documentation AngleSharp](https://github.com/AngleSharp/AngleSharp#documentation).</span><span class="sxs-lookup"><span data-stu-id="329b2-233">For more information, see the [AngleSharp documentation](https://github.com/AngleSharp/AngleSharp#documentation).</span></span>
* <span data-ttu-id="329b2-234">`SendAsync`les méthodes d’extension pour `HttpClient` compose un [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) et appellent [SendAsync (HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) pour envoyer des demandes au St.</span><span class="sxs-lookup"><span data-stu-id="329b2-234">`SendAsync` extension methods for the `HttpClient` compose an [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) and call [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) to submit requests to the SUT.</span></span> <span data-ttu-id="329b2-235">Les surcharges pour `SendAsync` acceptent le formulaire HTML ( `IHtmlFormElement` ) et les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="329b2-235">Overloads for `SendAsync` accept the HTML form (`IHtmlFormElement`) and the following:</span></span>
  * <span data-ttu-id="329b2-236">Bouton envoyer du formulaire ( `IHtmlElement` )</span><span class="sxs-lookup"><span data-stu-id="329b2-236">Submit button of the form (`IHtmlElement`)</span></span>
  * <span data-ttu-id="329b2-237">Collection de valeurs de formulaire ( `IEnumerable<KeyValuePair<string, string>>` )</span><span class="sxs-lookup"><span data-stu-id="329b2-237">Form values collection (`IEnumerable<KeyValuePair<string, string>>`)</span></span>
  * <span data-ttu-id="329b2-238">Bouton envoyer ( `IHtmlElement` ) et valeurs de formulaire ( `IEnumerable<KeyValuePair<string, string>>` )</span><span class="sxs-lookup"><span data-stu-id="329b2-238">Submit button (`IHtmlElement`) and form values (`IEnumerable<KeyValuePair<string, string>>`)</span></span>

> [!NOTE]
> <span data-ttu-id="329b2-239">[AngleSharp](https://anglesharp.github.io/) est une bibliothèque d’analyse tierce utilisée à des fins de démonstration dans cette rubrique et l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="329b2-239">[AngleSharp](https://anglesharp.github.io/) is a third-party parsing library used for demonstration purposes in this topic and the sample app.</span></span> <span data-ttu-id="329b2-240">AngleSharp n’est pas pris en charge ou requis pour le test d’intégration des applications ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="329b2-240">AngleSharp isn't supported or required for integration testing of ASP.NET Core apps.</span></span> <span data-ttu-id="329b2-241">D’autres analyseurs peuvent être utilisés, tels que le [HAP (html agilité Pack)](https://html-agility-pack.net/).</span><span class="sxs-lookup"><span data-stu-id="329b2-241">Other parsers can be used, such as the [Html Agility Pack (HAP)](https://html-agility-pack.net/).</span></span> <span data-ttu-id="329b2-242">Une autre approche consiste à écrire du code pour gérer directement le jeton de vérification de demande et le cookie anti-contrefaçon du système anti-contrefaçon.</span><span class="sxs-lookup"><span data-stu-id="329b2-242">Another approach is to write code to handle the antiforgery system's request verification token and antiforgery cookie directly.</span></span>

## <a name="customize-the-client-with-withwebhostbuilder"></a><span data-ttu-id="329b2-243">Personnaliser le client avec WithWebHostBuilder</span><span class="sxs-lookup"><span data-stu-id="329b2-243">Customize the client with WithWebHostBuilder</span></span>

<span data-ttu-id="329b2-244">Quand une configuration supplémentaire est requise dans une méthode de test, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) crée un nouveau `WebApplicationFactory` avec un [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) qui est ensuite personnalisé par la configuration.</span><span class="sxs-lookup"><span data-stu-id="329b2-244">When additional configuration is required within a test method, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) creates a new `WebApplicationFactory` with an [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) that is further customized by configuration.</span></span>

<span data-ttu-id="329b2-245">La `Post_DeleteMessageHandler_ReturnsRedirectToRoot` méthode de test de l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) montre l’utilisation de `WithWebHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="329b2-245">The `Post_DeleteMessageHandler_ReturnsRedirectToRoot` test method of the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) demonstrates the use of `WithWebHostBuilder`.</span></span> <span data-ttu-id="329b2-246">Ce test effectue une suppression d’enregistrement dans la base de données en déclenchant une soumission de formulaire dans St.</span><span class="sxs-lookup"><span data-stu-id="329b2-246">This test performs a record delete in the database by triggering a form submission in the SUT.</span></span>

<span data-ttu-id="329b2-247">Étant donné qu’un autre test de la `IndexPageTests` classe effectue une opération qui supprime tous les enregistrements de la base de données et peut s’exécuter avant la `Post_DeleteMessageHandler_ReturnsRedirectToRoot` méthode, la base de données est réamorcée dans cette méthode de test pour s’assurer qu’un enregistrement est présent pour que le St puisse être supprimé.</span><span class="sxs-lookup"><span data-stu-id="329b2-247">Because another test in the `IndexPageTests` class performs an operation that deletes all of the records in the database and may run before the `Post_DeleteMessageHandler_ReturnsRedirectToRoot` method, the database is reseeded in this test method to ensure that a record is present for the SUT to delete.</span></span> <span data-ttu-id="329b2-248">La sélection du premier bouton supprimer du `messages` formulaire dans le St est simulée dans la requête adressée au St :</span><span class="sxs-lookup"><span data-stu-id="329b2-248">Selecting the first delete button of the `messages` form in the SUT is simulated in the request to the SUT:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a><span data-ttu-id="329b2-249">Options du client</span><span class="sxs-lookup"><span data-stu-id="329b2-249">Client options</span></span>

<span data-ttu-id="329b2-250">Le tableau suivant indique les [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) par défaut disponibles lors de la création d' `HttpClient` instances.</span><span class="sxs-lookup"><span data-stu-id="329b2-250">The following table shows the default [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) available when creating `HttpClient` instances.</span></span>

| <span data-ttu-id="329b2-251">Option</span><span class="sxs-lookup"><span data-stu-id="329b2-251">Option</span></span> | <span data-ttu-id="329b2-252">Description</span><span class="sxs-lookup"><span data-stu-id="329b2-252">Description</span></span> | <span data-ttu-id="329b2-253">Default</span><span class="sxs-lookup"><span data-stu-id="329b2-253">Default</span></span> |
| ---
<span data-ttu-id="329b2-254">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="329b2-254">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="329b2-255">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="329b2-255">'Blazor'</span></span>
- <span data-ttu-id="329b2-256">'Identity'</span><span class="sxs-lookup"><span data-stu-id="329b2-256">'Identity'</span></span>
- <span data-ttu-id="329b2-257">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="329b2-257">'Let's Encrypt'</span></span>
- <span data-ttu-id="329b2-258">'Razor'</span><span class="sxs-lookup"><span data-stu-id="329b2-258">'Razor'</span></span>
- <span data-ttu-id="329b2-259">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="329b2-259">'SignalR' uid:</span></span> 

<span data-ttu-id="329b2-260">--- | titre de--- : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="329b2-260">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="329b2-261">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="329b2-261">'Blazor'</span></span>
- <span data-ttu-id="329b2-262">'Identity'</span><span class="sxs-lookup"><span data-stu-id="329b2-262">'Identity'</span></span>
- <span data-ttu-id="329b2-263">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="329b2-263">'Let's Encrypt'</span></span>
- <span data-ttu-id="329b2-264">'Razor'</span><span class="sxs-lookup"><span data-stu-id="329b2-264">'Razor'</span></span>
- <span data-ttu-id="329b2-265">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="329b2-265">'SignalR' uid:</span></span> 

-
<span data-ttu-id="329b2-266">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="329b2-266">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="329b2-267">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="329b2-267">'Blazor'</span></span>
- <span data-ttu-id="329b2-268">'Identity'</span><span class="sxs-lookup"><span data-stu-id="329b2-268">'Identity'</span></span>
- <span data-ttu-id="329b2-269">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="329b2-269">'Let's Encrypt'</span></span>
- <span data-ttu-id="329b2-270">'Razor'</span><span class="sxs-lookup"><span data-stu-id="329b2-270">'Razor'</span></span>
- <span data-ttu-id="329b2-271">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="329b2-271">'SignalR' uid:</span></span> 

-
<span data-ttu-id="329b2-272">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="329b2-272">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="329b2-273">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="329b2-273">'Blazor'</span></span>
- <span data-ttu-id="329b2-274">'Identity'</span><span class="sxs-lookup"><span data-stu-id="329b2-274">'Identity'</span></span>
- <span data-ttu-id="329b2-275">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="329b2-275">'Let's Encrypt'</span></span>
- <span data-ttu-id="329b2-276">'Razor'</span><span class="sxs-lookup"><span data-stu-id="329b2-276">'Razor'</span></span>
- <span data-ttu-id="329b2-277">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="329b2-277">'SignalR' uid:</span></span> 

<span data-ttu-id="329b2-278">------ | titre de--- : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="329b2-278">------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="329b2-279">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="329b2-279">'Blazor'</span></span>
- <span data-ttu-id="329b2-280">'Identity'</span><span class="sxs-lookup"><span data-stu-id="329b2-280">'Identity'</span></span>
- <span data-ttu-id="329b2-281">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="329b2-281">'Let's Encrypt'</span></span>
- <span data-ttu-id="329b2-282">'Razor'</span><span class="sxs-lookup"><span data-stu-id="329b2-282">'Razor'</span></span>
- <span data-ttu-id="329b2-283">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="329b2-283">'SignalR' uid:</span></span> 

<span data-ttu-id="329b2-284">---- | | [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | Obtient ou définit une valeur indiquant si les `HttpClient` instances de doivent suivre automatiquement les réponses de redirection.</span><span class="sxs-lookup"><span data-stu-id="329b2-284">---- | | [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | Gets or sets whether or not `HttpClient` instances should automatically follow redirect responses.</span></span><span data-ttu-id="329b2-285"> | `true`| | [BaseAddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | Obtient ou définit l’adresse de base des `HttpClient` instances.</span><span class="sxs-lookup"><span data-stu-id="329b2-285"> | `true` | | [BaseAddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | Gets or sets the base address of `HttpClient` instances.</span></span><span data-ttu-id="329b2-286"> | `http://localhost`| | [HandleCookies](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | Obtient ou définit une valeur indiquant si `HttpClient` les instances doivent gérer les cookies.</span><span class="sxs-lookup"><span data-stu-id="329b2-286"> | `http://localhost` | | [HandleCookies](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | Gets or sets whether `HttpClient` instances should handle cookies.</span></span><span data-ttu-id="329b2-287"> | `true`| | [MaxAutomaticRedirections](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | Obtient ou définit le nombre maximal de réponses de redirection que les `HttpClient` instances doivent suivre.</span><span class="sxs-lookup"><span data-stu-id="329b2-287"> | `true` | | [MaxAutomaticRedirections](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | Gets or sets the maximum number of redirect responses that `HttpClient` instances should follow.</span></span> <span data-ttu-id="329b2-288">| 7 |</span><span class="sxs-lookup"><span data-stu-id="329b2-288">| 7 |</span></span>

<span data-ttu-id="329b2-289">Créez la `WebApplicationFactoryClientOptions` classe et transmettez-la à la méthode [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) (les valeurs par défaut sont affichées dans l’exemple de code) :</span><span class="sxs-lookup"><span data-stu-id="329b2-289">Create the `WebApplicationFactoryClientOptions` class and pass it to the [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) method (default values are shown in the code example):</span></span>

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a><span data-ttu-id="329b2-290">Injecter des services fictifs</span><span class="sxs-lookup"><span data-stu-id="329b2-290">Inject mock services</span></span>

<span data-ttu-id="329b2-291">Les services peuvent être substitués dans un test à l’aide d’un appel à [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) sur le générateur de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="329b2-291">Services can be overridden in a test with a call to [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) on the host builder.</span></span> <span data-ttu-id="329b2-292">**Pour injecter des services fictifs, St doit avoir une `Startup` classe avec une `Startup.ConfigureServices` méthode.**</span><span class="sxs-lookup"><span data-stu-id="329b2-292">**To inject mock services, the SUT must have a `Startup` class with a `Startup.ConfigureServices` method.**</span></span>

<span data-ttu-id="329b2-293">L’exemple de St comprend un service étendu qui retourne un guillemet.</span><span class="sxs-lookup"><span data-stu-id="329b2-293">The sample SUT includes a scoped service that returns a quote.</span></span> <span data-ttu-id="329b2-294">Le guillemet est incorporé dans un champ masqué sur la page d’index lorsque la page d’index est demandée.</span><span class="sxs-lookup"><span data-stu-id="329b2-294">The quote is embedded in a hidden field on the Index page when the Index page is requested.</span></span>

<span data-ttu-id="329b2-295">*Services/IQuoteService. cs*:</span><span class="sxs-lookup"><span data-stu-id="329b2-295">*Services/IQuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

<span data-ttu-id="329b2-296">*Services/QuoteService. cs*:</span><span class="sxs-lookup"><span data-stu-id="329b2-296">*Services/QuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

<span data-ttu-id="329b2-297">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="329b2-297">*Startup.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

<span data-ttu-id="329b2-298">*Pages/Index.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="329b2-298">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

<span data-ttu-id="329b2-299">*Pages/index. cs*:</span><span class="sxs-lookup"><span data-stu-id="329b2-299">*Pages/Index.cs*:</span></span>

[!code-cshtml[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

<span data-ttu-id="329b2-300">Le balisage suivant est généré lors de l’exécution de l’application St :</span><span class="sxs-lookup"><span data-stu-id="329b2-300">The following markup is generated when the SUT app is run:</span></span>

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

<span data-ttu-id="329b2-301">Pour tester l’injection de service et de guillemets dans un test d’intégration, un service fictif est injecté dans le St par le test.</span><span class="sxs-lookup"><span data-stu-id="329b2-301">To test the service and quote injection in an integration test, a mock service is injected into the SUT by the test.</span></span> <span data-ttu-id="329b2-302">Le service factice remplace l’application par `QuoteService` un service fourni par l’application de test, appelé `TestQuoteService` :</span><span class="sxs-lookup"><span data-stu-id="329b2-302">The mock service replaces the app's `QuoteService` with a service provided by the test app, called `TestQuoteService`:</span></span>

<span data-ttu-id="329b2-303">*IntegrationTests.IndexPageTests.cs*:</span><span class="sxs-lookup"><span data-stu-id="329b2-303">*IntegrationTests.IndexPageTests.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

<span data-ttu-id="329b2-304">`ConfigureTestServices`est appelé, et le service étendu est inscrit :</span><span class="sxs-lookup"><span data-stu-id="329b2-304">`ConfigureTestServices` is called, and the scoped service is registered:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

<span data-ttu-id="329b2-305">Le balisage produit pendant l’exécution du test reflète le texte du devis fourni par `TestQuoteService` , donc l’assertion réussit :</span><span class="sxs-lookup"><span data-stu-id="329b2-305">The markup produced during the test's execution reflects the quote text supplied by `TestQuoteService`, thus the assertion passes:</span></span>

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="mock-authentication"></a><span data-ttu-id="329b2-306">Simuler l’authentification</span><span class="sxs-lookup"><span data-stu-id="329b2-306">Mock authentication</span></span>

<span data-ttu-id="329b2-307">Les tests de la `AuthTests` classe vérifient qu’un point de terminaison sécurisé :</span><span class="sxs-lookup"><span data-stu-id="329b2-307">Tests in the `AuthTests` class check that a secure endpoint:</span></span>

* <span data-ttu-id="329b2-308">Redirige un utilisateur non authentifié vers la page de connexion de l’application.</span><span class="sxs-lookup"><span data-stu-id="329b2-308">Redirects an unauthenticated user to the app's Login page.</span></span>
* <span data-ttu-id="329b2-309">Retourne le contenu d’un utilisateur authentifié.</span><span class="sxs-lookup"><span data-stu-id="329b2-309">Returns content for an authenticated user.</span></span>

<span data-ttu-id="329b2-310">Dans St, la `/SecurePage` page utilise une convention [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) pour appliquer un [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) à la page.</span><span class="sxs-lookup"><span data-stu-id="329b2-310">In the SUT, the `/SecurePage` page uses an [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) convention to apply an [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) to the page.</span></span> <span data-ttu-id="329b2-311">Pour plus d’informations, consultez [ Razor conventions d’autorisation des pages](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).</span><span class="sxs-lookup"><span data-stu-id="329b2-311">For more information, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

<span data-ttu-id="329b2-312">Dans le `Get_SecurePageRedirectsAnUnauthenticatedUser` test, un [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) est défini pour interdire les redirections en affectant à [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) la valeur `false` :</span><span class="sxs-lookup"><span data-stu-id="329b2-312">In the `Get_SecurePageRedirectsAnUnauthenticatedUser` test, a [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) is set to disallow redirects by setting [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) to `false`:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet2)]

<span data-ttu-id="329b2-313">En interautorisant le client à suivre la redirection, vous pouvez effectuer les vérifications suivantes :</span><span class="sxs-lookup"><span data-stu-id="329b2-313">By disallowing the client to follow the redirect, the following checks can be made:</span></span>

* <span data-ttu-id="329b2-314">Le code d’état retourné par le St peut être vérifié par rapport au résultat [HttpStatusCode. Redirect](/dotnet/api/system.net.httpstatuscode) attendu, pas le code d’état final après la redirection vers la page de connexion, qui serait [HttpStatusCode. OK](/dotnet/api/system.net.httpstatuscode).</span><span class="sxs-lookup"><span data-stu-id="329b2-314">The status code returned by the SUT can be checked against the expected [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode) result, not the final status code after the redirect to the Login page, which would be [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode).</span></span>
* <span data-ttu-id="329b2-315">La `Location` valeur d’en-tête dans les en-têtes de réponse est vérifiée pour confirmer qu’elle commence par `http://localhost/Identity/Account/Login` , et non par la réponse de page de connexion finale, où l' `Location` en-tête n’est pas présent.</span><span class="sxs-lookup"><span data-stu-id="329b2-315">The `Location` header value in the response headers is checked to confirm that it starts with `http://localhost/Identity/Account/Login`, not the final Login page response, where the `Location` header wouldn't be present.</span></span>

<span data-ttu-id="329b2-316">L’application de test peut simuler un <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> dans [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) afin de tester les aspects de l’authentification et de l’autorisation.</span><span class="sxs-lookup"><span data-stu-id="329b2-316">The test app can mock an <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> in [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) in order to test aspects of authentication and authorization.</span></span> <span data-ttu-id="329b2-317">Un scénario minimal renvoie un [AuthenticateResult. Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*):</span><span class="sxs-lookup"><span data-stu-id="329b2-317">A minimal scenario returns an [AuthenticateResult.Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*):</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet4&highlight=11-18)]

<span data-ttu-id="329b2-318">La `TestAuthHandler` méthode est appelée pour authentifier un utilisateur lorsque le schéma d’authentification est défini sur `Test` où `AddAuthentication` est inscrit pour `ConfigureTestServices` :</span><span class="sxs-lookup"><span data-stu-id="329b2-318">The `TestAuthHandler` is called to authenticate a user when the authentication scheme is set to `Test` where `AddAuthentication` is registered for `ConfigureTestServices`:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet3&highlight=7-12)]

<span data-ttu-id="329b2-319">Pour plus d’informations sur `WebApplicationFactoryClientOptions` , consultez la section [Options du client](#client-options) .</span><span class="sxs-lookup"><span data-stu-id="329b2-319">For more information on `WebApplicationFactoryClientOptions`, see the [Client options](#client-options) section.</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="329b2-320">Définir l’environnement</span><span class="sxs-lookup"><span data-stu-id="329b2-320">Set the environment</span></span>

<span data-ttu-id="329b2-321">Par défaut, l’environnement de l’hôte et de l’application de St est configuré pour utiliser l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="329b2-321">By default, the SUT's host and app environment is configured to use the Development environment.</span></span> <span data-ttu-id="329b2-322">Pour remplacer l’environnement de St lors de l’utilisation de `IHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="329b2-322">To override the SUT's environment when using `IHostBuilder`:</span></span>

* <span data-ttu-id="329b2-323">Définissez la `ASPNETCORE_ENVIRONMENT` variable d’environnement (par exemple,, `Staging` `Production` ou une autre valeur personnalisée, telle que `Testing` ).</span><span class="sxs-lookup"><span data-stu-id="329b2-323">Set the `ASPNETCORE_ENVIRONMENT` environment variable (for example, `Staging`, `Production`, or other custom value, such as `Testing`).</span></span>
* <span data-ttu-id="329b2-324">Remplacez `CreateHostBuilder` dans l’application de test pour lire les variables d’environnement précédées de `ASPNETCORE` .</span><span class="sxs-lookup"><span data-stu-id="329b2-324">Override `CreateHostBuilder` in the test app to read environment variables prefixed with `ASPNETCORE`.</span></span>

```csharp
protected override IHostBuilder CreateHostBuilder() =>
    base.CreateHostBuilder()
        .ConfigureHostConfiguration(
            config => config.AddEnvironmentVariables("ASPNETCORE"));
```

<span data-ttu-id="329b2-325">Si le St utilise l’hôte Web ( `IWebHostBuilder` ), remplacez `CreateWebHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="329b2-325">If the SUT uses the Web Host (`IWebHostBuilder`), override `CreateWebHostBuilder`:</span></span>

```csharp
protected override IWebHostBuilder CreateWebHostBuilder() =>
    base.CreateWebHostBuilder().UseEnvironment("Testing");
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a><span data-ttu-id="329b2-326">Comment l’infrastructure de test déduit le chemin racine du contenu de l’application</span><span class="sxs-lookup"><span data-stu-id="329b2-326">How the test infrastructure infers the app content root path</span></span>

<span data-ttu-id="329b2-327">Le `WebApplicationFactory` constructeur déduit le chemin d’accès [racine du contenu](xref:fundamentals/index#content-root) de l’application en recherchant un [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) sur l’assembly contenant les tests d’intégration avec une clé égale à l' `TEntryPoint` assembly `System.Reflection.Assembly.FullName` .</span><span class="sxs-lookup"><span data-stu-id="329b2-327">The `WebApplicationFactory` constructor infers the app [content root](xref:fundamentals/index#content-root) path by searching for a [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) on the assembly containing the integration tests with a key equal to the `TEntryPoint` assembly `System.Reflection.Assembly.FullName`.</span></span> <span data-ttu-id="329b2-328">Si un attribut avec la clé correcte est introuvable, revient `WebApplicationFactory` à la recherche d’un fichier solution (*. sln*) et ajoute le `TEntryPoint` nom de l’assembly au répertoire de la solution.</span><span class="sxs-lookup"><span data-stu-id="329b2-328">In case an attribute with the correct key isn't found, `WebApplicationFactory` falls back to searching for a solution file (*.sln*) and appends the `TEntryPoint` assembly name to the solution directory.</span></span> <span data-ttu-id="329b2-329">Le répertoire racine de l’application (chemin racine du contenu) est utilisé pour découvrir les affichages et les fichiers de contenu.</span><span class="sxs-lookup"><span data-stu-id="329b2-329">The app root directory (the content root path) is used to discover views and content files.</span></span>

## <a name="disable-shadow-copying"></a><span data-ttu-id="329b2-330">Désactiver les clichés instantanés</span><span class="sxs-lookup"><span data-stu-id="329b2-330">Disable shadow copying</span></span>

<span data-ttu-id="329b2-331">Les clichés instantanés provoquent l’exécution des tests dans un répertoire différent de celui du répertoire de sortie.</span><span class="sxs-lookup"><span data-stu-id="329b2-331">Shadow copying causes the tests to execute in a different directory than the output directory.</span></span> <span data-ttu-id="329b2-332">Pour que les tests fonctionnent correctement, les clichés instantanés doivent être désactivés.</span><span class="sxs-lookup"><span data-stu-id="329b2-332">For tests to work properly, shadow copying must be disabled.</span></span> <span data-ttu-id="329b2-333">L' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) utilise xUnit et désactive les clichés instantanés pour xUnit en incluant un fichier *xUnit. Runner. JSON* avec le paramètre de configuration correct.</span><span class="sxs-lookup"><span data-stu-id="329b2-333">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) uses xUnit and disables shadow copying for xUnit by including an *xunit.runner.json* file with the correct configuration setting.</span></span> <span data-ttu-id="329b2-334">Pour plus d’informations, consultez [configuration de xUnit avec JSON](https://xunit.github.io/docs/configuring-with-json.html).</span><span class="sxs-lookup"><span data-stu-id="329b2-334">For more information, see [Configuring xUnit with JSON](https://xunit.github.io/docs/configuring-with-json.html).</span></span>

<span data-ttu-id="329b2-335">Ajoutez le fichier *xUnit. Runner. JSON* à la racine du projet de test avec le contenu suivant :</span><span class="sxs-lookup"><span data-stu-id="329b2-335">Add the *xunit.runner.json* file to root of the test project with the following content:</span></span>

```json
{
  "shadowCopy": false
}
```

## <a name="disposal-of-objects"></a><span data-ttu-id="329b2-336">Suppression d’objets</span><span class="sxs-lookup"><span data-stu-id="329b2-336">Disposal of objects</span></span>

<span data-ttu-id="329b2-337">Après l’exécution des tests de l' `IClassFixture` implémentation, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) et [httpclient](/dotnet/api/system.net.http.httpclient) sont supprimés quand xUnit supprime le [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1).</span><span class="sxs-lookup"><span data-stu-id="329b2-337">After the tests of the `IClassFixture` implementation are executed, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) and [HttpClient](/dotnet/api/system.net.http.httpclient) are disposed when xUnit disposes of the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1).</span></span> <span data-ttu-id="329b2-338">Si les objets instanciés par le développeur nécessitent une suppression, supprimez-les dans l' `IClassFixture` implémentation.</span><span class="sxs-lookup"><span data-stu-id="329b2-338">If objects instantiated by the developer require disposal, dispose of them in the `IClassFixture` implementation.</span></span> <span data-ttu-id="329b2-339">Pour plus d’informations, consultez [implémentation d’une méthode dispose](/dotnet/standard/garbage-collection/implementing-dispose).</span><span class="sxs-lookup"><span data-stu-id="329b2-339">For more information, see [Implementing a Dispose method](/dotnet/standard/garbage-collection/implementing-dispose).</span></span>

## <a name="integration-tests-sample"></a><span data-ttu-id="329b2-340">Exemple de tests d’intégration</span><span class="sxs-lookup"><span data-stu-id="329b2-340">Integration tests sample</span></span>

<span data-ttu-id="329b2-341">L' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) se compose de deux applications :</span><span class="sxs-lookup"><span data-stu-id="329b2-341">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is composed of two apps:</span></span>

| <span data-ttu-id="329b2-342">Application</span><span class="sxs-lookup"><span data-stu-id="329b2-342">App</span></span> | <span data-ttu-id="329b2-343">Répertoire du projet</span><span class="sxs-lookup"><span data-stu-id="329b2-343">Project directory</span></span> | <span data-ttu-id="329b2-344">Description</span><span class="sxs-lookup"><span data-stu-id="329b2-344">Description</span></span> |
| --- | ---
<span data-ttu-id="329b2-345">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="329b2-345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="329b2-346">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="329b2-346">'Blazor'</span></span>
- <span data-ttu-id="329b2-347">'Identity'</span><span class="sxs-lookup"><span data-stu-id="329b2-347">'Identity'</span></span>
- <span data-ttu-id="329b2-348">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="329b2-348">'Let's Encrypt'</span></span>
- <span data-ttu-id="329b2-349">'Razor'</span><span class="sxs-lookup"><span data-stu-id="329b2-349">'Razor'</span></span>
- <span data-ttu-id="329b2-350">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="329b2-350">'SignalR' uid:</span></span> 

-
<span data-ttu-id="329b2-351">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="329b2-351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="329b2-352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="329b2-352">'Blazor'</span></span>
- <span data-ttu-id="329b2-353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="329b2-353">'Identity'</span></span>
- <span data-ttu-id="329b2-354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="329b2-354">'Let's Encrypt'</span></span>
- <span data-ttu-id="329b2-355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="329b2-355">'Razor'</span></span>
- <span data-ttu-id="329b2-356">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="329b2-356">'SignalR' uid:</span></span> 

-
<span data-ttu-id="329b2-357">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="329b2-357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="329b2-358">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="329b2-358">'Blazor'</span></span>
- <span data-ttu-id="329b2-359">'Identity'</span><span class="sxs-lookup"><span data-stu-id="329b2-359">'Identity'</span></span>
- <span data-ttu-id="329b2-360">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="329b2-360">'Let's Encrypt'</span></span>
- <span data-ttu-id="329b2-361">'Razor'</span><span class="sxs-lookup"><span data-stu-id="329b2-361">'Razor'</span></span>
- <span data-ttu-id="329b2-362">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="329b2-362">'SignalR' uid:</span></span> 

-
<span data-ttu-id="329b2-363">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="329b2-363">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="329b2-364">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="329b2-364">'Blazor'</span></span>
- <span data-ttu-id="329b2-365">'Identity'</span><span class="sxs-lookup"><span data-stu-id="329b2-365">'Identity'</span></span>
- <span data-ttu-id="329b2-366">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="329b2-366">'Let's Encrypt'</span></span>
- <span data-ttu-id="329b2-367">'Razor'</span><span class="sxs-lookup"><span data-stu-id="329b2-367">'Razor'</span></span>
- <span data-ttu-id="329b2-368">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="329b2-368">'SignalR' uid:</span></span> 

-
<span data-ttu-id="329b2-369">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="329b2-369">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="329b2-370">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="329b2-370">'Blazor'</span></span>
- <span data-ttu-id="329b2-371">'Identity'</span><span class="sxs-lookup"><span data-stu-id="329b2-371">'Identity'</span></span>
- <span data-ttu-id="329b2-372">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="329b2-372">'Let's Encrypt'</span></span>
- <span data-ttu-id="329b2-373">'Razor'</span><span class="sxs-lookup"><span data-stu-id="329b2-373">'Razor'</span></span>
- <span data-ttu-id="329b2-374">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="329b2-374">'SignalR' uid:</span></span> 

-
<span data-ttu-id="329b2-375">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="329b2-375">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="329b2-376">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="329b2-376">'Blazor'</span></span>
- <span data-ttu-id="329b2-377">'Identity'</span><span class="sxs-lookup"><span data-stu-id="329b2-377">'Identity'</span></span>
- <span data-ttu-id="329b2-378">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="329b2-378">'Let's Encrypt'</span></span>
- <span data-ttu-id="329b2-379">'Razor'</span><span class="sxs-lookup"><span data-stu-id="329b2-379">'Razor'</span></span>
- <span data-ttu-id="329b2-380">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="329b2-380">'SignalR' uid:</span></span> 

<span data-ttu-id="329b2-381">--------- | titre de--- : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="329b2-381">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="329b2-382">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="329b2-382">'Blazor'</span></span>
- <span data-ttu-id="329b2-383">'Identity'</span><span class="sxs-lookup"><span data-stu-id="329b2-383">'Identity'</span></span>
- <span data-ttu-id="329b2-384">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="329b2-384">'Let's Encrypt'</span></span>
- <span data-ttu-id="329b2-385">'Razor'</span><span class="sxs-lookup"><span data-stu-id="329b2-385">'Razor'</span></span>
- <span data-ttu-id="329b2-386">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="329b2-386">'SignalR' uid:</span></span> 

-
<span data-ttu-id="329b2-387">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="329b2-387">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="329b2-388">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="329b2-388">'Blazor'</span></span>
- <span data-ttu-id="329b2-389">'Identity'</span><span class="sxs-lookup"><span data-stu-id="329b2-389">'Identity'</span></span>
- <span data-ttu-id="329b2-390">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="329b2-390">'Let's Encrypt'</span></span>
- <span data-ttu-id="329b2-391">'Razor'</span><span class="sxs-lookup"><span data-stu-id="329b2-391">'Razor'</span></span>
- <span data-ttu-id="329b2-392">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="329b2-392">'SignalR' uid:</span></span> 

-
<span data-ttu-id="329b2-393">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="329b2-393">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="329b2-394">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="329b2-394">'Blazor'</span></span>
- <span data-ttu-id="329b2-395">'Identity'</span><span class="sxs-lookup"><span data-stu-id="329b2-395">'Identity'</span></span>
- <span data-ttu-id="329b2-396">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="329b2-396">'Let's Encrypt'</span></span>
- <span data-ttu-id="329b2-397">'Razor'</span><span class="sxs-lookup"><span data-stu-id="329b2-397">'Razor'</span></span>
- <span data-ttu-id="329b2-398">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="329b2-398">'SignalR' uid:</span></span> 

<span data-ttu-id="329b2-399">------ | | Application message (St) | *src/RazorPagesProject* | Permet à l’utilisateur d’ajouter, de supprimer, de supprimer et d’analyser des messages.</span><span class="sxs-lookup"><span data-stu-id="329b2-399">------ | | Message app (the SUT) | *src/RazorPagesProject* | Allows a user to add, delete one, delete all, and analyze messages.</span></span> <span data-ttu-id="329b2-400">| | Application de test | *tests/RazorPagesProject. tests* | Utilisé pour tester l’intégration du St.</span><span class="sxs-lookup"><span data-stu-id="329b2-400">| | Test app | *tests/RazorPagesProject.Tests* | Used to integration test the SUT.</span></span> |

<span data-ttu-id="329b2-401">Les tests peuvent être exécutés à l’aide des fonctionnalités de test intégrées d’un environnement de développement intégré (IDE), telles que [Visual Studio](https://visualstudio.microsoft.com).</span><span class="sxs-lookup"><span data-stu-id="329b2-401">The tests can be run using the built-in test features of an IDE, such as [Visual Studio](https://visualstudio.microsoft.com).</span></span> <span data-ttu-id="329b2-402">Si vous utilisez [Visual Studio code](https://code.visualstudio.com/) ou la ligne de commande, exécutez la commande suivante à l’invite de commandes dans le répertoire *tests/RazorPagesProject. tests* :</span><span class="sxs-lookup"><span data-stu-id="329b2-402">If using [Visual Studio Code](https://code.visualstudio.com/) or the command line, execute the following command at a command prompt in the *tests/RazorPagesProject.Tests* directory:</span></span>

```console
dotnet test
```

### <a name="message-app-sut-organization"></a><span data-ttu-id="329b2-403">Organisation message App (St)</span><span class="sxs-lookup"><span data-stu-id="329b2-403">Message app (SUT) organization</span></span>

<span data-ttu-id="329b2-404">Le St est un Razor système de messagerie des pages avec les caractéristiques suivantes :</span><span class="sxs-lookup"><span data-stu-id="329b2-404">The SUT is a Razor Pages message system with the following characteristics:</span></span>

* <span data-ttu-id="329b2-405">La page d’index de l’application (*pages/index. cshtml* et *pages/index. cshtml. cs*) fournit une interface utilisateur et des méthodes de modèle de page pour contrôler l’ajout, la suppression et l’analyse de messages (mots moyens par message).</span><span class="sxs-lookup"><span data-stu-id="329b2-405">The Index page of the app (*Pages/Index.cshtml* and *Pages/Index.cshtml.cs*) provides a UI and page model methods to control the addition, deletion, and analysis of messages (average words per message).</span></span>
* <span data-ttu-id="329b2-406">Un message est décrit par la `Message` classe (*Data/message. cs*) avec deux propriétés : `Id` (Key) et `Text` (message).</span><span class="sxs-lookup"><span data-stu-id="329b2-406">A message is described by the `Message` class (*Data/Message.cs*) with two properties: `Id` (key) and `Text` (message).</span></span> <span data-ttu-id="329b2-407">La `Text` propriété est obligatoire et limitée à 200 caractères.</span><span class="sxs-lookup"><span data-stu-id="329b2-407">The `Text` property is required and limited to 200 characters.</span></span>
* <span data-ttu-id="329b2-408">Les messages sont stockés à l’aide [de la base de données en mémoire de Entity Framework](/ef/core/providers/in-memory/)&#8224;.</span><span class="sxs-lookup"><span data-stu-id="329b2-408">Messages are stored using [Entity Framework's in-memory database](/ef/core/providers/in-memory/)&#8224;.</span></span>
* <span data-ttu-id="329b2-409">L’application contient une couche d’accès aux données (DAL) dans sa classe de contexte de base de données, `AppDbContext` (*Data/AppDbContext. cs*).</span><span class="sxs-lookup"><span data-stu-id="329b2-409">The app contains a data access layer (DAL) in its database context class, `AppDbContext` (*Data/AppDbContext.cs*).</span></span>
* <span data-ttu-id="329b2-410">Si la base de données est vide au démarrage de l’application, la Banque de messages est initialisée avec trois messages.</span><span class="sxs-lookup"><span data-stu-id="329b2-410">If the database is empty on app startup, the message store is initialized with three messages.</span></span>
* <span data-ttu-id="329b2-411">L’application comprend un `/SecurePage` qui n’est accessible qu’à un utilisateur authentifié.</span><span class="sxs-lookup"><span data-stu-id="329b2-411">The app includes a `/SecurePage` that can only be accessed by an authenticated user.</span></span>

<span data-ttu-id="329b2-412">&#8224;la rubrique EF [test with InMemory](/ef/core/miscellaneous/testing/in-memory), explique comment utiliser une base de données en mémoire pour les tests avec MSTest.</span><span class="sxs-lookup"><span data-stu-id="329b2-412">&#8224;The EF topic, [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), explains how to use an in-memory database for tests with MSTest.</span></span> <span data-ttu-id="329b2-413">Cette rubrique utilise l’infrastructure de test [xUnit](https://xunit.github.io/) .</span><span class="sxs-lookup"><span data-stu-id="329b2-413">This topic uses the [xUnit](https://xunit.github.io/) test framework.</span></span> <span data-ttu-id="329b2-414">Les concepts de test et les implémentations de test dans différents frameworks de test sont similaires, mais pas identiques.</span><span class="sxs-lookup"><span data-stu-id="329b2-414">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span>

<span data-ttu-id="329b2-415">Bien que l’application n’utilise pas le modèle de référentiel et ne soit pas un exemple efficace de [modèle d’unité de travail (UOW)](https://martinfowler.com/eaaCatalog/unitOfWork.html), les Razor pages prennent en charge ces modèles de développement.</span><span class="sxs-lookup"><span data-stu-id="329b2-415">Although the app doesn't use the repository pattern and isn't an effective example of the [Unit of Work (UoW) pattern](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages supports these patterns of development.</span></span> <span data-ttu-id="329b2-416">Pour plus d’informations, consultez [conception de la couche de persistance de l’infrastructure](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) et [logique du contrôleur de test](/aspnet/core/mvc/controllers/testing) (l’exemple implémente le modèle de référentiel).</span><span class="sxs-lookup"><span data-stu-id="329b2-416">For more information, see [Designing the infrastructure persistence layer](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) and [Test controller logic](/aspnet/core/mvc/controllers/testing) (the sample implements the repository pattern).</span></span>

### <a name="test-app-organization"></a><span data-ttu-id="329b2-417">Organisation des applications de test</span><span class="sxs-lookup"><span data-stu-id="329b2-417">Test app organization</span></span>

<span data-ttu-id="329b2-418">L’application de test est une application console dans le répertoire *tests/RazorPagesProject. tests* .</span><span class="sxs-lookup"><span data-stu-id="329b2-418">The test app is a console app inside the *tests/RazorPagesProject.Tests* directory.</span></span>

| <span data-ttu-id="329b2-419">Tester le répertoire de l’application</span><span class="sxs-lookup"><span data-stu-id="329b2-419">Test app directory</span></span> | <span data-ttu-id="329b2-420">Description</span><span class="sxs-lookup"><span data-stu-id="329b2-420">Description</span></span> |
| ---
<span data-ttu-id="329b2-421">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="329b2-421">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="329b2-422">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="329b2-422">'Blazor'</span></span>
- <span data-ttu-id="329b2-423">'Identity'</span><span class="sxs-lookup"><span data-stu-id="329b2-423">'Identity'</span></span>
- <span data-ttu-id="329b2-424">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="329b2-424">'Let's Encrypt'</span></span>
- <span data-ttu-id="329b2-425">'Razor'</span><span class="sxs-lookup"><span data-stu-id="329b2-425">'Razor'</span></span>
- <span data-ttu-id="329b2-426">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="329b2-426">'SignalR' uid:</span></span> 

-
<span data-ttu-id="329b2-427">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="329b2-427">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="329b2-428">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="329b2-428">'Blazor'</span></span>
- <span data-ttu-id="329b2-429">'Identity'</span><span class="sxs-lookup"><span data-stu-id="329b2-429">'Identity'</span></span>
- <span data-ttu-id="329b2-430">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="329b2-430">'Let's Encrypt'</span></span>
- <span data-ttu-id="329b2-431">'Razor'</span><span class="sxs-lookup"><span data-stu-id="329b2-431">'Razor'</span></span>
- <span data-ttu-id="329b2-432">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="329b2-432">'SignalR' uid:</span></span> 

-
<span data-ttu-id="329b2-433">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="329b2-433">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="329b2-434">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="329b2-434">'Blazor'</span></span>
- <span data-ttu-id="329b2-435">'Identity'</span><span class="sxs-lookup"><span data-stu-id="329b2-435">'Identity'</span></span>
- <span data-ttu-id="329b2-436">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="329b2-436">'Let's Encrypt'</span></span>
- <span data-ttu-id="329b2-437">'Razor'</span><span class="sxs-lookup"><span data-stu-id="329b2-437">'Razor'</span></span>
- <span data-ttu-id="329b2-438">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="329b2-438">'SignalR' uid:</span></span> 

-
<span data-ttu-id="329b2-439">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="329b2-439">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="329b2-440">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="329b2-440">'Blazor'</span></span>
- <span data-ttu-id="329b2-441">'Identity'</span><span class="sxs-lookup"><span data-stu-id="329b2-441">'Identity'</span></span>
- <span data-ttu-id="329b2-442">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="329b2-442">'Let's Encrypt'</span></span>
- <span data-ttu-id="329b2-443">'Razor'</span><span class="sxs-lookup"><span data-stu-id="329b2-443">'Razor'</span></span>
- <span data-ttu-id="329b2-444">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="329b2-444">'SignalR' uid:</span></span> 

-
<span data-ttu-id="329b2-445">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="329b2-445">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="329b2-446">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="329b2-446">'Blazor'</span></span>
- <span data-ttu-id="329b2-447">'Identity'</span><span class="sxs-lookup"><span data-stu-id="329b2-447">'Identity'</span></span>
- <span data-ttu-id="329b2-448">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="329b2-448">'Let's Encrypt'</span></span>
- <span data-ttu-id="329b2-449">'Razor'</span><span class="sxs-lookup"><span data-stu-id="329b2-449">'Razor'</span></span>
- <span data-ttu-id="329b2-450">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="329b2-450">'SignalR' uid:</span></span> 

-
<span data-ttu-id="329b2-451">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="329b2-451">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="329b2-452">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="329b2-452">'Blazor'</span></span>
- <span data-ttu-id="329b2-453">'Identity'</span><span class="sxs-lookup"><span data-stu-id="329b2-453">'Identity'</span></span>
- <span data-ttu-id="329b2-454">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="329b2-454">'Let's Encrypt'</span></span>
- <span data-ttu-id="329b2-455">'Razor'</span><span class="sxs-lookup"><span data-stu-id="329b2-455">'Razor'</span></span>
- <span data-ttu-id="329b2-456">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="329b2-456">'SignalR' uid:</span></span> 

-
<span data-ttu-id="329b2-457">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="329b2-457">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="329b2-458">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="329b2-458">'Blazor'</span></span>
- <span data-ttu-id="329b2-459">'Identity'</span><span class="sxs-lookup"><span data-stu-id="329b2-459">'Identity'</span></span>
- <span data-ttu-id="329b2-460">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="329b2-460">'Let's Encrypt'</span></span>
- <span data-ttu-id="329b2-461">'Razor'</span><span class="sxs-lookup"><span data-stu-id="329b2-461">'Razor'</span></span>
- <span data-ttu-id="329b2-462">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="329b2-462">'SignalR' uid:</span></span> 

<span data-ttu-id="329b2-463">--------- | titre de--- : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="329b2-463">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="329b2-464">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="329b2-464">'Blazor'</span></span>
- <span data-ttu-id="329b2-465">'Identity'</span><span class="sxs-lookup"><span data-stu-id="329b2-465">'Identity'</span></span>
- <span data-ttu-id="329b2-466">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="329b2-466">'Let's Encrypt'</span></span>
- <span data-ttu-id="329b2-467">'Razor'</span><span class="sxs-lookup"><span data-stu-id="329b2-467">'Razor'</span></span>
- <span data-ttu-id="329b2-468">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="329b2-468">'SignalR' uid:</span></span> 

-
<span data-ttu-id="329b2-469">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="329b2-469">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="329b2-470">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="329b2-470">'Blazor'</span></span>
- <span data-ttu-id="329b2-471">'Identity'</span><span class="sxs-lookup"><span data-stu-id="329b2-471">'Identity'</span></span>
- <span data-ttu-id="329b2-472">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="329b2-472">'Let's Encrypt'</span></span>
- <span data-ttu-id="329b2-473">'Razor'</span><span class="sxs-lookup"><span data-stu-id="329b2-473">'Razor'</span></span>
- <span data-ttu-id="329b2-474">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="329b2-474">'SignalR' uid:</span></span> 

-
<span data-ttu-id="329b2-475">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="329b2-475">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="329b2-476">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="329b2-476">'Blazor'</span></span>
- <span data-ttu-id="329b2-477">'Identity'</span><span class="sxs-lookup"><span data-stu-id="329b2-477">'Identity'</span></span>
- <span data-ttu-id="329b2-478">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="329b2-478">'Let's Encrypt'</span></span>
- <span data-ttu-id="329b2-479">'Razor'</span><span class="sxs-lookup"><span data-stu-id="329b2-479">'Razor'</span></span>
- <span data-ttu-id="329b2-480">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="329b2-480">'SignalR' uid:</span></span> 

<span data-ttu-id="329b2-481">------ | | *AuthTests* | Contient des méthodes de test pour :</span><span class="sxs-lookup"><span data-stu-id="329b2-481">------ | | *AuthTests* | Contains test methods for:</span></span><ul><li><span data-ttu-id="329b2-482">Accès à une page sécurisée par un utilisateur non authentifié.</span><span class="sxs-lookup"><span data-stu-id="329b2-482">Accessing a secure page by an unauthenticated user.</span></span></li><li><span data-ttu-id="329b2-483">Accès à une page sécurisée par un utilisateur authentifié avec un simulacre <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> .</span><span class="sxs-lookup"><span data-stu-id="329b2-483">Accessing a secure page by an authenticated user with a mock <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>.</span></span></li><li><span data-ttu-id="329b2-484">Obtention d’un profil utilisateur GitHub et vérification de la connexion de l’utilisateur du profil.</span><span class="sxs-lookup"><span data-stu-id="329b2-484">Obtaining a GitHub user profile and checking the profile's user login.</span></span></li></ul> <span data-ttu-id="329b2-485">| | *BasicTests* | Contient une méthode de test pour le routage et le type de contenu.</span><span class="sxs-lookup"><span data-stu-id="329b2-485">| | *BasicTests* | Contains a test method for routing and content type.</span></span> <span data-ttu-id="329b2-486">| | *IntegrationTests* | Contient les tests d’intégration de la page d’index à l’aide de la `WebApplicationFactory` classe personnalisée.</span><span class="sxs-lookup"><span data-stu-id="329b2-486">| | *IntegrationTests* | Contains the integration tests for the Index page using custom `WebApplicationFactory` class.</span></span> <span data-ttu-id="329b2-487">| | *Programmes d’assistance/utilitaires* | </span><span class="sxs-lookup"><span data-stu-id="329b2-487">| | *Helpers/Utilities* | </span></span><ul><li><span data-ttu-id="329b2-488">*Utilities.cs* contient la `InitializeDbForTests` méthode utilisée pour amorcer la base de données avec des données de test.</span><span class="sxs-lookup"><span data-stu-id="329b2-488">*Utilities.cs* contains the `InitializeDbForTests` method used to seed the database with test data.</span></span></li><li><span data-ttu-id="329b2-489">*HtmlHelpers.cs* fournit une méthode pour retourner un AngleSharp `IHtmlDocument` pour une utilisation par les méthodes de test.</span><span class="sxs-lookup"><span data-stu-id="329b2-489">*HtmlHelpers.cs* provides a method to return an AngleSharp `IHtmlDocument` for use by the test methods.</span></span></li><li><span data-ttu-id="329b2-490">*HttpClientExtensions.cs* fournissent des surcharges pour `SendAsync` Envoyer des demandes au St.</span><span class="sxs-lookup"><span data-stu-id="329b2-490">*HttpClientExtensions.cs* provide overloads for `SendAsync` to submit requests to the SUT.</span></span></li></ul> |

<span data-ttu-id="329b2-491">L’infrastructure de test est [xUnit](https://xunit.github.io/).</span><span class="sxs-lookup"><span data-stu-id="329b2-491">The test framework is [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="329b2-492">Les tests d’intégration sont effectués à l’aide de [Microsoft. AspNetCore. TestHost](/dotnet/api/microsoft.aspnetcore.testhost), qui comprend le [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span><span class="sxs-lookup"><span data-stu-id="329b2-492">Integration tests are conducted using the [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost), which includes the [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span></span> <span data-ttu-id="329b2-493">Étant donné que le package [Microsoft. AspNetCore. Mvc. testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) est utilisé pour configurer l’hôte de test et le serveur de test, les `TestHost` `TestServer` packages et ne nécessitent pas de références de package directes dans le fichier projet ou la configuration de développeur de l’application de test dans l’application de test.</span><span class="sxs-lookup"><span data-stu-id="329b2-493">Because the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package is used to configure the test host and test server, the `TestHost` and `TestServer` packages don't require direct package references in the test app's project file or developer configuration in the test app.</span></span>

<span data-ttu-id="329b2-494">**Amorçage de la base de données à des fins de test**</span><span class="sxs-lookup"><span data-stu-id="329b2-494">**Seeding the database for testing**</span></span>

<span data-ttu-id="329b2-495">Les tests d’intégration nécessitent généralement un petit jeu de données dans la base de données avant l’exécution du test.</span><span class="sxs-lookup"><span data-stu-id="329b2-495">Integration tests usually require a small dataset in the database prior to the test execution.</span></span> <span data-ttu-id="329b2-496">Par exemple, un test Delete requiert la suppression d’un enregistrement de base de données, de sorte que la base de données doit avoir au moins un enregistrement pour que la demande Delete aboutisse.</span><span class="sxs-lookup"><span data-stu-id="329b2-496">For example, a delete test calls for a database record deletion, so the database must have at least one record for the delete request to succeed.</span></span>

<span data-ttu-id="329b2-497">L’exemple d’application amorce la base de données avec trois messages dans *Utilities.cs* que les tests peuvent utiliser lorsqu’ils exécutent :</span><span class="sxs-lookup"><span data-stu-id="329b2-497">The sample app seeds the database with three messages in *Utilities.cs* that tests can use when they execute:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

<span data-ttu-id="329b2-498">Le contexte de base de données de St est inscrit dans sa `Startup.ConfigureServices` méthode.</span><span class="sxs-lookup"><span data-stu-id="329b2-498">The SUT's database context is registered in its `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="329b2-499">Le rappel de l’application de test `builder.ConfigureServices` est exécuté *après* l’exécution du code de l’application `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="329b2-499">The test app's `builder.ConfigureServices` callback is executed *after* the app's `Startup.ConfigureServices` code is executed.</span></span> <span data-ttu-id="329b2-500">Pour utiliser une base de données différente pour les tests, le contexte de base de données de l’application doit être remplacé dans `builder.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="329b2-500">To use a different database for the tests, the app's database context must be replaced in `builder.ConfigureServices`.</span></span> <span data-ttu-id="329b2-501">Pour plus d’informations, consultez la section [personnaliser WebApplicationFactory](#customize-webapplicationfactory) .</span><span class="sxs-lookup"><span data-stu-id="329b2-501">For more information, see the [Customize WebApplicationFactory](#customize-webapplicationfactory) section.</span></span>

<span data-ttu-id="329b2-502">Pour les SUTs qui utilisent toujours l' [hôte Web](xref:fundamentals/host/web-host), le rappel de l’application de test `builder.ConfigureServices` est exécuté *avant* le code du St `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="329b2-502">For SUTs that still use the [Web Host](xref:fundamentals/host/web-host), the test app's `builder.ConfigureServices` callback is executed *before* the SUT's `Startup.ConfigureServices` code.</span></span> <span data-ttu-id="329b2-503">Le rappel de l’application de test `builder.ConfigureTestServices` est exécuté *après*.</span><span class="sxs-lookup"><span data-stu-id="329b2-503">The test app's `builder.ConfigureTestServices` callback is executed *after*.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="329b2-504">Les tests d’intégration garantissent que les composants d’une application fonctionnent correctement à un niveau qui comprend l’infrastructure de prise en charge de l’application, telle que la base de données, le système de fichiers et le réseau.</span><span class="sxs-lookup"><span data-stu-id="329b2-504">Integration tests ensure that an app's components function correctly at a level that includes the app's supporting infrastructure, such as the database, file system, and network.</span></span> <span data-ttu-id="329b2-505">ASP.NET Core prend en charge les tests d’intégration à l’aide d’une infrastructure de tests unitaires avec un hôte Web de test et un serveur de test en mémoire.</span><span class="sxs-lookup"><span data-stu-id="329b2-505">ASP.NET Core supports integration tests using a unit test framework with a test web host and an in-memory test server.</span></span>

<span data-ttu-id="329b2-506">Cette rubrique suppose une compréhension de base des tests unitaires.</span><span class="sxs-lookup"><span data-stu-id="329b2-506">This topic assumes a basic understanding of unit tests.</span></span> <span data-ttu-id="329b2-507">Si vous n’êtes pas familiarisé avec les concepts de test, consultez la rubrique [tests unitaires dans .net Core et .NET standard](/dotnet/core/testing/) et son contenu lié.</span><span class="sxs-lookup"><span data-stu-id="329b2-507">If unfamiliar with test concepts, see the [Unit Testing in .NET Core and .NET Standard](/dotnet/core/testing/) topic and its linked content.</span></span>

<span data-ttu-id="329b2-508">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="329b2-508">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="329b2-509">L’exemple d’application est une Razor application pages et suppose une compréhension de base des Razor pages.</span><span class="sxs-lookup"><span data-stu-id="329b2-509">The sample app is a Razor Pages app and assumes a basic understanding of Razor Pages.</span></span> <span data-ttu-id="329b2-510">Si vous n’êtes pas familiarisé avec Razor les pages, consultez les rubriques suivantes :</span><span class="sxs-lookup"><span data-stu-id="329b2-510">If unfamiliar with Razor Pages, see the following topics:</span></span>

* <span data-ttu-id="329b2-511">[Présentation des Razor pages](xref:razor-pages/index)</span><span class="sxs-lookup"><span data-stu-id="329b2-511">[Introduction to Razor Pages](xref:razor-pages/index)</span></span>
* <span data-ttu-id="329b2-512">[Prise en main des Razor pages](xref:tutorials/razor-pages/razor-pages-start)</span><span class="sxs-lookup"><span data-stu-id="329b2-512">[Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start)</span></span>
* <span data-ttu-id="329b2-513">[RazorPages tests unitaires](xref:test/razor-pages-tests)</span><span class="sxs-lookup"><span data-stu-id="329b2-513">[Razor Pages unit tests](xref:test/razor-pages-tests)</span></span>

> [!NOTE]
> <span data-ttu-id="329b2-514">Pour tester la SPAs, nous vous recommandons d’utiliser un outil tel que le [sélénium](https://www.seleniumhq.org/), qui peut automatiser un navigateur.</span><span class="sxs-lookup"><span data-stu-id="329b2-514">For testing SPAs, we recommended a tool such as [Selenium](https://www.seleniumhq.org/), which can automate a browser.</span></span>

## <a name="introduction-to-integration-tests"></a><span data-ttu-id="329b2-515">Présentation des tests d’intégration</span><span class="sxs-lookup"><span data-stu-id="329b2-515">Introduction to integration tests</span></span>

<span data-ttu-id="329b2-516">Les tests d’intégration évaluent les composants d’une application à un niveau plus large que les [tests unitaires](/dotnet/core/testing/).</span><span class="sxs-lookup"><span data-stu-id="329b2-516">Integration tests evaluate an app's components on a broader level than [unit tests](/dotnet/core/testing/).</span></span> <span data-ttu-id="329b2-517">Les tests unitaires sont utilisés pour tester des composants logiciels isolés, tels que des méthodes de classe individuelles.</span><span class="sxs-lookup"><span data-stu-id="329b2-517">Unit tests are used to test isolated software components, such as individual class methods.</span></span> <span data-ttu-id="329b2-518">Les tests d’intégration confirment que deux ou plusieurs composants d’application fonctionnent ensemble pour produire un résultat attendu, y compris éventuellement chaque composant requis pour traiter entièrement une demande.</span><span class="sxs-lookup"><span data-stu-id="329b2-518">Integration tests confirm that two or more app components work together to produce an expected result, possibly including every component required to fully process a request.</span></span>

<span data-ttu-id="329b2-519">Ces tests plus larges sont utilisés pour tester l’infrastructure de l’application et l’infrastructure entière, y compris les composants suivants :</span><span class="sxs-lookup"><span data-stu-id="329b2-519">These broader tests are used to test the app's infrastructure and whole framework, often including the following components:</span></span>

* <span data-ttu-id="329b2-520">Base de données</span><span class="sxs-lookup"><span data-stu-id="329b2-520">Database</span></span>
* <span data-ttu-id="329b2-521">Système de fichiers</span><span class="sxs-lookup"><span data-stu-id="329b2-521">File system</span></span>
* <span data-ttu-id="329b2-522">Appliances réseau</span><span class="sxs-lookup"><span data-stu-id="329b2-522">Network appliances</span></span>
* <span data-ttu-id="329b2-523">Pipeline de requête-réponse</span><span class="sxs-lookup"><span data-stu-id="329b2-523">Request-response pipeline</span></span>

<span data-ttu-id="329b2-524">Les tests unitaires utilisent des composants fabriqués, appelés objets *substituts* ou *factices*, à la place des composants d’infrastructure.</span><span class="sxs-lookup"><span data-stu-id="329b2-524">Unit tests use fabricated components, known as *fakes* or *mock objects*, in place of infrastructure components.</span></span>

<span data-ttu-id="329b2-525">Contrairement aux tests unitaires, les tests d’intégration :</span><span class="sxs-lookup"><span data-stu-id="329b2-525">In contrast to unit tests, integration tests:</span></span>

* <span data-ttu-id="329b2-526">Utilisez les composants réels utilisés par l’application en production.</span><span class="sxs-lookup"><span data-stu-id="329b2-526">Use the actual components that the app uses in production.</span></span>
* <span data-ttu-id="329b2-527">Nécessitent davantage de code et de traitement des données.</span><span class="sxs-lookup"><span data-stu-id="329b2-527">Require more code and data processing.</span></span>
* <span data-ttu-id="329b2-528">Prend plus de temps pour s’exécuter.</span><span class="sxs-lookup"><span data-stu-id="329b2-528">Take longer to run.</span></span>

<span data-ttu-id="329b2-529">Par conséquent, limitez l’utilisation des tests d’intégration aux scénarios d’infrastructure les plus importants.</span><span class="sxs-lookup"><span data-stu-id="329b2-529">Therefore, limit the use of integration tests to the most important infrastructure scenarios.</span></span> <span data-ttu-id="329b2-530">Si un comportement peut être testé à l’aide d’un test unitaire ou d’un test d’intégration, choisissez le test unitaire.</span><span class="sxs-lookup"><span data-stu-id="329b2-530">If a behavior can be tested using either a unit test or an integration test, choose the unit test.</span></span>

> [!TIP]
> <span data-ttu-id="329b2-531">N’écrivez pas de tests d’intégration pour chaque permutation possible d’accès aux données et aux fichiers avec les bases de données et les systèmes de fichiers.</span><span class="sxs-lookup"><span data-stu-id="329b2-531">Don't write integration tests for every possible permutation of data and file access with databases and file systems.</span></span> <span data-ttu-id="329b2-532">Quel que soit le nombre d’emplacements au sein d’une application qui interagissent avec les bases de données et les systèmes de fichiers, un ensemble de tests d’intégration de lecture, d’écriture, de mise à jour et de suppression est généralement en mesure de tester correctement les composants de base de données et de système de fichiers.</span><span class="sxs-lookup"><span data-stu-id="329b2-532">Regardless of how many places across an app interact with databases and file systems, a focused set of read, write, update, and delete integration tests are usually capable of adequately testing database and file system components.</span></span> <span data-ttu-id="329b2-533">Utilisez des tests unitaires pour les tests de routine de la logique de méthode qui interagissent avec ces composants.</span><span class="sxs-lookup"><span data-stu-id="329b2-533">Use unit tests for routine tests of method logic that interact with these components.</span></span> <span data-ttu-id="329b2-534">Dans les tests unitaires, l’utilisation de substituts ou de simulacres d’infrastructure entraîne une exécution plus rapide des tests.</span><span class="sxs-lookup"><span data-stu-id="329b2-534">In unit tests, the use of infrastructure fakes/mocks result in faster test execution.</span></span>

> [!NOTE]
> <span data-ttu-id="329b2-535">Dans les discussions sur les tests d’intégration, le projet testé est fréquemment appelé le *système en cours de test*ou « St » pour l’instant.</span><span class="sxs-lookup"><span data-stu-id="329b2-535">In discussions of integration tests, the tested project is frequently called the *System Under Test*, or "SUT" for short.</span></span>
>
> <span data-ttu-id="329b2-536">*« St » est utilisé dans cette rubrique pour faire référence à l’application ASP.NET Core testée.*</span><span class="sxs-lookup"><span data-stu-id="329b2-536">*"SUT" is used throughout this topic to refer to the tested ASP.NET Core app.*</span></span>

## <a name="aspnet-core-integration-tests"></a><span data-ttu-id="329b2-537">ASP.NET Core les tests d’intégration</span><span class="sxs-lookup"><span data-stu-id="329b2-537">ASP.NET Core integration tests</span></span>

<span data-ttu-id="329b2-538">Les tests d’intégration dans ASP.NET Core requièrent les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="329b2-538">Integration tests in ASP.NET Core require the following:</span></span>

* <span data-ttu-id="329b2-539">Un projet de test est utilisé pour contenir et exécuter les tests.</span><span class="sxs-lookup"><span data-stu-id="329b2-539">A test project is used to contain and execute the tests.</span></span> <span data-ttu-id="329b2-540">Le projet de test a une référence à St.</span><span class="sxs-lookup"><span data-stu-id="329b2-540">The test project has a reference to the SUT.</span></span>
* <span data-ttu-id="329b2-541">Le projet de test crée un hôte Web de test pour le St et utilise un client de serveur de test pour gérer les demandes et les réponses avec le St.</span><span class="sxs-lookup"><span data-stu-id="329b2-541">The test project creates a test web host for the SUT and uses a test server client to handle requests and responses with the SUT.</span></span>
* <span data-ttu-id="329b2-542">Un testeur de test est utilisé pour exécuter les tests et signaler les résultats des tests.</span><span class="sxs-lookup"><span data-stu-id="329b2-542">A test runner is used to execute the tests and report the test results.</span></span>

<span data-ttu-id="329b2-543">Les tests d’intégration suivent une séquence d’événements qui incluent les étapes de test *arrange*, *Act*et *Assert* habituelles :</span><span class="sxs-lookup"><span data-stu-id="329b2-543">Integration tests follow a sequence of events that include the usual *Arrange*, *Act*, and *Assert* test steps:</span></span>

1. <span data-ttu-id="329b2-544">L’hôte Web de St est configuré.</span><span class="sxs-lookup"><span data-stu-id="329b2-544">The SUT's web host is configured.</span></span>
1. <span data-ttu-id="329b2-545">Un client de serveur de test est créé pour envoyer des demandes à l’application.</span><span class="sxs-lookup"><span data-stu-id="329b2-545">A test server client is created to submit requests to the app.</span></span>
1. <span data-ttu-id="329b2-546">L’étape *organiser* le test est exécutée : l’application de test prépare une demande.</span><span class="sxs-lookup"><span data-stu-id="329b2-546">The *Arrange* test step is executed: The test app prepares a request.</span></span>
1. <span data-ttu-id="329b2-547">L’étape de test *Act* est exécutée : le client envoie la demande et reçoit la réponse.</span><span class="sxs-lookup"><span data-stu-id="329b2-547">The *Act* test step is executed: The client submits the request and receives the response.</span></span>
1. <span data-ttu-id="329b2-548">L’étape de test d' *assertion* est exécutée : la réponse *réelle* est validée en tant que *réussite* ou *échec* en fonction d’une réponse *attendue* .</span><span class="sxs-lookup"><span data-stu-id="329b2-548">The *Assert* test step is executed: The *actual* response is validated as a *pass* or *fail* based on an *expected* response.</span></span>
1. <span data-ttu-id="329b2-549">Le processus se poursuit jusqu’à ce que tous les tests soient exécutés.</span><span class="sxs-lookup"><span data-stu-id="329b2-549">The process continues until all of the tests are executed.</span></span>
1. <span data-ttu-id="329b2-550">Les résultats des tests sont signalés.</span><span class="sxs-lookup"><span data-stu-id="329b2-550">The test results are reported.</span></span>

<span data-ttu-id="329b2-551">En règle générale, l’hôte Web de test est configuré différemment de l’hôte Web normal de l’application pour les séries de tests.</span><span class="sxs-lookup"><span data-stu-id="329b2-551">Usually, the test web host is configured differently than the app's normal web host for the test runs.</span></span> <span data-ttu-id="329b2-552">Par exemple, une base de données différente ou des paramètres d’application différents peuvent être utilisés pour les tests.</span><span class="sxs-lookup"><span data-stu-id="329b2-552">For example, a different database or different app settings might be used for the tests.</span></span>

<span data-ttu-id="329b2-553">Les composants d’infrastructure, tels que l’hôte Web de test et le serveur de test en mémoire ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)), sont fournis ou gérés par le package [Microsoft. AspNetCore. Mvc. testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) .</span><span class="sxs-lookup"><span data-stu-id="329b2-553">Infrastructure components, such as the test web host and in-memory test server ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)), are provided or managed by the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package.</span></span> <span data-ttu-id="329b2-554">L’utilisation de ce package simplifie la création et l’exécution des tests.</span><span class="sxs-lookup"><span data-stu-id="329b2-554">Use of this package streamlines test creation and execution.</span></span>

<span data-ttu-id="329b2-555">Le `Microsoft.AspNetCore.Mvc.Testing` package gère les tâches suivantes :</span><span class="sxs-lookup"><span data-stu-id="329b2-555">The `Microsoft.AspNetCore.Mvc.Testing` package handles the following tasks:</span></span>

* <span data-ttu-id="329b2-556">Copie le fichier de dépendances (*. DEPS*) à partir du St dans le répertoire *bin* du projet de test.</span><span class="sxs-lookup"><span data-stu-id="329b2-556">Copies the dependencies file (*.deps*) from the SUT into the test project's *bin* directory.</span></span>
* <span data-ttu-id="329b2-557">Définit la racine du [contenu](xref:fundamentals/index#content-root) sur la racine du projet St pour que les fichiers et les pages statiques soient trouvés lors de l’exécution des tests.</span><span class="sxs-lookup"><span data-stu-id="329b2-557">Sets the [content root](xref:fundamentals/index#content-root) to the SUT's project root so that static files and pages/views are found when the tests are executed.</span></span>
* <span data-ttu-id="329b2-558">Fournit la classe [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) pour simplifier l’amorçage du St avec `TestServer` .</span><span class="sxs-lookup"><span data-stu-id="329b2-558">Provides the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) class to streamline bootstrapping the SUT with `TestServer`.</span></span>

<span data-ttu-id="329b2-559">La documentation sur les [tests unitaires](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) décrit comment configurer un projet de test et Test Runner, ainsi que des instructions détaillées sur la façon d’exécuter des tests et des recommandations sur la façon de nommer des tests et des classes de test.</span><span class="sxs-lookup"><span data-stu-id="329b2-559">The [unit tests](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) documentation describes how to set up a test project and test runner, along with detailed instructions on how to run tests and recommendations for how to name tests and test classes.</span></span>

> [!NOTE]
> <span data-ttu-id="329b2-560">Lorsque vous créez un projet de test pour une application, séparez les tests unitaires des tests d’intégration dans différents projets.</span><span class="sxs-lookup"><span data-stu-id="329b2-560">When creating a test project for an app, separate the unit tests from the integration tests into different projects.</span></span> <span data-ttu-id="329b2-561">Cela permet de s’assurer que les composants de test d’infrastructure ne sont pas accidentellement inclus dans les tests unitaires.</span><span class="sxs-lookup"><span data-stu-id="329b2-561">This helps ensure that infrastructure testing components aren't accidentally included in the unit tests.</span></span> <span data-ttu-id="329b2-562">La séparation des tests d’unité et d’intégration permet également de contrôler l’ensemble des tests exécutés.</span><span class="sxs-lookup"><span data-stu-id="329b2-562">Separation of unit and integration tests also allows control over which set of tests are run.</span></span>

<span data-ttu-id="329b2-563">Il n’existe pratiquement aucune différence entre la configuration des tests d' Razor applications de pages et les applications MVC.</span><span class="sxs-lookup"><span data-stu-id="329b2-563">There's virtually no difference between the configuration for tests of Razor Pages apps and MVC apps.</span></span> <span data-ttu-id="329b2-564">La seule différence réside dans la façon dont les tests sont nommés.</span><span class="sxs-lookup"><span data-stu-id="329b2-564">The only difference is in how the tests are named.</span></span> <span data-ttu-id="329b2-565">Dans une Razor application de pages, les tests des points de terminaison de page sont généralement nommés après la classe de modèle de page (par exemple, `IndexPageTests` pour tester l’intégration des composants pour la page d’index).</span><span class="sxs-lookup"><span data-stu-id="329b2-565">In a Razor Pages app, tests of page endpoints are usually named after the page model class (for example, `IndexPageTests` to test component integration for the Index page).</span></span> <span data-ttu-id="329b2-566">Dans une application MVC, les tests sont généralement organisés par classes de contrôleur et nommés après les contrôleurs testés (par exemple, `HomeControllerTests` pour tester l’intégration des composants pour le contrôleur de démarrage).</span><span class="sxs-lookup"><span data-stu-id="329b2-566">In an MVC app, tests are usually organized by controller classes and named after the controllers they test (for example, `HomeControllerTests` to test component integration for the Home controller).</span></span>

## <a name="test-app-prerequisites"></a><span data-ttu-id="329b2-567">Conditions préalables pour les applications de test</span><span class="sxs-lookup"><span data-stu-id="329b2-567">Test app prerequisites</span></span>

<span data-ttu-id="329b2-568">Le projet de test doit :</span><span class="sxs-lookup"><span data-stu-id="329b2-568">The test project must:</span></span>

* <span data-ttu-id="329b2-569">Référencez les packages suivants :</span><span class="sxs-lookup"><span data-stu-id="329b2-569">Reference the following packages:</span></span>
  * [<span data-ttu-id="329b2-570">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="329b2-570">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)
  * [<span data-ttu-id="329b2-571">Microsoft. AspNetCore. Mvc. testing</span><span class="sxs-lookup"><span data-stu-id="329b2-571">Microsoft.AspNetCore.Mvc.Testing</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/)
* <span data-ttu-id="329b2-572">Spécifiez le kit de développement logiciel (SDK) Web dans le fichier projet ( `<Project Sdk="Microsoft.NET.Sdk.Web">` ).</span><span class="sxs-lookup"><span data-stu-id="329b2-572">Specify the Web SDK in the project file (`<Project Sdk="Microsoft.NET.Sdk.Web">`).</span></span> <span data-ttu-id="329b2-573">Le kit de développement logiciel (SDK) Web est requis pour référencer le [AspNetCore](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="329b2-573">The Web SDK is required when referencing the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="329b2-574">Ces conditions préalables peuvent être consultées dans l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/).</span><span class="sxs-lookup"><span data-stu-id="329b2-574">These prerequisites can be seen in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/).</span></span> <span data-ttu-id="329b2-575">Inspectez le fichier *tests/RazorPagesProject. tests/RazorPagesProject. tests. csproj* .</span><span class="sxs-lookup"><span data-stu-id="329b2-575">Inspect the *tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj* file.</span></span> <span data-ttu-id="329b2-576">L’exemple d’application utilise l’infrastructure de test [xUnit](https://xunit.github.io/) et la bibliothèque de l’analyseur [AngleSharp](https://anglesharp.github.io/) , de sorte que l’exemple d’application référence également :</span><span class="sxs-lookup"><span data-stu-id="329b2-576">The sample app uses the [xUnit](https://xunit.github.io/) test framework and the [AngleSharp](https://anglesharp.github.io/) parser library, so the sample app also references:</span></span>

* [<span data-ttu-id="329b2-577">xUnit</span><span class="sxs-lookup"><span data-stu-id="329b2-577">xunit</span></span>](https://www.nuget.org/packages/xunit/)
* [<span data-ttu-id="329b2-578">xUnit. Runner. VisualStudio</span><span class="sxs-lookup"><span data-stu-id="329b2-578">xunit.runner.visualstudio</span></span>](https://www.nuget.org/packages/xunit.runner.visualstudio/)
* [<span data-ttu-id="329b2-579">AngleSharp</span><span class="sxs-lookup"><span data-stu-id="329b2-579">AngleSharp</span></span>](https://www.nuget.org/packages/AngleSharp/)

## <a name="sut-environment"></a><span data-ttu-id="329b2-580">Environnement St</span><span class="sxs-lookup"><span data-stu-id="329b2-580">SUT environment</span></span>

<span data-ttu-id="329b2-581">Si l' [environnement](xref:fundamentals/environments) de St n’est pas défini, l’environnement est par défaut développement.</span><span class="sxs-lookup"><span data-stu-id="329b2-581">If the SUT's [environment](xref:fundamentals/environments) isn't set, the environment defaults to Development.</span></span>

## <a name="basic-tests-with-the-default-webapplicationfactory"></a><span data-ttu-id="329b2-582">Tests de base avec le WebApplicationFactory par défaut</span><span class="sxs-lookup"><span data-stu-id="329b2-582">Basic tests with the default WebApplicationFactory</span></span>

<span data-ttu-id="329b2-583">[WebApplicationFactory \< TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) est utilisé pour créer un [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) pour les tests d’intégration.</span><span class="sxs-lookup"><span data-stu-id="329b2-583">[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) is used to create a [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) for the integration tests.</span></span> <span data-ttu-id="329b2-584">`TEntryPoint`est la classe de point d’entrée de St, généralement la `Startup` classe.</span><span class="sxs-lookup"><span data-stu-id="329b2-584">`TEntryPoint` is the entry point class of the SUT, usually the `Startup` class.</span></span>

<span data-ttu-id="329b2-585">Les classes de test implémentent une interface de *contexte de classe* ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) pour indiquer que la classe contient des tests et fournir des instances d’objet partagé dans les tests de la classe.</span><span class="sxs-lookup"><span data-stu-id="329b2-585">Test classes implement a *class fixture* interface ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) to indicate the class contains tests and provide shared object instances across the tests in the class.</span></span>

<span data-ttu-id="329b2-586">La classe de test suivante, `BasicTests` , utilise le `WebApplicationFactory` pour démarrer le St et fournir un [httpclient](/dotnet/api/system.net.http.httpclient) à une méthode de test, `Get_EndpointsReturnSuccessAndCorrectContentType` .</span><span class="sxs-lookup"><span data-stu-id="329b2-586">The following test class, `BasicTests`, uses the `WebApplicationFactory` to bootstrap the SUT and provide an [HttpClient](/dotnet/api/system.net.http.httpclient) to a test method, `Get_EndpointsReturnSuccessAndCorrectContentType`.</span></span> <span data-ttu-id="329b2-587">La méthode vérifie si le code d’état de réponse a réussi (codes d’État dans la plage 200-299) et si l' `Content-Type` en-tête est `text/html; charset=utf-8` pour plusieurs pages d’application.</span><span class="sxs-lookup"><span data-stu-id="329b2-587">The method checks if the response status code is successful (status codes in the range 200-299) and the `Content-Type` header is `text/html; charset=utf-8` for several app pages.</span></span>

<span data-ttu-id="329b2-588">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) crée une instance de `HttpClient` qui suit automatiquement les redirections et gère les cookies.</span><span class="sxs-lookup"><span data-stu-id="329b2-588">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) creates an instance of `HttpClient` that automatically follows redirects and handles cookies.</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

<span data-ttu-id="329b2-589">Par défaut, les cookies non essentiels ne sont pas conservés entre les demandes lorsque la [stratégie de consentement RGPD](xref:security/gdpr) est activée.</span><span class="sxs-lookup"><span data-stu-id="329b2-589">By default, non-essential cookies aren't preserved across requests when the [GDPR consent policy](xref:security/gdpr) is enabled.</span></span> <span data-ttu-id="329b2-590">Pour conserver les cookies non essentiels, tels que ceux utilisés par le fournisseur TempData, marquez-les comme essentiels dans vos tests.</span><span class="sxs-lookup"><span data-stu-id="329b2-590">To preserve non-essential cookies, such as those used by the TempData provider, mark them as essential in your tests.</span></span> <span data-ttu-id="329b2-591">Pour obtenir des instructions sur le marquage d’un cookie comme étant essentiel, consultez [les cookies essentiels](xref:security/gdpr#essential-cookies).</span><span class="sxs-lookup"><span data-stu-id="329b2-591">For instructions on marking a cookie as essential, see [Essential cookies](xref:security/gdpr#essential-cookies).</span></span>

## <a name="customize-webapplicationfactory"></a><span data-ttu-id="329b2-592">Personnaliser WebApplicationFactory</span><span class="sxs-lookup"><span data-stu-id="329b2-592">Customize WebApplicationFactory</span></span>

<span data-ttu-id="329b2-593">La configuration d’hôte Web peut être créée indépendamment des classes de test en héritant de `WebApplicationFactory` pour créer une ou plusieurs fabriques personnalisées :</span><span class="sxs-lookup"><span data-stu-id="329b2-593">Web host configuration can be created independently of the test classes by inheriting from `WebApplicationFactory` to create one or more custom factories:</span></span>

1. <span data-ttu-id="329b2-594">Hériter de `WebApplicationFactory` et substituer [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost).</span><span class="sxs-lookup"><span data-stu-id="329b2-594">Inherit from `WebApplicationFactory` and override [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost).</span></span> <span data-ttu-id="329b2-595">Le [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) permet la configuration de la collection de services avec [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices), qui est exécuté avant l’application `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="329b2-595">The [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) allows the configuration of the service collection with [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices), which is executed before the app's `Startup.ConfigureServices`.</span></span> <span data-ttu-id="329b2-596">Le [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) permet de remplacer et de modifier les services inscrits dans la collection de services avec [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices):</span><span class="sxs-lookup"><span data-stu-id="329b2-596">The [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) allows overriding and modifying registered services in the service collection with [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices):</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   <span data-ttu-id="329b2-597">L’amorçage de base de données dans l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) est effectué par la `InitializeDbForTests` méthode.</span><span class="sxs-lookup"><span data-stu-id="329b2-597">Database seeding in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is performed by the `InitializeDbForTests` method.</span></span> <span data-ttu-id="329b2-598">La méthode est décrite dans la section [exemple de tests d’intégration : organisation](#test-app-organization) d’une application de test.</span><span class="sxs-lookup"><span data-stu-id="329b2-598">The method is described in the [Integration tests sample: Test app organization](#test-app-organization) section.</span></span>

2. <span data-ttu-id="329b2-599">Utilisez le personnalisé `CustomWebApplicationFactory` dans les classes de test.</span><span class="sxs-lookup"><span data-stu-id="329b2-599">Use the custom `CustomWebApplicationFactory` in test classes.</span></span> <span data-ttu-id="329b2-600">L’exemple suivant utilise la fabrique dans la `IndexPageTests` classe :</span><span class="sxs-lookup"><span data-stu-id="329b2-600">The following example uses the factory in the `IndexPageTests` class:</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   <span data-ttu-id="329b2-601">Le client de l’exemple d’application est configuré pour empêcher la `HttpClient` suite de redirections.</span><span class="sxs-lookup"><span data-stu-id="329b2-601">The sample app's client is configured to prevent the `HttpClient` from following redirects.</span></span> <span data-ttu-id="329b2-602">Comme expliqué plus loin dans la section [d’authentification des simulacres](#mock-authentication) , cela permet aux tests de vérifier le résultat de la première réponse de l’application.</span><span class="sxs-lookup"><span data-stu-id="329b2-602">As explained later in the [Mock authentication](#mock-authentication) section, this permits tests to check the result of the app's first response.</span></span> <span data-ttu-id="329b2-603">La première réponse est une redirection dans un grand nombre de ces tests avec un `Location` en-tête.</span><span class="sxs-lookup"><span data-stu-id="329b2-603">The first response is a redirect in many of these tests with a `Location` header.</span></span>

3. <span data-ttu-id="329b2-604">Un test standard utilise les `HttpClient` méthodes d’assistance et pour traiter la demande et la réponse :</span><span class="sxs-lookup"><span data-stu-id="329b2-604">A typical test uses the `HttpClient` and helper methods to process the request and the response:</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

<span data-ttu-id="329b2-605">Toute demande de publication à l’St doit être conforme à la vérification anti-contrefaçon effectuée automatiquement par le [système anti-contrefaçon de protection des données](xref:security/data-protection/introduction)de l’application.</span><span class="sxs-lookup"><span data-stu-id="329b2-605">Any POST request to the SUT must satisfy the antiforgery check that's automatically made by the app's [data protection antiforgery system](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="329b2-606">Pour organiser la requête de publication d’un test, l’application de test doit :</span><span class="sxs-lookup"><span data-stu-id="329b2-606">In order to arrange for a test's POST request, the test app must:</span></span>

1. <span data-ttu-id="329b2-607">Effectuez une demande pour la page.</span><span class="sxs-lookup"><span data-stu-id="329b2-607">Make a request for the page.</span></span>
1. <span data-ttu-id="329b2-608">Analysez le cookie anti-contrefaçon et le jeton de validation de demande à partir de la réponse.</span><span class="sxs-lookup"><span data-stu-id="329b2-608">Parse the antiforgery cookie and request validation token from the response.</span></span>
1. <span data-ttu-id="329b2-609">Effectuez la demande de publication avec le cookie anti-contrefaçon et le jeton de validation de demande en place.</span><span class="sxs-lookup"><span data-stu-id="329b2-609">Make the POST request with the antiforgery cookie and request validation token in place.</span></span>

<span data-ttu-id="329b2-610">Les `SendAsync` méthodes d’extension d’assistance (*helpers/HttpClientExtensions. cs*) et la `GetDocumentAsync` méthode d’assistance (*helpers/HtmlHelper. cs*) dans l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) utilisent l’analyseur [AngleSharp](https://anglesharp.github.io/) pour gérer la vérification anti-falsification avec les méthodes suivantes :</span><span class="sxs-lookup"><span data-stu-id="329b2-610">The `SendAsync` helper extension methods (*Helpers/HttpClientExtensions.cs*) and the `GetDocumentAsync` helper method (*Helpers/HtmlHelpers.cs*) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) use the [AngleSharp](https://anglesharp.github.io/) parser to handle the antiforgery check with the following methods:</span></span>

* <span data-ttu-id="329b2-611">`GetDocumentAsync`&ndash;Reçoit le [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) et retourne un `IHtmlDocument` .</span><span class="sxs-lookup"><span data-stu-id="329b2-611">`GetDocumentAsync` &ndash; Receives the [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) and returns an `IHtmlDocument`.</span></span> <span data-ttu-id="329b2-612">`GetDocumentAsync`utilise une fabrique qui prépare une *réponse virtuelle* en fonction de l’original `HttpResponseMessage` .</span><span class="sxs-lookup"><span data-stu-id="329b2-612">`GetDocumentAsync` uses a factory that prepares a *virtual response* based on the original `HttpResponseMessage`.</span></span> <span data-ttu-id="329b2-613">Pour plus d’informations, consultez la [documentation AngleSharp](https://github.com/AngleSharp/AngleSharp#documentation).</span><span class="sxs-lookup"><span data-stu-id="329b2-613">For more information, see the [AngleSharp documentation](https://github.com/AngleSharp/AngleSharp#documentation).</span></span>
* <span data-ttu-id="329b2-614">`SendAsync`les méthodes d’extension pour `HttpClient` compose un [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) et appellent [SendAsync (HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) pour envoyer des demandes au St.</span><span class="sxs-lookup"><span data-stu-id="329b2-614">`SendAsync` extension methods for the `HttpClient` compose an [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) and call [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) to submit requests to the SUT.</span></span> <span data-ttu-id="329b2-615">Les surcharges pour `SendAsync` acceptent le formulaire HTML ( `IHtmlFormElement` ) et les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="329b2-615">Overloads for `SendAsync` accept the HTML form (`IHtmlFormElement`) and the following:</span></span>
  * <span data-ttu-id="329b2-616">Bouton envoyer du formulaire ( `IHtmlElement` )</span><span class="sxs-lookup"><span data-stu-id="329b2-616">Submit button of the form (`IHtmlElement`)</span></span>
  * <span data-ttu-id="329b2-617">Collection de valeurs de formulaire ( `IEnumerable<KeyValuePair<string, string>>` )</span><span class="sxs-lookup"><span data-stu-id="329b2-617">Form values collection (`IEnumerable<KeyValuePair<string, string>>`)</span></span>
  * <span data-ttu-id="329b2-618">Bouton envoyer ( `IHtmlElement` ) et valeurs de formulaire ( `IEnumerable<KeyValuePair<string, string>>` )</span><span class="sxs-lookup"><span data-stu-id="329b2-618">Submit button (`IHtmlElement`) and form values (`IEnumerable<KeyValuePair<string, string>>`)</span></span>

> [!NOTE]
> <span data-ttu-id="329b2-619">[AngleSharp](https://anglesharp.github.io/) est une bibliothèque d’analyse tierce utilisée à des fins de démonstration dans cette rubrique et l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="329b2-619">[AngleSharp](https://anglesharp.github.io/) is a third-party parsing library used for demonstration purposes in this topic and the sample app.</span></span> <span data-ttu-id="329b2-620">AngleSharp n’est pas pris en charge ou requis pour le test d’intégration des applications ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="329b2-620">AngleSharp isn't supported or required for integration testing of ASP.NET Core apps.</span></span> <span data-ttu-id="329b2-621">D’autres analyseurs peuvent être utilisés, tels que le [HAP (html agilité Pack)](https://html-agility-pack.net/).</span><span class="sxs-lookup"><span data-stu-id="329b2-621">Other parsers can be used, such as the [Html Agility Pack (HAP)](https://html-agility-pack.net/).</span></span> <span data-ttu-id="329b2-622">Une autre approche consiste à écrire du code pour gérer directement le jeton de vérification de demande et le cookie anti-contrefaçon du système anti-contrefaçon.</span><span class="sxs-lookup"><span data-stu-id="329b2-622">Another approach is to write code to handle the antiforgery system's request verification token and antiforgery cookie directly.</span></span>

## <a name="customize-the-client-with-withwebhostbuilder"></a><span data-ttu-id="329b2-623">Personnaliser le client avec WithWebHostBuilder</span><span class="sxs-lookup"><span data-stu-id="329b2-623">Customize the client with WithWebHostBuilder</span></span>

<span data-ttu-id="329b2-624">Quand une configuration supplémentaire est requise dans une méthode de test, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) crée un nouveau `WebApplicationFactory` avec un [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) qui est ensuite personnalisé par la configuration.</span><span class="sxs-lookup"><span data-stu-id="329b2-624">When additional configuration is required within a test method, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) creates a new `WebApplicationFactory` with an [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) that is further customized by configuration.</span></span>

<span data-ttu-id="329b2-625">La `Post_DeleteMessageHandler_ReturnsRedirectToRoot` méthode de test de l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) montre l’utilisation de `WithWebHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="329b2-625">The `Post_DeleteMessageHandler_ReturnsRedirectToRoot` test method of the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) demonstrates the use of `WithWebHostBuilder`.</span></span> <span data-ttu-id="329b2-626">Ce test effectue une suppression d’enregistrement dans la base de données en déclenchant une soumission de formulaire dans St.</span><span class="sxs-lookup"><span data-stu-id="329b2-626">This test performs a record delete in the database by triggering a form submission in the SUT.</span></span>

<span data-ttu-id="329b2-627">Étant donné qu’un autre test de la `IndexPageTests` classe effectue une opération qui supprime tous les enregistrements de la base de données et peut s’exécuter avant la `Post_DeleteMessageHandler_ReturnsRedirectToRoot` méthode, la base de données est réamorcée dans cette méthode de test pour s’assurer qu’un enregistrement est présent pour que le St puisse être supprimé.</span><span class="sxs-lookup"><span data-stu-id="329b2-627">Because another test in the `IndexPageTests` class performs an operation that deletes all of the records in the database and may run before the `Post_DeleteMessageHandler_ReturnsRedirectToRoot` method, the database is reseeded in this test method to ensure that a record is present for the SUT to delete.</span></span> <span data-ttu-id="329b2-628">La sélection du premier bouton supprimer du `messages` formulaire dans le St est simulée dans la requête adressée au St :</span><span class="sxs-lookup"><span data-stu-id="329b2-628">Selecting the first delete button of the `messages` form in the SUT is simulated in the request to the SUT:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a><span data-ttu-id="329b2-629">Options du client</span><span class="sxs-lookup"><span data-stu-id="329b2-629">Client options</span></span>

<span data-ttu-id="329b2-630">Le tableau suivant indique les [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) par défaut disponibles lors de la création d' `HttpClient` instances.</span><span class="sxs-lookup"><span data-stu-id="329b2-630">The following table shows the default [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) available when creating `HttpClient` instances.</span></span>

| <span data-ttu-id="329b2-631">Option</span><span class="sxs-lookup"><span data-stu-id="329b2-631">Option</span></span> | <span data-ttu-id="329b2-632">Description</span><span class="sxs-lookup"><span data-stu-id="329b2-632">Description</span></span> | <span data-ttu-id="329b2-633">Default</span><span class="sxs-lookup"><span data-stu-id="329b2-633">Default</span></span> |
| ---
<span data-ttu-id="329b2-634">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="329b2-634">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="329b2-635">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="329b2-635">'Blazor'</span></span>
- <span data-ttu-id="329b2-636">'Identity'</span><span class="sxs-lookup"><span data-stu-id="329b2-636">'Identity'</span></span>
- <span data-ttu-id="329b2-637">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="329b2-637">'Let's Encrypt'</span></span>
- <span data-ttu-id="329b2-638">'Razor'</span><span class="sxs-lookup"><span data-stu-id="329b2-638">'Razor'</span></span>
- <span data-ttu-id="329b2-639">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="329b2-639">'SignalR' uid:</span></span> 

<span data-ttu-id="329b2-640">--- | titre de--- : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="329b2-640">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="329b2-641">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="329b2-641">'Blazor'</span></span>
- <span data-ttu-id="329b2-642">'Identity'</span><span class="sxs-lookup"><span data-stu-id="329b2-642">'Identity'</span></span>
- <span data-ttu-id="329b2-643">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="329b2-643">'Let's Encrypt'</span></span>
- <span data-ttu-id="329b2-644">'Razor'</span><span class="sxs-lookup"><span data-stu-id="329b2-644">'Razor'</span></span>
- <span data-ttu-id="329b2-645">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="329b2-645">'SignalR' uid:</span></span> 

-
<span data-ttu-id="329b2-646">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="329b2-646">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="329b2-647">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="329b2-647">'Blazor'</span></span>
- <span data-ttu-id="329b2-648">'Identity'</span><span class="sxs-lookup"><span data-stu-id="329b2-648">'Identity'</span></span>
- <span data-ttu-id="329b2-649">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="329b2-649">'Let's Encrypt'</span></span>
- <span data-ttu-id="329b2-650">'Razor'</span><span class="sxs-lookup"><span data-stu-id="329b2-650">'Razor'</span></span>
- <span data-ttu-id="329b2-651">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="329b2-651">'SignalR' uid:</span></span> 

-
<span data-ttu-id="329b2-652">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="329b2-652">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="329b2-653">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="329b2-653">'Blazor'</span></span>
- <span data-ttu-id="329b2-654">'Identity'</span><span class="sxs-lookup"><span data-stu-id="329b2-654">'Identity'</span></span>
- <span data-ttu-id="329b2-655">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="329b2-655">'Let's Encrypt'</span></span>
- <span data-ttu-id="329b2-656">'Razor'</span><span class="sxs-lookup"><span data-stu-id="329b2-656">'Razor'</span></span>
- <span data-ttu-id="329b2-657">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="329b2-657">'SignalR' uid:</span></span> 

<span data-ttu-id="329b2-658">------ | titre de--- : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="329b2-658">------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="329b2-659">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="329b2-659">'Blazor'</span></span>
- <span data-ttu-id="329b2-660">'Identity'</span><span class="sxs-lookup"><span data-stu-id="329b2-660">'Identity'</span></span>
- <span data-ttu-id="329b2-661">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="329b2-661">'Let's Encrypt'</span></span>
- <span data-ttu-id="329b2-662">'Razor'</span><span class="sxs-lookup"><span data-stu-id="329b2-662">'Razor'</span></span>
- <span data-ttu-id="329b2-663">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="329b2-663">'SignalR' uid:</span></span> 

<span data-ttu-id="329b2-664">---- | | [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | Obtient ou définit une valeur indiquant si les `HttpClient` instances de doivent suivre automatiquement les réponses de redirection.</span><span class="sxs-lookup"><span data-stu-id="329b2-664">---- | | [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | Gets or sets whether or not `HttpClient` instances should automatically follow redirect responses.</span></span><span data-ttu-id="329b2-665"> | `true`| | [BaseAddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | Obtient ou définit l’adresse de base des `HttpClient` instances.</span><span class="sxs-lookup"><span data-stu-id="329b2-665"> | `true` | | [BaseAddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | Gets or sets the base address of `HttpClient` instances.</span></span><span data-ttu-id="329b2-666"> | `http://localhost`| | [HandleCookies](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | Obtient ou définit une valeur indiquant si `HttpClient` les instances doivent gérer les cookies.</span><span class="sxs-lookup"><span data-stu-id="329b2-666"> | `http://localhost` | | [HandleCookies](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | Gets or sets whether `HttpClient` instances should handle cookies.</span></span><span data-ttu-id="329b2-667"> | `true`| | [MaxAutomaticRedirections](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | Obtient ou définit le nombre maximal de réponses de redirection que les `HttpClient` instances doivent suivre.</span><span class="sxs-lookup"><span data-stu-id="329b2-667"> | `true` | | [MaxAutomaticRedirections](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | Gets or sets the maximum number of redirect responses that `HttpClient` instances should follow.</span></span> <span data-ttu-id="329b2-668">| 7 |</span><span class="sxs-lookup"><span data-stu-id="329b2-668">| 7 |</span></span>

<span data-ttu-id="329b2-669">Créez la `WebApplicationFactoryClientOptions` classe et transmettez-la à la méthode [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) (les valeurs par défaut sont affichées dans l’exemple de code) :</span><span class="sxs-lookup"><span data-stu-id="329b2-669">Create the `WebApplicationFactoryClientOptions` class and pass it to the [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) method (default values are shown in the code example):</span></span>

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a><span data-ttu-id="329b2-670">Injecter des services fictifs</span><span class="sxs-lookup"><span data-stu-id="329b2-670">Inject mock services</span></span>

<span data-ttu-id="329b2-671">Les services peuvent être substitués dans un test à l’aide d’un appel à [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) sur le générateur de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="329b2-671">Services can be overridden in a test with a call to [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) on the host builder.</span></span> <span data-ttu-id="329b2-672">**Pour injecter des services fictifs, St doit avoir une `Startup` classe avec une `Startup.ConfigureServices` méthode.**</span><span class="sxs-lookup"><span data-stu-id="329b2-672">**To inject mock services, the SUT must have a `Startup` class with a `Startup.ConfigureServices` method.**</span></span>

<span data-ttu-id="329b2-673">L’exemple de St comprend un service étendu qui retourne un guillemet.</span><span class="sxs-lookup"><span data-stu-id="329b2-673">The sample SUT includes a scoped service that returns a quote.</span></span> <span data-ttu-id="329b2-674">Le guillemet est incorporé dans un champ masqué sur la page d’index lorsque la page d’index est demandée.</span><span class="sxs-lookup"><span data-stu-id="329b2-674">The quote is embedded in a hidden field on the Index page when the Index page is requested.</span></span>

<span data-ttu-id="329b2-675">*Services/IQuoteService. cs*:</span><span class="sxs-lookup"><span data-stu-id="329b2-675">*Services/IQuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

<span data-ttu-id="329b2-676">*Services/QuoteService. cs*:</span><span class="sxs-lookup"><span data-stu-id="329b2-676">*Services/QuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

<span data-ttu-id="329b2-677">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="329b2-677">*Startup.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

<span data-ttu-id="329b2-678">*Pages/Index.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="329b2-678">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

<span data-ttu-id="329b2-679">*Pages/index. cs*:</span><span class="sxs-lookup"><span data-stu-id="329b2-679">*Pages/Index.cs*:</span></span>

[!code-cshtml[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

<span data-ttu-id="329b2-680">Le balisage suivant est généré lors de l’exécution de l’application St :</span><span class="sxs-lookup"><span data-stu-id="329b2-680">The following markup is generated when the SUT app is run:</span></span>

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

<span data-ttu-id="329b2-681">Pour tester l’injection de service et de guillemets dans un test d’intégration, un service fictif est injecté dans le St par le test.</span><span class="sxs-lookup"><span data-stu-id="329b2-681">To test the service and quote injection in an integration test, a mock service is injected into the SUT by the test.</span></span> <span data-ttu-id="329b2-682">Le service factice remplace l’application par `QuoteService` un service fourni par l’application de test, appelé `TestQuoteService` :</span><span class="sxs-lookup"><span data-stu-id="329b2-682">The mock service replaces the app's `QuoteService` with a service provided by the test app, called `TestQuoteService`:</span></span>

<span data-ttu-id="329b2-683">*IntegrationTests.IndexPageTests.cs*:</span><span class="sxs-lookup"><span data-stu-id="329b2-683">*IntegrationTests.IndexPageTests.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

<span data-ttu-id="329b2-684">`ConfigureTestServices`est appelé, et le service étendu est inscrit :</span><span class="sxs-lookup"><span data-stu-id="329b2-684">`ConfigureTestServices` is called, and the scoped service is registered:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

<span data-ttu-id="329b2-685">Le balisage produit pendant l’exécution du test reflète le texte du devis fourni par `TestQuoteService` , donc l’assertion réussit :</span><span class="sxs-lookup"><span data-stu-id="329b2-685">The markup produced during the test's execution reflects the quote text supplied by `TestQuoteService`, thus the assertion passes:</span></span>

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="mock-authentication"></a><span data-ttu-id="329b2-686">Simuler l’authentification</span><span class="sxs-lookup"><span data-stu-id="329b2-686">Mock authentication</span></span>

<span data-ttu-id="329b2-687">Les tests de la `AuthTests` classe vérifient qu’un point de terminaison sécurisé :</span><span class="sxs-lookup"><span data-stu-id="329b2-687">Tests in the `AuthTests` class check that a secure endpoint:</span></span>

* <span data-ttu-id="329b2-688">Redirige un utilisateur non authentifié vers la page de connexion de l’application.</span><span class="sxs-lookup"><span data-stu-id="329b2-688">Redirects an unauthenticated user to the app's Login page.</span></span>
* <span data-ttu-id="329b2-689">Retourne le contenu d’un utilisateur authentifié.</span><span class="sxs-lookup"><span data-stu-id="329b2-689">Returns content for an authenticated user.</span></span>

<span data-ttu-id="329b2-690">Dans St, la `/SecurePage` page utilise une convention [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) pour appliquer un [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) à la page.</span><span class="sxs-lookup"><span data-stu-id="329b2-690">In the SUT, the `/SecurePage` page uses an [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) convention to apply an [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) to the page.</span></span> <span data-ttu-id="329b2-691">Pour plus d’informations, consultez [ Razor conventions d’autorisation des pages](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).</span><span class="sxs-lookup"><span data-stu-id="329b2-691">For more information, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

<span data-ttu-id="329b2-692">Dans le `Get_SecurePageRedirectsAnUnauthenticatedUser` test, un [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) est défini pour interdire les redirections en affectant à [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) la valeur `false` :</span><span class="sxs-lookup"><span data-stu-id="329b2-692">In the `Get_SecurePageRedirectsAnUnauthenticatedUser` test, a [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) is set to disallow redirects by setting [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) to `false`:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet2)]

<span data-ttu-id="329b2-693">En interautorisant le client à suivre la redirection, vous pouvez effectuer les vérifications suivantes :</span><span class="sxs-lookup"><span data-stu-id="329b2-693">By disallowing the client to follow the redirect, the following checks can be made:</span></span>

* <span data-ttu-id="329b2-694">Le code d’état retourné par le St peut être vérifié par rapport au résultat [HttpStatusCode. Redirect](/dotnet/api/system.net.httpstatuscode) attendu, pas le code d’état final après la redirection vers la page de connexion, qui serait [HttpStatusCode. OK](/dotnet/api/system.net.httpstatuscode).</span><span class="sxs-lookup"><span data-stu-id="329b2-694">The status code returned by the SUT can be checked against the expected [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode) result, not the final status code after the redirect to the Login page, which would be [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode).</span></span>
* <span data-ttu-id="329b2-695">La `Location` valeur d’en-tête dans les en-têtes de réponse est vérifiée pour confirmer qu’elle commence par `http://localhost/Identity/Account/Login` , et non par la réponse de page de connexion finale, où l' `Location` en-tête n’est pas présent.</span><span class="sxs-lookup"><span data-stu-id="329b2-695">The `Location` header value in the response headers is checked to confirm that it starts with `http://localhost/Identity/Account/Login`, not the final Login page response, where the `Location` header wouldn't be present.</span></span>

<span data-ttu-id="329b2-696">L’application de test peut simuler un <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> dans [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) afin de tester les aspects de l’authentification et de l’autorisation.</span><span class="sxs-lookup"><span data-stu-id="329b2-696">The test app can mock an <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> in [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) in order to test aspects of authentication and authorization.</span></span> <span data-ttu-id="329b2-697">Un scénario minimal renvoie un [AuthenticateResult. Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*):</span><span class="sxs-lookup"><span data-stu-id="329b2-697">A minimal scenario returns an [AuthenticateResult.Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*):</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet4&highlight=11-18)]

<span data-ttu-id="329b2-698">La `TestAuthHandler` méthode est appelée pour authentifier un utilisateur lorsque le schéma d’authentification est défini sur `Test` où `AddAuthentication` est inscrit pour `ConfigureTestServices` :</span><span class="sxs-lookup"><span data-stu-id="329b2-698">The `TestAuthHandler` is called to authenticate a user when the authentication scheme is set to `Test` where `AddAuthentication` is registered for `ConfigureTestServices`:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet3&highlight=7-12)]

<span data-ttu-id="329b2-699">Pour plus d’informations sur `WebApplicationFactoryClientOptions` , consultez la section [Options du client](#client-options) .</span><span class="sxs-lookup"><span data-stu-id="329b2-699">For more information on `WebApplicationFactoryClientOptions`, see the [Client options](#client-options) section.</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="329b2-700">Définir l’environnement</span><span class="sxs-lookup"><span data-stu-id="329b2-700">Set the environment</span></span>

<span data-ttu-id="329b2-701">Par défaut, l’environnement de l’hôte et de l’application de St est configuré pour utiliser l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="329b2-701">By default, the SUT's host and app environment is configured to use the Development environment.</span></span> <span data-ttu-id="329b2-702">Pour remplacer l’environnement de St :</span><span class="sxs-lookup"><span data-stu-id="329b2-702">To override the SUT's environment:</span></span>

* <span data-ttu-id="329b2-703">Définissez la `ASPNETCORE_ENVIRONMENT` variable d’environnement (par exemple,, `Staging` `Production` ou une autre valeur personnalisée, telle que `Testing` ).</span><span class="sxs-lookup"><span data-stu-id="329b2-703">Set the `ASPNETCORE_ENVIRONMENT` environment variable (for example, `Staging`, `Production`, or other custom value, such as `Testing`).</span></span>
* <span data-ttu-id="329b2-704">Remplacez `CreateWebHostBuilder` dans l’application de test pour lire la `ASPNETCORE_ENVIRONMENT` variable d’environnement.</span><span class="sxs-lookup"><span data-stu-id="329b2-704">Override `CreateWebHostBuilder` in the test app to read the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span>

```csharp
public class CustomWebApplicationFactory<TStartup> 
    : WebApplicationFactory<TStartup> where TStartup: class
{
    protected override IWebHostBuilder CreateWebHostBuilder()
    {
        return base.CreateWebHostBuilder()
            .UseEnvironment(
                Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT"));
    }

    ...
}
```

<span data-ttu-id="329b2-705">L’environnement peut également être défini directement sur le générateur de l’hôte dans un personnalisé <xref:Microsoft.AspNetCore.Mvc.Testing.WebApplicationFactory%601> :</span><span class="sxs-lookup"><span data-stu-id="329b2-705">The environment can also be set directly on the host builder in a custom <xref:Microsoft.AspNetCore.Mvc.Testing.WebApplicationFactory%601>:</span></span>

```csharp
public class CustomWebApplicationFactory<TStartup> 
    : WebApplicationFactory<TStartup> where TStartup: class
{
    protected override void ConfigureWebHost(IWebHostBuilder builder)
    {
        builder.UseEnvironment(
            Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT"));
    
        ...
    }

    ...
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a><span data-ttu-id="329b2-706">Comment l’infrastructure de test déduit le chemin racine du contenu de l’application</span><span class="sxs-lookup"><span data-stu-id="329b2-706">How the test infrastructure infers the app content root path</span></span>

<span data-ttu-id="329b2-707">Le `WebApplicationFactory` constructeur déduit le chemin d’accès [racine du contenu](xref:fundamentals/index#content-root) de l’application en recherchant un [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) sur l’assembly contenant les tests d’intégration avec une clé égale à l' `TEntryPoint` assembly `System.Reflection.Assembly.FullName` .</span><span class="sxs-lookup"><span data-stu-id="329b2-707">The `WebApplicationFactory` constructor infers the app [content root](xref:fundamentals/index#content-root) path by searching for a [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) on the assembly containing the integration tests with a key equal to the `TEntryPoint` assembly `System.Reflection.Assembly.FullName`.</span></span> <span data-ttu-id="329b2-708">Si un attribut avec la clé correcte est introuvable, revient `WebApplicationFactory` à la recherche d’un fichier solution (*. sln*) et ajoute le `TEntryPoint` nom de l’assembly au répertoire de la solution.</span><span class="sxs-lookup"><span data-stu-id="329b2-708">In case an attribute with the correct key isn't found, `WebApplicationFactory` falls back to searching for a solution file (*.sln*) and appends the `TEntryPoint` assembly name to the solution directory.</span></span> <span data-ttu-id="329b2-709">Le répertoire racine de l’application (chemin racine du contenu) est utilisé pour découvrir les affichages et les fichiers de contenu.</span><span class="sxs-lookup"><span data-stu-id="329b2-709">The app root directory (the content root path) is used to discover views and content files.</span></span>

## <a name="disable-shadow-copying"></a><span data-ttu-id="329b2-710">Désactiver les clichés instantanés</span><span class="sxs-lookup"><span data-stu-id="329b2-710">Disable shadow copying</span></span>

<span data-ttu-id="329b2-711">Les clichés instantanés provoquent l’exécution des tests dans un répertoire différent de celui du répertoire de sortie.</span><span class="sxs-lookup"><span data-stu-id="329b2-711">Shadow copying causes the tests to execute in a different directory than the output directory.</span></span> <span data-ttu-id="329b2-712">Pour que les tests fonctionnent correctement, les clichés instantanés doivent être désactivés.</span><span class="sxs-lookup"><span data-stu-id="329b2-712">For tests to work properly, shadow copying must be disabled.</span></span> <span data-ttu-id="329b2-713">L' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) utilise xUnit et désactive les clichés instantanés pour xUnit en incluant un fichier *xUnit. Runner. JSON* avec le paramètre de configuration correct.</span><span class="sxs-lookup"><span data-stu-id="329b2-713">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) uses xUnit and disables shadow copying for xUnit by including an *xunit.runner.json* file with the correct configuration setting.</span></span> <span data-ttu-id="329b2-714">Pour plus d’informations, consultez [configuration de xUnit avec JSON](https://xunit.github.io/docs/configuring-with-json.html).</span><span class="sxs-lookup"><span data-stu-id="329b2-714">For more information, see [Configuring xUnit with JSON](https://xunit.github.io/docs/configuring-with-json.html).</span></span>

<span data-ttu-id="329b2-715">Ajoutez le fichier *xUnit. Runner. JSON* à la racine du projet de test avec le contenu suivant :</span><span class="sxs-lookup"><span data-stu-id="329b2-715">Add the *xunit.runner.json* file to root of the test project with the following content:</span></span>

```json
{
  "shadowCopy": false
}
```

<span data-ttu-id="329b2-716">Si vous utilisez Visual Studio, définissez la propriété **copier dans le répertoire de sortie** du fichier sur **toujours copier**.</span><span class="sxs-lookup"><span data-stu-id="329b2-716">If using Visual Studio, set the file's **Copy to Output Directory** property to **Copy always**.</span></span> <span data-ttu-id="329b2-717">Si vous n’utilisez pas Visual Studio, ajoutez une `Content` cible au fichier projet de l’application de test :</span><span class="sxs-lookup"><span data-stu-id="329b2-717">If not using Visual Studio, add a `Content` target to the test app's project file:</span></span>

```xml
<ItemGroup>
  <Content Update="xunit.runner.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
  </Content>
</ItemGroup>
```

## <a name="disposal-of-objects"></a><span data-ttu-id="329b2-718">Suppression d’objets</span><span class="sxs-lookup"><span data-stu-id="329b2-718">Disposal of objects</span></span>

<span data-ttu-id="329b2-719">Après l’exécution des tests de l' `IClassFixture` implémentation, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) et [httpclient](/dotnet/api/system.net.http.httpclient) sont supprimés quand xUnit supprime le [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1).</span><span class="sxs-lookup"><span data-stu-id="329b2-719">After the tests of the `IClassFixture` implementation are executed, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) and [HttpClient](/dotnet/api/system.net.http.httpclient) are disposed when xUnit disposes of the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1).</span></span> <span data-ttu-id="329b2-720">Si les objets instanciés par le développeur nécessitent une suppression, supprimez-les dans l' `IClassFixture` implémentation.</span><span class="sxs-lookup"><span data-stu-id="329b2-720">If objects instantiated by the developer require disposal, dispose of them in the `IClassFixture` implementation.</span></span> <span data-ttu-id="329b2-721">Pour plus d’informations, consultez [implémentation d’une méthode dispose](/dotnet/standard/garbage-collection/implementing-dispose).</span><span class="sxs-lookup"><span data-stu-id="329b2-721">For more information, see [Implementing a Dispose method](/dotnet/standard/garbage-collection/implementing-dispose).</span></span>

## <a name="integration-tests-sample"></a><span data-ttu-id="329b2-722">Exemple de tests d’intégration</span><span class="sxs-lookup"><span data-stu-id="329b2-722">Integration tests sample</span></span>

<span data-ttu-id="329b2-723">L' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) se compose de deux applications :</span><span class="sxs-lookup"><span data-stu-id="329b2-723">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is composed of two apps:</span></span>

| <span data-ttu-id="329b2-724">Application</span><span class="sxs-lookup"><span data-stu-id="329b2-724">App</span></span> | <span data-ttu-id="329b2-725">Répertoire du projet</span><span class="sxs-lookup"><span data-stu-id="329b2-725">Project directory</span></span> | <span data-ttu-id="329b2-726">Description</span><span class="sxs-lookup"><span data-stu-id="329b2-726">Description</span></span> |
| --- | ---
<span data-ttu-id="329b2-727">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="329b2-727">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="329b2-728">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="329b2-728">'Blazor'</span></span>
- <span data-ttu-id="329b2-729">'Identity'</span><span class="sxs-lookup"><span data-stu-id="329b2-729">'Identity'</span></span>
- <span data-ttu-id="329b2-730">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="329b2-730">'Let's Encrypt'</span></span>
- <span data-ttu-id="329b2-731">'Razor'</span><span class="sxs-lookup"><span data-stu-id="329b2-731">'Razor'</span></span>
- <span data-ttu-id="329b2-732">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="329b2-732">'SignalR' uid:</span></span> 

-
<span data-ttu-id="329b2-733">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="329b2-733">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="329b2-734">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="329b2-734">'Blazor'</span></span>
- <span data-ttu-id="329b2-735">'Identity'</span><span class="sxs-lookup"><span data-stu-id="329b2-735">'Identity'</span></span>
- <span data-ttu-id="329b2-736">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="329b2-736">'Let's Encrypt'</span></span>
- <span data-ttu-id="329b2-737">'Razor'</span><span class="sxs-lookup"><span data-stu-id="329b2-737">'Razor'</span></span>
- <span data-ttu-id="329b2-738">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="329b2-738">'SignalR' uid:</span></span> 

-
<span data-ttu-id="329b2-739">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="329b2-739">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="329b2-740">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="329b2-740">'Blazor'</span></span>
- <span data-ttu-id="329b2-741">'Identity'</span><span class="sxs-lookup"><span data-stu-id="329b2-741">'Identity'</span></span>
- <span data-ttu-id="329b2-742">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="329b2-742">'Let's Encrypt'</span></span>
- <span data-ttu-id="329b2-743">'Razor'</span><span class="sxs-lookup"><span data-stu-id="329b2-743">'Razor'</span></span>
- <span data-ttu-id="329b2-744">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="329b2-744">'SignalR' uid:</span></span> 

-
<span data-ttu-id="329b2-745">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="329b2-745">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="329b2-746">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="329b2-746">'Blazor'</span></span>
- <span data-ttu-id="329b2-747">'Identity'</span><span class="sxs-lookup"><span data-stu-id="329b2-747">'Identity'</span></span>
- <span data-ttu-id="329b2-748">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="329b2-748">'Let's Encrypt'</span></span>
- <span data-ttu-id="329b2-749">'Razor'</span><span class="sxs-lookup"><span data-stu-id="329b2-749">'Razor'</span></span>
- <span data-ttu-id="329b2-750">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="329b2-750">'SignalR' uid:</span></span> 

-
<span data-ttu-id="329b2-751">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="329b2-751">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="329b2-752">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="329b2-752">'Blazor'</span></span>
- <span data-ttu-id="329b2-753">'Identity'</span><span class="sxs-lookup"><span data-stu-id="329b2-753">'Identity'</span></span>
- <span data-ttu-id="329b2-754">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="329b2-754">'Let's Encrypt'</span></span>
- <span data-ttu-id="329b2-755">'Razor'</span><span class="sxs-lookup"><span data-stu-id="329b2-755">'Razor'</span></span>
- <span data-ttu-id="329b2-756">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="329b2-756">'SignalR' uid:</span></span> 

-
<span data-ttu-id="329b2-757">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="329b2-757">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="329b2-758">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="329b2-758">'Blazor'</span></span>
- <span data-ttu-id="329b2-759">'Identity'</span><span class="sxs-lookup"><span data-stu-id="329b2-759">'Identity'</span></span>
- <span data-ttu-id="329b2-760">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="329b2-760">'Let's Encrypt'</span></span>
- <span data-ttu-id="329b2-761">'Razor'</span><span class="sxs-lookup"><span data-stu-id="329b2-761">'Razor'</span></span>
- <span data-ttu-id="329b2-762">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="329b2-762">'SignalR' uid:</span></span> 

<span data-ttu-id="329b2-763">--------- | titre de--- : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="329b2-763">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="329b2-764">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="329b2-764">'Blazor'</span></span>
- <span data-ttu-id="329b2-765">'Identity'</span><span class="sxs-lookup"><span data-stu-id="329b2-765">'Identity'</span></span>
- <span data-ttu-id="329b2-766">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="329b2-766">'Let's Encrypt'</span></span>
- <span data-ttu-id="329b2-767">'Razor'</span><span class="sxs-lookup"><span data-stu-id="329b2-767">'Razor'</span></span>
- <span data-ttu-id="329b2-768">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="329b2-768">'SignalR' uid:</span></span> 

-
<span data-ttu-id="329b2-769">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="329b2-769">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="329b2-770">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="329b2-770">'Blazor'</span></span>
- <span data-ttu-id="329b2-771">'Identity'</span><span class="sxs-lookup"><span data-stu-id="329b2-771">'Identity'</span></span>
- <span data-ttu-id="329b2-772">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="329b2-772">'Let's Encrypt'</span></span>
- <span data-ttu-id="329b2-773">'Razor'</span><span class="sxs-lookup"><span data-stu-id="329b2-773">'Razor'</span></span>
- <span data-ttu-id="329b2-774">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="329b2-774">'SignalR' uid:</span></span> 

-
<span data-ttu-id="329b2-775">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="329b2-775">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="329b2-776">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="329b2-776">'Blazor'</span></span>
- <span data-ttu-id="329b2-777">'Identity'</span><span class="sxs-lookup"><span data-stu-id="329b2-777">'Identity'</span></span>
- <span data-ttu-id="329b2-778">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="329b2-778">'Let's Encrypt'</span></span>
- <span data-ttu-id="329b2-779">'Razor'</span><span class="sxs-lookup"><span data-stu-id="329b2-779">'Razor'</span></span>
- <span data-ttu-id="329b2-780">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="329b2-780">'SignalR' uid:</span></span> 

<span data-ttu-id="329b2-781">------ | | Application message (St) | *src/RazorPagesProject* | Permet à l’utilisateur d’ajouter, de supprimer, de supprimer et d’analyser des messages.</span><span class="sxs-lookup"><span data-stu-id="329b2-781">------ | | Message app (the SUT) | *src/RazorPagesProject* | Allows a user to add, delete one, delete all, and analyze messages.</span></span> <span data-ttu-id="329b2-782">| | Application de test | *tests/RazorPagesProject. tests* | Utilisé pour tester l’intégration du St.</span><span class="sxs-lookup"><span data-stu-id="329b2-782">| | Test app | *tests/RazorPagesProject.Tests* | Used to integration test the SUT.</span></span> |

<span data-ttu-id="329b2-783">Les tests peuvent être exécutés à l’aide des fonctionnalités de test intégrées d’un environnement de développement intégré (IDE), telles que [Visual Studio](https://visualstudio.microsoft.com).</span><span class="sxs-lookup"><span data-stu-id="329b2-783">The tests can be run using the built-in test features of an IDE, such as [Visual Studio](https://visualstudio.microsoft.com).</span></span> <span data-ttu-id="329b2-784">Si vous utilisez [Visual Studio code](https://code.visualstudio.com/) ou la ligne de commande, exécutez la commande suivante à l’invite de commandes dans le répertoire *tests/RazorPagesProject. tests* :</span><span class="sxs-lookup"><span data-stu-id="329b2-784">If using [Visual Studio Code](https://code.visualstudio.com/) or the command line, execute the following command at a command prompt in the *tests/RazorPagesProject.Tests* directory:</span></span>

```dotnetcli
dotnet test
```

### <a name="message-app-sut-organization"></a><span data-ttu-id="329b2-785">Organisation message App (St)</span><span class="sxs-lookup"><span data-stu-id="329b2-785">Message app (SUT) organization</span></span>

<span data-ttu-id="329b2-786">Le St est un Razor système de messagerie des pages avec les caractéristiques suivantes :</span><span class="sxs-lookup"><span data-stu-id="329b2-786">The SUT is a Razor Pages message system with the following characteristics:</span></span>

* <span data-ttu-id="329b2-787">La page d’index de l’application (*pages/index. cshtml* et *pages/index. cshtml. cs*) fournit une interface utilisateur et des méthodes de modèle de page pour contrôler l’ajout, la suppression et l’analyse de messages (mots moyens par message).</span><span class="sxs-lookup"><span data-stu-id="329b2-787">The Index page of the app (*Pages/Index.cshtml* and *Pages/Index.cshtml.cs*) provides a UI and page model methods to control the addition, deletion, and analysis of messages (average words per message).</span></span>
* <span data-ttu-id="329b2-788">Un message est décrit par la `Message` classe (*Data/message. cs*) avec deux propriétés : `Id` (Key) et `Text` (message).</span><span class="sxs-lookup"><span data-stu-id="329b2-788">A message is described by the `Message` class (*Data/Message.cs*) with two properties: `Id` (key) and `Text` (message).</span></span> <span data-ttu-id="329b2-789">La `Text` propriété est obligatoire et limitée à 200 caractères.</span><span class="sxs-lookup"><span data-stu-id="329b2-789">The `Text` property is required and limited to 200 characters.</span></span>
* <span data-ttu-id="329b2-790">Les messages sont stockés à l’aide [de la base de données en mémoire de Entity Framework](/ef/core/providers/in-memory/)&#8224;.</span><span class="sxs-lookup"><span data-stu-id="329b2-790">Messages are stored using [Entity Framework's in-memory database](/ef/core/providers/in-memory/)&#8224;.</span></span>
* <span data-ttu-id="329b2-791">L’application contient une couche d’accès aux données (DAL) dans sa classe de contexte de base de données, `AppDbContext` (*Data/AppDbContext. cs*).</span><span class="sxs-lookup"><span data-stu-id="329b2-791">The app contains a data access layer (DAL) in its database context class, `AppDbContext` (*Data/AppDbContext.cs*).</span></span>
* <span data-ttu-id="329b2-792">Si la base de données est vide au démarrage de l’application, la Banque de messages est initialisée avec trois messages.</span><span class="sxs-lookup"><span data-stu-id="329b2-792">If the database is empty on app startup, the message store is initialized with three messages.</span></span>
* <span data-ttu-id="329b2-793">L’application comprend un `/SecurePage` qui n’est accessible qu’à un utilisateur authentifié.</span><span class="sxs-lookup"><span data-stu-id="329b2-793">The app includes a `/SecurePage` that can only be accessed by an authenticated user.</span></span>

<span data-ttu-id="329b2-794">&#8224;la rubrique EF [test with InMemory](/ef/core/miscellaneous/testing/in-memory), explique comment utiliser une base de données en mémoire pour les tests avec MSTest.</span><span class="sxs-lookup"><span data-stu-id="329b2-794">&#8224;The EF topic, [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), explains how to use an in-memory database for tests with MSTest.</span></span> <span data-ttu-id="329b2-795">Cette rubrique utilise l’infrastructure de test [xUnit](https://xunit.github.io/) .</span><span class="sxs-lookup"><span data-stu-id="329b2-795">This topic uses the [xUnit](https://xunit.github.io/) test framework.</span></span> <span data-ttu-id="329b2-796">Les concepts de test et les implémentations de test dans différents frameworks de test sont similaires, mais pas identiques.</span><span class="sxs-lookup"><span data-stu-id="329b2-796">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span>

<span data-ttu-id="329b2-797">Bien que l’application n’utilise pas le modèle de référentiel et ne soit pas un exemple efficace de [modèle d’unité de travail (UOW)](https://martinfowler.com/eaaCatalog/unitOfWork.html), les Razor pages prennent en charge ces modèles de développement.</span><span class="sxs-lookup"><span data-stu-id="329b2-797">Although the app doesn't use the repository pattern and isn't an effective example of the [Unit of Work (UoW) pattern](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages supports these patterns of development.</span></span> <span data-ttu-id="329b2-798">Pour plus d’informations, consultez [conception de la couche de persistance de l’infrastructure](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) et [logique du contrôleur de test](/aspnet/core/mvc/controllers/testing) (l’exemple implémente le modèle de référentiel).</span><span class="sxs-lookup"><span data-stu-id="329b2-798">For more information, see [Designing the infrastructure persistence layer](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) and [Test controller logic](/aspnet/core/mvc/controllers/testing) (the sample implements the repository pattern).</span></span>

### <a name="test-app-organization"></a><span data-ttu-id="329b2-799">Organisation des applications de test</span><span class="sxs-lookup"><span data-stu-id="329b2-799">Test app organization</span></span>

<span data-ttu-id="329b2-800">L’application de test est une application console dans le répertoire *tests/RazorPagesProject. tests* .</span><span class="sxs-lookup"><span data-stu-id="329b2-800">The test app is a console app inside the *tests/RazorPagesProject.Tests* directory.</span></span>

| <span data-ttu-id="329b2-801">Tester le répertoire de l’application</span><span class="sxs-lookup"><span data-stu-id="329b2-801">Test app directory</span></span> | <span data-ttu-id="329b2-802">Description</span><span class="sxs-lookup"><span data-stu-id="329b2-802">Description</span></span> |
| ---
<span data-ttu-id="329b2-803">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="329b2-803">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="329b2-804">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="329b2-804">'Blazor'</span></span>
- <span data-ttu-id="329b2-805">'Identity'</span><span class="sxs-lookup"><span data-stu-id="329b2-805">'Identity'</span></span>
- <span data-ttu-id="329b2-806">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="329b2-806">'Let's Encrypt'</span></span>
- <span data-ttu-id="329b2-807">'Razor'</span><span class="sxs-lookup"><span data-stu-id="329b2-807">'Razor'</span></span>
- <span data-ttu-id="329b2-808">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="329b2-808">'SignalR' uid:</span></span> 

-
<span data-ttu-id="329b2-809">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="329b2-809">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="329b2-810">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="329b2-810">'Blazor'</span></span>
- <span data-ttu-id="329b2-811">'Identity'</span><span class="sxs-lookup"><span data-stu-id="329b2-811">'Identity'</span></span>
- <span data-ttu-id="329b2-812">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="329b2-812">'Let's Encrypt'</span></span>
- <span data-ttu-id="329b2-813">'Razor'</span><span class="sxs-lookup"><span data-stu-id="329b2-813">'Razor'</span></span>
- <span data-ttu-id="329b2-814">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="329b2-814">'SignalR' uid:</span></span> 

-
<span data-ttu-id="329b2-815">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="329b2-815">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="329b2-816">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="329b2-816">'Blazor'</span></span>
- <span data-ttu-id="329b2-817">'Identity'</span><span class="sxs-lookup"><span data-stu-id="329b2-817">'Identity'</span></span>
- <span data-ttu-id="329b2-818">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="329b2-818">'Let's Encrypt'</span></span>
- <span data-ttu-id="329b2-819">'Razor'</span><span class="sxs-lookup"><span data-stu-id="329b2-819">'Razor'</span></span>
- <span data-ttu-id="329b2-820">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="329b2-820">'SignalR' uid:</span></span> 

-
<span data-ttu-id="329b2-821">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="329b2-821">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="329b2-822">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="329b2-822">'Blazor'</span></span>
- <span data-ttu-id="329b2-823">'Identity'</span><span class="sxs-lookup"><span data-stu-id="329b2-823">'Identity'</span></span>
- <span data-ttu-id="329b2-824">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="329b2-824">'Let's Encrypt'</span></span>
- <span data-ttu-id="329b2-825">'Razor'</span><span class="sxs-lookup"><span data-stu-id="329b2-825">'Razor'</span></span>
- <span data-ttu-id="329b2-826">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="329b2-826">'SignalR' uid:</span></span> 

-
<span data-ttu-id="329b2-827">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="329b2-827">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="329b2-828">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="329b2-828">'Blazor'</span></span>
- <span data-ttu-id="329b2-829">'Identity'</span><span class="sxs-lookup"><span data-stu-id="329b2-829">'Identity'</span></span>
- <span data-ttu-id="329b2-830">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="329b2-830">'Let's Encrypt'</span></span>
- <span data-ttu-id="329b2-831">'Razor'</span><span class="sxs-lookup"><span data-stu-id="329b2-831">'Razor'</span></span>
- <span data-ttu-id="329b2-832">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="329b2-832">'SignalR' uid:</span></span> 

-
<span data-ttu-id="329b2-833">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="329b2-833">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="329b2-834">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="329b2-834">'Blazor'</span></span>
- <span data-ttu-id="329b2-835">'Identity'</span><span class="sxs-lookup"><span data-stu-id="329b2-835">'Identity'</span></span>
- <span data-ttu-id="329b2-836">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="329b2-836">'Let's Encrypt'</span></span>
- <span data-ttu-id="329b2-837">'Razor'</span><span class="sxs-lookup"><span data-stu-id="329b2-837">'Razor'</span></span>
- <span data-ttu-id="329b2-838">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="329b2-838">'SignalR' uid:</span></span> 

-
<span data-ttu-id="329b2-839">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="329b2-839">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="329b2-840">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="329b2-840">'Blazor'</span></span>
- <span data-ttu-id="329b2-841">'Identity'</span><span class="sxs-lookup"><span data-stu-id="329b2-841">'Identity'</span></span>
- <span data-ttu-id="329b2-842">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="329b2-842">'Let's Encrypt'</span></span>
- <span data-ttu-id="329b2-843">'Razor'</span><span class="sxs-lookup"><span data-stu-id="329b2-843">'Razor'</span></span>
- <span data-ttu-id="329b2-844">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="329b2-844">'SignalR' uid:</span></span> 

<span data-ttu-id="329b2-845">--------- | titre de--- : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="329b2-845">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="329b2-846">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="329b2-846">'Blazor'</span></span>
- <span data-ttu-id="329b2-847">'Identity'</span><span class="sxs-lookup"><span data-stu-id="329b2-847">'Identity'</span></span>
- <span data-ttu-id="329b2-848">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="329b2-848">'Let's Encrypt'</span></span>
- <span data-ttu-id="329b2-849">'Razor'</span><span class="sxs-lookup"><span data-stu-id="329b2-849">'Razor'</span></span>
- <span data-ttu-id="329b2-850">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="329b2-850">'SignalR' uid:</span></span> 

-
<span data-ttu-id="329b2-851">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="329b2-851">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="329b2-852">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="329b2-852">'Blazor'</span></span>
- <span data-ttu-id="329b2-853">'Identity'</span><span class="sxs-lookup"><span data-stu-id="329b2-853">'Identity'</span></span>
- <span data-ttu-id="329b2-854">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="329b2-854">'Let's Encrypt'</span></span>
- <span data-ttu-id="329b2-855">'Razor'</span><span class="sxs-lookup"><span data-stu-id="329b2-855">'Razor'</span></span>
- <span data-ttu-id="329b2-856">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="329b2-856">'SignalR' uid:</span></span> 

-
<span data-ttu-id="329b2-857">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="329b2-857">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="329b2-858">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="329b2-858">'Blazor'</span></span>
- <span data-ttu-id="329b2-859">'Identity'</span><span class="sxs-lookup"><span data-stu-id="329b2-859">'Identity'</span></span>
- <span data-ttu-id="329b2-860">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="329b2-860">'Let's Encrypt'</span></span>
- <span data-ttu-id="329b2-861">'Razor'</span><span class="sxs-lookup"><span data-stu-id="329b2-861">'Razor'</span></span>
- <span data-ttu-id="329b2-862">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="329b2-862">'SignalR' uid:</span></span> 

<span data-ttu-id="329b2-863">------ | | *AuthTests* | Contient des méthodes de test pour :</span><span class="sxs-lookup"><span data-stu-id="329b2-863">------ | | *AuthTests* | Contains test methods for:</span></span><ul><li><span data-ttu-id="329b2-864">Accès à une page sécurisée par un utilisateur non authentifié.</span><span class="sxs-lookup"><span data-stu-id="329b2-864">Accessing a secure page by an unauthenticated user.</span></span></li><li><span data-ttu-id="329b2-865">Accès à une page sécurisée par un utilisateur authentifié avec un simulacre <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> .</span><span class="sxs-lookup"><span data-stu-id="329b2-865">Accessing a secure page by an authenticated user with a mock <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>.</span></span></li><li><span data-ttu-id="329b2-866">Obtention d’un profil utilisateur GitHub et vérification de la connexion de l’utilisateur du profil.</span><span class="sxs-lookup"><span data-stu-id="329b2-866">Obtaining a GitHub user profile and checking the profile's user login.</span></span></li></ul> <span data-ttu-id="329b2-867">| | *BasicTests* | Contient une méthode de test pour le routage et le type de contenu.</span><span class="sxs-lookup"><span data-stu-id="329b2-867">| | *BasicTests* | Contains a test method for routing and content type.</span></span> <span data-ttu-id="329b2-868">| | *IntegrationTests* | Contient les tests d’intégration de la page d’index à l’aide de la `WebApplicationFactory` classe personnalisée.</span><span class="sxs-lookup"><span data-stu-id="329b2-868">| | *IntegrationTests* | Contains the integration tests for the Index page using custom `WebApplicationFactory` class.</span></span> <span data-ttu-id="329b2-869">| | *Programmes d’assistance/utilitaires* | </span><span class="sxs-lookup"><span data-stu-id="329b2-869">| | *Helpers/Utilities* | </span></span><ul><li><span data-ttu-id="329b2-870">*Utilities.cs* contient la `InitializeDbForTests` méthode utilisée pour amorcer la base de données avec des données de test.</span><span class="sxs-lookup"><span data-stu-id="329b2-870">*Utilities.cs* contains the `InitializeDbForTests` method used to seed the database with test data.</span></span></li><li><span data-ttu-id="329b2-871">*HtmlHelpers.cs* fournit une méthode pour retourner un AngleSharp `IHtmlDocument` pour une utilisation par les méthodes de test.</span><span class="sxs-lookup"><span data-stu-id="329b2-871">*HtmlHelpers.cs* provides a method to return an AngleSharp `IHtmlDocument` for use by the test methods.</span></span></li><li><span data-ttu-id="329b2-872">*HttpClientExtensions.cs* fournissent des surcharges pour `SendAsync` Envoyer des demandes au St.</span><span class="sxs-lookup"><span data-stu-id="329b2-872">*HttpClientExtensions.cs* provide overloads for `SendAsync` to submit requests to the SUT.</span></span></li></ul> |

<span data-ttu-id="329b2-873">L’infrastructure de test est [xUnit](https://xunit.github.io/).</span><span class="sxs-lookup"><span data-stu-id="329b2-873">The test framework is [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="329b2-874">Les tests d’intégration sont effectués à l’aide de [Microsoft. AspNetCore. TestHost](/dotnet/api/microsoft.aspnetcore.testhost), qui comprend le [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span><span class="sxs-lookup"><span data-stu-id="329b2-874">Integration tests are conducted using the [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost), which includes the [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span></span> <span data-ttu-id="329b2-875">Étant donné que le package [Microsoft. AspNetCore. Mvc. testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) est utilisé pour configurer l’hôte de test et le serveur de test, les `TestHost` `TestServer` packages et ne nécessitent pas de références de package directes dans le fichier projet ou la configuration de développeur de l’application de test dans l’application de test.</span><span class="sxs-lookup"><span data-stu-id="329b2-875">Because the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package is used to configure the test host and test server, the `TestHost` and `TestServer` packages don't require direct package references in the test app's project file or developer configuration in the test app.</span></span>

<span data-ttu-id="329b2-876">**Amorçage de la base de données à des fins de test**</span><span class="sxs-lookup"><span data-stu-id="329b2-876">**Seeding the database for testing**</span></span>

<span data-ttu-id="329b2-877">Les tests d’intégration nécessitent généralement un petit jeu de données dans la base de données avant l’exécution du test.</span><span class="sxs-lookup"><span data-stu-id="329b2-877">Integration tests usually require a small dataset in the database prior to the test execution.</span></span> <span data-ttu-id="329b2-878">Par exemple, un test Delete requiert la suppression d’un enregistrement de base de données, de sorte que la base de données doit avoir au moins un enregistrement pour que la demande Delete aboutisse.</span><span class="sxs-lookup"><span data-stu-id="329b2-878">For example, a delete test calls for a database record deletion, so the database must have at least one record for the delete request to succeed.</span></span>

<span data-ttu-id="329b2-879">L’exemple d’application amorce la base de données avec trois messages dans *Utilities.cs* que les tests peuvent utiliser lorsqu’ils exécutent :</span><span class="sxs-lookup"><span data-stu-id="329b2-879">The sample app seeds the database with three messages in *Utilities.cs* that tests can use when they execute:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="329b2-880">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="329b2-880">Additional resources</span></span>

* [<span data-ttu-id="329b2-881">Tests unitaires</span><span class="sxs-lookup"><span data-stu-id="329b2-881">Unit tests</span></span>](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:test/razor-pages-tests>
* <xref:fundamentals/middleware/index>
* <xref:mvc/controllers/testing>
