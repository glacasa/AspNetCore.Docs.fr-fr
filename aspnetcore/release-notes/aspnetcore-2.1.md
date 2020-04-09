---
title: Nouveautés d’ASP.NET Core 2.1
author: isaac2004
description: Découvrez les nouvelles fonctionnalités d’ASP.NET Core 2.1.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- SignalR
uid: aspnetcore-2.1
ms.openlocfilehash: af5807b782d4acec8c7d40111dc508dfa6127057
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667544"
---
# <a name="whats-new-in-aspnet-core-21"></a><span data-ttu-id="c944a-103">Nouveautés d’ASP.NET Core 2.1</span><span class="sxs-lookup"><span data-stu-id="c944a-103">What's new in ASP.NET Core 2.1</span></span>

<span data-ttu-id="c944a-104">Cet article met en évidence les modifications les plus importantes dans ASP.NET 2.1 Core et fournit des liens vers la documentation appropriée.</span><span class="sxs-lookup"><span data-stu-id="c944a-104">This article highlights the most significant changes in ASP.NET Core 2.1, with links to relevant documentation.</span></span>

## SignalR

SignalR<span data-ttu-id="c944a-105">a été réécrit pour ASP.NET Core 2.1.</span><span class="sxs-lookup"><span data-stu-id="c944a-105"> has been rewritten for ASP.NET Core 2.1.</span></span> <span data-ttu-id="c944a-106">ASP.NET Core SignalR comprend un certain nombre d’améliorations :</span><span class="sxs-lookup"><span data-stu-id="c944a-106">ASP.NET Core SignalR includes a number of improvements:</span></span>

* <span data-ttu-id="c944a-107">Un modèle simplifié de montée en puissance parallèle.</span><span class="sxs-lookup"><span data-stu-id="c944a-107">A simplified scale-out model.</span></span>
* <span data-ttu-id="c944a-108">Un nouveau client JavaScript sans dépendance de jQuery.</span><span class="sxs-lookup"><span data-stu-id="c944a-108">A new JavaScript client with no jQuery dependency.</span></span>
* <span data-ttu-id="c944a-109">Un nouveau protocole binaire compact basé sur MessagePack.</span><span class="sxs-lookup"><span data-stu-id="c944a-109">A new compact binary protocol based on MessagePack.</span></span>
* <span data-ttu-id="c944a-110">Prise en charge des protocoles personnalisés.</span><span class="sxs-lookup"><span data-stu-id="c944a-110">Support for custom protocols.</span></span>
* <span data-ttu-id="c944a-111">Un nouveau modèle réponse de streaming.</span><span class="sxs-lookup"><span data-stu-id="c944a-111">A new streaming response model.</span></span>
* <span data-ttu-id="c944a-112">Prise en charge des clients basés sur des WebSocket nus.</span><span class="sxs-lookup"><span data-stu-id="c944a-112">Support for clients based on bare WebSockets.</span></span>

<span data-ttu-id="c944a-113">Pour plus d’informations, voir [ASP.NET Core SignalR ](xref:signalr/introduction).</span><span class="sxs-lookup"><span data-stu-id="c944a-113">For more information, see [ASP.NET Core SignalR](xref:signalr/introduction).</span></span>

## <a name="razor-class-libraries"></a><span data-ttu-id="c944a-114">Bibliothèques de classes Razor</span><span class="sxs-lookup"><span data-stu-id="c944a-114">Razor class libraries</span></span>

