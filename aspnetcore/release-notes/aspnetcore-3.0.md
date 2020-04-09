---
title: Quoi de neuf dans ASP.NET Core 3.0
author: rick-anderson
description: Renseignez-vous sur les nouvelles fonctionnalités dans ASP.NET Core 3.0.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- SignalR
uid: aspnetcore-3.0
ms.openlocfilehash: 4886673a9b16b8be8d9a0b0d5c7002a91760544e
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80976974"
---
# <a name="whats-new-in-aspnet-core-30"></a>Quoi de neuf dans ASP.NET Core 3.0

Cet article met en évidence les changements les plus significatifs dans ASP.NET Core 3.0 avec des liens vers la documentation pertinente.

## Blazor

Blazorest un nouveau cadre dans ASP.NET Core pour la construction d’interface utilisateur web interactive côté client avec .NET:

* Créez des interfaces utilisateur interactives riches à l’aide de C# plutôt que JavaScript.
* Partagez la logique d’application côté serveur et côté client écrite dans .NET.
* Affichez l’interface utilisateur en langage HTML et CSS pour une large prise en charge des navigateurs, y compris les navigateurs mobiles.

Blazorscénarios soutenus par le cadre :

* Composants d’interface utilisateur réutilisables (composants Razor)
* Itinéraire côté client
* Dispositions des composants
* Soutien à l’injection de dépendance
* Formulaires et validation
* Construire des bibliothèques de composants avec des bibliothèques de classe Razor
* Interopérabilité JavaScript

Pour plus d’informations, consultez <xref:blazor/index>.

### <a name="opno-locblazor-server"></a>BlazorServeur

Blazordécouple la logique de rendu des composants de la façon dont les mises à jour de l’interface utilisateur sont appliquées. BlazorServer fournit un support pour l’hébergement des composants Razor sur le serveur dans une application ASP.NET Core. Les mises à jour SignalR de l’interface utilisateur sont traitées sur une connexion. BlazorLe serveur est pris en charge dans ASP.NET Core 3.0.

### <a name="opno-locblazor-webassembly-preview"></a>BlazorWebAssembly (Avant-première)

Blazorles applications peuvent également être exécutées directement dans le navigateur à l’aide d’un temps d’exécution .NET basé sur WebAssembly. BlazorWebAssembly est en avant-première et *n’est pas* pris en charge dans ASP.NET Core 3.0. BlazorWebAssembly sera pris en charge dans une prochaine version de ASP.NET Core.

### <a name="razor-components"></a>Composants de rasoir

Blazorles applications sont construites à partir de composants. Les composants sont des morceaux autonomes de l’interface utilisateur (UI), tels qu’une page, un dialogue ou une forme. Les composants sont des classes normales .NET qui définissent la logique de rendu de l’interface utilisateur et les gestionnaires d’événements côté client. Vous pouvez créer de riches applications web interactives sans JavaScript.

Les composants Blazor sont généralement rédigés à l’aide de la syntaxe Razor, un mélange naturel de HTML et de C. Les composants Razor sont similaires aux vues Razor Pages et MVC en ce sens qu’ils utilisent tous les deux Razor. Contrairement aux pages et aux vues, qui sont basées sur un modèle de réponse aux demandes, les composants sont utilisés spécifiquement pour le traitement de la composition de l’interface utilisateur.

## <a name="grpc"></a>gRPC

