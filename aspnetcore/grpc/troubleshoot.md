---
title: Dépanner gRPC sur .NET Core
author: jamesnk
description: Erreurs de dépannage lors de l’utilisation de gRPC sur .NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 10/16/2019
uid: grpc/troubleshoot
ms.openlocfilehash: c501cda14f3bac9297695ece59cbc4634e4b7895
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664128"
---
# <a name="troubleshoot-grpc-on-net-core"></a><span data-ttu-id="d23b9-103">Dépanner gRPC sur .NET Core</span><span class="sxs-lookup"><span data-stu-id="d23b9-103">Troubleshoot gRPC on .NET Core</span></span>

<span data-ttu-id="d23b9-104">Par [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="d23b9-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="d23b9-105">Ce document traite des problèmes couramment rencontrés lors du développement d’applications gRPC sur .NET.</span><span class="sxs-lookup"><span data-stu-id="d23b9-105">This document discusses commonly encountered problems when developing gRPC apps on .NET.</span></span>

## <a name="mismatch-between-client-and-service-ssltls-configuration"></a><span data-ttu-id="d23b9-106">Décalage entre la configuration client et service SSL/TLS</span><span class="sxs-lookup"><span data-stu-id="d23b9-106">Mismatch between client and service SSL/TLS configuration</span></span>

<span data-ttu-id="d23b9-107">Le modèle et les échantillons gRPC utilisent [transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) pour sécuriser les services gRPC par défaut.</span><span class="sxs-lookup"><span data-stu-id="d23b9-107">The gRPC template and samples use [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) to secure gRPC services by default.</span></span> <span data-ttu-id="d23b9-108">les clients de gRPC doivent utiliser une connexion sécurisée pour appeler les services gRPC sécurisés avec succès.</span><span class="sxs-lookup"><span data-stu-id="d23b9-108">gRPC clients need to use a secure connection to call secured gRPC services successfully.</span></span>

<span data-ttu-id="d23b9-109">Vous pouvez vérifier que le service ASP.NET Core gRPC utilise TLS dans les journaux écrits sur le démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="d23b9-109">You can verify the ASP.NET Core gRPC service is using TLS in the logs written on app start.</span></span> <span data-ttu-id="d23b9-110">Le service sera à l’écoute sur un point de terminaison HTTPS :</span><span class="sxs-lookup"><span data-stu-id="d23b9-110">The service will be listening on an HTTPS endpoint:</span></span>

```
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

<span data-ttu-id="d23b9-111">Le client .NET `https` Core doit utiliser dans l’adresse du serveur pour passer des appels avec une connexion sécurisée :</span><span class="sxs-lookup"><span data-stu-id="d23b9-111">The .NET Core client must use `https` in the server address to make calls with a secured connection:</span></span>

```csharp
static async Task Main(string[] args)
{
    // The port number(5001) must match the port of the gRPC server.
    var channel = GrpcChannel.ForAddress("https://localhost:5001");
    var client = new Greet.GreeterClient(channel);
}
```

<span data-ttu-id="d23b9-112">Toutes les implémentations de clients gRPC prennent en charge TLS.</span><span class="sxs-lookup"><span data-stu-id="d23b9-112">All gRPC client implementations support TLS.</span></span> <span data-ttu-id="d23b9-113">gRPC clients d’autres langues nécessitent `SslCredentials`généralement le canal configuré avec .</span><span class="sxs-lookup"><span data-stu-id="d23b9-113">gRPC clients from other languages typically require the channel configured with `SslCredentials`.</span></span> <span data-ttu-id="d23b9-114">`SslCredentials`spécifie le certificat que le client utilisera, et il doit être utilisé au lieu d’informations d’identification non sécurisées.</span><span class="sxs-lookup"><span data-stu-id="d23b9-114">`SslCredentials` specifies the certificate that the client will use, and it must be used instead of insecure credentials.</span></span> <span data-ttu-id="d23b9-115">Pour des exemples de configuration des différentes implémentations de clients gRPC pour utiliser TLS, voir [gRPC Authentication](https://www.grpc.io/docs/guides/auth/).</span><span class="sxs-lookup"><span data-stu-id="d23b9-115">For examples of configuring the different gRPC client implementations to use TLS, see [gRPC Authentication](https://www.grpc.io/docs/guides/auth/).</span></span>

## <a name="call-a-grpc-service-with-an-untrustedinvalid-certificate"></a><span data-ttu-id="d23b9-116">Appelez un service gRPC avec un certificat non fiable/invalide</span><span class="sxs-lookup"><span data-stu-id="d23b9-116">Call a gRPC service with an untrusted/invalid certificate</span></span>

<span data-ttu-id="d23b9-117">Le client .NET gRPC exige que le service ait un certificat de confiance.</span><span class="sxs-lookup"><span data-stu-id="d23b9-117">The .NET gRPC client requires the service to have a trusted certificate.</span></span> <span data-ttu-id="d23b9-118">Le message d’erreur suivant est retourné lors de l’appel d’un service gRPC sans certificat de confiance :</span><span class="sxs-lookup"><span data-stu-id="d23b9-118">The following error message is returned when calling a gRPC service without a trusted certificate:</span></span>

> <span data-ttu-id="d23b9-119">Exception non gérée.</span><span class="sxs-lookup"><span data-stu-id="d23b9-119">Unhandled exception.</span></span> <span data-ttu-id="d23b9-120">System.Net.http.httpRequestException: La connexion SSL n’a pas pu être établie, voir exception interne.</span><span class="sxs-lookup"><span data-stu-id="d23b9-120">System.Net.Http.HttpRequestException: The SSL connection could not be established, see inner exception.</span></span>
> <span data-ttu-id="d23b9-121">---> System.Security.Authentication.AuthenticationException : Le certificat distant n’est pas valide selon la procédure de validation.</span><span class="sxs-lookup"><span data-stu-id="d23b9-121">---> System.Security.Authentication.AuthenticationException: The remote certificate is invalid according to the validation procedure.</span></span>

<span data-ttu-id="d23b9-122">Vous pouvez voir cette erreur si vous testez votre application localement et le certificat de développement core HTTPS ASP.NET n’est pas fiable.</span><span class="sxs-lookup"><span data-stu-id="d23b9-122">You may see this error if you are testing your app locally and the ASP.NET Core HTTPS development certificate is not trusted.</span></span> <span data-ttu-id="d23b9-123">Pour obtenir des instructions afin de résoudre ce problème, consultez [Faire confiance au certificat de développement ASP.NET Core HTTPS sur Windows et macOS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).</span><span class="sxs-lookup"><span data-stu-id="d23b9-123">For instructions to fix this issue, see [Trust the ASP.NET Core HTTPS development certificate on Windows and macOS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).</span></span>

<span data-ttu-id="d23b9-124">Si vous appelez un service gRPC sur une autre machine et que vous n’êtes pas en mesure de faire confiance au certificat, le client gRPC peut être configuré pour ignorer le certificat invalide.</span><span class="sxs-lookup"><span data-stu-id="d23b9-124">If you are calling a gRPC service on another machine and are unable to trust the certificate then the gRPC client can be configured to ignore the invalid certificate.</span></span> <span data-ttu-id="d23b9-125">Le code suivant utilise [HttpClientHandler.ServerCertificateCustomValidationCallback](/dotnet/api/system.net.http.httpclienthandler.servercertificatecustomvalidationcallback) pour permettre aux appels sans certificat de confiance :</span><span class="sxs-lookup"><span data-stu-id="d23b9-125">The following code uses [HttpClientHandler.ServerCertificateCustomValidationCallback](/dotnet/api/system.net.http.httpclienthandler.servercertificatecustomvalidationcallback) to allow calls without a trusted certificate:</span></span>

```csharp
var httpClientHandler = new HttpClientHandler();
// Return `true` to allow certificates that are untrusted/invalid
httpClientHandler.ServerCertificateCustomValidationCallback = 
    HttpClientHandler.DangerousAcceptAnyServerCertificateValidator;
var httpClient = new HttpClient(httpClientHandler);

var channel = GrpcChannel.ForAddress("https://localhost:5001",
    new GrpcChannelOptions { HttpClient = httpClient });
var client = new Greet.GreeterClient(channel);
```

> [!WARNING]
> <span data-ttu-id="d23b9-126">Les certificats non fiables ne doivent être utilisés que lors du développement de l’application.</span><span class="sxs-lookup"><span data-stu-id="d23b9-126">Untrusted certificates should only be used during app development.</span></span> <span data-ttu-id="d23b9-127">Les applications de production doivent toujours utiliser des certificats valides.</span><span class="sxs-lookup"><span data-stu-id="d23b9-127">Production apps should always use valid certificates.</span></span>

## <a name="call-insecure-grpc-services-with-net-core-client"></a><span data-ttu-id="d23b9-128">Appelez les services gRPC non sécurisés avec le client .NET Core</span><span class="sxs-lookup"><span data-stu-id="d23b9-128">Call insecure gRPC services with .NET Core client</span></span>

<span data-ttu-id="d23b9-129">Une configuration supplémentaire est nécessaire pour appeler des services gRPC non sécurisés avec le client .NET Core.</span><span class="sxs-lookup"><span data-stu-id="d23b9-129">Additional configuration is required to call insecure gRPC services with the .NET Core client.</span></span> <span data-ttu-id="d23b9-130">Le client gRPC `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` doit `true` définir `http` le commutateur et l’utiliser dans l’adresse du serveur :</span><span class="sxs-lookup"><span data-stu-id="d23b9-130">The gRPC client must set the `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` switch to `true` and use `http` in the server address:</span></span>

```csharp
// This switch must be set before creating the GrpcChannel/HttpClient.
AppContext.SetSwitch(
    "System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport", true);

// The port number(5000) must match the port of the gRPC server.
var channel = GrpcChannel.ForAddress("http://localhost:5000");
var client = new Greet.GreeterClient(channel);
```

## <a name="unable-to-start-aspnet-core-grpc-app-on-macos"></a><span data-ttu-id="d23b9-131">Impossible de démarrer ASP.NET’application Core gRPC sur macOS</span><span class="sxs-lookup"><span data-stu-id="d23b9-131">Unable to start ASP.NET Core gRPC app on macOS</span></span>

<span data-ttu-id="d23b9-132">Kestrel ne prend pas en charge HTTP/2 avec TLS sur macOS et les anciennes versions Windows telles que Windows 7.</span><span class="sxs-lookup"><span data-stu-id="d23b9-132">Kestrel doesn't support HTTP/2 with TLS on macOS and older Windows versions such as Windows 7.</span></span> <span data-ttu-id="d23b9-133">Le modèle et les échantillons de ASP.NET Core gRPC utilisent TLS par défaut.</span><span class="sxs-lookup"><span data-stu-id="d23b9-133">The ASP.NET Core gRPC template and samples use TLS by default.</span></span> <span data-ttu-id="d23b9-134">Vous verrez le message d’erreur suivant lorsque vous tentez de démarrer le serveur gRPC :</span><span class="sxs-lookup"><span data-stu-id="d23b9-134">You'll see the following error message when you attempt to start the gRPC server:</span></span>

> <span data-ttu-id="d23b9-135">Impossible de https://localhost:5001 se lier à l’interface IPv4 loopback: «HTTP/2 sur TLS n’est pas pris en charge sur macOS en raison de la prise de support ALPN manquant.</span><span class="sxs-lookup"><span data-stu-id="d23b9-135">Unable to bind to https://localhost:5001 on the IPv4 loopback interface: 'HTTP/2 over TLS is not supported on macOS due to missing ALPN support.'.</span></span>

<span data-ttu-id="d23b9-136">Pour contourner ce problème, configurez Kestrel et le client gRPC pour utiliser HTTP/2 *sans* TLS.</span><span class="sxs-lookup"><span data-stu-id="d23b9-136">To work around this issue, configure Kestrel and the gRPC client to use HTTP/2 *without* TLS.</span></span> <span data-ttu-id="d23b9-137">Vous ne devriez le faire que pendant le développement.</span><span class="sxs-lookup"><span data-stu-id="d23b9-137">You should only do this during development.</span></span> <span data-ttu-id="d23b9-138">Le fait de ne pas utiliser TLS entraînera l’envoi de messages gRPC sans cryptage.</span><span class="sxs-lookup"><span data-stu-id="d23b9-138">Not using TLS will result in gRPC messages being sent without encryption.</span></span>

<span data-ttu-id="d23b9-139">Kestrel doit configurer un point de terminaison HTTP/2 sans TLS en *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="d23b9-139">Kestrel must configure an HTTP/2 endpoint without TLS in *Program.cs*:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(options =>
            {
                // Setup a HTTP/2 endpoint without TLS.
                options.ListenLocalhost(5000, o => o.Protocols = 
                    HttpProtocols.Http2);
            });
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="d23b9-140">Lorsqu’un point de terminaison HTTP/2 est configuré sans TLS, les `HttpProtocols.Http2` [protocoles ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) du point de terminaison doivent être définis .</span><span class="sxs-lookup"><span data-stu-id="d23b9-140">When an HTTP/2 endpoint is configured without TLS, the endpoint's [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) must be set to `HttpProtocols.Http2`.</span></span> <span data-ttu-id="d23b9-141">`HttpProtocols.Http1AndHttp2`ne peut pas être utilisé parce que TLS est tenu de négocier HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="d23b9-141">`HttpProtocols.Http1AndHttp2` can't be used because TLS is required to negotiate HTTP/2.</span></span> <span data-ttu-id="d23b9-142">Sans TLS, toutes les connexions à la valeur par défaut de point de terminaison à HTTP/1.1, et les appels gRPC échouent.</span><span class="sxs-lookup"><span data-stu-id="d23b9-142">Without TLS, all connections to the endpoint default to HTTP/1.1, and gRPC calls fail.</span></span>

<span data-ttu-id="d23b9-143">Le client gRPC doit également être configuré pour ne pas utiliser TLS.</span><span class="sxs-lookup"><span data-stu-id="d23b9-143">The gRPC client must also be configured to not use TLS.</span></span> <span data-ttu-id="d23b9-144">Pour plus d’informations, voir [Call insecure gRPC services avec .NET Core client](#call-insecure-grpc-services-with-net-core-client).</span><span class="sxs-lookup"><span data-stu-id="d23b9-144">For more information, see [Call insecure gRPC services with .NET Core client](#call-insecure-grpc-services-with-net-core-client).</span></span>

> [!WARNING]
> <span data-ttu-id="d23b9-145">HTTP/2 sans TLS ne doit être utilisé que pendant le développement de l’application.</span><span class="sxs-lookup"><span data-stu-id="d23b9-145">HTTP/2 without TLS should only be used during app development.</span></span> <span data-ttu-id="d23b9-146">Les applications de production doivent toujours utiliser la sécurité des transports.</span><span class="sxs-lookup"><span data-stu-id="d23b9-146">Production apps should always use transport security.</span></span> <span data-ttu-id="d23b9-147">Pour plus d’informations, voir [considérations de sécurité dans gRPC pour ASP.NET Core](xref:grpc/security#transport-security).</span><span class="sxs-lookup"><span data-stu-id="d23b9-147">For more information, see [Security considerations in gRPC for ASP.NET Core](xref:grpc/security#transport-security).</span></span>

## <a name="grpc-c-assets-are-not-code-generated-from-proto-files"></a><span data-ttu-id="d23b9-148">les actifs gRPC C N’sont pas du code généré à partir de fichiers .proto</span><span class="sxs-lookup"><span data-stu-id="d23b9-148">gRPC C# assets are not code generated from .proto files</span></span>

<span data-ttu-id="d23b9-149">GRPC génération de code de clients en béton et les classes de base de service exige des fichiers protobuf et l’outillage pour être référencé à partir d’un projet.</span><span class="sxs-lookup"><span data-stu-id="d23b9-149">gRPC code generation of concrete clients and service base classes requires protobuf files and tooling to be referenced from a project.</span></span> <span data-ttu-id="d23b9-150">Vous devez inclure :</span><span class="sxs-lookup"><span data-stu-id="d23b9-150">You must include:</span></span>

* <span data-ttu-id="d23b9-151">*.fichiers proto* que vous `<Protobuf>` souhaitez utiliser dans le groupe d’éléments.</span><span class="sxs-lookup"><span data-stu-id="d23b9-151">*.proto* files you want to use in the `<Protobuf>` item group.</span></span> <span data-ttu-id="d23b9-152">[Les fichiers *.proto* importés](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions) doivent être référencés par le projet.</span><span class="sxs-lookup"><span data-stu-id="d23b9-152">[Imported *.proto* files](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions) must be referenced by the project.</span></span>
* <span data-ttu-id="d23b9-153">Référence de paquet au paquet d’outillage gRPC [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/).</span><span class="sxs-lookup"><span data-stu-id="d23b9-153">Package reference to the gRPC tooling package [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/).</span></span>

<span data-ttu-id="d23b9-154">Pour plus d’informations sur la génération <xref:grpc/basics>d’actifs GRPC C, voir .</span><span class="sxs-lookup"><span data-stu-id="d23b9-154">For more information on generating gRPC C# assets, see <xref:grpc/basics>.</span></span>

<span data-ttu-id="d23b9-155">Par défaut, `<Protobuf>` une référence génère un client concret et une classe de base de service.</span><span class="sxs-lookup"><span data-stu-id="d23b9-155">By default, a `<Protobuf>` reference generates a concrete client and a service base class.</span></span> <span data-ttu-id="d23b9-156">L’attribut de `GrpcServices` l’élément de référence peut être utilisé pour limiter la génération d’actifs C.</span><span class="sxs-lookup"><span data-stu-id="d23b9-156">The reference element's `GrpcServices` attribute can be used to limit C# asset generation.</span></span> <span data-ttu-id="d23b9-157">Les `GrpcServices` options valides sont les suivantes :</span><span class="sxs-lookup"><span data-stu-id="d23b9-157">Valid `GrpcServices` options are:</span></span>

* <span data-ttu-id="d23b9-158">`Both`(par défaut lorsqu’il n’est pas présent)</span><span class="sxs-lookup"><span data-stu-id="d23b9-158">`Both` (default when not present)</span></span>
* `Server`
* `Client`
* `None`

<span data-ttu-id="d23b9-159">Une application web ASP.NET Core hébergeant les services gRPC n’a besoin que de la classe de base de service générée :</span><span class="sxs-lookup"><span data-stu-id="d23b9-159">An ASP.NET Core web app hosting gRPC services only needs the service base class generated:</span></span>

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
</ItemGroup>
```

<span data-ttu-id="d23b9-160">Une application client gRPC effectuant des appels gRPC n’a besoin que du client en béton généré :</span><span class="sxs-lookup"><span data-stu-id="d23b9-160">A gRPC client app making gRPC calls only needs the concrete client generated:</span></span>

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
</ItemGroup>
```

## <a name="wpf-projects-unable-to-generate-grpc-c-assets-from-proto-files"></a><span data-ttu-id="d23b9-161">Projets WPF incapables de générer des actifs gRPC C ' à partir de fichiers .proto</span><span class="sxs-lookup"><span data-stu-id="d23b9-161">WPF projects unable to generate gRPC C# assets from .proto files</span></span>

<span data-ttu-id="d23b9-162">Les projets WPF ont un [problème connu](https://github.com/dotnet/wpf/issues/810) qui empêche la génération de code gRPC de fonctionner correctement.</span><span class="sxs-lookup"><span data-stu-id="d23b9-162">WPF projects have a [known issue](https://github.com/dotnet/wpf/issues/810) that prevents gRPC code generation from working correctly.</span></span> <span data-ttu-id="d23b9-163">Tous les types de gRPC générés `Grpc.Tools` dans un projet WPF par référencement et *.proto* fichiers créeront des erreurs de compilation lorsqu’ils sont utilisés :</span><span class="sxs-lookup"><span data-stu-id="d23b9-163">Any gRPC types generated in a WPF project by referencing `Grpc.Tools` and *.proto* files will create compilation errors when used:</span></span>

> <span data-ttu-id="d23b9-164">erreur CS0246: Le type ou nom de l’espace de nom «MyGrpcServices» n’a pas pu être trouvé (manquez-vous une directive en utilisant ou une référence d’assemblage?)</span><span class="sxs-lookup"><span data-stu-id="d23b9-164">error CS0246: The type or namespace name 'MyGrpcServices' could not be found (are you missing a using directive or an assembly reference?)</span></span>

<span data-ttu-id="d23b9-165">Vous pouvez contourner ce problème en :</span><span class="sxs-lookup"><span data-stu-id="d23b9-165">You can workaround this issue by:</span></span>

1. <span data-ttu-id="d23b9-166">Créez un nouveau projet de bibliothèque de classe .NET Core.</span><span class="sxs-lookup"><span data-stu-id="d23b9-166">Create a new .NET Core class library project.</span></span>
2. <span data-ttu-id="d23b9-167">Dans le nouveau projet, ajoutez des références pour activer [la génération de code C à partir de \* \*\* fichiers .proto :](xref:grpc/basics#generated-c-assets)</span><span class="sxs-lookup"><span data-stu-id="d23b9-167">In the new project, add references to enable [C# code generation from *\*.proto* files](xref:grpc/basics#generated-c-assets):</span></span>
    * <span data-ttu-id="d23b9-168">Ajoutez une référence au package [Grpc.Tools.](https://www.nuget.org/packages/Grpc.Tools/)</span><span class="sxs-lookup"><span data-stu-id="d23b9-168">Add a package reference to [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) package.</span></span>
    * <span data-ttu-id="d23b9-169">Ajoutez \* \*\* des fichiers `<Protobuf>` .proto au groupe d’éléments.</span><span class="sxs-lookup"><span data-stu-id="d23b9-169">Add *\*.proto* files to the `<Protobuf>` item group.</span></span>
3. <span data-ttu-id="d23b9-170">Dans l’application WPF, ajoutez une référence au nouveau projet.</span><span class="sxs-lookup"><span data-stu-id="d23b9-170">In the WPF application, add a reference to the new project.</span></span>

<span data-ttu-id="d23b9-171">L’application WPF peut utiliser les types générés par le GRPC à partir du projet de bibliothèque de nouvelle classe.</span><span class="sxs-lookup"><span data-stu-id="d23b9-171">The WPF application can use the gRPC generated types from the new class library project.</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]
