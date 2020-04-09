---
title: Authentification et autorisation en gRPC pour ASP.NET Core
author: jamesnk
description: Apprenez à utiliser l’authentification et l’autorisation dans gRPC pour ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 12/05/2019
uid: grpc/authn-and-authz
ms.openlocfilehash: c0312b186bbb35e3b802984484b7213016d8bf04
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78964442"
---
# <a name="authentication-and-authorization-in-grpc-for-aspnet-core"></a>Authentification et autorisation en gRPC pour ASP.NET Core

Par [James Newton-King](https://twitter.com/jamesnk)

[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/grpc/authn-and-authz/sample/) [(comment télécharger)](xref:index#how-to-download-a-sample)

## <a name="authenticate-users-calling-a-grpc-service"></a>Authentifier les utilisateurs appelant un service gRPC

gRPC peut être utilisé avec [ASP.NET authentification De base](xref:security/authentication/identity) pour associer un utilisateur à chaque appel.

Ce qui suit `Startup.Configure` est un exemple qui utilise gRPC et ASP.NET authentification de base :

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseRouting();
    
    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapGrpcService<GreeterService>();
    });
}
```

> [!NOTE]
> L’ordre dans lequel vous enregistrez le ASP.NET Core authentification middleware importe. Toujours `UseAuthentication` appeler `UseAuthorization` `UseRouting` et `UseEndpoints`après et avant .

Le mécanisme d’authentification que votre application utilise lors d’un appel doit être configuré. La configuration d’authentification est ajoutée `Startup.ConfigureServices` et sera différente selon le mécanisme d’authentification que votre application utilise. Pour des exemples de la façon de sécuriser ASP.NET applications Core, voir [échantillons d’authentification](xref:security/authentication/samples).

Une fois l’authentification a été configurée, l’utilisateur peut être consulté dans une méthode de service gRPC via le `ServerCallContext`.

```csharp
public override Task<BuyTicketsResponse> BuyTickets(
    BuyTicketsRequest request, ServerCallContext context)
{
    var user = context.GetHttpContext().User;

    // ... access data from ClaimsPrincipal ...
}