[gRPC](https://grpc.io/):

* Est un cadre populaire, haute performance RPC (appel procédure à distance).
* Offre une approche d’opinion du développement de l’API.
* Utilise des technologies modernes telles que :

  * HTTP/2 pour le transport.
  * Protocole Buffers comme langage de description d’interface.
  * Format de sérialisation binaire.
* Fournit des fonctionnalités telles que :

  * Authentification
  * Streaming bidirectionnel et contrôle des flux.
  * Annulation et délais d’attente.

GRPC fonctionnalité dans ASP.NET Core 3.0 comprend:

* [Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) &ndash; An ASP.NET Core framework for hosting gRPC services. gRPC sur ASP.NET Core s’intègre aux caractéristiques standard ASP.NET Core comme l’exploitation forestière, l’injection de dépendance (DI), l’authentification et l’autorisation.
* [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) &ndash; Un client gRPC pour .NET Core `HttpClient`qui s’appuie sur le familier .
* [Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) &ndash; gRPC intégration `HttpClientFactory`client avec .

Pour plus d’informations, consultez <xref:grpc/index>.

## SignalR

Voir [ SignalR le code de mise à jour](xref:migration/22-to-30#signalr) pour les instructions de migration. SignalRutilise `System.Text.Json` maintenant pour sérialiser/déséialiser les messages JSON. Consultez [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson) pour `Newtonsoft.Json`obtenir des instructions pour restaurer le sérialisateur basé sur le site.

Dans le JavaScript et SignalR.NET Clients pour , le support a été ajouté pour la reconnexion automatique. Par défaut, le client tente de se reconnecter immédiatement et de réessayer après 2, 10 et 30 secondes si nécessaire. Si le client se reconnecte avec succès, il reçoit une nouvelle pièce d’identité de connexion. La reconnexion automatique est l’adhésion :

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

Les intervalles de reconnexion peuvent être spécifiés en passant une gamme de durées basées sur la milliseconde :

```javascript
.withAutomaticReconnect([0, 3000, 5000, 10000, 15000, 30000])
//.withAutomaticReconnect([0, 2000, 10000, 30000]) The default intervals.
```

Une implémentation personnalisée peut être transmise pour le plein contrôle des intervalles de reconnexion.

Si la reconnexion échoue après le dernier intervalle de reconnexion :

* Le client considère que la connexion est hors connexion.
* Le client cesse d’essayer de se reconnecter.

Lors des tentatives de reconnexion, mettez à jour l’interface utilisateur de l’application pour informer l’utilisateur que la reconnexion est tentée.

Pour fournir des commentaires sur l’interface SignalR utilisateur lorsque la connexion est interrompue, l’API client a été élargie pour inclure les gestionnaires d’événements suivants :

* `onreconnecting`: Donne aux développeurs la possibilité de désactiver l’interface utilisateur ou de faire savoir aux utilisateurs que l’application est hors ligne.
* `onreconnected`: Donne aux développeurs l’occasion de mettre à jour l’interface utilisateur une fois la connexion rétablie.

Le code `onreconnecting` suivant utilise pour mettre à jour l’interface utilisateur tout en essayant de se connecter:

```javascript
connection.onreconnecting((error) => {
    const status = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messageInput").disabled = true;
    document.getElementById("sendButton").disabled = true;
    document.getElementById("connectionStatus").innerText = status;
});
```

Le code `onreconnected` suivant utilise pour mettre à jour l’interface utilisateur sur la connexion :

```javascript
connection.onreconnected((connectionId) => {
    const status = `Connection reestablished. Connected.`;
    document.getElementById("messageInput").disabled = false;
    document.getElementById("sendButton").disabled = false;
    document.getElementById("connectionStatus").innerText = status;
});
```

SignalR3.0 et plus tard fournit une ressource personnalisée aux gestionnaires d’autorisation lorsqu’une méthode de plaque tournante nécessite une autorisation. La ressource est `HubInvocationContext`un exemple de . Le `HubInvocationContext` comprend le:

* `HubCallerContext`
* Nom de la méthode du hub invoquée.
* Arguments à la méthode de moyeu.

Prenons l’exemple suivant d’une application de salle de chat permettant à plusieurs organisations de vous connecter via Azure Active Directory. Toute personne ayant un compte Microsoft peut se connecter pour discuter, mais seuls les membres de l’organisation propriétaire peuvent interdire les utilisateurs ou afficher les historiques de chat des utilisateurs. L’application pourrait restreindre certaines fonctionnalités de certains utilisateurs spécifiques.

```csharp
public class DomainRestrictedRequirement :
    AuthorizationHandler<DomainRestrictedRequirement, HubInvocationContext>,
    IAuthorizationRequirement
{
    protected override Task HandleRequirementAsync(AuthorizationHandlerContext context,
        DomainRestrictedRequirement requirement,
        HubInvocationContext resource)
    {
        if (context.User?.Identity?.Name == null)
        {
            return Task.CompletedTask;
        }

        if (IsUserAllowedToDoThis(resource.HubMethodName, context.User.Identity.Name))
        {
            context.Succeed(requirement);
        }

        return Task.CompletedTask;
    }

    private bool IsUserAllowedToDoThis(string hubMethodName, string currentUsername)
    {
        if (hubMethodName.Equals("banUser", StringComparison.OrdinalIgnoreCase))
        {
            return currentUsername.Equals("bob42@jabbr.net", StringComparison.OrdinalIgnoreCase);
        }

        return currentUsername.EndsWith("@jabbr.net", StringComparison.OrdinalIgnoreCase));
    }
}
```

Dans le code `DomainRestrictedRequirement` précédent, `IAuthorizationRequirement`sert de coutume . Étant `HubInvocationContext` donné que le paramètre de ressources est adopté, la logique interne peut :

* Inspectez le contexte dans lequel le Hub est appelé.
* Prenez des décisions en permettant à l’utilisateur d’exécuter des méthodes Hub individuelles.

Les méthodes Hub individuelles peuvent être marquées avec le nom de la stratégie que le code vérifie au moment de l’exécution. Alors que les clients tentent `DomainRestrictedRequirement` d’appeler des méthodes Hub individuelles, le gestionnaire exécute et contrôle l’accès aux méthodes. D’après la `DomainRestrictedRequirement` façon dont les contrôles accèdent :

* Tous les utilisateurs connectés peuvent `SendMessage` appeler la méthode.
* Seuls les utilisateurs qui se `@jabbr.net` sont connectés avec une adresse e-mail peuvent afficher l’historique des utilisateurs.
* Seuls `bob42@jabbr.net` les utilisateurs peuvent interdire les utilisateurs de la salle de chat.

```csharp
[Authorize]
public class ChatHub : Hub
{
    public void SendMessage(string message)
    {
    }

    [Authorize("DomainRestricted")]
    public void BanUser(string username)
    {
    }

    [Authorize("DomainRestricted")]
    public void ViewUserHistory(string username)
    {
    }
}
```

La `DomainRestricted` création de la politique pourrait impliquer :

* En *Startup.cs*, en ajoutant la nouvelle politique.
* Fournir l’exigence personnalisée `DomainRestrictedRequirement` comme paramètre.
* Enregistrement `DomainRestricted` auprès du middleware d’autorisation.

```csharp
services
    .AddAuthorization(options =>
    {
        options.AddPolicy("DomainRestricted", policy =>
        {
            policy.Requirements.Add(new DomainRestrictedRequirement());
        });
    });
```

SignalRhubs utilisent [Endpoint Routing](xref:fundamentals/routing). SignalRla connexion hub a déjà été effectuée explicitement :

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("hubs/chat");
});
```

Dans la version précédente, les développeurs avaient besoin de transférer les contrôleurs, les pages Razor et les moyeux dans une variété d’endroits. La connexion explicite se traduit par une série de segments de routage presque identiques :

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("hubs/chat");
});

app.UseRouting(routes =>
{
    routes.MapRazorPages();
});
```

