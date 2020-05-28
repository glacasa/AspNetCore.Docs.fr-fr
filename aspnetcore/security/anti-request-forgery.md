---
<span data-ttu-id="87ed2-101">titre : Auteur : Description : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="87ed2-101">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="87ed2-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="87ed2-102">'Blazor'</span></span>
- <span data-ttu-id="87ed2-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="87ed2-103">'Identity'</span></span>
- <span data-ttu-id="87ed2-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="87ed2-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="87ed2-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="87ed2-105">'Razor'</span></span>
- <span data-ttu-id="87ed2-106">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="87ed2-106">'SignalR' uid:</span></span> 

---
# <a name="prevent-cross-site-request-forgery-xsrfcsrf-attacks-in-aspnet-core"></a><span data-ttu-id="87ed2-107">Empêcher les attaques de falsification de requête intersites (XSRF/CSRF) dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="87ed2-107">Prevent Cross-Site Request Forgery (XSRF/CSRF) attacks in ASP.NET Core</span></span>

<span data-ttu-id="87ed2-108">Par [Rick Anderson](https://twitter.com/RickAndMSFT), [Fiyaz Hasan](https://twitter.com/FiyazBinHasan)et [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="87ed2-108">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Fiyaz Hasan](https://twitter.com/FiyazBinHasan), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="87ed2-109">La falsification de requête intersite (également appelée XSRF ou CSRF) est une attaque contre les applications hébergées sur le Web, dans laquelle une application Web malveillante peut influencer l’interaction entre un navigateur client et une application Web qui approuve ce navigateur.</span><span class="sxs-lookup"><span data-stu-id="87ed2-109">Cross-site request forgery (also known as XSRF or CSRF) is an attack against web-hosted apps whereby a malicious web app can influence the interaction between a client browser and a web app that trusts that browser.</span></span> <span data-ttu-id="87ed2-110">Ces attaques sont possibles, car les navigateurs Web envoient automatiquement certains types de jetons d’authentification à chaque demande adressée à un site Web.</span><span class="sxs-lookup"><span data-stu-id="87ed2-110">These attacks are possible because web browsers send some types of authentication tokens automatically with every request to a website.</span></span> <span data-ttu-id="87ed2-111">Cette forme d’exploitation est également connue sous le nom d' *attaque en un clic* ou d’une *session* , car l’attaque tire parti de la session précédemment authentifiée de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="87ed2-111">This form of exploit is also known as a *one-click attack* or *session riding* because the attack takes advantage of the user's previously authenticated session.</span></span>

<span data-ttu-id="87ed2-112">Exemple d’attaque CSRF :</span><span class="sxs-lookup"><span data-stu-id="87ed2-112">An example of a CSRF attack:</span></span>

1. <span data-ttu-id="87ed2-113">Un utilisateur se connecte `www.good-banking-site.com` à à l’aide de l’authentification par formulaire.</span><span class="sxs-lookup"><span data-stu-id="87ed2-113">A user signs into `www.good-banking-site.com` using forms authentication.</span></span> <span data-ttu-id="87ed2-114">Le serveur authentifie l’utilisateur et émet une réponse qui comprend un cookie d’authentification.</span><span class="sxs-lookup"><span data-stu-id="87ed2-114">The server authenticates the user and issues a response that includes an authentication cookie.</span></span> <span data-ttu-id="87ed2-115">Le site est vulnérable aux attaques, car il approuve les demandes qu’il reçoit avec un cookie d’authentification valide.</span><span class="sxs-lookup"><span data-stu-id="87ed2-115">The site is vulnerable to attack because it trusts any request that it receives with a valid authentication cookie.</span></span>
1. <span data-ttu-id="87ed2-116">L’utilisateur visite un site malveillant, `www.bad-crook-site.com` .</span><span class="sxs-lookup"><span data-stu-id="87ed2-116">The user visits a malicious site, `www.bad-crook-site.com`.</span></span>

   <span data-ttu-id="87ed2-117">Le site malveillant, `www.bad-crook-site.com` , contient un formulaire HTML semblable au suivant :</span><span class="sxs-lookup"><span data-stu-id="87ed2-117">The malicious site, `www.bad-crook-site.com`, contains an HTML form similar to the following:</span></span>

   ```html
   <h1>Congratulations! You're a Winner!</h1>
   <form action="http://good-banking-site.com/api/account" method="post">
       <input type="hidden" name="Transaction" value="withdraw">
       <input type="hidden" name="Amount" value="1000000">
       <input type="submit" value="Click to collect your prize!">
   </form>
   ```

   <span data-ttu-id="87ed2-118">Notez que le formulaire est `action` publié sur le site vulnérable, et non sur le site malveillant.</span><span class="sxs-lookup"><span data-stu-id="87ed2-118">Notice that the form's `action` posts to the vulnerable site, not to the malicious site.</span></span> <span data-ttu-id="87ed2-119">Il s’agit de la partie « inter-sites » de CSRF.</span><span class="sxs-lookup"><span data-stu-id="87ed2-119">This is the "cross-site" part of CSRF.</span></span>

1. <span data-ttu-id="87ed2-120">L’utilisateur sélectionne le bouton Envoyer.</span><span class="sxs-lookup"><span data-stu-id="87ed2-120">The user selects the submit button.</span></span> <span data-ttu-id="87ed2-121">Le navigateur effectue la demande et intègre automatiquement le cookie d’authentification pour le domaine demandé, `www.good-banking-site.com` .</span><span class="sxs-lookup"><span data-stu-id="87ed2-121">The browser makes the request and automatically includes the authentication cookie for the requested domain, `www.good-banking-site.com`.</span></span>
1. <span data-ttu-id="87ed2-122">La demande s’exécute sur le `www.good-banking-site.com` serveur avec le contexte d’authentification de l’utilisateur et peut effectuer toute action qu’un utilisateur authentifié est autorisé à effectuer.</span><span class="sxs-lookup"><span data-stu-id="87ed2-122">The request runs on the `www.good-banking-site.com` server with the user's authentication context and can perform any action that an authenticated user is allowed to perform.</span></span>

<span data-ttu-id="87ed2-123">En plus du scénario dans lequel l’utilisateur sélectionne le bouton pour envoyer le formulaire, le site malveillant peut :</span><span class="sxs-lookup"><span data-stu-id="87ed2-123">In addition to the scenario where the user selects the button to submit the form, the malicious site could:</span></span>

* <span data-ttu-id="87ed2-124">Exécutez un script qui envoie automatiquement le formulaire.</span><span class="sxs-lookup"><span data-stu-id="87ed2-124">Run a script that automatically submits the form.</span></span>
* <span data-ttu-id="87ed2-125">Envoyer l’envoi de formulaire en tant que requête AJAX.</span><span class="sxs-lookup"><span data-stu-id="87ed2-125">Send the form submission as an AJAX request.</span></span>
* <span data-ttu-id="87ed2-126">Masquez le formulaire en utilisant CSS.</span><span class="sxs-lookup"><span data-stu-id="87ed2-126">Hide the form using CSS.</span></span>

<span data-ttu-id="87ed2-127">Ces scénarios alternatifs ne nécessitent aucune action ou entrée de la part de l’utilisateur autre que la visite initiale du site malveillant.</span><span class="sxs-lookup"><span data-stu-id="87ed2-127">These alternative scenarios don't require any action or input from the user other than initially visiting the malicious site.</span></span>

<span data-ttu-id="87ed2-128">L’utilisation de HTTPs n’empêche pas une attaque CSRF.</span><span class="sxs-lookup"><span data-stu-id="87ed2-128">Using HTTPS doesn't prevent a CSRF attack.</span></span> <span data-ttu-id="87ed2-129">Le site malveillant peut envoyer une `https://www.good-banking-site.com/` demande tout aussi facilement qu’il peut envoyer une demande non sécurisée.</span><span class="sxs-lookup"><span data-stu-id="87ed2-129">The malicious site can send an `https://www.good-banking-site.com/` request just as easily as it can send an insecure request.</span></span>

<span data-ttu-id="87ed2-130">Certaines attaques ciblent des points de terminaison qui répondent aux demandes d’extraction, auquel cas une balise d’image peut être utilisée pour effectuer l’action.</span><span class="sxs-lookup"><span data-stu-id="87ed2-130">Some attacks target endpoints that respond to GET requests, in which case an image tag can be used to perform the action.</span></span> <span data-ttu-id="87ed2-131">Cette forme d’attaque est courante sur les sites de forum qui autorisent les images, mais qui bloquent JavaScript.</span><span class="sxs-lookup"><span data-stu-id="87ed2-131">This form of attack is common on forum sites that permit images but block JavaScript.</span></span> <span data-ttu-id="87ed2-132">Les applications qui changent d’État sur les demandes d’extraction, où les variables ou les ressources sont modifiées, sont vulnérables aux attaques malveillantes.</span><span class="sxs-lookup"><span data-stu-id="87ed2-132">Apps that change state on GET requests, where variables or resources are altered, are vulnerable to malicious attacks.</span></span> <span data-ttu-id="87ed2-133">**Demandes d’obtention dont l’état de modification n’est pas sécurisé. Une meilleure pratique consiste à ne jamais modifier l’état d’une requête d’extraction.**</span><span class="sxs-lookup"><span data-stu-id="87ed2-133">**GET requests that change state are insecure. A best practice is to never change state on a GET request.**</span></span>

<span data-ttu-id="87ed2-134">Les attaques CSRF sont possibles pour les applications Web qui utilisent des cookies pour l’authentification, car :</span><span class="sxs-lookup"><span data-stu-id="87ed2-134">CSRF attacks are possible against web apps that use cookies for authentication because:</span></span>

* <span data-ttu-id="87ed2-135">Les navigateurs stockent les cookies émis par une application Web.</span><span class="sxs-lookup"><span data-stu-id="87ed2-135">Browsers store cookies issued by a web app.</span></span>
* <span data-ttu-id="87ed2-136">Les cookies stockés incluent des cookies de session pour les utilisateurs authentifiés.</span><span class="sxs-lookup"><span data-stu-id="87ed2-136">Stored cookies include session cookies for authenticated users.</span></span>
* <span data-ttu-id="87ed2-137">Les navigateurs envoient tous les cookies associés à un domaine à l’application Web à chaque demande, quelle que soit la façon dont la demande à l’application a été générée dans le navigateur.</span><span class="sxs-lookup"><span data-stu-id="87ed2-137">Browsers send all of the cookies associated with a domain to the web app every request regardless of how the request to app was generated within the browser.</span></span>

<span data-ttu-id="87ed2-138">Toutefois, les attaques CSRF ne sont pas limitées à l’exploitation des cookies.</span><span class="sxs-lookup"><span data-stu-id="87ed2-138">However, CSRF attacks aren't limited to exploiting cookies.</span></span> <span data-ttu-id="87ed2-139">Par exemple, l’authentification de base et Digest est également vulnérable.</span><span class="sxs-lookup"><span data-stu-id="87ed2-139">For example, Basic and Digest authentication are also vulnerable.</span></span> <span data-ttu-id="87ed2-140">Une fois qu’un utilisateur se connecte avec l’authentification de base ou Digest, le navigateur envoie automatiquement les informations d’identification jusqu’à la fin de la session &dagger; .</span><span class="sxs-lookup"><span data-stu-id="87ed2-140">After a user signs in with Basic or Digest authentication, the browser automatically sends the credentials until the session&dagger; ends.</span></span>

<span data-ttu-id="87ed2-141">&dagger;Dans ce contexte, la *session* fait référence à la session côté client au cours de laquelle l’utilisateur est authentifié.</span><span class="sxs-lookup"><span data-stu-id="87ed2-141">&dagger;In this context, *session* refers to the client-side session during which the user is authenticated.</span></span> <span data-ttu-id="87ed2-142">Elle n’est pas liée aux sessions côté serveur ou aux intergiciels (middleware) de [Session ASP.net Core](xref:fundamentals/app-state).</span><span class="sxs-lookup"><span data-stu-id="87ed2-142">It's unrelated to server-side sessions or [ASP.NET Core Session Middleware](xref:fundamentals/app-state).</span></span>

<span data-ttu-id="87ed2-143">Les utilisateurs peuvent se protéger contre les vulnérabilités de CSRF en acceptant les précautions suivantes :</span><span class="sxs-lookup"><span data-stu-id="87ed2-143">Users can guard against CSRF vulnerabilities by taking precautions:</span></span>

* <span data-ttu-id="87ed2-144">Déconnectez-vous de Web Apps une fois que vous avez fini de les utiliser.</span><span class="sxs-lookup"><span data-stu-id="87ed2-144">Sign off of web apps when finished using them.</span></span>
* <span data-ttu-id="87ed2-145">Effacez régulièrement les cookies du navigateur.</span><span class="sxs-lookup"><span data-stu-id="87ed2-145">Clear browser cookies periodically.</span></span>

<span data-ttu-id="87ed2-146">Toutefois, les vulnérabilités CSRF sont fondamentalement un problème avec l’application Web, et non l’utilisateur final.</span><span class="sxs-lookup"><span data-stu-id="87ed2-146">However, CSRF vulnerabilities are fundamentally a problem with the web app, not the end user.</span></span>

## <a name="authentication-fundamentals"></a><span data-ttu-id="87ed2-147">Concepts de base de l’authentification</span><span class="sxs-lookup"><span data-stu-id="87ed2-147">Authentication fundamentals</span></span>

<span data-ttu-id="87ed2-148">L’authentification basée sur les cookies est une forme d’authentification courante.</span><span class="sxs-lookup"><span data-stu-id="87ed2-148">Cookie-based authentication is a popular form of authentication.</span></span> <span data-ttu-id="87ed2-149">Les systèmes d’authentification basés sur les jetons augmentent en popularité, en particulier pour les applications à page unique (SPAs).</span><span class="sxs-lookup"><span data-stu-id="87ed2-149">Token-based authentication systems are growing in popularity, especially for Single Page Applications (SPAs).</span></span>

### <a name="cookie-based-authentication"></a><span data-ttu-id="87ed2-150">Authentification basée sur les cookies</span><span class="sxs-lookup"><span data-stu-id="87ed2-150">Cookie-based authentication</span></span>

<span data-ttu-id="87ed2-151">Lorsqu’un utilisateur s’authentifie à l’aide de son nom d’utilisateur et de son mot de passe, il reçoit un jeton qui contient un ticket d’authentification pouvant être utilisé pour l’authentification et l’autorisation.</span><span class="sxs-lookup"><span data-stu-id="87ed2-151">When a user authenticates using their username and password, they're issued a token, containing an authentication ticket that can be used for authentication and authorization.</span></span> <span data-ttu-id="87ed2-152">Le jeton est stocké sous la forme d’un cookie qui accompagne chaque requête du client.</span><span class="sxs-lookup"><span data-stu-id="87ed2-152">The token is stored as a cookie that accompanies every request the client makes.</span></span> <span data-ttu-id="87ed2-153">La génération et la validation de ce cookie sont effectuées par l’intergiciel (middleware) d’authentification des cookies.</span><span class="sxs-lookup"><span data-stu-id="87ed2-153">Generating and validating this cookie is performed by the Cookie Authentication Middleware.</span></span> <span data-ttu-id="87ed2-154">L' [intergiciel](xref:fundamentals/middleware/index) sérialise un principal d’utilisateur dans un cookie chiffré.</span><span class="sxs-lookup"><span data-stu-id="87ed2-154">The [middleware](xref:fundamentals/middleware/index) serializes a user principal into an encrypted cookie.</span></span> <span data-ttu-id="87ed2-155">Lors des demandes suivantes, l’intergiciel valide le cookie, recrée le principal et attribue le principal à la propriété [User](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user) de [HttpContext](/dotnet/api/microsoft.aspnetcore.http.httpcontext).</span><span class="sxs-lookup"><span data-stu-id="87ed2-155">On subsequent requests, the middleware validates the cookie, recreates the principal, and assigns the principal to the [User](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user) property of [HttpContext](/dotnet/api/microsoft.aspnetcore.http.httpcontext).</span></span>

### <a name="token-based-authentication"></a><span data-ttu-id="87ed2-156">Authentification basée sur les jetons</span><span class="sxs-lookup"><span data-stu-id="87ed2-156">Token-based authentication</span></span>

<span data-ttu-id="87ed2-157">Lorsqu’un utilisateur est authentifié, il reçoit un jeton (et non un jeton anti-contrefaçon).</span><span class="sxs-lookup"><span data-stu-id="87ed2-157">When a user is authenticated, they're issued a token (not an antiforgery token).</span></span> <span data-ttu-id="87ed2-158">Le jeton contient des informations utilisateur sous la forme de [revendications](/dotnet/framework/security/claims-based-identity-model) ou d’un jeton de référence qui fait pointer l’application vers l’état utilisateur géré dans l’application.</span><span class="sxs-lookup"><span data-stu-id="87ed2-158">The token contains user information in the form of [claims](/dotnet/framework/security/claims-based-identity-model) or a reference token that points the app to user state maintained in the app.</span></span> <span data-ttu-id="87ed2-159">Lorsqu’un utilisateur tente d’accéder à une ressource nécessitant une authentification, le jeton est envoyé à l’application avec un en-tête d’autorisation supplémentaire sous forme de jeton du porteur.</span><span class="sxs-lookup"><span data-stu-id="87ed2-159">When a user attempts to access a resource requiring authentication, the token is sent to the app with an additional authorization header in form of Bearer token.</span></span> <span data-ttu-id="87ed2-160">Cela rend l’application sans État.</span><span class="sxs-lookup"><span data-stu-id="87ed2-160">This makes the app stateless.</span></span> <span data-ttu-id="87ed2-161">Dans chaque requête suivante, le jeton est transmis dans la demande de validation côté serveur.</span><span class="sxs-lookup"><span data-stu-id="87ed2-161">In each subsequent request, the token is passed in the request for server-side validation.</span></span> <span data-ttu-id="87ed2-162">Ce jeton n’est pas *chiffré*. elle est *encodée*.</span><span class="sxs-lookup"><span data-stu-id="87ed2-162">This token isn't *encrypted*; it's *encoded*.</span></span> <span data-ttu-id="87ed2-163">Sur le serveur, le jeton est décodé pour accéder à ses informations.</span><span class="sxs-lookup"><span data-stu-id="87ed2-163">On the server, the token is decoded to access its information.</span></span> <span data-ttu-id="87ed2-164">Pour envoyer le jeton sur les demandes suivantes, stockez le jeton dans le stockage local du navigateur.</span><span class="sxs-lookup"><span data-stu-id="87ed2-164">To send the token on subsequent requests, store the token in the browser's local storage.</span></span> <span data-ttu-id="87ed2-165">Ne vous inquiétez pas de la vulnérabilité CSRF si le jeton est stocké dans le stockage local du navigateur.</span><span class="sxs-lookup"><span data-stu-id="87ed2-165">Don't be concerned about CSRF vulnerability if the token is stored in the browser's local storage.</span></span> <span data-ttu-id="87ed2-166">CSRF est un problème lorsque le jeton est stocké dans un cookie.</span><span class="sxs-lookup"><span data-stu-id="87ed2-166">CSRF is a concern when the token is stored in a cookie.</span></span> <span data-ttu-id="87ed2-167">Pour plus d’informations, consultez l’exemple de code GitHub problème [Spa ajoute deux cookies](https://github.com/dotnet/AspNetCore.Docs/issues/13369).</span><span class="sxs-lookup"><span data-stu-id="87ed2-167">For more information, see the GitHub issue [SPA code sample adds two cookies](https://github.com/dotnet/AspNetCore.Docs/issues/13369).</span></span>

### <a name="multiple-apps-hosted-at-one-domain"></a><span data-ttu-id="87ed2-168">Plusieurs applications hébergées sur un domaine</span><span class="sxs-lookup"><span data-stu-id="87ed2-168">Multiple apps hosted at one domain</span></span>

<span data-ttu-id="87ed2-169">Les environnements d’hébergement partagés sont vulnérables au détournement de session, à la CSRF de connexion et à d’autres attaques.</span><span class="sxs-lookup"><span data-stu-id="87ed2-169">Shared hosting environments are vulnerable to session hijacking, login CSRF, and other attacks.</span></span>

<span data-ttu-id="87ed2-170">Bien que `example1.contoso.net` et `example2.contoso.net` soient des hôtes différents, il existe une relation d’approbation implicite entre les hôtes sous le `*.contoso.net` domaine.</span><span class="sxs-lookup"><span data-stu-id="87ed2-170">Although `example1.contoso.net` and `example2.contoso.net` are different hosts, there's an implicit trust relationship between hosts under the `*.contoso.net` domain.</span></span> <span data-ttu-id="87ed2-171">Cette relation d’approbation implicite permet aux hôtes potentiellement non fiables d’affecter les cookies de l’autre (les stratégies de même origine qui régissent les demandes AJAX ne s’appliquent pas nécessairement aux cookies HTTP).</span><span class="sxs-lookup"><span data-stu-id="87ed2-171">This implicit trust relationship allows potentially untrusted hosts to affect each other's cookies (the same-origin policies that govern AJAX requests don't necessarily apply to HTTP cookies).</span></span>

<span data-ttu-id="87ed2-172">Les attaques qui exploitent des cookies approuvés entre des applications hébergées sur le même domaine peuvent être évitées en ne partageant pas de domaines.</span><span class="sxs-lookup"><span data-stu-id="87ed2-172">Attacks that exploit trusted cookies between apps hosted on the same domain can be prevented by not sharing domains.</span></span> <span data-ttu-id="87ed2-173">Lorsque chaque application est hébergée sur son propre domaine, il n’existe aucune relation d’approbation de cookie implicite à exploiter.</span><span class="sxs-lookup"><span data-stu-id="87ed2-173">When each app is hosted on its own domain, there is no implicit cookie trust relationship to exploit.</span></span>

## <a name="aspnet-core-antiforgery-configuration"></a><span data-ttu-id="87ed2-174">Configuration de l’anti-contrefaçon ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="87ed2-174">ASP.NET Core antiforgery configuration</span></span>

> [!WARNING]
> <span data-ttu-id="87ed2-175">ASP.NET Core implémente l’anti-falsification à l’aide de la [protection des données ASP.net Core](xref:security/data-protection/introduction).</span><span class="sxs-lookup"><span data-stu-id="87ed2-175">ASP.NET Core implements antiforgery using [ASP.NET Core Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="87ed2-176">La pile de protection des données doit être configurée pour fonctionner dans une batterie de serveurs.</span><span class="sxs-lookup"><span data-stu-id="87ed2-176">The data protection stack must be configured to work in a server farm.</span></span> <span data-ttu-id="87ed2-177">Pour plus d’informations, consultez Configuration de la [protection des données](xref:security/data-protection/configuration/overview) .</span><span class="sxs-lookup"><span data-stu-id="87ed2-177">See [Configuring data protection](xref:security/data-protection/configuration/overview) for more information.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="87ed2-178">L’intergiciel anti-contrefaçon est ajouté au conteneur d' [injection de dépendances](xref:fundamentals/dependency-injection) lorsque l’une des API suivantes est appelée dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="87ed2-178">Antiforgery middleware is added to the [Dependency injection](xref:fundamentals/dependency-injection) container when one of the following APIs is called in `Startup.ConfigureServices`:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*>
* <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapRazorPages*>
* <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>
* <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub*>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="87ed2-179">Un intergiciel anti-contrefaçon est ajouté au conteneur d' [injection de dépendances](xref:fundamentals/dependency-injection) lorsque <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> est appelé dans`Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="87ed2-179">Antiforgery middleware is added to the [Dependency injection](xref:fundamentals/dependency-injection) container when <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> is called in `Startup.ConfigureServices`</span></span>

::: moniker-end

<span data-ttu-id="87ed2-180">Dans ASP.NET Core 2,0 ou version ultérieure, [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injecte des jetons anti-contrefaçon dans des éléments de formulaire HTML.</span><span class="sxs-lookup"><span data-stu-id="87ed2-180">In ASP.NET Core 2.0 or later, the [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injects antiforgery tokens into HTML form elements.</span></span> <span data-ttu-id="87ed2-181">Le balisage suivant dans un Razor fichier génère automatiquement des jetons anti-contrefaçon :</span><span class="sxs-lookup"><span data-stu-id="87ed2-181">The following markup in a Razor file automatically generates antiforgery tokens:</span></span>

```cshtml
<form method="post">
    ...
</form>
```

<span data-ttu-id="87ed2-182">De même, [IHtmlHelper. BeginForm](/dotnet/api/microsoft.aspnetcore.mvc.rendering.ihtmlhelper.beginform) génère des jetons anti-contrefaçon par défaut si la méthode du formulaire n’est pas obtenue.</span><span class="sxs-lookup"><span data-stu-id="87ed2-182">Similarly, [IHtmlHelper.BeginForm](/dotnet/api/microsoft.aspnetcore.mvc.rendering.ihtmlhelper.beginform) generates antiforgery tokens by default if the form's method isn't GET.</span></span>

<span data-ttu-id="87ed2-183">La génération automatique de jetons anti-contrefaçon pour les éléments de formulaire HTML se produit lorsque la `<form>` balise contient l' `method="post"` attribut et que l’une des conditions suivantes est vraie :</span><span class="sxs-lookup"><span data-stu-id="87ed2-183">The automatic generation of antiforgery tokens for HTML form elements happens when the `<form>` tag contains the `method="post"` attribute and either of the following are true:</span></span>

* <span data-ttu-id="87ed2-184">L’attribut action est vide ( `action=""` ).</span><span class="sxs-lookup"><span data-stu-id="87ed2-184">The action attribute is empty (`action=""`).</span></span>
* <span data-ttu-id="87ed2-185">L’attribut action n’est pas fourni ( `<form method="post">` ).</span><span class="sxs-lookup"><span data-stu-id="87ed2-185">The action attribute isn't supplied (`<form method="post">`).</span></span>

<span data-ttu-id="87ed2-186">La génération automatique de jetons anti-contrefaçon pour les éléments de formulaire HTML peut être désactivée :</span><span class="sxs-lookup"><span data-stu-id="87ed2-186">Automatic generation of antiforgery tokens for HTML form elements can be disabled:</span></span>

* <span data-ttu-id="87ed2-187">Désactivez explicitement les jetons anti-contrefaçon avec l' `asp-antiforgery` attribut :</span><span class="sxs-lookup"><span data-stu-id="87ed2-187">Explicitly disable antiforgery tokens with the `asp-antiforgery` attribute:</span></span>

  ```cshtml
  <form method="post" asp-antiforgery="false">
      ...
  </form>
  ```

* <span data-ttu-id="87ed2-188">L’élément de formulaire est exclu du balisage tag à l’aide du tag Helper [! symbol opt-out](xref:mvc/views/tag-helpers/intro#opt-out):</span><span class="sxs-lookup"><span data-stu-id="87ed2-188">The form element is opted-out of Tag Helpers by using the Tag Helper [! opt-out symbol](xref:mvc/views/tag-helpers/intro#opt-out):</span></span>

  ```cshtml
  <!form method="post">
      ...
  </!form>
  ```

* <span data-ttu-id="87ed2-189">Supprimez `FormTagHelper` de la vue.</span><span class="sxs-lookup"><span data-stu-id="87ed2-189">Remove the `FormTagHelper` from the view.</span></span> <span data-ttu-id="87ed2-190">Le `FormTagHelper` peut être supprimé d’une vue en ajoutant la directive suivante à la Razor vue :</span><span class="sxs-lookup"><span data-stu-id="87ed2-190">The `FormTagHelper` can be removed from a view by adding the following directive to the Razor view:</span></span>

  ```cshtml
  @removeTagHelper Microsoft.AspNetCore.Mvc.TagHelpers.FormTagHelper, Microsoft.AspNetCore.Mvc.TagHelpers
  ```

> [!NOTE]
> <span data-ttu-id="87ed2-191">Les [ Razor pages](xref:razor-pages/index) sont automatiquement protégées à partir de XSRF/CSRF.</span><span class="sxs-lookup"><span data-stu-id="87ed2-191">[Razor Pages](xref:razor-pages/index) are automatically protected from XSRF/CSRF.</span></span> <span data-ttu-id="87ed2-192">Pour plus d’informations, consultez [XSRF/CSRF et Razor pages](xref:razor-pages/index#xsrf).</span><span class="sxs-lookup"><span data-stu-id="87ed2-192">For more information, see [XSRF/CSRF and Razor Pages](xref:razor-pages/index#xsrf).</span></span>

<span data-ttu-id="87ed2-193">L’approche la plus courante pour la protection contre les attaques CSRF consiste à utiliser le *modèle de jeton du synchronisateur* (STP).</span><span class="sxs-lookup"><span data-stu-id="87ed2-193">The most common approach to defending against CSRF attacks is to use the *Synchronizer Token Pattern* (STP).</span></span> <span data-ttu-id="87ed2-194">Le protocole STP est utilisé lorsque l’utilisateur demande une page avec des données de formulaire :</span><span class="sxs-lookup"><span data-stu-id="87ed2-194">STP is used when the user requests a page with form data:</span></span>

1. <span data-ttu-id="87ed2-195">Le serveur envoie un jeton associé à l’identité de l’utilisateur actuel au client.</span><span class="sxs-lookup"><span data-stu-id="87ed2-195">The server sends a token associated with the current user's identity to the client.</span></span>
1. <span data-ttu-id="87ed2-196">Le client renvoie le jeton au serveur pour vérification.</span><span class="sxs-lookup"><span data-stu-id="87ed2-196">The client sends back the token to the server for verification.</span></span>
1. <span data-ttu-id="87ed2-197">Si le serveur reçoit un jeton qui ne correspond pas à l’identité de l’utilisateur authentifié, la demande est rejetée.</span><span class="sxs-lookup"><span data-stu-id="87ed2-197">If the server receives a token that doesn't match the authenticated user's identity, the request is rejected.</span></span>

<span data-ttu-id="87ed2-198">Le jeton est unique et imprévisible.</span><span class="sxs-lookup"><span data-stu-id="87ed2-198">The token is unique and unpredictable.</span></span> <span data-ttu-id="87ed2-199">Le jeton peut également être utilisé pour garantir le séquencement correct d’une série de requêtes (par exemple, en vérifiant la séquence de demande de : page 1 > page 2 > page 3).</span><span class="sxs-lookup"><span data-stu-id="87ed2-199">The token can also be used to ensure proper sequencing of a series of requests (for example, ensuring the request sequence of: page 1 > page 2 > page 3).</span></span> <span data-ttu-id="87ed2-200">Tous les formulaires dans ASP.NET Core modèles MVC et Razor pages génèrent des jetons anti-contrefaçon.</span><span class="sxs-lookup"><span data-stu-id="87ed2-200">All of the forms in ASP.NET Core MVC and Razor Pages templates generate antiforgery tokens.</span></span> <span data-ttu-id="87ed2-201">Les deux exemples de vue suivants génèrent des jetons anti-contrefaçon :</span><span class="sxs-lookup"><span data-stu-id="87ed2-201">The following pair of view examples generate antiforgery tokens:</span></span>

```cshtml
<form asp-controller="Manage" asp-action="ChangePassword" method="post">
    ...
</form>

@using (Html.BeginForm("ChangePassword", "Manage"))
{
    ...
}
```

<span data-ttu-id="87ed2-202">Ajoutez explicitement un jeton anti-contrefaçon à un `<form>` élément sans utiliser de balise tag avec le programme d’assistance HTML [`@Html.AntiForgeryToken`](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.htmlhelper.antiforgerytoken) :</span><span class="sxs-lookup"><span data-stu-id="87ed2-202">Explicitly add an antiforgery token to a `<form>` element without using Tag Helpers with the HTML helper [`@Html.AntiForgeryToken`](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.htmlhelper.antiforgerytoken):</span></span>

```cshtml
<form action="/" method="post">
    @Html.AntiForgeryToken()
</form>
```

<span data-ttu-id="87ed2-203">Dans chacun des cas précédents, ASP.NET Core ajoute un champ de formulaire masqué semblable au suivant :</span><span class="sxs-lookup"><span data-stu-id="87ed2-203">In each of the preceding cases, ASP.NET Core adds a hidden form field similar to the following:</span></span>

```cshtml
<input name="__RequestVerificationToken" type="hidden" value="CfDJ8NrAkS ... s2-m9Yw">
```

<span data-ttu-id="87ed2-204">ASP.NET Core comprend trois [filtres](xref:mvc/controllers/filters) pour l’utilisation des jetons anti-contrefaçon :</span><span class="sxs-lookup"><span data-stu-id="87ed2-204">ASP.NET Core includes three [filters](xref:mvc/controllers/filters) for working with antiforgery tokens:</span></span>

* [<span data-ttu-id="87ed2-205">ValidateAntiForgeryToken</span><span class="sxs-lookup"><span data-stu-id="87ed2-205">ValidateAntiForgeryToken</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute)
* [<span data-ttu-id="87ed2-206">AutoValidateAntiforgeryToken</span><span class="sxs-lookup"><span data-stu-id="87ed2-206">AutoValidateAntiforgeryToken</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute)
* [<span data-ttu-id="87ed2-207">IgnoreAntiforgeryToken</span><span class="sxs-lookup"><span data-stu-id="87ed2-207">IgnoreAntiforgeryToken</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute)

## <a name="antiforgery-options"></a><span data-ttu-id="87ed2-208">Options anti-contrefaçon</span><span class="sxs-lookup"><span data-stu-id="87ed2-208">Antiforgery options</span></span>

<span data-ttu-id="87ed2-209">Personnaliser les options anti- [contrefaçon](/dotnet/api/Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions) dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="87ed2-209">Customize [antiforgery options](/dotnet/api/Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions) in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
services.AddAntiforgery(options => 
{
    // Set Cookie properties using CookieBuilder properties†.
    options.FormFieldName = "AntiforgeryFieldname";
    options.HeaderName = "X-CSRF-TOKEN-HEADERNAME";
    options.SuppressXFrameOptionsHeader = false;
});
```

<span data-ttu-id="87ed2-210">&dagger;Définissez les propriétés anti-contrefaçon `Cookie` à l’aide des propriétés de la classe [CookieBuilder](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder) .</span><span class="sxs-lookup"><span data-stu-id="87ed2-210">&dagger;Set the antiforgery `Cookie` properties using the properties of the [CookieBuilder](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder) class.</span></span>

| <span data-ttu-id="87ed2-211">Option</span><span class="sxs-lookup"><span data-stu-id="87ed2-211">Option</span></span> | <span data-ttu-id="87ed2-212">Description</span><span class="sxs-lookup"><span data-stu-id="87ed2-212">Description</span></span> |
| ---
<span data-ttu-id="87ed2-213">titre : Auteur : Description : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="87ed2-213">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="87ed2-214">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="87ed2-214">'Blazor'</span></span>
- <span data-ttu-id="87ed2-215">'Identity'</span><span class="sxs-lookup"><span data-stu-id="87ed2-215">'Identity'</span></span>
- <span data-ttu-id="87ed2-216">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="87ed2-216">'Let's Encrypt'</span></span>
- <span data-ttu-id="87ed2-217">'Razor'</span><span class="sxs-lookup"><span data-stu-id="87ed2-217">'Razor'</span></span>
- <span data-ttu-id="87ed2-218">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="87ed2-218">'SignalR' uid:</span></span> 

<span data-ttu-id="87ed2-219">--- | titre de--- : Auteur : Description : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="87ed2-219">--- | --- title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="87ed2-220">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="87ed2-220">'Blazor'</span></span>
- <span data-ttu-id="87ed2-221">'Identity'</span><span class="sxs-lookup"><span data-stu-id="87ed2-221">'Identity'</span></span>
- <span data-ttu-id="87ed2-222">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="87ed2-222">'Let's Encrypt'</span></span>
- <span data-ttu-id="87ed2-223">'Razor'</span><span class="sxs-lookup"><span data-stu-id="87ed2-223">'Razor'</span></span>
- <span data-ttu-id="87ed2-224">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="87ed2-224">'SignalR' uid:</span></span> 

-
<span data-ttu-id="87ed2-225">titre : Auteur : Description : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="87ed2-225">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="87ed2-226">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="87ed2-226">'Blazor'</span></span>
- <span data-ttu-id="87ed2-227">'Identity'</span><span class="sxs-lookup"><span data-stu-id="87ed2-227">'Identity'</span></span>
- <span data-ttu-id="87ed2-228">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="87ed2-228">'Let's Encrypt'</span></span>
- <span data-ttu-id="87ed2-229">'Razor'</span><span class="sxs-lookup"><span data-stu-id="87ed2-229">'Razor'</span></span>
- <span data-ttu-id="87ed2-230">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="87ed2-230">'SignalR' uid:</span></span> 

-
<span data-ttu-id="87ed2-231">titre : Auteur : Description : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="87ed2-231">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="87ed2-232">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="87ed2-232">'Blazor'</span></span>
- <span data-ttu-id="87ed2-233">'Identity'</span><span class="sxs-lookup"><span data-stu-id="87ed2-233">'Identity'</span></span>
- <span data-ttu-id="87ed2-234">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="87ed2-234">'Let's Encrypt'</span></span>
- <span data-ttu-id="87ed2-235">'Razor'</span><span class="sxs-lookup"><span data-stu-id="87ed2-235">'Razor'</span></span>
- <span data-ttu-id="87ed2-236">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="87ed2-236">'SignalR' uid:</span></span> 

<span data-ttu-id="87ed2-237">------ | | [Cookie](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | Détermine les paramètres utilisés pour créer les cookies anti-contrefaçon.</span><span class="sxs-lookup"><span data-stu-id="87ed2-237">------ | | [Cookie](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | Determines the settings used to create the antiforgery cookies.</span></span> <span data-ttu-id="87ed2-238">| | [FormFieldName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | Nom du champ de formulaire masqué utilisé par le système anti-contrefaçon pour le rendu des jetons anti-contrefaçon dans les vues.</span><span class="sxs-lookup"><span data-stu-id="87ed2-238">| | [FormFieldName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | The name of the hidden form field used by the antiforgery system to render antiforgery tokens in views.</span></span> <span data-ttu-id="87ed2-239">| | [HeaderName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | Nom de l’en-tête utilisé par le système anti-contrefaçon.</span><span class="sxs-lookup"><span data-stu-id="87ed2-239">| | [HeaderName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | The name of the header used by the antiforgery system.</span></span> <span data-ttu-id="87ed2-240">Si `null` la condition est, le système considère uniquement les données de formulaire.</span><span class="sxs-lookup"><span data-stu-id="87ed2-240">If `null`, the system considers only form data.</span></span> <span data-ttu-id="87ed2-241">| | [SuppressXFrameOptionsHeader](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | Spécifie s’il faut supprimer la génération de l' `X-Frame-Options` en-tête.</span><span class="sxs-lookup"><span data-stu-id="87ed2-241">| | [SuppressXFrameOptionsHeader](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | Specifies whether to suppress generation of the `X-Frame-Options` header.</span></span> <span data-ttu-id="87ed2-242">Par défaut, l’en-tête est généré avec la valeur « SAMEORIGIN ».</span><span class="sxs-lookup"><span data-stu-id="87ed2-242">By default, the header is generated with a value of "SAMEORIGIN".</span></span> <span data-ttu-id="87ed2-243">La valeur par défaut est `false`.</span><span class="sxs-lookup"><span data-stu-id="87ed2-243">Defaults to `false`.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
services.AddAntiforgery(options => 
{
    options.CookieDomain = "contoso.com";
    options.CookieName = "X-CSRF-TOKEN-COOKIENAME";
    options.CookiePath = "Path";
    options.FormFieldName = "AntiforgeryFieldname";
    options.HeaderName = "X-CSRF-TOKEN-HEADERNAME";
    options.RequireSsl = false;
    options.SuppressXFrameOptionsHeader = false;
});
```

| <span data-ttu-id="87ed2-244">Option</span><span class="sxs-lookup"><span data-stu-id="87ed2-244">Option</span></span> | <span data-ttu-id="87ed2-245">Description</span><span class="sxs-lookup"><span data-stu-id="87ed2-245">Description</span></span> |
| ---
<span data-ttu-id="87ed2-246">titre : Auteur : Description : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="87ed2-246">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="87ed2-247">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="87ed2-247">'Blazor'</span></span>
- <span data-ttu-id="87ed2-248">'Identity'</span><span class="sxs-lookup"><span data-stu-id="87ed2-248">'Identity'</span></span>
- <span data-ttu-id="87ed2-249">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="87ed2-249">'Let's Encrypt'</span></span>
- <span data-ttu-id="87ed2-250">'Razor'</span><span class="sxs-lookup"><span data-stu-id="87ed2-250">'Razor'</span></span>
- <span data-ttu-id="87ed2-251">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="87ed2-251">'SignalR' uid:</span></span> 

<span data-ttu-id="87ed2-252">--- | titre de--- : Auteur : Description : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="87ed2-252">--- | --- title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="87ed2-253">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="87ed2-253">'Blazor'</span></span>
- <span data-ttu-id="87ed2-254">'Identity'</span><span class="sxs-lookup"><span data-stu-id="87ed2-254">'Identity'</span></span>
- <span data-ttu-id="87ed2-255">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="87ed2-255">'Let's Encrypt'</span></span>
- <span data-ttu-id="87ed2-256">'Razor'</span><span class="sxs-lookup"><span data-stu-id="87ed2-256">'Razor'</span></span>
- <span data-ttu-id="87ed2-257">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="87ed2-257">'SignalR' uid:</span></span> 

-
<span data-ttu-id="87ed2-258">titre : Auteur : Description : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="87ed2-258">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="87ed2-259">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="87ed2-259">'Blazor'</span></span>
- <span data-ttu-id="87ed2-260">'Identity'</span><span class="sxs-lookup"><span data-stu-id="87ed2-260">'Identity'</span></span>
- <span data-ttu-id="87ed2-261">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="87ed2-261">'Let's Encrypt'</span></span>
- <span data-ttu-id="87ed2-262">'Razor'</span><span class="sxs-lookup"><span data-stu-id="87ed2-262">'Razor'</span></span>
- <span data-ttu-id="87ed2-263">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="87ed2-263">'SignalR' uid:</span></span> 

-
<span data-ttu-id="87ed2-264">titre : Auteur : Description : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="87ed2-264">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="87ed2-265">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="87ed2-265">'Blazor'</span></span>
- <span data-ttu-id="87ed2-266">'Identity'</span><span class="sxs-lookup"><span data-stu-id="87ed2-266">'Identity'</span></span>
- <span data-ttu-id="87ed2-267">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="87ed2-267">'Let's Encrypt'</span></span>
- <span data-ttu-id="87ed2-268">'Razor'</span><span class="sxs-lookup"><span data-stu-id="87ed2-268">'Razor'</span></span>
- <span data-ttu-id="87ed2-269">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="87ed2-269">'SignalR' uid:</span></span> 

<span data-ttu-id="87ed2-270">------ | | [Cookie](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | Détermine les paramètres utilisés pour créer les cookies anti-contrefaçon.</span><span class="sxs-lookup"><span data-stu-id="87ed2-270">------ | | [Cookie](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | Determines the settings used to create the antiforgery cookies.</span></span> <span data-ttu-id="87ed2-271">| | [CookieDomain](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiedomain) | Domaine du cookie.</span><span class="sxs-lookup"><span data-stu-id="87ed2-271">| | [CookieDomain](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiedomain) | The domain of the cookie.</span></span> <span data-ttu-id="87ed2-272">La valeur par défaut est `null`.</span><span class="sxs-lookup"><span data-stu-id="87ed2-272">Defaults to `null`.</span></span> <span data-ttu-id="87ed2-273">Cette propriété est obsolète et sera supprimée dans une version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="87ed2-273">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="87ed2-274">L’alternative recommandée est cookie. domain.</span><span class="sxs-lookup"><span data-stu-id="87ed2-274">The recommended alternative is Cookie.Domain.</span></span> <span data-ttu-id="87ed2-275">| | [CookieName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiename) | Nom du cookie.</span><span class="sxs-lookup"><span data-stu-id="87ed2-275">| | [CookieName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiename) | The name of the cookie.</span></span> <span data-ttu-id="87ed2-276">Si la valeur n’est pas définie, le système génère un nom unique à partir de [DefaultCookiePrefix](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.defaultcookieprefix) (». AspNetCore. anti-contrefaçon.»).</span><span class="sxs-lookup"><span data-stu-id="87ed2-276">If not set, the system generates a unique name beginning with the [DefaultCookiePrefix](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.defaultcookieprefix) (".AspNetCore.Antiforgery.").</span></span> <span data-ttu-id="87ed2-277">Cette propriété est obsolète et sera supprimée dans une version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="87ed2-277">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="87ed2-278">L’alternative recommandée est Cookie.Name.</span><span class="sxs-lookup"><span data-stu-id="87ed2-278">The recommended alternative is Cookie.Name.</span></span> <span data-ttu-id="87ed2-279">| | [CookiePath](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiepath) | Chemin d’accès défini sur le cookie.</span><span class="sxs-lookup"><span data-stu-id="87ed2-279">| | [CookiePath](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiepath) | The path set on the cookie.</span></span> <span data-ttu-id="87ed2-280">Cette propriété est obsolète et sera supprimée dans une version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="87ed2-280">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="87ed2-281">L’alternative recommandée est cookie. Path.</span><span class="sxs-lookup"><span data-stu-id="87ed2-281">The recommended alternative is Cookie.Path.</span></span> <span data-ttu-id="87ed2-282">| | [FormFieldName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | Nom du champ de formulaire masqué utilisé par le système anti-contrefaçon pour le rendu des jetons anti-contrefaçon dans les vues.</span><span class="sxs-lookup"><span data-stu-id="87ed2-282">| | [FormFieldName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | The name of the hidden form field used by the antiforgery system to render antiforgery tokens in views.</span></span> <span data-ttu-id="87ed2-283">| | [HeaderName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | Nom de l’en-tête utilisé par le système anti-contrefaçon.</span><span class="sxs-lookup"><span data-stu-id="87ed2-283">| | [HeaderName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | The name of the header used by the antiforgery system.</span></span> <span data-ttu-id="87ed2-284">Si `null` la condition est, le système considère uniquement les données de formulaire.</span><span class="sxs-lookup"><span data-stu-id="87ed2-284">If `null`, the system considers only form data.</span></span> <span data-ttu-id="87ed2-285">| | [RequireSSL](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.requiressl) | Spécifie si le système anti-contrefaçon exige le protocole HTTPs.</span><span class="sxs-lookup"><span data-stu-id="87ed2-285">| | [RequireSsl](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.requiressl) | Specifies whether HTTPS is required by the antiforgery system.</span></span> <span data-ttu-id="87ed2-286">Si la `true` , les demandes non-HTTPS échouent.</span><span class="sxs-lookup"><span data-stu-id="87ed2-286">If `true`, non-HTTPS requests fail.</span></span> <span data-ttu-id="87ed2-287">La valeur par défaut est `false`.</span><span class="sxs-lookup"><span data-stu-id="87ed2-287">Defaults to `false`.</span></span> <span data-ttu-id="87ed2-288">Cette propriété est obsolète et sera supprimée dans une version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="87ed2-288">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="87ed2-289">L’alternative recommandée consiste à définir cookie. SecurePolicy.</span><span class="sxs-lookup"><span data-stu-id="87ed2-289">The recommended alternative is to set Cookie.SecurePolicy.</span></span> <span data-ttu-id="87ed2-290">| | [SuppressXFrameOptionsHeader](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | Spécifie s’il faut supprimer la génération de l' `X-Frame-Options` en-tête.</span><span class="sxs-lookup"><span data-stu-id="87ed2-290">| | [SuppressXFrameOptionsHeader](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | Specifies whether to suppress generation of the `X-Frame-Options` header.</span></span> <span data-ttu-id="87ed2-291">Par défaut, l’en-tête est généré avec la valeur « SAMEORIGIN ».</span><span class="sxs-lookup"><span data-stu-id="87ed2-291">By default, the header is generated with a value of "SAMEORIGIN".</span></span> <span data-ttu-id="87ed2-292">La valeur par défaut est `false`.</span><span class="sxs-lookup"><span data-stu-id="87ed2-292">Defaults to `false`.</span></span> |

::: moniker-end

<span data-ttu-id="87ed2-293">Pour plus d’informations, consultez [CookieAuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.CookieAuthenticationOptions).</span><span class="sxs-lookup"><span data-stu-id="87ed2-293">For more information, see [CookieAuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.CookieAuthenticationOptions).</span></span>

## <a name="configure-antiforgery-features-with-iantiforgery"></a><span data-ttu-id="87ed2-294">Configurer des fonctionnalités anti-contrefaçon avec IAntiforgery</span><span class="sxs-lookup"><span data-stu-id="87ed2-294">Configure antiforgery features with IAntiforgery</span></span>

<span data-ttu-id="87ed2-295">[IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) fournit l’API pour configurer les fonctionnalités anti-contrefaçon.</span><span class="sxs-lookup"><span data-stu-id="87ed2-295">[IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) provides the API to configure antiforgery features.</span></span> <span data-ttu-id="87ed2-296">`IAntiforgery`peut être demandé dans la `Configure` méthode de la `Startup` classe.</span><span class="sxs-lookup"><span data-stu-id="87ed2-296">`IAntiforgery` can be requested in the `Configure` method of the `Startup` class.</span></span> <span data-ttu-id="87ed2-297">L’exemple suivant utilise l’intergiciel (middleware) de la page d’hébergement de l’application pour générer un jeton anti-contrefaçon et l’envoyer dans la réponse en tant que cookie (à l’aide de la Convention d’affectation de noms angulaire par défaut décrite plus loin dans cette rubrique) :</span><span class="sxs-lookup"><span data-stu-id="87ed2-297">The following example uses middleware from the app's home page to generate an antiforgery token and send it in the response as a cookie (using the default Angular naming convention described later in this topic):</span></span>

```csharp
public void Configure(IApplicationBuilder app, IAntiforgery antiforgery)
{
    app.Use(next => context =>
    {
        string path = context.Request.Path.Value;

        if (
            string.Equals(path, "/", StringComparison.OrdinalIgnoreCase) ||
            string.Equals(path, "/index.html", StringComparison.OrdinalIgnoreCase))
        {
            // The request token can be sent as a JavaScript-readable cookie, 
            // and Angular uses it by default.
            var tokens = antiforgery.GetAndStoreTokens(context);
            context.Response.Cookies.Append("XSRF-TOKEN", tokens.RequestToken, 
                new CookieOptions() { HttpOnly = false });
        }

        return next(context);
    });
}
```

### <a name="require-antiforgery-validation"></a><span data-ttu-id="87ed2-298">Exiger une validation anti-contrefaçon</span><span class="sxs-lookup"><span data-stu-id="87ed2-298">Require antiforgery validation</span></span>

<span data-ttu-id="87ed2-299">[ValidateAntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute) est un filtre d’action qui peut être appliqué à une action individuelle, à un contrôleur ou globalement.</span><span class="sxs-lookup"><span data-stu-id="87ed2-299">[ValidateAntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute) is an action filter that can be applied to an individual action, a controller, or globally.</span></span> <span data-ttu-id="87ed2-300">Les demandes adressées aux actions auxquelles ce filtre est appliqué sont bloquées, sauf si la demande comprend un jeton anti-contrefaçon valide.</span><span class="sxs-lookup"><span data-stu-id="87ed2-300">Requests made to actions that have this filter applied are blocked unless the request includes a valid antiforgery token.</span></span>

```csharp
[HttpPost]
[ValidateAntiForgeryToken]
public async Task<IActionResult> RemoveLogin(RemoveLoginViewModel account)
{
    ManageMessageId? message = ManageMessageId.Error;
    var user = await GetCurrentUserAsync();

    if (user != null)
    {
        var result = 
            await _userManager.RemoveLoginAsync(
                user, account.LoginProvider, account.ProviderKey);

        if (result.Succeeded)
        {
            await _signInManager.SignInAsync(user, isPersistent: false);
            message = ManageMessageId.RemoveLoginSuccess;
        }
    }

    return RedirectToAction(nameof(ManageLogins), new { Message = message });
}
```

<span data-ttu-id="87ed2-301">L' `ValidateAntiForgeryToken` attribut requiert un jeton pour les demandes aux méthodes d’action qu’il marque, y compris les requêtes http d’extraction.</span><span class="sxs-lookup"><span data-stu-id="87ed2-301">The `ValidateAntiForgeryToken` attribute requires a token for requests to the action methods it marks, including HTTP GET requests.</span></span> <span data-ttu-id="87ed2-302">Si l' `ValidateAntiForgeryToken` attribut est appliqué sur les contrôleurs de l’application, il peut être substitué par `IgnoreAntiforgeryToken` l’attribut.</span><span class="sxs-lookup"><span data-stu-id="87ed2-302">If the `ValidateAntiForgeryToken` attribute is applied across the app's controllers, it can be overridden with the `IgnoreAntiforgeryToken` attribute.</span></span>

> [!NOTE]
> <span data-ttu-id="87ed2-303">ASP.NET Core ne prend pas en charge l’ajout de jetons anti-contrefaçon pour la récupération automatique des demandes.</span><span class="sxs-lookup"><span data-stu-id="87ed2-303">ASP.NET Core doesn't support adding antiforgery tokens to GET requests automatically.</span></span>

### <a name="automatically-validate-antiforgery-tokens-for-unsafe-http-methods-only"></a><span data-ttu-id="87ed2-304">Valider automatiquement les jetons anti-contrefaçon pour les méthodes HTTP non sécurisées uniquement</span><span class="sxs-lookup"><span data-stu-id="87ed2-304">Automatically validate antiforgery tokens for unsafe HTTP methods only</span></span>

<span data-ttu-id="87ed2-305">ASP.NET Core applications ne génèrent pas de jetons anti-contrefaçon pour les méthodes HTTP sécurisées (obtenir, tête, OPTIONS et TRACE).</span><span class="sxs-lookup"><span data-stu-id="87ed2-305">ASP.NET Core apps don't generate antiforgery tokens for safe HTTP methods (GET, HEAD, OPTIONS, and TRACE).</span></span> <span data-ttu-id="87ed2-306">Au lieu d’appliquer globalement l' `ValidateAntiForgeryToken` attribut, puis de le remplacer par des `IgnoreAntiforgeryToken` attributs, l’attribut [AutoValidateAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute) peut être utilisé.</span><span class="sxs-lookup"><span data-stu-id="87ed2-306">Instead of broadly applying the `ValidateAntiForgeryToken` attribute and then overriding it with `IgnoreAntiforgeryToken` attributes, the [AutoValidateAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute) attribute can be used.</span></span> <span data-ttu-id="87ed2-307">Cet attribut fonctionne de la même façon `ValidateAntiForgeryToken` que l’attribut, sauf qu’il ne requiert pas de jetons pour les demandes effectuées à l’aide des méthodes http suivantes :</span><span class="sxs-lookup"><span data-stu-id="87ed2-307">This attribute works identically to the `ValidateAntiForgeryToken` attribute, except that it doesn't require tokens for requests made using the following HTTP methods:</span></span>

* <span data-ttu-id="87ed2-308">GET</span><span class="sxs-lookup"><span data-stu-id="87ed2-308">GET</span></span>
* <span data-ttu-id="87ed2-309">HEAD</span><span class="sxs-lookup"><span data-stu-id="87ed2-309">HEAD</span></span>
* <span data-ttu-id="87ed2-310">OPTIONS</span><span class="sxs-lookup"><span data-stu-id="87ed2-310">OPTIONS</span></span>
* <span data-ttu-id="87ed2-311">TRACE</span><span class="sxs-lookup"><span data-stu-id="87ed2-311">TRACE</span></span>

<span data-ttu-id="87ed2-312">Nous vous recommandons d’utiliser `AutoValidateAntiforgeryToken` large pour les scénarios non-API.</span><span class="sxs-lookup"><span data-stu-id="87ed2-312">We recommend use of `AutoValidateAntiforgeryToken` broadly for non-API scenarios.</span></span> <span data-ttu-id="87ed2-313">Cela garantit que les actions de publication sont protégées par défaut.</span><span class="sxs-lookup"><span data-stu-id="87ed2-313">This ensures POST actions are protected by default.</span></span> <span data-ttu-id="87ed2-314">L’alternative consiste à ignorer les jetons anti-contrefaçon par défaut, sauf si `ValidateAntiForgeryToken` est appliqué à des méthodes d’action individuelles.</span><span class="sxs-lookup"><span data-stu-id="87ed2-314">The alternative is to ignore antiforgery tokens by default, unless `ValidateAntiForgeryToken` is applied to individual action methods.</span></span> <span data-ttu-id="87ed2-315">Dans ce scénario, il est plus probable qu’une méthode d’action de publication reste non protégée par erreur, laissant l’application vulnérable aux attaques CSRF.</span><span class="sxs-lookup"><span data-stu-id="87ed2-315">It's more likely in this scenario for a POST action method to be left unprotected by mistake, leaving the app vulnerable to CSRF attacks.</span></span> <span data-ttu-id="87ed2-316">Toutes les publications doivent envoyer le jeton anti-contrefaçon.</span><span class="sxs-lookup"><span data-stu-id="87ed2-316">All POSTs should send the antiforgery token.</span></span>

<span data-ttu-id="87ed2-317">Les API ne disposent pas d’un mécanisme automatique pour l’envoi de la partie non-cookie du jeton.</span><span class="sxs-lookup"><span data-stu-id="87ed2-317">APIs don't have an automatic mechanism for sending the non-cookie part of the token.</span></span> <span data-ttu-id="87ed2-318">L’implémentation dépend probablement de l’implémentation du code client.</span><span class="sxs-lookup"><span data-stu-id="87ed2-318">The implementation probably depends on the client code implementation.</span></span> <span data-ttu-id="87ed2-319">Voici quelques exemples :</span><span class="sxs-lookup"><span data-stu-id="87ed2-319">Some examples are shown below:</span></span>

<span data-ttu-id="87ed2-320">Exemple de niveau de classe :</span><span class="sxs-lookup"><span data-stu-id="87ed2-320">Class-level example:</span></span>

```csharp
[Authorize]
[AutoValidateAntiforgeryToken]
public class ManageController : Controller
{
```

<span data-ttu-id="87ed2-321">Exemple global :</span><span class="sxs-lookup"><span data-stu-id="87ed2-321">Global example:</span></span>

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="87ed2-322">services. AddMvc (options = options de>. Filters. Add (New AutoValidateAntiforgeryTokenAttribute ()));</span><span class="sxs-lookup"><span data-stu-id="87ed2-322">services.AddMvc(options =>     options.Filters.Add(new AutoValidateAntiforgeryTokenAttribute()));</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

```csharp
services.AddControllersWithViews(options =>
    options.Filters.Add(new AutoValidateAntiforgeryTokenAttribute()));
```

::: moniker-end

### <a name="override-global-or-controller-antiforgery-attributes"></a><span data-ttu-id="87ed2-323">Remplacer les attributs globaux ou anti-contrefaçon de contrôleur</span><span class="sxs-lookup"><span data-stu-id="87ed2-323">Override global or controller antiforgery attributes</span></span>

<span data-ttu-id="87ed2-324">Le filtre [IgnoreAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute) est utilisé pour éliminer la nécessité d’un jeton anti-contrefaçon pour une action donnée (ou un contrôleur).</span><span class="sxs-lookup"><span data-stu-id="87ed2-324">The [IgnoreAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute) filter is used to eliminate the need for an antiforgery token for a given action (or controller).</span></span> <span data-ttu-id="87ed2-325">Lorsqu’il est appliqué, ce filtre remplace `ValidateAntiForgeryToken` les `AutoValidateAntiforgeryToken` filtres et spécifiés à un niveau supérieur (globalement ou sur un contrôleur).</span><span class="sxs-lookup"><span data-stu-id="87ed2-325">When applied, this filter overrides `ValidateAntiForgeryToken` and `AutoValidateAntiforgeryToken` filters specified at a higher level (globally or on a controller).</span></span>

```csharp
[Authorize]
[AutoValidateAntiforgeryToken]
public class ManageController : Controller
{
    [HttpPost]
    [IgnoreAntiforgeryToken]
    public async Task<IActionResult> DoSomethingSafe(SomeViewModel model)
    {
        // no antiforgery token required
    }
}
```

## <a name="refresh-tokens-after-authentication"></a><span data-ttu-id="87ed2-326">Actualiser les jetons après l’authentification</span><span class="sxs-lookup"><span data-stu-id="87ed2-326">Refresh tokens after authentication</span></span>

<span data-ttu-id="87ed2-327">Les jetons doivent être actualisés une fois l’utilisateur authentifié, en redirigeant l’utilisateur vers une page d’affichage ou de Razor pages.</span><span class="sxs-lookup"><span data-stu-id="87ed2-327">Tokens should be refreshed after the user is authenticated by redirecting the user to a view or Razor Pages page.</span></span>

## <a name="javascript-ajax-and-spas"></a><span data-ttu-id="87ed2-328">JavaScript, AJAX et SPAs</span><span class="sxs-lookup"><span data-stu-id="87ed2-328">JavaScript, AJAX, and SPAs</span></span>

<span data-ttu-id="87ed2-329">Dans les applications HTML traditionnelles, les jetons anti-contrefaçon sont transmis au serveur à l’aide de champs de formulaire masqués.</span><span class="sxs-lookup"><span data-stu-id="87ed2-329">In traditional HTML-based apps, antiforgery tokens are passed to the server using hidden form fields.</span></span> <span data-ttu-id="87ed2-330">Dans les applications basées sur JavaScript modernes et en SPAs, de nombreuses demandes sont effectuées par programme.</span><span class="sxs-lookup"><span data-stu-id="87ed2-330">In modern JavaScript-based apps and SPAs, many requests are made programmatically.</span></span> <span data-ttu-id="87ed2-331">Ces demandes AJAX peuvent utiliser d’autres techniques (comme les en-têtes de demande ou les cookies) pour envoyer le jeton.</span><span class="sxs-lookup"><span data-stu-id="87ed2-331">These AJAX requests may use other techniques (such as request headers or cookies) to send the token.</span></span>

<span data-ttu-id="87ed2-332">Si les cookies sont utilisés pour stocker les jetons d’authentification et pour authentifier les demandes d’API sur le serveur, CSRF est un problème potentiel.</span><span class="sxs-lookup"><span data-stu-id="87ed2-332">If cookies are used to store authentication tokens and to authenticate API requests on the server, CSRF is a potential problem.</span></span> <span data-ttu-id="87ed2-333">Si le stockage local est utilisé pour stocker le jeton, la vulnérabilité CSRF peut être atténuée car les valeurs du stockage local ne sont pas envoyées automatiquement au serveur avec chaque demande.</span><span class="sxs-lookup"><span data-stu-id="87ed2-333">If local storage is used to store the token, CSRF vulnerability might be mitigated because values from local storage aren't sent automatically to the server with every request.</span></span> <span data-ttu-id="87ed2-334">Par conséquent, l’utilisation du stockage local pour stocker le jeton anti-contrefaçon sur le client et l’envoi du jeton en tant qu’en-tête de demande est une approche recommandée.</span><span class="sxs-lookup"><span data-stu-id="87ed2-334">Thus, using local storage to store the antiforgery token on the client and sending the token as a request header is a recommended approach.</span></span>

### <a name="javascript"></a><span data-ttu-id="87ed2-335">JavaScript</span><span class="sxs-lookup"><span data-stu-id="87ed2-335">JavaScript</span></span>

<span data-ttu-id="87ed2-336">À l’aide de JavaScript avec des vues, le jeton peut être créé à l’aide d’un service à partir de la vue.</span><span class="sxs-lookup"><span data-stu-id="87ed2-336">Using JavaScript with views, the token can be created using a service from within the view.</span></span> <span data-ttu-id="87ed2-337">Injectez le service [Microsoft. AspNetCore. anticontrefaçon. IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) dans la vue et appelez [GetAndStoreTokens](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery.getandstoretokens):</span><span class="sxs-lookup"><span data-stu-id="87ed2-337">Inject the [Microsoft.AspNetCore.Antiforgery.IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) service into the view and call [GetAndStoreTokens](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery.getandstoretokens):</span></span>

[!code-csharp[](anti-request-forgery/sample/MvcSample/Views/Home/Ajax.cshtml?highlight=4-10,12-13,35-36)]

<span data-ttu-id="87ed2-338">Cette approche élimine le besoin de traiter directement les cookies du serveur ou de les lire à partir du client.</span><span class="sxs-lookup"><span data-stu-id="87ed2-338">This approach eliminates the need to deal directly with setting cookies from the server or reading them from the client.</span></span>

<span data-ttu-id="87ed2-339">L’exemple précédent utilise JavaScript pour lire la valeur du champ masqué pour l’en-tête de publication AJAX.</span><span class="sxs-lookup"><span data-stu-id="87ed2-339">The preceding example uses JavaScript to read the hidden field value for the AJAX POST header.</span></span>

<span data-ttu-id="87ed2-340">JavaScript peut également accéder à des jetons dans des cookies et utiliser le contenu du cookie pour créer un en-tête avec la valeur du jeton.</span><span class="sxs-lookup"><span data-stu-id="87ed2-340">JavaScript can also access tokens in cookies and use the cookie's contents to create a header with the token's value.</span></span>

```csharp
context.Response.Cookies.Append("CSRF-TOKEN", tokens.RequestToken, 
    new Microsoft.AspNetCore.Http.CookieOptions { HttpOnly = false });
```

<span data-ttu-id="87ed2-341">En supposant que le script demande à envoyer le jeton dans un en-tête appelé `X-CSRF-TOKEN` , configurez le service anti-contrefaçon pour Rechercher l' `X-CSRF-TOKEN` en-tête :</span><span class="sxs-lookup"><span data-stu-id="87ed2-341">Assuming the script requests to send the token in a header called `X-CSRF-TOKEN`, configure the antiforgery service to look for the `X-CSRF-TOKEN` header:</span></span>

```csharp
services.AddAntiforgery(options => options.HeaderName = "X-CSRF-TOKEN");
```

<span data-ttu-id="87ed2-342">L’exemple suivant utilise JavaScript pour effectuer une demande AJAX avec l’en-tête approprié :</span><span class="sxs-lookup"><span data-stu-id="87ed2-342">The following example uses JavaScript to make an AJAX request with the appropriate header:</span></span>

```javascript
function getCookie(cname) {
    var name = cname + "=";
    var decodedCookie = decodeURIComponent(document.cookie);
    var ca = decodedCookie.split(';');
    for(var i = 0; i <ca.length; i++) {
        var c = ca[i];
        while (c.charAt(0) == ' ') {
            c = c.substring(1);
        }
        if (c.indexOf(name) == 0) {
            return c.substring(name.length, c.length);
        }
    }
    return "";
}

var csrfToken = getCookie("CSRF-TOKEN");

var xhttp = new XMLHttpRequest();
xhttp.onreadystatechange = function() {
    if (xhttp.readyState == XMLHttpRequest.DONE) {
        if (xhttp.status == 200) {
            alert(xhttp.responseText);
        } else {
            alert('There was an error processing the AJAX request.');
        }
    }
};
xhttp.open('POST', '/api/password/changepassword', true);
xhttp.setRequestHeader("Content-type", "application/json");
xhttp.setRequestHeader("X-CSRF-TOKEN", csrfToken);
xhttp.send(JSON.stringify({ "newPassword": "ReallySecurePassword999$$$" }));
```

### <a name="angularjs"></a><span data-ttu-id="87ed2-343">AngularJS</span><span class="sxs-lookup"><span data-stu-id="87ed2-343">AngularJS</span></span>

<span data-ttu-id="87ed2-344">AngularJS utilise une convention pour traiter CSRF.</span><span class="sxs-lookup"><span data-stu-id="87ed2-344">AngularJS uses a convention to address CSRF.</span></span> <span data-ttu-id="87ed2-345">Si le serveur envoie un cookie avec le nom `XSRF-TOKEN` , le `$http` service AngularJS ajoute la valeur de cookie à un en-tête lorsqu’il envoie une demande au serveur.</span><span class="sxs-lookup"><span data-stu-id="87ed2-345">If the server sends a cookie with the name `XSRF-TOKEN`, the AngularJS `$http` service adds the cookie value to a header when it sends a request to the server.</span></span> <span data-ttu-id="87ed2-346">Ce processus est automatique.</span><span class="sxs-lookup"><span data-stu-id="87ed2-346">This process is automatic.</span></span> <span data-ttu-id="87ed2-347">L’en-tête n’a pas besoin d’être défini explicitement dans le client.</span><span class="sxs-lookup"><span data-stu-id="87ed2-347">The header doesn't need to be set in the client explicitly.</span></span> <span data-ttu-id="87ed2-348">Le nom d’en-tête est `X-XSRF-TOKEN` .</span><span class="sxs-lookup"><span data-stu-id="87ed2-348">The header name is `X-XSRF-TOKEN`.</span></span> <span data-ttu-id="87ed2-349">Le serveur doit détecter cet en-tête et valider son contenu.</span><span class="sxs-lookup"><span data-stu-id="87ed2-349">The server should detect this header and validate its contents.</span></span>

<span data-ttu-id="87ed2-350">Pour que ASP.NET Core API fonctionne avec cette Convention au démarrage de votre application :</span><span class="sxs-lookup"><span data-stu-id="87ed2-350">For ASP.NET Core API to work with this convention in your application startup:</span></span>

* <span data-ttu-id="87ed2-351">Configurez votre application pour fournir un jeton dans un cookie appelé `XSRF-TOKEN` .</span><span class="sxs-lookup"><span data-stu-id="87ed2-351">Configure your app to provide a token in a cookie called `XSRF-TOKEN`.</span></span>
* <span data-ttu-id="87ed2-352">Configurez le service anti-contrefaçon pour rechercher un en-tête nommé `X-XSRF-TOKEN` .</span><span class="sxs-lookup"><span data-stu-id="87ed2-352">Configure the antiforgery service to look for a header named `X-XSRF-TOKEN`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IAntiforgery antiforgery)
{
    app.Use(next => context =>
    {
        string path = context.Request.Path.Value;

        if (
            string.Equals(path, "/", StringComparison.OrdinalIgnoreCase) ||
            string.Equals(path, "/index.html", StringComparison.OrdinalIgnoreCase))
        {
            // The request token can be sent as a JavaScript-readable cookie, 
            // and Angular uses it by default.
            var tokens = antiforgery.GetAndStoreTokens(context);
            context.Response.Cookies.Append("XSRF-TOKEN", tokens.RequestToken, 
                new CookieOptions() { HttpOnly = false });
        }

        return next(context);
    });
}

public void ConfigureServices(IServiceCollection services)
{
    // Angular's default header name for sending the XSRF token.
    services.AddAntiforgery(options => options.HeaderName = "X-XSRF-TOKEN");
}
```

<span data-ttu-id="87ed2-353">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/anti-request-forgery/sample/AngularSample) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="87ed2-353">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/anti-request-forgery/sample/AngularSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="extend-antiforgery"></a><span data-ttu-id="87ed2-354">Étendre l’anti-contrefaçon</span><span class="sxs-lookup"><span data-stu-id="87ed2-354">Extend antiforgery</span></span>

<span data-ttu-id="87ed2-355">Le type [IAntiForgeryAdditionalDataProvider](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider) permet aux développeurs d’étendre le comportement du système anti-CSRF en arrondissant les données supplémentaires dans chaque jeton.</span><span class="sxs-lookup"><span data-stu-id="87ed2-355">The [IAntiForgeryAdditionalDataProvider](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider) type allows developers to extend the behavior of the anti-CSRF system by round-tripping additional data in each token.</span></span> <span data-ttu-id="87ed2-356">La méthode [GetAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.getadditionaldata) est appelée chaque fois qu’un jeton de champ est généré, et la valeur de retour est incorporée dans le jeton généré.</span><span class="sxs-lookup"><span data-stu-id="87ed2-356">The [GetAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.getadditionaldata) method is called each time a field token is generated, and the return value is embedded within the generated token.</span></span> <span data-ttu-id="87ed2-357">Un responsable de l’implémentation peut retourner un horodateur, une valeur à usage unique ou toute autre valeur, puis appeler [ValidateAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.validateadditionaldata) pour valider ces données lors de la validation du jeton.</span><span class="sxs-lookup"><span data-stu-id="87ed2-357">An implementer could return a timestamp, a nonce, or any other value and then call [ValidateAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.validateadditionaldata) to validate this data when the token is validated.</span></span> <span data-ttu-id="87ed2-358">Le nom d’utilisateur du client étant déjà incorporé dans les jetons générés, il n’est pas nécessaire d’inclure ces informations.</span><span class="sxs-lookup"><span data-stu-id="87ed2-358">The client's username is already embedded in the generated tokens, so there's no need to include this information.</span></span> <span data-ttu-id="87ed2-359">Si un jeton contient des données supplémentaires, mais qu’aucun `IAntiForgeryAdditionalDataProvider` n’est configuré, les données supplémentaires ne sont pas validées.</span><span class="sxs-lookup"><span data-stu-id="87ed2-359">If a token includes supplemental data but no `IAntiForgeryAdditionalDataProvider` is configured, the supplemental data isn't validated.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="87ed2-360">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="87ed2-360">Additional resources</span></span>

* <span data-ttu-id="87ed2-361">[CSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) sur le projet OWASP ( [Open Web Application Security](https://www.owasp.org/index.php/Main_Page) ).</span><span class="sxs-lookup"><span data-stu-id="87ed2-361">[CSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) on [Open Web Application Security Project](https://www.owasp.org/index.php/Main_Page) (OWASP).</span></span>
* <xref:host-and-deploy/web-farm>
