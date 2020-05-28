---
<span data-ttu-id="2ad8d-101">titre : Auteur : Description : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="2ad8d-101">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2ad8d-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2ad8d-102">'Blazor'</span></span>
- <span data-ttu-id="2ad8d-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2ad8d-103">'Identity'</span></span>
- <span data-ttu-id="2ad8d-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2ad8d-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="2ad8d-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2ad8d-105">'Razor'</span></span>
- <span data-ttu-id="2ad8d-106">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="2ad8d-106">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-loadstress-testing"></a><span data-ttu-id="2ad8d-107">ASP.NET Core le test de charge/stress</span><span class="sxs-lookup"><span data-stu-id="2ad8d-107">ASP.NET Core load/stress testing</span></span>

<span data-ttu-id="2ad8d-108">Les tests de charge et les tests de stress sont importants pour garantir qu’une application Web est performante et évolutive.</span><span class="sxs-lookup"><span data-stu-id="2ad8d-108">Load testing and stress testing are important to ensure a web app is performant and scalable.</span></span> <span data-ttu-id="2ad8d-109">Leurs objectifs sont différents même s’ils partagent souvent des tests similaires.</span><span class="sxs-lookup"><span data-stu-id="2ad8d-109">Their goals are different even though they often share similar tests.</span></span>

<span data-ttu-id="2ad8d-110">**Tests de charge**: Testez si l’application peut gérer une charge d’utilisateurs spécifiée pour un certain scénario tout en répondant toujours à l’objectif de réponse.</span><span class="sxs-lookup"><span data-stu-id="2ad8d-110">**Load tests**: Test whether the app can handle a specified load of users for a certain scenario while still satisfying the response goal.</span></span> <span data-ttu-id="2ad8d-111">L’application est exécutée dans des conditions normales.</span><span class="sxs-lookup"><span data-stu-id="2ad8d-111">The app is run under normal conditions.</span></span>

<span data-ttu-id="2ad8d-112">**Tests de stress**: stabilité de l’application de test lors de l’exécution dans des conditions extrêmes, souvent pendant une longue période de temps.</span><span class="sxs-lookup"><span data-stu-id="2ad8d-112">**Stress tests**: Test app stability when running under extreme conditions, often for a long period of time.</span></span> <span data-ttu-id="2ad8d-113">Les tests mettent en place une charge utilisateur élevée, des pics ou une augmentation progressive de la charge, sur l’application, ou ils limitent les ressources informatiques de l’application.</span><span class="sxs-lookup"><span data-stu-id="2ad8d-113">The tests place high user load, either spikes or gradually increasing load, on the app, or they limit the app's computing resources.</span></span>

<span data-ttu-id="2ad8d-114">Les tests de contrainte déterminent si une application en contrainte peut récupérer après une défaillance et retourner normalement au comportement attendu.</span><span class="sxs-lookup"><span data-stu-id="2ad8d-114">Stress tests determine if an app under stress can recover from failure and gracefully return to expected behavior.</span></span> <span data-ttu-id="2ad8d-115">En cas de stress, l’application n’est pas exécutée dans des conditions normales.</span><span class="sxs-lookup"><span data-stu-id="2ad8d-115">Under stress, the app isn't run under normal conditions.</span></span>