SignalR3.0 hubs peuvent être acheminés via le routage de point final. Avec le routage de point de terminaison, généralement tous les itinéraires peuvent être configurés dans `UseRouting`:

```csharp
app.UseRouting(routes =>
{
    routes.MapRazorPages();
    routes.MapHub<ChatHub>("hubs/chat");
});
```

ASP.NET Core 3.0 SignalR a ajouté :

Streaming client-serveur. Avec le streaming client-serveur, les méthodes côté serveur `IAsyncEnumerable<T>` `ChannelReader<T>`peuvent prendre des exemples d’un ou . Dans l’échantillon C `UploadStream` SUIVANT, la méthode du Hub recevra un flux de chaînes du client :

```csharp
public async Task UploadStream(IAsyncEnumerable<string> stream)
{
    await foreach (var item in stream)
    {
        // process content
    }
}
```

.NET applications client peuvent `IAsyncEnumerable<T>` passer `ChannelReader<T>` soit `stream` un ou `UploadStream` une instance comme l’argument de la méthode Hub ci-dessus.

Une `for` fois la boucle terminée et les sorties de la fonction locale, l’achèvement du flux est envoyé :

```csharp
async IAsyncEnumerable<string> clientStreamData()
{
    for (var i = 0; i < 5; i++)
    {
        var data = await FetchSomeData();
        yield return data;
    }
}

await connection.SendAsync("UploadStream", clientStreamData());
```