```

### <a name="bearer-token-authentication"></a>Authentification symbolique du porteur

Le client peut fournir un jeton d’accès pour l’authentification. Le serveur valide le jeton et l’utilise pour identifier l’utilisateur.

Sur le serveur, l’authentification symbolique du porteur est configurée à l’aide du [middleware JWT Bearer](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).

Dans le client .NET gRPC, le jeton peut être envoyé avec des appels comme un en-tête:

```csharp
public bool DoAuthenticatedCall(
    Ticketer.TicketerClient client, string token)
{
    var headers = new Metadata();
    headers.Add("Authorization", $"Bearer {token}");

    var request = new BuyTicketsRequest { Count = 1 };
    var response = await client.BuyTicketsAsync(request, headers);

    return response.Success;
}
```

La configuration `ChannelCredentials` d’un canal est une autre façon d’envoyer le jeton au service avec des appels gRPC. L’identification est exécutée chaque fois qu’un appel gRPC est fait, ce qui évite la nécessité d’écrire du code à plusieurs endroits pour passer le jeton vous-même.

L’identification dans l’exemple suivant configure le canal pour envoyer le jeton avec chaque appel gRPC:

```csharp
private static GrpcChannel CreateAuthenticatedChannel(string address)
{
    var credentials = CallCredentials.FromInterceptor((context, metadata) =>
    {
        if (!string.IsNullOrEmpty(_token))
        {
            metadata.Add("Authorization", $"Bearer {_token}");
        }
        return Task.CompletedTask;
    });

    // SslCredentials is used here because this channel is using TLS.
    // CallCredentials can't be used with ChannelCredentials.Insecure on non-TLS channels.
    var channel = GrpcChannel.ForAddress(address, new GrpcChannelOptions
    {
        Credentials = ChannelCredentials.Create(new SslCredentials(), credentials)
    });
    return channel;
}
```

### <a name="client-certificate-authentication"></a>Authentification par certificat client

Un client pourrait également fournir un certificat client pour l’authentification. [L’authentification des certificats](https://tools.ietf.org/html/rfc5246#section-7.4.4) se produit au niveau TLS, bien avant qu’elle n’arrive à ASP.NET Core. Lorsque la demande entre ASP.NET Core, le [paquet d’authentification du certificat client](xref:security/authentication/certauth) vous permet de résoudre le certificat à un `ClaimsPrincipal`.

> [!NOTE]
> L’hôte doit être configuré pour accepter les certificats clients. Voir [configurer votre hôte pour exiger des certificats](xref:security/authentication/certauth#configure-your-host-to-require-certificates) pour obtenir des informations sur l’acceptation des certificats clients en Kestrel, IIS et Azure.

Dans le client .NET gRPC, le `HttpClientHandler` certificat du client est ajouté à cela est ensuite utilisé pour créer le client gRPC:

```csharp
public Ticketer.TicketerClient CreateClientWithCert(
    string baseAddress,
    X509Certificate2 certificate)
{
    // Add client cert to the handler
    var handler = new HttpClientHandler();
    handler.ClientCertificates.Add(certificate);

    // Create the gRPC channel
    var channel = GrpcChannel.ForAddress(baseAddress, new GrpcChannelOptions
    {
        HttpClient = new HttpClient(handler)
    });

    return new Ticketer.TicketerClient(channel);
}
```

### <a name="other-authentication-mechanisms"></a>Autres mécanismes d’authentification

De nombreux mécanismes d’authentification soutenus par ASP.NET Core fonctionnent avec gRPC :

* Azure Active Directory
* Certificat client
* IdentityServer
* Jeton JWT
* OAuth 2.0
* OpenID Connect
* Un certificat de fournisseur d'identité WS-Federation

Pour plus d’informations sur la configuration de l’authentification sur le serveur, voir [ASP.NET authentification de base](xref:security/authentication/identity).

La configuration du client gRPC pour utiliser l’authentification dépendra du mécanisme d’authentification que vous utilisez. Les exemples précédents de jetons et de certificats de client montrent quelques façons dont le client gRPC peut être configuré pour envoyer des métadonnées d’authentification avec des appels gRPC :

* Les clients GRPC `HttpClient` fortement tapés utilisent à l’interne. L’authentification peut être configurée sur [HttpClientHandler](/dotnet/api/system.net.http.httpclienthandler), ou `HttpClient`en ajoutant des instances [personnalisées de HttpMessageHandler](/dotnet/api/system.net.http.httpmessagehandler) à la .
* Chaque appel gRPC `CallOptions` a un argument facultatif. Les en-têtes personnalisés peuvent être envoyés à l’aide de la collection d’en-têtes de l’option.

> [!NOTE]
> L’authentification windows (NTLM/Kerberos/Négocier) ne peut pas être utilisée avec gRPC. gRPC nécessite HTTP/2, et HTTP/2 ne prend pas en charge Windows Authentication.

## <a name="authorize-users-to-access-services-and-service-methods"></a>Autoriser les utilisateurs à accéder aux services et aux méthodes de service

Par défaut, toutes les méthodes d’un service peuvent être appelées par des utilisateurs non authentiques. Pour exiger l’authentification, appliquez l’attribut [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) au service :

```csharp
[Authorize]
public class TicketerService : Ticketer.TicketerBase
{
}
```

Vous pouvez utiliser les arguments et `[Authorize]` les propriétés du constructeur de l’attribut pour restreindre l’accès aux seuls utilisateurs correspondant à des [politiques d’autorisation](xref:security/authorization/policies)spécifiques . Par exemple, si vous avez `MyAuthorizationPolicy`une politique d’autorisation personnalisée appelée, assurez-vous que seuls les utilisateurs correspondant à cette stratégie peuvent accéder au service en utilisant le code suivant :

```csharp
[Authorize("MyAuthorizationPolicy")]
public class TicketerService : Ticketer.TicketerBase
{
}
```

Les méthodes de `[Authorize]` service individuelles peuvent également appliquer l’attribut. Si l’utilisateur actuel ne correspond pas aux stratégies appliquées à la **fois** à la méthode et à la classe, une erreur est retournée à l’appelant :

```csharp
[Authorize]
public class TicketerService : Ticketer.TicketerBase
{
    public override Task<AvailableTicketsResponse> GetAvailableTickets(
        Empty request, ServerCallContext context)
    {
        // ... buy tickets for the current user ...
    }

    [Authorize("Administrators")]
    public override Task<BuyTicketsResponse> RefundTickets(
        BuyTicketsRequest request, ServerCallContext context)
    {
        // ... refund tickets (something only Administrators can do) ..
    }
}
```

## <a name="additional-resources"></a>Ressources supplémentaires

* [Authentification de jetons porteur dans ASP.NET Core](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [Configurer l’authentification du certificat client dans ASP.NET Core](xref:security/authentication/certauth)