<span data-ttu-id="2ad8d-116">Visual Studio 2019 est la dernière version de Visual Studio avec des fonctionnalités de test de charge.</span><span class="sxs-lookup"><span data-stu-id="2ad8d-116">Visual Studio 2019 is the last version of Visual Studio with load test features.</span></span> <span data-ttu-id="2ad8d-117">Pour les clients nécessitant des outils de test de charge à l’avenir, nous vous recommandons d’autres outils, tels que Apache JMeter, Akamai CloudTest et BlazeMeter.</span><span class="sxs-lookup"><span data-stu-id="2ad8d-117">For customers requiring load testing tools in the future, we recommend alternate tools, such as Apache JMeter, Akamai CloudTest, and BlazeMeter.</span></span> <span data-ttu-id="2ad8d-118">Pour plus d’informations, consultez les [notes de publication de Visual Studio 2019](/visualstudio/releases/2019/release-notes-v16.0#test-tools).</span><span class="sxs-lookup"><span data-stu-id="2ad8d-118">For more information, see the [Visual Studio 2019 Release Notes](/visualstudio/releases/2019/release-notes-v16.0#test-tools).</span></span>

## <a name="visual-studio-tools"></a><span data-ttu-id="2ad8d-119">Outils Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2ad8d-119">Visual Studio tools</span></span>

<span data-ttu-id="2ad8d-120">Visual Studio permet aux utilisateurs de créer, développer et déboguer des tests de charge et de performances Web.</span><span class="sxs-lookup"><span data-stu-id="2ad8d-120">Visual Studio allows users to create, develop, and debug web performance and load tests.</span></span> <span data-ttu-id="2ad8d-121">Une option est disponible pour créer des tests en enregistrant des actions dans un navigateur Web.</span><span class="sxs-lookup"><span data-stu-id="2ad8d-121">An option is available to create tests by recording actions in a web browser.</span></span>

<span data-ttu-id="2ad8d-122">Pour plus d’informations sur la création, la configuration et l’exécution de projets de test de charge à l’aide de Visual Studio 2017, consultez [démarrage rapide : créer un projet de test de charge](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017).</span><span class="sxs-lookup"><span data-stu-id="2ad8d-122">For information on how to create, configure, and run a load test projects using Visual Studio 2017, see [Quickstart: Create a load test project](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017).</span></span>

<span data-ttu-id="2ad8d-123">Les tests de charge peuvent être configurés pour s’exécuter sur site ou dans le Cloud à l’aide d’Azure DevOps.</span><span class="sxs-lookup"><span data-stu-id="2ad8d-123">Load tests can be configured to run on-premise or run in the cloud using Azure DevOps.</span></span>

## <a name="third-party-tools"></a><span data-ttu-id="2ad8d-124">Outils tiers</span><span class="sxs-lookup"><span data-stu-id="2ad8d-124">Third-party tools</span></span>

<span data-ttu-id="2ad8d-125">La liste suivante contient des outils de performances Web tiers avec différents ensembles de fonctionnalités :</span><span class="sxs-lookup"><span data-stu-id="2ad8d-125">The following list contains third-party web performance tools with various feature sets:</span></span>

* [<span data-ttu-id="2ad8d-126">Apache JMeter</span><span class="sxs-lookup"><span data-stu-id="2ad8d-126">Apache JMeter</span></span>](https://jmeter.apache.org/)
* [<span data-ttu-id="2ad8d-127">ApacheBench (AB)</span><span class="sxs-lookup"><span data-stu-id="2ad8d-127">ApacheBench (ab)</span></span>](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [<span data-ttu-id="2ad8d-128">Gatling</span><span class="sxs-lookup"><span data-stu-id="2ad8d-128">Gatling</span></span>](https://gatling.io/)
* [<span data-ttu-id="2ad8d-129">K6</span><span class="sxs-lookup"><span data-stu-id="2ad8d-129">k6</span></span>](https://k6.io)
* [<span data-ttu-id="2ad8d-130">Caroubes</span><span class="sxs-lookup"><span data-stu-id="2ad8d-130">Locust</span></span>](https://locust.io/)
* [<span data-ttu-id="2ad8d-131">Surtension de l’ouest du vent</span><span class="sxs-lookup"><span data-stu-id="2ad8d-131">West Wind WebSurge</span></span>](https://websurge.west-wind.com/)
* [<span data-ttu-id="2ad8d-132">Netlingue</span><span class="sxs-lookup"><span data-stu-id="2ad8d-132">Netling</span></span>](https://github.com/hallatore/Netling)
* [<span data-ttu-id="2ad8d-133">Vegeta</span><span class="sxs-lookup"><span data-stu-id="2ad8d-133">Vegeta</span></span>](https://github.com/tsenart/vegeta)