Les applications client SignalR `Subject` JavaScript utilisent le (ou un `stream` sujet `UploadStream` [RxJS](https://rxjs.dev/api/index/class/Subject)) pour l’argument de la méthode Hub ci-dessus.

```javascript
let subject = new signalR.Subject();
await connection.send("StartStream", "MyAsciiArtStream", subject);
```

Le code JavaScript `subject.next` pourrait utiliser la méthode pour gérer les chaînes au fur et à mesure qu’elles sont capturées et prêtes à être envoyées au serveur.

```javascript
subject.next("example");
subject.complete();
```

À l’aide de code comme les deux extraits précédents, des expériences de streaming en temps réel peuvent être créées.

## <a name="new-json-serialization"></a>Nouvelle sérialisation JSON

ASP.NET Core 3.0 utilise <xref:System.Text.Json> désormais par défaut pour la sérialisation JSON :

* Lit et écrit JSON asynchronement.
* Est optimisé pour le texte UTF-8.
* Des performances `Newtonsoft.Json`généralement supérieures à .

Pour ajouter Json.NET à ASP.NET Core 3.0, voir [Add Newtonsoft.Json-based JSON format support](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).

## <a name="new-razor-directives"></a>Nouvelles directives Razor

La liste suivante contient de nouvelles directives Razor :

* [`@attribute`](xref:mvc/views/razor#attribute)&ndash; La `@attribute` directive applique l’attribut donné à la classe de la page ou de la vue générée. Par exemple : `@attribute [Authorize]`.
* [`@implements`](xref:mvc/views/razor#implements)&ndash; La `@implements` directive met en œuvre une interface pour la classe générée. Par exemple : `@implements IDisposable`.

## <a name="identityserver4-supports-authentication-and-authorization-for-web-apis-and-spas"></a>IdentityServer4 prend en charge l’authentification et l’autorisation des API web et des APE

ASP.NET Core 3.0 offre l’authentification dans les applications à page unique (ZSP) en utilisant le support pour l’autorisation d’API Web. ASP.NET’identité de base pour l’authentification et le stockage des utilisateurs est combinée avec [IdentityServer4](https://identityserver.io/) pour la mise en œuvre de Open ID Connect.

IdentityServer4 est un cadre OpenID Connect et OAuth 2.0 pour ASP.NET Core 3.0. Il permet les caractéristiques de sécurité suivantes :

* Authentification en tant que service (AaaS)
* Signature unique/off (SSO) sur plusieurs types d’applications
* Contrôle d’accès pour les API
* Porte d’entrée de la Fédération

Pour plus d’informations, consultez [la documentation IdentityServer4](http://docs.identityserver.io/en/latest/index.html) ou [l’authentification et l’autorisation pour les APE](xref:security/authentication/identity/spa).

## <a name="certificate-and-kerberos-authentication"></a>Certificate et authentification Kerberos

L’authentification des certificats exige :

* Configurer le serveur pour accepter les certificats.
* Ajout de l’authentification middleware dans `Startup.Configure`.
* Ajout du service d’authentification du certificat dans `Startup.ConfigureServices`.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
            .AddCertificate();
    // Other service configuration removed.
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();
    // Other app configuration removed.
}
```

Les options d’authentification des certificats comprennent la possibilité de :

* Acceptez les certificats autosuffants.
* Vérifiez la révocation du certificat.
* Vérifiez que le certificat délivré a les bons drapeaux d’utilisation en elle.

Un mandant utilisateur par défaut est construit à partir des propriétés du certificat. Le principal utilisateur contient un événement qui permet de compléter ou de remplacer le mandant. Pour plus d’informations, consultez <xref:security/authentication/certauth>.

[L’authentification de Windows](/windows-server/security/windows-authentication/windows-authentication-overview) a été étendue à Linux et macOS. Dans les versions précédentes, Windows Authentication a été limitée à [l’IIS](xref:host-and-deploy/iis/index) et [HttpSys](xref:fundamentals/servers/httpsys). Dans ASP.NET Core 3.0, [Kestrel](xref:fundamentals/servers/kestrel) a la possibilité d’utiliser Negotiate, [Kerberos](/windows-server/security/kerberos/kerberos-authentication-overview), et [NTLM sur Windows](/windows-server/security/kerberos/ntlm-overview), Linux, et macOS pour windows de domaine rejoint hôtes. Le support Kestrel de ces systèmes d’authentification est fourni par le paquet [NuGet Microsoft.AspNetCore.Authentication.Negotiate.](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) Comme avec les autres services d’authentification, configurer l’application d’authentification large, puis configurer le service:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(NegotiateDefaults.AuthenticationScheme)
        .AddNegotiate();
    // Other service configuration removed.
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();
    // Other app configuration removed.
}
```

Exigences de l’hôte :

* Les hôtes Windows doivent avoir [des noms principaux de service](/windows/win32/ad/service-principal-names) (SN) ajoutés au compte utilisateur hébergeant l’application.
* Les machines Linux et macOS doivent être jointes au domaine.
  * Les SPN doivent être créés pour le processus web.
  * [Les fichiers Keytab](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) doivent être générés et configurés sur la machine hôte.

Pour plus d’informations, consultez <xref:security/authentication/windowsauth>.

## <a name="template-changes"></a>Modifications de modèle

Les modèles d’interface utilisateur Web (Razor Pages, MVC avec contrôleur et vues) ont supprimé ce qui suit :

* L’interface utilisateur de consentement à un cookie n’est plus incluse. Pour activer la fonction de consentement au cookie dans une application <xref:security/gdpr>générée par ASP.NET Core 3.0, voir .
* Les scripts et les actifs statiques connexes sont maintenant référencés comme des fichiers locaux au lieu d’utiliser des CDN. Pour plus d’informations, voir [Scripts et actifs statiques connexes sont maintenant référencés comme des fichiers locaux au lieu d’utiliser des CDN en fonction de l’environnement actuel (aspnet/AspNetCore.Docs #14350)](https://github.com/dotnet/AspNetCore.Docs/issues/14350).

Le modèle angulaire mis à jour pour utiliser Angular 8.

Le modèle de bibliothèque de classe Razor (RCL) par défaut au développement de composants Razor par défaut. Une nouvelle option de modèle dans Visual Studio fournit un support de modèle pour les pages et les vues. Lors de la création d’un RCL à `--support-pages-and-views` partir`dotnet new razorclasslib --support-pages-and-views`du modèle dans une coque de commande, passez l’option ( ).

## <a name="generic-host"></a>Hôte générique

Les modèles ASP.NET Core 3.0 <xref:fundamentals/host/generic-host>utilisent . Versions précédentes utilisées <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>. L’utilisation de l’hôte générique de base .NET (<xref:Microsoft.Extensions.Hosting.HostBuilder>) fournit une meilleure intégration des applications ASP.NET Core avec d’autres scénarios de serveur qui ne sont pas spécifiques au Web. Pour plus d’informations, voir [HostBuilder remplace WebHostBuilder](xref:migration/22-to-30?view=aspnetcore-2.2#hostbuilder-replaces-webhostbuilder).

### <a name="host-configuration"></a>Configuration de l’hôte

Avant la sortie de ASP.NET Core 3.0, les variables `ASPNETCORE_` de l’environnement préfixées ont été chargées pour la configuration hôte de l’hébergeur. Dans 3.0, `AddEnvironmentVariables` est utilisé pour charger `DOTNET_` les variables `CreateDefaultBuilder`de l’environnement préfixées avec pour la configuration de l’hôte avec .

### <a name="changes-to-startup-constructor-injection"></a>Modifications apportées à l’injection de constructeurs startup

L’hôte générique ne prend `Startup` en charge que les types suivants pour l’injection de constructeurs :

* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

Tous les services peuvent encore être `Startup.Configure` injectés directement comme arguments à la méthode. Pour plus d’informations, voir [Generic Host limite l’injection de constructeurs startup (aspnet/Announcements #353)](https://github.com/aspnet/Announcements/issues/353).

## <a name="kestrel"></a>Kestrel

* La configuration Kestrel a été mise à jour pour la migration vers l’hôte générique. Dans 3.0, Kestrel est configuré sur `ConfigureWebHostDefaults`le constructeur d’hébergeur web fourni par .
* Les adaptateurs de connexion ont été supprimés de Kestrel et remplacés par Connection Middleware, qui est similaire à HTTP Middleware dans le pipeline ASP.NET Core, mais pour les connexions de niveau inférieur.
* La couche de transport Kestrel a `Connections.Abstractions`été exposée comme une interface publique dans .
* L’ambiguïté entre les en-têtes et les remorques a été résolue en déplaçant les en-têtes de fuite vers une nouvelle collection.
* Synchrones I/O API, `HttpRequest.Body.Read`tels que , sont une source commune de famine de fil conduisant à des plantages d’applications. En 3.0, `AllowSynchronousIO` est désactivé par défaut.

Pour plus d’informations, consultez <xref:migration/22-to-30#kestrel>.

## <a name="http2-enabled-by-default"></a>HTTP/2 activé par défaut

HTTP/2 est activé par défaut dans Kestrel pour les paramètres HTTPS. LE support HTTP/2 pour IIS ou HTTP.sys est activé lorsqu’il est pris en charge par le système d’exploitation.

## <a name="eventcounters-on-request"></a>EventCounters sur demande

L’Hosting EventSource, `Microsoft.AspNetCore.Hosting`, émet <xref:System.Diagnostics.Tracing.EventCounter> les nouveaux types suivants liés aux demandes entrantes :

* `requests-per-second`
* `total-requests`
* `current-requests`
* `failed-requests`

## <a name="endpoint-routing"></a>Itinéraire de point de terminaison

Endpoint Routing, qui permet aux cadres (par exemple, MVC) de bien fonctionner avec middleware, est amélioré :

* L’ordre de middleware et les points de terminaison est configurable dans le pipeline de traitement de demande de `Startup.Configure`.
* Les points de terminaison et les articles intermédiaires sont bien composés avec d’autres technologies ASP.NET basées sur le noyau, telles que les contrôles de santé.
* Les points de terminaison peuvent mettre en œuvre une stratégie, comme le CORS ou l’autorisation, dans les articles intermédiaires et MVC.
* Les filtres et les attributs peuvent être placés sur les méthodes dans les contrôleurs.

Pour plus d’informations, consultez <xref:fundamentals/routing#routing-basics>.

## <a name="health-checks"></a>Contrôles d'intégrité

Les contrôles de santé utilisent le routage des points de terminaison avec l’hôte générique. Dans `Startup.Configure`, `MapHealthChecks` appelez le constructeur de point de terminaison avec l’URL de point de terminaison ou le chemin relatif :

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
});
```

Les points de terminaison de contrôles de santé peuvent :

* Spécifier un ou plusieurs hôtes/ports autorisés.
* Exiger l’autorisation.
* Exigez CORS.

Pour plus d’informations, consultez les articles suivants :

* <xref:migration/22-to-30#health-checks>
* <xref:host-and-deploy/health-checks>

## <a name="pipes-on-httpcontext"></a>Pipes sur HttpContext

Il est maintenant possible de lire le corps de <xref:System.IO.Pipelines> demande et d’écrire le corps de réponse à l’aide de l’API. Les <!-- <xref:Microsoft.AspNetCore.Http.HttpRequest.BodyReader> --> `HttpRequest.BodyReader`propriété fournit <xref:System.IO.Pipelines.PipeReader> un qui peut être utilisé pour lire le corps de demande. Les <!-- <xref:Microsoft.AspNetCore.Http.> --> `HttpResponse.BodyWriter`propriété fournit <xref:System.IO.Pipelines.PipeWriter> un qui peut être utilisé pour écrire le corps de réponse. `HttpRequest.BodyReader`est un analogue `HttpRequest.Body` du flux. `HttpResponse.BodyWriter`est un analogue `HttpResponse.Body` du flux.

<!-- indirectly related, https://github.com/dotnet/docs/pull/14414 won't be published by 9/23  -->

## <a name="improved-error-reporting-in-iis"></a>Amélioration des rapports d’erreurs dans l’IIS

Les erreurs de démarrage lors de l’hébergement des applications ASP.NET Core dans IIS produisent maintenant des données diagnostiques plus riches. Ces erreurs sont signalées au Journal d’événements Windows avec des traces de pile partout où cela est applicable. En outre, tous les avertissements, erreurs et exceptions non gérées sont enregistrés dans le journal d’événements Windows.

## <a name="worker-service-and-worker-sdk"></a>Service aux travailleurs et travailleurS SDK

.NET Core 3.0 présente le nouveau modèle d’application Service aux travailleurs. Ce modèle fournit un point de départ pour la rédaction de services à long terme dans .NET Core.

Pour plus d'informations, consultez les pages suivantes :

* [.NET Core Workers as Windows Services (en)](https://devblogs.microsoft.com/aspnet/net-core-workers-as-windows-services/)
* <xref:fundamentals/host/hosted-services>
* <xref:host-and-deploy/windows-service>

## <a name="forwarded-headers-middleware-improvements"></a>Améliorations de Têtes en avant Middleware

Dans les versions précédentes de <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> ASP.NET <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> Core, les appels et les posaient problème lorsqu’ils étaient déployés sur un Linux Azure ou derrière tout proxy inversé autre que l’IIS. Le correctif pour les versions précédentes est documenté dans [Forward le système pour Linux et non-IIS mandataires inversés](xref:host-and-deploy/proxy-load-balancer#forward-the-scheme-for-linux-and-non-iis-reverse-proxies).

Ce scénario est fixé dans ASP.NET Core 3.0. L’hôte permet le [Middleware Forwarded Headers](xref:host-and-deploy/proxy-load-balancer#forwarded-headers-middleware-options) lorsque la variable de l’environnement `ASPNETCORE_FORWARDEDHEADERS_ENABLED` est réglée à `true`. `ASPNETCORE_FORWARDEDHEADERS_ENABLED`est réglé `true` dans nos images de conteneurs.

## <a name="performance-improvements"></a>Optimisation des performances

ASP.NET Core 3.0 comprend de nombreuses améliorations qui réduisent l’utilisation de la mémoire et améliorent le débit :

* Réduction de l’utilisation de la mémoire lors de l’utilisation du conteneur d’injection de dépendance intégré pour les services de portée.
* Réduction des allocations dans l’ensemble du cadre, y compris les scénarios de middleware et le routage.
* Réduction de l’utilisation de la mémoire pour les connexions WebSocket.
* Amélioration de la réduction de la mémoire et du débit pour les connexions HTTPS.
* Nouveau sérialisateur JSON optimisé et entièrement asynchrone.
* Réduction de l’utilisation de la mémoire et des améliorations de débit dans l’analyse de forme.

## <a name="aspnet-core-30-only-runs-on-net-core-30"></a>ASP.NET Core 3.0 ne fonctionne que sur .NET Core 3.0

À partir de ASP.NET Core 3.0, le cadre NET n’est plus un cadre cible pris en charge. Projets ciblant .NET Framework peut continuer d’une manière entièrement pris en charge en utilisant le [.NET Core 2.1 LTS version](https://dotnet.microsoft.com/download/dotnet-core/2.1). La plupart des paquets liés au noyau 2.1.x de base de ASP.NET seront pris en charge indéfiniment, au-delà de la période de trois ans de LTS pour .NET Core 2.1.

Pour plus d’informations sur la migration, consultez [Port votre code de .NET Framework à .NET Core](/dotnet/core/porting/).

## <a name="use-the-aspnet-core-shared-framework"></a>Utiliser le cadre partagé ASP.NET Core

Le cadre partagé ASP.NET Core 3.0, contenu dans le [métapackage Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), ne nécessite plus d’élément explicite `<PackageReference />` dans le fichier du projet. Le cadre partagé est automatiquement `Microsoft.NET.Sdk.Web` référencé lors de l’utilisation du SDK dans le fichier du projet :

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

## <a name="assemblies-removed-from-the-aspnet-core-shared-framework"></a>Assemblées retirées du cadre commun ASP.NET Core

Les assemblages les plus notables retirés du cadre commun ASP.NET Core 3.0 sont les :

* [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) (Json.NET). Pour ajouter Json.NET à ASP.NET Core 3.0, voir [Add Newtonsoft.Json-based JSON format support](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support). ASP.NET Core 3.0 introduit `System.Text.Json` pour la lecture et l’écriture JSON. Pour plus d’informations, voir [Nouvelle sérialisation JSON](#new-json-serialization) dans ce document.
* [Entity Framework Core](/ef/core/)

Pour une liste complète des assemblages supprimés du cadre partagé, voir [les assemblages supprimés de Microsoft.AspNetCore.App 3.0](https://github.com/dotnet/AspNetCore/issues/3755). Pour plus d’informations sur la motivation de ce changement, voir [Breaking changes to Microsoft.AspNetCore.App in 3.0](https://github.com/aspnet/Announcements/issues/325) and [A first look at changes coming in ASP.NET Core 3.0](https://devblogs.microsoft.com/aspnet/a-first-look-at-changes-coming-in-asp-net-core-3-0/).

<!-- 
## Additional information
For the complete list of changes, see the [ASP.NET Core 3.0 Release Notes](WHERE IS THIS????).
-->
 