<span data-ttu-id="c944a-115">Avec ASP.NET Core 2.1, il est plus facile de générer une interface utilisateur basée sur Razor, de l’inclure dans une bibliothèque et de la partager entre plusieurs projets.</span><span class="sxs-lookup"><span data-stu-id="c944a-115">ASP.NET Core 2.1 makes it easier to build and include Razor-based UI in a library and share it across multiple projects.</span></span> <span data-ttu-id="c944a-116">Le nouveau SDK Razor permet de générer des fichiers Razor dans un projet de bibliothèque de classes qui peut être placé dans un package NuGet.</span><span class="sxs-lookup"><span data-stu-id="c944a-116">The new Razor SDK enables building Razor files into a class library project that can be packaged into a NuGet package.</span></span> <span data-ttu-id="c944a-117">Les vues et les pages dans les bibliothèques sont automatiquement découvertes et peuvent être remplacées par l’application.</span><span class="sxs-lookup"><span data-stu-id="c944a-117">Views and pages in libraries are automatically discovered and can be overridden by the app.</span></span> <span data-ttu-id="c944a-118">Grâce à l’intégration de la compilation Razor dans la build :</span><span class="sxs-lookup"><span data-stu-id="c944a-118">By integrating Razor compilation into the build:</span></span>

* <span data-ttu-id="c944a-119">Le temps de démarrage de l’application est nettement plus rapide.</span><span class="sxs-lookup"><span data-stu-id="c944a-119">The app startup time is significantly faster.</span></span>
* <span data-ttu-id="c944a-120">Les mises à jour rapides des pages et vues Razor au moment de l’exécution sont toujours disponibles dans le cadre d’un flux de travail de développement itératif.</span><span class="sxs-lookup"><span data-stu-id="c944a-120">Fast updates to Razor views and pages at runtime are still available as part of an iterative development workflow.</span></span>

