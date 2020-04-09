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
# <a name="authentication-and-authorization-in-grpc-for-aspnet-core"></a><span data-ttu-id="81976-103">Authentification et autorisation en gRPC pour ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="81976-103">Authentication and authorization in gRPC for ASP.NET Core</span></span>

<span data-ttu-id="81976-104">Par [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="81976-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="81976-105">[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/grpc/authn-and-authz/sample/) [(comment télécharger)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="81976-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/grpc/authn-and-authz/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="authenticate-users-calling-a-grpc-service"></a><span data-ttu-id="81976-106">Authentifier les utilisateurs appelant un service gRPC</span><span class="sxs-lookup"><span data-stu-id="81976-106">Authenticate users calling a gRPC service</span></span>

<span data-ttu-id="81976-107">gRPC peut être utilisé avec [ASP.NET authentification De base](xref:security/authentication/identity) pour associer un utilisateur à chaque appel.</span><span class="sxs-lookup"><span data-stu-id="81976-107">gRPC can be used with [ASP.NET Core authentication](xref:security/authentication/identity) to associate a user with each call.</span></span>

<span data-ttu-id="81976-108">Ce qui suit `Startup.Configure` est un exemple qui utilise gRPC et ASP.NET authentification de base :</span><span class="sxs-lookup"><span data-stu-id="81976-108">The following is an example of `Startup.Configure` which uses gRPC and ASP.NET Core authentication:</span></span>

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
> <span data-ttu-id="81976-109">L’ordre dans lequel vous enregistrez le ASP.NET Core authentification middleware importe.</span><span class="sxs-lookup"><span data-stu-id="81976-109">The order in which you register the ASP.NET Core authentication middleware matters.</span></span> <span data-ttu-id="81976-110">Toujours `UseAuthentication` appeler `UseAuthorization` `UseRouting` et `UseEndpoints`après et avant .</span><span class="sxs-lookup"><span data-stu-id="81976-110">Always call `UseAuthentication` and `UseAuthorization` after `UseRouting` and before `UseEndpoints`.</span></span>

<span data-ttu-id="81976-111">Le mécanisme d’authentification que votre application utilise lors d’un appel doit être configuré.</span><span class="sxs-lookup"><span data-stu-id="81976-111">The authentication mechanism your app uses during a call needs to be configured.</span></span> <span data-ttu-id="81976-112">La configuration d’authentification est ajoutée `Startup.ConfigureServices` et sera différente selon le mécanisme d’authentification que votre application utilise.</span><span class="sxs-lookup"><span data-stu-id="81976-112">Authentication configuration is added in `Startup.ConfigureServices` and will be different depending upon the authentication mechanism your app uses.</span></span> <span data-ttu-id="81976-113">Pour des exemples de la façon de sécuriser ASP.NET applications Core, voir [échantillons d’authentification](xref:security/authentication/samples).</span><span class="sxs-lookup"><span data-stu-id="81976-113">For examples of how to secure ASP.NET Core apps, see [Authentication samples](xref:security/authentication/samples).</span></span>

<span data-ttu-id="81976-114">Une fois l’authentification a été configurée, l’utilisateur peut être consulté dans une méthode de service gRPC via le `ServerCallContext`.</span><span class="sxs-lookup"><span data-stu-id="81976-114">Once authentication has been setup, the user can be accessed in a gRPC service methods via the `ServerCallContext`.</span></span>

```csharp
public override Task<BuyTicketsResponse> BuyTickets(
    BuyTicketsRequest request, ServerCallContext context)
{
    var user = context.GetHttpContext().User;

    // ... access data from ClaimsPrincipal ...
}

```

### <a name="bearer-token-authentication"></a><span data-ttu-id="81976-115">Authentification symbolique du porteur</span><span class="sxs-lookup"><span data-stu-id="81976-115">Bearer token authentication</span></span>

<span data-ttu-id="81976-116">Le client peut fournir un jeton d’accès pour l’authentification.</span><span class="sxs-lookup"><span data-stu-id="81976-116">The client can provide an access token for authentication.</span></span> <span data-ttu-id="81976-117">Le serveur valide le jeton et l’utilise pour identifier l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="81976-117">The server validates the token and uses it to identify the user.</span></span>

<span data-ttu-id="81976-118">Sur le serveur, l’authentification symbolique du porteur est configurée à l’aide du [middleware JWT Bearer](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).</span><span class="sxs-lookup"><span data-stu-id="81976-118">On the server, bearer token authentication is configured using the [JWT Bearer middleware](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).</span></span>

<span data-ttu-id="81976-119">Dans le client .NET gRPC, le jeton peut être envoyé avec des appels comme un en-tête:</span><span class="sxs-lookup"><span data-stu-id="81976-119">In the .NET gRPC client, the token can be sent with calls as a header:</span></span>

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

<span data-ttu-id="81976-120">La configuration `ChannelCredentials` d’un canal est une autre façon d’envoyer le jeton au service avec des appels gRPC.</span><span class="sxs-lookup"><span data-stu-id="81976-120">Configuring `ChannelCredentials` on a channel is an alternative way to send the token to the service with gRPC calls.</span></span> <span data-ttu-id="81976-121">L’identification est exécutée chaque fois qu’un appel gRPC est fait, ce qui évite la nécessité d’écrire du code à plusieurs endroits pour passer le jeton vous-même.</span><span class="sxs-lookup"><span data-stu-id="81976-121">The credential is run each time a gRPC call is made, which avoids the need to write code in multiple places to pass the token yourself.</span></span>

<span data-ttu-id="81976-122">L’identification dans l’exemple suivant configure le canal pour envoyer le jeton avec chaque appel gRPC:</span><span class="sxs-lookup"><span data-stu-id="81976-122">The credential in the following example configures the channel to send the token with every gRPC call:</span></span>

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

### <a name="client-certificate-authentication"></a><span data-ttu-id="81976-123">Authentification par certificat client</span><span class="sxs-lookup"><span data-stu-id="81976-123">Client certificate authentication</span></span>

<span data-ttu-id="81976-124">Un client pourrait également fournir un certificat client pour l’authentification.</span><span class="sxs-lookup"><span data-stu-id="81976-124">A client could alternatively provide a client certificate for authentication.</span></span> <span data-ttu-id="81976-125">[L’authentification des certificats](https://tools.ietf.org/html/rfc5246#section-7.4.4) se produit au niveau TLS, bien avant qu’elle n’arrive à ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="81976-125">[Certificate authentication](https://tools.ietf.org/html/rfc5246#section-7.4.4) happens at the TLS level, long before it ever gets to ASP.NET Core.</span></span> <span data-ttu-id="81976-126">Lorsque la demande entre ASP.NET Core, le [paquet d’authentification du certificat client](xref:security/authentication/certauth) vous permet de résoudre le certificat à un `ClaimsPrincipal`.</span><span class="sxs-lookup"><span data-stu-id="81976-126">When the request enters ASP.NET Core, the [client certificate authentication package](xref:security/authentication/certauth) allows you to resolve the certificate to a `ClaimsPrincipal`.</span></span>

> [!NOTE]
> <span data-ttu-id="81976-127">L’hôte doit être configuré pour accepter les certificats clients.</span><span class="sxs-lookup"><span data-stu-id="81976-127">The host needs to be configured to accept client certificates.</span></span> <span data-ttu-id="81976-128">Voir [configurer votre hôte pour exiger des certificats](xref:security/authentication/certauth#configure-your-host-to-require-certificates) pour obtenir des informations sur l’acceptation des certificats clients en Kestrel, IIS et Azure.</span><span class="sxs-lookup"><span data-stu-id="81976-128">See [configure your host to require certificates](xref:security/authentication/certauth#configure-your-host-to-require-certificates) for information on accepting client certificates in Kestrel, IIS and Azure.</span></span>

<span data-ttu-id="81976-129">Dans le client .NET gRPC, le `HttpClientHandler` certificat du client est ajouté à cela est ensuite utilisé pour créer le client gRPC:</span><span class="sxs-lookup"><span data-stu-id="81976-129">In the .NET gRPC client, the client certificate is added to `HttpClientHandler` that is then used to create the gRPC client:</span></span>

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

### <a name="other-authentication-mechanisms"></a><span data-ttu-id="81976-130">Autres mécanismes d’authentification</span><span class="sxs-lookup"><span data-stu-id="81976-130">Other authentication mechanisms</span></span>

<span data-ttu-id="81976-131">De nombreux mécanismes d’authentification soutenus par ASP.NET Core fonctionnent avec gRPC :</span><span class="sxs-lookup"><span data-stu-id="81976-131">Many ASP.NET Core supported authentication mechanisms work with gRPC:</span></span>

* <span data-ttu-id="81976-132">Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="81976-132">Azure Active Directory</span></span>
* <span data-ttu-id="81976-133">Certificat client</span><span class="sxs-lookup"><span data-stu-id="81976-133">Client Certificate</span></span>
* <span data-ttu-id="81976-134">IdentityServer</span><span class="sxs-lookup"><span data-stu-id="81976-134">IdentityServer</span></span>
* <span data-ttu-id="81976-135">Jeton JWT</span><span class="sxs-lookup"><span data-stu-id="81976-135">JWT Token</span></span>
* <span data-ttu-id="81976-136">OAuth 2.0</span><span class="sxs-lookup"><span data-stu-id="81976-136">OAuth 2.0</span></span>
* <span data-ttu-id="81976-137">OpenID Connect</span><span class="sxs-lookup"><span data-stu-id="81976-137">OpenID Connect</span></span>
* <span data-ttu-id="81976-138">Un certificat de fournisseur d'identité WS-Federation</span><span class="sxs-lookup"><span data-stu-id="81976-138">WS-Federation</span></span>

<span data-ttu-id="81976-139">Pour plus d’informations sur la configuration de l’authentification sur le serveur, voir [ASP.NET authentification de base](xref:security/authentication/identity).</span><span class="sxs-lookup"><span data-stu-id="81976-139">For more information on configuring authentication on the server, see [ASP.NET Core authentication](xref:security/authentication/identity).</span></span>

<span data-ttu-id="81976-140">La configuration du client gRPC pour utiliser l’authentification dépendra du mécanisme d’authentification que vous utilisez.</span><span class="sxs-lookup"><span data-stu-id="81976-140">Configuring the gRPC client to use authentication will depend on the authentication mechanism you are using.</span></span> <span data-ttu-id="81976-141">Les exemples précédents de jetons et de certificats de client montrent quelques façons dont le client gRPC peut être configuré pour envoyer des métadonnées d’authentification avec des appels gRPC :</span><span class="sxs-lookup"><span data-stu-id="81976-141">The previous bearer token and client certificate examples show a couple of ways the gRPC client can be configured to send authentication metadata with gRPC calls:</span></span>

* <span data-ttu-id="81976-142">Les clients GRPC `HttpClient` fortement tapés utilisent à l’interne.</span><span class="sxs-lookup"><span data-stu-id="81976-142">Strongly typed gRPC clients use `HttpClient` internally.</span></span> <span data-ttu-id="81976-143">L’authentification peut être configurée sur [HttpClientHandler](/dotnet/api/system.net.http.httpclienthandler), ou `HttpClient`en ajoutant des instances [personnalisées de HttpMessageHandler](/dotnet/api/system.net.http.httpmessagehandler) à la .</span><span class="sxs-lookup"><span data-stu-id="81976-143">Authentication can be configured on [HttpClientHandler](/dotnet/api/system.net.http.httpclienthandler), or by adding custom [HttpMessageHandler](/dotnet/api/system.net.http.httpmessagehandler) instances to the `HttpClient`.</span></span>
* <span data-ttu-id="81976-144">Chaque appel gRPC `CallOptions` a un argument facultatif.</span><span class="sxs-lookup"><span data-stu-id="81976-144">Each gRPC call has an optional `CallOptions` argument.</span></span> <span data-ttu-id="81976-145">Les en-têtes personnalisés peuvent être envoyés à l’aide de la collection d’en-têtes de l’option.</span><span class="sxs-lookup"><span data-stu-id="81976-145">Custom headers can be sent using the option's headers collection.</span></span>

> [!NOTE]
> <span data-ttu-id="81976-146">L’authentification windows (NTLM/Kerberos/Négocier) ne peut pas être utilisée avec gRPC.</span><span class="sxs-lookup"><span data-stu-id="81976-146">Windows Authentication (NTLM/Kerberos/Negotiate) can't be used with gRPC.</span></span> <span data-ttu-id="81976-147">gRPC nécessite HTTP/2, et HTTP/2 ne prend pas en charge Windows Authentication.</span><span class="sxs-lookup"><span data-stu-id="81976-147">gRPC requires HTTP/2, and HTTP/2 doesn't support Windows Authentication.</span></span>

## <a name="authorize-users-to-access-services-and-service-methods"></a><span data-ttu-id="81976-148">Autoriser les utilisateurs à accéder aux services et aux méthodes de service</span><span class="sxs-lookup"><span data-stu-id="81976-148">Authorize users to access services and service methods</span></span>

<span data-ttu-id="81976-149">Par défaut, toutes les méthodes d’un service peuvent être appelées par des utilisateurs non authentiques.</span><span class="sxs-lookup"><span data-stu-id="81976-149">By default, all methods in a service can be called by unauthenticated users.</span></span> <span data-ttu-id="81976-150">Pour exiger l’authentification, appliquez l’attribut [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) au service :</span><span class="sxs-lookup"><span data-stu-id="81976-150">To require authentication, apply the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute to the service:</span></span>

```csharp
[Authorize]
public class TicketerService : Ticketer.TicketerBase
{
}
```

<span data-ttu-id="81976-151">Vous pouvez utiliser les arguments et `[Authorize]` les propriétés du constructeur de l’attribut pour restreindre l’accès aux seuls utilisateurs correspondant à des [politiques d’autorisation](xref:security/authorization/policies)spécifiques .</span><span class="sxs-lookup"><span data-stu-id="81976-151">You can use the constructor arguments and properties of the `[Authorize]` attribute to restrict access to only users matching specific [authorization policies](xref:security/authorization/policies).</span></span> <span data-ttu-id="81976-152">Par exemple, si vous avez `MyAuthorizationPolicy`une politique d’autorisation personnalisée appelée, assurez-vous que seuls les utilisateurs correspondant à cette stratégie peuvent accéder au service en utilisant le code suivant :</span><span class="sxs-lookup"><span data-stu-id="81976-152">For example, if you have a custom authorization policy called `MyAuthorizationPolicy`, ensure that only users matching that policy can access the service using the following code:</span></span>

```csharp
[Authorize("MyAuthorizationPolicy")]
public class TicketerService : Ticketer.TicketerBase
{
}
```

<span data-ttu-id="81976-153">Les méthodes de `[Authorize]` service individuelles peuvent également appliquer l’attribut.</span><span class="sxs-lookup"><span data-stu-id="81976-153">Individual service methods can have the `[Authorize]` attribute applied as well.</span></span> <span data-ttu-id="81976-154">Si l’utilisateur actuel ne correspond pas aux stratégies appliquées à la **fois** à la méthode et à la classe, une erreur est retournée à l’appelant :</span><span class="sxs-lookup"><span data-stu-id="81976-154">If the current user doesn't match the policies applied to **both** the method and the class, an error is returned to the caller:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="81976-155">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="81976-155">Additional resources</span></span>

* [<span data-ttu-id="81976-156">Authentification de jetons porteur dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="81976-156">Bearer Token authentication in ASP.NET Core</span></span>](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [<span data-ttu-id="81976-157">Configurer l’authentification du certificat client dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="81976-157">Configure Client Certificate authentication in ASP.NET Core</span></span>](xref:security/authentication/certauth)
