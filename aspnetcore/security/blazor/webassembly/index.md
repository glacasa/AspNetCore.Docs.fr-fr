---
title: Sécuriser ASP.NET WebAssembly Core Blazor
author: guardrex
description: Apprenez à Blazor sécuriser les applications WebAssemlby sous forme d’applications à page unique (APE).
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/31/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/index
ms.openlocfilehash: be286d770cd8d6e5cf7885b91be8654f74ffd743
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80538970"
---
# <a name="secure-aspnet-core-opno-locblazor-webassembly"></a><span data-ttu-id="70f60-103">Sécuriser ASP.NET WebAssembly Core Blazor</span><span class="sxs-lookup"><span data-stu-id="70f60-103">Secure ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="70f60-104">Par [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="70f60-104">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Blazor<span data-ttu-id="70f60-105">Les applications WebAssembly sont sécurisées de la même manière que les applications à une page unique (APE).</span><span class="sxs-lookup"><span data-stu-id="70f60-105"> WebAssembly apps are secured in the same manner as Single Page Applications (SPAs).</span></span> <span data-ttu-id="70f60-106">Il existe plusieurs approches pour authentifier les utilisateurs aux SPAs, mais l’approche la plus courante et la plus globale est d’utiliser une implémentation basée sur le [protocole OAuth 2.0](https://oauth.net/), comme [Open ID Connect (OIDC)](https://openid.net/connect/).</span><span class="sxs-lookup"><span data-stu-id="70f60-106">There are several approaches for authenticating users to SPAs, but the most common and comprehensive approach is to use an implementation based on the [OAuth 2.0 protocol](https://oauth.net/), such as [Open ID Connect (OIDC)](https://openid.net/connect/).</span></span>

## <a name="authentication-library"></a><span data-ttu-id="70f60-107">Bibliothèque d’authentification</span><span class="sxs-lookup"><span data-stu-id="70f60-107">Authentication library</span></span>