<span data-ttu-id="c944a-121">Pour plus d’informations, consultez [Créer une interface utilisateur réutilisable à l’aide du projet Razor Class Library](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="c944a-121">For more information, see [Create reusable UI using the Razor Class Library project](xref:razor-pages/ui-class).</span></span>

## <a name="identity-ui-library--scaffolding"></a><span data-ttu-id="c944a-122">Bibliothèque de l’interface utilisateur d’identité et génération de modèles automatique</span><span class="sxs-lookup"><span data-stu-id="c944a-122">Identity UI library & scaffolding</span></span>

<span data-ttu-id="c944a-123">ASP.NET Core 2.1 fournit [l’identité ASP.NET Core](xref:security/authentication/identity) comme [bibliothèque de classes Razor](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="c944a-123">ASP.NET Core 2.1 provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="c944a-124">Les applications qui incluent l’identité peuvent appliquer le nouveau générateur de modèles automatique d’identité de manière sélective pour ajouter le code source contenu dans la bibliothèque de classes Razor d’identité (RCL).</span><span class="sxs-lookup"><span data-stu-id="c944a-124">Apps that include Identity can apply the new Identity scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="c944a-125">Vous pouvez souhaiter générer le code source afin de pouvoir modifier le code et changer le comportement.</span><span class="sxs-lookup"><span data-stu-id="c944a-125">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="c944a-126">Par exemple, vous pouvez demander au générateur de modèles automatique de générer le code utilisé dans l’inscription.</span><span class="sxs-lookup"><span data-stu-id="c944a-126">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="c944a-127">Le code généré est prioritaire sur le même code dans la bibliothèque de classes Razor d’identité.</span><span class="sxs-lookup"><span data-stu-id="c944a-127">Generated code takes precedence over the same code in the Identity RCL.</span></span>

<span data-ttu-id="c944a-128">Les applications qui **n’incluent pas** l’authentification peuvent appliquer le générateur de modèles automatique d’identité pour ajouter le package d’identité de la bibliothèque de classes.</span><span class="sxs-lookup"><span data-stu-id="c944a-128">Apps that do **not** include authentication can apply the Identity scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="c944a-129">Vous pouvez sélectionner le code d’identité à générer.</span><span class="sxs-lookup"><span data-stu-id="c944a-129">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="c944a-130">Pour plus d’informations, consultez [Identité de vue de structure dans les projets ASP.NET Core](xref:security/authentication/scaffold-identity).</span><span class="sxs-lookup"><span data-stu-id="c944a-130">For more information, see [Scaffold Identity in ASP.NET Core projects](xref:security/authentication/scaffold-identity).</span></span>

## <a name="https"></a><span data-ttu-id="c944a-131">HTTPS</span><span class="sxs-lookup"><span data-stu-id="c944a-131">HTTPS</span></span>

<span data-ttu-id="c944a-132">L’importance croissante accordée à la sécurité et à la confidentialité justifie l’activation du protocole HTTPS pour les applications web.</span><span class="sxs-lookup"><span data-stu-id="c944a-132">With the increased focus on security and privacy, enabling HTTPS for web apps is important.</span></span> <span data-ttu-id="c944a-133">La mise en œuvre du protocole HTTPS devient de plus en plus stricte sur le web.</span><span class="sxs-lookup"><span data-stu-id="c944a-133">HTTPS enforcement is becoming increasingly strict on the web.</span></span> <span data-ttu-id="c944a-134">Les sites qui n’utilisent pas HTTPS sont considérés comme peu sûrs.</span><span class="sxs-lookup"><span data-stu-id="c944a-134">Sites that don't use HTTPS are considered insecure.</span></span> <span data-ttu-id="c944a-135">Les navigateurs (Chrome, Mozilla) commencent à imposer l’utilisation des fonctionnalités web dans un contexte sécurisé.</span><span class="sxs-lookup"><span data-stu-id="c944a-135">Browsers (Chromium, Mozilla) are starting to enforce that web features must be used from a secure context.</span></span> <span data-ttu-id="c944a-136">Le [RGPD](xref:security/gdpr) exige l’utilisation du protocole HTTPS pour protéger la confidentialité des utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="c944a-136">[GDPR](xref:security/gdpr) requires the use of HTTPS to protect user privacy.</span></span> <span data-ttu-id="c944a-137">L’utilisation du protocole HTTPS en production est critique et son utilisation en développement peut aider à éviter les problèmes liés au déploiement (tels que les liens non sécurisés).</span><span class="sxs-lookup"><span data-stu-id="c944a-137">While using HTTPS in production is critical, using HTTPS in development can help prevent issues in deployment (for example, insecure links).</span></span> <span data-ttu-id="c944a-138">ASP.NET Core 2.1 inclut un certain nombre d’améliorations qui facilitent l’utilisation du protocole HTTPS pendant le développement et sa configuration en production.</span><span class="sxs-lookup"><span data-stu-id="c944a-138">ASP.NET Core 2.1 includes a number of improvements that make it easier to use HTTPS in development and to configure HTTPS in production.</span></span> <span data-ttu-id="c944a-139">Pour plus d’informations, consultez [Appliquer le protocole HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="c944a-139">For more information, see [Enforce HTTPS](xref:security/enforcing-ssl).</span></span>

### <a name="on-by-default"></a><span data-ttu-id="c944a-140">Activé par défaut</span><span class="sxs-lookup"><span data-stu-id="c944a-140">On by default</span></span>

<span data-ttu-id="c944a-141">Pour faciliter le développement de sites web sécurisés, le protocole HTTPS est maintenant activé par défaut.</span><span class="sxs-lookup"><span data-stu-id="c944a-141">To facilitate secure website development, HTTPS  is now enabled by default.</span></span> <span data-ttu-id="c944a-142">À compter de la version 2.1, Kestrel écoute sur `https://localhost:5001` quand un certificat de développement local est présent.</span><span class="sxs-lookup"><span data-stu-id="c944a-142">Starting in 2.1, Kestrel listens on `https://localhost:5001` when a local development certificate is present.</span></span> <span data-ttu-id="c944a-143">Un certificat de développement est créé :</span><span class="sxs-lookup"><span data-stu-id="c944a-143">A development certificate is created:</span></span>

* <span data-ttu-id="c944a-144">Dans le cadre de la première exécution du kit SDK .NET Core, quand vous utilisez celui-ci pour la première fois.</span><span class="sxs-lookup"><span data-stu-id="c944a-144">As part of the .NET Core SDK first-run experience, when you use the SDK for the first time.</span></span>
* <span data-ttu-id="c944a-145">Manuellement à l’aide du nouvel outil `dev-certs`.</span><span class="sxs-lookup"><span data-stu-id="c944a-145">Manually using the new `dev-certs` tool.</span></span>

<span data-ttu-id="c944a-146">Exécutez `dotnet dev-certs https --trust` pour approuver le certificat.</span><span class="sxs-lookup"><span data-stu-id="c944a-146">Run `dotnet dev-certs https --trust` to trust the certificate.</span></span>

### <a name="https-redirection-and-enforcement"></a><span data-ttu-id="c944a-147">Redirection et application du protocole HTTPS</span><span class="sxs-lookup"><span data-stu-id="c944a-147">HTTPS redirection and enforcement</span></span>

<span data-ttu-id="c944a-148">En règle générale, les applications web doivent écouter sur les protocoles HTTP et HTTPS, mais ensuite rediriger tout le trafic HTTP vers HTTPS.</span><span class="sxs-lookup"><span data-stu-id="c944a-148">Web apps typically need to listen on both HTTP and HTTPS, but then redirect all HTTP traffic to HTTPS.</span></span> <span data-ttu-id="c944a-149">Dans la version 2.1 a été introduit le middleware (intergiciel) de redirection HTTPS spécialisé, qui redirige intelligemment en fonction de la présence de ports de serveur lié ou de configuration.</span><span class="sxs-lookup"><span data-stu-id="c944a-149">In 2.1, specialized HTTPS redirection middleware that intelligently redirects based on the presence of configuration or bound server ports has been introduced.</span></span>

<span data-ttu-id="c944a-150">Vous pouvez renforcer l’utilisation du protocole HTTPS en utilisant le protocole [HSTS (HTTP Strict Transport Security Protocol)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts).</span><span class="sxs-lookup"><span data-stu-id="c944a-150">Use of HTTPS can be further enforced using [HTTP Strict Transport Security Protocol (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts).</span></span> <span data-ttu-id="c944a-151">Le protocole HSTS indique aux navigateurs de toujours accéder au site via HTTPS.</span><span class="sxs-lookup"><span data-stu-id="c944a-151">HSTS instructs browsers to always access the site via HTTPS.</span></span> <span data-ttu-id="c944a-152">ASP.NET Core 2.1 ajoute un middleware HSTS qui prend en charge des options pour l’âge maximal, les sous-domaines et la liste de préchargement HSTS.</span><span class="sxs-lookup"><span data-stu-id="c944a-152">ASP.NET Core 2.1 adds HSTS middleware that supports options for max age, subdomains, and the HSTS preload list.</span></span>

### <a name="configuration-for-production"></a><span data-ttu-id="c944a-153">Configuration pour la production</span><span class="sxs-lookup"><span data-stu-id="c944a-153">Configuration for production</span></span>

<span data-ttu-id="c944a-154">En production, HTTPS doit être explicitement configuré.</span><span class="sxs-lookup"><span data-stu-id="c944a-154">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="c944a-155">Dans la version 2.1, le schéma de configuration par défaut pour la configuration HTTPS pour Kestrel a été ajouté.</span><span class="sxs-lookup"><span data-stu-id="c944a-155">In 2.1, default configuration schema for configuring HTTPS for Kestrel has been added.</span></span> <span data-ttu-id="c944a-156">Les applications peuvent être configurées pour utiliser :</span><span class="sxs-lookup"><span data-stu-id="c944a-156">Apps can be configured to use:</span></span>

* <span data-ttu-id="c944a-157">Plusieurs points de terminaison, y compris les URL.</span><span class="sxs-lookup"><span data-stu-id="c944a-157">Multiple endpoints including the URLs.</span></span> <span data-ttu-id="c944a-158">Pour plus d’informations, consultez [Implémentation du serveur web Kestrel : configuration de point de terminaison](xref:fundamentals/servers/kestrel#endpoint-configuration).</span><span class="sxs-lookup"><span data-stu-id="c944a-158">For more information, see [Kestrel web server implementation: Endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>
* <span data-ttu-id="c944a-159">Le certificat à utiliser pour le protocole HTTPS à partir d’un fichier sur disque ou d’un magasin de certificats.</span><span class="sxs-lookup"><span data-stu-id="c944a-159">The certificate to use for HTTPS either from a file on disk or from a certificate store.</span></span>

## <a name="gdpr"></a><span data-ttu-id="c944a-160">RGPD</span><span class="sxs-lookup"><span data-stu-id="c944a-160">GDPR</span></span>

<span data-ttu-id="c944a-161">ASP.NET Core fournit des API et des modèles qui aident à satisfaire à certaines des exigences du [Règlement général sur la protection des données (RGPD)](https://www.eugdpr.org/).</span><span class="sxs-lookup"><span data-stu-id="c944a-161">ASP.NET Core provides APIs and templates to help meet some of the [EU General Data Protection Regulation (GDPR)](https://www.eugdpr.org/) requirements.</span></span> <span data-ttu-id="c944a-162">Pour plus d’informations, consultez [Prise en charge du RGPD dans ASP.NET Core](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="c944a-162">For more information, see [GDPR support in ASP.NET Core](xref:security/gdpr).</span></span> <span data-ttu-id="c944a-163">Un [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) montre comment utiliser et tester la plupart des API et points d’extension RGPD ajoutés aux modèles ASP.NET Core 2.1.</span><span class="sxs-lookup"><span data-stu-id="c944a-163">A [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) shows how to use and lets you test most of the GDPR extension points and APIs added to the ASP.NET Core 2.1 templates.</span></span>

## <a name="integration-tests"></a><span data-ttu-id="c944a-164">Tests d’intégration</span><span class="sxs-lookup"><span data-stu-id="c944a-164">Integration tests</span></span>

<span data-ttu-id="c944a-165">Un nouveau package est introduit qui simplifie la création et l’exécution de tests.</span><span class="sxs-lookup"><span data-stu-id="c944a-165">A new package is introduced that streamlines test creation and execution.</span></span> <span data-ttu-id="c944a-166">Le package [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/) gère les tâches suivantes :</span><span class="sxs-lookup"><span data-stu-id="c944a-166">The [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/) package handles the following tasks:</span></span>

* <span data-ttu-id="c944a-167">Copie du fichier de dépendance (*\*.deps*) de l’application testée dans le dossier *poubelle* du projet de test.</span><span class="sxs-lookup"><span data-stu-id="c944a-167">Copies the dependency file (*\*.deps*) from the tested app into the test project's *bin* folder.</span></span>
* <span data-ttu-id="c944a-168">Il définit la racine du contenu sur la racine du projet de l’application testée afin que soient trouvés les pages/vues et fichiers statiques quand les tests sont exécutés.</span><span class="sxs-lookup"><span data-stu-id="c944a-168">Sets the content root to the tested app's project root so that static files and pages/views are found when the tests are executed.</span></span>
* <span data-ttu-id="c944a-169">Il fournit la classe [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) afin de simplifier l’amorçage de l’application testée avec [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span><span class="sxs-lookup"><span data-stu-id="c944a-169">Provides the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) class to streamline bootstrapping the tested app with [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span></span>

<span data-ttu-id="c944a-170">Le test suivant utilise [xUnit](https://xunit.github.io/) pour vérifier que la page Index se charge avec un code d’état de réussite et avec l’en-tête Content-Type correct :</span><span class="sxs-lookup"><span data-stu-id="c944a-170">The following test uses [xUnit](https://xunit.github.io/) to check that the Index page loads with a success status code and with the correct Content-Type header:</span></span>

```csharp
public class BasicTests
    : IClassFixture<WebApplicationFactory<RazorPagesProject.Startup>>
{
    private readonly HttpClient _client;

    public BasicTests(WebApplicationFactory<RazorPagesProject.Startup> factory)
    {
        _client = factory.CreateClient();
    }

    [Fact]
    public async Task GetHomePage()
    {
        // Act
        var response = await _client.GetAsync("/");

        // Assert
        response.EnsureSuccessStatusCode(); // Status Code 200-299
        Assert.Equal("text/html; charset=utf-8",
            response.Content.Headers.ContentType.ToString());
    }
}
```

<span data-ttu-id="c944a-171">Pour plus d’informations, consultez la rubrique [Tests d’intégration](xref:test/integration-tests).</span><span class="sxs-lookup"><span data-stu-id="c944a-171">For more information, see the [Integration tests](xref:test/integration-tests) topic.</span></span>

## <a name="apicontroller-actionresultt"></a><span data-ttu-id="c944a-172">[ApiController], ActionResult\<T></span><span class="sxs-lookup"><span data-stu-id="c944a-172">[ApiController], ActionResult\<T></span></span>

<span data-ttu-id="c944a-173">ASP.NET Core 2.1 ajoute de nouvelles conventions de programmation qui facilitent la génération d’API web propres et descriptives.</span><span class="sxs-lookup"><span data-stu-id="c944a-173">ASP.NET Core 2.1 adds new programming conventions that make it easier to build clean and descriptive web APIs.</span></span> <span data-ttu-id="c944a-174">`ActionResult<T>` est un nouveau type qui permet à une application de retourner soit un type de réponse, soit tout autre résultat d’action (à l’image d’IActionResult) tout en indiquant toujours le type de réponse.</span><span class="sxs-lookup"><span data-stu-id="c944a-174">`ActionResult<T>` is a new type added to allow an app to return either a response type or any other action result (similar to IActionResult), while still indicating the response type.</span></span> <span data-ttu-id="c944a-175">L’attribut `[ApiController]` a également été ajouté comme moyen d’accepter des comportements et des conventions propres aux API web.</span><span class="sxs-lookup"><span data-stu-id="c944a-175">The `[ApiController]` attribute has also been added as the way to opt in to Web API-specific conventions and behaviors.</span></span>

<span data-ttu-id="c944a-176">Pour plus d’informations, consultez [Créer des API web avec ASP.NET Core](xref:web-api/index).</span><span class="sxs-lookup"><span data-stu-id="c944a-176">For more information, see [Build Web APIs with ASP.NET Core](xref:web-api/index).</span></span>

## <a name="ihttpclientfactory"></a><span data-ttu-id="c944a-177">IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="c944a-177">IHttpClientFactory</span></span>

<span data-ttu-id="c944a-178">ASP.NET Core 2.1 inclut un nouveau service `IHttpClientFactory` qui facilite la configuration et l’utilisation d’instances de `HttpClient` dans les applications.</span><span class="sxs-lookup"><span data-stu-id="c944a-178">ASP.NET Core 2.1 includes a new `IHttpClientFactory` service that makes it easier to configure and consume instances of `HttpClient` in apps.</span></span> <span data-ttu-id="c944a-179">`HttpClient` intègre déjà le concept de délégation des gestionnaires qui pourraient être liés ensemble pour les requêtes HTTP sortantes.</span><span class="sxs-lookup"><span data-stu-id="c944a-179">`HttpClient` already has the concept of delegating handlers that could be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="c944a-180">La fabrique :</span><span class="sxs-lookup"><span data-stu-id="c944a-180">The factory:</span></span>

* <span data-ttu-id="c944a-181">Rend plus intuitive l’inscription des instances de `HttpClient` par client nommé.</span><span class="sxs-lookup"><span data-stu-id="c944a-181">Makes registering of instances of `HttpClient` per named client more intuitive.</span></span>
* <span data-ttu-id="c944a-182">Implémente un gestionnaire Polly qui permet d’utiliser des stratégies Polly pour des fonctionnalités telles que Retry (nouvelle tentative) ou CircuitBreaker (disjoncteur).</span><span class="sxs-lookup"><span data-stu-id="c944a-182">Implements a Polly handler that allows Polly policies to be used for Retry, CircuitBreakers, etc.</span></span>

<span data-ttu-id="c944a-183">Pour plus d’informations, consultez [Lancer des requêtes HTTP](xref:fundamentals/http-requests).</span><span class="sxs-lookup"><span data-stu-id="c944a-183">For more information, see [Initiate HTTP Requests](xref:fundamentals/http-requests).</span></span>

## <a name="kestrel-transport-configuration"></a><span data-ttu-id="c944a-184">Configuration du transport Kestrel</span><span class="sxs-lookup"><span data-stu-id="c944a-184">Kestrel transport configuration</span></span>

<span data-ttu-id="c944a-185">Dans ASP.NET Core 2.1, le transport par défaut de Kestrel n’est plus basé sur Libuv, mais sur des sockets managés.</span><span class="sxs-lookup"><span data-stu-id="c944a-185">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="c944a-186">Pour plus d’informations, consultez [Implémentation du serveur web Kestrel : configuration du transport](xref:fundamentals/servers/kestrel#transport-configuration).</span><span class="sxs-lookup"><span data-stu-id="c944a-186">For more information, see [Kestrel web server implementation: Transport configuration](xref:fundamentals/servers/kestrel#transport-configuration).</span></span>

## <a name="generic-host-builder"></a><span data-ttu-id="c944a-187">Générateur d’hôte générique</span><span class="sxs-lookup"><span data-stu-id="c944a-187">Generic host builder</span></span>

<span data-ttu-id="c944a-188">Le générateur d’hôte générique (`HostBuilder`) a été introduit.</span><span class="sxs-lookup"><span data-stu-id="c944a-188">The Generic Host Builder (`HostBuilder`) has been introduced.</span></span> <span data-ttu-id="c944a-189">Ce générateur peut être utilisé pour les applications qui ne traitent pas les requêtes HTTP (messagerie, les tâches en arrière-plan, etc.).</span><span class="sxs-lookup"><span data-stu-id="c944a-189">This builder can be used for apps that don't process HTTP requests (Messaging, background tasks, etc.).</span></span>

<span data-ttu-id="c944a-190">Pour plus d’informations, consultez [Hôte générique .NET](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="c944a-190">For more information, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

## <a name="updated-spa-templates"></a><span data-ttu-id="c944a-191">Modèles SPA mis à jour</span><span class="sxs-lookup"><span data-stu-id="c944a-191">Updated SPA templates</span></span>

<span data-ttu-id="c944a-192">Les modèles d’applications monopages pour Angular, React et React avec Redux sont mis à jour pour utiliser les systèmes de génération et les structures de projet standard pour chaque framework.</span><span class="sxs-lookup"><span data-stu-id="c944a-192">The Single Page Application templates for Angular, React, and React with Redux are updated to use the standard project structures and build systems for each framework.</span></span>

<span data-ttu-id="c944a-193">Le modèle Angular est basé sur l’interface CLI Angular, tandis que les modèles React sont basés sur create-react-app.</span><span class="sxs-lookup"><span data-stu-id="c944a-193">The Angular template is based on the Angular CLI, and the React templates are based on create-react-app.</span></span>

<span data-ttu-id="c944a-194">Pour plus d'informations, consultez les pages suivantes :</span><span class="sxs-lookup"><span data-stu-id="c944a-194">For more information, see:</span></span>

* <xref:spa/angular>
* <xref:spa/react>
* <xref:spa/react-with-redux>

## <a name="razor-pages-search-for-razor-assets"></a><span data-ttu-id="c944a-195">Recherche Razor Pages de ressources Razor</span><span class="sxs-lookup"><span data-stu-id="c944a-195">Razor Pages search for Razor assets</span></span>

<span data-ttu-id="c944a-196">Dans la version 2.1, Razor Pages recherche les ressources Razor (par exemple, les dispositions et pages partielles) dans les répertoires suivants dans l’ordre indiqué :</span><span class="sxs-lookup"><span data-stu-id="c944a-196">In 2.1, Razor Pages search for Razor assets (such as layouts and partials) in the following directories in the listed order:</span></span>

1. <span data-ttu-id="c944a-197">Dossier Pages en cours.</span><span class="sxs-lookup"><span data-stu-id="c944a-197">Current Pages folder.</span></span>
1. <span data-ttu-id="c944a-198">*/Pages/Shared/*</span><span class="sxs-lookup"><span data-stu-id="c944a-198">*/Pages/Shared/*</span></span>
1. <span data-ttu-id="c944a-199">*/Views/Shared/*</span><span class="sxs-lookup"><span data-stu-id="c944a-199">*/Views/Shared/*</span></span>

## <a name="razor-pages-in-an-area"></a><span data-ttu-id="c944a-200">Razor Pages dans une zone</span><span class="sxs-lookup"><span data-stu-id="c944a-200">Razor Pages in an area</span></span>

<span data-ttu-id="c944a-201">Razor Pages prend désormais en charge les [zones](xref:mvc/controllers/areas).</span><span class="sxs-lookup"><span data-stu-id="c944a-201">Razor Pages now support [areas](xref:mvc/controllers/areas).</span></span> <span data-ttu-id="c944a-202">Pour obtenir un exemple de zones, créez une application web Razor Pages avec des comptes d’utilisateur individuels.</span><span class="sxs-lookup"><span data-stu-id="c944a-202">To see an example of areas, create a new Razor Pages web app with individual user accounts.</span></span> <span data-ttu-id="c944a-203">Une application web Razor Pages avec des comptes d’utilisateur individuels inclut */Areas/Identity/Pages*.</span><span class="sxs-lookup"><span data-stu-id="c944a-203">A Razor Pages web app with individual user accounts includes */Areas/Identity/Pages*.</span></span>

## <a name="mvc-compatibility-version"></a><span data-ttu-id="c944a-204">Version de compatibilité MVC</span><span class="sxs-lookup"><span data-stu-id="c944a-204">MVC compatibility version</span></span>

<span data-ttu-id="c944a-205">La méthode <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> permet à une application d’accepter ou de refuser les changements de comportement potentiellement cassants introduits dans ASP.NET Core MVC 2.1 ou version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="c944a-205">The <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> method allows an app to opt-in or opt-out of potentially breaking behavior changes introduced in ASP.NET Core MVC 2.1 or later.</span></span>

<span data-ttu-id="c944a-206">Pour plus d’informations, consultez <xref:mvc/compatibility-version>.</span><span class="sxs-lookup"><span data-stu-id="c944a-206">For more information, see <xref:mvc/compatibility-version>.</span></span>

## <a name="migrate-from-20-to-21"></a><span data-ttu-id="c944a-207">Migrer depuis la version 2.0 vers la version 2.1</span><span class="sxs-lookup"><span data-stu-id="c944a-207">Migrate from 2.0 to 2.1</span></span>

<span data-ttu-id="c944a-208">Consultez [Migrer depuis ASP.NET Core 2.0 vers 2.1](xref:migration/20_21).</span><span class="sxs-lookup"><span data-stu-id="c944a-208">See [Migrate from ASP.NET Core 2.0 to 2.1](xref:migration/20_21).</span></span>

## <a name="additional-information"></a><span data-ttu-id="c944a-209">Informations supplémentaires</span><span class="sxs-lookup"><span data-stu-id="c944a-209">Additional information</span></span>

<span data-ttu-id="c944a-210">Pour obtenir la liste complète des modifications, consultez les [Notes de publication d’ASP.NET Core 2.1 ](https://github.com/dotnet/aspnetcore/releases/tag/2.1.0).</span><span class="sxs-lookup"><span data-stu-id="c944a-210">For the complete list of changes, see the [ASP.NET Core 2.1 Release Notes](https://github.com/dotnet/aspnetcore/releases/tag/2.1.0).</span></span>
