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
# <a name="secure-aspnet-core-opno-locblazor-webassembly"></a>Sécuriser ASP.NET WebAssembly Core Blazor

Par [Javier Calvarro Nelson](https://github.com/javiercn)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

BlazorLes applications WebAssembly sont sécurisées de la même manière que les applications à une page unique (APE). Il existe plusieurs approches pour authentifier les utilisateurs aux SPAs, mais l’approche la plus courante et la plus globale est d’utiliser une implémentation basée sur le [protocole OAuth 2.0](https://oauth.net/), comme [Open ID Connect (OIDC)](https://openid.net/connect/).

## <a name="authentication-library"></a>Bibliothèque d’authentification

BlazorWebAssembly prend en charge l’authentification `Microsoft.AspNetCore.Components.WebAssembly.Authentication` et l’autorisation des applications à l’aide d’OIDC via la bibliothèque. La bibliothèque fournit un ensemble de primitifs pour l’authentification transparente contre ASP.NET backends Core. La bibliothèque intègre ASP.NET’identité de base avec le support d’autorisation API construit sur le serveur [d’identité](https://identityserver.io/). La bibliothèque peut s’authentifier par rapport à tout fournisseur d’identité tiers (IP) qui prend en charge OIDC, qui sont appelés fournisseurs OpenID (OP).

Le support d’authentification de Blazor WebAssembly est construit au-dessus de la bibliothèque *oidc-client.js,* qui est utilisée pour gérer les détails sous-jacents du protocole d’authentification.

D’autres options pour authentifier les APE existent, comme l’utilisation de cookies SameSite. Cependant, la conception Blazor d’ingénierie de WebAssembly est réglée sur OAuth Blazor et OIDC comme la meilleure option pour l’authentification dans les applications WebAssembly. [L’authentification basée](xref:security/anti-request-forgery#token-based-authentication) sur le jeton basé sur [JSON Web Tokens (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) a été choisie plutôt que [l’authentification basée sur les cookies](xref:security/anti-request-forgery#cookie-based-authentication) pour des raisons fonctionnelles et de sécurité :

* L’utilisation d’un protocole basé sur les jetons offre une surface d’attaque plus petite, car les jetons ne sont pas envoyés dans toutes les demandes.
* Les paramètres du serveur ne nécessitent pas de protection contre [la falsification de la demande de cross-Site (CSRF)](xref:security/anti-request-forgery) parce que les jetons sont envoyés explicitement. Cela vous permet Blazor d’héberger des applications WebAssembly aux côtés des pages MVC ou Razor.
* Les jetons ont des autorisations plus étroites que les cookies. Par exemple, les jetons ne peuvent pas être utilisés pour gérer le compte utilisateur ou modifier le mot de passe d’un utilisateur à moins qu’une telle fonctionnalité ne soit explicitement implémentée.
* Les jetons ont une courte durée de vie, une heure par défaut, ce qui limite la fenêtre d’attaque. Les jetons peuvent également être révoqués à tout moment.
* Les JTS autonomes offrent des garanties au client et au serveur sur le processus d’authentification. Par exemple, un client a les moyens de détecter et de valider que les jetons qu’il reçoit sont légitimes et ont été émis dans le cadre d’un processus d’authentification donné. Si un tiers tente de changer un jeton au milieu du processus d’authentification, le client peut détecter le jeton commuté et éviter de l’utiliser.
* Les jetons avec OAuth et OIDC ne comptent pas sur l’agent utilisateur se comporter correctement pour s’assurer que l’application est sécurisée.
* Les protocoles basés sur le jeton, tels que OAuth et OIDC, permettent d’authentifier et d’autoriser les applications hébergées et autonomes avec le même ensemble de caractéristiques de sécurité.

## <a name="authentication-process-with-oidc"></a>Processus d’authentification avec OIDC

La `Microsoft.AspNetCore.Components.WebAssembly.Authentication` bibliothèque offre plusieurs primitifs pour mettre en œuvre l’authentification et l’autorisation à l’aide d’OIDC. En termes généraux, l’authentification fonctionne comme suit :

* Lorsqu’un utilisateur anonyme sélectionne le bouton de [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) connexion ou demande une page avec l’attribut appliqué, l’utilisateur est redirigé vers la page de connexion de l’application (`/authentication/login`).
* Dans la page de connexion, la bibliothèque d’authentification se prépare à une redirection vers le critère d’autorisation. Le critère d’autorisation Blazor est en dehors de l’application WebAssembly et peut être hébergé à une origine distincte. Le point de terminaison est chargé de déterminer si l’utilisateur est authentifié et d’émettre un ou plusieurs jetons en réponse. La bibliothèque d’authentification fournit un rappel de connexion pour recevoir la réponse d’authentification.
  * Si l’utilisateur n’est pas authentifié, l’utilisateur est redirigé vers le système d’authentification sous-jacent, qui est généralement ASP.NET’identité de base.
  * Si l’utilisateur a déjà été authentifié, le point de terminaison d’autorisation génère`/authentication/login-callback`les jetons appropriés et redirige le navigateur vers le point de terminaison de connexion ( ).
* Lorsque Blazor l’application WebAssembly charge le point`/authentication/login-callback`de terminaison de connexion (), la réponse d’authentification est traitée.
  * Si le processus d’authentification se termine avec succès, l’utilisateur est authentifié et envoyé en option à l’URL protégée d’origine demandée par l’utilisateur.
  * Si le processus d’authentification échoue pour quelque raison`/authentication/login-failed`que ce soit, l’utilisateur est envoyé à la page a échoué à la connexion (), et une erreur s’affiche.

## <a name="support-prerendering-with-authentication"></a>Prendre en charge le prerendering par l’authentification

Après avoir suivi les conseils Blazor dans l’un des sujets hébergés de l’application WebAssembly, utilisez les instructions suivantes pour créer une application qui :

* Prerenders chemins pour lesquels l’autorisation n’est pas nécessaire.
* Ne pré-prerender chemins pour lesquels l’autorisation est requise.

Dans la classe `Program` de l’application Client *(Program.cs*), tenez compte des enregistrements `ConfigureCommonServices`de services communs dans une méthode distincte (par exemple, )

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

Dans l’application `Startup.ConfigureServices`Server, enregistrez les services supplémentaires suivants :

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

Dans la méthode `Startup.Configure` de l’application Server, remplacez par `endpoints.MapFallbackToFile("index.html")` `endpoints.MapFallbackToPage("/_Host")`:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

Dans l’application Server, créez un dossier *Pages* s’il n’existe pas. Créez une page *_Host.cshtml* à l’intérieur du dossier *Pages* de l’application Server. Collez le contenu du fichier *wwwroot/index.html* de l’application Client dans le fichier *Pages/_Host.cshtml.* Mettre à jour le contenu du fichier :

* Ajoutez `@page "_Host"` en haut du fichier.
* Remplacer `<app>Loading...</app>` l’étiquette par ce qui suit :

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
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a>Options pour les applications hébergées et les fournisseurs de connexion tiers

Lors de l’authentification et de l’autorisation d’une application WebAssembly hébergée Blazor avec un fournisseur tiers, il existe plusieurs options disponibles pour authentifier l’utilisateur. Celui que vous choisissez dépend de votre scénario.

Pour plus d’informations, consultez <xref:security/authentication/social/additional-claims>.

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a>Authentifier les utilisateurs pour appeler uniquement des API tiers protégés

Authentifier l’utilisateur avec un flux OAuth côté client par rapport au fournisseur D’API tiers :

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 Dans ce scénario :

* Le serveur hébergeant l’application ne joue pas un rôle.
* Les API sur le serveur ne peuvent pas être protégés.
* L’application ne peut appeler que des API tiers protégés.

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a>Authentifier les utilisateurs avec un fournisseur tiers et appeler des API protégées sur le serveur hôte et le tiers

Configurer l’identité avec un fournisseur de connexion tiers. Obtenez les jetons requis pour l’accès à l’API par des tiers et entreposez-les.

Lorsqu’un utilisateur se connecte, Identity recueille l’accès et rafraîchit les jetons dans le cadre du processus d’authentification. À ce stade, il existe quelques approches disponibles pour faire des appels API à des API tiers.

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a>Utilisez un jeton d’accès au serveur pour récupérer le jeton d’accès tiers

Utilisez le jeton d’accès généré sur le serveur pour récupérer le jeton d’accès tiers à partir d’un point de terminaison API serveur. À partir de là, utilisez le jeton d’accès tiers pour appeler les ressources de l’API tiers directement à partir d’Identité sur le client.

Nous ne recommandons pas cette approche. Cette approche exige de traiter le jeton d’accès tiers comme s’il était généré pour un client public. En termes OAuth, l’application publique n’a pas de secret client parce qu’on ne peut pas faire confiance pour stocker des secrets en toute sécurité, et le jeton d’accès est produit pour un client confidentiel. Un client confidentiel est un client qui a un secret client et est supposé être en mesure de stocker en toute sécurité des secrets.

* Le jeton d’accès tiers pourrait bénéficier d’une portée supplémentaire pour effectuer des opérations sensibles en fonction du fait que le tiers a émis le jeton pour un client plus fiable.
* De même, les jetons de rafraîchissement ne devraient pas être émis à un client qui n’est pas digne de confiance, car ce faisant donne au client un accès illimité à moins que d’autres restrictions ne soient mises en place.

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a>Effectuez des appels aPI du client vers l’API serveur afin d’appeler des API tiers

Faites un appel API du client au serveur API. Sur le serveur, récupérez le jeton d’accès pour la ressource API tierce et émetz n’importe quel appel nécessaire.

Bien que cette approche nécessite un réseau supplémentaire sauter à travers le serveur pour appeler une API tiers, il se traduit finalement par une expérience plus sûre:

* Le serveur peut stocker des jetons de rafraîchissement et s’assurer que l’application ne perd pas l’accès aux ressources tierces.
* L’application ne peut pas fuite des jetons d’accès à partir du serveur qui peuvent contenir des autorisations plus sensibles.