Blazor<span data-ttu-id="70f60-108">WebAssembly prend en charge l’authentification `Microsoft.AspNetCore.Components.WebAssembly.Authentication` et l’autorisation des applications à l’aide d’OIDC via la bibliothèque.</span><span class="sxs-lookup"><span data-stu-id="70f60-108"> WebAssembly supports authenticating and authorizing apps using OIDC via the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library.</span></span> <span data-ttu-id="70f60-109">La bibliothèque fournit un ensemble de primitifs pour l’authentification transparente contre ASP.NET backends Core.</span><span class="sxs-lookup"><span data-stu-id="70f60-109">The library provides a set of primitives for seamlessly authenticating against ASP.NET Core backends.</span></span> <span data-ttu-id="70f60-110">La bibliothèque intègre ASP.NET’identité de base avec le support d’autorisation API construit sur le serveur [d’identité](https://identityserver.io/).</span><span class="sxs-lookup"><span data-stu-id="70f60-110">The library integrates ASP.NET Core Identity with API authorization support built on top of [Identity Server](https://identityserver.io/).</span></span> <span data-ttu-id="70f60-111">La bibliothèque peut s’authentifier par rapport à tout fournisseur d’identité tiers (IP) qui prend en charge OIDC, qui sont appelés fournisseurs OpenID (OP).</span><span class="sxs-lookup"><span data-stu-id="70f60-111">The library can authenticate against any third-party Identity Provider (IP) that supports OIDC, which are called OpenID Providers (OP).</span></span>

<span data-ttu-id="70f60-112">Le support d’authentification de Blazor WebAssembly est construit au-dessus de la bibliothèque *oidc-client.js,* qui est utilisée pour gérer les détails sous-jacents du protocole d’authentification.</span><span class="sxs-lookup"><span data-stu-id="70f60-112">The authentication support in Blazor WebAssembly is built on top of the *oidc-client.js* library, which is used to handle the underlying authentication protocol details.</span></span>

<span data-ttu-id="70f60-113">D’autres options pour authentifier les APE existent, comme l’utilisation de cookies SameSite.</span><span class="sxs-lookup"><span data-stu-id="70f60-113">Other options for authenticating SPAs exist, such as the use of SameSite cookies.</span></span> <span data-ttu-id="70f60-114">Cependant, la conception Blazor d’ingénierie de WebAssembly est réglée sur OAuth Blazor et OIDC comme la meilleure option pour l’authentification dans les applications WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="70f60-114">However, the engineering design of Blazor WebAssembly is settled on OAuth and OIDC as the best option for authentication in Blazor WebAssembly apps.</span></span> <span data-ttu-id="70f60-115">[L’authentification basée](xref:security/anti-request-forgery#token-based-authentication) sur le jeton basé sur [JSON Web Tokens (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) a été choisie plutôt que [l’authentification basée sur les cookies](xref:security/anti-request-forgery#cookie-based-authentication) pour des raisons fonctionnelles et de sécurité :</span><span class="sxs-lookup"><span data-stu-id="70f60-115">[Token-based authentication](xref:security/anti-request-forgery#token-based-authentication) based on [JSON Web Tokens (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) was chosen over [cookie-based authentication](xref:security/anti-request-forgery#cookie-based-authentication) for functional and security reasons:</span></span>

* <span data-ttu-id="70f60-116">L’utilisation d’un protocole basé sur les jetons offre une surface d’attaque plus petite, car les jetons ne sont pas envoyés dans toutes les demandes.</span><span class="sxs-lookup"><span data-stu-id="70f60-116">Using a token-based protocol offers a smaller attack surface area, as the tokens aren't sent in all requests.</span></span>
* <span data-ttu-id="70f60-117">Les paramètres du serveur ne nécessitent pas de protection contre [la falsification de la demande de cross-Site (CSRF)](xref:security/anti-request-forgery) parce que les jetons sont envoyés explicitement.</span><span class="sxs-lookup"><span data-stu-id="70f60-117">Server endpoints don't require protection against [Cross-Site Request Forgery (CSRF)](xref:security/anti-request-forgery) because the tokens are sent explicitly.</span></span> <span data-ttu-id="70f60-118">Cela vous permet Blazor d’héberger des applications WebAssembly aux côtés des pages MVC ou Razor.</span><span class="sxs-lookup"><span data-stu-id="70f60-118">This allows you to host Blazor WebAssembly apps alongside MVC or Razor pages apps.</span></span>
* <span data-ttu-id="70f60-119">Les jetons ont des autorisations plus étroites que les cookies.</span><span class="sxs-lookup"><span data-stu-id="70f60-119">Tokens have narrower permissions than cookies.</span></span> <span data-ttu-id="70f60-120">Par exemple, les jetons ne peuvent pas être utilisés pour gérer le compte utilisateur ou modifier le mot de passe d’un utilisateur à moins qu’une telle fonctionnalité ne soit explicitement implémentée.</span><span class="sxs-lookup"><span data-stu-id="70f60-120">For example, tokens can't be used to manage the user account or change a user's password unless such functionality is explicitly implemented.</span></span>
* <span data-ttu-id="70f60-121">Les jetons ont une courte durée de vie, une heure par défaut, ce qui limite la fenêtre d’attaque.</span><span class="sxs-lookup"><span data-stu-id="70f60-121">Tokens have a short lifetime, one hour by default, which limits the attack window.</span></span> <span data-ttu-id="70f60-122">Les jetons peuvent également être révoqués à tout moment.</span><span class="sxs-lookup"><span data-stu-id="70f60-122">Tokens can also be revoked at any time.</span></span>
* <span data-ttu-id="70f60-123">Les JTS autonomes offrent des garanties au client et au serveur sur le processus d’authentification.</span><span class="sxs-lookup"><span data-stu-id="70f60-123">Self-contained JWTs offer guarantees to the client and server about the authentication process.</span></span> <span data-ttu-id="70f60-124">Par exemple, un client a les moyens de détecter et de valider que les jetons qu’il reçoit sont légitimes et ont été émis dans le cadre d’un processus d’authentification donné.</span><span class="sxs-lookup"><span data-stu-id="70f60-124">For example, a client has the means to detect and validate that the tokens it receives are legitimate and were emitted as part of a given authentication process.</span></span> <span data-ttu-id="70f60-125">Si un tiers tente de changer un jeton au milieu du processus d’authentification, le client peut détecter le jeton commuté et éviter de l’utiliser.</span><span class="sxs-lookup"><span data-stu-id="70f60-125">If a third party attempts to switch a token in the middle of the authentication process, the client can detect the switched token and avoid using it.</span></span>
* <span data-ttu-id="70f60-126">Les jetons avec OAuth et OIDC ne comptent pas sur l’agent utilisateur se comporter correctement pour s’assurer que l’application est sécurisée.</span><span class="sxs-lookup"><span data-stu-id="70f60-126">Tokens with OAuth and OIDC don't rely on the user agent behaving correctly to ensure that the app is secure.</span></span>
* <span data-ttu-id="70f60-127">Les protocoles basés sur le jeton, tels que OAuth et OIDC, permettent d’authentifier et d’autoriser les applications hébergées et autonomes avec le même ensemble de caractéristiques de sécurité.</span><span class="sxs-lookup"><span data-stu-id="70f60-127">Token-based protocols, such as OAuth and OIDC, allow for authenticating and authorizing hosted and standalone apps with the same set of security characteristics.</span></span>

## <a name="authentication-process-with-oidc"></a><span data-ttu-id="70f60-128">Processus d’authentification avec OIDC</span><span class="sxs-lookup"><span data-stu-id="70f60-128">Authentication process with OIDC</span></span>

<span data-ttu-id="70f60-129">La `Microsoft.AspNetCore.Components.WebAssembly.Authentication` bibliothèque offre plusieurs primitifs pour mettre en œuvre l’authentification et l’autorisation à l’aide d’OIDC.</span><span class="sxs-lookup"><span data-stu-id="70f60-129">The `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library offers several primitives to implement authentication and authorization using OIDC.</span></span> <span data-ttu-id="70f60-130">En termes généraux, l’authentification fonctionne comme suit :</span><span class="sxs-lookup"><span data-stu-id="70f60-130">In broad terms, authentication works as follows:</span></span>

* <span data-ttu-id="70f60-131">Lorsqu’un utilisateur anonyme sélectionne le bouton de [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) connexion ou demande une page avec l’attribut appliqué, l’utilisateur est redirigé vers la page de connexion de l’application (`/authentication/login`).</span><span class="sxs-lookup"><span data-stu-id="70f60-131">When an anonymous user selects the login button or requests a page with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute applied, the user is redirected to the app's login page (`/authentication/login`).</span></span>
* <span data-ttu-id="70f60-132">Dans la page de connexion, la bibliothèque d’authentification se prépare à une redirection vers le critère d’autorisation.</span><span class="sxs-lookup"><span data-stu-id="70f60-132">In the login page, the authentication library prepares for a redirect to the authorization endpoint.</span></span> <span data-ttu-id="70f60-133">Le critère d’autorisation Blazor est en dehors de l’application WebAssembly et peut être hébergé à une origine distincte.</span><span class="sxs-lookup"><span data-stu-id="70f60-133">The authorization endpoint is outside of the Blazor WebAssembly app and can be hosted at a separate origin.</span></span> <span data-ttu-id="70f60-134">Le point de terminaison est chargé de déterminer si l’utilisateur est authentifié et d’émettre un ou plusieurs jetons en réponse.</span><span class="sxs-lookup"><span data-stu-id="70f60-134">The endpoint is responsible for determining whether the user is authenticated and for issuing one or more tokens in response.</span></span> <span data-ttu-id="70f60-135">La bibliothèque d’authentification fournit un rappel de connexion pour recevoir la réponse d’authentification.</span><span class="sxs-lookup"><span data-stu-id="70f60-135">The authentication library provides a login callback to receive the authentication response.</span></span>
  * <span data-ttu-id="70f60-136">Si l’utilisateur n’est pas authentifié, l’utilisateur est redirigé vers le système d’authentification sous-jacent, qui est généralement ASP.NET’identité de base.</span><span class="sxs-lookup"><span data-stu-id="70f60-136">If the user isn't authenticated, the user is redirected to the underlying authentication system, which is usually ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="70f60-137">Si l’utilisateur a déjà été authentifié, le point de terminaison d’autorisation génère`/authentication/login-callback`les jetons appropriés et redirige le navigateur vers le point de terminaison de connexion ( ).</span><span class="sxs-lookup"><span data-stu-id="70f60-137">If the user was already authenticated, the authorization endpoint generates the appropriate tokens and redirects the browser back to the login callback endpoint (`/authentication/login-callback`).</span></span>
* <span data-ttu-id="70f60-138">Lorsque Blazor l’application WebAssembly charge le point`/authentication/login-callback`de terminaison de connexion (), la réponse d’authentification est traitée.</span><span class="sxs-lookup"><span data-stu-id="70f60-138">When the Blazor WebAssembly app loads the login callback endpoint (`/authentication/login-callback`), the authentication response is processed.</span></span>
  * <span data-ttu-id="70f60-139">Si le processus d’authentification se termine avec succès, l’utilisateur est authentifié et envoyé en option à l’URL protégée d’origine demandée par l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="70f60-139">If the authentication process completes successfully, the user is authenticated and optionally sent back to the original protected URL that the user requested.</span></span>
  * <span data-ttu-id="70f60-140">Si le processus d’authentification échoue pour quelque raison`/authentication/login-failed`que ce soit, l’utilisateur est envoyé à la page a échoué à la connexion (), et une erreur s’affiche.</span><span class="sxs-lookup"><span data-stu-id="70f60-140">If the authentication process fails for any reason, the user is sent to the login failed page (`/authentication/login-failed`), and an error is displayed.</span></span>

## <a name="support-prerendering-with-authentication"></a><span data-ttu-id="70f60-141">Prendre en charge le prerendering par l’authentification</span><span class="sxs-lookup"><span data-stu-id="70f60-141">Support prerendering with authentication</span></span>

<span data-ttu-id="70f60-142">Après avoir suivi les conseils Blazor dans l’un des sujets hébergés de l’application WebAssembly, utilisez les instructions suivantes pour créer une application qui :</span><span class="sxs-lookup"><span data-stu-id="70f60-142">After following the guidance in one of the hosted Blazor WebAssembly app topics, use the following instructions to create an app that:</span></span>

* <span data-ttu-id="70f60-143">Prerenders chemins pour lesquels l’autorisation n’est pas nécessaire.</span><span class="sxs-lookup"><span data-stu-id="70f60-143">Prerenders paths for which authorization isn't required.</span></span>
* <span data-ttu-id="70f60-144">Ne pré-prerender chemins pour lesquels l’autorisation est requise.</span><span class="sxs-lookup"><span data-stu-id="70f60-144">Doesn't prerender paths for which authorization is required.</span></span>

<span data-ttu-id="70f60-145">Dans la classe `Program` de l’application Client *(Program.cs*), tenez compte des enregistrements `ConfigureCommonServices`de services communs dans une méthode distincte (par exemple, )</span><span class="sxs-lookup"><span data-stu-id="70f60-145">In the Client app's `Program` class (*Program.cs*), factor common service registrations into a separate method (for example, `ConfigureCommonServices`):</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.RootComponents.Add<App>("app");

        services.AddBaseAddressHttpClient();
        services.Add...;

        ConfigureCommonServices(builder.Services);

        await builder.Build().RunAsync();
    }

    public static void ConfigureCommonServices(IServiceCollection services)
    {
        // Common service registrations
    }
}
```

<span data-ttu-id="70f60-146">Dans l’application `Startup.ConfigureServices`Server, enregistrez les services supplémentaires suivants :</span><span class="sxs-lookup"><span data-stu-id="70f60-146">In the Server app's `Startup.ConfigureServices`, register the following additional services:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Server;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddRazorPages();
    services.AddScoped<AuthenticationStateProvider, ServerAuthenticationStateProvider>();
    services.AddScoped<SignOutSessionStateManager>();

    Client.Program.ConfigureCommonServices(services);
}
```

<span data-ttu-id="70f60-147">Dans la méthode `Startup.Configure` de l’application Server, remplacez par `endpoints.MapFallbackToFile("index.html")` `endpoints.MapFallbackToPage("/_Host")`:</span><span class="sxs-lookup"><span data-stu-id="70f60-147">In the Server app's `Startup.Configure` method, replace `endpoints.MapFallbackToFile("index.html")` with `endpoints.MapFallbackToPage("/_Host")`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

<span data-ttu-id="70f60-148">Dans l’application Server, créez un dossier *Pages* s’il n’existe pas.</span><span class="sxs-lookup"><span data-stu-id="70f60-148">In the Server app, create a *Pages* folder if it doesn't exist.</span></span> <span data-ttu-id="70f60-149">Créez une page *_Host.cshtml* à l’intérieur du dossier *Pages* de l’application Server.</span><span class="sxs-lookup"><span data-stu-id="70f60-149">Create a *_Host.cshtml* page inside the Server app's *Pages* folder.</span></span> <span data-ttu-id="70f60-150">Collez le contenu du fichier *wwwroot/index.html* de l’application Client dans le fichier *Pages/_Host.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="70f60-150">Paste the contents from the Client app's *wwwroot/index.html* file into the *Pages/_Host.cshtml* file.</span></span> <span data-ttu-id="70f60-151">Mettre à jour le contenu du fichier :</span><span class="sxs-lookup"><span data-stu-id="70f60-151">Update the file's contents:</span></span>

* <span data-ttu-id="70f60-152">Ajoutez `@page "_Host"` en haut du fichier.</span><span class="sxs-lookup"><span data-stu-id="70f60-152">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="70f60-153">Remplacer `<app>Loading...</app>` l’étiquette par ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="70f60-153">Replace the `<app>Loading...</app>` tag with the following:</span></span>

  ```cshtml
  <app>
      @if (!HttpContext.Request.Path.StartsWithSegments("/authentication"))
      {
          <component type="typeof(Wasm.Authentication.Client.App)" render-mode="Static" />
      }
      else
      {
          <text>Loading...</text>
      }
  </app>
  ```
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a><span data-ttu-id="70f60-154">Options pour les applications hébergées et les fournisseurs de connexion tiers</span><span class="sxs-lookup"><span data-stu-id="70f60-154">Options for hosted apps and third-party login providers</span></span>

<span data-ttu-id="70f60-155">Lors de l’authentification et de l’autorisation d’une application WebAssembly hébergée Blazor avec un fournisseur tiers, il existe plusieurs options disponibles pour authentifier l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="70f60-155">When authenticating and authorizing a hosted Blazor WebAssembly app with a third-party provider, there are several options available for authenticating the user.</span></span> <span data-ttu-id="70f60-156">Celui que vous choisissez dépend de votre scénario.</span><span class="sxs-lookup"><span data-stu-id="70f60-156">Which one you choose depends on your scenario.</span></span>

<span data-ttu-id="70f60-157">Pour plus d’informations, consultez <xref:security/authentication/social/additional-claims>.</span><span class="sxs-lookup"><span data-stu-id="70f60-157">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a><span data-ttu-id="70f60-158">Authentifier les utilisateurs pour appeler uniquement des API tiers protégés</span><span class="sxs-lookup"><span data-stu-id="70f60-158">Authenticate users to only call protected third party APIs</span></span>

<span data-ttu-id="70f60-159">Authentifier l’utilisateur avec un flux OAuth côté client par rapport au fournisseur D’API tiers :</span><span class="sxs-lookup"><span data-stu-id="70f60-159">Authenticate the user with a client-side OAuth flow against the third-party API provider:</span></span>

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 <span data-ttu-id="70f60-160">Dans ce scénario :</span><span class="sxs-lookup"><span data-stu-id="70f60-160">In this scenario:</span></span>

* <span data-ttu-id="70f60-161">Le serveur hébergeant l’application ne joue pas un rôle.</span><span class="sxs-lookup"><span data-stu-id="70f60-161">The server hosting the app doesn't play a role.</span></span>
* <span data-ttu-id="70f60-162">Les API sur le serveur ne peuvent pas être protégés.</span><span class="sxs-lookup"><span data-stu-id="70f60-162">APIs on the server can't be protected.</span></span>
* <span data-ttu-id="70f60-163">L’application ne peut appeler que des API tiers protégés.</span><span class="sxs-lookup"><span data-stu-id="70f60-163">The app can only call protected third-party APIs.</span></span>

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a><span data-ttu-id="70f60-164">Authentifier les utilisateurs avec un fournisseur tiers et appeler des API protégées sur le serveur hôte et le tiers</span><span class="sxs-lookup"><span data-stu-id="70f60-164">Authenticate users with a third-party provider and call protected APIs on the host server and the third party</span></span>

<span data-ttu-id="70f60-165">Configurer l’identité avec un fournisseur de connexion tiers.</span><span class="sxs-lookup"><span data-stu-id="70f60-165">Configure Identity with a third-party login provider.</span></span> <span data-ttu-id="70f60-166">Obtenez les jetons requis pour l’accès à l’API par des tiers et entreposez-les.</span><span class="sxs-lookup"><span data-stu-id="70f60-166">Obtain the tokens required for third-party API access and store them.</span></span>

<span data-ttu-id="70f60-167">Lorsqu’un utilisateur se connecte, Identity recueille l’accès et rafraîchit les jetons dans le cadre du processus d’authentification.</span><span class="sxs-lookup"><span data-stu-id="70f60-167">When a user logs in, Identity collects access and refresh tokens as part of the authentication process.</span></span> <span data-ttu-id="70f60-168">À ce stade, il existe quelques approches disponibles pour faire des appels API à des API tiers.</span><span class="sxs-lookup"><span data-stu-id="70f60-168">At that point, there are a couple of approaches available for making API calls to third-party APIs.</span></span>

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a><span data-ttu-id="70f60-169">Utilisez un jeton d’accès au serveur pour récupérer le jeton d’accès tiers</span><span class="sxs-lookup"><span data-stu-id="70f60-169">Use a server access token to retrieve the third-party access token</span></span>

<span data-ttu-id="70f60-170">Utilisez le jeton d’accès généré sur le serveur pour récupérer le jeton d’accès tiers à partir d’un point de terminaison API serveur.</span><span class="sxs-lookup"><span data-stu-id="70f60-170">Use the access token generated on the server to retrieve the third-party access token from a server API endpoint.</span></span> <span data-ttu-id="70f60-171">À partir de là, utilisez le jeton d’accès tiers pour appeler les ressources de l’API tiers directement à partir d’Identité sur le client.</span><span class="sxs-lookup"><span data-stu-id="70f60-171">From there, use the third-party access token to call third-party API resources directly from Identity on the client.</span></span>

<span data-ttu-id="70f60-172">Nous ne recommandons pas cette approche.</span><span class="sxs-lookup"><span data-stu-id="70f60-172">We don't recommend this approach.</span></span> <span data-ttu-id="70f60-173">Cette approche exige de traiter le jeton d’accès tiers comme s’il était généré pour un client public.</span><span class="sxs-lookup"><span data-stu-id="70f60-173">This approach requires treating the third-party access token as if it were generated for a public client.</span></span> <span data-ttu-id="70f60-174">En termes OAuth, l’application publique n’a pas de secret client parce qu’on ne peut pas faire confiance pour stocker des secrets en toute sécurité, et le jeton d’accès est produit pour un client confidentiel.</span><span class="sxs-lookup"><span data-stu-id="70f60-174">In OAuth terms, the public app doesn't have a client secret because it can't be trusted to store secrets safely, and the access token is produced for a confidential client.</span></span> <span data-ttu-id="70f60-175">Un client confidentiel est un client qui a un secret client et est supposé être en mesure de stocker en toute sécurité des secrets.</span><span class="sxs-lookup"><span data-stu-id="70f60-175">A confidential client is a client that has a client secret and is assumed to be able to safely store secrets.</span></span>

* <span data-ttu-id="70f60-176">Le jeton d’accès tiers pourrait bénéficier d’une portée supplémentaire pour effectuer des opérations sensibles en fonction du fait que le tiers a émis le jeton pour un client plus fiable.</span><span class="sxs-lookup"><span data-stu-id="70f60-176">The third-party access token might be granted additional scopes to perform sensitive operations based on the fact that the third-party emitted the token for a more trusted client.</span></span>
* <span data-ttu-id="70f60-177">De même, les jetons de rafraîchissement ne devraient pas être émis à un client qui n’est pas digne de confiance, car ce faisant donne au client un accès illimité à moins que d’autres restrictions ne soient mises en place.</span><span class="sxs-lookup"><span data-stu-id="70f60-177">Similarly, refresh tokens shouldn't be issued to a client that isn't trusted, as doing so gives the client unlimited access unless other restrictions are put into place.</span></span>

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a><span data-ttu-id="70f60-178">Effectuez des appels aPI du client vers l’API serveur afin d’appeler des API tiers</span><span class="sxs-lookup"><span data-stu-id="70f60-178">Make API calls from the client to the server API in order to call third-party APIs</span></span>

<span data-ttu-id="70f60-179">Faites un appel API du client au serveur API.</span><span class="sxs-lookup"><span data-stu-id="70f60-179">Make an API call from the client to the server API.</span></span> <span data-ttu-id="70f60-180">Sur le serveur, récupérez le jeton d’accès pour la ressource API tierce et émetz n’importe quel appel nécessaire.</span><span class="sxs-lookup"><span data-stu-id="70f60-180">From the server, retrieve the access token for the third-party API resource and issue whatever call is necessary.</span></span>

<span data-ttu-id="70f60-181">Bien que cette approche nécessite un réseau supplémentaire sauter à travers le serveur pour appeler une API tiers, il se traduit finalement par une expérience plus sûre:</span><span class="sxs-lookup"><span data-stu-id="70f60-181">While this approach requires an extra network hop through the server to call a third-party API, it ultimately results in a safer experience:</span></span>

* <span data-ttu-id="70f60-182">Le serveur peut stocker des jetons de rafraîchissement et s’assurer que l’application ne perd pas l’accès aux ressources tierces.</span><span class="sxs-lookup"><span data-stu-id="70f60-182">The server can store refresh tokens and ensure that the app doesn't lose access to third-party resources.</span></span>
* <span data-ttu-id="70f60-183">L’application ne peut pas fuite des jetons d’accès à partir du serveur qui peuvent contenir des autorisations plus sensibles.</span><span class="sxs-lookup"><span data-stu-id="70f60-183">The app can't leak access tokens from the server that might contain more sensitive permissions.</span></span>
